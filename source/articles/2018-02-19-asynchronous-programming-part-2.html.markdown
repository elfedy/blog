---
title: An introduction to JavaScript Asynchronous Programming - Part 2 | DOM Events
date: 2018-02-19
---


[Last time](/2017/10/02/asynchrounous-programming-part-1.html) we had fun with time functions. However, there are much more important and more powerful concepts related to asynchronous code. One of them is DOM Events.

When using JS in the context of a Browser, we can be aware of lots of different things that happen during the user’s interaction with the web page. Most importantly, through the use of JS code, we have the ability to run code whenever an interaction or significant event happens.

In browser terminology, all the things happening that we can do something about are called events. Events are fired within the browser window and tend to be attached to a specific element that resides in it. In other words, every event has a "target". 

Some examples of this are:

* A user clicking on a link will fire a `click` event associated with that link.
* A user scrolling the page wll fire a `scroll` event targeting the document element.
* User typing a key on the keyboard will fire the `keydown`, `keypress` and `keyup` events on the document element.
* A form being submitted will fire a `submit` event targeting that form.
* A resource (e.g.: an image) that has finished loading will fire a `load` event targeting the document element.

In order to do whatever we want with events, we need to take the following steps:

1. Identify the browser event that we want to react to and what DOM object does the event target when it happens.
2. Add an event listener to that event and specify a callback to be added to the execution queue whenever the event happens. There are several ways to do this, but the most common one is calling the [addEventListener()](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener) function on the element that the event will target.
3. Whenever that events occurs on a user interaction with the web page, the callback will be run and will be passed an [Event](https://developer.mozilla.org/en-US/docs/Web/API/Event) object as an argument to the callback. This object has lots of properties with information about an event that we have at our disposal within the callback.

### A simple example

Using DOM events lets us do countless of things on web pages. Lets see some examples to go through these concepts and exercise our async muscule in the process.

Although I will show most of the examples here, I recommend you to type them yourself into `.html` files and the running them using your favorite browser.

We will start by creating buttons that change the color of a box.

```html
<div class="box">
</div>

<button class="turn-red">Turn the box Red!</button>
<button class="turn-blue">Turn the box Blue!</button>

<style>
  .box {
    height: 100px;
    width: 100px;
    margin: 10px;
    background-color: #000;
  }
</style>

<script>
  // defining the callbacks here
  const turnRed = () => {
    box = document.querySelector('.box');
    box.style = 'background-color: red;'
  }

  const turnBlue = () => {
    box = document.querySelector('.box');
    box.style = 'background-color: blue;'
  }

  // Adding the corresponding event listener to each button
  const turnRedButton = document.querySelector('.turn-red');
  turnRedButton.addEventListener('click', turnRed);

  const turnBlueButton = document.querySelector('.turn-blue');
  turnBlueButton.addEventListener('click', turnBlue);
</script>
```
<div class="code__sample">
  <div class="box__single">
  </div>

  <button class="turn-red">Turn the box Red!</button>
  <button class="turn-blue">Turn the box Blue!</button>

  <style>
    .box__single {
      height: 100px;
      width: 100px;
      margin: 10px;
      background-color: #000;
    }
  </style>

  <script>
    // defining the callback here
    const turnRed = () => {
      box = document.querySelector(".box__single");
      box.style = 'background-color: red;'
    }

    const turnBlue = () => {
      box = document.querySelector(".box__single");
      box.style = 'background-color: blue;'
    }

    // Adding an event listener to the button
    const turnRedButton = document.querySelector(".turn-red");
    turnRedButton.addEventListener("click", turnRed);

    const turnBlueButton = document.querySelector(".turn-blue");
    turnBlueButton.addEventListener("click", turnBlue);
  </script>
</div>


Here is the thought process I described above:

1. We want to react to the user clicking the button by changing the box color to red or blue depending on the button he/she clicks.

2. We create a function for each button that looks for the box and changes it to the corresponding color. We then add this function as a callback to be run in reaction to the `click` action that has each button as the target. We do this by calling the `addEventListener()` function on the object we want to do this to and passing the event name and the callback as arguments.

3. We click one of the buttons and the magic happens…

### The Event object
In the last example we used a callback that took no arguments. The event handler actually receives an [Event](https://developer.mozilla.org/en-US/docs/Web/API/Event) object as an argument that we can use to get data about the event. Inside the callback, we then have access to that data to do anything we want.

```html
<div class="boxes">
  <div class="box">
    Turn me red!
  </div>
  <div class="box">
    No, turn ME red!
  </div>
  <div class="box">
    Don't listen to those boxes! Turn me red instead!
  </div>
</div>

<div class="info"></div>

<script>
  const turnAllBlue = () => {
    document.querySelectorAll('.box').forEach((box) => {
      box.style = 'background-color: blue;'
    });
  }

  // Notice we are requiring an argument (e) for the callback
  const turnTargetRed = (e) => {
    /* Now we have access to the event thorugh the e object
      Look at your console and see all the events properties
      for yourself */
    console.log(e);

    // We can get the event target
    box = e.target;

    turnAllBlue();
    box.style = 'background-color: red;'

    // And do whatever we want with the event data
    const info = `Your event was of type ${e.type}, you clicked
      ${e.clientX} X and ${e.clientY} Y coordinates in your browser`

    document.querySelector('.info').innerHTML = info
  }

  /* Adding an event listener to all the boxes so the callback
    triggers whenever any of the boxes is clicked */
  document.querySelectorAll('.box').forEach((box) => {
    box.addEventListener('click', turnTargetRed);
  });
</script>

<style>
  .box {
    height: 100px;
    width: 100px;
    margin: 10px;
    background-color: blue;
    color: #fff;
    text-align: center;
    padding: 5px;
  }

  .boxes {
    display: flex;
  }
</style>
```

<div class="code__sample">
  <div class="boxes">
    <div class="box">
      Turn me red!
    </div>
    <div class="box">
      No, turn ME red!
    </div>
    <div class="box">
      Don't listen to those boxes! Turn me red instead!
    </div>
  </div>

  <div class="info"></div>
</div>

<script>
  const turnAllBlue = () => {
    document.querySelectorAll('.box').forEach((box) => {
      box.style = 'background-color: blue;'
    });
  }

  const turnTargetRed = (e) => {
    /* Now we have access to the event thorugh the e object
      Look at your console and see all the events properties
      for yourself */
    console.log(e);

    // We can get the target
    box = e.target;

    turnAllBlue();
    box.style = 'background-color: red;'

    // And do whatever we want with the event data
    const info = `Your event was of type ${e.type}, you clicked
      ${e.clientX} X and ${e.clientY} Y coordinates in your browser`

    document.querySelector('.info').innerHTML = info
  }

	/* Adding an event listener to all the boxes so the callback
    triggers whenever any of the boxes is clicked */
  document.querySelectorAll('.box').forEach((box) => {
    box.addEventListener('click', turnTargetRed);
  });
</script>

<style>
  .box {
    height: 100px;
    width: 100px;
    margin: 10px;
    background-color: blue;
    color: #fff;
    text-align: center;
    padding: 5px;
  }

  .boxes {
    display: flex;
  }
</style>

### Things are still single threaded..
All that we seen here is asynchronous code and the same rules apply for it. In particular, we are still running code in a single threaded way. When an event that has a listener associated to it occurs, the callback is added to the execution queue and run when its turn comes.

A quick way to prove this is to go to the last example (either in this article or in your own .html file), open the web inspector’s console and print a long series of numbers

```javascript 
for(let i = 1; i < 100000 ; i++) { console.log(i) };
```

while the numbers are printing, try clicking on a red box. You will see the the box does not change its color until all the numbers are printed. That is because when you fire the “click” event, the code is not run automatically. The corresponding callback is added to the execution queue and will be run whenever the browser’s engine finishes running everything it needs to run before the callback gets his turn to be executed. 

### Callbacks within callbacks:
Callbacks are just regular JS functions, so we can trigger more asynchronous code inside them. 

Let’s show this by creating a minion spawner! 

```html
<div>
  <a class="spawner" href="#">Start Spawning!</a>
  <div class="status"></div>
  <div class="minion-container"></div>
</div>

<script>
  const spawner = document.querySelector('.spawner');
  const container = document.querySelector('.minion-container');

  let isSpawning = false

  // Spawn a minion if we are in spawning mode
  const spawnNextMinion = () => {
    if(isSpawning) {
      let minion = document.createElement('div');
      minion.classList.add('minion');
      container.appendChild(minion);
      setTimeout(spawnNextMinion, 1000);
    }
  }

  // Toggle spawning mode and spawn a minion if it applies
  spawner.addEventListener('click', (e) => {
    isSpawning = !isSpawning
    e.preventDefault();
    e.target.innerText = isSpawning ? 
      'Stop Spawning!' : 'Continue Spawning!'
    if(isSpawning) { spawnNextMinion() };
  });

</script>
  
<style>
  .spawner {
  }

  .minion-container {
    display: flex;
    flex-wrap: wrap;
    max-width: 100px;
  }

  .minion {
    height: 10px;
    width: 10px;
    margin: 3px;
    background-color: #fae458;
  }

</style>
```

<div class="code__sample">
  <a class="spawner" href="#">Start Spawning!</a>
  <div class="status"></div>
  <div class="minion-container"></div>
</div>

<script>
  const spawner = document.querySelector('.spawner');
  const container = document.querySelector('.minion-container');
  let isSpawning = false

  const spawnNextMinion = () => {
    if(isSpawning) {
      const minion = document.createElement('div');
      minion.classList.add("minion");
      container.appendChild(minion);
      setTimeout(spawnNextMinion, 1000);
    }
  }

  spawner.addEventListener('click', (e) => {
    isSpawning = !isSpawning
    e.preventDefault();
    e.target.innerText = isSpawning ? "Stop Spawning!" : "Continue Spawning!"
    if(isSpawning) { spawnNextMinion() };
  });

</script>
  
<style>
  .spawner {
  }

  .minion-container {
    display: flex;
    flex-wrap: wrap;
    max-width: 100px;
  }

  .minion {
    height: 10px;
    width: 10px;
    margin: 3px;
    background-color: #fae458;
  }

</style>

A user can click a button to toggle between spawning and no spawning mode. `spawnNextMinion()` will check what state we are currently in and add itself to the execution queue again in one second in the case we are in spawning mode.

Be warned. This stuff seems pretty cool, but if we take things a little bit further, we have to be careful no to turn our app into a callback party we can’t control (We will see some features to handle callbacks gracefully in future posts) 

### Wrapping up
There are lots of other stuff related to events that you should learn and become familiar with. As this series focuses on asynchronous programming we will not be touching those topics, but the [excellent MDN documentation](https://developer.mozilla.org/en-US/docs/Web/Guide/Events) is a great place to continue reading about them.

