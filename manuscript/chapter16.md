# React to events

To make a web page interactive, you have to respond to user actions. Let's discover how to do so.

## TL;DR

* You can make a web page interactive by writing JavaScript code tied to **events** within the browser.

* Numerous types of events can be handled. Each event type is associated with an `Event` object that contains information about the event itself and its properties.

* `keypress`, `keydown` and `keyup` events let you react to keyboard-related events.

* `click`, `mousedown` and `mouseup` events let you react to mouse-related events.

* Page loading and closing are associated with the events `load` and `beforeunload` respectively.

* An event propagates within the DOM tree from its node of origin until the document root. This propagation can be interrupted with the `stopPropagation()` method.

## Introduction to events

Up until now, your JavaScript code was executed right from the start. The execution order of statements was determined in advance and the only user interactions were data input through `prompt()` calls.

To add more interactivity, the page should react to the user's actions: clicking on a button, filling a form, etc. In that case, the execution order of statements is not determined in advance anymore, but depends on the user behavior. His actions trigger **events** that can be handled by writing JavaScript code.

This way of writing programs is called **event-driven programming**. It is often used by user interfaces, and more generaly anytime a program needs to interact with an user.

### A first example

Here's some starter HTML code.

```html
<button id="myButton">Click me!</button>
```

 And here's the associated JavaScript code.

```js
function showMessage() {
  alert("Hello!");
}
// Access the button
const buttonElement = document.getElementById("myButton");
// Listen to the "click" event
buttonElement.addEventListener("click", showMessage);
```

Clicking on the web page button shows an `"Hello!` message.

![Execution result](images/chapter16-01.png)

### Adding an event listener

Called on a DOM element, the `addEventListener()` method adds a **handler** for a particular event. This method takes as parameter the **event type** and the associated **function**. This function gets called whenever an event of the corresponding type appears for the DOM element.

The above JavaScript code could be rewritten more concisely using an anonymous function, for an identical result.

```js
// Show a message when the user clicks on the button
document.getElementById("myButton").addEventListener("click", () => {
  alert("Hello!");
});
```

### Removing an event listener

In some particular cases, you might want to stop reacting to an event on a DOM element. To achieve this, call the `removeEventListener()` on the element, massing as a parameter the function which used to handle the event.

> This can only work if the handler function is not anonymous.

```js
// Remove the handler for the click event
buttonElement.removeEventListener("click", showMessage);
```

## The event family

Manay types of events can be triggered by DOM elements. Here are the main event categories.

| Category | Examples |
|---|---|
| Keyboard events | Pressing or releasing a key |
| Mouse events | Clicking on a mouse button, pressing or releasing a mouse button, hovering over a zone |
| Window events | Loading or closing a page, resizing, scrolling |
| Form events | Changing focus on a form field, submitting a form |

Every event is associated to an `Event` object which has both **properties** (informations about the event) and **methods** (ways to act on the event). This object can be used by the handler function.

Many properties of the `Event` object associated to an event depend on the event type. Some properties are always present, like `type` that returns the event type and `target` that return the event target (the DOM element that is the event source).

The `Event` object is passed as a parameter to the handler function. The following code uses it to show the event type and target in the console.

```js
// Show event type and target when the user clicks on the button
document.getElementById("myButton").addEventListener("click", e => {
  console.log(`Event type: ${e.type}, target: ${e.target}`);
});
```

> The parameter name chosen for the `Event` object is generaly `e` or `event`.

![Execution result](images/chapter16-02.png)

## Reacting to common events

### Key presses

The most common solution for reacting to key presses on a keyboard involves handling `keypress` events that happen on a web page (the DOM `body` element, which corresponds to the global variable called `document` in JavaScript).

The following example shows in the console the character assoaciated to a pressed key. Yhe character info is given by the `charCode` property of the `Event` object associated to the event. This property returns a numerical value (called **Unicode value**) that can be translated to a string value by the `String.FromCharCode()` method.

```js
// Show the pressed character
document.addEventListener("keypress", e => {
    console.log(`You pressed the ${String.fromCharCode(e.charCode)} key`);
});
```

![Execution result](images/chapter16-03.png)

To manage the press and release of any key (not only the ones producing characters), you'll use the `keydown` and `keyup` events. This example uses the same function to manage two events. This time, the key's code is accessible in the `keyCode` property of the `Event` object.

