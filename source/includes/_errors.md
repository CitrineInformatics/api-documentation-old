# Errors

The Citrination API uses the following error codes:


Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request is malformed
401 | Unauthorized -- Your API key is wrong
404 | Not Found -- The specified method or document could not be found
406 | Not Acceptable -- You requested a format that isn't json
429 | Too Many Requests -- You're making too many requests per second. Please wait
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarially offline for maintanance. Please try again later.
