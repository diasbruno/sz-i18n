sz-i18n
======

[![Join the chat at https://gitter.im/StefanYohansson/sz-i18n](https://badges.gitter.im/StefanYohansson/sz-i18n.svg)](https://gitter.im/StefanYohansson/sz-i18n?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

[![Build Status](https://travis-ci.org/StefanYohansson/sz-i18n.svg?branch=master)](https://travis-ci.org/StefanYohansson/sz-i18n) [![npm version](https://badge.fury.io/js/sz-i18n.svg)](https://badge.fury.io/js/sz-i18n)

[![NPM](https://nodei.co/npm/sz-i18n.png?downloads=true&stars=true)](https://nodei.co/npm/sz-i18n/)

Internationalization library. ️ Easy and Fast. ⚡️

Highly inspired by: https://github.com/roddeh/i18njs

## Install
```sh
npm install sz-i18n
```

## Test
```sh
npm test
```

## How to use

### Simple example

```javascript
  var i = new i18n
  var ja = i.create('ja', {values: {'Cancel': 'キャンセル'}})
  i.translate('Cancel')
  // result: キャンセル
```

### Two dictionaries

```javascript
  var i = new i18n
  var ja = i.create('ja', {values: {'Cancel': 'キャンセル'}})
  var pt = i.create('pt', {values: {'Cancel': 'Cancelar'}})

  i.using('pt').translate('Cancel')
  // result: Cancelar
  // or
  pt.translate('Cancel')
  // result: Cancelar

  i.using('ja').translate('Cancel')
  // result: キャンセル
  // or
  ja.translate('Cancel')
  // result: キャンセル
```

### Change dictionary values

```javascript
  var i = new i18n
  var ja = i.create('ja', {values: {'Cancel': 'キャンセル'}})
  // result: キャンセル

  i.using('ja').translate('Cancel')

  i.getDictionary('ja').add({values: {
  "Hello": "こんにちは"
  }})

  i.using('ja').translate('Hello')
  // result: こんにちは
```

### Pluralisation

```javascript
  var i = new i18n
  var ja = i.create('ja', {
            values: {
              "Hello": "こんにちは",
              "Yes": "はい",
              "No": "いいえ",
              "Ok": "Ok",
              "Cancel": "キャンセル",
              "%n comments":[
              [0, null, "%n コメント"]
              ],
              "_monkeys":"猿も木から落ちる"
            }
          })

 i.translate("%n comments", 0)
 // result: 0 コメント
 i.translate("%n comments", 1)
 // result: 1 コメント
 i.translate("%n comments", 2)
 // result: 2 コメント
```

### Interpolation

```javascript
  var i = new i18n
  var pt = i.create('pt', {values: {'Welcome %{name}': 'Bem Vindo %{name}'}})

  i.translate('Welcome %{name}', { name:"John" })
  // result: Bem Vindo John
```

### Contexts

```javascript
  var i = new i18n
  var en = _i.create('en', {
      values: {},
      contexts:[
        {
          "matches": {
            "gender":"male"
          },
          "values": {
            "%{name} uploaded %n photos to their %{album} album":[
              [0, 0, "%{name} uploaded %n photos to his %{album} album"],
              [1, 1, "%{name} uploaded %n photo to his %{album} album"],
              [2, null, "%{name} uploaded %n photos to his %{album} album"]
            ]
          }
        },
        {
          "matches": {
            "gender":"female"
          },
          "values": {
            "%{name} uploaded %n photos to their %{album} album":[
              [0, 0, "%{name} uploaded %n photos to her %{album} album"],
              [1, 1, "%{name} uploaded %n photo to her %{album} album"],
              [2, null, "%{name} uploaded %n photos to her %{album} album"]
            ]
          }
        }
      ]
    })

  i.using('en').translate("%{name} uploaded %n photos to their %{album} album", 1,
    { name:"John", album:"Buck's Night" },
    { gender:"male" }
  )
  // result: John uploaded 1 photo to his Buck's Night album
```

## How it works

### Base json

You need to write a json (base source) with values and contexts, this structure is wrote in your default system language.
You can generate this file by running:
```shell
sz-i18n generate
```
this command will output something like: (so you can save anywhere in your project)
```json
// src-lang/en.json
  {
    "values": {
      "Cancel": "Cancel",
      "%n comments": [
        [0, 0, "%n comments"],
        [1, 1, "%n comment"],
        [2, null, "%n comments"]
      ]
    },
    "contexts": [
      {
        "matches": {
          "gender": "male"
        },
        "values": {
          "%{name} uploaded %n photos to their %{album} album": [
            [0, 0, "%{name} uploaded %n photos to his %{album} album"],
            [1, 1, "%{name} uploaded %n photo to his %{album} album"],
            [2, null, "%{name} uploaded %n photos to his %{album} album"]
          ]
        }
      }
    ]
  }
```

### init

This command will prompt some information about sz-i18n configuration and write a file in your current path.

### extract

This command will extract i18n strings from your source code folder.

```shell
sz-i18n extract
```

if you don't configure i18n.json file (use `init` command), you can use like this:

```shell
sz-i18n extract [<source_code>] [<base>] [-t <langs>]
```

### export

```shell
sz-i18n export
```

if you don't configure i18n.json file (use `init` command), you can use like this:

```shell
sz-i18n export sz-cli/test-files/src-lang/en.json -o sz-cli/test-files/lang/ -t pt,ja
```

This will export base json into multiple simple json (each supported language), so you can use a translator tool and import back.

```json
// lang/pt/pt.json
{
  "%n comment": "%n comentário",
  "%n comments": "%n comentários",
  "%{name} uploaded %n photo to her %{album} album": "",
  "%{name} uploaded %n photo to his %{album} album": "",
  "%{name} uploaded %n photos to her %{album} album": "",
  "%{name} uploaded %n photos to his %{album} album": "",
  "%{name} uploaded %n photos to their %{album} album": "",
  "Cancel": "Cancelar",
  "Due -%n days ago": "",
  "Due Today": "",
  "Due Tomorrow": "",
  "Due Yesterday": "",
  "Due in %n days": ""
}
```

### import

```shell
sz-i18n import sz-cli/test-files/lang/ -b sz-cli/test-files/src-lang/en.json -o sz-cli/test-files/src-lang/ -t pt,ja
```

It'll parse simple json key/values and replace in source base file creating new base lang files (each supported language).

```json
// src-lang/pt.json
{
  "values": {
    "Cancel": "Cancelar",
    "%n comments": [
      [0, 0, "%n comentários"],
      [1, 1, "%n comentário"],
      [2, null, "%n comentários"]
    ]
  },
  "contexts": [
    {
      "matches": {
        "gender": "male"
      },
      "values": {
        "%{name} uploaded %n photos to their %{album} album": [
          [0, 0, "%{name} uploaded %n photos to his %{album} album"],
          [1, 1, "%{name} uploaded %n photo to his %{album} album"],
          [2, null, "%{name} uploaded %n photos to his %{album} album"]
        ]
      }
    }
  ]
}
```
