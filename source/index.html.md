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

Thumbtack provides a set of APIs that expose the service and quote details for a pro.
The purpose of these APIs is to provide a mechanism for data integration with third-party CRM systems.

# Authentication

Where applicable, APIs use HTTP basic authentication. Thumbtack will provide Partners with a username
and password. The basic header looks as follows:

`Authorization: Basic <encoding>`

`<encoding>` is the base64 encoding of the username followed by a colon, followed by password.

> To authorize, use this code:

 ```shell
 # With shell, you can just pass the correct header with each request
 curl "api_endpoint_here"
    -H "Authorization: AUTH_HEADER"
```

> Make sure to replace `AUTH_HEADER` with your personal header.

# Messages

## Message a Thumbtack Customer

```shell
curl https://api.thumbtack.com/v1/lead/123/message
  -H "Authorization: AUTH_HEADER"
  -d '{"message": {"text": "Hello"}}'
```

> The above command returns JSON structured like this:

```json
{
    "status": "success"
}
```

Send messages on behalf of the Pro to a Thumbtack customer for a given lead.

### HTTP Request

`POST https://api.thumbtack.com/v1/lead/:leadID/message`

`:leadID` is the identifier of the lead whose customer you wish to message.

### Query Parameters

Parameter | Type | Description | Required
--------- | ---- | ----------- | --------
message | object | JSON message object | Y
message.text | string | Text of the message | Y

# Help

If you have any questions, feel free to reach out to us at
partnerproducts-eng AT thumbtack DOT com
