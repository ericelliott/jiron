Jiron
=====

A hypermedia type based on Siren and the Jade template language.

> Part or small portion of a whole: patch of fog.

APIs are becoming crucially important to application development, but there are a few problems with the current state of types used for APIs:


## JSON troubles

JSON is a defacto standard format for types, and the syntax is terse, but:

1. It meets very few requirements for proper hypermedia support.
2. What few hypermedia types for JSON that do exist tend to have far fewer affordances than HTML.


## HTML troubles

HTML is the most popular hypermedia type in the world, but:

1. We take it for granted as a markup language for UI. It's hard to imagine how to apply it to good API design.
2. There are no popular standards or implementations of HTML as an API type.
3. HTML is verbose, and expensive over the network compared to JSON.


## Jade

Jade is a very terse, elegant markup syntax that was originally designed as a template language that compiles to HTML. One day as I was contemplating the problems we have with our current breed of hypermedia types, it occurred to me that Jade might be a great format to base a new type on.


## Siren

Siren is an elegant way to add hypermedia semantics to JSON, including:

* Links and relations
* Entities with classes
* Actions with templates
* Metadata

The thing that makes Siren great is that it defines a set of semantics that revolve around the concept of entities -- which happen to map really well to resource representations. Put Siren semantics on top of a resourceful API, and you've got a recipe for a highly scalable, highly decoupled architecture.

The biggest problem I have with Siren is that it has few affordances for user messaging.

## Jiron

Jiron is a mapping of Siren semantics combined with the expressive power and affordances of HTML, with the slick syntax of Jade.

Here's a sample:

```jade
head(profile='http://ericelliott.me/profiles/jiron-resource')
  title Albums
  link(rel='self, canonical', href='http://albums.com/albums')
body
  h1.title Albums
  p.description A list of albums you should listen to.
  ul.albums
    li.album
      a(rel='item', href='/albums/a65x0qxr')
        span.name Dark Side of the Moon
        span.artist Pink Floyd
    li.album
      a(rel='item', href='/albums/a7ff1qxw')
        span.name Random Access Memories
        span.artist Daft Punk

  ul.links
    li.link
      a(rel='next', href='/albums?offset=2&limit=1') Next

  // A count of the total number of entities
  // available. Useful for paging info.
  p.entityCount 3
```

And the equivalent HTML:

```html
<!DOCTYPE html>
<head profile="http://ericelliott.me/profiles/jiron-resource">
  <title>Albums</title>
  <link rel="self, canonical" href="http://albums.com/albums">
</head>
<body>
  <h1 class="title">Albums</h1>
  <p class="description">A list of albums you should
    listen to.</p>
  <ul class="albums">
    <li class="album">
      <a rel="item" href="/albums/a65x0qxr">
        <span class="name">Dark Side of the Moon</span>
        <span class="artist">Pink Floyd</span>
      </a>
    </li>
    <li class="album">
      <a rel="item" href="/albums/a7ff1qxw">
        <span class="name">Random Access Memories</span>
        <span class="artist">Daft Punk</span>
      </a>
    </li>
  </ul>
  <ul class="links">
    <li class="link">
        <a rel="next"
          href="/albums?offset=2&amp;limit=2">Next</a>
    </li>
  </ul>
  <!-- A count of the total number of entities-->
  <!-- available. Useful for paging info.-->
  <p class="entityCount">3</p>
</body>
```

Since Jiron is based on an existing HTML template syntax, the documents are easy to interpret. Browserify users get support on the client side automatically using [https://github.com/substack/node-jadeify](node-jadeify). You can also use browserify to export an AMD module or stand-alone module for the browser.

Using it in the browser is simple:

```js
jade.render('a.album(href="/albums/123") Pretty Hate Machine');
```

Which creates the string:

```html
<a href="/albums/123" class="album">Pretty Hate Machine</a>
```

Now you can add that to a `documentFragment` and use CSS selectors to query it. Better yet -- just slap some CSS on it and render it as-is. Try that with JSON.


## Cool party tricks

Here are some cool things you can do with Jiron that you can't do with JSON:

* Deliver code on demand with JavaScript links (including any client SDK you need to customize any Jiron browser for your particular API)
* Deliver default templates that make browsing your API directly in a browser pleasant
* Deliver clickable links and usable forms while your users are browsing the API directly
* Use CSS for styling default views
* Intersperse human-readable text and media with hypertext controls and affordances -- like HTML, only structured specifically for APIs

Tricky with HTML:
* Support any HTTP method type. PUT, PATCH, DELETE? No problem.
* Support HTTP header changes in links:

```jade
a(headers='Accept:application/vnd.jiron+jade') Some Jiron resource
```

Which would translate to this, if HTML knew how to deal with it:

```html
<a headers="Accept:application/vnd.jiron+jade">Some Jiron resource</a>
```
