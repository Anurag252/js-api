### Introduction to browser events
Mouse events:

click – when the mouse clicks on an element (touchscreen devices generate it on a tap).
contextmenu – when the mouse right-clicks on an element.
mouseover / mouseout – when the mouse cursor comes over / leaves an element.
mousedown / mouseup – when the mouse button is pressed / released over an element.
mousemove – when the mouse is moved.
Keyboard events:

keydown and keyup – when a keyboard key is pressed and released.
Form element events:

submit – when the visitor submits a <form>.
focus – when the visitor focuses on an element, e.g. on an <input>.
Document events:

DOMContentLoaded – when the HTML is loaded and processed, DOM is fully built.
CSS events:

transitionend – when a CSS-animation finishes.
  
  There are several ways to assign a handler. Let’s see them, starting from the simplest one.

## HTML-attribute
A handler can be set in HTML with an attribute named on<event>.

For instance, to assign a click handler for an input, we can use onclick, like here:

`<input value="Click me" onclick="alert('Click!')" type="button">`
  As we know, HTML attribute names are not case-sensitive, so ONCLICK works as well as onClick and onCLICK… But usually attributes are lowercased: onclick.
  
  ## DOM property
We can assign a handler using a DOM property on<event>.

For instance, elem.onclick:

`<input id="elem" type="button" value="Click me">
<script>
  elem.onclick = function() {
    alert('Thank you');
  };
</script>`
  
  If the handler is assigned using an HTML-attribute then the browser reads it, creates a new function from the attribute content and writes it to the DOM property.
  
  As there’s only one onclick property, we can’t assign more than one event handler.
  
  Accessing the element: this
The value of this inside a handler is the element. The one which has the handler on it.

In the code below button shows its contents using this.innerHTML:

`<button onclick="alert(this.innerHTML)">Click me</button>`
  
  Don’t use setAttribute for handlers.

Such a call won’t work:

// a click on <body> will generate errors,
// because attributes are always strings, function becomes a string
document.body.setAttribute('onclick', function() { alert(1) });
  
  DOM-property case matters.

Assign a handler to elem.onclick, not elem.ONCLICK, because DOM properties are case-sensitive.
  ### addEventListener
  element.addEventListener(event, handler, [options]);
  
  ### removeEventListener
  element.removeEventListener(event, handler, [options]);
  
  Multiple calls to addEventListener allow to add multiple handlers, like this:
  
  There exist events that can’t be assigned via a DOM-property. Only with addEventListener.

For instance, the DOMContentLoaded event, that triggers when the document is loaded and DOM is built.
  
  Event object
To properly handle an event we’d want to know more about what’s happened. Not just a “click” or a “keydown”, but what were the pointer coordinates? Which key was pressed? And so on.

When an event happens, the browser creates an event object, puts details into it and passes it as an argument to the handler.


Here’s an example of getting pointer coordinates from the event object:
  
  `<input type="button" value="Click me" id="elem">

<script>
  elem.onclick = function(event) {
    // show event type, element and coordinates of the click
    alert(event.type + " at " + event.currentTarget);
    alert("Coordinates: " + event.clientX + ":" + event.clientY);
  };
</script>`
  
  ##Object handlers: handleEvent
  We can assign not just a function, but an object as an event handler using addEventListener. When an event occurs, its handleEvent method is called.
  
  ### Bubbling and capturing
  
  ## Bubbling
  
  When an event happens on an element, it first runs the handlers on it, then on its parent, then all the way up on other ancestors.
  
  `<form onclick="alert('form')">FORM
  <div onclick="alert('div')">DIV
    <p onclick="alert('p')">P</p>
  </div>
</form>`
  
  event.target – is the “target” element that initiated the event, it doesn’t change through the bubbling process.
this – is the “current” element, the one that has a currently running handler on it.
**Stopping bubbling**
A bubbling event goes from the target element straight up. Normally it goes upwards till <html>, and then to document object, and some events even reach window, calling all handlers on the path.

But any handler may decide that the event has been fully processed and stop the bubbling.

The method for it is event.stopPropagation().
  
  
 ### Capturing
There’s another phase of event processing called “capturing”. It is rarely used in real code, but sometimes can be useful.

The standard DOM Events describes 3 phases of event propagation:

