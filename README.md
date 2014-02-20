stylus-conventions
==================

## Introduction

This is a curated set of conventions and best practices for
[Stylus](http://learnboost.github.io/stylus/), an expressive, dynamic, robust
and advanced CSS preprocessor. Frustrated with there not being a set of
conventions set in place (that could be easily found), I set forth to find out
on my own.

The majority of my findings come from working on [Axis](http://roots.cx/axis/),
a terse, modular and powerful CSS framework by the awesome
[Jeff Escalante](https://github.com/jenius) of
[Carrot Creative](http://carrot.is). Some findings come from examining any
repeating patterns in existing libraries
(like [Nib](http://visionmedia.github.io/nib/)).

## Why?

Stylus is nice and welcoming. It's syntax is warm and forgiving. This friendly
neighbourhood preprocessor does not differentiate between pure-CSS or
indentation-based, bracketless code. It tries to be as transparent as possible,
especially when it comes to mixins.

_This is its biggest flaw._

Not that Stylus is an overly-flawed preprocessor, it's actually quite the
opposite. Out of all of the preprocessors I've tried, Stylus is - in my opinion -
the most superior.

It's problem lies in the fact that because its syntax is so forgiving, and
there are so many different ways to write a block of Stylus code; it can be
confusing to learn - it supplies the developer with no definitive direction.
This, I postulate, is the main reason that it has not received as much attention
as it should have.

The confusion doesn't stop at learning, though. Once you're engrained in
variables, mixins and functions not requiring a prepended dollar sign ($) or
'at' symbol (@), you'll soon start to realize that you can no longer tell the
difference between them. This makes for very confusing code - and this is only
one example of Stylus' quirks.

This is why conventions and best practices need to be put in place _(and I'm
rather surprised it's taken this long!)_.

## Letter Casing

If you're wondering how to structure tidbits of your Stylus code, and one of the
questions you have is "Camel Case, Underscores or Hyphenated?", then you need to
examine HTML and CSS very closely.

Which of the following do you see more often in CSS?

    a) linearGradient
    b) linear_gradient
    c) linear-gradient

If you chose 'C', then there's your answer. Try keep things hyphenated.
It _just makes sense_.

## Which syntax?

Stylus supports near to anything you throw at it. You have the freedom of choice
here.

If you prefer looking at neat code, this syntax is for you:

    .selector
      border-radius: 15px
      border: 1px solid blue

If you're fond of your curly braces, then you can have them (and keep them,
thanks):

    .selector {
      border-radius: 15px;
      border: 1px solid blue
    }

Stylus supports punctuated code as well as code that has an inherent lack of
punctuation. It even allows you to remove colons. But you should not do this, as
it makes for very unreadable code in large projects. It may be quick to type,
but it is _not_ quick to scan or read.

    .selector
      border-radius 15px // hard to read.

## Variable Naming

This convention splits off into two categories, and there are approximately the
same amount of users for both conventions.

Stylus doesn't force you to declare variables with anything prepended to them -
you simply define a variable like so:

    my-variable = 'hey look, a variable!'

Some developers like this, others don't. Some like to declare them with a dollar
sign:

    $my-variable = "I've got bling"

I've seen a few developers argue that adding in that extra dollar sign adds
"way too much bloat". Well, in Stylus - there are actually _benefits_ to using
the dollar sign.

The first benefit; your variables are now easier to spot. You know what to look
for in order to quickly find them in your code.

The second benefit, and most important - is that you will never run into
Transparent Mixin Keyword Argument Conflicts (patent pending).

If you're wondering what I mean by that gargantuan term, let me demonstrate.
Suppose you wanted to port
[Compass' Vertical Rhythm module](http://compass-style.org/reference/compass/typography/vertical_rhythm/)
to Stylus, and you reached the point of bringing in the shorthand **rhythm()**
mixin. This is what you'd end up with:

    rhythm(leader = 0, padding-leader = 0, padding-trailer = 0, trailer = 0, font-size = base-font-size)
      leader: leader, font-size
      padding-leader: padding-leader, font-size
      padding-trailer: padding-trailer, font-size
      trailer: trailer, font-size

This is invalid Stylus. It does not throw an error, though. It fails silently.
Everything compiles except this mixin. If there were any best way to confuse a
compiler, this would be the way to do it. What's happening here is that the
rhythm mixin has it's own local variable scope, and within that scope, leader,
trailer and friends are not mixins. They are keyword arguments.

By prepending the dollar sign, your mixins stay safe - **$leader** is not the
same as **leader**.

    rhythm($leader = 0, $padding-leader = 0, $padding-trailer = 0, $trailer = 0, $font-size = $base-font-size)
      leader: $leader, $font-size
      padding-leader: $padding-leader, $font-size
      padding-trailer: $padding-trailer, $font-size
      trailer: $trailer, $font-size

The code may be relatively long, but it's readable, and that is a good thing.

For those developers who want to do away with the dollar sign, though - an
accepted convention is to sacrifice a little readability by aliasing the keyword
arguments to a short acronym:

    rhythm(l = 0, pl= 0, pt = 0, t = 0, font-size = base-font-size)
      leader: l, font-size
      padding-leader: pl, font-size
      padding-trailer: pt, font-size
      trailer: t, font-size

## Mixin Naming

Naming mixins in Stylus is fairly straightforward, but eventually you might run
into a problem that Jeff and I encountered in Axis. There's no snappy name for
it (yet), but there is a rule to follow. The problem we encountered was with a
mixin that Jeff created for rapid creation of navigation bars in Axis:

    nav()
      // pretend there's some code here

Can you spot the problem yet? No? C'mon - it's easy to see what's wrong here.
It's just a mixin definition with nothing underneath it!

Still can't guess? Well - here's the thing; **nav** is an element that is
already present in HTML5.

So, what does that mean for us? I'll tell you. Suppose, later on down the line,
you want to style a **ul** inside a separate **nav** element...

    nav ul
      list-style-type: none

...And then things start to break. You get a confusing error message that makes
no sense, and you don't know what to do. It took me three hours to learn what
was actually going on. Stylus interpreted the **nav** selector as a mixin, and
passed **ul** as a parameter. That's some pretty weird behaviour. So, quick tip:

**_Never name your mixins after HTML elements!_**

## Mixin Introspection

Stylus has two different levels of introspection, meaning that mixins and
functions can be called at either the _root_ or _block_ level, and are capable
of being aware of what level they were called at.

Block level mixins are called inside the body of a CSS selector. Root level
mixins are called outside of that scope.

    root-mixin() // a root level mixin
    body
      block-mixin() // a block level mixin

Root-level mixins **and** block-level mixins without arguments should _always_
be called with parentheses. Block-level mixins **with arguments** should be
called transparently, as if they were a CSS property:

    foo() // root level w/ parens
    body
      bar() // no arguments, must have parens
      baz: 1px, 3px, 5px // has args, no parens

Folowing this particular convention will not only make your code more readable
and maintainable, but it will also prevent a few rare compilation errors.

## Function Naming

We've discussed most of the apparent conventions in Stylus - but what about
functions? They're still kinda hard to differentiate from mixins...

Well, have a good look at Nib and a few other Stylus libraries. Notice anything?

I bet you did - it seems like most functions are prepended with a hyphen. Now,
it's easy to tell mixins and functions apart!

    -random-number()
      return 4px // guaranteed to be random. Now look away ;)
    body
      font-size: -random-number()

## Conditional Assignment

Stylus supports two forms of conditional assignment. One of them is the
recommended way to establish a global settings file.

I'll let you guess which one looks better in a conditional statement and which
one looks more... settings-appropriate.

    $my-variable1 ?= red
    $my-variable2 := blue

If you guessed that the second one is better for settings files, then you were
right.

## Placeholder Selectors

Stylus didn't always use to have placeholder selectors, and there were some
conventions regarding this. But now, it _does_ have placeholder selectors. They
are prepended with a dollar sign.

    $placeholder
      background: red
    .red-thing
      @extends $placeholder

## Optional Child Selector

Sometimes, when you're working with dynamic content - you might have a selector,
inside a selector, inside another selector. Let's assume they have a class of
**.a, .b and .c**, respectively.

You want to select **.c**, but **.b** is not always present on the page. Using
the Stylus **parent selector (&)**, you can still adjust your stylesheet to be
aware of that:

    .a
      > .b, &
        > .c
          color: red

Now, even if **.b** is not present, **.c** will still get styled appropriately.
