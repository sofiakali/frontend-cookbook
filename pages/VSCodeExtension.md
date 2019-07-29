# VS Code Ackee frontend extension

A set of usefull snippets and commands we use at Ackee for web apps development.

*  [Marketplace link](https://marketplace.visualstudio.com/items?itemName=ackee.ackee-frontend)
*  [GitHub project](https://github.com/AckeeCZ/vscode-frontend)

## Contributing

[Extension documentation](https://code.visualstudio.com/api/get-started/your-first-extension)

### Adding a snippet

It's very easy - just add the snippet into a corresponding language file in `snippets` folder. (e.g. `snippets/javascript.json`).

### Adding a command

The extension is basically a typescript node project using the VS Code API. See [the documentation](https://code.visualstudio.com/api/get-started/extension-anatomy) for an extension anatomy. There is `extension.ts` file where you register commands and use app's Model. There is `model.ts` containing all extension's logic.

To create a new command:
1. Add the functionality to `Model` class
2. Register the new command in `etension.ts`
3. Add the command to `package.json` sections `contributes` and `activationEvents`

## Publishing

### Travis

The project has continues delivery set up on Travis. To release a new version, run `npm version VERSION` in updated `master` branch.

### Publishing manually

[Official VS Code documentation guide](https://code.visualstudio.com/api/working-with-extensions/publishing-extension) for publishing an extension

**Requirements**:

*  Azure DevOps organization account for Ackee
*  Personal Microsoft account
*  Personal access token (PAT) for Marketplace (see [the official documentation](https://code.visualstudio.com/api/working-with-extensions/publishing-extension) for guide how to retrieve it)


After all requirements are met:
1. Install `vsce` CLI by `npm i -g vsce`
2. Login to ackee organization (you will be prompted to enter the PAT) with `vsce login ackee`.
3. Pack the extension with `vsce package`
4. Publish an extension with `vsce publish minor`. The last parameter of the command is a version (see the doc for more info).

Alternatively, the extension can be packed and published throuh Marketplace publisher [mangement page](https://marketplace.visualstudio.com/manage).