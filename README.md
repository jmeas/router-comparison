# router-comparison

Wtf even are routers? This repository is an effort to answer that question.

### About

Routers are playing an increasingly more important role in client side apps. For many apps,
the application's response to a user moving through the app is dictated by the router. That is
a huge responsibility!

But I have found that no two routers are the same. Some are even drastically different from others. I made this chart
to compare some of the features of the most popular routers.

### Why

Nested routers are used in virtually every client side application these days. However, Backbone, my
preferred library, does not have a nested router. In fact, you can't even build one off of Backbone's router,
because of the way the code is structured (I'm working to fix this in Backbone v2.0.0).

I want to build a new router that can be used in Backbone apps, and I want it to be great. By looking at every
existing router, I can pluck the features I find most useful, and discard the ones that I find unnecessary.

At the end of each section, I'll have an "opinions" section that explains what I find to be the most useful implementation
of that particular feature.

### Inspirations

I'll often draw comparisons between particular routers, and that's usually because one of them was directly inspired
by the other.

Ember's router is the first nested router that I know of.

UI-Router came a few months later, and shares some similarities to Ember Router, but also has a lot of differences. 

React Router was inspired by Ember's router, and Stateman was inspired by UI-Router.

StateRouter is the name of my work in progress router. It will likely be more similar to Ember's router than the UI-Router.

### Routers

The following routers have been considered:

- [Backbone](http://backbonejs.org/#Router)
- [Ember](http://guides.emberjs.com/v1.10.0/routing/)
- [React](https://github.com/rackt/react-router)
- [UI-Router](https://github.com/angular-ui/ui-router)
- [Stateman](https://github.com/leeluolee/stateman)
- StateRouter\*

\* This is a new router that I'm working on to be used primarily in Backbone apps.

### Feature Table

A description of each feature can be found below the table.

Feature             | Backbone | Ember | React | UI-Router | Stateman | StateRouter
------------------- | -------- | ----- | ----- | --------- | -------- | -----------
DSL                 | ✔        | ✔     | ✔     | ✔         | ✔        | ✔
Regex for `:dynamic`| ✘        | ✘     | ✘     | ✔         | ✔        | ✘
Splats              | ✔        | ✔     | ✔     | ✔         | ✘        | ✔
Optional segments   | ✔        | ✘     | ✔     | ✘*        | ✘*       | ✘
Unnamed  segments   | ?        | ?     | ?     | ✘         | ✔        | ✘
404 abstraction     | ✘        | ✘     | ✔     | ✔         | ✔        | ✘
Regex instead of DSL| ✔        | ✘     | ✘     | ✘         | ✘        | ✘
Asynchronous        | ✘*       | ✔     | ✔     | ✔         | ✔        | ✔
Sort: specificity   | ✘        | ✔     | ?     | ✘         | ✘        | ✔
Sort: order added   | ✔        | ✘     | ?     | ✔         | ✔        | ✘ 
Nested routes       | ✘        | ✔     | ✔     | ✔         | ✔        | ✔
Reusable routes     | ✘        | ✔     | ?     | ?         | ?        | ✘  
Optional history    | ✘        | ✔     | ✘     | ✔         | ✘        | ✔
Includes history    | ✔        | ✔     | ✔     | ?         | ✔        | ✘
Cancel navigation   | ✔*       | ✔     | ✔     | ✔         | ✔        | ✔
Redirect navigation | ✔        | ✔     | ✔     | ✔*        | ✔        | ✔
Template links      | ✘        | ✔     | ✔     | ✔         | ✘        | ✘
`.active` links     | ✘        | ✔     | ✔     | ✘         | ✘        | ✘
Query params        | ✘        | ✔     | ✔     | ✔         | ✔        | ✔
'Index' states      | ✘        | ✔     | ✔     | ✘         | ✘        | ✔ 
'Loading' states    | ✘        | ✔     | ✘     | ✘         | ✘        | ✔
'Error' states      | ✘        | ✔     | ✘     | ✘*        | ✘*       | ✔
'Abstract' states   | ✘        | ✘     | ✘     | ✔         | ✘        | ✘
Pubsub              | ✔        | ✘     | ✘     | ✔         | ✔        | ✘
Scrolling           | ✘        | ✘     | ✔     | ✘*        | ✘        | ?
Group data fetching | ✘        | ✔     | ✔*    | ✘         | ✘        | ✘ 
Enter hook          | ✔        | ✔     | ✔*    | ✔         | ✔        | ✔ 
Exit hook           | ✘        | ✔     | ✘     | ✔         | ✔        | ✔ 
Update hook         | ✘        | ✔     | ✘     | ✘         | ✔        | ✔ 

\* Refer to the corresponding section below to see notes.

### Features

#### DSL

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✔        | ✔     | ✔     | ✔         | ✔        | ✔

A DSL is a domain specific language. If you haven't heard that term before, don't worry: it's a concept you may already
be familiar with, even if you don't know the name. An example of a DSL are the templating languages we use to output HTML,
like Handlebars, Underscore, or Angular templates.

These features are like mini programming languages that are designed to do one task.

In Routers, the DSL is the syntax you use to specify what sorts of URLs each route matches. For instance, `books/:bookId`
is an example of a DSL, where the `:` designates the start of a dynamic URL segment.

Every router comes with a DSL, and they share a common set of features. However, there are differences. The
next few features in this list cover those differences.

##### Thoughts

DSLs are a no-brainer! The alternative would be writing regular expressions for everything, which is pretty gross, I think. Given how pretty
much every router supports their own DSL, it seems like everyone is in agreement on this point.

#### Regex for `:dynamic`

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✘        | ✘     | ✘     | ✔         | ✔        | ✘

Dynamic parts capture a single URL segment within a path. A URL segment are each of the sections separated by `/`. So,
for instance, in the URL `"/books/2"`, there are two segments: `books` and `2`.

Generally, dynamic segments capture **anything** between the parenthesis. But some libraries allow you to change
what's matched by a dynamic segment with a regex and/or type annotation.

UI-Router is the first library that I know of to do this. Because Stateman is based off of UI-Router, it, too,
has a similar feature.

An example in UI-Router is:

```js
"/user/{id:[0-9a-fA-F]{1,8}}""
```

UI-Router has an extra feature not found in Stateman: it allows you to specify a type, too. These are like built-in
regexes that you can reference. So, for instance, `'/calendar/{start:date}'` is a valid route in UI-Router (but not
Stateman).

##### Thoughts

I think that this feature provides great value. 

#### Splats

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
✔        | ✔     | ✔     | ✔         | ✘        | ✔

Splats can be used to capture more than one segment of the URL, and are a denoted by a `*`. An example is:

`"/users/*splat"`

This would match `/users/sandwich`, as well as `/users/james/picture`, and even `/users/james/picture/edit`.

Splats are interesting because of the way different routers treat them. In Backbone and Ember, they're of particular importance,
because they're the way that you specify 404 routes.

`"*notFound"`

This works really well for Ember, but not so well for Backbone. The difference is that Ember has a unique matching algorithm:
less specific routes are matched last (see: sort by specificity). So even if you specify the 404 route first, it won't be matched
unless nothing else does.

Backbone isn't so smart. It matches on a first come, first serve basis, which requires you to place the 404 route last. In practice,
this can end up being a tedious requirement to fulfill.

Other libraries use one of two other abstractions for routes: either a special named route, and/or a pubsub event that is fired on the
router itself.

#### Optional segments

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Optional segments are, well, exactly what you'd expect. Segments that don't need to be there. They're generally designated by a `?` or
parentheses.

An example from Backbone, which uses parentheses:

`docs/:section(/:subsection)`

#### Unnamed segments

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

An unnamed segment is a dynamic segment, or a splat, that doesn't require an identifying name. For instance, in the route URL
`"books/:bookId"`, `bookId` is the name of the dynamic segment.

Stateman added this feature, although it (surprisingly) does not exist in 

#### 404 abstraction

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

A 404 abstraction is anything other than splats to designate not found pages. The alternatives are:

1. A specially named Route (React Router, Stateman)
2. Pubsub events (UI-Router, Stateman)

#### Regex instead of DSL

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Backbone lets you write a regular expression instead of the DSL. As you would expect, this gets ugly quickly. Because of the small
amount of code necessary to implement the feature, it does fit in with Backbone's minimalist ideas. It's comparable, I think, to
allowing regex in the dynamic segments.

#### Asynchronous

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Whether or not the route handlers are asynchronous. This is important for routers that support nesting, because you'll often
be fetching data in your handlers.

Backbone is listed as not supporting this, but I want to clarify what I mean. Because Backbone's handlers are independent,
it simply doesn't matter if your callback is asynchronous or synchronous: nothing depends on them (out of the box).

#### Sort: specificity

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Route resolution is an important component of any router. If the user has two routes:

`books/:bookId` and `books/author`

Which will match when the user navigates to `books/author`? Does it depend on the order
that they are added?

For most routers, the answer to the above question is **yes**. `books/author` will
need to be added to the router before `books/:bookId`, or else the dynamic segment
will match the string.

For routers that support regex for dynamic segments, this matters less because that provides
a way to make your dynamic segments even more specific.

Ember is the only router that I know of to support the proper algorithm to support this.

#### Sort: order added

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

All other routers sort by the order added. Therefore, you must specify your dynamic segments and
splats last, or use regexes to restrict what your dynamic segments match.

#### Nested routes

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Are the routes related? Does entering a child route cause you to first enter the parent route? If yes,
then it's a nested router.

Given that routes encode your app's *location* (which is often represented by a URL), it's no surprise
that most routers these days are nested.

Backbone is the exception here.

#### Reusable routes

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Coming soon...

#### Optional history

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Because a nested router is just a state machine, it should not need history to function. This is
useful in apps that don't encode the location of the user in the URL, like embedded apps.

Interestingly, Ember and the UI-Router are the only two that support this feature. The two libraries
that were inspired by these libraries, React's Router and Stateman, seem to have foregone including this
feature.

#### Includes history

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Whether or not the library includes a History implementation (to support, say, hash changes in old
browsers).

Generally, the routers that are bundled with a framework do include a history implementation. This also
makes since given that they're all designed to work with 

#### Cancel navigation

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Whether or not there's an abstraction for canceling navigation.

In Ember and React Router, you get the opportunity for each route to specify whether the transition
should be cancelled or not.

In UI-Router and Stateman, an event is fired on the router itself that allows you to cancel the transition,
so it's more like a global setting.

Backbone provides a method that you can return false from to prevent transitioning.

#### Redirect navigation

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Very similar to the above: can you stop the transition and instead redirect? The same hooks used for canceling
is used for redirection in all libraries.

The UI-Router had a few bugs with redirecting the last time I used it, and I do not believe that they have been fixed.

#### Template links

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Some libraries provide an abstraction to be used in your template to generate links that are automagically hooked up
to the router.

This feature is super rad, if you haven't used it before!

#### `.active` links

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Does the router automatically append an active class to the links in the template when that state is entered?

#### Query params

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Coming soon...

#### 'Index' states

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Index states provide you a different state to enter when a state is landed upon directly,
versus when that state is activated by a child state.

For instance,

`books`
`books.index`
`books.book`

Given these states, landing on "books" will activate both books and books.index.

Landing on "books.book" will activate "books" and "books.book", but not "books.index"

#### 'Loading' states

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

While transitions are in progress some routers give you a `loading` state to display.

#### 'Error' states

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

When an error occurs when doing a transition, some routers allow you to specify an `error` state to enter.

#### 'Abstract' states

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Abstract states are about as difficult to wrap your head around as index states are. Abstract states are
states that they, themselves, cannot be activated. Instead, they're activated when their child states are.

This gives you a hook to, say, load a particular set of data for a group of sibling routes.

#### Pubsub

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Whether or not you can do something like:

`router.on('event', myCallback)`

These routers are generally the ones that give you global hooks to catch errors and 404 states. Those hooks
are these events.

#### Scrolling

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Browsers remember your scroll position when you navigate an app with the Back and Forward buttons. They also
scroll you to the top of the page when a new link is clicked.

This feature is about whether or not the router supports this out of the box.

The UI-Router is pretty weird, in this regard: it allows you to scroll to a particular element when the route changes.

#### Group data fetching

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

If 3 routes are activated, and each specify data to be fetched, does all of the fetching happen before views start rendering?

#### Enter hook

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

Does a callback fire when the route is entered?

In Backbone, this is all that you get.

In React, from what I understand it sort of just implicitly renders the component. I'm not sure if there's much else that you'd want to do (or could do),
but I'm only gathering this from their docs, as I haven't used their router.

#### Exit hook

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

When a route is being transitioned out, do you get a hook to do things?

One thing to note is that this seems to be conflated with the ability to cancel/redirect in certain routers. In the React Router, for instance,
there's no distinct "exit" hook, but there is a hook called before you leave, which is intended for canceling. I don't know enough about React
to know whether this is a good or a bad thing!

#### Update hook

Backbone | Ember | React | UI-Router | Stateman | StateRouter
-------- | ----- | ----- | --------- | -------- | -----------
?        | ?     | ?     | ?         | ?        | ?

An update hook is distinct from an enter hook, in that it is generally related to whether the "context" of a route changes. Consider a route
with a URL `:bookId`. When the user transitions to this same route, but with a different ID, what happens?

In some routers, the "enter" hook is called again. In other routers, only the "update" route is triggered.

In Stateman, the update hook is called for every route in the tree of active routes. Ember only fires it on routes that explicitly
have had a context change.

### Omissions

I omitted certain routers from the above chart.

#### Ampersand.js

Too similar to Backbone's.

#### Marionette.js

Too similar to Backbone's.

#### Angular.js v1

Not very notable, and in the process of being completely replaced in Angular v2.
