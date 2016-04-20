# Elm-lite, in javascript

## Introduction

Elm is a fantastic programming language. Through functional programming and the
Elm architecture, it facilitates a style of writing web applications, that
allows for code that is modular, reliable, and easy to refactor.

If you are starting a new project that requires a fast interactive front-end and
you are willing to learn a new language and ecosystem, I would strongly
encourage you to choose Elm.

But if you don't have that flexibility, say you want to stay inside the
javascript world and borrow some of the fantastic ideas behind Elm, this blog
post is an attempt to show you how.

In this blog post, we'll build something similar to fantasy football, where
there is a list of all available players and you can draft them into your
fantasy team, but to do it for the US Congress and Senate. The application we'll
be building is available [here](http://www.juancaicedo.com/elm-to-js/) and the
source code is available on [GitHub](https://github.com/JuanCaicedo/elm-to-js).

The most important tool to achieve the architecture we are going for is
functional programming. If you're unfamiliar with FP, I would point you towards
[this book as a wonderful introduction](https://www.gitbook.com/book/drboolean/mostly-adequate-guide/details).

We will use a library Ramda which I find to be the swiss army knife of
functional javascript. It builds on the ideas of utility libraries like Lodash
by exposing methods that are "functional first".

## Wiring the application

The essential wiring we'll use to try to emulate the Elm architecture is by
leveraging a module called [main-loop](https://github.com/Raynos/main-loop).

Main loop allows us to create a mechanism for taking the state of our
application, represented in a javascript object, and run it through a render
function that turns that state into a virtual dom node. We can the attach the
result of that operation and append it on to the page. This will put the visual
representation of the state on the page.

```javascript
var mainLoop = require('main-loop');

var render = function(state) {
  // Coming up soon!
}

var initialState = {
  title: 'test'
};

var loop = mainLoop(initialState, render, vdom);

document.querySelector('#content').appendChild(loop.target);
```

Where the magic happens is that we can call the `update` method on `loop` at any
point and pass it a new state. When we do that, the virtual-dom will take care
of figuring out the which minimal updates need to be applied to the dom to make
the new representation we need.

```javascript
loop.update({
  title: 'new title'
});
```

## Hyperscript Helpers

One of my favorite features of Elm is the ability to represent html in normal
everyday functions. That allows you to run any normal operations (like `map`) to
calculate your views. It also allows you to unit test your views just like any
other function.

```elm
titleView state =
  h1 [] [ text state.title ]
```

Luckily there's a JS library called
[hyperscript-helpers](https://github.com/ohanhi/hyperscript-helpers) that brings
that functionality!

```javascript
var titleView = function(state){
  h1('.title', state.title);
};
```

## Rendering arbitrary lists

### Initial State

Our initial state will have two properties, both arrays of objects,
`availableLegislators` and `selectedLegislators`.

```javascript
/* Initial state */
var initialState =  {
  selectedLegislators: [{
    firstName: 'Juan',
    lastName: 'Caicedo'
  }, {
    firstName: 'Carson',
    lastName: 'Banov'
  }],
  availableLegislators: [{
    firstName: 'Senator',
    lastName: 'One'
  }, {
    firstName: 'Congresswoman',
    lastName: 'Two'
  }]
};
```

This is the initial state that we will pass to the `mainLoop` function, along
with the virtual-dom module and the rendering function we will be describing
next.

### Render functions

Let's start out with the main `render` function. This function only needs to
create a main `div` and then it can delegate the rendering of each of the two
arrays in the state.

```javascript
var render = function (state) {
  return div('.container', [
    legislatorTableView('Your Team', state.selectedLegislators),
    legislatorTableView('Available', state.availableLegislators)
  ]);
};
```

Both of the children to this main `div` will be structurally identically, the
only difference will be the title rendered above the table and the actual
contents of the table. We can encapsulate these similarities by defining a new
function `legislatorTableView` and passing the differences in as parameters.

```javascript
var legislatorTableView = function (title, legislators) {
  return div('.col-xs-6', [
    h1(title),
    table('.table.table-striped', [
      tbody(
        R.map(legislatorView, legislators)
      )
    ])
  ]);
};
```

The `legislatorTableView` renders another `div` (adding some bootstrap styling
to make it take up half of the screen), renders the title we passed in,
initiates another table, and delegates the rendering of each row to another
function `legislatorView`.

```javascript
var legislatorView = function (legislator) {
  return tr('.container-fluid', [
    td('.col-xs-6', legislator.firstName),
    td('.col-xs-6', legislator.lastName)
  ]);
});
```

This function `legislatorView` is the lowest level of our rendering, so it
just takes care of turning a single object into a row of a table with two cells.

With all those view functions, we now have a way of rendering our initial state
into a page with two tables side by side, each with two rows.

## Dealing with updates

We would now like to add functionality so that if you click on a legislator, it
will move that legislator from one table to the other. `hyperscript-helpers`
exposes a simple way to wire up `onclick` handlers, but that means that we need
to be able to trigger an update from inside the rendering code.

`main-loop` gives us the `.update` function, but we need to first instantiate
the loop before we can call it. Since we have to pass the `render` function in
order to instantiate the loop, there's no way the render function can call
`loop.update` directly.

### Actions

In Elm, updating is done through an indirect, but wonderfully elegant mechanism.
Views have access to an "address" to which they can pass an "action". An action
is just a structure that has a type and some data associated with it. These
actions all find their way eventually to an update function that knows how to
calculate a new state given the old state and an action.

We can emulate this mechanism by first creating an `action` function to
instantiate this data structure.

```javascript
var action = function(type, data) {
  return {
    type: type,
    data: data
  };
};
```

### Update events

Next we will need to wire up some unavoidable stateful code. This code will go
in our "unsafe" section of the code, like instantiating the main loop.

We instantiate a new event emitter. Here we're doing it with Node's built-in
`EventEmitter`, since we'll be using browserify to build a client-side bundle,
but the same could be done with the DOM events or with jQuery's events.

```javascript
var emitter = new EventEmitter();
```

The `address` function here just takes care of triggering an event of type
`'update'` and sending the action it gets along as data with the event. It's
worth noting that the addresses used in the Elm architecture are actually part
of a more complex update wiring which is therefore much more robust, but for
these purposes, a simple `address` illustrates the idea.

```javascript
function address(action) {
  emitter.emit('update', action);
};
```

Then we register an event listener on any update event. It will call `update`
passing it the current state of `loop` and action to calculate a new state with.

```javascript
emitter.on('update', function(action) {
  var newState = update(loop.state, action);
  loop.update(newState);
});
```

### Changing state

`update` simply needs to be a function that checks the type of the action and
uses its data to calculate a new state. This is not very complicated at all,
simply some appending to the one list and removing from the other, depending on
the direction. Though it would be possible to return these two properties as a
new state, I prefer to use `R.merge`, which mirrors how Elm "modifies" records.
It creates a new object from the first object and the properties of the second
object.

```javascript
var update = function (state, action) {
  var newSelected;
  var newAvailable;

  // fallback case
  var newState = state;

  if (action.type === 'Drop') {
    newSelected = R.reject(R.equals(action.data), state.selectedLegislators);
    newAvailable = R.append(action.data, state.availableLegislators);

    newState = R.merge(state, {
      selectedLegislators: newSelected,
      availableLegislators: newAvailable
    });
  } else if (action.type === 'Select') {
    newSelected = R.append(action.data, state.selectedLegislators);
    newAvailable = R.reject(R.equals(action.data), state.availableLegislators);

    newState = R.merge(state, {
      selectedLegislators: newSelected,
      availableLegislators: newAvailable
    });
  }
  return newState;
};
```

One great feature of this update mechanism is that it does not ever reference
any state outside the function definition and it is therefore very easy to test!

### Triggering updates from views

We can change the definition of `render` to now also take `address` as a
parameter. We will also `curry` the whole function so that we will be able to
specify `address` without needing to specify `state` (as that will be specified
when `main-loop` calls that function). We will then pass the address function as
well as the type of the action to send whenever a use clicks on a row to
`legislatorTableView`.

```javascript
var render = R.curry(function (address, state) {
  return div('.container', [
    legislatorSelectView(address, 'Drop', 'Your Team', state.selectedLegislators),
    legislatorSelectView(address, 'Select', 'Available', state.availableLegislators)
  ]);
});
```

This also means that we should change how we instantiate the main loop since we
changed the definition of `render`.

```javascript
var loop = mainLoop(initialState, render(address), vdom);
```

The definition of `legislatorTableView` will also change, but not by much. Now
we will just add the two new arguments, and we will pass them to
`legislatorView`. Note that we will also be currying `legislatorView`, so we can
call it with `address` and `type` to partially apply those arguments.

```javascript
var legislatorTableView = function (address, type, title, legislators) {
  return div('.col-xs-6', [
    h1(title),
    table('.table.table-striped', [
      tbody(
        R.map(legislatorView(address, type), legislators)
      )
    ])
  ]);
};
```

And now down at the `legislatorView` level, we will actually wire up a a call to
address. `hyperscript-helpers` allows us to pass an object to any element we are
instantiating and specify attributes that should have. We can specify an
`onclick` handler and when it's called simply call `address`, passing it an
action from our `type` and the current `legislator`. Then `address` will trigger
an update event and our `update` function!

```javascript
var legislatorView = R.curry(function (address, type, legislator) {
  return tr('.container-fluid', {
    onclick: function(ev) {
      address(action('Toggle', action(type, legislator)));
    }
  }, [
    td('.col-xs-6', legislator.firstName),
    td('.col-xs-6', legislator.lastName)
  ]);
});
```

## Requesting data asynchronously

### Tasks

Another fantastic feature of Elm is how it represents asynchronous actions as
data. It does that through a data structure known as a Tasks. They are somewhat
similar to promises, except that they are completely stateless and in order to
execute a task you have to specify what to do if the task succeeds and what to
do if it fails.

This design is extremely useful because it allows you to create the logic of
your stateful asynchronous actions in stateless functions. This allows you to
isolate effects of those operations, keeping the majority of code still
easy to test and refactor.

```javascript
var getJSON = function (url, params) {
  return new Task(function(reject, result) {
    $.getJSON(url, params, result).fail(reject);
  });
```

You can then save the task and execute in in a controlled environment when you
are ready to deal with the results of it.

```javascript
var myTask = getJSON('url', {apikey: 'key'});

myTask.fork(
  function(data) {
    // specify what to do if the task succeed
  },
  function(err) {
    // specify what to do with a json error
  });
```

### Nested actions

To do this, first let's enable our `update` code and `render` code to deal with
nested actions. This will allow us to have multiple top level actions, each
divided into subactions.

First let's change `legislatorView` to send a `Toggle` action with two
subactions, `Drop` and `Select`.

```javascript
var legislatorView = R.curry(function (address, choice, legislator) {
  return tr('.container-fluid', {
    onclick: function(ev) {
      address(action('Toggle', action(choice, legislator)));
    }
  }, [
    td('.col-xs-6', legislator.firstName),
    td('.col-xs-6', legislator.lastName)
  ]);
});
```

And then let's change `update` to handle these nested actions.

```javascript
var update = function (state, action) {
  var newSelected;
  var newAvailable;

  // fallback case
  var newState = state;

  if (action.type === 'Toggle') {
    if (action.data.type === 'Drop') {
      newSelected = R.reject(R.equals(action.data.data), state.selectedLegislators);
      newAvailable = R.append(action.data.data, state.availableLegislators);

      newState = R.merge(state, {
        selectedLegislators: newSelected,
        availableLegislators: newAvailable
      });
    } else if (action.data.type === 'Select') {
      newSelected = R.append(action.data.data, state.selectedLegislators);
      newAvailable = R.reject(R.equals(action.data.data), state.availableLegislators);

      newState = R.merge(state, {
        selectedLegislators: newSelected,
        availableLegislators: newAvailable
      });
    }
  }
  return newState;
};
```

### Task-based JSON request

We discussed a task version of `getJSON` earlier, so now let's go ahead and make
a function for calling it.

```javascript
var fetchLegislators = function(address, jsonTask) {
  jsonTask.fork(
    function(error) {
      address(
        action(
          'PopulateAvailableLegislators',
          action(
            'Error',
            error
          )
        )
      );
    },
    function(response) {
      address(
        action(
          'PopulateAvailableLegislators',
          action(
            'Success',
            R.map(decodeLegislator, response.results)
          )
        )
      );
    }
  );
};
```

`fetchLegislators` is not really a stateless function: it has side-effects
because it is executing an asynchronous task and it doesn't have a return value,
but writing it will still allow us to keep our code more organized.

Note that although the function might seem complicated (25 lines of code!), it's
really just saying "execute a json task, then send an action if it's successful
and a different one if it fails". We pass the results of the response though
a decoder function just because we would like the names of the properties to
match that of our existing legislators (which is not what the api returns).

```javascript
var decodeLegislator = function (legislator) {
  return {
    firstName: legislator.first_name,
    lastName: legislator.last_name
  };
};
```

### Update based off ajax response

Let's now add another section to our `update` function to handle the action
sent by the JSON request.

```javascript
var update = function (state, action) {
  var newSelected;
  var newAvailable;

  // fallback case
  var newState = state;

  if (action.type === 'Toggle') {
    /* ... */
  } else if (action.type === 'PopulateAvailableLegislators') {
    if (action.data.type === 'Success') {
      newState = R.merge(state, {
        availableLegislators: action.data.data
      });
    } else if (action.data.type === 'Error') {
      console.log('Error', error);
    }
  }
  return newState;
};
```

Note that we are explicitly choosing not to do anything with the error, but it
flows through the application just like other actions and we could instead
attach it to the state and use it to render an error message for the user.

### Triggering the JSON request

Now we can add a few more lines to the stateful section of our application to
trigger the request.

```
var dataUrl = ''; // url to request data from'
var dataParams = {}; // api key and other query params
var jsonTask = getJSON(dataUrl, dataParams);
fetchLegislators(address, jsonTask);
```

We instantiate a task to make the json request and then we execute it by passing
it to `fetchLegislators`.

## Conclusion

Elm is a fantastic programming language, and the Elm architecture is a fantastic
way to structure web applications. If it's not possible for you to start using
Elm for your application, it's possible to get some benefits from copying some
ideas from them into your javascript code.

If you like the ideas in this blog post, you should consider looking at
[olmo](https://github.com/kaleidos/olmo) which is a very thorough look at
porting the Elm architecture to javascript. And if you would like to build a
full web application in this style, I would encourage you to look at
[Redux](http://redux.js.org/), which is a Flux implementation which borrows
heavily from the Elm architecture.