Capturing phase – the event goes down to the element.
Target phase – the event reached the target element.
Bubbling phase – the event bubbles up from the element.
  
  The code sets click handlers on every element in the document to see which ones are working.

  
  for html :- html --> body --> form --> div --> p
If you click on <p>, then the sequence is:

HTML → BODY → FORM → DIV (capturing phase, the first listener):
P (target phase, triggers two times, as we’ve set two listeners: capturing and bubbling)
DIV → FORM → BODY → HTML (bubbling phase, the second listener).
  
  to setup an event handler on bubbling phase use addEventHandler and pass true capture values 👎
  There are two possible values of the capture option:

If it’s false (default), then the handler is set on the bubbling phase.
If it’s true, then the handler is set on the capturing phase.
  
  
  ### Event delegation
  
  defining handler on table so that it is handled for all cells
  we can use **behaviour pattern** 🦖 --> declare an attribute and use event handler on all that attribute like thi s:- event.target.dataset.counter for data-counter
  
  stoppropagation vs stoppropagationimmediate
  
  ###Preventing browser actions
There are two ways to tell the browser we don’t want it to act:

The main way is to use the event object. There’s a method event.preventDefault().
If the handler is assigned using on<event> (not by addEventListener), then returning false also works the same.
  
  
  The “passive” handler option
The optional passive: true option of addEventListener signals the browser that the handler is not going to call preventDefault().

Why that may be needed?

There are some events like touchmove on mobile devices (when the user moves their finger across the screen), that cause scrolling by default, but that scrolling can be prevented using preventDefault() in the handler.

So when the browser detects such event, it has first to process all handlers, and then if preventDefault is not called anywhere, it can proceed with scrolling. That may cause unnecessary delays and “jitters” in the UI.

The passive: true options tells the browser that the handler is not going to cancel scrolling. Then browser scrolls immediately providing a maximally fluent experience, and the event is handled by the way.
  
  
  event.defaultPrevented
The property event.defaultPrevented is true if the default action was prevented, and false otherwise.
  
  ### dispatching events
  We can create Event objects like this:

`let event = new Event(type[, options]);`
  
  Arguments:

**type** – event type, a string like "click" or our own like "my-event".

options – the object with two optional properties:

bubbles: true/false – if true, then the event bubbles.
cancelable: true/false – if true, then the “default action” may be prevented. Later we’ll see what it means for custom events.
By default both are false: {bubbles: false, cancelable: false}
  
  After an event object is created, we should “run” it on an element using the call elem.dispatchEvent(event).
  
  event.isTrusted
There is a way to tell a “real” user event from a script-generated one.

The property event.isTrusted is true for events that come from real user actions and false for script-generated events.
  
  
  Notes:

We should use addEventListener for our custom events, because on<event> only exists for built-in events, document.onhello doesn’t work.
Must set bubbles:true, otherwise the event won’t bubble up. // if bubbling is needed
  
  client ctor does not allow custom props , but we can assign manually a few props 
  like event.prop = somevslue
  
  Custom events
For our own, completely new events types like "hello" we should use new CustomEvent. Technically CustomEvent is the same as Event, with one exception.

In the second argument (object) we can add an additional property detail for any custom information that we want to pass with the event.
  
   elem.dispatchEvent(new CustomEvent("hello", {
    detail: { name: "John" }
  }));
  
  
  event.preventDefault()
Many browser events have a “default action”, such as navigating to a link, starting a selection, and so on.

For new, custom events, there are definitely no default browser actions, but a code that dispatches such event may have its own plans what to do after triggering the event.

By calling event.preventDefault(), an event handler may send a signal that those actions should be canceled.

In that case the call to elem.dispatchEvent(event) returns false. And the code that dispatched it knows that it shouldn’t continue.
  
  ## Events-in-events are synchronous
  
  Usually events are processed in a queue. That is: if the browser is processing onclick and a new event occurs, e.g. mouse moved, then it’s handling is queued up, corresponding mousemove handlers will be called after onclick processing is finished.

