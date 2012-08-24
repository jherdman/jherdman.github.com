---
layout: post
title: Public Asset Bundling in Rails
---

## IMPORTANT NOTE

The below is kept merely for historical purposes. You should not use the techniques
described below as Rails' [Asset Pipeline](http://guides.rubyonrails.org/asset_pipeline.html)
is a much better option

<hr>

Sometimes, in larger Rails projects, we want to group together JavaScript or <abbr title="Cascading Style Sheet">CSS</abbr> files. Let's use the the [Uploadify][uploadify] project to illustrate how we can use a seldom discussed Rails feature to simplify inclusion of <abbr title="Cascading Style Sheet">CSS</abbr> and JavaScript files that are used in conjunction with each other: <code>ActionView::Helpers::AssetTagHelper</code>.

## Materials

* [Uploadify][uploadify]
* [jQuery][jquery]

## Considering the Norm

Rails defines a set of default JavaScript files to include in our view when we use <code>#javascript_include_tag(:default)</code>. From the Rails documentation we can see an example of the output:

```ruby
javascript_include_tag :defaults # =>
    "<script type='text/javascript' src='/javascripts/prototype.js'></script>
    <script type='text/javascript' src='/javascripts/effects.js'></script>
    ...
    <script type='text/javascript' src='/javascripts/application.js'></script>"
```

This is called an expansion. We have expansions for JavaScript and Stylesheets. We can use with both <code>#javascript_include_tag</code> and <code>#stylesheet_link_tag</code>. We can also register our own. It's through expansions that we can bundle together JavaScript and and CSS files.

## Registering Expansions

Registering expansions is done through two different methods: <code>ActionView::Helpers::AssetTagHelper.register_javascript_expansion</code>, and <code>ActionView::Helpers::AssetTagHelper.register_stylesheet_expansion</code>. Each of these methods takes a Hash as an argument which points to a list of asset names (e.g. "jquery.js").

Let's look at how we would define the default <code>:default</code> for our JavaScript bundle:

```ruby
ActionView::Helpers::AssetTagHelper.register_javascript_expansion :default => [
  'prototype',
  'effects',
  ...
  'application'
]
```

That's a little verbose for setting the default, so Rails provides us <code>ActionView::Helpers::AssetTagHelper.register_javascript_include_default</code>. Let's try this again:

```ruby
ActionView::Helpers::AssetTagHelper.register_javascript_include_default(
  'prototype',
  'effects',
  ...
  'application'
)
```

Let's set a default using jQuery:

``` ruby
ActionView::Helpers::AssetTagHelper.register_javascript_include_default(
  'jquery',
  'application'
)
```

Easy as pie! Let's setup expansions for [Uploadify][uploadify]:

``` ruby
ActionView::Helpers::AssetTagHelper.register_javascript_expansion :uploadify => [
  'swfobject',
  'uploadify',
]

ActionView::Helpers::AssetTagHelper.register_stylesheet_expansion :uploadify => [
  'uploadify'
]
```

Now we can easily maintain asset bundles. Cool, eh?

[uploadify]: http://www.uploadify.com/ "Uploadify: a Multiple File Upload Plugin for jQuery"
[jquery]: http://jquery.com/
[ror]: http://rubyonrails.org
