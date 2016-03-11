# Better JSON through streams

Web applications that handle big amounts of data are not easy. Receiving big
amounts data is slow, cumbersome, and prone to failing, so it makes for a
challenging user experience. Sending big amounts of data is also difficult,
particularly when you start introducing some complexities into how that data
needs to be processed before it can be sent.

In this blog post, we're going to look at some of those challenges and model
them with a simple web application.

## Difficulties

### Slow and coupled server responses

Assembling a big HTML page server-side and sending it to the user is slow. Worse
than the actual speed of it is the perception of speed. If a user requests a
page and then has to sit around waiting before they see anything on the screen,
they get unhappy.

Speeding up the initial server response should always be a performance goal,
but it becomes difficult to achieve when you think about the fact that our
response is dictated by the slowest operation we need to perform. If assembling
our large chunk of data involves making a request to another service, our
response is going to be at least as slow as our request/response cycle with
that service.

### Big AJAX request are slow and brittle

This problem with slow server responses is in part why AJAX is such a useful
tool. Instead of needing to wait for a large amount of data to be compiled
before reponding, we can instead respond with a minimal page very quickly, and
then after that make a request to get the data we will need. Then once we have
that data, we can render it to the user.

However, moving the data operation to the front-end doesn't necessarily make it
any faster. In fact, it will likely make it slower, since most clients are
less powerful than most servers, and they are likely to be on lower quality,
slower connections. The user will still have to wait for all of the data to be
ready before getting to see it, and any connection problems will lead to all of
the data being unavailable.

### Multiple AJAX requests increase complexity

A better option could be to break up that large AJAX request into many smaller
requests, then as each one of those reponses finishes, plot its results on
the page. This incremental approach generally leads to a much better user experience
because users can start seeing and interacting with data as soon as it's
available instead of having to wait for all the data. It is also much robust
to lost connections as each request can fail individually without affecting
data which has already been successfully loaded.

Handling many more requests is no simple feat though. Our client-side logic has
to become more complex, because it now needs to know how much data to request.
The server-side logic will also need to be more complex because it will need a
mechanism to tell which data the client has and hasn't received.

## What are we going to build?

We're going to model these difficulties through a web app that renders an
adorable 8-bit pixel art cat with some sun shining down on it.

![image of cat and sun]()

The image is represented in JSON. It is an array of objects that include three
properties, an X position, a Y position, and a color.

Our page will have one big div (representing a grid) broken up into many other
divs (representing rows) broken up into many other divs (representing cells).
For each point, we'll want the cell that corresponds to its X and Y postion to
have a CSS class to style it with a background color.

### Constraints

#### Multiple data sources

The points for the cat are going to be stored in a JSON file on our server. The
points for the sun are going to be stored in a JSON file that we will fetch
over HTTP GitHub.

#### Huge response

Our JSON is going to be impractically big. Unnecessarily big. In fact, each
point in the cat JSON is going to contain 100 uneeded properties just to make
the file pretty big, about 500k. If that doesn't seem unreasonable, don't worry,
the sun points are going to have 600 uneeded properties, making that JSON about
1mb.

#### Bad connections

We want people to be able to use this app even if they have a bad connection.

## Our solution

We are going to build client-side that makes a single request to the server. It
will have a single event handler that tells it how to handle a single point
when it receives one.

Then we're going to build a server-side that streams a JSON response to the client,
making it so that we don't have to wait for all the data to be ready before we
start responding.

And we're going to get the data for that response, all the cat and sun points
we need to send, as a single data stream. We'll obtain that by getting a
separate stream for each of those two sources and then merging them into one.
This will abstract away the fact that one of them is fast (read from disk) and
one of them is slow (over the network).

## Tools

### Oboe.js, event-driven JSON parsing

This is a tool that allows you to parse JSON and use parts of it before the
whole document has been parsed.

```javascript
var source = //url or readable stream
var pattern = //string representing a node in the tree

oboe(source)
  .node(pattern, function(data){
    // you can use the data however you want
  })
```

