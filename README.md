Sprinkles
=========

Sprinkles is [ActiveSupport][as] for your browser.

The goal of this project is to isolate a small collection of helpers and extensions to make our lives as front-end engineers a little easier — just like ActiveSupport has done for [Ruby on Rails][ror] engineers.

Though syntatically optimal, monkey patching is general considered a [bad idea][red] so Sprinkles prefixes all methods and globals with a "$" to differentiate them from any native methods your client may have.

In Sprinkles, you'll find:

* [Array extensions](#array-extensions)
* [Cookies](#cookies)
* [Date extensions](#date-extensions)
* [Query string params](#query-string-params)
* [String inflections](#string-inflections)
* [XHR helpers](#xhr-ajax)

What you won't find in this project is:

* Effects or animation (e.g., `fadeOut()`)
* Browser backwards-compatibility
* Heavy-handed DOM manipulation (e.g., `wrapAll()`)
* DOM selection (e.g., [Sizzle][siz])
* Anything a modern browser can already do

This project is under active development so things will change dramatically as it matures. For contributions, please fork and submit pull requests.

[as]:  https://github.com/rails/rails/tree/master/activesupport
[ror]: http://rubyonrails.org
[siz]: http://sizzlejs.com
[red]: http://www.reddit.com/r/javascript/comments/279ion/sprinkles_the_activesupport_of_vanilla_js/

## Array Extensions

Your browser is all grown up! Let it `loop()`, `map()`, and `reduce()` your arrays.

``` js
var pies = ["apple", "pecan", "cherry"];

pies.forEach(function(pie, i) {
  alert("Do you like " + pie + " pie ?");
});

pies.map(function(pie, i) {
  return pie + " pie";
}); // Returns ["apple pie", "pecan pie", "cherry pie"]

pies.reduce(function(previousPie, currentPie) {
  return previousPie + "," + currentPie;
}); // Returns "apple,pecan,cherry"
```

Sprinkles adds `$flatten()` to `Array.prototype`.

``` js
[[1], [2], [3]].$flatten(); // Returns [1, 2, 3]
```

## Cookies

Working with cookies isn't very fun if all you have is `document.cookie`. Sprinkles adds `document.$cookies` that makes managing cookies a little easier. The API is designed to closely match that of `localStorage`.

``` js
document.$cookies.setItem("foo", "bar"); // Write a cookie, "foo", with value "bar"
document.$cookies.getItem("foo");        // Returns "bar"
document.$cookies.removeItem("foo");     // Remove cookie, "foo"
document.$cookies.clear();               // Remove all cookies
```

Sprinkles always assumes the path on all your cookies is `/` and does not support cookies that specify `domain`, `max-age`, `expires`, or `secure` — maybe one day.

## Date Extensions

Manipulating a date in plain old JavaScript is no fun. Sprinkles makes it better.

``` js
var d = new Date(1986, 0, 24, 20, 25); // Jan 24th, 1986 at 20:25
d.$beginningOfDay();                    // Jan 24th, 1986 at 00:00
d.$endOfDay();                          // Jan 24th, 1986 at 23:59
d.$beginningOfMonth();                  // Jan  1st, 1986 at 00:00
d.$endOfMonth();                        // Jan 31st, 1986 at 23:59
d.$tomorrow();                          // Jan 25th, 1986 at 20:25
d.$yesterday();                         // Jan 23rd, 1986 at 20:25
d.$monthName();                         // "January"
d.$dayName();                           // "Friday"
```

## DOM Manipulation

Sprinkles won't do things a modern browser can already do, even if that means a bit more typing.

``` js
var results = document.querySelector("#results"), // Retrieve an element
    result  = document.createElement("div");      // Create an element

result.classList.add("result");                   // Add a class to an element
result.textContent = "One more thing...";         // Set the content of an element

results.appendChild(result);                      // Add an element as a child
```

If you do a lot of this, maybe you should write a function like `createElement(name, content, parent)`.

## Query String Params

Read the query string params off any URL with the class methods Sprinkles adds to `Location`:

``` js
Location.$getParams("http://example.com/?a=1&b=2")     // Returns { "a": "1", "b": "2" }
Location.$getParam("http://example.com/?a=1&b=2", "a") // Returns "1"
```

Sprinkles also adds instance methods `Location` to read query string params from the current window's location:

``` js
// Assuming window.location = "http://example.com/?a=1&b=2"
window.location.$getParams()   // Returns { "a": "1", "b": "2" }
window.location.$getParam("a") // Returns "1"
```

## String Inflections

ActiveSupport has a bunch of slick [string inflections][inf]. Sprinkles just has the one for now.

``` js
"1".$ordinalize()  // "1st"
"22".$ordinalize() // "22nd"
```

[inf]: http://api.rubyonrails.org/classes/ActiveSupport/Inflector.html

## XHR (AJAX)

Creating an `XMLHttpRequest` object from scratch is tedious, so `get()` and `getJSON()` have been added as methods on `window` as convenience.

``` js
window.$get("http://example.com/plain-text",
  function(text) { console.log(text) },     // success
  function(text) { console.log(text) }      // error
);

window.$getJSON("http://example.com/json",
  function(object) { console.log(object) }, // success
  function(object) { console.log(object) }  // error
})
```

## Development

Sprinkles uses [Grunt][grn] to run development-oriented tasks. Grunt relies on [Node Packaged Modules][npm] (NPM). You can install NPM with [Homebrew][hmb]. With NPM installed and from inside the Sprinkles project root run:

``` sh
npm install
```

This is the Ruby equivalent of running `bundle install`. Now you can run the following tasks individually:

* `grunt concat`
* `grunt jshint`
* `grunt qunit`
* `grunt uglify`

Unfortunately, the tests [don't currently pass][i10] from the command line. This is due to the fact that PhantomJS, which drives the headless tests, is not a browser. This means that things like `document.cookie` and `window.location` don't behave like you'd expect. It seems sensible that running tests in-browser takes priority over running them from the command line so for now to run them boot up Sprinkles in web server (e.g., `python -m SimpleHTTPServer 8080`) and hit the test file directly (e.g. http://localhost:8080/test/index.html). If you're feeling especially debuggy, run `grunt test` from the console.

When you're done with a feature, you should [semantically](sem) increment the version number in `package.json` and run `grunt build` to update the distribution files.

[grn]: http://gruntjs.com
[npm]: https://www.npmjs.org
[hmb]: http://brew.sh
[i10]: https://github.com/canaryup/sprinkles/issues/10
[sem]: http://semver.org