The notable exception is when one event is initiated from within another one, e.g. using dispatchEvent. Such events are processed immediately: the new event handlers are called, and then the current event handling is resumed
  
  
  `<button id="menu">Menu (click me)</button>

<script>
  menu.onclick = function() {
    alert(1);

    setTimeout(() => menu.dispatchEvent(new CustomEvent("menu-open", {
      bubbles: true
    })));

    alert(2);
  };

  document.addEventListener('menu-open', () => alert('nested'));
</script>`
  
  
  ## mouse events
  order -> mouse down , mouse up , click
  there is a button prop with mouse clicks 
  
  event.button has it values :- 0 left,1 middle,2 right ,3,4 
  
  there is also below props 👎
  
  Event properties:

shiftKey: Shift
altKey: Alt (or Opt for Mac)
ctrlKey: Ctrl
metaKey: Cmd for Mac
  
  this is fired as which key was pressed along with mouse click
  
  **Coordinates: clientX/Y, pageX/Y**
  
All mouse events provide coordinates in two flavours:

Window-relative: clientX and clientY.
Document-relative: pageX and pageY.
  
 
There are multiple ways to prevent the selection, that you can read in the chapter Selection and Range.

In this particular case the most reasonable way is to prevent the browser action on mousedown. It prevents both these selections:

Before...
`<b ondblclick="alert('Click!')" onmousedown="return false">
  Double-click me
</b>`
...After
  
  
  Preventing copying
If we want to disable selection to protect our page content from copy-pasting, then we can use another event: oncopy.


  
  ## Drag and drop
  Drag’n’Drop algorithm
The basic Drag’n’Drop algorithm looks like this:

On mousedown – prepare the element for moving, if needed (maybe create a clone of it, add a class to it or whatever).
Then on mousemove move it by changing left/top with position:absolute.
On mouseup – perform all actions related to finishing the drag’n’drop.
  
  there are a few events like Drazone , dropable , drable . We can also use mousemove to do drag and drop
  
  do a mousemove event
  hide the object
  and get the x ,y of mouse pointer
  if it matches the x ,y of drop zone , do action
  
  Today :- pointer types includes all mouse and touch events , so odnt code against mouse types , use pointer types events 
  
  
  Pointer event properties
Pointer events have the same properties as mouse events, such as clientX/Y, target, etc., plus some others:

pointerId – the unique identifier of the pointer causing the event. --> useful in multi touch interfaces 

Browser-generated. Allows us to handle multiple pointers, such as a touchscreen with stylus and multi-touch (examples will follow).

pointerType – the pointing device type. Must be a string, one of: “mouse”, “pen” or “touch”.

We can use this property to react differently on various pointer types.

isPrimary – is true for the primary pointer (the first finger in multi-touch).
  
  
  Some pointer devices measure contact area and pressure, e.g. for a finger on the touchscreen, there are additional properties for that:

width – the width of the area where the pointer (e.g. a finger) touches the device. Where unsupported, e.g. for a mouse, it’s always 1.
height – the height of the area where the pointer touches the device. Where unsupported, it’s always 1.
pressure – the pressure of the pointer tip, in range from 0 to 1. For devices that don’t support pressure must be either 0.5 (pressed) or 0.
tangentialPressure – the normalized tangential pressure.
tiltX, tiltY, twist – pen-specific properties that describe how the pen is positioned relative the surface.
  
  
  browser's native drag drop events :- on dragstart 
  
  Slider is not available in native vriwsers , we can use pointer events to enable that 
  
  Pointer capture is an event when called is attached to element and targets that element only 
  
  The main method is:

elem.setPointerCapture(pointerId) – binds events with the given pointerId to elem. After the call all pointer events with the same pointerId will have elem as the target (as if happened on elem), no matter where in document they really happened.
  
  In other words, `elem.setPointerCapture(pointerId)` retargets all subsequent events with the given pointerId to elem.

The binding is removed:

