## 1. Floating and clearing

### Float

- The float CSS property specifies that an element should be taken from the normal flow and placed along the left or right side of its container, where text and inline elements will wrap around it.
- when an element is floated it is taken out of the normal flow of the document. It is shifted to the left or right until it touches the edge of its **containing box** or another **floated element**.
- As float implies the use of the block layout, it modifies the computed value of the display value to `block`.

#### Fixing margin collapsing for the floats' container

Solution 1: Clear the floats for the last element in the floats' container.

```css
#container:after {
	content: "";
	display: block;
	clear: both;
}
```

Solution 2: Set the `overflow` property for the floats' container.

```css
#container {
	overflow: auto | hidden;
}
```

Solution 3: Use inline-block instead of float

- Inline-block behaves the same as inline elements. It will be added to the same line and pushed to the next line when it wraps.
- But unline inline element, you can set `width` and `height` for it just like `block` elements.
- When using set `display` property to `inline-block`, you will probably want to set `vertical-align` to `top` so all `inline-block` elements can align properly.

**[JSFiddle demo](http://jsfiddle.net/haixuanc/r2usqqwp/4/)**

### Clear

- The `clear` CSS property specifies whether an element can be next to `floating` elements that precede it or must be moved down (**cleared**) below them.
- The `clear` property applies to both floating and non-floating elements.
- When applied to non-floating blocks, it moves the border edge of the element down until it is below the margin edge of all relevent floats. This movement (when it happens) causes **margin collapsing** not to occur.

## 2. Inline vs block elements

**Block:** Takes up the full width available, with a new line before and after (`display:block;`)

**Inline:** Takes up only as much width as it needs, and does not force new lines (`display:inline;`)

## 3. Display

**[Tutorial on CSS Layout, Responsive Design, Media Queries, inline-block, column, flexbox](http://learnlayout.com)**

### `box-sizing`

Set `box-sizing` to `border-box` will prevent `padding` and `border` from increasing the size of an element. That is, the element overall width is only determined by the `width` property.

### Media queries

An example:

```css
@media screen and (min-width:600px) {
  nav {
    float: left;
    width: 25%;
  }
  section {
    margin-left: 25%;
  }
}

@media screen and (max-width:599px) {
  nav li {
    display: inline;
  }
}
```

## 4. Selectors

### Rule specificity and cascading

- If more than one rule applies to an element and specifies the same property, then CSS gives priority to the rule that has the more specific selector. An ID selector is more specific than a class selector, which in turn is more specific than a tag selector.
- If the stylesheet has conflicting rules and they are equally specific, then CSS gives priority to the rule that is later in the stylesheet.
- When you have a problem with conflicting rules, try to resolve it by making one of the rules more specific, so that it has priority. If you cannot do that, try moving one of the rules nearer the end of the stylesheet so that it has priority.

### Selectors based on relationships

| Selector | Selects |
|----------|---------|
| A E	        | Any E element that is a descendant of an A element (that is: a child, or a child of a child, etc.) |
| A > E	        | Any E element that is a child (i.e. direct descendant) of an A element |
| E:first-child	| Any E element that is the first child of its parent |
| B + E	        | Any E element that is the next sibling of a B element (that is: the next child of the same parent) |

