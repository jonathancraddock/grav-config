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

### Social Metadata Tags

* [Online OpenGraph/Twitter Preview](https://www.opengraph.xyz/)  
^- *see how 'social' cards will appear on various sites...*

```twig
{% block metasocial %}

<!-- Open Graph data -->
  <meta property="og:title" content="{{ page.title }}" />
  <meta property="og:type" content="article" />
  <meta property="og:url" content="{{ page.url(true, true) }}" />
  <meta property="og:site_name" content="example.com" />
  <meta property="og:description" content="{{ page.summary|striptags|trim|e }}" />

<!-- Twitter Card data -->
  <meta name="twitter:title" content="{{ page.title }}" />
  <meta name="twitter:card" content="summary_large_image" />
  <meta name="twitter:url" content="{{ page.url(true, true) }}" />
  <meta name="twitter:site" content="example.com" />
  <meta name="twitter:description" content="{{ page.summary|striptags|trim|e }}" />

<!-- Image data -->
{% set image = page.media.images|first %}
{% set imgdefault = "https://example.com/user/images/img-default.jpg" %}

{% if image %}
  <meta property="og:image" content="https://example.com{{ image.url|raw }}" />
  <meta name="twitter:image" content="https://example.com{{ image.url|raw }}" />
  <meta name="twitter:image:src" content="https://example.com{{ image.url|raw }}" />
  <meta itemprop="image" content="https://example.com{{ image.url|raw }}" />
{% else %}
  <meta property="og:image" content="{{imgdefault}}" />
  <meta name="twitter:image" content="{{imgdefault}}" />
  <meta name="twitter:image:src" content="{{imgdefault}}" />
  <meta itemprop="image" content="{{imgdefault}}" />
{% endif %}

{% endblock %}
```

[Twig](https://twig.symfony.com/doc/3.x/) can `truncate` your description, if required. The IF statement allows for articles/pages which may not have their own image.

-----
