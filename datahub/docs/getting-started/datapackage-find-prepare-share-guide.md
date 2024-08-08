---
title: Data Package Find-Prepare-Share Guide
date: 2018-04-26
authors: ["Dmytro Gierman", "Branko Djordjevic"]
excerpt: The guide for finding, packaging and sharing data.
---

**In this guide we walk through creating a data package from scratch and publishing to the DataHub. As a bonus, we'll also show you how to collect data and clean up before packing it for publication.**


# What is Data Package
[csv]: /docs/data-packages/csv
Let's say that you obtained data about pharmaceutical drug spending by countries and that you already have it saved in a [comma separated values][csv] (CSV) file.

[CSV][csv] (or Excel) is a good format for presenting tabular data, for example the user of your data can clearly see the amount each country spent on pharmaceuticals in a given year. However, there are things that the CSV file doesn't show. For example, we don't know the:

- general description of the data
- information about the sources 
- license information
- data file structure information etc.

All this information is called **metadata** and should be stored alongside with the data to make the information frame around the raw data you have, so users could understand the context.

[dp]: /docs/data-packages
And this is where [**Data Package**][dp] comes in. 

[**Data Package**][dp] - *is a data plus a meta-information about it.*

Data Package is a simple container format used to describe and package a collection of data. The format provides a simple contract for data interoperability that supports frictionless delivery, installation and management of data.

