# fabric.TextAngle

A lightweight [Fabric.js](http://fabricjs.com/) custom object that flows text along a **diagonal or Bézier curve** — no external dependencies required.

> Extends `fabric.IText`, so text remains **double-click editable** directly on the canvas.

---

## Features

- 📐 **Diagonal / angled text** — place text along any straight or curved baseline
- 🖊️ **Inline editing** — double-click to edit text in place (falls back to flat layout while editing)
- 🎛️ **Draggable control points** — reshape the curve interactively via an SVG overlay
- ↕️ **Flip support** — render text above or below the baseline
- 🔡 **Custom kerning** — adjust letter spacing independently of the font
- 💾 **Serializable** — `toObject` / `fromObject` round-trip support
- 🚫 **Zero dependencies** — only Fabric.js itself required

---

## Demo

Open [`index.html`](./index.html) in a browser (no build step needed):

```
# Just serve the directory, e.g.:
npx serve .
# or simply open index.html directly in your browser
```

The demo lets you:
- Edit the text, color, font size, font family, and kerning live
- Toggle **Edit Control Points** to drag the endpoints and reshape the curve
- Double-click the text object to edit it inline on the canvas

---

## Installation

Copy [`TextAngle.js`](./TextAngle.js) into your project and load it **after** Fabric.js:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/fabric.js/5.3.0/fabric.min.js"></script>
<script src="./TextAngle.js"></script>
```

---

## Usage

### Basic

```js
const obj = new fabric.TextAngle('Hello, World!', {
  left: 200,
  top: 200,
  fontSize: 60,
  fill: '#cc5500',
});

canvas.add(obj);
```

### With custom control points

Control points define the Bézier curve. Each point is expressed as **normalized coordinates** relative to the total text width (`x`) and font size (`y`):

```js
const obj = new fabric.TextAngle('ANGLE EFFECT', {
  left: 400,
  top: 300,
  originX: 'center',
  originY: 'center',
  fontSize: 60,
  fontFamily: 'Arial',
  fill: '#cc5500',

  // Two points → straight diagonal line
  // Seven points → two-segment cubic Bézier
  ctrlPts: [
    { x: 0,   y: 1   },   // start (bottom-left area)
    { x: 1,   y: 0.4 },   // end   (upper-right area)
  ],

  kerning: 0,
  flipped: false,
  editable: true,
});
```

---

## Options

| Option | Type | Default | Description |
|---|---|---|---|
| `ctrlPts` | `Array<{x, y}>` | `[{x:0,y:1},{x:1,y:0.4}]` | Normalized Bézier control points. 2 points = diagonal line; 7 points = two cubic segments. |
| `kerning` | `number` | `0` | Extra spacing (px) between characters. Negative values tighten, positive loosen. |
| `flipped` | `boolean` | `false` | When `true`, text renders below the baseline instead of above. |

All standard `fabric.IText` options (`fontSize`, `fontFamily`, `fontWeight`, `fontStyle`, `fill`, `stroke`, `strokeWidth`, etc.) are also supported.

---

## Control Points Format

### Two-point (straight diagonal)

```js
ctrlPts: [
  { x: 0, y: 1 },   // start of baseline
  { x: 1, y: 0.4 }, // end of baseline
]
```

Internally converted to a cubic Bézier with the midpoint as both handles.

### Seven-point (two cubic Bézier segments)

```js
ctrlPts: [
  p0,   // segment 1 start
  p1,   // segment 1 control handle 1
  p2,   // segment 1 control handle 2
  p3,   // segment 1 end / segment 2 start
  p4,   // segment 2 control handle 1
  p5,   // segment 2 control handle 2
  p6,   // segment 2 end
]
```

---

## Serialization

`TextAngle` objects serialize and deserialize cleanly:

```js
// Serialize
const json = canvas.toJSON();

// Deserialize
fabric.util.enlivenObjects([json.objects[0]], ([obj]) => {
  canvas.add(obj);
});

// Or use fromObject directly
fabric.TextAngle.fromObject(objectData, (obj) => {
  canvas.add(obj);
});
```

The `ctrlPts`, `kerning`, and `flipped` properties are included automatically in `toObject`.

---

## API

### `set(key, value)`

Reactively updates the curve layout whenever any of `text`, `fontSize`, `fontFamily`, `fontWeight`, `fontStyle`, `fontVariant`, `kerning`, `ctrlPts`, or `flipped` change.

### `enterEditing()` / `exitEditing()`

Overrides Fabric's inline editing hooks — switches between curved rendering and flat layout for editing, then restores the curve on exit.

### `toObject(props)`

Returns a plain object representation including `ctrlPts`, `kerning`, and `flipped` alongside standard Fabric properties.

### `fabric.TextAngle.fromObject(object, callback)`

Static factory method for deserializing a `TextAngle` from a plain object (e.g., from `canvas.toJSON()`).

---

## Compatibility

- **Fabric.js**: 5.x (tested against 5.3.0)
- **Browser**: Any modern browser with Canvas 2D support

---

## License

MIT — see [LICENSE](./LICENSE).  
Copyright © 2026 Kenneth D'silva (Modracx)
