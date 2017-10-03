---
title: An introduction to JavaScript Asynchronous Programming - Part 1 | General Definitions and Timers
date: 2017-10-02
---

If you want to work in web development, mastering how asynchronous things are handled in JavaScript is a must. I had a tough time understanding asynchronous programming. Not because it is a complex concept, but because I did not find a good place that puts it all together in a simple way. Here’s my humble attempt to do that. My idea is to introduce it small example after small example. Let’s start with some generalities.

### General Definitions
JavaScript is single threaded but has a non blocking Input/Output (I/O) stream. What single threaded means is that it can only process one thing at a time. What non blocking I/O means is that whenever our JS program sends information to the outside world (makes an HTTP request, asks for information in a Database, etc), it never waits for a response to resume execution of the rest of the program.
JavaScript also has the ability to listen to events from the outside world, such as when a page is loaded, a timer ends, a DOM element is clicked or a response from an HTTP request arrives. If we want to do something when that event occurs, we need to associate it with a function that will be called when that event is received. This function is know as “callback”.

#### The Event Loop
As I said before, JavaScript can only execute one thing at a time. However, an event can happen anytime. In particular, one or several events can arrive when the interpreter is still executing some other instruction. That’s why the JS engine needs some sort of algorithm to handle the execution of things. This algorithm is called the Event Loop, and it basically consists in JS behaving the following way:

* If we load JS code, the interpreter will begin executing it until there is no more code to run.

* Sometimes it will send information to the outside world, this is an extra instruction such like everything else. The interpreter will not wait for something to come back and will keep on executing pending instructions.

* Sometimes it will receive an event from the outside world. If that event has an associated callback function (A function we need to run when that event occurs), the execution of that callback will be added to an execution queue. 

* When the current block has finished being executed, JS will look at the next function in its queue and start executing that.

#### How to think about asynchronous programming
From the above, we can build some rules of the thumb to deal with asynchronous stuff:

1. When we are requesting information from the outside world, we need to know what types of future responses we can get.

2. We need to listen to every one of those types and provide an adequate callback function for each to handle it accordingly. Everything we want to do after the event happens needs to be be directly or indirectly invoked by that callback.

3. We need to be aware that the response will be handled when JS has received the message and its single thread of execution has handled the previous blocks in its queue. 

### Timers
Our first contact with asynchronous code will be through using timers. Timers let us "delay" the execution of a function by a certain time. By delay, I mean that at a specific time, the function will be added to the execution queue, it won’t be necessarily executed right away.

We will be using timers to see all of the concepts above in action. Please code along with me in the following examples. The code should be written in a `.js` file and then run with node.

#### Example 1
We are going to start with the `setTimeout` function. This function takes another function (its callback) as a first argument and adds it to the execution queue after a certain amount of milliseconds defined in its second argument. Lets see it in action: 

```javascript
setTimeout(() => console.log('I am inside the callback'), 5000);
console.log('I am inside the first execution block');
```
Here is what happens within the JS interpreter:

1. It executes the first function, setting a 5 second timer that will add the callback to the execution queue.

2. It resumes the current block, executing the second console.log statement.

3. After doing that, it has nothing else in that block or in its execution queue, so it stays waiting for events.

4. The timer is done and the callback is executed.

The result of running the code will be the following:

```
I am inside the first execution block
(~ 5 seconds later)
I am inside the callback
```

#### Example 2

Now we will see the Event Loop at work:

```javascript
setTimeout(() => console.log('I count to ten milliseconds'), 10);
setTimeout(() => console.log('I do not count at all'), 0);
for(var i = 1; i < 100000 ; i++) { console.log(i) };
```

Which will throw:

```
1
…
999999
I do not count at all
I count to ten milliseconds
```

This is what happens when executing the code:

1. The first setTimeout is sent.

2. The second setTimeout is sent, and as it finishes (almost) immediately, JS adds the corresponding callback to the execution queue.

3. The for block starts executing, logging the numbers form 1 to 99999.

4. Assuming the computer takes more than 10 milliseconds to print all numbers (which is my case), the second timeout is finished and its callback is added to the queue.

5. The for loop ends, now we have no more statements to run on our file. JS will look to its execution queue to see what’s next in the queue (log “I do not count at all”).

6. After executing the callback, JS looks again to its execution queue to execute the last callback (log “I count to ten milliseconds”). 

#### Example 3
Another cool timer function is `setInterval`. It will add a callback to the execution queue every fixed period of time.

Let’s have some fun with it:

```javascript
const travelTerminal = function() {
    console.log(Array(steps + 1).join(' ') + '|');
    switch(direction) {
      case 'right':
         steps ++;
        break;
      case 'left':
        steps --;
        steps = steps  < 0 ? 0 : steps;
        break;
    }
  }
 
 const changeDirection = function() {
    nextDirection = {
      right: 'left',
      left: 'right',
    };
    direction = nextDirection[direction];
 };
 
 let steps = 0;
 let direction = 'right';
 setInterval(changeDirection, 5000);
 setInterval(travelTerminal, 500);
```

### Wrapping Up
We’ve seen the main concepts of asynchronous programming and played a little bit with timers. If you have managed to grasp these concepts, the rest will be quite easy because it consists of pretty much the same idea but with different names and data structures. 

There is more to timers than what I explained here. For more information you should check the [MDN docs](https://developer.mozilla.org/en-US/Add-ons/Code_snippets/Timers)
Next time we will be dealing with events in the DOM.

