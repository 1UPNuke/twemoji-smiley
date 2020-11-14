A simple library that replaces emoji that change based on hair, gender and/or race with standard circular yellow smileys.
The goal of this project is to make emoji inclusive, without adding a ton of complexity to include any and all facets of human life.

I will be deliberately ignoring the [Unicode 13.0 spec](https://unicode.org/versions/Unicode13.0.0/) and the [Emoji 13.0 spec](https://www.unicode.org/reports/tr51/tr51-18.html) to achieve this effect.

## API

Following are all the methods exposed in the `twemoji` namespace.

### twemoji.parse( ... ) V1

This is the main parsing utility and has 3 overloads per parsing type.

Although there are two kinds of parsing supported by this utility, we recommend you use [DOM parsing](https://github.com/twitter/twemoji#dom-parsing), explained below. Each type of parsing accepts a callback to generate an image source or an options object with parsing info.

The second kind of parsing is string parsing, explained in the legacy documentation [here](https://github.com/twitter/twemoji/blob/master/LEGACY.md#string-parsing). This is unrecommended because this method does not sanitize the string or otherwise prevent malicious code from being executed; such sanitization is out of scope.

#### DOM parsing

If the first argument to `twemoji.parse` is an `HTMLElement`, generated image tags will replace emoji that are **inside `#text` nodes only** without compromising surrounding nodes or listeners, and completely avoiding the usage of `innerHTML`.

If security is a major concern, this parsing can be considered the safest option but with a slight performance penalty due to DOM operations that are inevitably *costly*.

```js
var div = document.createElement('div');
div.textContent = 'I \u2764\uFE0F emoji!';
document.body.appendChild(div);

twemoji.parse(document.body);

var img = div.querySelector('img');

// note the div is preserved
img.parentNode === div; // true

img.src;        // https://twemoji.maxcdn.com/v/latest/72x72/2764.png
img.alt;        // \u2764\uFE0F
img.className;  // emoji
img.draggable;  // false

```

All other overloads described for `string` are available in exactly the same way for DOM parsing.

### Object as parameter

Here's the list of properties accepted by the optional object that can be passed to the `parse` function.

```js
  {
    callback: Function,   // default the common replacer
    attributes: Function, // default returns {}
    base: string,         // default MaxCDN
    ext: string,          // default ".png"
    className: string,    // default "emoji"
    size: string|number,  // default "72x72"
    folder: string        // in case it's specified
                          // it replaces .size info, if any
  }
```

#### callback

The function to invoke in order to generate image `src`(s).

By default it is a function like the following one:

```js
function imageSourceGenerator(icon, options) {
  return ''.concat(
    options.base, // by default Twitter Inc. CDN
    options.size, // by default "72x72" string
    '/',
    icon,         // the found emoji as code point
    options.ext   // by default ".png"
  );
}
```

#### base

The default url is the same as `twemoji.base`, so if you modify the former, it will reflect as default for all parsed strings or nodes.

#### ext

The default image extension is the same as `twemoji.ext` which is `".png"`.

If you modify the former, it will reflect as default for all parsed strings or nodes.

#### className

The default `class` for each generated image is `emoji`. It is possible to specify a different one through this property.

##### size

The default asset size is the same as `twemoji.size` which is `"72x72"`.

If you modify the former, it will reflect as default for all parsed strings or nodes.

#### folder

In case you don't want to specify a size for the image. It is possible to choose a folder, as in the case of SVG emoji.

```js
twemoji.parse(genericNode, {
  folder: 'svg',
  ext: '.svg'
});
```

This will generate urls such `https://twemoji.maxcdn.com/svg/2764.svg` instead of using a specific size based image.

## Utilities

Basic utilities / helpers to convert code points to JavaScript surrogates and vice versa.

### twemoji.convert.fromCodePoint()

For a given HEX codepoint, returns UTF-16 surrogate pairs.

```js
twemoji.convert.fromCodePoint('1f1e8');
 // "\ud83c\udde8"
```

### twemoji.convert.toCodePoint()

For given UTF-16 surrogate pairs, returns the equivalent HEX codepoint.

```js
 twemoji.convert.toCodePoint('\ud83c\udde8\ud83c\uddf3');
 // "1f1e8-1f1f3"

 twemoji.convert.toCodePoint('\ud83c\udde8\ud83c\uddf3', '~');
 // "1f1e8~1f1f3"
```

## Tips

### Inline Styles

If you'd like to size the emoji according to the surrounding text, you can add the following CSS to your stylesheet:

```css
img.emoji {
   height: 1em;
   width: 1em;
   margin: 0 .05em 0 .1em;
   vertical-align: -0.1em;
}
```

This will make sure emoji derive their width and height from the `font-size` of the text they're shown with. It also adds just a little bit of space before and after each emoji, and pulls them upwards a little bit for better optical alignment.

### UTF-8 Character Set

To properly support emoji, the document character set must be set to UTF-8. This can done by including the following meta tag in the document `<head>`

```html
<meta charset="utf-8">
```

### Exclude Characters (V1)

To exclude certain characters from being replaced by twemoji.js, call twemoji.parse() with a callback, returning false for the specific unicode icon. For example:

```js
twemoji.parse(document.body, {
    callback: function(icon, options, variant) {
        switch ( icon ) {
            case 'a9':      // © copyright
            case 'ae':      // ® registered trademark
            case '2122':    // ™ trademark
                return false;
        }
        return ''.concat(options.base, options.size, '/', icon, options.ext);
    }
});
```

## Contributing

The contributing documentation can be found [here](https://github.com/1UPNuke/twemoji-smiley/tree/master/CONTRIBUTING.md).

## Attribution Requirements

As an open source project, attribution is critical from a legal, practical and motivational perspective in our opinion. The graphics are licensed under the CC-BY 4.0 which has a pretty good guide on [best practices for attribution](https://wiki.creativecommons.org/Best_practices_for_attribution).

However, we consider the guide a bit onerous and as a project, will accept a mention in a project README or an 'About' section or footer on a website. In mobile applications, a common place would be in the Settings/About section (for example, see the mobile Twitter application Settings->About->Legal section). We would consider a mention in the HTML/JS source sufficient also.

## License

Copyright 2019 Twitter, Inc and other contributors

Code licensed under the MIT License: <http://opensource.org/licenses/MIT>

Graphics licensed under CC-BY 4.0: <https://creativecommons.org/licenses/by/4.0/>