Data Package stores the meta-information in the special **Descriptor File**. It has a [JSON](https://en.wikipedia.org/wiki/JSON) format and a special name: `datapackage.json`. Here you can read a short [descriptor specification](https://frictionlessdata.io/specs/data-package/#specification).

*Descriptor File* could have a complex structure, but don't worry - you don't need to type it manually or write programs for creating it. Soon we'll show how to make it automatically.

Before creating a data package you need to get the data and clean it. Please, continue to read more on how to get data for your Data Package.

# Getting data

After finding the source of data (some organization, web-site, internet archive, etc), there are few common ways to get the data:

### 0. You already have the data files

You could have a lot of useful data already as a result of your work.

### 1. Manually download data files

If you found a target files in the web, and the data is not updating regularly - you can just download files and put them into, say, "sources" or "archive" folder for future analysis.

### 2. Automatic download

If the data source is updated regularly, it would be natural to write down the process of getting data, using some script (instead of downloading the resources manually each time).

Here is a simple example in Python:
```python
# python program
import urllib

source = 'http://ourairports.com/data/airports.csv'
archive = 'data.csv'

urllib.urlretrieve(source, archive)
```
You can also use a shell script:
```shell
#!/usr/bin/sh
SOURCE='http://ourairports.com/data/airports.csv'
wget -O data.csv $SOURCE
```
As a result you'll get the `data.csv` file in the current program directory.


### 3. Get data from the API

Sometimes the data source organization has a web API endpoints. In that case you should read the API documentation on how to use it.

For example U.S. Energy Information Administration published their instructions here: https://www.eia.gov/opendata/commands.php 
Here is an example of using their API:
```python
# python script
import requests
import json

API_URL = 'http://api.eia.gov/series/'
series_id = 'NG.RNGWHHD.D'

response = requests.get(API_URL, params={
    'series_id': series_id,
    'out': 'json'
})

data = response.json()
```

Another good example of getting data via API is described here: http://okfnlabs.org/handbook/data/tutorial/#from-an-api

Web APIs often return data in JSON format, so then you need to analyze the JSON file structure and write a script to derive the information you need.

If the API returns a CSV file - you can save this file or load it directly into your program.


# Clean and Transform Data (and why)

Now you have data, stored on the disk, probably it is a CSV or a XLS file.

Let's make this data clean and nice, so it becomes machine-friendly and your customers could use these programs to analyze and process information automatically.

## Clean and Prepare

Use this check-list:
- data files have no comments (move them into README)
- data files have no empty rows
- tables are unpivoted ([what is pivot and unpivot](https://www.excel-university.com/unpivot-excel-data/))
- We prefer using `.` rather than `,` to separate decimal values.
- If data is not available, you either mark that cell as 0 or NaN.
- The Data Package and resources (data files) names should use `-` instead of spaces.
  e.g. `gold-prices/data/prices.csv`

## Use Python

By now you can understand why we use programming skills here, since you can basically automate most of these processes.

We are not going to teach you how to use Python, but here are some tips, that would help:
- refresh your memory with a list of [common String operations](https://docs.python.org/2/library/string.html)
- use `csv` module for working with csv
- use `xlrd` or any similar module for handling excel files
- use `pandas` module and its `melt()` method to unpivot tables
- don't try to create a perfect program but rather a simple script,
  our focus here is the data itself, not the data processing combined
- use git & [GitHub](http://github.com/) to store your code

The project structure could be like this:
```bash
folder
 ├── archive
 │    └── source.csv    # original files
 ├── data
 │    └── data.csv      # cleaned files
 ├── README.md          # Full description of the package for Humans
 └── scripts
      └── process.py    # script that gets and cleans the data
```

If you prefer to use UNIX shell scripts instead of Python, this guide will help you:
https://github.com/rufuspollock/command-line-data-wrangling

We also have to describe the data file format, which gives the ability to prove that data is valid. The next section describes how to do it:

# Create and share a data package

Now you have downloaded and prepared the data.

To create a **Data Package** you need to describe the data structure and to save this information into `datapackage.json` descriptor file.

The easiest way is to use the [command line tool](https://datahub.io/Download) from the [DataHub project](https://datahub.io).

Install the program, go to the data project folder and run `data init ` there:
```
$ data init
> This process initializes a new datapackage.json file.
> Once there is a datapackage.json file, you can still run `data init`
  to update/extend it.
> Press ^C at any time to quit.

> Detected special file: README.md
> archive/source.csv is just added to resources
> data/data.csv is just added to resources
> scripts/process.py is just added to resources
> Default "ODC-PDDL" license is added. If you would like to add a different
  license, run `data init -i` or edit `datapackage.json` manually.
> 
💾 Descriptor is saved in "datapackage.json"
```

Now you can examine the descriptor file and update or fix it if you think something is wrong (title, description, data field formats, redundant resources, etc):
```json
{
  "name": "airport-codes",
  "title": "Airport codes",
  "resources": [
    {
      "path": "archive/source.csv",
      "pathType": "local",
      "name": "source",
      "format": "csv",
      "mediatype": "text/csv",
      "encoding": "ISO-8859-1",
      "dialect": {
        "delimiter": ",",
        "quoteChar": "\""
      },
      "schema": {
        "fields": [
          {
            "name": "id",
            "type": "integer",
            "format": "default"
          },
....
```
After making changes, to ensure that descriptor and data are correct, run `data validate`. You will see if any errors appear:
```bash
$ data validate path_to_invalid_data_package
> Error! Validation has failed for "missing-column"
> Error! The column header names do not match the field names in the schema on line 2
```
After fixing them you will see:
```bash
$ data validate path_to_data_package
> Your Data Package is valid!
```

Now everything is OK, its time to share your data with the world. Use `data push` for that:
```
$ data push --public
Completed: scripts/process.py
Completed: datapackage.json
Completed: archive/source.csv
  Uploading [******************************] 100% (0.0s left)   archive/source.csv🙌  your data is published!
🔗  https://datahub.io/AcckiyGerman/airport-codes/v/1 (copied to clipboard)
```
The Data Package is stored online and your colleagues could access it using the browser: [http://datahub.io/your-name/data-package-name/](#)

That's it. Our goal is reached! We have
- gathered information
- cleaned the data
- created a Data Package
- pushed it online

Thank you for reading.

## Additional materials

Read more about [DataHub command line tool](https://datahub.io/docs/features/data-cli).

Creating Data Packages examples and a [lot of information about the Data Hub](https://datahub.io/docs).

Ask your question in the chat channel: https://gitter.im/datahubio/chat
