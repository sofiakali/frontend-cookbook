# VS Code Ackee frontend extension

A set of usefull snippets and commands we use at Ackee for web apps development.

*  [Marketplace link](https://marketplace.visualstudio.com/items?itemName=ackee.ackee-frontend)
*  [GitHub project](https://github.com/AckeeCZ/vscode-frontend)

## Contributing

[Extension documentation](https://code.visualstudio.com/api/get-started/your-first-extension)

### Adding a snippet

It's very easy - just add the snippet into a corresponding language file in `snippets` folder. (e.g. `snippets/javascript.json`).

### Adding a command

*To be done*

## Publishing

[Official VS Code documentation guide](https://code.visualstudio.com/api/working-with-extensions/publishing-extension) for publishing an extension

**Requirements**:

*  Azure DevOps organization account for Ackee
*  Personal Microsoft account
*  Personal access token (PAT) for Marketplace


After all requirements are met:
1. Install `vsce` CLI by `npm i -g vsce`
2. Login to ackee organization (you will be prompted to enter the PAT) with `vsce login ackee`.
3. Pack the extension with `vsce package`
4. Publish an extension with `vsce publish minor`. The last parameter of the command is a version (see the doc for more info).

Alterntively, the extension can be packed and published throuh Marketplace publisher [mangement page](https://marketplace.visualstudio.com/manage).