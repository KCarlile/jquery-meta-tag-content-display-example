# jQuery Meta Tag Example

## Overview

This code example demonstrates a technique to use jQuery to show persona icons (or any content) based on the presence of meta tags. For example, let's say you want to display persona icons on a page to illustrated the target audience. You can have a set of icons on the page that are hidden and then enable display of certain persona icons by adding a meta tag to trigger display of that persona icon.

### File Index

| File | Description |
|------|-------------|
|[index.html](index.html)|Simple example with the least amount of code.|
|[index-code.html](index-code.html)|Simple example with code displayed on the page for reference.|
|[metatags.html](metatags.html)|Example of looping over meta tags and displaying them and their attributes.|
|[README.md](README.md)|This file.|

## How Does It Work?

There are multiple elements that make this functionality work.

- A jQuery CDN include is added below the page content, but before the `</body>` tag, so we can leverage the jQuery library loaded after HTML content.
- jQuery/JavaScript code does the dynamic work for us (more on this later) which exists between the jQuery CDN include and the `</body>` tag.
- A set of HTML elements (`<span>`s in this case) represent the persona icons with attributes that are selectable by jQuery, such as:\
`<span class="personaIcon" id="someUniquePersonaName" style="display: none;">[Some emoji code]</span>`\
(The `style="display: none;"` part is important as we want these icons to start hidden so we only show specific ones based on the meta tags present on the page.)
Alternately, you could just use `<img>` elements with images and the same class and id attributes.
- A set of meta tags is used to trigger display of the persona icons, such as:\
`<meta type="persona" icon="cowboy">`

## Page Execution

Page execution happens like this:

1. The first jQuery/JavaScript block waits for the page to fully load before doing anything: `$(document).ready(...);`
1. Once the page is loaded, it calls the `showIconsByMetaTags()` function.
1. Next, it loops over all of the meta tags that have an attribute called "type" with the value "persona," like this: `<meta type="persona" icon="robot">`
1. When it finds a metatag with those matching attributes, it looks for an element on the page with `class="personaIcon"` and `id="robot"` or whatever value is in the icon attribute in the meta tag.
1. If it finds an element with `class="personaIcon" id="robot"`, it makes that element visible.
1. The script continues until it runs out of meta tags to check.

### HTML

The persona icons code looks like this:

```html
<span style="font-size: 4em; display: none;" class="personaIcon" id="cowboy">&#129312;</span>
<span style="font-size: 4em; display: none;" class="personaIcon" id="vampire">&#129499;</span>
<span style="font-size: 4em; display: none;" class="personaIcon" id="zombie">&#129503;</span>
<span style="font-size: 4em; display: none;" class="personaIcon" id="robot">&#129302;</span>
<span style="font-size: 4em; display: none;" class="personaIcon" id="alien">&#128125;</span>
```

The meta tags code looks like this:

```html
<meta type="persona" icon="cowboy">
<meta type="persona" icon="vampire">
<meta type="persona" icon="zombie">
<meta type="persona" icon="robot">
<meta type="NOTpersona" icon="alien">
```

You can see that the meta tag for the alien icon has `type="HIDEpersona"` to indicate that we don't want to show this one, but simply omitting the meta tag or changing the type to blank or anything other than `persona` will work to exclude the alien icon or any others.

### jQuery/JavaScript

The following line should be below your content before the `</body>` tag. This loads the jQuery library via CDN. If your page already has jQuery loaded, you may not need this.

```html
<script src="https://code.jquery.com/jquery-3.6.1.min.js"
    integrity="sha256-o88AwQnZB+VDvE9tvIXrMQaPlFFSUTR+nldQm1LuPXQ="
    crossorigin="anonymous"></script>
```

This next block of code is doing the heavy lifting. (Well, the jQuery library is doing the _real_ heavy lifting.) This goes after the jQuery CDN include but also before the `</body>` tag.

