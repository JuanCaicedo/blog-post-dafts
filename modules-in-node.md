# Multiple modules in Node.js

I'm one of the organizers for [NodeSchoolDC](http://nodeschool.io/washingtondc/)
and at our meetups I see people get stuck on the exact same problem all the
time. That problem is exercise 6, `Make it Modular`, which is the first time
that you have to write a program that is split across two different files. I
wanted to go ahead and explain it here to clear things up.

## What does `require` do?

The simple version is that `require` is a global function set up in Node that
finds another file on the system and executes it. It then gives you back
whatever is the value of that file's `module.exports`.

```javascript
/* my-module.js */
module.exports = 'hello world';

/* main.js */
var imported = require('./my-module');
console.log(imported); // hello-world
```

This value could be anything, including a function.

```javascript
/* my-module.js */
module.exports = function(a, b) {
  return 'first: ' + a + ', second: ' + b;
};

/* main.js */
var myFunction = require('./my-module');

var result = myFunction(1, 2);
console.log(result); // first: 1, second: 2
```

You don't have to define the variable or function right then, it can be defined
earlier.

```javascript
/* my-module.js */
var myVar = 'hello-world';
module.exports = myVar;

/* or also */
function myFunction(a, b) {
  return 'first: ' + a + ', second: ' + b;
}
module.exports = myFunction;
```

## Scope inside a module

For now, you don't need to know any details about javascript closures, you just
need to know that when you `require` a file and get back its `module.exports`,
it has access to any variables defined in that file.

```javascript
/* my-module.js */
var b = 'inside';

module.exports = function(a) {
  return 'first: ' + a + ', second: ' + b;
};

/* main.js */
var func = require('./my-module');

var result = func('outside');
console.log(result); // first: outside, second: inside
```

However, the file that requires that module doesn't have access to any of those
variables.

```javascript
/* my-module.js */
var b = 'inside';

module.exports = function(a) {
  return 'first: ' + a + ', second: ' + b;
};

/* main.js */
var func = require('./my-module');

var result = func(1);
console.log(b); // undefined
```

## Working with callbacks

Functions are first class citizens in Javascript. That means that you can assign
a function to a variable just like any other value. That includes the ones you
pass into a function.

```javascript
var myFunction = function(a, cb) {
  cb('first', a);
}

myFunction('second', function(myFirst, mySecond) {
  console.log('first: ', myFirst, ', second: ', mySecond)
  // => first: first, second: second
});
```

The code above can really easily be split up into multiple modules.

```javascript
/* my-module.js */
var myFunction = function(a, cb) {
  cb('inside', a);
}

module.exports = myFunction;

/* main.js*/
var myModule = require('./my-module');

myModule('outside, function(myFirst, mySecond) {
  console.log('first: ', myFirst, ', second: ', mySecond)
  // => first: inside, second: outside
});
```

In Node, callbacks are always written to take an error as the first parameter.
This makes it easy to handle errors inside your calling code, which makes for
more maintainable programs. The above code in a more conventional style would
look like this:

```javascript
/* my-module.js */
var myFunction = function(a, cb) {
  cb(null, 'inside', a);
}

module.exports = myFunction;

/* main.js*/
var myModule = require('./my-module');

myModule('outside', function(null, myFirst, mySecond) {
  if (error) {
    return console.log('Error!:', error);
  }
  console.log('first: ', myFirst, ', second: ', mySecond)
  // => first: inside, second: outside
});
```

## Multiple callbacks

The other thing that makes this exercise tricky is that you are actually dealing
with two levels of callbacks.

Your `main.js` file needs to define a callback which your `my-module.js` will
call after it finishes. This callback will be the last thing called, so you
need to say what to do with an error if it exists and also what to do with the
list of filtered files.

Your `my-module` will also need to define a callback for `fs.readdir` to call
after that finishes. Inside that callback, you will need to "bubble" any errors.
That means that if an error exists, you can call the callback you've received
from `main.js` with that error. Otherwise, you can filter the files you've
received and then pass `null` (no error) and the filtered files to the callback
from `main.js`.

To reiterate, the key thing here is that the callback passed to `my-module`
needs to get called inside the callback given to `fs.readdir`.

## Conclusion

The concept of splitting code across different modules can be pretty daunting at
first, but it's an incredibly useful tool for organizing your code. It helps to
know that there's nothing complicated going on, just simple javascript
functions. In fact, the patterns you just learned are the same ones used in all
Node modules you may find in the wild, and even in the Node core itself!

I would recommend you work through this exercise on your own (you're smart, you
can do it!), but afterwards if you're interested in seeing my solution, it's
posted [here](https://gist.github.com/JuanCaicedo/5d45403382d998508239ea6fb6837d13).