automatically when pointerup or pointercancel events occur,
automatically when elem is removed from the document,
when elem.releasePointerCapture(pointerId) is called.
  
  ## keyboard keyup and keydown
  better to use input event on input types rather than keyboard events as there are other kinds on devices 
  
  with keydown and key up we get event.code and event.key 
  difference is if we press shift + Z we get event code as KeyZ and event key as cpas Z and normal z will give key as smallcaps z and code is same
  For events triggered by auto-repeat, the event object has event.repeat property set to true. :- this is when a key has been pressed for a long time 
  
  we can implement component where we do not allow any other chaarcters to be passed apart from phone numbee (just return false if key pressed is anything other than number )
  
  
  ## Scroll
  we can attach an event listener with scroll as :--
  
  `window.addEventListener('scroll', function() {
  document.getElementById('showScroll').innerHTML = window.pageYOffset + 'px';
});`
  
  We can’t prevent scrolling by using event.preventDefault() in onscroll listener, because it triggers after the scroll has already happened.
 
  
  But we can prevent scrolling by event.preventDefault() on an event that causes the scroll, for instance keydown event for pageUp and pageDown.
  
  infinite scrool is done by hiding scroll and adding data on scroll event of the page 
  
  we can also load images on scroll by changing the src to certain value (store in dayta src and use that ) 
  
  ## form properties
  Document forms are members of the special collection document.forms.

That’s a so-called “named collection”: it’s both named and ordered. We can use both the name or the number in the document to get the form.

document.forms.my; // the form with name="my"
document.forms[0]; // the first form in the document
  
  all elements inside a form is available in form.element collection
  the elements property also works for <fieldset>.
  we can use shorthand as form.elements.login to form.login
  
  use input.value and textarea.value to access the values inside these html fields
  
  <select> has 3 important properties 
    select.options
    select.value
    select.selectedIndex
    
    
    Unlike most other controls, <select> allows to select multiple options at once if it has multiple attribute. This attribute is rarely used, though.
    
    new Option
In the specification there’s a nice short syntax to create an <option> element:

`option = new Option(text, value, defaultSelected, selected);`
    
    An element receives the focus when the user either clicks on it or uses the Tab key on the keyboard. There’s also an autofocus HTML attribute that puts the focus onto an element by default when a page loads and other means of getting the focus.
    
    
    ## Events focus/blur
The focus event is called on focusing, and blur – when the element loses the focus.
    
    ## Methods focus/blur
Methods elem.focus() and elem.blur() set/unset the focus on the element.
    
    
    
  Please note that we can’t “prevent losing focus” by calling event.preventDefault() in onblur, because onblur works after the element lost the focus.
    
    
    
    **Allow focusing on any element: tabindex**
By default, many elements do not support focusing.

The list varies a bit between browsers, but one thing is always correct: focus/blur support is guaranteed for elements that a visitor can interact with: <button>, <input>, <select>, <a> and so on.

On the other hand, elements that exist to format something, such as <div>, <span>, <table> – are unfocusable by default. The method elem.focus() doesn’t work on them, and focus/blur events are never triggered.

This can be changed using HTML-attribute tabindex.
    
    
    There are two special values:

tabindex="0" puts an element among those without tabindex. That is, when we switch elements, elements with tabindex=0 go after elements with tabindex ≥ 1.

Usually it’s used to make an element focusable, but keep the default switching order. To make an element a part of the form on par with <input>.

tabindex="-1" allows only programmatic focusing on an element. The Tab key ignores such elements, but method elem.focus() works.
    
    Events focus and blur do not bubble.but propagate down on the capturing phase.
    
    there are focusin and focusout events – exactly the same as focus/blur, but they bubble.

Note that they must be assigned using elem.addEventListener, not on<event>.
    
    The current focused element is available as document.activeElement.
    
    ## Events: change, input, cut, copy, paste
    
    ## Event: change
The change event triggers when the element has finished changing.

For text inputs that means that the event occurs when it loses focus.

For instance, while we are typing in the text field below – there’s no event. But when we move the focus somewhere else, for instance, click on a button – there will be a change event:
    
    Event: input
The input event triggers every time after a value is modified by the user.

Unlike keyboard events, it triggers on any value change, even those that does not involve keyboard actions: pasting with a mouse or using speech recognition to dictate the text.
    
    Can’t prevent anything in oninput
    
    ## clipboard events:-
    
    We also can use event.preventDefault() to abort the action, then nothing gets copied/pasted.

For instance, the code below prevents all such events and shows what we are trying to cut/copy/paste:


    
    ## Forms: event and method submit
    The submit event triggers when the form is submitted, it is usually used to validate the form before sending it to the server or to abort the submission and process it in JavaScript.

The method form.submit() allows to initiate form sending from JavaScript. We can use it to dynamically create and send our own forms to server.
    
    There are two main ways to submit a form:

The first – to click <input type="submit"> or <input type="image">.
The second – press Enter on an input field.
    
    Relation between submit and click