```html
<script>
// wait for the page to be fully loaded
$(document).ready(function() {
    // call the function to show icons by meta tags once the page has loaded
    showIconsByMetaTags();
}); // end document ready function

/**
 * Loops over meta tags looking for those with type="persona" and captures the icon
 * attribute's value. Then, it looks for an element with class="personaIcon" and id
 * matching the icon attribute's value. If it finds an element like this,
 * the element is unhidden.
 */
function showIconsByMetaTags() {
    // loop over the meta tags looking for those with type="persona" attributes
    $("meta[type=persona]").each(function (i) {
        // capture the icon attribute text so we can target the correct icon to display
        icon = $(this).attr("icon");

        // look for elements with class="personaIcon" and id="THE_ICON_VALUE_FROM_META_TAG"
        $(".personaIcon#" + icon).show();
    }); // end each function for meta tags type=persona
} // end showIconsByMetaTags function
</script>
```

## Adapting For Your Code

If you have an existing page with meta tags and icons (or other content) to show based on meta tags, you'll need to make some modifications to the code.

Ensure that your icon elements have a class that you can target that is unique to these items, but the same for all of them, such as `class="personaIcon"` as we have done here. They will also need unique IDs so each one can be targeted and this should be the same as an attribute in the meta tag.

For example, if you have `<img>`s instead of `<span>`s with different classes, it might look something like this:

```html
<img src="some/path/to/icons/image1.png" class="icons" id="icon1">
<img src="some/path/to/icons/image2.png" class="icons" id="icon2">
<img src="some/path/to/icons/image3.png" class="icons" id="icon3">
<img src="some/path/to/icons/image4.png" class="icons" id="icon4">
```

As in this alternative example above, you might not even have the inline style to hide the images (`style="display: none;"`) if your CSS has already hidden anything targeted with `img .icons`.

Let's presume a different set of meta tags as well:

```html
<meta function="show" persona="1">
<meta function="show" persona="2">
<meta function="show" persona="3">
<meta function="show" persona="4">
```

Here, you can see that the attributes _and_ values are all different from our example.

To make the jQuery/JavaScript code work with these different icon tags and meta tags, let's see what we'd need to change in the code.

Looking at the `showIconsbyMetaTags()` function, we see this:

```javascript
function showIconsByMetaTags() {
    // loop over the meta tags looking for those with type="persona" attributes
    $("meta[type=persona]").each(function (i) {
        // capture the icon attribute text so we can target the correct icon to display
        icon = $(this).attr("icon");

        // look for elements with class="personaIcon" and id="THE_ICON_VALUE_FROM_META_TAG"
        $(".personaIcon#" + icon).show();
    }); // end each function for meta tags type=persona
} // end showIconsByMetaTags function
```

To make that work with our new icons and meta tags code, we'll need to change a few things. First, let's change the way we target the meta tags:

In this line...

```javascript
// loop over the meta tags looking for those with type="persona" attributes
$("meta[type=persona]").each(function (i) {
```

...it's looking for a meta tag an attribute called `type` with a value of `persona`. Our new meta tags have an attribute of `function` with a value of `show`, so the jQuery/Javascript would need to look like this:

```javascript
// loop over the meta tags looking for those with type="persona" attributes
$("meta[function=show]").each(function (i) {
```

Now we need to change the icon name attribute from `icon` to `persona`. So, this line...

```javascript
// capture the icon attribute text so we can target the correct icon to display
icon = $(this).attr("icon");
```

...becomes this:

```javascript
// capture the icon attribute text so we can target the correct icon to display
icon = $(this).attr("persona");
```

Lastly, let's change how we target the icon elements, which are now `<img>`s instead of `<span>`s. This line...

```javascript
// look for elements with class="personaIcon" and id="THE_ICON_VALUE_FROM_META_TAG"
$(".personaIcon#" + icon).show();
```

...becomes this:

```javascript
// look for elements with class="personaIcon" and id="THE_ICON_VALUE_FROM_META_TAG"
$(".icons#" + icon).show();
```

If you have other elements with the `icons` class on the page, you may need to further target those `<img>` elements to be more specific, like this:

```javascript
// look for elements with class="personaIcon" and id="THE_ICON_VALUE_FROM_META_TAG"
$("img.personaIcon#" + icon).show();
```
