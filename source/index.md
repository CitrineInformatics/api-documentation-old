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

The API can be easily accessed using HTTP requests or a python client. An implementation of the python client can be downloaded from [https://github.com/CitrineInformatics/python-citrination-client](https://github.com/CitrineInformatics/python-citrination-client).

## Search API

### <a name="search_all">Search All Data Sets</a>

```python
# Retrieve the power factor of CrFeSn
from citrination_client import CitrinationClient
client = CitrinationClient('your-unique-api-key', 'https://your-site.citrination.com')
client.search(formula='CrFeSn', property='power factor', from_page=0, per_page=10)
```

```shell
# Retrieve the power factor of CrFeSn
curl https://your-site.citrination.com/api/mifs/search
  -H 'X-API-Key: your-api-key'
  -d 'formula=CrFeSn&property=power+factor&from=0&per_page=10'
```

The general search API is accessed at https://your-site.citrination.com/api/mifs/search. This will search across all data sets stored at your-site.citrination.com. The following parameters are supported (all are optional):

Parameter | Description
--------- | ------- | -----------
term | The basic search query. Terms entered here are searched for in all fields.
formula | Limit the search results by chemical formula.
property | Limit the search results by property name.
contributor | Limit the search results by the name of the person that contributed the data.
reference | Limit the search results by the original reference for the data.
min_measurement | Minimum value for property value.
max_measurement | Maximum value for property value.
from | If using pagination, set the index of starting record. Defaults to 0.
per_page | If using pagination, sets the number of records that are returned. Defaults to 10.

### Search a Single Data set

```python
# Retrieve the power factor of CrFeSn in data set 12
from citrination_client import CitrinationClient
client = CitrinationClient('your-unique-api-key', 'https://your-site.citrination.com')
client.search(formula='CrFeSn', property='power factor', from_page=0, per_page=10, data_set_id='12')
```

```shell
# Retrieve the power factor of CrFeSn in data set 12
curl https://your-site.citrination.com/api/data_sets/_ID_/mifs/search
  -H 'X-API-Key: your-api-key'
  -d 'formula=CrFeSn&property=power+factor&from=0&per_page=10'
```

The data-set-specific search API is accessed at https://your-site.citrination.com/api/data_sets/_ID_/mifs/search. This will search against the single data set with id equal to _ID_ at your-site.citrination.com. This API supports the same set of parameters as [searching all data sets](#search_all).

### Search Results



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
