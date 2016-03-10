---
title: Ledge Link API Reference

language_tabs:
  - shell

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
    "zip_code": "",
    "credit_range": "",
    "first_name": "",
    "ssn": "",
    "phone_number": "",
    "income": "",
    "birthdate": "",
    "email": "",
    "developer_id": 1,
    "last_name": "",
    "apt": "",
    "state": "",
    "street": "",
    "city": ""
}'
```

> <h3 class="toc-ignore">Response:</h3>

```json
{
    "token": "example_user_token"
}
```

Create a user that can apply for offers and loans.  In the default UI modules included with the Link SDK, this method is called when a user submits a request to get offers after filling out all required info.  This method will support posting user data that has been passed into the SDK when a user launches and opt in to Link from within a partner application.

### POST Parameters

Parameter | Description
--------- | -----------
first_name*|User's legal First Name EX: Michael)
last_name*|User's legal Last Name EX: Bluth)
birthdate*|User's Birthday in format MM-DD-YYYY EX: 07-04-1776)
ssn*|User's 9 Digit Social Security Number. XXX-XX-XXXX EX: 123-45-6789)
email*|User's email address. EX: developer@ledge.me)
phone_number*|User's Phone number in E164 format. EX: +1 424-260-8561)
income*|User's annual pretax income expressed as a Numeric input truncated to two decimal places. EX: 100000.001 would become 100000.00
credit_range*|1 = Excellent Credit (760+), 2 = Good Credit (700+), 3 = Fair Credit (640+), 4 = Poor Credit
street*|User’s mailing address (street + street number) EX: 2633 Lincoln Blvd
apt|User's apartment or unit number EX: #715 <i>(default=)</i>
city*|User's city EX: Santa Monica
state*|User's US State 2 letter abbreviation. EX: CA
zip_code*|User's ZIP or postal code (5 or 9 digits) EX: 90405
developer_id*|Developer's Link ID.  This cannot be changed once it has been set.

## Update User

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/user/update

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/user/update
 -H "Content-Type: application/json"
 -H "Authorization: Bearer {user_token}"
 -d '{
    "zip_code": "",
    "credit_range": "",
    "first_name": "",
    "ssn": "",
    "phone_number": "",
    "income": "",
    "birthdate": "",
    "email": "",
    "last_name": "",
    "apt": "",
    "state": "",
    "street": "",
    "city": ""
}'
```

> <h3 class="toc-ignore">Response:</h3>

```json
{
    "type": "user",
    "first_name": "John",
    "last_name": "Smith",
    "birth_date": "02/14/1980",
    "income": "60000",
    "self_reported_credit_score_range": 3,
    "email": "john.smith@corp.com",
    "phone_number": "+12015555555",
    "address": {
        "type": "address",
        "street": "1310 Fillmore st",
        "apt": "123",
        "city": "San Francisco",
        "state": "CA",
        "zip_code": "94115"
    }
}
```

Update current user's info.  In the default UI modules included in the Link SDK, this method is called when a user submits updates to their profile info after previously registering an account.  Please note that the developer ID associated with a user is set with the /user/create method and cannot subsequently be updated.

### POST Parameters

Parameter | Description
--------- | -----------
first_name*|<ul><li>User's legal First Name</li><li>Example: Michael</li></ul>
last_name*|User's legal Last Name
Example: Bluth
birthdate*|User's Birthday in format MM-DD-YYYY
Example: 07-04-1776
ssn|User's 9 Digit Social Security Number. XXX-XX-XXXX
Example: 123-45-6789
email*|User's email address.
Example: developer@ledge.me
phone_number*|User's Phone number in E164 format.
EX: +1 424-260-8561
income*|User's annual pretax income expressed as a Numeric input truncated to two decimal places.
Example: 100000.001 would become 100000.00
credit_range*|1 = Excellent Credit (760+), 2 = Good Credit (700+), 3 = Fair Credit (640+), 4 = Poor Credit
street*|User’s mailing address (street + street number)
Example: 2633 Lincoln Blvd
apt|User's apartment or unit number
Example: #715 <i>(default=)</i>
city*|User's city
Example: Santa Monica
state*|User's US State 2 letter abbreviation.
Example: CA
zip_code*|User's ZIP or postal code (5 or 9 digits)
Example: 90405 or 90230-4124

## Get User Info

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/user/getInfo

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/user/getInfo
 -H "Content-Type: application/json"
 -H "Authorization: Bearer {user_token}"
