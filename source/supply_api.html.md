---
title: Thumbtack API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='#contact-us'>Partner with Us!</a>

includes:
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

Your use of our API is subject to Thumbtack's <a href='#api-terms-of-use'>API Terms of Use</a>.

# Authentication

> To authorize, use this code:

 ```shell
 # With shell, you can just pass the correct header with each request
 curl "api_endpoint_here"
    -H "Authorization: AUTH_HEADER"
```

> Make sure to replace `AUTH_HEADER` with your personal header.

Where applicable, APIs use HTTP basic authentication. Thumbtack will provide Partners with a username
and password. The basic header looks as follows:

`Authorization: Basic <encoding>`

`<encoding>` is the base64 encoding of the username followed by a colon, followed by password.

Thumbtack will provide Partners with two sets of credentials - one for Thumbtack's test environment and
one for Thumbtack's production environment.

Note that `curl` provides a way to pass in `<username>` and `<password>` like so:
`--user '<thumbtack_username>:<thumbtack_password>'`. This is an acceptable alternative to providing the credentials
as an Authorization header. Feel free to read up on
<a href='https://en.wikipedia.org/wiki/Basic_access_authentication'>Basic Auth</a> to understand the relationship
between username/password and the encoded Authorization header.

# Thumbtack Endpoints

Thumbtack will expose the following endpoints to Partners.
All endpoints should be versioned to support future schema changes.
Endpoints will use HTTP basic authentication, and Thumbtack will provide
Partners with username and password for Partners to call these endpoints.
Note that passing in the `Content-Type` header is required.

## Messages

> Sample Request

```shell
curl https://api.thumbtack.com/v1/lead/123/message
  -H "Authorization: AUTH_HEADER"
  -H 'Content-Type:application/json'
  -d '{"text": "Hello John, how can I help you?"}'
```

> The above command returns JSON structured like this:

```json
{
    "status": "success"
}
```

Send messages on behalf of the Pro to a Thumbtack customer for a given lead.
This is an endpoint Thumbtack has created for Partners to call. Thumbtack provides both a production
and test environment endpoint.

### HTTP Endpoint (Production Environment)

`POST https://api.thumbtack.com/v1/business/:businessID/lead/:leadID/message`

`:businessID` is the identifier of your business.
`:leadID` is the identifier of the lead whose customer you wish to message.

### HTTP Endpoint (Test Environment)

`POST https://staging-pro-api.thumbtack.com/v1/business/:businessID/lead/:leadID/message`

`:businessID` is the identifier of your business.
`:leadID` is the identifier of the lead whose customer you wish to message.

### Expected Request Body

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
text | string | Text of the message | Y

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
    "title": "Interior Painting",
    "description": "There is a stain on the door that needs to be touched up.",
    "schedule": "Date: Tue, May 05 2020\nTime: 6:00 PM\nLength: 3.5 hours",
    "location": {
      "city": "San Jose",
      "state": "CA",
      "zipCode": "95125"
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
    ]
  },
  "customer": {
    "customerID": "331138063184986319",
    "name": "John Davis"
    "email": "johndavis@gmail.com"
    "phone": "1234567890"
  },
  "business": {
    "businessID": "286845156044809661",
    "name": "Tim's Painting Business"
  }
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
price | string | Price estimate for the job | N
request | object | JSON request object | Y
request.requestID | string | ID of the request | Y
request.category | string | Category of the request | Y
request.title | string | Title of the request | Y
request.description | string | Description of the request | N
request.schedule\* | string | Details on when the customer wants to complete the job | N
request.location | object | JSON location object | Y
request.location.city | string | City for the request location | Y
request.location.state | string | State (two letter abbreviation) for the request location | Y
request.location.zipCode | string | Zip code (five digit) for the request location | Y
request.travelPreferences | string | Travel preferences of the customer | Y
request.details | array | Array of details about the request | N
request.details.[i].question | string | Request specific question about the job (see example below for clarification) | Y
request.details.[i].answer | string | Answer to the question (see example below for clarification) | Y
customer | object | JSON customer object | Y
customer.customerID | string | ID of the customer | Y
customer.name | string | Name of the customer | Y
customer.email\*\* | string | Email address of the customer | N
customer.phone\*\* | string | Phone number of the customer if we have it | N
business | object | JSON business object | Y
business.businessID | string | ID of the business (pro) | Y
business.name | string | Business name | Y

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
    "text": "Do you offer fridge cleaning or is that extra?"
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

If you have any questions, feel free to reach out to us at
partnerproducts-eng AT thumbtack DOT com
