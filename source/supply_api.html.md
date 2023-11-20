---
title: Thumbtack API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='#contact-us'>Partner with Us!</a>

includes:
  - authentication_errors
  - errors
  - terms_of_use

search: true
---

# Introduction

The purpose of this API is to enable Supply Partners to integrate Thumbtack leads into their customer
acquisition workflows. This is accomplished by supporting two functions - **(1) lead transfer** and
**(2) two way messaging**. To support lead transfer, the Supply Partner will expose an API endpoint that
Thumbtack will call. To support two way messaging, both Thumbtack and the Supply Partner will expose
API endpoints for the other party to call.

Partners currently need approval to integrate with us. To request approval, feel free to reach out to us at teampartnerships AT thumbtack DOT com

Your use of our API is subject to Thumbtack's <a href='#api-terms-of-use'>API Terms of Use</a>.

# Authentication

## STEP 1: Get Client Credentials

Contact Thumbtack Partnership team for get the client-side credentials. These credentials will contain these two piece of information:

* Client ID / Consumer Key 

* Client Secret / Consumer Secret

While requesting the client credentials, partners will have to provide redirectURL to the Thumbtack team.

### Test OAuth flow during API Integration

Partners can test the OAuth flow by requesting the test client. They will need two separate clients for testing and production. 
They will have to contact the Thumbtack partnership team for creating the test client which will allow you to test the entire OAuth flow.
They will have to provide different redirectURL for the Production credentials and staging credentials.

## STEP 2: Connect Thumbtack account with Partner
> Sample URL

```
https://thumbtack.com/services/partner-connect/?client_id=THUMBTACK%20INTERNAL&redirect_uri=http://redirect.thumbtack.com&response_type=code&scope=messages
```

Use the consumer key as a client_ID that Thumbtack provided to you to request an AuthCode. Also, include the redirect_url so that  Thumbtack knows where to redirect users after they authorize access to their data.

`https://thumbtack.com/services/partner-connect/?client_id=<CLIENT_ID>&redirect_uri=<REDIRECT_URL>&response_type=code&scope=<SCOPE>`

### Expected Parameters in the URL

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
client_id | string | Consumer key provided by Thumbtack | Y
scope | string | Authorization scopes allow to access specific information from Thumbtack.| Y
redirect_uri | string | Thumbtack redirects Pro to this URL after they authorization | N
response_type | string | Must contain string `code` as a response type | Y
state | string | Client can insert state information that will be appended to the redirect_uri upon success user authorization | N

### Authorization scopes

Partners will have to pass the relevant scope with which they want to integrate. 
Partners can pass multiple scopes if they want to integrate with multiple Thumbtack endpoints.
The Access token issued will be limited to the scopes granted.

Available scopes:

* messages 
  
* availability
  
* bookings
  
* targeting 

### Test OAuth flow during API Integration
> Sample URL

```
https://thumbtack.com/services/partner-connect/?client_id=%3CCLIENT_ID%3E&redirect_uri=%3CREDIRECT_URL%3E&response_type=code&scope=messages
```

Partners will be able to get the AuthCode with the test Client via partner connect URL.

## STEP 3: Pro Authorizes Partner

Pro can select the Thumbtack Business name from the dropdown and click on the “Confirm Connection” button to Authorize that partner.

![image info](images/authcode.png)

### Test OAuth flow during API Integration

<span style="color:Red">**NOTE:**</span> If Partners are using test Client, they can generate the Auth-code as many times as they want. 
They are not required to disconnect the flow before issuing the Auth-Code again.

## STEP 4: Authorization Code
> Sample URL when Redirect URL is https://redirect.thumbtack.com

```
https://redirect.thumbtack.com/?code=BnCICCQ9KWF6WXj_xtiN3A
```

After confirming the connection, There are two ways the client will receive auth-code:

* If the client sends `redirect_uri` in the URL(step-1), then Thumbtack redirects the pro to a third-party URL with the authorization code in the URL. Redirection URL will look like below:

`<REDIRECT_URL>/?code=<AUTHORIZATION_CODE>&scope=<SCOPE>
`

* If the client does not send `redirect_uri` in the URL(step-1), then the client will see auth code like below.

![image info](images/authcode_2.png)

### Expected Parameters in the URL
Parameter | Type | Description 
--------- | ---- | ----------- 
code | string | Authorization code appended to redirect_url in the previous step

Grab the Authorization Code and follow the below steps to get the Access Token.

<span style="color:Green">***Auth code expiry time - 5 minutes***</span>

### Test OAuth flow during API Integration
> Sample URL when Redirect URL is https://redirect.thumbtack.com

```
https://redirect.thumbtack.com/?code=BnCICCQ9KWF6WXj_xtiN3A&scope=messages
```

This step issues the auth-code for test client same as the production flow. Please check the above information for more details.

<span style="color:Red">**NOTE:**</span> If Partners are using test Client, they can generate the Auth-code as many times as they want.
They are not required to disconnect the flow before issuing the Auth-Code again.

## STEP 5: Request Access Token

> Sample Authorization Header

```
Authorization: Basic VEhVTUJUQUNLIElOVEVSTkFMOlp4UnFMbnZZQUMzSERqVFlkem9sdFEK=
```

> Sample Request

```shell
curl --location --request POST 'https://pro-api.thumbtack.com/v2/tokens/access?grant_type=authorization_code&code=BnCICCQ9KWF6WXj_xtiN3A&redirect_uri=http://redirect.thumbtack.com&token_type=AUTH_CODE' \
--header 'Authorization: Basic VEhVTUJUQUNLIElOVEVSTkFMOlp4UnFMbnZZQUMzSERqVFlkem9sdFEK='
```

> Sample Response

```
{
   "access_token": "1.eyJCdXNpbmVzc1BLIjozODgwMDYwNjczNTExNTA1OTUsIkNsaWVudElEIjoiVEhVTUJUQUNLIElOVEVSTkFMIiwiU2NvcGUiOlsibWVzc2FnZXMiXSwiRXhwaXJlc0F0IjoiMjAyMS0xMC0xNFQwMjo1OToyOS44MjIwMDU2ODhaIiwiU3JjQXV0aENvZGUiOiIwNjcwODgwODI0M2QyOTYxN2E1OTc4ZmZjNmQ4OGRkY2UzYjBjNDQyOThmYzFjMTQ5YWZiZjRjODk5NmZiOTI0MjdhZTQxZTQ2NDliOTM0Y2E0OTU5OTFiNzg1MmI4NTUifQ.qJDfeuYfdFZCSVQmBUgr_kDoZeeEUD4y4oVTVMEc4EQ",
   "token_type": "bearer",
   "expires_in": 3600,
   "refresh_token": "_s3cnwGAb5VgBt-CmYFSOw"
}
```