When a form is sent using Enter on an input field, a click event triggers on the <input type="submit">.

That’s rather funny, because there was no click at all.
    
    
    To submit a form to the server manually, we can call form.submit().

Then the submit event is not generated. It is assumed that if the programmer calls form.submit(), then the script already did all related processing.
    
    ## Page: DOMContentLoaded, load, beforeunload, unload
    
    The lifecycle of an HTML page has three important events:

DOMContentLoaded – the browser fully loaded HTML, and the DOM tree is built, but external resources like pictures <img> and stylesheets may not yet have loaded.
load – not only HTML is loaded, but also all the external resources: images, styles etc.
beforeunload/unload – the user is leaving the page.
    
    Each event may be useful:

DOMContentLoaded event – DOM is ready, so the handler can lookup DOM nodes, initialize the interface.
load event – external resources are loaded, so styles are applied, image sizes are known etc.
beforeunload event – the user is leaving: we can check if the user saved the changes and ask them whether they really want to leave.
unload – the user almost left, but we still can initiate some operations, such as sending out statistics.
    
    The DOMContentLoaded event happens on the document object.

We must use addEventListener to catch it:

`document.addEventListener("DOMContentLoaded", ready);
// not "document.onDOMContentLoaded = ..."`
    
    DOMContentLoaded and scripts
When the browser processes an HTML-document and comes across a <script> tag, it needs to execute before continuing building the DOM. That’s a precaution, as scripts may want to modify DOM, and even document.write into it, so DOMContentLoaded has to wait.

So DOMContentLoaded definitely happens after such scripts:
    
    DOMContentLoaded and styles
External style sheets don’t affect DOM, so DOMContentLoaded does not wait for them.

But there’s a pitfall. If we have a script after the style, then that script must wait until the stylesheet loads:
    
    ## window.onload
    The load event on the window object triggers when the whole page is loaded including styles, images and other resources. This event is available via the onload property.

The example below correctly shows image sizes, because window.onload waits for all images:
    
    ## window.onunload
    
    window.onunload
When a visitor leaves the page, the unload event triggers on window. We can do something there that doesn’t involve a delay, like closing related popup windows.

The notable exception is sending analytics.

Let’s say we gather data about how the page is used: mouse clicks, scrolls, viewed page areas, and so on.

Naturally, unload event is when the user leaves us, and we’d like to save the data on our server.

There exists a special navigator.sendBeacon(url, data) method for such needs, described in the specification https://w3c.github.io/beacon/.

It sends the data in background. The transition to another page is not delayed: the browser leaves the page, but still performs sendBeacon.
    
    The request is sent as POST.
We can send not only a string, but also forms and other formats, as described in the chapter Fetch, but usually it’s a stringified object.
The data is limited by 64kb.
    
    
    ## window.onbeforeunload
    If a visitor initiated navigation away from the page or tries to close the window, the beforeunload handler asks for additional confirmation.

If we cancel the event, the browser may ask the visitor if they are sure.

You can try it by running this code and then reloading the page:
    
    `window.onbeforeunload = function() {
  return false;
};`
    For historical reasons, returning a non-empty string also counts as canceling the event. Some time ago browsers used to show it as a message, but as the modern specification says, they shouldn’t.
    
    Here’s an example:

`window.onbeforeunload = function() {
  return "There are unsaved changes. Leave now?";
};`
The behavior was changed, because some webmasters abused this event handler by showing misleading and annoying messages. So right now old browsers still may show it as a message, but aside of that – there’s no way to customize the message shown to the user.
    
    ## readyState
    The document.readyState property tells us about the current loading state.

There are 3 possible values:

"loading" – the document is loading.
"interactive" – the document was fully read.
"complete" – the document was fully read and all resources (like images) are loaded too.
    
    There’s also the readystatechange event that triggers when the state changes, so we can print all these states like this:
    
    ## Defer and Async
    
    when script tag is placed ta top , it might miss the event listeners tag 
    putting at the end , does not block html , but it starts downloading after all htmk
    
    defer :- will download in parallel
    does not block page 
    and fires DOMContentLoaded event
    
    Deferred scripts keep their relative order, just like regular scripts.
    
    The defer attribute is only for external scripts
The defer attribute is ignored if the <script> tag has no src.
    
    async