You register callbacks which will fire when certain nodes of the tree have been
parsed. For example, you can register a callback that will fire for each
object in an array, and when it fires you can use that object.

### Highland.js, a utility belt for streaming

I've always found initiating my own streams to be quite a hassle, and have been
confused by how to work with them once I have one. Highland is library that
makes it easy to initiate, manage, and manipulate streams.

```javascript
var data = ['one', 'two', 'three'];
highland(data)
  .pipe(anotherStream)
```

We'll introduce more highland utility functions as we need them.

### Some Node.js usual suspects

Some other tools we'll use which are fairly common place

- Node.js, v4.2.6
- Express.js
- request


## Receiving a stream response

We will start at the client side and work backwards. Our response is going to be
one big JSON, but we don't want to have to wait for it all to be ready before we
start giving using it.

We give Oboe a url to make a request to. Oboe will make that request and begin
parsing the responsonse.

```javascript
oboe('http://localhost:3000/data')
```

We then register two listeners on that response. The first
will look for any object inside of that JSON response that has three properties,
an x position, a y position, and a color. When it finds an object like that
(in this case it will only find it inside the `pixels` array, but it could be
anywhere in the response), it will run a helper function to fetch the DOM
element corresponding to those coordinates in our grid and then it will add
a CSS class to it to style it with that color.

```javascript
  .node('{x y color}', function(point) {
    var grid = document.querySelector('.grid');
    var cell = getCell(grid, point.x, point.y);
    cell.classList.add(point.color);
  })
```

The second listener will fire once the whole response has been parsed, and it
will simply give the user some visual feedback that all the data has been
loaded.

```javascript
  .done(function(){
    var element = document.querySelector('#status-message');
    element.textContent = 'All data Loaded!';
  })
```

## Sending a streamed response

The server-side application will have a route to serve data to the client. The
We will need a template for our response, which can have whatever metadata or
other non-stream properties we might want to send. The important thing is that
when we get to the pixels array, there's a placeholder string that we will be
able to identify even after the object has been JSON stringifed.

```javascript
router.get('/data', function(req, res) {
  var response = {
    exif: {
      software: 'http://make8bitart.com',
      dateTime: '2015-11-07T15:35:13.415Z',
      dateTimeOriginal: '2015-11-07T00:24:05.776Z'
    },
    pixif: {
      pixels: ["#{pixels}"]
    },
    end: 'test'
  };
```

The `res` object that express sends to the client is a writeable stream, which
means that you can pipe a readable stream directly to it and it will be sent to
the client. However it can only receive a string stream, so we need to convert
our response to that format. We can do so by stringifying our template.

```javascript
var json = JSON.stringify(response);
```

After stringifying, we will split the response so that we have the section
before the `#{pixels}` placeholder and after it. This will be useful later when
adding data to our response.

```javascript
var parts = json.split('"#{pixels}"');
```

Now we can make a new highland stream out of the reponse before the placeholder
and after it.

```
highland([
  parts[0], // before placeholder
  parts[1]  // after placeholder
])
```

We will call `invoke` on that stream to tell each element of the
stream to call it's `split` method with the argument `''`. This will mean that
all elements that flow through our stream will get split up into a stream of
characters.

```
  .invoke('split', [''])
```

Then we will tell highland that we want it to read each of it's elements
sequentially. That is, it will wait for all of the "before" stream to be read
before it starts processing any of the "after" stream. This is extremely
important so that our valid JSON will end up as still valid JSON, otherwise we'd
get a stream of seemily random characters.

```
  .sequence()
```

Now we can send that stream through to the response!

```
  .pipe(res);
```

## Adding data to the response

Now that the client is receiving a streamed response , we can proceed to add
some data to that response. For now let's just add some static data for to make
sure this works as expected.

```javascript
var points = [{
    x: 1,
    y: 2,
    color: 'orange'
  }, {
    x: 2,
    y: 2,
    color: 'orange'
  }];
```

We can make a new highland stream from this array.

```javascript
var pointsStream = highland(points)
```

