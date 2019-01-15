# 🧪 Cypress

[Cypress](https://www.cypress.io/) is javascript framework for **end to end testing** of web applications. We have chosen it over other frameworks for its simplicity of use and setup. We even [wrote a blog post](https://www.ackee.cz/blog/cypress-testovani-webovych-aplikaci/) with example about Cypress. In this article we share a few examples and tips that might help you.

## Table of contents
* [Examples and tips](#examples-and-tips)
    * [Custom commands](#custom-commands)
    * [Environment variables](#environment-variables)
    * [Fixtures](#fixtures)
    * [Add screenshots and videos folders to gitignore](#add-screenshots-and-videos-folders-to-gitignore)

## Examples and tips
### Custom commands

[Documentation](https://docs.cypress.io/api/cypress-api/custom-commands.html#Syntax)

Custom commands are extremly usefull when you repeat same sequences of commands in more more tests. Why not to create a own command to avoid this repetition? They can be defined in `cypress/support/commands.js` file.

Below is example with custom `authVisit` command we use in almost every test to authenticate before navigating to a specific route/page.

```javascript
// Definition command.js
Cypress.Commands.add('authVisit', url => {
    const { username, password } = Cypress.env('auth'); // get credentials from enviroment variables

    cy.visit(url);
    cy.get('input[name="email"]').type(username);
    cy.get('input[name="password"]').type(password);
    cy.get('button[type="submit"]').click();
});
```

```javascript
// Usage in products.spec.js test file
describe('Test group', () => {
    it('should authenticate, visit page and test stuff', () => {
        cy.authVisit('/products');
        // ...
    });
})
```

### Environment variables

[Documentation](https://docs.cypress.io/guides/guides/environment-variables.html#Setting)

Environment variables are useful for storing dynamic values that can be different for multiple development environments. In the example below we use it for storing REST API url which can naturally vary for different environments (development/stage).

```json
// Definition in cypress.env.json
{
    apiUrl: 'https://api-dev.ack.ee'
}
```

```javascript
// Usage in test file
Cypress.env('apiUrl') // https://api-dev.ack.ee
```

### Fixtures

[Documentation](https://docs.cypress.io/api/commands/fixture.html#Syntax)

Fixtures are great for storing static test data (e.g. REST API responses/requests). They are defined in JSON files so you can easilly organize them.

Example with API response:

```json
// Definiton in products.json
[
    { "id": 1, "name": "Pen" },
    { "id": 2, "name": "Pencil" }
]
```

```javascript
// products.spec.js test file

// load fixture
cy.fixture('products.json').as('productsData');
// define route with test data as response
cy.route('GET', 'http://api.ack.ee', '@productsData');
```

### Add *screenshots* and *videos* folders to gitignore

When you run tests in command line using `cypress run` command, it creates these two folders with images and videos from done tests. Adding it to `.gitignore` is recommended to save storage in your git repository.

```javascript
// .gitignore file
cypress/screenshots
cypress/videos
```