The async attribute is somewhat like defer. It also makes the script non-blocking. But it has important differences in the behavior.

The async attribute means that a script is completely independent:

The browser doesn’t block on async scripts (like defer).
Other scripts don’t wait for async scripts, and async scripts don’t wait for them.
DOMContentLoaded and async scripts don’t wait for each other:
DOMContentLoaded may happen both before an async script (if an async script finishes loading after the page is complete)
…or after an async script (if an async script is short or was in HTTP-cache)

    
    Dynamic scripts
There’s one more important way of adding a script to the page.

We can create a script and append it to the document dynamically using JavaScript:

let script = document.createElement('script');
script.src = "/article/script-async-defer/long.js";
document.body.append(script); // (*)
    
    That is:

They don’t wait for anything, nothing waits for them.
The script that loads first – runs first (“load-first” order).
This can be changed if we explicitly set script.async=false. Then scripts will be executed in the document order, just like defer.
    
    In practice, defer is used for scripts that need the whole DOM and/or their relative execution order is important.

And async is used for independent scripts, like counters or ads. And their relative execution order does not matter.
    
    ## Resource loading: onload and onerror
    
    There are two events for it:

onload – successful load,
onerror – an error occurred.
    
    We can load it dynamically, like this:

let script = document.createElement('script');
script.src = "my.js";

document.head.append(script);
    
    script.onload
The main helper is the load event. It triggers after the script was loaded and executed.
    
    
    script.onerror
Errors that occur during the loading of the script can be tracked in an error event.

For instance, let’s request a script that doesn’t exist:
    
    Crossorigin policy
There’s a rule: scripts from one site can’t access contents of the other site. So, e.g. a script at https://facebook.com can’t read the user’s mailbox at https://gmail.com.

Or, to be more precise, one origin (domain/port/protocol triplet) can’t access the content from another one. So even if we have a subdomain, or just another port, these are different origins with no access to each other.
    
    Similar cross-origin policy (CORS) is enforced for other types of resources as well.

To allow cross-origin access, the <script> tag needs to have the crossorigin attribute, plus the remote server must provide special headers.

There are three levels of cross-origin access:

No crossorigin attribute – access prohibited.
crossorigin="anonymous" – access allowed if the server responds with the header Access-Control-Allow-Origin with * or our origin. Browser does not send authorization information and cookies to remote server.
crossorigin="use-credentials" – access allowed if the server sends back the header Access-Control-Allow-Origin with our origin and Access-Control-Allow-Credentials: true. Browser sends authorization information and cookies to remote server.
    
    
    ## Mutation observer
    MutationObserver is a built-in object that observes a DOM element and fires a callback when it detects a change.

We’ll first take a look at the syntax, and then explore a real-world use case, to see where such thing may be useful.
    
    MutationObserver is easy to use.

First, we create an observer with a callback-function:

let observer = new MutationObserver(callback);
And then attach it to a DOM node:

observer.observe(node, config);
config is an object with boolean options “what kind of changes to react on”:

childList – changes in the direct children of node,
subtree – in all descendants of node,
attributes – attributes of node,
attributeFilter – an array of attribute names, to observe only selected ones.
characterData – whether to observe node.data (text content),
    
    There’s a method to stop observing the node:

observer.disconnect() – stops the observation.
When we stop the observing, it might be possible that some changes were not yet processed by the observer. In such cases, we use

observer.takeRecords() – gets a list of unprocessed mutation records – those that happened, but the callback has not handled them.
    
    can be used to remove ads from page :- keep listening for ads on documen and remove them
    
    sample : -
    `<div contentEditable id="elem">Click and <b>edit</b>, please</div>

<script>
let observer = new MutationObserver(mutationRecords => {
  console.log(mutationRecords); // console.log(the changes)
});

// observe everything except attributes
observer.observe(elem, {
  childList: true, // observe direct children
  subtree: true, // and lower descendants too
  characterDataOldValue: true // pass old data to callback
});
</script>`
    
    ## Selection and Range
    
    Range
The basic concept of selection is Range, that is essentially a pair of “boundary points”: range start and range end.

A Range object is created without parameters:

let range = new Range();
    
    ## selection and range 
    selectin and range are apis to transform selected text 
    
    ## Event Loops and Microtask
    
    


    
    
