## Router for Web Components
> Works with [Polymer](http://www.polymer-project.org/), [X-Tag](http://www.x-tags.org/), and natively with the [platform](https://github.com/Polymer/platform) polyfill.

> [erikringsmuth.github.io/app-router](http://erikringsmuth.github.io/app-router)

Lazy-loads content. Binds path variables and query parameters to page element's attributes. Supports multiple layouts. Works with `hashchange` and HTML5 `pushState`. One set of routes will match regular paths `/`, hash paths `#/`, and hashbang paths `#!/`.

[Download](https://github.com/erikringsmuth/app-router/archive/master.zip) or run `bower install app-router --save`.

## Configuration

```html
<!doctype html>
<html>
  <head>
    <title>App Router</title>
    <link rel="import" href="/bower_components/app-router/app-router.html">
  </head>
  <body>
    <app-router>
      <!-- matches an exact path -->
      <app-route path="/home" import="/pages/home-page.html"></app-route>

      <!-- matches using a wildcard -->
      <app-route path="/customer/*" import="/pages/customer-page.html"></app-route>

      <!-- matches using a path variable -->
      <app-route path="/order/:id" import="/pages/order-page.html"></app-route>

      <!-- matches everything else -->
      <app-route path="*" import="/pages/not-found-page.html"></app-route>
    </app-router>
  </body>
</html>
```

Changing the URL will find the first `app-route` that matches, load the element or template, and replace the current view.

## Data Binding
Path variables and query parameters automatically attach to the element's attributes.

``` html
<!-- url -->
http://www.example.com/order/123?sort=ascending

<!-- route -->
<app-route path="/order/:id" import="/pages/order-page.html"></app-route>

<!-- will bind 123 to the page's `id` attribute and "ascending" to the `sort` attribute -->
<order-page id="123" sort="ascending"></order-page>
```

See it in action [here](http://erikringsmuth.github.io/app-router/#/databinding/1337?queryParam1=Routing%20with%20Web%20Components!).

## Multiple Layouts
Each page chooses which layout to use. This allows multiple layouts in the same app. Use `<content>` tag insertion points to insert the page into the layout. This is similar to nested routes but completely decouples the page layout from the router.

This is a simple example showing a page and it's layout.

#### home-page.html

```html
<link rel="import" href="/layouts/simple-layout.html">
<polymer-element name="home-page" noscript>
  <template>
    <simple-layout>
      <div class="title">Home</div>
      <p>The home page!</p>
    </simple-layout>
  </template>
</polymer-element>
```

#### simple-layout.html

```html
<polymer-element name="simple-layout" noscript>
  <template>
    <core-toolbar>
      <content select=".title"><!-- content with class 'title' --></content>
    </core-toolbar>
    <content><!-- all other content --></content>
  </template>
</polymer-element>
```

## &lt;app-route&gt; options

#### import a custom element
Lazy-load a custom element.

```html
<app-route path="/customer/:customerId" import="/pages/customer-page.html"></app-route>
```

When you navigate to `/customer/123` the router will load `/pages/customer-page.html`, replace the active view with a new `customer-page` element, and bind any attributes `element.setAttribute('customerId', 123)`.

You can manually set the element's name with the `element` attribute if it's different from the file name. This is useful when bundling (vulcanizing) custom elements.

```html
<app-route path="/customer/:customerId" import="/pages/page-bundle.html" element="customer-page"></app-route>
```

#### pre-loaded custom element
You can route to a pre-loaded custom element. In this case, load the element normally in the `<head>` and include the `element="element-name"` attribute on the route. This is how you'd bundle and pre-load custom elements.

```html
<head>
  <link rel="import" href="/pages/page-bundle.html">
</head>
<app-router>
  <app-route path="/customer/:customerId" element="customer-page"></app-route>
</app-router>
```

#### import template
You can use a `<template>` instead of a custom element. This doesn't have data binding and is lighter-weight than a custom element. Just include the `template` attribute.

```html
<app-route path="/example" import="/pages/template-page.html" template></app-route>
```

#### inline template
Finally, you can in-line a `<template>` like this.

```html
<app-route path="/example" template>
  <template>
    <p>Inline template FTW!</p>
  </template>
</app-route>
```

## Shadow DOM
By default the router puts the active route's content in an `<active-route></active-route>` element.

```html
<app-router>
  <!-- app-routes -->
  <active-route><!-- active route content --></active-route>
</app-router>
```

If you want the active route's content isolated in a shadow tree you can add the `shadow` attribute like this.

```html
<app-router shadow>
  # shadow-root <!-- the active route's content will be inside the shadow root -->
  <!-- app-routes -->
</app-router>
```

## Navigation
There are three ways change the active route. `hashchange`, `pushState()`, and a full page load.

#### hashchange
If you're using `hashchange` you don't need to do anything. Clicking a link `<a href="/#/new/page">New Page</a>` will fire a `hashchange` event and tell the router to load the new route. You don't need to handle the event in your code.

#### pushState
If you're using HTML5 `pushState` you need one extra step. The `pushState()` method was not meant to change the page, it was only meant to push state into history. This is an "undo" feature for single page applications. To use `pushState()` to navigate to another route you need to call it like this.

```js
history.pushState(stateObj, title, '/new/page'); // push a new URL into the history stack
history.go(0); // go to the current state in the history stack, this fires a popstate event
```

#### Full page load
Clicking a link `<a href="/new/page">New Page</a>` without a hash path will do a full page load. You need to make sure your server will return `index.html` when looking up the resource at `/new/page`. The simplest set up is to always return `index.html` and let the `app-router` handle the routing including a not found page.

## Demo Site
Check out the `app-router` in action at [erikringsmuth.github.io/app-router](http://erikringsmuth.github.io/app-router). The <a href="https://github.com/erikringsmuth/app-router/tree/gh-pages">gh-pages branch</a> shows the demo site code.

## Tests
1. Start a static content server in `app-router` (node `http-server` or `python -m SimpleHTTPServer`)
2. Run unit tests [http://localhost:8080/tests/SpecRunner.html](http://localhost:8080/tests/SpecRunner.html)
3. Run functional tests [http://localhost:8080/tests/functional-test-site/](http://localhost:8080/tests/functional-test-site/)