We will want these to be strings as well, so we can tell our stream to map
each element by `JSON stringify`.

```javascript
  .map(point => JSON.stringify(point))
```

This new stream will be getting fit into where our `"#{points}"` placeholder
used to be, as the elements inside of an array. Since we want to end up with
valid JSON, we will need for the elements of this stream to be comma separated.
We can achieve that by telling our stream to put a comma in between all the
elements that flow through it.

```javascript
  .intersperse(',');
```

Now we can fit this new stream in between our "before" and "after" streams, and
everything will proceed just like we would expect.

```javascript
highland([
    parts[0],
    pointStream,
    parts[1]
  ])
  .invoke('split', [''])
  .sequence()
  .pipe(res);
```

## Getting data from another module

We can reduce the complexity of our route by moving all the code for getting
our point stream to another module.

```javascript
var points = require('../data/points');

/* inside the /data route */
var pointStream = points.getStream()
```

This means that no matter what that module does, as long as it returns a stream
of objects, the rest of our response assembly will still work the same.

## Reading cat points from a file

Let's now work towards using real data sources as opposed to hard-coded objects.
First we will read just the cat points from disk and send them back out of the
`points` module.

First, we will read the data in `cat-points.json` into memory as a stream.

```javascript
function getDataStream() {
  var catPath = path.resolve(__dirname, './cat-points.json');
  var catSource = fs.createReadStream(catPath);
```

The contents of the file will come into our application as a stream of strings
however and ultimately we need a stream of objects. Let's abstract that
conversion to another function called `getPointStream`.

```javascript
  var catStream = getPointStream(catSource);

  return catStream;
}
```

To do this we will leverage Oboe again. Doing so will allow us to read the file
and act on each element of its `pixel` array without waiting for the full file
to be read. We will also need to use a new technique for creating a highland
stream. We will call `highland` and pass it a function taking one argument
`push`. Inside this function, we call `push`, passing it an error or `null`
and a piece of data, as many times as we want and eventually pass it the value
`highland.nil`. Each `push` will push that data through our stream and `nil`
will signal that the stream is over.

```javascript
function getPointStream(sourceStream) {
  return highland(function(push) {
    oboe(sourceStream)
      .node('{x y color}', function(point) {
        push(null, point);
      })
      .done(function() {
        push(null, highland.nil);
      });
  });
}
```

## Reading sun points over the network

The final piece of the puzzle is to add the sun points to our response. Luckily,
the work we've done to abstract our data will make this pretty straight forward.

In `getDataStream`, we will add another readable point stream like our cat
stream, but with a different origin. We will use the `request` library to
request another file from GitHub. This will be a much slower operation both to
start and to complete than reading a file from disk.

Luckily, `request` returns a readable stream, so we can pass it to
`getPointStream`, and Oboe will handle it the same as the result of
`fs.getReadStream`.

```javascript
function getDataStream() {
  /* get cat stream */

  var sunUrl = 'https://raw.githubusercontent.com/JuanCaicedo/better-json-through-streams/master/data/sun-points.json';
  var sunSource = request(sunUrl);
  var sunStream = getPointStream(sunSource);
```

Now we have two streams, `catStream` and `sunStream`, that we would like to
send back out to another module. We can call `highland` with both of these
streams and then call `merge` to end up with a single stream we can return.
This stream will emit an event when any of the streams it contains emit in an
event. Unlike sequence, it will pass off each of these elements as soon as they
are ready, mixing them together. This means that cat points will get passed off
as soon as possible, and will not be held up by however long the sun points
take.

```
  return highland([
    catStream,
    sunStream
  ]).merge();
}
```

## Conclusion

Designing an application with streaming as the main strategy for data transfer
is extremely powerful. It allows us to create a client-side focusing on how to
handle a single unit of data without worrying about the full response. It
allows us to create a server-side that begins sending data to the client as
quickly as possible, without waiting to have all the data ready up front. And
finally is allows us to create modules that abstract away the source of our
data, allowing us to focus on its similarities as opposed to its differences.