Once the pro authorizes access, the user is redirected back to the URL with the authorization code. This API will use an authorization code from the request return access token and refresh token in the response.

### Request Endpoint

`POST https://pro-api.thumbtack.com/v2/tokens/access`

### Request Header

`Authorization: Basic <B64-encoded_oauth_credentials>`

The `Authorization: Basic` authorization header is generated through a Base64 encoding of `<client_id>:<client_secret>`.
You can use https://www.base64encode.org/ to see how headers should be encoded.

### Expected Request Params

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
grant_type | string | Must contain string `authorization_code` as a grant type | Y
code | string | Authorization code appended to redirect_url in the step-3 | Y
redirect_uri | string | Thumbtack redirected to this URL after they authorization | Y
token_type | string | Must contain string `AUTH_CODE` as a token type | Y

### Expected Response Params

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
access_token | string | The access token issued by the authorization server | Y
token_type | string | Type of the token issued. Must return `bearer` as a token type | Y
expires_in | number | Access Token lifetime in seconds. The default expiry is `3600` seconds (1 hour).  | Y
refresh_token | string | The refresh token issued by the authorization server | Y

<span style="color:Green">***Access Token expiry time - 1 hour***</span>

<span style="color:Green">***Refresh Token expiry time - 720 hours***</span>

### Test OAuth flow during API Integration
> Sample Request

```shell
curl --location --request POST 'https://pro-api.thumbtack.com/v2/tokens/access?grant_type=authorization_code&code=BnCICCQ9KWF6WXj_xtiN3A&redirect_uri=http://redirect.thumbtack.com&token_type=AUTH_CODE' \
--header 'Authorization: Basic VEhVTUJUQUNLIElOVEVSTkFMOlp4UnFMbnZZQUMzSERqVFlkem9sdFEK='
```

> Sample Response

```
{
   "access_token": "1.eyJCdXNpbmVzc1BLIjozODgwMDYwNjczNTExNTA1OTUsIkNsaWVudElEIjoiVEhVTUJUQUNLIElOVEVSTkFMIiwiU2NvcGUiOlsibWVzc2FnZXMiXSwiRXhwaXJlc0F0IjoiMjAyMS0xMC0xNFQwMjo1OToyOS44MjIwMDU2ODhaIiwiU3JjQXV0aENvZGUiOiIwNjcwODgwODI0M2QyOTYxN2E1OTc4ZmZjNmQ4OGRkY2UzYjBjNDQyOThmYzFjMTQ5YWZiZjRjODk5NmZiOTI0MjdhZTQxZTQ2NDliOTM0Y2E0OTU5OTFiNzg1MmI4NTUifQ.qJDfeuYfdFZCSVQmBUgr_kDoZeeEUD4y4oVTVMEc4EQ",
   "token_type": "bearer",
   "expires_in": 3600,
   "refresh_token": "_s3cnwGAb5VgBt-CmYFSOw"
}
```

Partners will be able to use the test auth-code and call /tokens API to get the test access token and refresh token pair in the /token API response.
Test token's behavior is the same as the production environment. 

## STEP 6: Refresh Access Token

> Sample Authorization Header

```
Authorization: Basic VEhVTUJUQUNLIElOVEVSTkFMOlp4UnFMbnZZQUMzSERqVFlkem9sdFEK=
```

> Sample Request 

```shell
curl --location --request POST 'https://pro-api.thumbtack.com/v2/tokens/access?grant_type=refresh_token&refresh_token=zRw4chXenfjNLenO2_lVOQ&token_type=REFRESH' \
--header 'Authorization: Basic VEhVTUJUQUNLIElOVEVSTkFMOlp4UnFMbnZZQUMzSERqVFlkem9sdFEK='
```

> Sample Response

```
{
   "access_token": "1.eyJCdXNpbmVzc1BLIjozMTQxNDgwOTYyOTIxODQyNDMsIkNsaWVudElEIjoiVEhVTUJUQUNLIElOVEVSTkFMIiwiU2NvcGUiOltdLCJFeHBpcmVzQXQiOiIyMDIxLTEwLTE0VDAyOjQ1OjA2LjI4NjUwNzkxOFoiLCJTcmNBdXRoQ29kZSI6ImIzYzUzNjk3ZGU0ZTlmNWQxNTk2NDhmMGM2MTc3YWRhZTNiMGM0NDI5OGZjMWMxNDlhZmJmNGM4OTk2ZmI5MjQyN2FlNDFlNDY0OWI5MzRjYTQ5NTk5MWI3ODUyYjg1NSJ9.tgDkhV1ooM1C7UjWhZ_mCXbzF11YH4NgsweJdfvG-f8",
   "token_type": "bearer",
   "expires_in": 3600,
   "refresh_token": "eoZNGASGdTWop1jINi-ouQ"
}
```

When Access Token expires, use the below API with refresh token which has a long lifetime, to get a new access token and refresh token pair.
### Request Endpoint

`POST https://pro-api.thumbtack.com/v2/tokens/access`

### Request Header

`Authorization: Basic <B64-encoded_oauth_credentials>`

The `Authorization: Basic` authorization header is generated through a Base64 encoding of `<client_id>:<client_secret>`.
You can use https://www.base64encode.org/ to see how headers should be encoded.

### Expected Request Params

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
grant_type | string | Must contain string `refresh_token` as a grant type | Y
refresh_token | string | Refresh Token value that you received along with the access token in step:4 | Y
token_type | string | Must contain string `REFRESH` as a token type | Y

### Expected Response Params

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
access_token | string | The access token issued by the authorization server | Y
token_type | string | Type of the token issued. Must return `bearer` as a token type | Y
expires_in | number | Access Token lifetime in seconds. The default expiry is `3600` seconds (1 hour).  | Y
refresh_token | string | The refresh token issued by the authorization server | Y

### Error handling with grace period 
We have implemented a grace period logic in OAuth refresh token flow to handle Network errors, Time-out errors or similar one.
If you receive `null` in the response to `/tokens/access` API at the time of refreshing the token,
we encourage Partners to implement the retry logic on their side which will call `/tokens/access` API with same refresh token to get the valid response.
<span style="color:Green">***Grace period time - 10 sec***</span>

### Test OAuth flow during API Integration
> Sample Request

