---
title: Citrination API Reference

language_tabs:
  - shell
  - python

toc_footers:
  - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

This document includes necessary information for working with the citrination API using either a shell (via curl) or python script. For every example, you can switch between languages using the tabs to the right.

The API currently includes the ability to search against data already on citrination. Additional features will be added in the future and included in this document.

# Using the API

## Accessing Your Site

The API can be accessed at https://citrination.com or at any sub-domain, e.g. https://your-site.citrination.com (where "your-site" is the specific site that you want to access).

## API Token

Each citrination user is assigned a unique access key. You can access your API key by logging into your-site.citrination.com and clicking on "Account" at the top of the page.

Note that your API Token is valid only on the site that you accessed it from. In other words, a token for accessing the API at your-site.citrination.com is not valid for another-site.citrination.com.

## Connecting to the API

```python
# Your API key is used in the constructor for CitrinationClient objects
from citrination_client import CitrinationClient
client = CitrinationClient('your-unique-api-key', 'https://your-site.citrination.com')
```

```shell
# Authentication tokens must be applied to the headers of any requests made to the Citrination API
curl https://your-site.citrination.com
  -H 'X-API-Key: your-unique-api-key'
```

The API can be easily accessed using HTTP requests or a python client. An implementation of the python client can be downloaded from https://github.com/CitrineInformatics/python-citrination-client

## Search API

### Search All Data Sets

```python
# Retrieve the power factor of CrFeSn
from citrination_client import CitrinationClient
client = CitrinationClient('your-unique-api-key', 'https://your-site.citrination.com')
client.search(formula='CrFeSn', property='Power factor' from_page=0, per_page=10)
```

```shell
curl --data "term=RbOs2O6&from=0&per_page=10"
 "https://your-site.citrination.com/api/mifs/search"
  -H "X-API-Key: your-api-key"
  -H "Content-Type: application/json"
```







> The above command returns JSON structured like this:

```json
{
    "sample": {
        "data_set_id": 213,
        "material": {
            "chemicalFormula": "RbOs2O6"
        },
        "measurement": [
            {
                "property": {
                    "units": "K",
                    "scalar": [
                        {
                            "value": "6.3"
                        }
                    ],
                    "name": "Superconducting critical temperature (Tc)"
                },
                "reference": [
                    {
                        "url": "http://arxiv.org/abs/1109.5422v1"
                    },
                    {
                        "doi": "10.1143/jpsj.80.104708"
                    }
                ]
            }
        ]
    }
}
```

This endpoint searches data based on text input to the term field. We index chemical formulas in a variety of ways, and the term field in this method is very flexible. For example, you could search "band gap of gallium nitride", or "ternary oxides" and get back a variety of interesting results, ranked according to our proprietary scoring algorithm.

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
term | true | The basic search query
formula | false | Limit the search results by the chemical formula entered here
property | false | Name of the property to search for
contributor | false | Limit the search results by the name of the person that contributed the data
reference | false | Limit the search results by the original reference for the data
min_measurement | false | Minimum decimal value for property value
max_measurement | false | Maximum decimal value for property value
from | false | If using pagination, set the starting record. Defaults to 0
per_page | false | If using pagination, sets how many records to return. Defaults to 10


<aside class="success">
Don't forget your API key!
</aside>


## Search a specific data set  
```python
from citrination_client import CitrinationClient
client = CitrinationClient('your-unique-api-key', 'https://your-site.citrination.com')
client.search(term='RbOs2O6', from_page=0, per_page=10, data_set_id=213)
```

```shell
curl --data "term=RbOs2O6&from=0&per_page=10"
 "https://your-site.citrination.com/api/data_sets/213/mifs/search"
  -H "X-API-Key: your-api-key"
  -H "Content-Type: application/json"
```

> The above command returns JSON structured like this:

```json
{
    "sample": {
        "data_set_id": 213,
        "material": {
            "chemicalFormula": "RbOs2O6"
        },
        "measurement": [
            {
                "property": {
                    "units": "K",
                    "scalar": [
                        {
                            "value": "6.3"
                        }
                    ],
                    "name": "Superconducting critical temperature (Tc)"
                },
                "reference": [
                    {
                        "url": "http://arxiv.org/abs/1109.5422v1"
                    },
                    {
                        "doi": "10.1143/jpsj.80.104708"
                    }
                ]
            }
        ]
    }
}
```
This API is identical to the main search API, but limited to a particular data_set. You will notice that the search results include a "mif_id" field. This ID is the value to supply to the data_sets endpoint when searching.

This endpoint searches data based on text input to the term field. We index chemical formulas in a variety of ways, and the term field in this method is very flexible. For example, you could search "band gap of gallium nitride", or "ternary oxides" and get back a variety of interesting results, ranked according to our proprietary scoring algorithm.

<aside class="warning">
You can use a ? in the 'term' parameter to retrieve all structured data for a given sample.
</aside>

### HTTP Request

`POST https://your-site.citrination.com/api/data_sets/<id>/mifs/search`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the sample to search

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
term | true | The basic search query
formula | false | Limit the search results by the chemical formula entered here
contributor | false | Limit the search results by the name of the person that contributed the data
reference | false | Limit the search results by the original reference for the data
min_measurement | false | Minimum decimal value for property value
max_measurement | false | Maximum decimal value for property value
from | false | If using pagination, set the starting record. Defaults to 0
per_page | false | If using pagination, sets how many records to return. Defaults to 10


<aside class="success">
Don't forget your API key!
</aside>

<!--
## Upload Data
```python
from citrination_client import CitrinationClient
client = CitrinationClient('your-unique-api-key', 'https://your-site.citrination.com')
client.upload(name='My Published Paper', description='Band Gaps of My Favorite Compounds', filename='mypaper.pdf')
```

You can upload data using the python client, but not directly through HTTP at this time.
Uploading data will start it off in our data processing pipeline, and in a few minutes time
it will be available on the web via https://your-site.citrination.com/data_uploads
-->
