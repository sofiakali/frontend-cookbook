# Displaying long data lists

Lets say you have a list of 500 items. You don't want them to be rendered all the time because it will impact the performance. There is a technique called [_windowing_](https://reactjs.org/docs/optimizing-performance.html#virtualize-long-lists) that can be used to optimize the performance. In _windowing_, you render only items that can be currently seen on the screen.

## `react-virtualized` or `react-window`

We don't want to reinvent the wheel. We do want to use tested libraries that implement _windowing_. There are two well known libraries solving the same:

- [react-virtualized](https://www.npmjs.com/package/react-virtualized) the former library, solves a lot of use cases
- [react-window](https://www.npmjs.com/package/react-window) the latter library, created by the author of `react-virtualized`, lightweight solution supporting only horizontal/vertical lists and grids with fixed and variable item size.

**The author of the libraries recommends using `react-window` everytime it solves your use cases.**

## `react-window` Use Cases
There are two type of components for displaying data in the package: `List` for list data and `Grid` for tabular data. Both of them can be either `FixedSize` or `VariableSize`. This gives us:

* `FixedSizeList`
* `VariableSizeList`
* `FixedSizeGrid`
* `VariableSizeGrid`

The API for `Grid` is almost the same as for `List` but two dimensional.
### List With Dynamic Sizes

Sometime you need the container of the list to have dynamic height or width. For example, if you have the list of the size of the window. The problem is that you have to set the height and the width to the `List` as a number.

Luckily, there is a package [react-virtualized-auto-sizer](https://www.npmjs.com/package/react-virtualized-auto-sizer) that can help.

```javascript
import AutoSizer from "react-virtualized-auto-sizer";
import { FixedSizeList as List } from "react-window";

const MyList = ({ data }) => (
  <AutoSizer>
    {({ height, width }) => (
      <List
        ref={ref}
        height={height}
        width={width}
        itemData={data}
        itemCount={data.length}
        itemSize={100}
      >
        {ItemRenderer}
      </List>
    )}
  </AutoSizer>
);
```

### Dynamic `itemData` in VariableSizeList

When you use `VariableSizeList`, you have to set `itemSize` prop to be a function that accepts an index and returns the size. `react-window` then creates styles for the items and caches the styles. The problem is that these styles are cached unless you invalidate them, does't matter if `itemData` change or not.

To invalidate the styles when your `itemData` change you can use api of `List` and React lifecycle methods (or Hooks like in the example).

```javascript
import React from "react";
import { VariableSizeList as List } from "react-window";

const useResetCache = data => {
  const ref = React.useRef();
  // Invalidate the styles cache anytime `data` change
  React.useEffect(() => {
    ref.current.resetAfterIndex(0);
  }, [data]);
  return ref;
};

const MyList = ({ data }) => {
  const ref = useResetCache(data);
  return (
    <List
      ref={ref}
      height={height}
      width={width}
      itemData={data}
      itemCount={data.length}
      itemSize={index => {
        const item = data[index];
        return item.type === "my-type" ? 50 : 100;
      }}
      itemKey={index => {
        const item = data[index];
        return item.key;
      }}
    >
      {ItemRenderer}
    </List>
  );
};
```
