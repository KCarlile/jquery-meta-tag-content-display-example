# jQuery Meta Tag Example

## Table Of Contents
1. [Overview](#overview)
   1. [File Index](#file-index)
1. [Try It Yourself](#try-it-yourself)
1. [How Does It Work?](#how-does-it-work)
   1. [Page Execution](#page-execution)
   1. [HTML](#html)
   1. [jQuery/JavaScript](#jqueryjavascript)
   1. [Copying/Pasting Into Your Code](#copyingpasting-into-your-code)
1. [Adapting For Your Code](#adapting-for-your-code)
   1. [Different Markup](#different-markup)
   1. [Changing The jQuery/JavaScript](#changing-the-jqueryjavascript)
      1. [Finding The New Meta Tags](#finding-the-new-meta-tags)
      1. [Targeting The New Elements](#targeting-the-new-elements)
      1. [More Specific Targeting](#more-specific-targeting)
1. [Dynamic Selector Version](#dynamic-selector-version)

## Overview

This code example demonstrates a technique to use jQuery to show persona icons (or any content) based on the presence of meta tags. For example, let's say you want to display persona icons on a page to illustrated the target audience. You can have a set of icons on the page that are hidden and then enable display of certain persona icons by adding a meta tag to trigger display of that persona icon.

### Concept

The code provided here is presented as a generic, platform-independent example. If you want to apply this concept to a specific platform, you may wish to simplify the code, but some of this code may be useful for reference. The overall concept being used here is as follows:

1. When the page loads, all possible persona icons are present, but hidden from display.
1. Look for the meta tag(s) containing the associated persona values for the page.
1. For each persona value, show the hidden role icon associated with that persona value.

Or, for a more technical summary:

- An HTML page exists with some meta tags that define some variables for the page. In this case, those meta tags define the personas/roles that apply to the content on this page.
  - The meta tag can be a singular tag:
    ```html
    <meta name="personas" content="Admin, ContentManager, Reviewer">
    ```
  - Or, the meta tag can be multiple meta tags with the same name and one value per tag:
    ```html
    <meta name="persona" content="Admin">
    <meta name="persona" content="Content Manager">
    <meta name="persona" content="Reviewer">
    ```
    However, the code in this example assumes multiple meta tags.
- On the HTML page, there are _N_ role icons where _N_ is equal to the max number of personas and the role icons have CSS (inline or global) that sets `display: none;`. The role icon items must also have an ID that matches one of the personas and a class that matches the meta tag name. (Technically, these don't actually need to be identical, but you have to do some mapping if they aren't the same.)
  - If you wrap your role icons in `<div>`s or any other markup, you may want to put the `display: none;` styling on the wrapper rather than the `<img>` itself.
  - Example:
    ```html
    <img src="images/role1.png" class="personas" id="Admin" style="display: none;" />
    <img src="images/role2.png" class="personas" id="ContentManager" style="display: none;" />
    <img src="images/role3.png" class="personas" id="Reviewer" style="display: none;" />
    ```
- Add some JavaScript that does the following:
  - Once the page has been fully loaded, get the personas for the page:
    - A) For multiple meta tags each with a singular value, loop over the meta tags where `name = "persona"` and capture each role.
    - B) For one meta tag with multiple values, find the meta tag with `name = "personas"` and split the values by `,` to capture each role.
  - Loop over the personas and role icons (by `class="personas"`) to set the role icons to visible where there is a match (`style="display: block;"`, `style="display: inline;"`, or remove the `display` property completely from the element).

### File Index

| File | Description |
|------|-------------|
|[index.html](index.html)|Simple example with the least amount of code.|
|[index-code.html](index-code.html)|Simple example with code displayed on the page for reference.|
|[index-dynamic-selector.html](index-dynamic-selector.html)|Example with parameterized options for more flexible usage.|
|[metatags.html](metatags.html)|Example of looping over meta tags and displaying them and their attributes.|
|[README.md](README.md)|This file.|

## Try It Yourself

If you download any of the `*.html` files, you can open them in a browser to test the functionality. However, your computer must be connected to the internet as the jQuery libraries are loaded via CDN. (PrismJS libraries are also loaded via CDN in the `index-code.html` example.)

## How Does It Work?

There are multiple elements that make this functionality work.

- A jQuery CDN include is added below the page content, but before the `</body>` tag, so we can leverage the jQuery library loaded after HTML content.
- jQuery/JavaScript code does the dynamic work for us (more on this later) which exists between the jQuery CDN include and the `</body>` tag.
- A set of HTML elements (`<span>`s in this case) represent the persona icons with attributes that are selectable by jQuery, such as:
`<span class="personaIcon" id="someUniquePersonaName" style="display: none;">[Some emoji code]</span>`\
(The `style="display: none;"` part is important as we want these icons to start hidden so we only show specific ones based on the meta tags present on the page.)
Alternately, you could just use `<img>` elements with images and the same class and id attributes.
- A set of meta tags is used to trigger display of the persona icons, such as:
`<meta type="persona" icon="cowboy">`

### Page Execution

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

### Copying/Pasting Into Your Code

Each file has some markers to indicate where to start and stop copying the code to paste into your page. Those markers look like this:

```html
<!-- ########## START COPY/PASTE ########## -->
<html-and-js-code>
    ...
</html-and-js-code>
<!-- ########## END COPY/PASTE ########## -->
```

That block should be pasted just above your `</body>` tag.

## Adapting For Your Code

If you have an existing page with meta tags and icons (or other content) to show based on meta tags, you'll need to make some modifications to the code.

### Different Markup

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

### Changing The jQuery/JavaScript

To make the jQuery/JavaScript code work with these different icon tags and meta tags, let's see what we'd need to change in the code.

#### Finding The New Meta Tags

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

Now we need to change the icon name attribute from `icon` to `persona`. Look for this code:

```javascript
// capture the icon attribute text so we can target the correct icon to display
icon = $(this).attr("icon");
```

...and make this change:

<table>
    <tr>
        <td>Before</td>
        <td>After</td>
    </tr>
    <tr>
<td>

```javascript
icon = $(this).attr("icon");
```

</td>
<td>

```javascript
icon = $(this).attr("persona");
```

</td>
    </tr>
</table>

#### Targeting The New Elements

Lastly, let's change how we target the icon elements, which are now `<img>`s instead of `<span>`s. Look for this code:

```javascript
// look for elements with class="personaIcon" and id="THE_ICON_VALUE_FROM_META_TAG"
$(".personaIcon#" + icon).show();
```

...and make this change:

<table>
    <tr>
        <td>Before</td>
        <td>After</td>
    </tr>
    <tr>
<td>

```javascript
$(".personaIcon#" + icon).show();
```

</td>
<td>

```javascript
$(".icons#" + icon).show();
```

</td>
    </tr>
</table>

#### More Specific Targeting

If you have other elements with the `icons` class on the page, you may need to further target those `<img>` elements to be more specific, like this:

```javascript
// look for elements with class="personaIcon" and id="THE_ICON_VALUE_FROM_META_TAG"
$("img.personaIcon#" + icon).show();
```

## Dynamic Selector Version

You can try using the dynamic selector code version of this in this file: [index-dynamic-selector.html](index-dynamic-selector.html)

__:warning: This code has not been tested with all possible meta tag and icon options. :warning:__

The primary difference in this file is that an attempt has been made to make this configurable for different options so that it's more flexible. You can change the values in the constants to tailor it to your meta tags and icons without having to modify the rest of the code.

These are the constants that you can configure:

```javascript
// ---------- START CONFIG constants ----------
/**
 * Name of the attribute and its value for identifying these types of meta tags.
 * For example: <meta type="persona" icon="cowboy"> would be "type=persona".
 */
const META_TAG_SELECTOR = "type=persona";

/**
 * Name of the attribute containing the icon's identifier.
 * For example: <meta type="persona" icon="cowboy"> would be "icon".
 */
const META_TAG_ICON_VALUE = "icon";

/**
 * jQuery selector for finding the icon set, not an individual one.
 * For example: <span style="display: none;" class="personaIcon" id="cowboy">&#129312;</span>
 * would be "span.personaIcon" to indicate <span> tags with a class="personaIcon".
 */
const ICON_GROUP_SELECTOR = "span.personaIcon";

/**
 * jQuery selector for the icon's unique identifier, which is usually an id attribute.
 * For example: <span style="display: none;" class="personaIcon" id="cowboy">&#129312;</span>
 * would be "#" as the id attribute contains the unique identifier.
 */
const ICON_SELECTOR = "#";
// ---------- END CONFIG constants ----------
```
