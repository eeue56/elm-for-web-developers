# elm-for-web-developers

Kickstart your web development with Elm. 

A collection of notes for web developers looking into moving to Elm.

* [CSS](#stylesheets)
* [FAQ](#faq)

# Where to start?

The easiest place to start is through looking at some of the [examples on the elm-lang site](http://elm-lang.org/examples). I would suggest avoiding looking at any of the Games/Tasks/community examples. If you are looking to do HTML-things rather than canvas things, look at the [checkboxes](http://elm-lang.org/examples/checkboxes) example. It should introduce the [start-app](https://github.com/evancz/elm-architecture-tutorial/) structure that the majority of Elm programs follow. 

Elm is normally used with what is refered to as the "Elm Architecture". Think of this as a framework imposing structure upon you. If you're coming from a Jquery backgorund, this might not make much sense. I would suggest playing around with some other JS frameworks so that you get the idea of what it means to have an opinionated structure to your web apps.

## From Angular

If you're coming from Angular, the best comparision with startapp is directives. Each view function should be considered a directive, each with their own restricted scope. In order to send things outside of the scope, you must use Signals to send some data to be processed in the update function. The update function will take a model, an update object, and return a new model. Every time a model changes, the view is recreated with the bits that changed. startApp's view function gets given a [`Signal.Address`](http://package.elm-lang.org/packages/elm-lang/core/2.1.0/Signal#Mailbox). For basic sites, you don't need to worry about what this is other than it can allow you to write [interactive code](https://github.com/evancz/elm-todomvc/blob/master/Todo.elm#L202) by creating the objects to send to the view function.

## From React

If you're coming from React, there are already [documented cases](http://noredinktech.tumblr.com/post/126978281075/walkthrough-introducing-elm-to-a-js-web-app) of people using React alongside Elm. [Relm](https://github.com/eeue56/relm) aims to be a library for simplifying the usage of React and Elm as a couple, though at the moment it is mainly just examples.


## Integrating Elm into your existing applications

`Elm.worker(Elm.ModuleName)` should be your best friend for integrating Elm into existing applications. It allows you to use ports and use Elm for controlling your applications without needing to have any of the views in Elm itself. I've used it for managing state in my React apps between the different components. The `send` function allows your child components to trigger `subscribe` functions in your parent scope, keeping your data flow in a top-down structure as promoted by things like Flux.

`Elm.embed(Elm.ModuleName)` is perfect for when you want to do some of your view in Elm, but the rest without. Note: there are some bugs with the core libraries when you use embed, for things like `touch` or `mouse` actions. 

With either of these, you can use ports for allowing communication between your Javascript and your Elm applications. A typical use case of this might be using existing AJAX requests without moving all the HTTP requests to Elm.

# Interaction and signals

Elm makes it easy to notify when an interaction happens in your views.

```elm
clockTypeSelectView : Signal.Address Update -> Clock -> Html.Html
```

denotes when a function does something based on an input that may change without the developer telling it to - the fact that it takes a signal means there is probably some level of reactivity in the function. 

```elm
quarterlyView : Clock -> Html.Html
```

the lack of signal lets the developer know that that function involves no interaction, but may change based on changes within the `Clock` model.

# Stylesheets

## Linking

In order to use stylesheets in Elm, you must create a [custom index.html](https://github.com/eeue56/broken-clock/blob/master/index.html) page. In there, you can link in any stylesheets you want. 

E.G:
```html
<link rel="stylesheet" href="styles.css">
```

You must include "elm.js" in there - this is created when you run elm-make, and contains your elm application and the elm runtime. 

In your terminal:
```
elm-make <main-filename> --output elm.js
```

In index.html: 

```html
<script type="text/javascript" src="elm.js"></script>
```

You must also then bootstrap your Elm application by putting in a call to Elm.fullscreen:

```javascript
<script type="text/javascript">
  Elm.fullscreen(Elm.ModuleName);
</script>
```

A full working example can be found [here](https://github.com/eeue56/broken-clock/blob/master/index.html). You could also do this by using a build tool to insert the stylesheets into the html generated by elm-make after they're created, but this way you don't need to worry about that. 

## Using classes in Elm

Giving HTML elements classes in Elm is as easy as

```elm
div [ class "clock-time" ] []
```

with the CSS

```elm
.clock-time {
    color : red;
}

```

# Optimization

While Elm is fast [at some things](http://elm-lang.org/blog/blazing-fast-html), it's easy to write non-performant code in Elm if you stick too rigidly to the functional programming style.

## Move things out of let bindings

If a function can be defined outside of a let binding, then take it out. 

TODO: add example

## Flat is better than nested

The less levels of functions involved, the better. If you find yourself nesting function calls within function calls over and over, look and see if you can simplify.

# FAQ

## Why does start-app give me an unexpected type error?

If you get an error like this 
```elm
  -- TYPE MISMATCH ----------------------------------------------------- index.elm
  
  The 1st argument to function `start` has an unexpected type.
  
  14|   StartApp.start { model = model, view = view, update = update }
                       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  As I infer the type of values flowing through your program, I see a conflict
  between these two types:

    Int

    String

```

this probably means that somewhere in your view or update methods, there is a type error. In order to get a more useful error message, add type signatures to your functions, which will force it to check the types as you expect them to be.


## How do I do HTML?

If you're trying to use HTML, then you probably want [elm-html](http://package.elm-lang.org/packages/evancz/elm-html/4.0.1).

## Can I enter HTML as a string?

Not with elm-html by default. You could hack up a solution like so

```elm
import Html exposing (div)
import Html.Attributes exposing (property, attribute)
import Json.Encode exposing (string)

main = 
  div [ property  "innerHTML" <| string "<div>hello</div>"  ] []
```

but that's not a good idea. Read up on the elm-html and understand how virtual-doms work and why they're a good idea. 

Footnote - I have an example [here](https://github.com/eeue56/relm/tree/master/proposal/basic-html) of abusing Native to hack out some HTML from a string.


## Do I always have to manually create an index.html?

No! If you run `elm-make <filename> --output index.html`, it will create both elm.js and a version of it in the the index.html file.
