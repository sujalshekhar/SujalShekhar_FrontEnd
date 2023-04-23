# Q.1 - Explain what the simple List component does.

**Ans** List is simple react component which renders 'ul' items with "SingleListItem" component which contains 'li' tag. 

It iterates over the 'items' array props, which is an array of objects containing text key. Each time it renders 'SingleListItem' component with four props : 
<li>index : index of the items array</li>
<li>isSelected : a boolean prop which tells that whether item is selected or not, depending on item is selected or not, it changes background color.</li>
<li>onClickHandler : it takes item index as a input, when any item is getting clicked, this function gets called with item index.</li>
<li>text : this is the text element from the items array which is being passed as a prop to each 'SingleListItem' component</li>

With the use of useEffect hook, when items array is getting changed, it sets the value of 'selectedIndex' to null. However, it has an error in positions of 'selectedIndex' and 'setSelectedIndex'.

At the end, List component is the wrapper for 'WrappedListComponent' component inside memo function which is used to  unnecessary re-renders when its props don't change.

# Q.2 - What problems / warnings are there with code?

**Ans** Problems and warnings in this code are : 
<li>In 'SingleListItem' component, onClickHandler is not being called correctly because it is being called on the prop itself, which is not the right way of calling function. Everytime component re-renders onClickHandler function will called everytime even without clicking, which is not right.
</li>


```js
onClick = {() => onCLickHandler(index)}
```

<li>
In 'WrappedSingleListItem' component, index and isSelected props are not valided properly, it should be made required field. Corrected code is below : 
</li>

```js
index: PropTypes.number.isRequired,
isSelected : PropTypes.bool.isRequired
```

<li>
In 'SingleListItem' component, isSelected prop is not passed properly because it requires boolean but we are passing selectedIndex which is number. Corrected code is below :
</li>


```js
isSelected = {selectedIndex === index}
```

<li>
In 'WrappedListComponent' component, position of state variable in array destructuring is wrong. Because of this, we are changing the state through wrong variable, which is giving error that setSelectedIndex is not function. Corrected code is below :
</li>

```js
const [selectedIndex, setSelectedIndex] = useState();
```

<li>
In 'WrappedListComponent' component, 'selectedIndex' state is initialised to undefined, but we should initialise it to 'null'. Corrected code is below :
</li>

```js
const [selectedIndex, setSelectedIndex] = useState(null);
```

<li>
In 'WrappedListComponent' component, the syntax of validating items prop is wrong, correct code is below : 
</li>


```js
items: PropTypes.arrayOf(PropTypes.shape({
  text: PropTypes.string.isRequired,
})),

```

<li>
While setting the default props of 'WrappedListComponent' component, it is being initialised with 'null', but it should be initialised as empty array. Correct code is below : 
</li>

```js
WrappedListComponent.defaultProps = {
  items: []
};
```

<li>
In 'WrappedListComponent' component, while setting the value of state, we are getting type error, because we are using number variable as a function. Correct code is below :
</li>

```js
useEffect(() => {
    selectedIndex(null);
  }, [items]);

  const handleClick = index => {
    selectedIndex(index);
  };
```

<li>
We should use unique 'key' prop while using map function.
</li>

# Q-3 - Please fix, optimize, and/or modify the component as much as you think is necessary.

**Ans** 

```js

import React, { useState, useEffect, memo } from 'react';
import PropTypes from 'prop-types';

// Single List Item
const WrappedSingleListItem = ({
  index,
  isSelected,
  onClickHandler,
  text,
}) => {
  return (
    <li
      style={{ backgroundColor: isSelected ? 'green' : 'red'}}
      onClick={() => onClickHandler(index)} // changed 
    >
      {text}
    </li>
  );
};

WrappedSingleListItem.propTypes = {
  index: PropTypes.number.isRequired, // changed
  isSelected: PropTypes.bool.isRequired, // changed
  onClickHandler: PropTypes.func.isRequired,
  text: PropTypes.string.isRequired,
};

const SingleListItem = memo(WrappedSingleListItem);

// List Component
const WrappedListComponent = ({
  items,
}) => {
  const [selectedIndex, setSelectedIndex] = useState(); // changed

  useEffect(() => {
    setSelectedIndex(null);
  }, [items]);

  const handleClick = index => {
    setSelectedIndex(index);
  };

  return (
    <ul style={{ textAlign: 'left' }}>
      {items.map((item, index) => (
        <SingleListItem
          key={index} // changed
          onClickHandler={() => handleClick(index)}
          text={item.text}
          index={index}
          isSelected={selectedIndex === index} // changed
        />
      ))}
    </ul>
  )
};

WrappedListComponent.propTypes = {
  items: PropTypes.arrayOf(PropTypes.shape({ // changed
    text: PropTypes.string.isRequired,
  })),
};

WrappedListComponent.defaultProps = {
  items: [] // changed
};

const List = memo(WrappedListComponent);

export default List;


```