```shell
curl --location --request POST 'https://pro-api.thumbtack.com/v2/tokens/access?grant_type=refresh_token&refresh_token=zRw4chXenfjNLenO2_lVOQ&token_type=REFRESH' \
--header 'Authorization: Basic VEhVTUJUQUNLIElOVEVSTkFMOlp4UnFMbnZZQUMzSERqVFlkem9sdFEK='
```

> Sample Response

```
{
   "access_token": "1.eyJCdXNpbmVzc1BLIjozODgwMDYwNjczNTExNTA1OTUsIkNsaWVudElEIjoiVEhVTUJUQUNLIElOVEVSTkFMIiwiU2NvcGUiOlsibWVzc2FnZXMiXSwiRXhwaXJlc0F0IjoiMjAyMS0xMC0xNFQwMjo1OToyOS44MjIwMDU2ODhaIiwiU3JjQXV0aENvZGUiOiIwNjcwODgwODI0M2QyOTYxN2E1OTc4ZmZjNmQ4OGRkY2UzYjBjNDQyOThmYzFjMTQ5YWZiZjRjODk5NmZiOTI0MjdhZTQxZTQ2NDliOTM0Y2E0OTU5OTFiNzg1MmI4NTUifQ.qJDfeuYfdFZCSVQmBUgr_kDoZeeEUD4y4oVTVMEc4EQ",
   "token_type": "bearer",
   "expires_in": 3600,
   "refresh_token": "_s3cnwGAb5VgBt-CmYFSOw"
}
```

Partners will be able to use the test auth-code and call /tokens API to get the test access token and refresh token pair in the /token API response.
Test token's behavior is the same as the production environment.

# Thumbtack Endpoints

Thumbtack will expose the following endpoints to Partners.
All endpoints should be versioned to support future schema changes.
Note that passing in the `Content-Type` header is required.

## Messages

> Sample Authorization Header

```
Authorization: Bearer 1.eyJCdXNpbmVzc1BLIjozOTUwMjMyMDc2MTEwMjMzNzIsIkNsaWVudElEIjoicHJvbmV4aSIsIlNjb3BlIjpbIm1lc3NhZ2VzIl0sIkV4cGlyZXNBdCI6IjIwMjEtMTAtMjJUMjI6MTM6MDMuNDMwMTMwNzQ4WiIsIlNyY0F1dGhDb2RlIjoiNGJlOTFjNTIyN2RmOGIxNTdjNjZlMTlkOGRhMjI0ZTRlM2IwYzQ0Mjk4ZmMxYzE0OWFmYmY0Yzg5OTZmYjkyNDI3YWU0MWU0NjQ5YjkzNGNhNDk1OTkxYjc4NTJiODU1IiwiQWNjb3VudElEIjozMDczMDYyNDIwMTgzOTQzMTZ9.CyNhsuC0JQDM9zNrpjduD7Ja_qLbQw51bq_H7Ry39C4
```

> Sample Request

```shell
curl --location --request POST 'https://api.thumbtack.com/v2/business/395023207611023372/lead/123/message' \
--header 'Authorization: Bearer 1.eyJCdXNpbmVzc1BLIjozOTUwMjMyMDc2MTEwMjMzNzIsIkNsaWVudElEIjoicHJvbmV4aSIsIlNjb3BlIjpbIm1lc3NhZ2VzIl0sIkV4cGlyZXNBdCI6IjIwMjEtMTAtMjJUMjI6MTM6MDMuNDMwMTMwNzQ4WiIsIlNyY0F1dGhDb2RlIjoiNGJlOTFjNTIyN2RmOGIxNTdjNjZlMTlkOGRhMjI0ZTRlM2IwYzQ0Mjk4ZmMxYzE0OWFmYmY0Yzg5OTZmYjkyNDI3YWU0MWU0NjQ5YjkzNGNhNDk1OTkxYjc4NTJiODU1IiwiQWNjb3VudElEIjozMDczMDYyNDIwMTgzOTQzMTZ9.CyNhsuC0JQDM9zNrpjduD7Ja_qLbQw51bq_H7Ry39C4' \
--header 'Content-Type: application/json' \
--data-raw '{"text": "Hello, how can I help you?"}'
```

> Sample Response

```json
{
    "status": "success"
}
```

<span style="color:Red">**NOTE:**</span> 
Please contact Thumbtack partnership team if you want to integrate with this endpoint.

Send messages on behalf of the Pro to a Thumbtack customer for a given lead.
This is an endpoint Thumbtack has created for Partners to call. Thumbtack provides both a production
and test environment endpoint. <br />
`/v2` Endpoint will require `Bearer <access_token>` in the `Authorization` header. <br />
`/v1` Endpoint will use HTTP basic authentication, and Thumbtack will provide
Partners with a username and password for Partners to call these endpoints.

### Request Endpoint (Production Environment)
`POST https://pro-api.thumbtack.com/v2/business/{BusinessID}/lead/{leadID}/message`

`:businessID` is the identifier of your business. <br />
`:leadID` is the identifier of the lead whose customer you wish to message.

### HTTP Endpoint (Test Environment)
`POST https://staging-pro-api.thumbtack.com/v2/business/{BusinessID}/lead/{leadID}/message`

`:businessID` is the identifier of your business. <br />
`:leadID` is the identifier of the lead whose customer you wish to message.

### Request Header
`Authorization: Bearer <access_token>`

`access_token` is the value that you received along with the `/token/access API`.

### Expected Request Body

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
text | string | Text of the message | Y

### Test OAuth flow during API Integration
> Sample Request

```shell
curl --location --request POST 'https://api.thumbtack.com/v2/business/395023207611023372/lead/123/message' \
--header 'Authorization: Bearer 1.eyJCdXNpbmVzc1BLIjozOTUwMjMyMDc2MTEwMjMzNzIsIkNsaWVudElEIjoicHJvbmV4aSIsIlNjb3BlIjpbIm1lc3NhZ2VzIl0sIkV4cGlyZXNBdCI6IjIwMjEtMTAtMjJUMjI6MTM6MDMuNDMwMTMwNzQ4WiIsIlNyY0F1dGhDb2RlIjoiNGJlOTFjNTIyN2RmOGIxNTdjNjZlMTlkOGRhMjI0ZTRlM2IwYzQ0Mjk4ZmMxYzE0OWFmYmY0Yzg5OTZmYjkyNDI3YWU0MWU0NjQ5YjkzNGNhNDk1OTkxYjc4NTJiODU1IiwiQWNjb3VudElEIjozMDczMDYyNDIwMTgzOTQzMTZ9.CyNhsuC0JQDM9zNrpjduD7Ja_qLbQw51bq_H7Ry39C4' \
--header 'Content-Type: application/json' \
--data-raw '{"text": "Hello, how can I help you?"}'
```