```

> <h3 class="toc-ignore">Response:</h3>

```json
{
    "type": "user",
    "first_name": "John",
    "last_name": "Smith",
    "birth_date": "02/14/1980",
    "income": "60000",
    "self_reported_credit_score_range": 3,
    "email": "john.smith@corp.com",
    "phone_number": "+12015555555",
    "address": {
        "type": "address",
        "street": "1310 Fillmore st",
        "apt": "123",
        "city": "San Francisco",
        "state": "CA",
        "zip_code": "94115"
    }
}
```

Get current user's info.  In the default UI modules included in the Link SDK, this method is called whenever user data is retrieved and/or displayed; for example, in the case that a user opts to edit their existing profile info, /user/getInfo is called to pre-fill previously submitted profile info.  SSN is not be returned via this method.

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

The Loan Purposes endpoint returns a list of valid loan purposes and should be called prior to sending an offer request to the ['requestOffers'](#request-offers) endpoint.  In the default UI modules included with the Link SDK, the loan purpose selector is displayed on the same screen as loan amount.


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
    "loan_amount": "",
    "loan_purpose_id": "",
    "rows": 1,
    "currency": ""
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
                "id": 1998182190,
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
                "expiration_date": 1457716315,
                "application_method": "api"
            },
            {
                "type": "offer",
                "id": 314515208,
                "lender": {
                    "type": "lender",
                    "lender_name": "Ninja Loans",
                    "small_image": null,
                    "large_image": null,
                    "about": null
                },
                "currency": "USD",
                "loan_amount": 2500.01,
                "payment_amount": 281.24,
                "interest_rate": 8,
                "payment_count": 10,
                "term": {
                    "type": "term",
                    "duration": 10,
                    "unit": 2
                },
                "expiration_date": 1457716315,
                "application_method": "web"
            }
        ],
        "page": 0,
        "has_more": false,
        "total_count": 2
    },
    "offer_request_id": 103016972
}
```

Submit a request to populate offers for a user and receive a list of pre-qualified offers.  The user should be prompted to consent to share their data with affiliate lenders and authorizing a soft credit pull (which will not affect their credit score) prior to requesting offers; this consent flow is enforced by default in the UI modules included with the Ledge SDK.  Posts to the method will return an array of offers with accompanying loan terms (subject to change when an loan application is submitted).

### POST Parameters

Parameter | Description
--------- | -----------
currency|Three digit currency string. "USD" is currently the only supported currency. <i>(default=USD)</i>
loan_amount*|Loan amount you are requesting with maximum two decimal places. Currency specifier required.
loan_purpose_id*|See Config Loan Purpose section.
rows|Maximum number of offers returned. <i>(default=10)</i>

## Get Offers List

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/offer/getOffers/{offer_request_id}

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/offer/getOffers/{offer_request_id}
 -H "Content-Type: application/json"
 -H "Authorization: Bearer {user_token}"
 -d '{
    "page": 1,
    "rows": 1
}'
```

> <h3 class="toc-ignore">Response:</h3>

```json
{
    "type": "list",
    "data": [
        {
            "type": "offer",
            "id": 3643733953,
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
            "expiration_date": 1457716315,
            "application_method": "api"
        },
        {
            "type": "offer",
            "id": 1256860378,
            "lender": {
                "type": "lender",
                "lender_name": "Ninja Loans",
                "small_image": null,
                "large_image": null,
                "about": null
            },
            "currency": "USD",
            "loan_amount": 2500.01,
            "payment_amount": 281.24,
            "interest_rate": 8,
            "payment_count": 10,
            "term": {
                "type": "term",
                "duration": 10,
                "unit": 2
            },
            "expiration_date": 1457716315,
            "application_method": "web"
        }
    ],
    "page": 0,
    "has_more": false,
    "total_count": 2
}
```

Get offers based on a given {offer_request_id}

### POST Parameters

Parameter | Description
--------- | -----------
offer_request_id*|See Request Offers section.
page|0 indexed page of results. <i>(default=0)</i>
rows|Maximum number of rows per page. <i>(default=10)</i>

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
    "id": 2519897282,
    "status": 1,
    "create_time": 1457543515.148443,
    "offer": {
        "type": "offer",
        "id": 1739834508,
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
        "expiration_date": 1457716315,
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

Create a loan application for a previously received offer.  The user should be prompted to consent to a hard credit pull and formal underwriting decision prior to creating a loan application; the required consent and disclosures are displayed by default in the UI modules included with the Link SDK.


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
    "id": 735169377,
    "status": 1,
    "create_time": 1457543515.150377,
    "offer": {
        "type": "offer",
        "id": 1851589608,
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
        "expiration_date": 1457716315,
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

Retrieve the status, associated terms, and pending actions (I.E. signing loan documents, uploading ID, etc.) for a previously submitted application.


## Get Applications list

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/application/list

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/application/list
 -H "Content-Type: application/json"
 -H "Authorization: Bearer {user_token}"
 -d '{
    "page": 1,
    "rows": 1
}'
```

> <h3 class="toc-ignore">Response:</h3>

```json
{
    "type": "application",
    "id": 908245951,
    "status": 1,
    "create_time": 1457543515.153444,
    "offer": {
        "type": "offer",
        "id": 1911005941,
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
        "expiration_date": 1457716315,
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

Retrieve a list of all previously submitted applications made by a user via a partner's Link instance.

### POST Parameters

Parameter | Description
--------- | -----------
page|0 indexed page of results. <i>(default=0)</i>
rows|Maximum number of applications returned per page. <i>(default=10)</i>
