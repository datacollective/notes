---
title: "data: Command Line Tool"
excerpt: Learn how to use our Command Line Tool.
---


## Overview

**`data`** is an important part of the [DataHub](/docs/about) project. It is a command line tool that helps you to publish (and get) data.

For example you have a set of data as a result of your work, be it a few data files and a description. And you want to share it with your colleagues. With **`data`** you just need to:

```bash
$ cd data-folder
$ data init  # generate datapackage.json for my data files
> "Answer a few questions here, e.g. dataset name, files to include, etc"
$ data push  # upload the dataset onto the DataHub
> "As a result you'll got a link to share:
http://datahub.io/user-name/data-package-name
```

That's it! Your data is online. You can make your data public or private, add some pretty graphics, and many more. Please read https://datahub.io/docs for details.

## Usage examples:

Here we show examples of usage for common `data` commands. To see the full command documentation click on the command name or proceed to the [help pages](https://github.com/datahq/data-cli/tree/master/docs).

### data login

You should login at the first use of `data` tool:

```bash
$ data login
? Login with... Github
> Opening browser and waiting for you to authenticate online
> You are logged in!
```

### data push

*Find out more about `data push` - https://github.com/datahq/data-cli/blob/master/docs/push.md*

Upload a dataset or a separate file to the DataHub:

```bash
$ data push mydata.csv
? Please, confirm name for this dataset:
0-selfish-cougar-7 mydataset
? Please, confirm title for this dataset:
Mydataset Mydataset
  Uploading [******************************] 100% (0.0s left)
  your data is published!
🔗  https://datahub.io/myname/mydataset/v/1 (copied to clipboard)
```

**Note:** by default, findability flag for your dataset is set to `--public`. Use `--unlisted` flag if you want it to not appear in the search results.

### data get

*Find out more about `data get` - https://github.com/datahq/data-cli/blob/master/docs/get.md*

Get a dataset from the DataHub or GitHub:

```bash
$ data get http://datahub.io/core/gold-prices
Time elapsed: 1.72 s
Dataset/file is saved in "core/gold-prices"
```

### data info

*Find out more about `data info` - https://github.com/datahq/data-cli/blob/master/docs/info.md*

Shows info about the dataset (local or remote):

```bash
$ data info http://datahub.io/core/gold-prices
# Gold Prices (Monthly in USD)

Monthly gold prices since 1950 in USD (London market). Data is sourced from the Bundesbank.

## Data
    * [Bundesbank statistic ... [see more below]

## RESOURCES
┌───────────────────┬────────┬───────┬───────┐
│ Name              │ Format │ Size  │ Title │
├───────────────────┼────────┼───────┼───────┤
│ data_csv          │ csv    │ 16172 │       │
├───────────────────┼────────┼───────┼───────┤
│ data_json         │ json   │ 32956 │       │
├───────────────────┼────────┼───────┼───────┤
│ gold-prices_zip   │ zip    │ 17755 │       │
├───────────────────┼────────┼───────┼───────┤
│ data              │ csv    │ 16170 │       │
└───────────────────┴────────┴───────┴───────┘

## README
Monthly gold prices since 1950 in USD (London market). Data is sourced from the Bundesbank.
...

### Licence
...
```

### data cat

*Find out more about `data cat` - https://github.com/datahq/data-cli/blob/master/docs/cat.md*

Works similar as Unix `cat` command but works with remote resources and displays tabular data in a nice ascii table:

```bash
$ data cat http://datahub.io/core/gold-prices/r/0.csv
┌─────────────────────────────┬─────────────────────────────┐
│ date                        │ price                       │
├─────────────────────────────┼─────────────────────────────┤
│ 1950-02-01                  │ 34.730                      │
├─────────────────────────────┼─────────────────────────────┤
│ 1950-03-01                  │ 34.730                      │

...........
```

You can also convert tabular data into different formats (the source could be remote as well):

```bash
$ data cat prices.csv prices.md
> All done! Your data is saved in "prices.md"
user@pc:~/Downloads$ cat prices.md
| date       | price    |
| ---------- | -------- |
| 1950-02-01 | 34.730   |
| 1950-03-01 | 34.730   |
```

### data init

*Find out more about `data init` - https://github.com/datahq/data-cli/blob/master/docs/init.md*

Data-cli has an `init` command that will automatically generate Data Package metadata including scanning the current directory for data files and inferring [table schema] for tabular files:

```bash
$ data init
This process initializes a new datapackage.json file.
Once there is a datapackage.json file, you can still run `data init`
to update/extend it.
Press ^C at any time to quit.

? Enter Data Package name prices
? Enter Data Package title prices
? Do you want to add following file as a resource "prices.csv" - y/n? y
prices.csv is just added to resources
? Do you want to add following file as a resource "prices.xls" - y/n? y
prices.xls is just added to resources

? Going to write to /home/user/Downloads/datapackage.json:
{
  "name": "prices",
  "title": "prices",
  "resources": [
    {
      "path": "prices.csv",
      "name": "prices",
      "format": "csv",
....
    },
      "schema": {
        "fields": [
          {
            "name": "date",
            "type": "date",
            "format": "default"
          },
          {
........
    {
      "path": "prices.xls",
      "pathType": "local",
      "name": "prices",
      "format": "xls",
      "mediatype": "application/vnd.ms-excel",
      "encoding": "windows-1250"
    }
  ]
}


Is that OK - y/n? y
datapackage.json file is saved in /home/user/Downloads/datapackage.json
```

### data validate

*Find out more about `data validate` - https://github.com/datahq/data-cli/blob/master/docs/validate.md*

```bash
$ data validate path/to/correct/datapackage
> Your Data Package is valid!
```

```bash
$ data validate path/to/invalid-data
> Error! Validation has failed for "missing-column"
> Error! The column header names do not match the field names in the schema on line 2
```

### data help

Also you can run "help" command in your terminal to see command docs:

```bash
$ data help
'General description'
$ data help push
> 'push command description'

# data help get
# data help init
# etc ...
```

## Installation

```
npm install data-cli --global
```
After installation you can run `data-cli` by the name `data`:
```
data --version
> 0.8.9
```

If you're not using NPM you can install `data-cli` binaries following [this instructions](https://datahub.io/docs/getting-started/installing-data#installing-binaries).
