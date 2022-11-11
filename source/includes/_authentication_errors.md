# Authentication OAth2.0 Errors

## 400 Errors: StatusBadRequest

Error Code | Description
---------- | -------
INVALID_CLIENT | Client authorization failed due to invalid or missing client ID provided
UNAUTHORIZED_CLIENT | The client is not authorized to use this grant type
INVALID_REQUEST | A request is missing a request parameter like, code, redirect_uri, refresh_token
INVALID_GRANT | The provided authorization grant or refresh token is invalid, expired, revoked, or does not match the redirect URL
UNSUPPORTED_GRANT_TYPE | The authorization grant_type provided in the request is not supported by the authorization server
INSUFFICIENT_SCOPE |  The request required higher privileges than provided by the access token
503 | Service Unavailable -- We're temporarily unavailable to serve your request. Please try again later.

## 401 Errors: StatusUnauthorized

Error Code | Description
---------- | -------
INVALID_TOKEN | The access token provided in the authorization header is not valid

## 403 Errors: StatusForBidden

Error Code | Description
---------- | -------
INSUFFICIENT_SCOPE | The request required higher privileges than provided by the access token
