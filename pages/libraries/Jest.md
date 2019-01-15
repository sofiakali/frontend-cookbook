# 🤡 Jest

[Jest](https://jestjs.io/) is javascript testing framework. We use it mainly for *unit testing* of React components and complex Redux selectors.

## Unit tests examples

### React components

We test React components in complement with [Enzyme](https://airbnb.io/enzyme/), utility testing library for React.

In this example, `RatingSimple` is component that renders 0 to 5 stars based on `rating` prop. It has also two sizes - *normal* and *small*.

```jsx
import React from 'react';
import { shallow } from 'enzyme';

import RatingSimple, { colors } from '../RatingSimple';

describe('RatingSimple', () => {
    it('renders with 0 filled stars', () => {
        const wrapper = shallow(<RatingSimple />);

        expect(wrapper.find('Icon').length).toBe(5);
        expect(wrapper.find({ color: colors.DEFAULT }).length).toBe(5);
    });

    it('renders small', () => {
        const wrapper = shallow(<RatingSimple small />);
        expect(wrapper.find('.rating__stars--small').length).toBe(1);
    });
});
```

### Complex Redux selectors

Selectors can often contain complex logic especially when combining more entities together.

In the example below, there is [reselect](https://github.com/reduxjs/reselect) selector for maping product names to user's product ids. *Note: for the sake of example, let's assume it is not obtained from API in this form.*

```javascript
// selector.js
import { createSelector } from 'reselect';

const user = state => state.user;
const products = state => state.products;

export const userProductsSelector = createSelector(
    user,
    products,
    (user, products) => ({
        ...user,
        productNames: products.filter(product => user.productIds.includes(product.id))
    })
);
```

```javascript
// selector.test.js
import { userProductsSelector } from '../selector.js';

describe('userProducts selector', () => {
    const state = {
        user: { id: 1, name: 'Tomáš Král', productIds: [1, 2] },
        products: [
            { id: 1, name: 'Pen' },
            { id: 2, name: 'Pencil' }
        ]
    }

    it('maps products to ids', () => {
        expect(userProductsSelector(state).toEqual({
            ...state.user,
            productNames: ['Pen', 'Pencil']
        }))
    });
});
```