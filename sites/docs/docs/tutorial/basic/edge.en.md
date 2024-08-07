---
nav: Guide
group:
  title: Basics
  order: 1
title: Edge
order: 2
toc: content
---

Like nodes, LogicFlow has some basic edges built in.

1. line
2. polyline
3. bezier

The effect is as follows：

<code id="edge-shapes" src="../../../src/tutorial/basic/edge/shapes"></code>

## Selecting built-in edges inherited by custom edges

```tsx | pure
// line
import { LineEdge, LineEdgeModel } from '@logicflow/core'
// polyline
import { PolylineEdge, PolylineEdgeModel } from '@logicflow/core'
// bezier
import { BezierEdge, BezierEdgeModel } from '@logicflow/core'
```

## Inheritance-based customization of edges

Like nodes, LogicFlow edges support inheritance-based customization. Again, you only need to inherit
from both `view` and `model`.
However, unlike nodes, in most cases it is not recommended to customize `view` when customizing
edges due to the editing complexity of edges.
You just need to customize the style class in [edgeModel](../../api/edgeModel.en.md).

<code id="edge-custom" src="../../../src/tutorial/basic/edge/custom"></code>

:::info{title=提示}
Custom edges also need to be registered using `register`.
:::

## Customize side text position

By default, the position of the text on the edge is the position when the user double-clicks on the
edge. If the text is added to the edge via the API, the text position will be as follows.

- line: the middle of the start and end points
- polyline: middle of the longest line segment
- bezier: middle of start, end, and adjustment points.

LogicFlow allows developers to customize the text position, e.g. the text position will always be
next to the start of the edge. The way to define this is to set the property `customTextPosition` to
true, then override the `getTextPosition` method, the coordinates sent back by this method are the
coordinates of the text.

<code id="edge-text" src="../../../src/tutorial/basic/edge/textPosition"></code>

## Customize the type of edges between nodes

By default, edges generated by manually connecting nodes from anchors are of the type specified by
the initialization `edgeType`, which can also be specified by `lf.setDefaultEdgeType(edgeType)`. But
when the edge types needed to connect different nodes to each other are different, the only way to
customize the types of the edges between nodes is to customize the edge types between nodes.

```tsx | pure
const lf = new LogicFlow({
  ...,
  // Set default margins manually
  edgeType: 'bezier',
  // currentEdge information when moving existing edges, otherwise null
  edgeGenerator: (sourceNode, targetNode, currentEdge) => {
    // custom-edge
    if (sourceNode.type === 'rect') return 'custom-edge'
  }
})

```

## Customize arrows

After version `1.1.27`, LogicFlow supports individual customization of the arrows at each end of a
line. As with previous customization methods, full customization is supported by customizing basic
data such as size by theme and by overriding the corresponding methods.

### Theme settings

```tsx | pure
lf.setTheme({
  arrow: {
    offset: 4, // Arrow vertical line length
    verticalLength: 2, // Arrow bottom line length
  },
});
```

### Customizing arrow shapes

When customizing the line view, you can override the `getEndArrow` and `getStartArrow` methods to
customize the shapes of the ends of the line, which can be any of the `svg` shapes returned by the
two methods.

Here's an example of controlling the appearance of a line by using the arrowType in the line's
properties.

<code id="custom-arrow" src="../../../src/tutorial/basic/edge/arrow"></code>

### Customize the adjustment point style

When initializing a LogicFlow instance, you can enable the function of adjusting the start and end
points of edges with the parameter `adjustEdgeStartAndEnd`.

When customizing the line view, you can override the `getAdjustPointShape` method to achieve the
custom adjust point shape.

```tsx | pure
// lf.js
const lf = new LogicFlow({
  adjustEdgeStartAndEnd: true,
});

// edge.js
class CustomEdge extends LineEdge {
  getAdjustPointShape(x, y, edgeModel) {
    return h("g", {}, [
      h("image", {
        x: x - 9,
        y: y - 9,
        width: 18,
        height: 18,
        cursor: "move",
        href: "data:image/svg+xml;base64,PCFET0NUWVBFIHN2ZyBQVUJMSUMgIi0vL1czQy8vRFREIFNWRyAxLjEvL0VOIiAiaHR0cDovL3d3dy53My5vcmcvR3JhcGhpY3MvU1ZHLzEuMS9EVEQvc3ZnMTEuZHRkIj48c3ZnIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgeG1sbnM6eGxpbms9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGxpbmsiIHdpZHRoPSIyMnB4IiBoZWlnaHQ9IjIycHgiIHZlcnNpb249IjEuMSI+PGNpcmNsZSBjeD0iMTEiIGN5PSIxMSIgcj0iNyIgc3Ryb2tlPSIjZmZmIiBmaWxsPSIjMjliNmYyIi8+PGNpcmNsZSBjeD0iMTEiIGN5PSIxMSIgcj0iMyIgc3Ryb2tlPSIjZmZmIiBmaWxsPSJ0cmFuc3BhcmVudCIvPjwvc3ZnPg==",
      }),
    ]);
  }
}
```

<a href="https://codesandbox.io/embed/logicflow026-edgeanimation-forked-fdg3v0?fontsize=14&hidenavigation=1&theme=dark" target="_blank">
Go to CodeSandbox for examples</a>

