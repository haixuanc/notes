## 1. Floating and clearing

### Float

- The float CSS property specifies that an element should be taken from the normal flow and placed along the left or right side of its container, where text and inline elements will wrap around it.
- when an element is floated it is taken out of the normal flow of the document. It is shifted to the left or right until it touches the edge of its **containing box** or another **floated element**.
- As float implies the use of the block layout, it modifies the computed value of the display value to `block`.

<h2>Examples</h2>
<div>
<p class="rside">This is some text. This is some text. This is some text.
    This is some text. This is some text. This is some text.</p>
<p class="lside">This is some text. This is some text. This is some text.
    This is some text. This is some text. This is some text.</p>
<p> This is some text. This is some text. This is some text.
    This is some text. This is some text. This is some text.
    This is some text. This is some text. This is some text.
    This is some text. This is some text. This is some text.
    This is some text. This is some text. This is some text.
    This is some text. This is some text. This is some text.
    This is some text. This is some text. This is some text.</p>
</div>

<h2>Styles Used</h2>


### Clear

- The `clear` CSS property specifies whether an element can be next to `floating` elements that precede it or must be moved down (**cleared**) below them.
- The `clear` property applies to both floating and non-floating elements.
- When applied to non-floating blocks, it moves the border edge of the element down until it is below the margin edge of all relevent floats. This movement (when it happens) causes **margin collapsing** not to occur.