> Sample Response

```json
{
    "status": "success"
}
```

If the access token passes as an authorization header related to the test client, Thumbtack will only validate the token and return the dummy response. 
(As test clients are only for testing the OAuth flow and to check if they can hit the actual API with that token or not.)

## Thumbtack Info

> Sample Authorization Header

```
Authorization: Bearer 1.eyJCdXNpbmVzc1BLIjozOTUwMjMyMDc2MTEwMjMzNzIsIkNsaWVudElEIjoicHJvbmV4aSIsIlNjb3BlIjpbIm1lc3NhZ2VzIl0sIkV4cGlyZXNBdCI6IjIwMjEtMTAtMjJUMjI6MTM6MDMuNDMwMTMwNzQ4WiIsIlNyY0F1dGhDb2RlIjoiNGJlOTFjNTIyN2RmOGIxNTdjNjZlMTlkOGRhMjI0ZTRlM2IwYzQ0Mjk4ZmMxYzE0OWFmYmY0Yzg5OTZmYjkyNDI3YWU0MWU0NjQ5YjkzNGNhNDk1OTkxYjc4NTJiODU1IiwiQWNjb3VudElEIjozMDczMDYyNDIwMTgzOTQzMTZ9.CyNhsuC0JQDM9zNrpjduD7Ja_qLbQw51bq_H7Ry39C4
```

> Sample Request

```shell
curl --location --request GET 'https://pro-api.thumbtack.com/v2/get-thumbtack-info' \
--header 'Authorization: Bearer 1.eyJCdXNpbmVzc1BLIjozMTQxNDgwOTYyOTIxODQyNDMsIkNsaWVudElEIjoiODJNUlY1MmFGSXUwU0ZZTEpPM3NzcHg3aGF3RjVaZXozdVJTVkNRMCtsST0iLCJTY29wZSI6WyJtZXNzYWdlcyJdLCJFeHBpcmVzQXQiOiIyMDIxLTExLTEyVDIyOjE0OjU3Ljc3MzI5NTgyM1oiLCJTcmNBdXRoQ29kZSI6IjE2NDE1OWY1MDk2YzgxZTRiNzA5ODY4MjJjYWI3MWQzZTNiMGM0NDI5OGZjMWMxNDlhZmJmNGM4OTk2ZmI5MjQyN2FlNDFlNDY0OWI5MzRjYTQ5NTk5MWI3ODUyYjg1NSIsIkFjY291bnRJRCI6MzgxMzU1NTEwMjM1Mjc1Mjc0fQ.IXT9OXvHO43u7Z4SZPjNtbQ_hipMIAbNI7GmpmvobUE' \
--header 'Content-Type: application/json'
```

> Sample Response

```json
{
  "business_pk": "388006067351150595",
  "client_id": "THUMBTACK INTERNAL",
  "scope": [
    "messages"
  ],
  "business_name": "cleaner business"
}
```

Return thumbtack information by parsing the access_token value that you received along with the /token API.

### HTTP Endpoint
`GET https://pro-api.thumbtack.com/v2/get-thumbtack-info`

### Request Header

`Authorization: Bearer <access_token>`

`access_token` is the value that you received along with the `/token/access API`.

### Expected Response Params

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
business_pk | string | Business PK related to the access token. | Y
client_id | string | Consumer key provided by Thumbtack which related to the access token. | Y
scope | array | Authorization scopes allow to access specific information from Thumbtack.  | Y
business_name | string | Business Name related to the access token. | Y

### Test OAuth flow during API Integration
> Sample Request

```shell
curl --location --request GET 'https://pro-api.thumbtack.com/v2/get-thumbtack-info' \
--header 'Authorization: Bearer 1.eyJCdXNpbmVzc1BLIjozMTQxNDgwOTYyOTIxODQyNDMsIkNsaWVudElEIjoiODJNUlY1MmFGSXUwU0ZZTEpPM3NzcHg3aGF3RjVaZXozdVJTVkNRMCtsST0iLCJTY29wZSI6WyJtZXNzYWdlcyJdLCJFeHBpcmVzQXQiOiIyMDIxLTExLTEyVDIyOjE0OjU3Ljc3MzI5NTgyM1oiLCJTcmNBdXRoQ29kZSI6IjE2NDE1OWY1MDk2YzgxZTRiNzA5ODY4MjJjYWI3MWQzZTNiMGM0NDI5OGZjMWMxNDlhZmJmNGM4OTk2ZmI5MjQyN2FlNDFlNDY0OWI5MzRjYTQ5NTk5MWI3ODUyYjg1NSIsIkFjY291bnRJRCI6MzgxMzU1NTEwMjM1Mjc1Mjc0fQ.IXT9OXvHO43u7Z4SZPjNtbQ_hipMIAbNI7GmpmvobUE' \
--header 'Content-Type: application/json'
```

> Sample Response

```json
{
  "business_pk": "388006067351150595",
  "client_id": "THUMBTACK INTERNAL",
  "scope": [
    "messages"
  ],
  "business_name": "cleaner business"
}
```

Partners will be able to use the access token to get the thumbtack information like (BusinessPK, clientID, etc.)

## Disconnect from OAuth Flow
> Sample Authorization Header

```
Authorization: Basic VEhVTUJUQUNLIElOVEVSTkFMOlp4UnFMbnZZQUMzSERqVFlkem9sdFEK=
```

> Sample Request

```shell
curl --location --request POST 'https://pro-api.thumbtack.com/v2/disconnect-partner?business_pk=432428213655306241' \
--header 'Authorization: Basic VEhVTUJUQUNLIElOVEVSTkFMOlp4UnFMbnZZQUMzSERqVFlkem9sdFEK='
```

> Sample Response

```json
{
  "business_pk": "432428213655306241",
  "client_id": "THUMBTACK INTERNAL"
}
```

Removes tokens and disables the service on the Thumbtack side so which will allow the partner/pro to trigger the OAuth initialization flow again.
Endpoints will use HTTP basic authentication, and Thumbtack will provide 
Partners with a username and password for Partners to call these endpoints.

### HTTP Endpoint
`POST https://pro-api.thumbtack.com/v2/disconnect-partner`

### Request Header

`Authorization: Basic <B64-encoded_oauth_credentials>`

The `Authorization: Basic` authorization header is generated through a Base64 encoding of `<client_id>:<client_secret>`.
You can use https://www.base64encode.org/ to see how headers should be encoded.

