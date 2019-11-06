# Testing Essentials

For testing generally, you will always need at least **testing framework** & usually also **mocking library**.

We have a luck that our testing framework also includes mocking funcionality (& much more, later about it) so that's all we need for now. There is already a recipe about [**how to start with Jest**](/pages/libraries/Jest.md) - our testing framework. Take all the text here as and addition to it.

## Matchers

TBD

## Mocking

>Technique that allows you to focus on testing one specific part of code, faking other parts assuming they work as intended.

In case you din't do it yet, read more about some Jest basics to be ready for the examples:

* [how to return a value from mock](https://jestjs.io/docs/en/mock-functions#mock-return-values)
* [advanced mock implementation](https://jestjs.io/docs/en/mock-functions#mock-implementations)
* [how to test asynchronous](https://jestjs.io/docs/en/tutorial-async) code using
    * [callbacks](https://jestjs.io/docs/en/asynchronous#callbacks)
    * [promises](https://jestjs.io/docs/en/asynchronous#promises)
    * [async/await](https://jestjs.io/docs/en/asynchronous#async-await)


### Why we need mocking?

* **SPEED**‼ Less real code to execute means faster tests run.
* **Focus** When dependencies are mocked and only tested code is run, we can quickly identify why test fails.

### Basic mocks

We will ilustrate it on mocking 3rd party library, but you can use it same way also for other sources, modules, etc. in app.

So there is a selector that uses `routingSelector` from `@ackee/chris` package and since there is no browser and no real location, we need to mock it to return some.


```js
// mySelectors.js
import { createSelector } from 'reselect'
import { routingSelector } from '@ackee/chris'

export const selectMenuItems = createSelector(
    routingSelector,
    location => {
        const currentPathname = location.pathname

        return ['home', 'about', 'team', 'purchase'].map(id => {
            const route = `/${id}`
            
            return {
                id,
                route,
                active: route === currentPathname,
            }
        })
    }
)
```

Notice:
* how we mimic location object and provide only properties we need
* how we mock a module and then import it to be able to "program" intended behaviour
* `selectMenuItems` can receive fake state object `{}` since routingSelector is mocked and doesn't use it

```jsx
// __tests__/mySelectors.js
import { selectMenuItems } from '../mySelectors.js'
import { routingSelector } from '@ackee/chris'

jest.mock('@ackee/chris')

describe('selectMenuItems', () => {
    it('returns all menu items', () => {
        const items = selectMenuItems({})
        expect(items).toHaveLength(4)
    })
    
    it('marks current route active', () => {
        routingSelector.mock.returnValue({ pathname: '/team' })

        const items = selectMenuItems({})
        
        expect(items[2]).toHaveProperty('active', true)
    })
    
    it('does not mark other than current route as active', () => {
        routingSelector.mock.returnValue({ pathname: '/team' })
        
        const items = selectMenuItems({})
        
        expect(items[0]).toHaveProperty('active', false)
        expect(items[1]).toHaveProperty('active', false)
        expect(items[3]).toHaveProperty('active', false)
    })
})
```

in the example above whole `@ackee/chris` module is mocked. Sometimes you need only a certain part of a module to be mocked and the rest remain untouched. That's called **partial mock** and it's easy to accomplish it.

In the previous example we would just change the mock command and in following example, `routingSelector` will be mocked function whereas `combinedDependenciesHandlers` will be a real function.

```js
import { routingSelector, combineDependenciesHandlers } from '@ackee/chris'

jest.mock('@ackee/chris', () => {
    return {
        ...jest.requireActual('@ackee/chris'),
        routingSelector: jest.fn(),
    };
});
```

You can also check [module examples in jest repository](https://github.com/facebook/jest/tree/master/examples/module-mock) as well as [manual mock examples](https://github.com/facebook/jest/tree/master/examples/manual-mocks).
