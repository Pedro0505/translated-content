---
title: HTMLElement.offsetLeft
slug: Web/API/HTMLElement/offsetLeft
translation_of: Web/API/HTMLElement/offsetLeft
---
{{ APIRef("HTML DOM") }}

Свойство **offsetLeft** содержит левое смещение элемента относительно offsetParent. Содержит расстояние от offsetParent до границы элемента.

For block-level elements, `offsetTop`, `offsetLeft`, `offsetWidth`, and `offsetHeight` describe the border box of an element relative to the `offsetParent`.

However, for inline-level elements (such as **span**) that can wrap from one line to the next, `offsetTop` and `offsetLeft` describe the positions of the _first_ border box (use {{domxref("Element.getClientRects()")}} to get its width and height), while `offsetWidth` and `offsetHeight` describe the dimensions of the _bounding_ border box (use {{domxref("Element.getBoundingClientRect()")}} to get its position). Therefore, a box with the left, top, width and height of `offsetLeft`, `offsetTop`, `offsetWidth` and `offsetHeight` will not be a bounding box for a span with wrapped text.

## Syntax

```
left = element.offsetLeft;
```

`left` is an integer representing the offset to the left in pixels _from the closest relatively positioned_ parent element.

## Example

```js
var colorTable = document.getElementById("t1");
var tOLeft = colorTable.offsetLeft;

if (tOLeft > 5) {
  // large left offset: do something here
}
```

This example shows a 'long' sentence that wraps within a div with a blue border, and a red box that one might think should describe the boundaries of the span.

![Image:offsetLeft.jpg](/@api/deki/files/790/=OffsetLeft.jpg)

```html
<div style="width: 300px; border-color:blue; border-style:solid; border-width:1;">
  <span>Short span. </span>
  <span id="longspan">Long span that wraps within this div.</span>
</div>

<div id="box" style="position: absolute; border-color: red; border-width: 1; border-style: solid; z-index: 10">
</div>

<script type="text/javascript">
  var box = document.getElementById("box");
  var longspan = document.getElementById("longspan");
  box.style.left = longspan.offsetLeft + document.body.scrollLeft + "px";
  box.style.top = longspan.offsetTop + document.body.scrollTop + "px";
  box.style.width = longspan.offsetWidth + "px";
  box.style.height = longspan.offsetHeight + "px";
</script>
```

## Specification

| Specification                                                                                    | Status                           | Comment |
| ------------------------------------------------------------------------------------------------ | -------------------------------- | ------- |
| {{SpecName('CSSOM View', '#dom-htmlelement-offsetleft', 'offsetLeft')}} | {{Spec2('CSSOM View')}} |         |

## Browser compatibility

{{Compat}}

## See also

- {{domxref("HTMLElement.offsetParent")}}, {{domxref("HTMLElement.offsetTop")}}, {{domxref("HTMLElement.offsetWidth")}}, {{domxref("HTMLElement.offsetHeight")}}
