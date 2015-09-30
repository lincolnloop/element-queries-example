
Element queries provide styling opportunities specific to an element's dimensions. You could consider them the holy grail of responsive web design, allowing authors to determine an element's look and feel regardless on where it's placed. If given ample space you may wish to provide a richer experience or if your element finds its way into a cramped sidebar perhaps you'd only show the bare essentials. Element queries give us a way to do this without tying into an overall site structure which makes them a perfect solution for widgets, complex layouts, style guide-based designs, or anything with lots of individual components.

Currently, element queries aren't part of any specification but with a little bit of JavaScript you can get started using them today. Before we go much further, this all might make more sense if we look at an example (desktop-only for the full effect!):

<div class="demo-cta-box">
  <a href="http://lincolnloop.github.io/element-queries-example/" class="btn btn-demo">View the Demo</a>

  <p><a href="https://github.com/lincolnloop/element-queries-example"><strong>Get the Code on Github</strong></a></p>
</div>

Let's look through this!

## Overview of the Article Component

As seen in the example I've designed an Article component that works at three different sizes (we'll call them states):

1. A small, list item-like state with only a title and timestamp.
2. A medium sized state based on the list state from #1, but with a photo for added context.
3. A featured state, complete with large photo, larger title, full attribution, abstract, and read more call-to-action.

For demo purposes, I've put the basic Article component HTML into different sized columns, allowing you to see each state. Resizing your browser or editing a column's width in your browser's developer tools will trigger layout changes ([which is separate CSS](https://github.com/lincolnloop/element-queries-example/blob/gh-pages/page.css)), resulting in a visual change to a few Article components.

You can imagine this Article component being used throughout a news website or packaged as a syndication widget. It's a pattern found CNN.com<sup>[1]</sup>, which just so happens to be using element queries!

## How It's Built

Since element queries aren't part of any specification (yet) we'll need some help getting the functionality we want. I recommend [eq.js by Sam Richards](https://github.com/Snugug/eq.js), a lightweight polyfill<sup>[2]</sup>, though most of the other solutions work similarly. With eq.js you'll only need to include one file:

```
@syntax: HTML
<script src="eq.min.js"> </script>
```

Like other element query options, eq.js uses attributes to connect style changes to components. A basic structure for our example looks like this:

### HTML

```
@syntax: HTML
<article
  class="article"
  data-eq-pts="article--medium: 280, article--large: 440">
  <a href="#" class="article-photo">
    <img
      src="picture.jpg"
      class="article-photoImg"
      alt="A description of the photo" />
  </a>

  <div class="article-main">
    <header class="article-header">
      <h4 class="article-title">
        <a href="#">Article Title</a>
      </h4>
      <p class="article-meta">
        <span class="article-byline">
          By <a href="#" class="article-author">Author Name</a>
        </span>
        <time class="article-published">X hours ago</time>
      </p>
    </header>
    <div class="article-abstract">
      <p>...</p>
      <a href="#" class="more-cta">Read More</a>
    </div>
  </div>
</article>
```

### CSS

```
@syntax: CSS
.article {
  /*
    All our basic styles.
    Serves as our default state.
  */
}

[data-eq-state$="article--medium"] { {
  /*
    Styles specific to our medium state.
    Should override smallest state.
  */
}


[data-eq-state$="article--large"] { {
  /*
    Our largest size.
    Should override small and medium.
  */
}
```

Similar to mobile-first design patterns, we are specifying our smallest size and then overriding styles as we have more space, except we're using attribute selectors instead of media queries.

The Article component has different sizes for the title and photo for example (the CSS is lengthy to show here, be sure to  [view the CSS](https://github.com/lincolnloop/element-queries-example/blob/gh-pages/component-article.css)). In some cases, like the smaller sizes, we have no room for the abstract so we hide it altogether.

eq.js will parse our DOM and apply a `data-eq-state` attribute with the appropriate values when the `DOMContentLoaded` event triggers. Our largest state will have a `data-eq-state` value of `article--medium article-large`, while the middle state will only have `article--medium`. Here's how it looks when it runs:

```
@syntax: HTML
<article class="article" data-eq-pts="article--medium: 280, article--large: 440" data-eq-state="article--medium article--large">
  <!-- Rest of .article -->
```

It's worth noting that if you're willing to take a slight (almost negligible) performance hit when rendering you can move configuration to a Sass file. eq.js provides some tools for this. Here's how they might look applied to our example:

```
@syntax: SCSS
.article {
  @include eq-pts((
    medium: 280,
    large: 440
  ));
}

/* Add this at the end of your stylesheet */
@include eq-selectors;
```

eq.js will pass your size map to a `:before` pseudo element that results in something like this, which it can read and use to apply the appropriate attributes:

```
@syntax: CSS
.article:before {
  display: none;
  content: "medium: 280, large: 440";
}
```

## Closing Thoughts

Implementation is pretty simple, but with all polyfills and experimental methods there are some gotchas:

* We're dependent upon JavaScript, which could be a problem depending on your audience.
* The JavaScript dependency also means you may have a flicker as the element queries are applied *after* CSS loads. You can get around this by hiding elements until they've fully rendered, perhaps with a nice effect.
* [You can hit some circluar issues](http://www.xanthir.com/b4VG0), but like anything else you'll hit issues if you're reckless.

These issues aside, I'm positive once you start developing this way you'll find it difficult to go back. Element queries, or some future native implementation of them, really are an ideal way to build a truly flexible modular design. Hopefully we'll have native support soon but until then we have excellent options via polyfills.
