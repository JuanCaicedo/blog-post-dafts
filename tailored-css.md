# Tailored CSS

## The site should be fast

We value site speed a lot at SpanishDict.com. As we've started expanding our
site into Latin America, this has become even more important. Connection speeds
tend to be slower than in the USA, so optimizing for them is always on our mind.

In researching how to improve our site's speed, a recurring theme is that
[perceived speed tends to be more important than actual speed](http://blog.teamtreehouse.com/perceived-performance).
We're always on the lookout for ways to make the site _feel_ faster.

## State of CSS at SpanishDict

One of the things that can slow down how a site feels (it certainly did for us)
is blocking CSS. When you add a `link` tag, the browser has to first load and
parse the entire file, because it doesn't know how to render the content without
it.

Our CSS is not well organized, and as a result our css files are large. For
desktop, we build one large file that contains all the styles for our read-only
pages (pages
like
[translations](http://www.spanishdict.com/translate/ser),
[conjugations](http://www.spanishdict.com/conjugate/ser),
and [grammar articles](http://www.spanishdict.com/guide/ser-vs-estar)).
That file comes out to about 40kb _after_ being minified and gzipped.

We picked up an approach known as
[critical CSS](https://www.smashingmagazine.com/2015/08/understanding-critical-css/),
in which you inline all the styles required to render the above the fold content
of the page and then you load the rest of the styles asynchronously. Doing so
lets that content load much faster, so users feel the site is faster 🙌

## How do we build it?

For about a year, we took an mvp approach to this. We built a separate
`critical.CSS` file and we would deciding what styles could go above the fold
and which couldn't.

This wasn't easy. It meant that every time we added new styles, we had to figure
out which ones could possibly end up styling above the fold content. We were
very liberal with we included in critical CSS (because we reeeally didn't
want users to
see [FOUC](https://en.wikipedia.org/wiki/Flash_of_unstyled_content) 😱).That
meant that our critical CSS file kept growing and growing.

## What is tailored CSS?

To combat this, we built a way to keep all of our styles in a single CSS file
and through an automated process generate a critical CSS file. We call this
tailored CSS.

Our strategy leverages a tool
called [Penthouse](https://github.com/pocketjoso/penthouse). It's a library that
receives a CSS file and url, determines which styles are needed by the above the
fold content, and outputs just those styles.

## Multiple sizes and pages

We realized quickly that we couldn't just take the results of a single
Penthouse run and give it to users.

The types of pages that we serve 90% of our traffic can be split up into 7
different categories. Each has styles that aren't used at all by the others, so
it made sense to generate a separate tailored CSS file for each.

Our desktop site uses media queries to render slightly different styles for
three ranges of screen widths. Because we can't know ahead of time which ones a
user is going to need, our tailored CSS needs to include the styles for all of
them.

Even within a page category, a page can load in many different states. Different
styles will be rendered above the fold as a result. For example, one of our
grammar articles might have a custom table above the fold, but another might
have a dialogue box.

Our solution is to run Penthouse for each state, for each screen size, for each
page category. The styles for each page category are then concatenated together
and run through a series of postCSS plugins that deduplicate styles, organize
media queries, and minify the files.

## Increased complexity in development

Though Penthouse is an amazing tool, we haven't found using it to be completely
reliable. To be fair with the team behind it, most of that difficulty is
probably the result of complexities in our own codebase.

However, debugging when and why certain styles do or don't get included has not
been an easy task. Because of that, we decided as a team that tailored CSS
should be built at development time instead of at deploy time. Doing so means
changes are reviewed as part of our normal pull request process.

All in all, we run penthouse 43 times. The process takes around 5-6 minutes, and
it affects our ability to iterate quickly.

Tailored CSS also needs to be generated from *all* the most up to date styles.
That means that before generating it, we have to pull and merge our master
branch. When there are multiple pull requests open that change styles, we often
end up having to the process multiple times before a PR can be merged.

Generating production code based on what the site looks like in development
introduces a dependency between the state of our development environment and
production. To generate tailored CSS accurately, we now need to have running all the
services that make up our site and recent database snapshot.

## Moving forward

Tailored CSS definitely got the job done, but it also generated a huge amount of
technical debt. We are definitely open to other alternatives.

One approach that I would love to take is to reorganize our UI code into
components. It's hard to figure out which styles are above the fold, but much
easier to reason through which components could be rendered above the fold. With
that, we could manually listing the components that should be considered
critical and build a CSS file from that. However, reorganizing styles would
require a significant time investment with no immediate business value.

Have you tried building anything like tailored CSS? From what I can tell, it's
widely recommended, but not widely done. If so, let me know!
