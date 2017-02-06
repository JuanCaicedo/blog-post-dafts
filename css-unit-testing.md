# CSS unit testing

## A big little book

Early into my first jobs as a software developer, one of my friends recommended
a book to me. Little did I know that it would be one of the most influential
reads of my programming career. That book
was
[Test-Driven Development By Example](https://www.amazon.com/Test-Driven-Development-Kent-Beck/dp/0321146530),
by programming legend Kent Beck.

That book really convinced me of the long term benefits of testing for writing
maintainable code. For me the benefit of tests (and especially test driven
development) is that they give you a safe way to refactor your code.

## Refactoring, with tests

All systems trend towards complexity. In other words, over time all projects
build
up [techincal debt](https://www.techopedia.com/definition/27913/technical-debt).
I find refactoring to be one of the most valuable things a software engineer can
do because it allows you to develop future features in that codebase much more easily.

However, refactoring naively has a high risk of introducing bugs into your code.

Building up good test coverage gives you a quick way of verifying that all your
code works the way you expect it to. That way after you refactor code, you can
be sure that you haven't changed its behavior.

## How I got to into front-end development

I started out my career as a Java developer, working almost exclusively in the
front end. Testing and TDD are pretty mature in the Java community, so I got
lots of practice with them during that time.

In 2015, I left my job as a Java developer to become a full-stack Javascript
developer at Curiosity Media working
on [SpanishDict.com](http://www.spanishdict.com/). Our company makes testing our
codebase a huge priority, and thanks to that we're constantly improving our code
and our JS is pretty easy to work with.

In October of 2016, we started a big project to launch a new section of
SpanishDict,
the [language usage guide](http://www.spanishdict.com/guide/ser-vs-estar). I
ended writing almost all the styles for this section of the site, the first time
I had done any substantial CSS work.

I loved it.

After we successfully launched the language guide, my manager approached me
about developing more of a focus in the front-end, especially in CSS. Though all
of our engineers are comfortable working all through the stack, most of us are
much more experienced in the back-end, and he wanted someone to champion the
other side.

Sounds good to me!

## Where did all my tests go?

As I started reading through our `styles/` directory and getting more familiar
with all our CSS code, I realized something surprising.

A lot of it was kind of a mess.

Not for any particular reason. Over time requirements have changed, as has the
structure of the site and our knowledge of best practices. Also, the browsers we
support have changed, as have the tricks we have to do to make the site
compatible with all of them.

We have a lot of legacy CSS. But I felt a bad feeling when I wanted to go back
and improve that code.

I was scared to break things.

Things were already working, so maybe it was better to just leave them alone
instead of messing with them.

That made me stop and think though, I would never take that attitude with our
JS code. What was the difference.

Well, we don't have tests.

## Automated layout testing

That realization got me thinking, then it got me searching, and eventually it
got me playing around with a library
called [Quixote](https://github.com/jamesshore/quixote). It's a library that
integrates with browser testing tools
like [Karma](http://karma-runner.github.io)
or [Test'em](https://github.com/airportyh/testem) to let you make assertions
about styled html.

As of now, I've used it with great success for doing layout testing. The feeling
of being able to safely refactor CSS is awesome! It also helped me understand
some of the complexities of laying out, aligning, and centering elements with
CSS using
[different techniques](http://blog.karenmenezes.com/2014/apr/13/floats-inline-block-or-display-table-or-flexbox/)
(as well as showing me how awesome flexbox is).

## Test-Driven CSS

If the idea of automated layout testing sound interesting to you, and if you
love TDD and want to bring it to your style development, you might like coming
to an [online workshop](https://forwardcourses.com/workshops/119) I'm giving for
Forward Courses.

In it we'll build the styles for some elements I developed for the SpanishDict
language guide, but we'll do it all following TDD using Karma and Quixote. We'll
even explore how to do cross-browser testing and responsive design.

The workshop will be on Tuesday, February 7th 2017. You can watch the workshop
live by signing up for a two week free trial with ForwardCourses. Then
afterwards you can decide you love them and stay on to watch their whole catalog
of courses for just $19 a month 😀.

## Beyond

While I've been putting this workshop together, a lot of people have mentioned
visual regression testing to me.

Visual regression testing is an approach where you compare a snapshot of the
page before and after your refactoring. If the page looks different, the tests
fail. This seems like a really interesting approach, I think about it like
integration testing, where Quixote is more like unit testing.

I'm going to explore some of these tools,
particularly
[BackstopJS](https://garris.github.io/BackstopJS/),
[PhantomCSS](https://github.com/Huddle/PhantomCSS),
and [Ghost Inspector](https://ghostinspector.com/).
