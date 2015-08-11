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

The API can be accessed at https://citrination.com/api or at any sub-domain, e.g. https://your-site.citrination.com/api (where "your-site" is the specific site that you want to access).

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

The API can be accessed using HTTP requests or a python client. An implementation of the python client can be downloaded from [https://github.com/CitrineInformatics/python-citrination-client](https://github.com/CitrineInformatics/python-citrination-client). Python examples in this document use this client.

## Search API

### <a name="search_all">Search All Data Sets</a>

```python
# Retrieve the power factor of CrFeSn
from citrination_client import CitrinationClient
client = CitrinationClient('your-unique-api-key', 'https://your-site.citrination.com')
r = client.search(formula='CrFeSn', property='power factor')
# Use r.json() to convert the content of the response from JSON
```

```shell
# Retrieve the power factor of CrFeSn
curl https://your-site.citrination.com/api/mifs/search
  -H 'X-API-Key: your-api-key'
  -d 'formula=CrFeSn&property=power+factor'
```

The general search API is accessed at https://your-site.citrination.com/api/mifs/search. This will search across all data sets stored at your-site.citrination.com. The following parameters are supported:

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
per_page | If using pagination, sets the number of records that are returned. Defaults to 10. The maximum value is 100 (see [pagination](#pagination)).

\* When searching over *all* data sets, at least one parameter other than _from_ or _per_page_ must be set.

### <a name=search_single>Search a Single Data set</a>

```python
# Retrieve the power factor of CrFeSn in data set 12
from citrination_client import CitrinationClient
client = CitrinationClient('your-unique-api-key', 'https://your-site.citrination.com')
r = client.search(formula='CrFeSn', property='power factor', data_set_id='12')
# Use r.json() to convert the content of the response from JSON
```

```shell
# Retrieve the power factor of CrFeSn in data set 12
curl https://your-site.citrination.com/api/data_sets/12/mifs/search
  -H 'X-API-Key: your-api-key'
  -d 'formula=CrFeSn&property=power+factor'
```

The data-set-specific search API is accessed at https://your-site.citrination.com/api/data_sets/_ID_/mifs/search. This will search against the single data set with id equal to _ID_ at your-site.citrination.com. This API supports the same set of parameters as [searching all data sets](#search_all).

### Search Results

> Example response from the search API

```json
{
    "hits": 1,
    "results": [
        {
            "sample": {
                "data_set_id": 1,
                "material": {
                    "chemicalFormula": "CrFeSn"
                },
                "measurement": [
                    {
                        "dataType": "Experimental",
                        "property": {
                            "units": "mW/K$^2 \\cdot$m",
                            "scalar": [
                                {
                                    "value": "0"
                                }
                            ],
                            "name": "Power factor"
                        },
                        "reference": [
                            {
                                "citation": "10.1143/JPSJ.79.124702"
                            }
                        ]
                    },
                    {
                        "dataType": "Experimental",
                        "property": {
                            "scalar": [
                                {
                                    "value": "0"
                                }
                            ],
                            "name": "Thermoelectric figure of merit (zT)"
                        },
                        "reference": [
                            {
                                "citation": "10.1143/JPSJ.79.124702"
                            }
                        ]
                    }
                ]
            }
        }
    ],
    "time": 3
}
```

Results from a search across [all data sets](#search_all) or a [single data set](#search_single) return the same response, an example of which is shown to the right.

The top level object of a search result contains three fields: hits, which gives the number of results that were found; time, which is the number of milliseconds that the search endpoint took to run the query; results, which contains the records that matched the search query. The value of the results field is structured according to the [MIF Schema](http://citrineinformatics.github.io/mif-documentation).

#### Python Response

The return object of CitrinationClient.search() is a [response object](http://www.python-requests.org/en/latest/api/#requests.Response) from the [requests package](http://www.python-requests.org). In the search examples, r.status_code gives the status code from the API response and r.json() decodes the content of the response from JSON.

### <a name="pagination">Pagination</a>

```python
# Page through all records in data set with id '1'
from citrination_client import CitrinationClient
client = CitrinationClient('your-unique-api-key', 'https://your-site.citrination.com')
size = 1
start = 0
while size > 0:
  r = client.search(data_set_id='1', from=start, per_page=100)
  size = len(r.json())
  start += size
  time.sleep(3) # Remember to sleep to avoid rate limiting!
```

If you need to access more results than the number specified by _per_page_, then you can iterate through them by setting the _from_ parameter. The python example to the right shows the code to iterate through all records in a data set with id '1'.

There is a hard limit of 100 results returned per request. Any value of _per_page_ larger than that will be reduced to 100.

### Rate Limiting

Access to the search API is rate-limited. Currently users must wait 3 seconds between search requests via the API. The API will return a 403 response code when the rate limit is exceeded (note that the timer is reset each time that a request is made, regardless of whether results were returned or not).

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

# Useful Links

* [http://citrineinformatics.github.io/api-documentation](http://citrineinformatics.github.io/api-documentation) This document.
* [http://citrineinformatics.github.io/mif-documentation](http://citrineinformatics.github.io/mif-documentation) Definition of the MIF schema (structure of the results of the search API).
* [https://github.com/CitrineInformatics/python-citrination-client](https://github.com/CitrineInformatics/python-citrination-client) Python client for accessing the citrination API.
