---
title: Thumbtack API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='#'>Partner with Us!</a>

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

**Note:** Thumbtack **does not provide customer contact information (email or phone number)** in the lead.
In order for the Supply Partner to contact the Thumbtack customer, the communication needs to be a
message (rather than call) and the message needs to be delivered via API.

Your use of an API is subject to the <a href='#api-terms-of-use'>API Terms of Use</a>.

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

# Thumbtack Endpoints

## Messages

> Sample Request

```shell
curl https://api.thumbtack.com/v1/lead/123/message
  -H "Authorization: AUTH_HEADER"
  -d '{"text": "Hello John, how can I help you?"}'
```

> The above command returns JSON structured like this:

```json
{
    "status": "success"
}
```

Send messages on behalf of the Pro to a Thumbtack customer for a given lead.
This is an endpoint Thumbtack has created for Partners to call.

### HTTP Endpoint

`POST https://api.thumbtack.com/v1/business/:businessID/lead/:leadID/message`

`:businessID` is the identifier of your business.
`:leadID` is the identifier of the lead whose customer you wish to message.

### Expected Request Body

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
text | string | Text of the message | Y

# Partner Endpoints

Partners will expose the following endpoints to Thumbtack.
All endpoints should be versioned to support future schema changes.
Endpoints will use HTTP basic authentication, and partners will provide
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
    "location": {
      "city": "San Jose",
      "state": "CA",
      "zipCode": "95125",
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
      },
      ...
    ]
  },
  "customer": {
    "customerID": "331138063184986319",
    "name": "Jay J.",
  },
  "business": {
    "businessID": "286845156044809661",
    "name": "Tim's Painting Business",
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
business | object | JSON business object | Y
business.businessID | string | ID of the business (pro) | Y
business.name | string | Business name | Y


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


# Help

If you have any questions, feel free to reach out to us at
partnerproducts-eng AT thumbtack DOT com
