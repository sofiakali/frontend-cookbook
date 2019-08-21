# 🇨🇿 Setup spreadsheet translations

There is a tool called [ackee-localize-spreadsheet-sdk](https://www.npmjs.com/package/ackee-localize-spreadsheet-sdk) that is built up to help us manage localizations in our applicationss that use `react-intl` library.

Our motivation for building such tool was a move of responsibility for filling translations out of repositories. We likely want to let customer (or some translations specialized department) fill translations independently on our source code.

Here is few steps you need to go through to setup project for translations from spreadsheet
* [Make a spreadsheet](#make-a-spreadsheet)
* [Setup config](#setup-config)
* [Install npm package](#install-npm-package)
* [Generate translations](#generate-translations)

## Make a spreadsheet

Create a spreadsheet on Google drive. Better than make a clear spreadsheet is to duplicate one of existing, you can try [Abibuch project spreadsheet](https://docs.google.com/spreadsheets/d/1Klsl7XqBxPdeAgGA84kho49-_dwOpFXmWOeRzV3pWd4/edit#gid=0) or a [Spreadsheet template](https://docs.google.com/spreadsheets/d/1HKjvejcuHIY73WvEkipD7_dmF9dFeNLji3nS2RXcIzk/edit#gid=0).  

**Don't forget to clone a spreadsheet to you own location.** The best place to keep project's spreadsheet is the `Projekty > My Project > Web` folder on Google Drive, where `My Project` is name of your project, eg. Abibuch. 

Important step is to **make the spreadsheet published**. Go to `File > Publishing on web` in your spreadshet and press `Publish` if there is one.

## Setup config

In your project root open `.ackeeconfig.json` (make one if it's not there) and fill these fields: `sheet_id`, `dir`, `cols`.

* `sheet_id` - id of your spreadsheet, you can get it from the spreadsheet url
* `dir` - directory to generate translation files into
* `cols` - comman separated list of languages to use. Names should correspond to column headers in the spreadsheet. Also they are used as a names for generated translation files.

Typical `.ackkeconfig.json` looks like
```json
{
    "localization": {
        "sheet_id": "1Klsl7XqBxPdeAgGA84kho49-_dwOpFXmWOeRzV3pWd4",
        "dir": "src/app/localization",
        "cols": "en,de",
        "type": "key_web"
    }
}
```

## Install npm package

Install [ackee-localize-spreadsheet-sdk](https://www.npmjs.com/package/ackee-localize-spreadsheet-sdk) to your project

```bash
npm install ackee-localize-spreadsheet-sdk 
```

then make `localize` npm script (or however you want to name it) and use it in other build scripts

```json
{
  "scripts": {
    "start": "npm run localize && cross-env NODE_ENV=development webpack-dev-server --progress --inline --colors",
    "build": "npm run localize && npm run build:dev",
    ...
    "localize": "ackee-localize-spreadsheet-sdk",
  }
}
```

which ensures your scripts are generated before each app build or start.

## Generate translations

Before you generate first localization files, make few other steps:

* Add ignoring translation files - since now translation files are generated from spreadsheet thus there is no reason to version them anymore. Add pattern for ignoring them into project's `.gitignore`.

  ```
  # .gitignore

  src/app/localization/*.json
  ```

  where `src/app/localization` is a folder you [put into `.ackeeconfig.json`]((#setup-config)) as a `dir` field.

* Eventually remove old files - if they are any versioned localizations files like `cs.yml`, etc., it's time to remove them from the repository.

* Import JSONs

  Make sure that you're importing localization files as a JSON files like in the example below

  ```js 
  // src/app/localization/index.js

  import de from './de.json';
  import en from './en.json';
  ```

### It's done

Now you can run `npm run localize` or `npm start` which will first run the `localize` script.
