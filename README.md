# elm-for-web-developers

Kickstart your web development with Elm. 

A collection of notes for web developers looking into moving to Elm.

# Interaction, purity and signals

Elm's signals make it easy to mark a function as pure. 

```
clockTypeSelectView : Signal.Address Update -> Clock -> Html.Html
```

denotes when a function does something impure - the fact that it takes a signal means there is probably some level of reactivity in the function

```
quarterlyView : Clock -> Html.Html
```

the lack of signal lets the developer know that that function involves no interaction, but may change based on changes within the `Clock` model.

# Stylesheets

## Linking

In order to use stylesheets in Elm, you must create a custom index.html page. In there, you can link in any stylesheets you want

E.G:
```
<link rel="stylesheet" href="styles.css">
```

You must include "elm.js" in there - this is created when you run elm-make, and contains your elm application and the elm runtime. 

In your terminal:
```
elm-make <main-filename>
```

In index.html: 

```
<script type="text/javascript" src="elm.js"></script>
```

You must also then bootstrap your Elm application by putting in a call to Elm.fullscreen:

```
<script type="text/javascript">
  Elm.fullscreen(Elm.ModuleName);
</script>
```

A full working example can be found [here](https://github.com/eeue56/broken-clock/blob/master/index.html).

## Using classes in Elm

Giving HTML elements in Elm is as easy as

```
div [ class "clock-time" ] []
```
