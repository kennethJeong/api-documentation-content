.. _makingrequests:

Making Requests
===============

Content Negotiation
-------------------
Clients using the API should specify that they accept responses using the `application/vnd.api+json` format. For convenience, we will also accept `application/json` since it is the default for many popular client libraries.

The Server will respond with a `Content-Type` header that mirrors the format requested by the client.



Setting Headers
---------------
To specify the headers, use this code:

**Shell**

.. code-block:: shell

  curl "endpoint-url" \
  -H "Authorization: Bearer <api-key>"
  -H "Accept: application/vnd.api+json"

**Java**

.. code-block:: java

  import java.io.*;
  import java.net.*;

  URL url = new URL("endpoint-url");
  HttpURLConnection conn = (HttpURLConnection) url.openConnection();
  conn.setRequestMethod("GET");
  conn.setRequestProperty("Authorization","Bearer <api-key>");
  conn.setRequestProperty("Accept", "application/vnd.api+json");

  conn.getInputStream()

**Python**

.. code-block:: python

  import requests

  url = "endpoint-url"

  header = {
    "Authorization": "Bearer <api-key>",
    "Accept": "application/vnd.api+json"
  }

  r = requests.get(url, headers=header)

**Go**

.. code-block:: go

  import "net/http"

  client := &http.Client{}
  req, _ := http.NewRequest("GET","endpoint-url",nil)
  req.Header.Set("Authorization", "Bearer <api-key>")
  req.Header.Set("Accept", "application/vnd.api+json")
  res, _ := client.Do(req)



.. _regions:

Platforms and Regions
---------------------
The PUBG API shards data by either `platform` or `platform-region` depending on the request, and therefore requires a shard to be specified in the URL for most requests. The current shards are:

**shards/$platform** - *the platform shard*

- **kakao** - Kakao
- **psn** - PS4
- **steam** - Steam
- **tournament** - Tournaments
- **xbox** - Xbox

**shards/$platform-region** - *the platform-region shard*

- **pc-as** - Asia
- **pc-eu** - Europe
- **pc-jp** - Japan
- **pc-kakao** - Kakao
- **pc-krjp** - Korea
- **pc-na** - North America
- **pc-oc** - Oceania
- **pc-ru** - Russia
- **pc-sa** - South and Central America
- **pc-sea** - South East Asia
- **pc-tournament** - Tournaments
- **psn-as** - Asia
- **psn-eu** - Europe
- **psn-na** - North America
- **psn-oc** - Oceania
- **xbox-as** - Asia
- **xbox-eu** - Europe
- **xbox-na** - North America
- **xbox-oc** - Oceania
- **xbox-sa** - South America

The shard is specified after the pubg domain and before the endpoint like this::

  "...pubg.com/shards/steam/endpoint..."

**Note: Use the platform shard when making requests for PC and PS4 players' season stats for seasons after division.bro.official.2018-09, and for Xbox season stats for seasons after division.bro.official.2018-08. Use the platform-region shard for making any other requests for players' season stats.**

**The platform shard should be used at all other endpoints that require a shard. The platform-region shard is deprecated.**



GZIP
----
Clients can specify the header `Accept-Encoding: gzip`, and the server will compress responses. Responses will be returned with `Content-Encoding: gzip`.

Given the size of matches, this can have significant performance benefits.

To specify the header Accept-Encoding, use this code:

**Shell:**

.. code-block:: shell

  -H "Accept-Encoding: gzip"


**Java:**

.. code-block:: java

  conn.setRequestProperty("Accept-Encoding","gzip");


**Python:**

.. code-block:: python

  header = {"Accept-Encoding":"gzip"}


**Go:**

.. code-block:: go

  req.Header.Set("Accept-Encoding", "gzip")

Data Retention Period
---------------------
The data retention period is 14 days. **Match data** older than 14 days will not be available.



Responses
---------
All Server responses will be in JSON-API format and contain a root JSON object.

Each response will contain at least one of the following top-level members:

- `data` : the response's "primary data"
- `errors` : an array of error objects

A response may contain any of these top-level members:

- `links`: a links object related to the primary data.
- `included`: an array of resource objects that are related to the primary data and/or each other ("included resources").
- `meta`: not currently used.

If a document does not contain a top-level data key, the included array will not be present either.



Cross Origin Resource Sharing
-----------------------------
The API supports Cross Origin Resource Sharing (CORS) for AJAX requests from any origin. You can read the CORS W3C Recommendation `here <https://www.w3.org/TR/cors/>`_.

Here's a sample request sent from a browser hitting 'example.com':

**Shell:**

.. code-block:: shell

  curl -i https://api.pubg.com/status -H "Origin: http://example.com"
  HTTP/1.1 200 OK
  ...
  Access-Control-Allow-Origin: *
  Access-Control-Expose-Headers: Content-Length

This is what the CORS preflight request looks like:

.. code-block:: shell

  curl -i https://api.pubg.com/status -H "Origin: http://example.com" -X OPTIONS
  HTTP/1.1 200 OK
  ...
  Access-Control-Allow-Headers: Origin,X-Title-Id,Authorization
  Access-Control-Allow-Methods: GET,POST,OPTIONS
  Access-Control-Allow-Origin: *
  Access-Control-Max-Age: 86400