### Expected Request Params

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
business_pk | string | business_pk that you can get via `/get-thumbtack-info` API | Y

### Expected Response Params

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
business_pk | string | Business PK | Y
client_id | string | Consumer key provided by Thumbtack related to the business. | Y

### Test OAuth flow during API Integration
> Sample Request

```shell
curl --location --request POST 'https://pro-api.thumbtack.com/v2/disconnect-partner?business_pk=432428213655306241' \
--header 'Authorization: Basic VEhVTUJUQUNLIElOVEVSTkFMOlp4UnFMbnZZQUMzSERqVFlkem9sdFEK='
```

> Sample Response

```json
{
  "business_pk": "432428213655306241",
  "client_id": "THUMBTACK INTERNAL"
}
```
Disconnect flow will remove all the previous auth-codes and tokens from the Thumbtack database.

<span style="color:Red">**NOTE:**</span> Thumbtack team will always recommend that partners should call this endpoint when they completed the testing so that Thumbtack can do a cleanup in order to remove older tokens and older auth codes.

## Targeting Endpoints

<span style="color:Red">**NOTE:**</span>
Please contact Thumbtack partnership team if you want to integrate with these endpoints.

Targeting preferences tell Thumbtack where you work when you work, and what kind of work you do. 
They also help the right customers find you. 
That’s why you need to set targeting preferences before you appear in Thumbtack search results.
To use these endpoints, partners will require to pass the `Bearer <access_token>` in the `Authorization` header.
Note that passing in the `Content-Type` header is required.

<span style="color:Red">**NOTE:**</span>
Please use the `targeting` scope while initializing the OAuth flow to use any of the targeting APIs.
Sample partner-connect URL with targeting scope: <br />
```
https://thumbtack.com/services/partner-connect/?client_id=<ClientID>&redirect_uri=<RedirectURL>&response_type=code&scope=targeting
```

### 1. Service based Targeting Endpoint
> Sample Authorization Header

```
Authorization: Bearer 1.eyJCdXNpbmVzc1BLIjo0NDAzNzA2NzE5ODQyMjIyMTMsIkNsaWVudElEIjoiZkVsSlQ4TzhNZm1zdGZqeFVPclhDWFJKSDl2RE1PL2VWL2NGTUkzenFLST0iLCJTY29wZSI6WyJ0YXJnZXRpbmciXSwiRXhwaXJlc0F0IjoiMjAyMy0wNC0xM1QyMDowODo0OC4yNTAyNjgxMjNaIiwiU3JjQXV0aENvZGUiOiJkOWU4MWMwNTRmMGFmYjBlMDE1ZWJlYmI2ZmJjNDkxMWUzYjBjNDQyOThmYzFjMTQ5YWZiZjRjODk5NmZiOTI0MjdhZTQxZTQ2NDliOTM0Y2E0OTU5OTFiNzg1MmI4NTUiLCJBY2NvdW50SUQiOjM5ODEyMjMyMDExNjUwNjYzM30.EKPoRvI29bSEnD5ofWIBzxY0HkTWCVc6aej8h3Pu_gg
```

> Sample Request

```shell
curl --location --request PATCH 'https://pro-api.thumbtack.com/v3/accounts/~/services/440370671984222213/preferences/targeting' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer 1.eyJCdXNpbmVzc1BLIjo0NDAzNzA2NzE5ODQyMjIyMTMsIkNsaWVudElEIjoiZkVsSlQ4TzhNZm1zdGZqeFVPclhDWFJKSDl2RE1PL2VWL2NGTUkzenFLST0iLCJTY29wZSI6WyJ0YXJnZXRpbmciXSwiRXhwaXJlc0F0IjoiMjAyMy0wNC0xM1QyMDowODo0OC4yNTAyNjgxMjNaIiwiU3JjQXV0aENvZGUiOiJkOWU4MWMwNTRmMGFmYjBlMDE1ZWJlYmI2ZmJjNDkxMWUzYjBjNDQyOThmYzFjMTQ5YWZiZjRjODk5NmZiOTI0MjdhZTQxZTQ2NDliOTM0Y2E0OTU5OTFiNzg1MmI4NTUiLCJBY2NvdW50SUQiOjM5ODEyMjMyMDExNjUwNjYzM30.EKPoRvI29bSEnD5ofWIBzxY0HkTWCVc6aej8h3Pu_gg' \
--data-raw '{  
   "enabled": "true" 
}'
```

> Sample Response

```json
{
  "status": "success"
}
```

Targeting preferences can be managed from the Services page or through this endpoint which allows you to 
turn targeting on and off for a specific serviceID.

### Request Endpoint (Production Environment)
`PATCH https://pro-api.thumbtack.com/v3/accounts/{accountID}/services/{serviceID}/preferences/targeting`

`:accountID` is the identifier of your account. (<span style="color:Red">**NOTE:**</span>  you can pass `~` for now) <br />
`:serviceID` is the identifier of your business.

### Request Header
`Authorization: Bearer <access_token>`

`access_token` is the value that you received along with the `/token/access API`.

### Expected Request Body

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
enabled | boolean | Pause or turn on targeting. Value can be “true” or “false”  | Y

### Error Codes:

#### 400 Bad Request
* Invalid ServiceID type
* Invalid body
* Missing body

#### 401 Unauthorized
* Invalid auth header
* Missing auth header
* Invalid ServiceID

#### 404 Not Found
* Missing ServiceID

### 2. Category based Targeting Endpoint

> Sample Authorization Header

```
Authorization: Bearer 1.eyJCdXNpbmVzc1BLIjo0NDAzNzA2NzE5ODQyMjIyMTMsIkNsaWVudElEIjoiZkVsSlQ4TzhNZm1zdGZqeFVPclhDWFJKSDl2RE1PL2VWL2NGTUkzenFLST0iLCJTY29wZSI6WyJ0YXJnZXRpbmciXSwiRXhwaXJlc0F0IjoiMjAyMy0wNC0xMlQwMDowODo1NS42MTY2NzUyMjZaIiwiU3JjQXV0aENvZGUiOiJmN2E4NzFiOTNmZTFiYjE2ODU2NmFhYjJhM2U3OGY5OGUzYjBjNDQyOThmYzFjMTQ5YWZiZjRjODk5NmZiOTI0MjdhZTQxZTQ2NDliOTM0Y2E0OTU5OTFiNzg1MmI4NTUiLCJBY2NvdW50SUQiOjM5ODEyMjMyMDExNjUwNjYzM30.DGOkATz50BcFyIOJAq8l4zB0IN84flSk7Sd-XlDWVXI
```

> Sample Request

