# Errors

The Ledge API uses the following error codes:


Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request is invalid. You may be missing a required parameter.
403 | Forbidden -- Your user token is no longer valid.
404 | Not Found -- The item or uri is not found.
414 | Wrong Mime Type - Api is json only.
417 | Http not supported - Switch to https.
429 | Too Many Requests -- Slow down!
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarially offline for maintanance. Please try again later.
