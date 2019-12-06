# Errors

The Thumbtack API uses the following error codes:


Error Code | Meaning
---------- | -------
400 | Bad Request -- Something about your request was invalid or malformed.
401 | Unauthorized -- Your API key is wrong.
404 | Not Found -- The requested resource was not be found.
404 | Conflict -- Something about your request conflicted with the current state of the server.
429 | Too Many Requests -- You made too many requests, too quickly.
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarily unavailable to serve your request. Please try again later.
