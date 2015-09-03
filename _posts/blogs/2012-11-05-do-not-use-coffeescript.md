---
layout:     post
title:      Do Not Use CoffeeScript
category:   blog
description: If your app is an universal app and you changed the background colour of an UITableView, you will see it works on iPhone but does not work on iPad.
---

CoffeeScript is not a framework. It is used to generate readable, clean, tight and safe Javascript code.

Pros:

1. Python style whitespacing
2. Ruby styled lightweight syntax

Javascript:

```
    var validate = function(form) {
      var required_field_names = ['name','email'];
      return false;
    }
```

Coffee:

```
    validate = (form) ->
      required_field_names = ['name','email']
      return false
```

CoffeeScript returns the result of the last expression in a function

```
    validate = (form) ->
      required_field_names = ['name','email']
      false
```

Though there are many reasons to use CoffeeScript.The biggest reason I decided not to use CoffeeScript is debugging. When you debug your web app, you see javascript. What you see is not what you wrote.
