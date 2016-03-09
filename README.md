## PureState.js

A stupid, simple, clean, ridiculously small state management library that is supposed to cover every use case of complex solutions such as Flux/Reflux/etc without the overengineering. See the rant below.

```javascript
var state = require("./purestate");

// Stateful variables are just JS values wrapped with a `state` call
var x = state(0);

// This reads a stateful variable; read as "console.log(x)"
console.log(x());

// This writes a stateful variable; read as "x = 1"
x(1);

// Stateful values can depend on other stateful values 
var y = state(() => x() + 1);
var z = state(() => [x(), y(), x()+y()]);

console.log(x());
console.log(y());
console.log(z());

// Those above output "1", "2", "[1, 2, 3]"

// If you change a stateful value, all values that depend on it are updated.

x(10); // sets x to 10

console.log(x());
console.log(y());
console.log(z());

// Now those above output "10", "20", "[10, 20, 30]"
```

The idea is simple. 99% of your program should consist of pure functions and values. The 1% that doesn't should be written as if it did. Then, during runtime, allow yourself to mutate those "stateful pure values" without fear, and let PureState deal with recomputing every other value that depends in it doing the minimal amount of work necessary. And never again deal with state directly.

See `example_counter.html` and `example_todo.html` for a quick example of how that simple concept is powerful enough to, for example, write interactive MVC web applications in very simple, pure way. That is just to show the idea - of course those examples could be much better with, for example, VirtualDOM or React instead of strings for rendering.

### The rant

After so many years, seems like the public is finally learning to appreciate the benefits of purity and immutability. The evolution from the times of jQuery, through Angular, to React, show it. React nails the issue of rendering views, but it still has issues with state. The initial proposal of Flux had clear weakenesses and the market slowly gravitated torwards better solutions.

FRP-inspired answers such as Redux are the hot thing now. I think those are the wrong approach. Folds ("reducers") are merely simulating state through the continuous application of a function to a list-like structure. The fact it is pure under the hoods doesn't make your state less stateful. It just makes it more awkward. Your giant reducer function is no better than just mutating variables on your heap, and zipping streams is just a fancy way to combine variables. Except now you have a reversible history. It is overengineering for no real benefit.

In my understanding, the solution to state is simple: stop fighting it. State is not bad - what is bad is the century-old practice to use state where it is not needed. As long as you do it right - i.e., keep 99% of your application pure, identify the minimal amount of data that needs to be mutated and only alter it - then it is perfectly healthy to treat state as a first-class citizen.