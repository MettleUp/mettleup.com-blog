---
title: Obangular! Angular & Oboe
date: 2015-07-14
author: "rory"
description: "Streaming JSON with AngularJS"
tags: [ "development", "frontend", "angularjs", "json", "nodejs" ]
image: "/images/bg/red-blue-todo.jpg"
---

I was recently challenged to create an Angular application that consumed newline delimited JSON. My initial reaction can be elegantly summarized as:

> Why the f— would someone want to do that?

As I learned, there are plenty of scenarios where a JSON stream can be useful.

In a typical client-side application pages or views will not load until the backend service has finished supplying the requested data. Until that happens whole sections may appear unresponsive to the user. This is especially apparent when using ui-router resolves in Angular.

Streaming JSON on the other hand allows the application to fill in data as it is received. In my experience this drastically improves the perception of performance.

Consider this: **do you really need the complete set of data before loading the view?**

Online stores, communication channels (chat, email, micro-blogging, etc), logging, or other “push” interfaces immediately come to mind as candidates for a streaming service. The benefits increase if the application is transmitting over a mobile network.

Of course, there’s just one problem: **Angular will not parse newline delimited JSON.** That is to say that Angular expects either an object or an array from its backend service, and a JSON stream does not fit the bill. Here’s what Chrome had to say on my first attempt:

```
SyntaxError: Unexpected token {
```

## Enter Oboe.js

Fortunately there is a [javascript library](http://oboejs.com/) built by [Jim Higson](https://twitter.com/jimhigson) that can consume the stream. And, as is typical, I discovered early on that I was not the first person to explore this challenge. Ronald Brinkerink [asked the same question](http://stackoverflow.com/questions/25567864/a-solution-for-streaming-json-using-oboe-js-in-angularjs) on Stack Overflow a while back and even [wrote a module](https://github.com/RonB/angular-oboe) that contained his solution.

I did not end up using the module because I wanted to adhere to John Papa’s [style guide](https://github.com/johnpapa/angular-styleguide), but my code was heavily influenced by it.

Here’s the relevant code snippet that exposes Oboe as an Angular service.

```javascript
function Data(DataStream) {
  function Stream (params) {
    return DataStream.get(params);
  };
  return Stream;
}

function getDataStream(params) {
  var defer = $q.defer();
  var stream = oboe(params)
                  .start(begin)
                  .fail(error)
                  .node(params.pattern, stream)
                  .done(complete);

  function stream(n) {
    defer.notify(n);
    return oboe.drop;
  }

  return defer.promise;
}
```

In this example the application is a storefront selling [cool-ascii-faces](https://github.com/maxogden/cool-ascii-faces). Loading more products is as simple as calling `loadMoreProducts()` as shown in the controller snippet below:

```javascript
function Products(Data) {
  var vm = this;
  
  vm.finished = false;
  vm.loadMoreProducts = loadMoreProducts;
  vm.products = [];

  var config = {
    pattern: '{id size price face}',
    url: '/api/products',
    start: start,
    done: done
  };
  
  // oboe.js: finished loading stream
  function completed() {
    vm.finished = true;
  }

  // get next batch from api
  function loadMoreProducts() {
    Data(config).then(completed, error, node);
  }

  // oboe.js: stream parsing
  function node(product) {
    // add product to dom
    vm.products.push(product);
  }
}
```

Pagination was required to be handled via infinite scrolling. I used a custom directive (not shown) to load more products when the existing catalog nears the bottom of the page. Products at the top are **not** destroyed as the user scrolls down, but they probably should be. This will definitely be an issue with a sufficiently large catalog.

```html
<section>
  <div class="row" when-nearing-end="vm.loadMoreProducts()">
    <div class="box item" ng-repeat="p in vm.products track by p.id">
      <div class="face" ng-style="{'font-size':p.size+'px'}">{{::p.face}}</div>
      <div class="price">{{::p.price / 100 | currency:"$":2}}</div>
    </div>
    <div class="col-xs-12 banner">
      <span class="center" ng-if="!vm.finished"><img class="loading-gif" src="/styles/images/loading.gif"/> loading...</span>
      <span class="center" ng-if="vm.finished">~ end of catalog ~</span> 
    </div>
  </div>
</section>
```

I have since decided that firing an event when nearing the bottom of the page was not ideal. Products should probably be loaded whenever the loading bar is present on the screen. Otherwise there could be scenarios, such as an excessively large screen, where the bottom of the page is never reached. However, I have not yet implemented that solution.

## Wonderful, but...

...the code snippets are incomplete.

Never fear. Here’s the "finished" [Github](https://github.com/rorygarand/discount-ascii-warehouse) repo and a [working demo](http://ascii.rorygarand.com/).

In fact, the demo has one additional killer feature not covered in this write up: **a picture of a kitten is inserted after every 20 products.** You’re welcome.