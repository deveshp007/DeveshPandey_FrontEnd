# Frontend Engineer Assignment

## React Component Code Review

## Introduction :

<b>Based on the code below answer the following queries:</b>

---

<b>1.</b> Explain what the simple List component does.

_Solution_ :

The simple List component is a UI element that displays a collection of items in a vertical or horizontal list. Each item can have a text, image, icon etc. It provides a clean and simple way to display a list of items.

The given code defines a simple React component called `List` that renders a list of items into a list which is created using the `ul` and `li` HTML elements. The component takes an array of items as a prop. In the given code it is having two sub components `WrappedSingleListItem` and `WrappedListComponent`.

The List component renders a `ul` element containing a list of `SingleListItem` components. The `SingleListItem` component is a memoized functional component that renders a `li` element containing the text of the item it represents.

And the `WrappedListComponent` renders the list by mapping over the array of items and passing each item to the SingleListItem component, which is a memoized version of WrappedSingleListItem.

When an item is clicked, the `onClickHandler` function is called, which sets the `selectedIndex` state to the index of the clicked item. This causes the background color of the clicked item to change to green while the background color of the previously clicked item (if any) is changed back to red.

The `List` component uses the `useState` hook to keep track of the selected index and the `useEffect` hook to reset the selected index to null when the items prop changes.

---

<b>2.</b> What problems / warnings are there with code?

_Solution_ :

1. In the `SingleListItem` component, the `onClickHandler` prop is not being passed the index properly. Instead of passing a callback function, it's immediately invoking the `handleClick` function and passing its return value (which is undefined) to the `onClick` prop. To fix this, you can change the prop to:

```js
onClickHandler={() => onClickHandler(index)}
```

2. In the `WrappedListComponent` component, the `selectedIndex` state is being initialized without a default value, which can lead to unexpected behavior. To fix this, you can initialize the state to null:

```js
const [selectedIndex, setSelectedIndex] = useState(null);
```

3. In the `WrappedListComponent` component, the `isSelected` prop is being passed the `selectedIndex` state directly, which is a number. However, the prop is expecting a boolean value. To fix this, you can change the prop to:

```js
isSelected={selectedIndex === index}
```

4. In the `WrappedListComponent` component, the items prop is being defined as an array of objects with a text property, but the syntax is incorrect. Instead of `PropTypes.array(PropTypes.shapeOf({...}))`, it should be:

```js
PropTypes.arrayOf(
  PropTypes.shape({
    text: PropTypes.string.isRequired,
  })
);
```

5. In the `WrappedListComponent` component, the items prop is being set to null by default, but the prop type is defined as an array. It's better to set the default value to an empty array, like this:

```js
WrappedListComponent.defaultProps = {
  items: [],
};
```

---

<b>3.</b> Please fix, optimize, and/or modify the component as much as you think is necessary.

_Solution_ :

```js
import React, { useState, useCallback, useMemo } from "react";
import PropTypes from "prop-types";

// Single List Item
const SingleListItem = React.memo(({ index, isSelected, onClick, text }) => {
  return (
    <li
      style={{ backgroundColor: isSelected ? "green" : "red" }}
      onClick={onClick}
    >
      {text}
    </li>
  );
});

SingleListItem.propTypes = {
  index: PropTypes.number.isRequired,
  isSelected: PropTypes.bool.isRequired,
  onClick: PropTypes.func.isRequired,
  text: PropTypes.string.isRequired,
};

// List Component
const List = ({ items, multiple }) => {
  const [selectedIndexes, setSelectedIndexes] = useState([]);

  const handleClick = useCallback(
    (index) => {
      if (multiple) {
        if (selectedIndexes.includes(index)) {
          setSelectedIndexes(selectedIndexes.filter((i) => i !== index));
        } else {
          setSelectedIndexes([...selectedIndexes, index]);
        }
      } else {
        setSelectedIndexes([index]);
      }
    },
    [multiple, selectedIndexes, setSelectedIndexes]
  );

  const memoizedItems = useMemo(
    () =>
      items.map((item, index) => (
        <SingleListItem
          key={index}
          onClick={() => handleClick(index)}
          text={item.text}
          index={index}
          isSelected={selectedIndexes.includes(index)}
        />
      )),
    [items, selectedIndexes, handleClick]
  );

  return <ul style={{ textAlign: "left" }}>{memoizedItems}</ul>;
};

List.propTypes = {
  items: PropTypes.arrayOf(
    PropTypes.shape({
      text: PropTypes.string.isRequired,
    })
  ).isRequired,
  multiple: PropTypes.bool,
};

List.defaultProps = {
  multiple: false,
};

export default List;
```

In the above code, I have implemented the following changes:

- The List component now takes a multiple prop which controls whether multiple items can be selected at once.

- removed `useEffect` and replaced with a `useMemo` hook to memoize the array of items that are rendered in the list. Since the items are pure and dependent on the items and `selectedIndex` state variables, this will prevent unnecessary re-renders of the items in the list.

- The `handleClick` function has been wrapped with `useCallback` to prevent unnecessary re-creation of the function reference on every render. And it has been updated to handle both single and multiple selection. If multiple is true, clicking an item will toggle its selection. If multiple is false, clicking an item will select it and deselect any other selected items. This can be done as:

```js
return (
  <div>
    <h1>List</h1>
    <List items={items} multiple={true} />
  </div>
);
```
<br>

<b>Live Deplpoyment: https://frontend-assignment-steeleye.netlify.app/</b>

---