```shell
curl --location --request PATCH 'https://pro-api.thumbtack.com/v3/accounts/~/services/440370671984222213/categories/109125193401647362/preferences/targeting' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer 1.eyJCdXNpbmVzc1BLIjo0NDAzNzA2NzE5ODQyMjIyMTMsIkNsaWVudElEIjoiZkVsSlQ4TzhNZm1zdGZqeFVPclhDWFJKSDl2RE1PL2VWL2NGTUkzenFLST0iLCJTY29wZSI6WyJ0YXJnZXRpbmciXSwiRXhwaXJlc0F0IjoiMjAyMy0wNC0xMlQwMDowODo1NS42MTY2NzUyMjZaIiwiU3JjQXV0aENvZGUiOiJmN2E4NzFiOTNmZTFiYjE2ODU2NmFhYjJhM2U3OGY5OGUzYjBjNDQyOThmYzFjMTQ5YWZiZjRjODk5NmZiOTI0MjdhZTQxZTQ2NDliOTM0Y2E0OTU5OTFiNzg1MmI4NTUiLCJBY2NvdW50SUQiOjM5ODEyMjMyMDExNjUwNjYzM30.DGOkATz50BcFyIOJAq8l4zB0IN84flSk7Sd-XlDWVXI' \
--data-raw '{  
   "enabled": "false" 
}'
```

> Sample Response

```json
{
  "status": "success"
}
```

Targeting preferences can be managed from the Services page or through this endpoint which allows you 
to turn targeting on and off for a specific serviceID and categoryID.

### Request Endpoint (Production Environment)
`PATCH https://pro-api.thumbtack.com/v3/accounts/{accountID}/services/{serviceID}/categories/{categoryID}/preferences/targeting`

`:accountID` is the identifier of your account. (<span style="color:Red">**NOTE:**</span>  you can pass `~` for now) <br />
`:serviceID` is the identifier of your business. <br />
`:categoryID` is the identifier of your category related to your business.

### Request Header
`Authorization: Bearer <access_token>`

`access_token` is the value that you received along with the `/token/access API`.

### Expected Request Body

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
enabled | boolean | Pause or turn on targeting. Value can be “true” or “false” | Y

### Error Codes:

#### 400 Bad Request
* Invalid ServiceID type
* Invalid CategoryID type
* Invalid body
* Missing body

#### 401 Unauthorized
* Invalid auth header
* Missing auth header
* Invalid ServiceID

#### 404 Not Found
* Missing ServiceID
* Missing CategoryID
* Invalid CategoryID

# Partner Endpoints

Partners will expose the following endpoints to Thumbtack.
All endpoints should be versioned to support future schema changes.
Endpoints will use HTTP basic authentication, and Partners will provide
Thumbtack with username and password for Thumbtack to call these endpoints.

## Leads

> Sample Request Body

```json
{
  "leadID": "299614694480093245",
  "createTimestamp": "1498760294",
  "request": {
    "requestID": "2999842694480093245",
    "category": "Interior Painting",
    "categoryID": "122681972262289742",
    "title": "Interior Painting",
    "description": "There is a stain on the door that needs to be touched up.",
    "schedule": "Date: Tue, May 05 2020\nTime: 6:00 PM\nLength: 3.5 hours",
    "location": {
      "address1": "101 Alma Street",
      "address2": "",
      "city": "Palo Alto",
      "state": "CA",
      "zipCode": "94301"
    },
    "travelPreferences": "Professional must travel to my address.",
    "details": [
      {
        "question": "Type of property",
        "answer": "Home"
      },
      {
        "question": "Number of rooms",
        "answer": "4 rooms"
      }
    ],
    "attachments": [
      {
        "fileName": "door.jpg",
        "fileSize": 1139,
        "mimeType": "image/jpeg",
        "url": "https://www.thumbtack.com/attachment/b180b7d3bf981dd2896732f979f44fbf45fc4224/door.jpg",
        "description": "my stain"
      }
    ]
  },
  "customer": {
    "customerID": "331138063184986319",
    "name": "John Davis",
    "phone": "1234567890"
  },
  "business": {
    "businessID": "286845156044809661",
    "name": "Tim's Painting Business"
  },
  "leadType": "INSTANT_BOOK",
  "leadPrice": null,
  "chargeState": null
}
```

> Sample Request (assuming above JSON was saved as data.json)

```shell
curl -X POST https://www.api.[partner].com/v1/lead
    -d data.json
    -H 'Content-Type:application/json'
    --user '<thumbtack_username>:<thumbtack_password>'
```

This endpoint will be called by Thumbtack to send leads to Partners.
Leads will be coming from the Thumbtack customer to the Partner's Pro (represented as a business on Thumbtack).

### HTTP Endpoint

`POST https://api.[partner].com/v1/lead`

*Note that the naming of this endpoint is flexible and dictated by the Partner.*

### Expected Request Body

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
leadID | string | ID of the lead | Y
createTimestamp | string | Unix timestamp (seconds) of when lead was created in UTC timezone | Y
request | object | JSON request object | Y
request.requestID | string | ID of the request | Y
request.category | string | Category of the request | Y
request.categoryID | string | Thumbtack ID of the request category | Y
request.title | string | Title of the request | Y
request.description | string | Description of the request | N
request.schedule\* | string | Details on when the customer wants to complete the job | N
request.location | object | JSON location object | Y
request.location.address1 | string | Street address for the following lead types INSTANT_BOOK, BOOKING, INSTANT_CONSULT, SERVICE_CALL | Y
request.location.address2 | string | Additional address info for the following lead types INSTANT_BOOK, BOOKING, INSTANT_CONSULT, SERVICE_CALL | Y
request.location.city | string | City for the request location | Y
request.location.state | string | State (two letter abbreviation) for the request location | Y
request.location.zipCode | string | Zip code (five digit) for the request location | Y
request.travelPreferences | string | Travel preferences of the customer | Y
request.details | array | Array of details about the request | N
request.details.[i].question | string | Request specific question about the job (see example below for clarification) | Y
request.details.[i].answer | string | Answer to the question (see example below for clarification) | Y
request.attachments | array | Array of JSON attachment objects included in the request | N
request.attachments.[i].fileName | string | Attachment file name | Y
request.attachments.[i].fileSize | number | Attachment size in bytes | Y
request.attachments.[i].mimeType | string | Attachment MIME type | Y
request.attachments.[i].url | string | URL to download attachment | Y
request.attachments.[i].description | string | User-provided description of the attachment | N
customer | object | JSON customer object | Y
customer.customerID | string | ID of the customer | Y
customer.name | string | Name of the customer | Y
customer.phone\*\* | string | Phone number of the customer if we have it | N
business | object | JSON business object | Y
business.businessID | string | ID of the business (pro) | Y
business.name | string | Business name | Y
price | string | Price estimate for the job | N
leadType | string | Type of lead that Thumbtack is sending | N
leadPrice | string | Estimated price that Pro will pay to Thumbtack | N
chargeState | string | leadPrice Charge state value | N 

