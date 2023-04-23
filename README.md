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
)
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