```js
// Show information on a keyboard event
function keyboardInfo(e) {
    console.log(`Keyboard event: ${e.type}, key: ${e.keyCode}`);
}

// Integrate this function into key press and release:
document.addEventListener("keydown", keyboardInfo);
document.addEventListener("keyup", keyboardInfo);
```

![Execution result](images/chapter16-04.png)

This results demonstrates that the launch order of keyboard-related events is as follows: `keydown` -> `keypress` -> `keyup`.

> The `keydown` is fired several times when a key is kept pressed.

### Mouse clicks

Mouse clicks on any DOM element produce a event of the `click` type. Tactile interfaces like smartphones and tablets also have `click` events associated with buttons, which are kicked off by actually pressing a finger on the button.

The `Event` object associated with a `click` event has a `button` property which lets you know the mouse button used, as well as `clientX` and `clientY` properties that return the horizontal and vertical coordinates of the place where the click happened. These coordinates are defined relative to the page zone currently shown by the browser.

![Difference between absolute and relative coordinates](images/chapter16-05.png)

The below code shows information on all click events that happen on a web page. The `mouseInfo()` function associated to the event uses another function, called `getMouseButton()`, to retrieve the clicked mouse button.

```js
// Return the name of the mouse button
function getMouseButton(code) {
    let button = "unknown";
    switch (code) {
    case 0: // 0 is the code for the left mouse button
        button = "left";
        break;
    case 1: // 1 is the code for the middle mouse button
        button = "middle";
        break;
    case 2: // 2 is the code for the right button
        button = "right";
        break;
    }
    return button;
}

// Show info about mouse event
function mouseInfo(e) {
    console.log(`Mouse event: ${e.type}, button: ${getMouseButton(e.button)}, X: ${e.clientX}, Y: ${e.clientY}`);
}

// Add mouse click event listener
document.addEventListener("click", mouseInfo);
```

![Execution result](images/chapter16-06.png)

You can use `mousedown` and `mouseup` events similarly to `keydown` and `keyup` to deal with mouse buttons' press and release events. The code below associates the same handler to these two events.

```js
// Handle mouse button press and release
document.addEventListener("mousedown", mouseInfo);
document.addEventListener("mouseup", mouseInfo);
```

![Execution result](images/chapter16-07.png)

The appearance order for mouse-related events is: `mousedown` -> `mouseup` -> `click`.

### Page loading

Depending on how complex it is, a web page can take time to be loaded 100% by the browser. You can add an event listener to know when this happens; it's an event listener on the window  object. This avoids messy situations where JavaScript interacts with pages that aren't fully loaded.

The following code displays a message in the console once the page is fully loaded.

// Web page loading event
window.addEventListener("load", function () {
    console.log("The page has been loaded!");
});

## Page closing

## Go farther with events

### Event propagation

The DOM represents a web page as a hierarchy of nodes. Events triggered on a child node are going to then trigger on the parent node, then the parent node of the parent node, up until the root of the DOM (the document variable). This is called event propagation.

To see propagation in action, add this HTML code in the <body> tag of your HTML code.

<p id="para">A paragraph with a <button id="propa">button</button> inside
</p>
Now add the complementary JavaScript code below to course.js. It adds click event handlers on the button, its parent (the paragraph), and the parent of that too (the root of the DOM). 

// Click handler on the document
document.addEventListener("click", function () {
    console.log("Document handler");
});
// Click handler on the paragraph
document.getElementById("para").addEventListener("click", function () {
    console.log("Paragraph handler");
});
// Click handler on the button
document.getElementById("propa").addEventListener("click", function (e) {
    console.log("Button handler");
});
Test it! Click the button and look at the console. http://codepen.io/eclairereese/pen/rLMYrj?editors=1111 ✏️
The result in the browser console demonstrates the propagation of click events from the button up to the document level. You clicked the button, which means you also clicked the paragraph, which means you also clicked the document. But maybe you only want an event to kick off once the button is clicked and not count its larger ecosystem!

Event propagation can be interrupted at any moment by calling the stopPropagation method on the Event object from a function that manages an event. This is useful to avoid the same event being handled multiple times.

Adding a line in the button's click handler prevents the click event from propagating everywhere in the DOM tree.

// Click handler on the button
document.getElementById("propa").addEventListener("click", function (e) {
    console.log("Button handler");
    e.stopPropagation(); // Stop the event propagation
});