\* There are a variety of possible schedule strings. The most common structure is as follows:

First line – `Date` or `Dates`. This could return a full date (e.g. "Fri Apr 10 2020") or just the
month and day (e.g. "Apr 10"). If multiple dates, then comma-separated (e.g. "Apr 10, Apr 11, Apr 12")

Second line – `Time` or `Times`. This could return an exact time (e.g. "3:00 PM"), one time range
(e.g. "Morning (9am - 12pm)"), or multiple time ranges (e.g. "Early Morning (before 9am), Morning
(9am - 12pm), Afternoon (12pm - 3pm), Late Afternoon (3pm - 6pm)").

Third line - `Length` e.g. "4 hours".

\*\* Some of our partners were onboarded before we added support for these fields. We do not 
send these fields to them without confirmation that they are ready to accept these fields. 
If you are one such partner and want to start getting these, please get in touch with us.

### Lead Type Values

Possible values for the `leadType` field are mentioned below:

Lead Types |
--------- |
AVAILABILITY |
CONTACT |
ESTIMATION | 
CALL |
BOOKING |
PHONE_CONSULTATION |
INSTANT_BOOK |
SPONSORED |
INSTANT_CONSULT |
SERVICE_CALL |
FULFILLMENT |
REQUEST_A_QUOTE |
MISMATCH_REQUEST_A_QUOTE |

### ChargeState Values

When `leadPrice` is null, you will get `chargeState` as null.

When `leadPrice` is non-null, you will get `chargeState` as `Charged`.


## Messages

> Sample Request Body

```json
{
  "leadID": "299614694480093245",
  "customerID": "331138063184986319",
  "businessID": "286845156044809661",
  "message": {
    "messageID": "8699842694484326245",
    "createTimestamp": "1498760294",
    "text": "Do you offer fridge cleaning or is that extra?",
    "attachments": [
      {
        "fileName": "fridge.jpg",
        "fileSize": 3940,
        "mimeType": "image/jpeg",
        "url": "https://www.thumbtack.com/attachment/b180b7d3bf981dd2896732f979f44fbf45fc4224/fridge.jpg",
        "description": "refrigerator"
      }
    ]
  }
}
```

> Sample Request (assuming above JSON was saved as data.json)

```shell
curl -X POST https://www.api.[partner].com/v1/message
    -d data.json
    -H 'Content-Type:application/json'
    --user '<thumbtack_username>:<thumbtack_password>'
```

This endpoint will be called by Thumbtack to send messages to Partners.
Messages will be coming from the Thumbtack customer to the Partner's Pro (represented as a business on Thumbtack).

Partners should return back 200 on successful processing of the messages.
Sometimes messages might be delivered for leads that were created before the
corresponding business was enabled in the API. In these cases, partners will not have seen
that lead come through ProAPI before, and they should return 202.

### HTTP Endpoint

`POST https://api.[partner].com/v1/message`

*Note that the naming of this endpoint is flexible and dictated by the Partner.*

### Expected Request Body

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
leadID | string | ID of the lead | Y
customerID | string | ID of the customer who sent the message | Y
businessID | string | ID of the business (pro) who is receiving the message | Y
message | object | JSON message object | Y
message.messageID | string | ID of the message | Y
message.createTimestamp | string | Unix timestamp (seconds) of when message was created in UTC timezone | Y
message.text | string | Text of the message | Y
message.attachments | array | Array of JSON attachment objects included in the message | N
message.attachments.[i].fileName | string | Attachment file name | Y
message.attachments.[i].fileSize | number | Attachment size in bytes | Y
message.attachments.[i].mimeType | string | Attachment MIME type | Y
message.attachments.[i].url | string | URL to download attachment | Y
message.attachments.[i].description | string | User-provided description of the attachment | N

## Update Lead

> Sample Request Body

```json
{
  "leadID": "465324000282984455",
  "leadPrice": "$26.00",
  "chargeState": "Charged",
}
```

> Sample Request (assuming above JSON was saved as data.json)

```shell
curl -X PUT https://www.api.[partner].com/v1/lead/update
    -d data.json
    -H 'Content-Type:application/json'
    --user '<thumbtack_username>:<thumbtack_password>'
```

This endpoint will be called by Thumbtack to update the lead payload to Partners. 
Currently, lead price is generated after generating the leads, so Thumbtack needs a new `updateLead` API for sending updated payload to any Partner. 

This update lead endpoint should be `PUT`.

### HTTP Endpoint

`PUT https://api.[partner].com/v1/lead/update`

*Note that the naming of this endpoint is flexible and dictated by the Partner.*

### Expected Request Body

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
leadID | string | ID of the lead | Y
leadPrice | string | Estimated price that Pro will pay to Thumbtack | N
chargeState | string | leadPrice Charge state value | N

### ChargeState Values

When `leadPrice` is null, you will get `chargeState` as null.

When `leadPrice` is non-null, you will get `chargeState` as `Charged`.

## Reviews

> Sample Request Body

```json
{
  "review": {
    "businessID": "286845156044809661",
    "categoryID": "299614694480093245",
    "createTime": "1517986598726",
    "leadID": "299614694480093245",
    "modifyTime": "1517986598726",
    "photos": [
      {
        "description": "test review photo",
        "fileName": "test_review_photo.jpg",
        "fileSize": 20,
        "mimeType": "image/jpeg",
        "url": "test.url.com/attachment/b180b7d3bf981dd2896732f979f44fbf45fc4224/test_review_photo.jpg"
      }
    ],
    "rating": "4",
    "reviewID": "318840849076158553",
    "reviewerNickname": "Nick Name",
    "text": "best service ever",
    "verified": true
  },
  "reviewEventType": "REVIEW_ADDED"
}
```

> Sample Request (assuming above JSON was saved as data.json)

```shell
curl -X POST https://www.api.[partner].com/v1/review
    -d data.json
    -H 'Content-Type:application/json'
    --user '<thumbtack_username>:<thumbtack_password>'
```

This endpoint will be called by Thumbtack to notify Partners of new reviews. Partners should return back 200 on successful processing of the request.

### HTTP Endpoint

`POST https://api.[partner].com/v1/review`

*Note that the naming of this endpoint is flexible and dictated by the Partner.*

### Expected Request Body

Parameter | Type    | Description                                                                                    | Required
--------- |---------|------------------------------------------------------------------------------------------------| -------
review | object  | JSON review object                                                                             | Y
review.reviewID | string  | ID of the review                                                                               | Y
review.businessID | string  | ID of the business (pro) who is receiving the review                                           | Y
review.leadID | string  | ID of the lead tied to the review. Only present if the review is verified                      | N
review.categoryID | string  | Thumbtack ID of the request category of the lead. Only present if the review is verified       | N
review.rating | string  | Review rating, from 1-5                                                                        | Y
review.text | string  | Review text. Only present if the review is verified                                            | N
review.reviewerName | string  | Name of reviewer. Only present if the review is verified                                       | N
review.createTime | string  | Epoch time of review creation                                                                  | Y
review.modifyTime | string  | Epoch time of last review modification                                                         | N
review.verified | boolean | This means that the review has a corresponding lead on Thumbtack                               | Y
review.photos | array   | Array of JSON attachment objects included in the review. Only present if the review is verified | N
review.photos.[i].fileName | string  | Attachment file name                                                                           | Y
review.photos.[i].fileSize | number  | Attachment size in bytes                                                                       | Y
review.photos.[i].mimeType | string  | Attachment MIME type                                                                           | Y
review.photos.[i].url | string  | URL to download attachment                                                                     | Y
review.photos.[i].description | string  | User-provided description of the attachment                                                    | N
reviewEventType | string  | The event type that triggered the webhook request | Y

### Review Event Types

Currently the only supported value for the `reviewEventType` field is `REVIEW_ADDED`. This event occurs when a new review is added.

### Verified Reviews

Reviews from Thumtback customers are labeled as "verified". This kind of review is tied to a leadID.

# Development Guide

Please follow the steps outlined in this section to integrate with Thumbtack's API.

*Note that in order to call Thumbtack's test endpoints, Partners will need test credentials (username + password).
Please <a href='#contact-us'>contact us</a> and we will get you set up with the appropriate test credentials.*

## Build Partner Endpoints

The first step is to build the endpoints outlined in the <a href='#partner-endpoints'>Partner Endpoints</a> section.

Thumbtack supports both a test and production environment, so we recommend Partners to build both test and production
endpoints as well. At a minimum, it is recommended that Partners test their endpoints by calling them (using `curl` or
any similar tool) and validating that the endpoints return a successful `HTTP 200` response.

The purpose of this is to ensure that the Partner endpoints can accept the payloads Thumbtack will provide.
Feel free to use the sample request bodies provided throughout this guide to simulate the payloads Thumbtack will provide.

## Create Test Data on Thumbtack

> Sample Response

```json
{
    "leadID": "380497493950742534",
    "createTimestamp": "1579643094",
    "price": "More information needed to give an estimate",
    "request": {
        "requestID": "380497492346044421",
        "category": "House Cleaning",
        "title": "House Cleaning",
        "description": "I am looking for someone to clean my apartment before I move",
        "schedule": "Date: Tue, May 05 2020\nTime: 6:00 PM\nLength: 3.5 hours",
        "location": {
            "city": "San Francisco",
            "state": "CA",
            "zipCode": "94103"
        },
        "travelPreferences": "Professional must travel to my address"
    },
    "customer": {
        "customerID": "380497491930800133",
        "name": "John Davis"
    },
    "business": {
        "businessID": "380497492389642246",
        "name": "Mr. Clean's Sparkly Cleaning Service"
    }
}
```

> Sample Request

```shell
curl -X POST https://staging-pro-api.thumbtack.com/v1/test/create-lead
    -H 'Content-Type:application/json'
    --user '<thumbtack_username>:<thumbtack_password>'
    -d '{
        "businessID": <business_id>
    }'
```

Once you have successfully created your endpoints, the next step is to test calling
<a href='#messages'>Thumbtack's messages endpoint.</a>.

In order to do this, you will need to provide a valid `businessID` and `leadID`. By valid, we mean the `businessID`
and `leadID` need to exist on Thumbtack. Thus, the Partner needs to create a dummy business and lead on Thumbtack's
test environment.

To do this, we have provided an endpoint Partners can call to generate this dummy data.

### HTTP Endpoint

`POST https://staging-pro-api.thumbtack.com/v1/test/create-lead`

Calling this endpoint will create a dummy business and lead on Thumbtack's test environment.
On the right, we've provided a sample request and response. Note that passing in the `Content-Type` header is required.

**Note**: For the sample request, the `businessID` field is optional (although if you have been provided a `businessID`,
it's recommended you provide it in the request. However, if you don't have a `businessID` you must provide an empty
data payload.

## Test Thumbtack Messaging Endpoint

Now that you've generated dummy data on Thumbtack's test environment, you should be able to test calling Thumbtack's
messages endpoint.

Take the `businessID` and `leadID` that were returned from calling `https://staging-pro-api.thumbtack.com/v1/test/create-lead`
and construct the appropriate Thumbtack messages endpoint.

In our case `businessID` is `380497492389642246` and `leadID` is `380497493950742534` so our Thumbtack messages endpoint
would look like - `https://api.thumbtack.com/v1/business/380497492389642246/lead/380497493950742534/message`.

However, **because we want to run this on our test environment**, we want to use the `staging-pro-api` host instead of
the `api` host. Thus, the endpoint we want to call would be `https://staging-pro-api.thumbtack.com/v1/business/380497492389642246/lead/380497493950742534/message`.

Call this endpoint as detailed in the <a href='#messages'>Thumbtack's messages endpoint</a> section, and validate
that you get a `success` response.

## Go To Production

At this point, the Partner's endpoints have been validated to accept Thumbtack payloads and the Partner has been
able to successfully call Thumbtack's endpoint to send a message. The next step is to move the integration to
Production.

To do this, the Partner needs to <a href='#contact-us'>contact Thumbtack</a> to set up Production credentials
for the Partner's integration as well as inform Thumbtack about the Partner's endpoints. A Thumbtack representative
will walk through the final steps to getting the Partner integrated onto the Thumbtack production environment.

Once a Partner has been fully set up on Production, Thumbtack will start pinging the Partner's endpoints
when a Partner receives both leads and messages from Thumbtack customers. The Partner will be expected to ping
Thumbtack's messages endpoint when they want to respond to a Thumbtack customer.

# Contact Us

If you have any questions, feel free to reach out to us at teampartnerships AT thumbtack DOT com
