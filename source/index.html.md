---
title: Ledge Link API Reference

language_tabs:
  - shell
  - python
  - java
  - objective-c
  - swift

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to Ledge Link! Here you will find information on how to integrate with and use the Link API. We've tried to make this documentation as straightforward as possible and provide clear example code, but please [drop us a line](mailto:developer@ledge.me) with any questions you may have. If you're planning to use our API in production, you should take a look at our [privacy policy](https://ledge.me/link/privacy).

Ledge Link makes it easy to connect your mobile app users with loans from our network of partners and generate revenue whenever a user receives a loan through your branded offer portal.  

Link is the first natively mobile loan affiliate network, enabling you to introduce loan discovery as a seamless extension of your existing mobile user experience.  Link also makes it easy for you to share existing data about your users in a complaint, opt-in fashion to dramatically streamline the loan discovery process.

As a developer, you can integrate Link via the REST API or through our SDKs (available for [Android](https://ledge.me/link/android) and [iOS](https://ledge.me/link/ios).  The Link SDKs include drop-in native modules that handle all data capture, input validation, and required disclosures for processing offers and/or applications.  These modules can be easily customized to match your brand, with global config values for modifying fonts, colors, and other stylistic elements. 

The Link API is architected around REST, and uses HTTP response codes to indicate status and errors. All responses come in standard JSON. The Link API is served over HTTPS to ensure data privacy; HTTP is not supported.

# Authentication

There are two types of authentication within the Link API:

1. Developer authentication
2. User authentication (via the User endpoint)

This section explains how to authenticate as a Link developer in order to create users, return offers, and submit applications on the Link network.

Developer authentication is required to access any of the Link API endpoints, and each endpoint requires a `developer_id` be passed as a POST parameter with all requests.

You can register for a developer ID [here](https:link.ledge.me/register).

#Users

## Create User

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/user/create

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/user/create
 -H "Content-Type: application/json"
 -d '{
    "state": "",
    "apt": "",
    "last_name": "",
    "developer_id": 1,
    "city": "",
    "first_name": "",
    "zip_code": "",
    "ssn": "",
    "income": "",
    "street": "",
    "birthdate": "",
    "phone_number": "",
    "email": ""
}'
```

> <h3 class="toc-ignore">Response</h3>

```json
{
    "token": "example_user_token"
}
```

### POST Parameters

Parameter | Description
--------- | -----------
first_name*|str
last_name*|str
birthdate*|Birthday in format MM-DD-YYYY
ssn*|9 Digit SSN. XXX-XX-XXXX
email*|str
phone_number*|Phone number in E164 format. ex. +1 333-444-5555
income*|Numeric input truncated to two decimal places. 10.001 would become 10.00.
street*|str
apt|str <i>(default=)</i>
city*|str
state*|US State 2 letter abbreviation.
zip_code*|str
developer_id*|int

#Config

## Loan Purposes List

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/config/loanPurposes

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/config/loanPurposes
 -H "Content-Type: application/json"
```

> <h3 class="toc-ignore">Response:</h3>

```json
{
    "type": "list",
    "data": [
        {
            "type": "loan_purpose",
            "loan_purpose_id": 1,
            "description": "Debt Consolidation"
        },
        {
            "type": "loan_purpose",
            "loan_purpose_id": 2,
            "description": "Home Improvement"
        },
        ...
    ],
    "page": 0,
    "has_more": false,
    "total_count": 17
}
```

#Offers

## Request Offers

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/offer/requestOffers

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/offer/requestOffers
 -H "Content-Type: application/json"
 -H "Authorization: Bearer {user_token}"
 -d '{
    "rows": 1,
    "loan_purpose_id": "",
    "currency": "",
    "loan_amount": ""
}'
```

> <h3 class="toc-ignore">Response:</h3>

```json
{
    "type": "offer_request",
    "offers": {
        "type": "list",
        "data": [
            {
                "type": "offer",
                "id": 3865552060,
                "lender": {
                    "type": "lender",
                    "lender_name": "Ninja Loans",
                    "small_image": null,
                    "large_image": null,
                    "about": null
                },
                "currency": "USD",
                "loan_amount": 2500.01,
                "payment_amount": 264.53,
                "interest_rate": 6,
                "payment_count": 10,
                "term": {
                    "type": "term",
                    "duration": 10,
                    "unit": 2
                },
                "expiration_date": 1457528805,
                "application_method": "api"
            },
            {
             ...
            }
        ],
        "page": 0,
        "has_more": false,
        "total_count": 2
    },
    "offer_request_id": 1427496866
}
```

### POST Parameters

Parameter | Description
--------- | -----------
currency|Three digit currency string. "USD" is currently the only supported currency. <i>(default=USD)</i>
loan_amount*|Numeric input truncated to two decimal places. 10.001 would become 10.00.
loan_purpose_id*|See Config Loan Purpose section.
rows|int <i>(default=10)</i>

## Get Offers List

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/offer/getOffers/{offer_request_id}

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/offer/getOffers/{offer_request_id}
 -H "Content-Type: application/json"
 -H "Authorization: Bearer {user_token}"
 -d '{
    "rows": 1,
    "page": 1
}'
```

> <h3 class="toc-ignore">Response:</h3>

```json
{
    "type": "list",
    "data": [
        {
            "type": "offer",
            "id": 124095277,
            "lender": {
                "type": "lender",
                "lender_name": "Ninja Loans",
                "small_image": null,
                "large_image": null,
                "about": null
            },
            "currency": "USD",
            "loan_amount": 2500.01,
            "payment_amount": 264.53,
            "interest_rate": 6,
            "payment_count": 10,
            "term": {
                "type": "term",
                "duration": 10,
                "unit": 2
            },
            "expiration_date": 1457528805,
            "application_method": "api"
        },
        {
         ...
        }
    ],
    "page": 0,
    "has_more": false,
    "total_count": 2
}
```

### POST Parameters

Parameter | Description
--------- | -----------
offer_request_id*|int
page|int <i>(default=0)</i>
rows|int <i>(default=10)</i>

#Applications

## Apply to an offer

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/application/create/{offer_id}

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/application/create/{offer_id}
 -H "Content-Type: application/json"
 -H "Authorization: Bearer {user_token}"
```

> <h3 class="toc-ignore">Response:</h3>

```json
{
    "type": "application",
    "id": 3171461728,
    "status": 1,
    "create_time": 1457356005.189656,
    "offer": {
        "type": "offer",
        "id": 2036168219,
        "lender": {
            "type": "lender",
            "lender_name": "Avant",
            "small_image": "http://img.com/asd",
            "large_image": "http://img.com/asdoiu",
            "about": "Lorem Ipsum"
        },
        "currency": "USD",
        "loan_amount": 2500.01,
        "payment_amount": 264.53,
        "interest_rate": 6,
        "payment_count": 10,
        "term": {
            "type": "term",
            "duration": 10,
            "unit": 2
        },
        "expiration_date": 1457528805,
        "application_method": "api"
    },
    "errors": {
        "type": "list",
        "data": [],
        "page": 0,
        "has_more": false,
        "total_count": 0
    },
    "required_actions": {
        "type": "list",
        "data": [],
        "page": 0,
        "has_more": false,
        "total_count": 0
    }
}
```

## Get Application status

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/application/status/{application_id}

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/application/status/{application_id}
 -H "Content-Type: application/json"
 -H "Authorization: Bearer {user_token}"
```

> <h3 class="toc-ignore">Response:</h3>

```json
{
    "type": "application",
    "id": 1550224233,
    "status": 1,
    "create_time": 1457356005.191949,
    "offer": {
        "type": "offer",
        "id": 3882603015,
        "lender": {
            "type": "lender",
            "lender_name": "Avant",
            "small_image": "http://img.com/asd",
            "large_image": "http://img.com/asdoiu",
            "about": "Lorem Ipsum"
        },
        "currency": "USD",
        "loan_amount": 2500.01,
        "payment_amount": 264.53,
        "interest_rate": 6,
        "payment_count": 10,
        "term": {
            "type": "term",
            "duration": 10,
            "unit": 2
        },
        "expiration_date": 1457528805,
        "application_method": "api"
    },
    "errors": {
        "type": "list",
        "data": [],
        "page": 0,
        "has_more": false,
        "total_count": 0
    },
    "required_actions": {
        "type": "list",
        "data": [],
        "page": 0,
        "has_more": false,
        "total_count": 0
    }
}
```

## Get Applications list

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/application/list

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/application/list
 -H "Content-Type: application/json"
 -H "Authorization: Bearer {user_token}"
 -d '{
    "rows": 1,
    "page": 1
}'
```

> <h3 class="toc-ignore">Response:</h3>

```json
{
    "type": "application",
    "id": 3504625771,
    "status": 1,
    "create_time": 1457356005.196281,
    "offer": {
        "type": "offer",
        "id": 3229200725,
        "lender": {
            "type": "lender",
            "lender_name": "Avant",
            "small_image": "http://img.com/asd",
            "large_image": "http://img.com/asdoiu",
            "about": "Lorem Ipsum"
        },
        "currency": "USD",
        "loan_amount": 2500.01,
        "payment_amount": 264.53,
        "interest_rate": 6,
        "payment_count": 10,
        "term": {
            "type": "term",
            "duration": 10,
            "unit": 2
        },
        "expiration_date": 1457528805,
        "application_method": "api"
    },
    "errors": {
        "type": "list",
        "data": [],
        "page": 0,
        "has_more": false,
        "total_count": 0
    },
    "required_actions": {
        "type": "list",
        "data": [],
        "page": 0,
        "has_more": false,
        "total_count": 0
    }
}
```

### POST Parameters

Parameter | Description
--------- | -----------
page|int <i>(default=0)</i>
rows|int <i>(default=10)</i>
