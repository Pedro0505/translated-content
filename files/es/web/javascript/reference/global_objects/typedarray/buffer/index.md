---
title: TypedArray.prototype.buffer
slug: Web/JavaScript/Reference/Global_Objects/TypedArray/buffer
tags:
  - Buffer
  - JavaScript
  - Propiedad
translation_of: Web/JavaScript/Reference/Global_Objects/TypedArray/buffer
original_slug: Web/JavaScript/Referencia/Objetos_globales/TypedArray/buffer
---
{{JSRef}}

La propiedad de acceso **`buffer`** representa el {{jsxref("ArrayBuffer")}} referenciada por un _TypedArray_ en tiempo de construcción.

## Sintaxis

```
typedArray.buffer
```

## Descripción

La propiedad `buffer` es una propiedad de acceso cuya función _set accessor_ es `undefined`, significa que tu sólo puedes leer esta propiedad. El valor es establecido cuando _TypedArray_ se construye y no puede ser cambiado. _TypedArray_ es uno de los [TypedArray objects](/es/docs/Web/JavaScript/Reference/Global_Objects/TypedArray#TypedArray_objects).

## Ejemplos

### Usando la propiedad `buffer`

```js
var buffer = new ArrayBuffer(8);
var uint16 = new Uint16Array(buffer);
uint16.buffer; // ArrayBuffer { byteLength: 8 }
```

## Especificaciones

| Especificación                                                                                                                   | Estatus                      | Comentario          |
| -------------------------------------------------------------------------------------------------------------------------------- | ---------------------------- | ------------------- |
| {{SpecName('ES6', '#sec-get-%typedarray%.prototype.buffer', 'TypedArray.prototype.buffer')}}     | {{Spec2('ES6')}}         | Definición inicial. |
| {{SpecName('ESDraft', '#sec-get-%typedarray%.prototype.buffer', 'TypedArray.prototype.buffer')}} | {{Spec2('ESDraft')}} |                     |

## Compatibilidad del navegador

{{Compat("javascript.builtins.TypedArray.buffer")}}

## Véase también

- [JavaScript typed arrays](/es/docs/Web/JavaScript/Typed_arrays)
- {{jsxref("TypedArray")}}
