# router-comparison

Wtf even are routers? This repository is an attempt to address that question by comparing the
features of the most popular client side routers.

## Table of Contents

- [About](#about)
- [Why](#why)
- [Routers Compared](#routers-compared)
  - [Inspirations](#inspirations)
  - [Omissions](#omissions)
- [Features](#features)
  - [DSL](#dsl)
  - [Regex for `:dynamic`](#regex-for-dynamic)
  - [Splats](#splats)
  - [Optional segments](#optional-segments)
  - [Unnamed segments](#unnamed-segments)
  - [Regex instead of DSL](#regex-instead-of-dsl)
  - [Asynchronous](#asynchronous)
  - [Sort: specificity](#sort-specificity)
  - [Sort: deepest](#sort-deepest)
  - [Sort: order added](#sort-order-added)
  - [Nested routes](#nested-routes)
  - [Reset namespace](#reset-namespace)
  - [Absolute URLs](#absolute-urls)
  - [Optional history](#optional-history)
  - [Includes history](#includes-history)
  - [Cancel navigation](#cancel-navigation)
  - [Redirect navigation](#redirect-navigation)
  - [Template links](#template-links)
  - [`.active` links](#active-links)
  - [Query params](#query-params)
  - ['Index' states](#index-states)
  - ['Abstract' states](#abstract-states)
  - ['Loading' states](#loading-states)
  - ['Error' states](#error-states)
  - ['Not Found' states](#not-found-states)
  - [Pubsub](#pubsub)
  - [Not found event](#not-found-event)
  - [Scrolling](#scrolling)
  - [Group data fetching](#group-data-fetching)
  - [Enter hook](#enter-hook)
  - [Exit hook](#exit-hook)
  - [Update hook](#update-hook)

## About

Routers are playing an increasingly important role in client side apps. Many developers use the router
to dictate what happens as the user navigates through the application – a substantial responsibility!

Perhaps unsurprisingly, no two routers are the same. I created this chart to compare some of the features
of the most popular, or otherwise noteworthy, routers.

## Why

Nested routers are used in virtually every client side application these days. However, my
preferred library, Backbone, does not have a nested router. In fact, you can't even build one from
Backbone's router because of the way the code is structured ([I'm working to fix this in Backbone
v2.0.0](https://github.com/jashkenas/backbone/pull/3660)).

I want to build a new router that can be used in Backbone apps, and I want it to be the best it can
be. By examining existing routers, I can pluck the features I find most useful, and discard the ones
that I find less so.

## Routers Compared

The following routers have been considered:

- [Backbone](http://backbonejs.org/#Router)
- [Ember](http://guides.emberjs.com/v1.10.0/routing/)
- [React](https://github.com/rackt/react-router)
- [UI-Router](https://github.com/angular-ui/ui-router)
- [Stateman](https://github.com/leeluolee/stateman)
- StateRouter\*

\*This is a new stand-alone router that I'm building.

#### Inspirations

Throughout this document, I'll often draw comparisons between routers. This is usually because one of them was
inspired by, and modeled after, the other. For this reason, their differences are of particular interest to me.

Ember's router is the first nested router that I know of. UI-Router came shortly after, but it was largely an
independent endeavor. Nevertheless, both share similarities with one another, which is to be expected
considering their similar approach to routing.

Each of these original two routers has since inspired another popular router. Ember inspired the React Router,
and UI-Router inspired Stateman.

StateRouter is the name of my work-in-progress router. It's the reason that this document exists.

As a chart, the flow of influence might look something like:

```
 UI-Router     Ember
    |            |
 Stateman      React    ---(all of them)---> StateRouter
```

#### Omissions

I omitted certain routers from the above chart.

##### Ampersand.js

Too similar to Backbone's.

##### Marionette.js

Too similar to Backbone's.

##### Angular.js v1

I don't think many people use this (UI Router is more popular), and it's in the process of being
completely replaced in Angular v2.

##### Angular.js v2

The API for this router is still in flux, and it's difficult to find documentation on how it is intended to work.

## Features

### DSL

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✔        | ✔     | ✔     | ✔         | ✔        | ✔

DSL stands for "domain specific language." If you haven't heard that term before, don't worry: it's
a concept you may already be familiar with, even if you don't know the name. Example DSLs are the
templating languages we use to output HTML, like Handlebars, Underscore, or Angular templates.

DSLs are like mini programming languages that are designed to do one task.

In Routers, the DSL is the syntax you use to specify what sorts of URLs each route matches. For
instance, `books/:bookId` is an example of a DSL, where the `:` designates the start of a dynamic
URL segment.

Every router comes with a DSL, and they share a common set of features. However, there are
differences. The next few features in this list cover those differences.

##### Thoughts

DSLs are a no-brainer! The alternative would be writing regular expressions for everything, which is
pretty gross, I think. Given that pretty much every router supports their own DSL, it seems like
everyone is in agreement on this point.

### Regex for `:dynamic`

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✘     | ✘     | ✔         | ✔        | ✔

Dynamic parts capture a single URL segment within a path. A URL segment are each of the sections
separated by `/`. So, for instance, in the URL `"/books/2"`, there are two segments: `books` and
`2`.

Generally, dynamic segments capture **anything** between the parenthesis. But some libraries allow
you to change what's matched by a dynamic segment with a regex and/or type annotation.

UI-Router is the first library that I know of to do this. Because Stateman is based off of
UI-Router, it, too, has a similar feature.

An example in UI-Router is:

```js
"/user/{id:[0-9a-fA-F]{1,8}}""
```

UI-Router has an extra feature not found in Stateman: it allows you to specify a type, too. These
are like built-in regexes that you can reference. So, for instance, `'/calendar/{start:date}'` is a
valid route in UI-Router (but not Stateman).

Although Backbone does not support this feature, it does support [using a regular expression for the
whole route URL instead of the DSL.](#regex-instead-of-dsl).

Ember and React, on the other hand, don't offer anything like this. Shucks!

##### Thoughts

This feature is super useful, I think. It allows the developer to move validation into the
route itself, maximizing the number of invalid requests that are automatically sent off to the 404
route.

When routers don't support this, the developer is forced to write custom validation logic in each
route, which is more work for them.

### Splats

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✔        | ✔     | ✔     | ✔         | ✘        | ✔

Splats can be used to capture more than one segment of the URL, and are a denoted by `*`. An
example is:

`"/users/*splat"`

This would match `/users/sandwich`, as well as `/users/james/picture`, and even
`/users/james/picture/edit`.

Splats are interesting because of the way different routers treat them. In Backbone and Ember,
they're of particular importance, because they're the way that you specify 404 routes.

`"*notFound"`

This works really well for Ember, but not so well for Backbone. The difference is that Ember has a
unique matching algorithm: less specific routes are matched last (see:
[sort by specificity]((#sort-specificity))). So even if you specify the 404 route first, it won't be
matched unless nothing else does.

Backbone isn't so smart. It matches on a first come, first serve basis, which requires you to place
the 404 route last. In practice, this can end up being a tedious requirement to fulfill.

Other libraries use one of two other abstractions for 404s: either [a 404 state](#not-found-states),
and/or [a pubsub event](#not-found-event) that is fired on the router itself.

##### Thoughts

In the applications I have developed, splats have only been useful to match 404 routes. There might
be some good use cases I simply haven't encountered, though, so I intend to include them in 
StateRouter. Additionally, I intend for them to be the algorithm behind
[404 states](#not-found-states) in StateRouter.

If you've used splats successfully in an app for a feature other than 404 routes, do let me know
by raising an issue! I'm curious to hear the use case.

Although 404 states can be implemented as thin abstraction layers over splats, I think there is
value in that. An explicit 404 state is far more expressive than using splats directly to
represent 404 states.

### Optional segments

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✔        | ✘     | ✔     | ✔         | ✘        | ✔

Optional segments are, well, exactly what you'd expect: segments that don't need to be there.
They're generally designated by a `?` or parentheses.

UI-Router also supports optional segments, albeit in a different fashion. When a parameter
is given a default value (which can be set when you configure your route), then it is made optional.

An example from Backbone, which uses parentheses:

`docs/:section(/:subsection)`

##### Thoughts

These haven't been particularly useful to me, but it seems like many other developers disagree. There's
[an open issue](https://github.com/tildeio/route-recognizer/issues/60) to support these in Ember, for
instance. So, because I don't want StateRouter to be the only router on the street that doesn't support
these things, they'll def. be in there.

### Unnamed segments

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✘     | ✘     | ✘         | ✔        | ✘

An unnamed segment is a dynamic segment, or a splat, that doesn't require an identifying name. For
instance, in the route URL `"books/:bookId"`, `bookId` is the name of the dynamic segment.

Stateman added this feature, although it does not exist in the UI-Router.

##### Thoughts

I like being explicit, so I don't intend to add this feature to StateRouter.

### Regex instead of DSL

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✔        | ✘     | ✘     | ✔         | ✘        | ✘

Backbone and UI-Router allow you to write a regular expression instead of the DSL. As you would expect,
this gets ugly quickly. Because of the small amount of code necessary to implement the feature, it does
fit in with Backbone's minimalist ideas. It's comparable, I think, to allowing
[regex in the dynamic segments.]((#regex-for-dynamic))

##### Thoughts

This is another one of those features that is difficult for me to think up of a good use case for. If
the DSL is powerful enough, it should not be necessary for a developer to use Regex directly.

Nevertheless, I would be interested in supporting this feature, but it seems to be incompatible with
[sorting by specificity](#sort-specificity), which I find more valuable.

### Asynchronous

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✔*       | ✔     | ✔     | ✔         | ✔        | ✔

Whether or not the route handlers are asynchronous. This is important for routers that support
nesting, because you'll often be fetching data in your handlers.

Because Backbone's handlers are independent, it simply doesn't matter if your callback is
asynchronous or synchronous: nothing really *depends* on them.

##### Thoughts

Considering that most routers are intended to be used to orchestrate data fetching, it only makes
sense that they're built to support asynchronous handlers!

### Sort: specificity

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✔     | ✘*    | ✘         | ✘        | ✔

Sorting is the order in which the router attempts to find a route. If the user has two
routes corresponding to the following two URLs:

`books/:bookId` and `books/author`

Which will match when the user navigates to `books/author`? Does it depend on the order
that they are added?

For most routers, the answer to the above question is **yes**. `books/author` will
need to be added to the router before `books/:bookId`, or else the dynamic segment
will always match the string.

For routers that support [regex for dynamic segments](#regex-for-dynamic), this matters less because
that feature provides a way to make your dynamic segments even more specific.

Ember and my own router will implement this feature.

React Router uses an algorithm that indirectly leads to sorting by specificity. Their
algorithm attempts to match [the deepest route first.](#sort-deepest)

##### Thoughts

:+1:!


### Sort: deepest

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✘*    | ✔     | ✘         | ✘        | ✘*

Sorting by the deepest route is similar to sorting by specificity. It tries to
match the most-nested routes first.

Because deeper routes tend to be more specific, Ember's algorithm approximates
this one.

##### Thoughts

Sort by specificity is more intuitable, I think, so I intend to use that instead.

### Sort: order added

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✔        | ✘     | ✘     | ✔         | ✔        | ✘ 

All other routers sort by the order added. Therefore, you must specify your dynamic segments and
splats last, or use regexes to restrict what your dynamic segments match.

##### Thoughts

This is easier to implement than sorting by specificity, but more difficult for developers to work
with. However, for Routers that allow you override the regex used by dynamic segments, this is less
of an issue.

### Nested routes

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✔     | ✔     | ✔         | ✔        | ✔

Are the routes related? Does entering a child route cause you to first enter the parent route? If
yes, then it's a nested router.

Given that routes encode your app's *location* (which is often represented by a URL), it's no
surprise that most routers these days are nested.

Backbone is the exception here.

##### Thoughts

All contemporary routers support nesting because it is a super useful feature!

### Reset Namespace

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✔     | ✘     | ✘         | ✘        | ✘  

Most routers require that children routes be built from their parent's namespace. For instance, if
you want to add a child route to `books.book`, it must necessarily begin with `books.book`. A route
called `comments` could not, for instance, be a child of `books.book`.

##### Thoughts

I don't think that this is particularly useful. A friend of mine on the Ember Data team suggests
that nobody use this feature, which is pretty telling, I think.

### Absolute URLs

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✔        | ✘     | ✔     | ✔         | ✔        | ✔  

A child route's URL is often appended to the parent's route. Are you able to specify that the child's
URL is an absolute URL?

Backbone's URLs are independent, so, yes, they are all absolute.

In UI-Router and Stateman, you can use the `^` prefix to indicate that the URL is absolute.

In React Router, prefixing with `/` indicates an absolute URL, whereas no prefix is not an
absolute route.

##### Thoughts

I haven't worked on an app that would use this, but it is used by React and UI-Routers, so I'll
likely include it.

### Optional history

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✔     | ✘     | ✔         | ✘        | ✔

Because a nested router is just a state machine, it should not logically need to depend on URLs to
function. This is useful in apps that don't encode the location of the user in the URL, like
embedded apps.

Interestingly, Ember and the UI-Router are the only two that support this feature. The two libraries
that were inspired by these libraries, React's Router and Stateman, seem to have foregone including
it.

##### Thoughts

This feature is a must! It's a shame that the React Router and Stateman didn't recognize the value.

### Includes history

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✔        | ✔     | ✔     | ✔*        | ✔        | ✘

Whether or not the library includes a History implementation (to support, say, hash changes in old
browsers).

Generally, the routers that are bundled with a framework do include a history implementation.

UI-Router uses Angular's `$location` server.

##### Thoughts

Very useful for routers that are part of a framework. Because StateRouter is just a router, it
doesn't make sense to bundle up a history implementation for it. A Backbone-specific version will
be subsequently released that ties in nicely with Backbone's history. Otherwise, you'll need
to wire up your own history, which won't be too hard.

### Cancel navigation

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✔*       | ✔     | ✔     | ✔         | ✔        | ✔

Whether or not there's an abstraction for canceling navigation.

In Ember and React Router you get the opportunity for each route to specify whether the transition
should be cancelled or not.

In UI-Router and Stateman, an event is fired on the router itself that allows you to cancel the
transition, so it's more like a global setting. v1 of the UI-Router (unreleased as of 8/16) will
allow cancellation at any level of granularity.

Backbone provides a method that you can return false from to prevent transitioning.

##### Thoughts

Per-route cancellation is fantastic. Global cancellation...less so.

### Redirect navigation

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✔        | ✔     | ✔     | ✔*        | ✔        | ✔

Very similar to the above: can you stop the transition and instead redirect? The same hooks used for
canceling is used for redirection in all libraries.

The UI-Router had
[a bad bug with redirecting](https://github.com/angular-ui/ui-router/issues/326#issuecomment-52731196)
the last time I used it, and I do not believe that it has been fixed.

##### Thoughts

Same as above.

### Template links

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✔     | ✔     | ✔         | ✘        | ✘

Some libraries provide an abstraction to be used in your template to generate links that are
automagically hooked up to the router.

This feature is super rad – if you haven't used it before you should play with one of the routers
that support it.

##### Thoughts

So, so good, but out of scope for a standalone router, I think. I plan to build a version of
StateRouter exclusively for Backbone apps, and it will have templating features like this.

### `.active` links

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✔     | ✔     | ✔         | ✘        | ✘

Does the router automatically append an active class to the links in the template when that state is
entered?

##### Thoughts

:heart_eyes: Although I love this feature, it won't be in the core StateRouter library, given just
how many templating languages there are out there.

### Query params

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✔     | ✔     | ✔         | ✔        | ✔

Coming soon...

##### Thoughts

Coming soon...

### 'Index' states

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✔     | ✔     | ✘         | ✘        | ✔ 

To understand index states, you must first know that nested routers involve states being active
or inactive. Because they are nested, when a child is landed on, all of its parents are *also*
active.

Index states are special states that can be associated with each state. They're only activated
when their associated state is landed on directly.

I understand that this may be confusing, so let's look at an example.

Consider three routes,

`books`
`books.index`
`books.book`

Given these states, landing on "books" will activate both books and books.index.

However, landing on "books.book" will activate both "books" and "books.book", but not "books.index"

##### Thoughts

:+1: This is a super useful feature to better control the fetching of data.

### 'Abstract' states

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✘     | ✘     | ✔         | ✘        | ✘

Abstract states are maybe even more difficult to understand than index states. Abstract states are
states that cannot ever be landed on directly. Instead, they can only be activated when their child
states are.

This gives you a hook to, say, load a particular set of data for a group of sibling routes.

##### Thoughts

It's my understanding that these provide the same value as index states, but in a way that I find
to be more confusing.

### 'Loading' states

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✔     | ✘     | ✘         | ✘        | ✔

While transitions are in progress some routers give you a `loading` state to display.

##### Thoughts

:+1:

### 'Error' states

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✔     | ✘     | ✘*        | ✘*       | ✔

When an error occurs when doing a transition, some routers allow you to specify an `error` state to
enter.

UI-Router and Stateman provide events to manage this.

##### Thoughts

:+1:

### 'Not Found' states

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✘     | ✔     | ✘         | ✔*       | ✔

Not found states let you specify unique not found states for each portion of your
application.

Stateman provides a not found state that only exists on the root level, so it's substantially
less useful than what is provided by React.

##### Thoughts

Very useful when done on a per-route basis.

### Pubsub

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✔        | ✘     | ✘     | ✔         | ✔        | ✘

Whether or not you can do something like:

`router.on('event', myCallback)`

These routers are generally the ones that give you global hooks to catch errors and 404 states, as
those hooks are events.

##### Thoughts

If the router is for another library that has pubsub, then I think that supplying events for
consistency's sake makes sense. Otherwise, I think the majority of routing features don't benefit
much from including pubsub.

One issue with pubsub is that everything is globally configured. Instead of associating logic
with a particular route, it requires that you use if-then statements in a global callback to
specify per-route behaviors.

### Not found event

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✘     | ✘     | ✔         | ✔        | ✘

Not found events are triggered on the router directly, and give you a global hook to manage
404s. This is less powerful than Not Found states, because it requires that you place all of your
404 logic within a single callback.

##### Thoughts

:-1:

### Scrolling

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✘     | ✔     | ✔*        | ✘        | ?

Browsers remember your scroll position when you navigate an app with the Back and Forward buttons.
They also scroll you to the top of the page when a new link is clicked.

This feature is about whether or not the router supports this out of the box.

The UI-Router is markedly different: it allows you to scroll to a particular element when the route
changes.

##### Thoughts

Definitely useful, but I'm torn on whether or not it's a good idea to bundle this into the router
directly!

UI-Router's scrolling feature might be useful in some situations, but I don't think that it's
useful as a default, or even as a feature to include in a core routing library.

### Group data fetching

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✔     | ✔*    | ✘*        | ✘        | ?

If 3 routes are activated, and each specify data to be fetched, does all of the fetching happen
before views start rendering?

Ember does this out of the box. React, being just a view layer, naturally doesn't have any APIs
directly related to fetching data. But with some pretty messy boilerplate code you can pull it off
(see the examples under [the Router.run method](http://rackt.github.io/react-router/#Router.run))

UI-Router's unreleased v1 will allow you to specify 'policies' to manage this.

##### Thoughts

I'm on the fence about this one. I like the idea of fetching all of the data first to run the XHRs
concurrently, but I'm worried that it may make passing each route the data that it requested more
difficult.

I'm leaning more toward grouping them, though.

### Enter hook

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✔        | ✔     | ✔*    | ✔         | ✔        | ✔ 

Does a callback fire when the route is entered?

In Backbone, this is all that you get.

In React, from what I understand it sort of just implicitly renders the component. I'm not sure if
there's much else that you'd want to do (or could do), but I'm only gathering this from their docs
as I haven't used their router.

In Ember, this is a no-op that you can use to include additional logic.

##### Thoughts

A good idea.

### Exit hook

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✔     | ✘     | ✔         | ✔        | ✔ 

When a route is being transitioned out, do you get a hook to do things?

One thing to note is that this seems to be conflated with the ability to cancel/redirect in certain
routers. In the React Router, for instance, there's no distinct "exit" hook, but there is a hook
called before you leave, which is intended for canceling. I don't know enough about React to know
whether this is a good or a bad thing!

In Backbone, there's no such thing as a route being 'active' or 'inactive.' Routes are just
callbacks out-of-the-box.

##### Thoughts

Very useful for teardown when you're working with a nested router.

### Update hook

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✔     | ✘     | ✘         | ✔        | ✔ 

An update hook is distinct from an enter hook, in that it is generally related to whether the
"context" of a route changes. Consider a route with a URL `:bookId`. When the user transitions to
this same route, but with a different ID, what happens?

In Ember, the update hook is the most important hook. It's where the view is rendered, for instance.
The update hook in Ember is called `setup`. And the order of callbacks goes
`exit => enter => setup`

`exit` starts at the deepest route, and works its way up. `enter` and `setup` work their way
down the chain. `setup` begins on the first route whose context is changed.

Stateman operates in a different way. The order goes `exit => update => enter`. And, unlike
Ember, `update` is called on any route that is unchanged, even if the context hasn't changed.
Also, the `update` hook in Stateman works its way the tree, like the `exit` hook.

##### Thoughts

:+1: It is useful to differentiate entering and updating, I think. I tend to prefer Ember's
hook structure.
