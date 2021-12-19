# Grav (Notes)

Some configuration notes while experimenting with Grav.

* Grav Official Site -> https://getgrav.org/
* Learn Grav -> https://learn.getgrav.org/
* Github -> https://github.com/getgrav/grav

-----

### SimpleSearch Plugin

Searches for a string such as "northern bridge valley" return no results, since that explicit string is not matched. Substituting "northern, bridge, valley" (comma separated) returns the result you would expect, assuming your expectation is for something closer to the behaviour of a typical search engine.

Experimenting with a minor modification to the javascript: `.../user/plugins/simplesearch/js/simplesearch.js`

```javascript
if (field.checkValidity()) {
    window.location.href = location + separator + field.value;
}
```
->  
```javascript
if (field.checkValidity()) {
    // replace spaces and-or commas with a single comma
    window.location.href = location + separator + field.value.replace(/[\s,]+/g,",");
}
```
^- *replacing instances of spaces and/or commas with a single comma*

And in the searchbox TWIG template: `.../user/themes/YOURTHEME/templates/partials/simplesearch_searchbox.html.twig`

```twig
value="{{ query|e }}"
```
->  
```twig
value="{{ query|e|regex_replace('/,+/','') }}"
```
^- *to remove commas when search page reloads*

Couple of problems with this, not least the change is likely to be overwrittern by future updates.

-----

