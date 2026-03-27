---
title: Data Formats
nav_order: 3
---

The API supports JSON for input and output. In addition you can specify input using form data.

The format of the output data is provided as is and the reference is this document.

All data is UTF-8.

All examples and object attributes in this documentation are JSON. They are lowercase and use underscore as separator.

## Output

To specify JSON as output use HTTP-header `Accept: application/json`. Output can also be defined by using extension in the path: `/resources.json` but it is recommended to use `/resources` and specify output format in the HTTP-header. Multiple items are returned as an array.

## Input

To specify JSON as input use HTTP-header `Content-Type: application/json`. The body must be JSON-formatted and include the object with required attributes.

To use normal form data specify HTTP-header `application/x-www-form-urlencoded`. Form values should be sent like this: `resource[title]=Unicorn`. To specify array values use: `selected_resources[]=1337&selected_resources[]=555`. The same applies when doing a GET request with URL parameters.
