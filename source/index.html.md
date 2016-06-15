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

# Developer Sandbox

## Example Native Apps using SDK

Below are downloadable example apps that use our SDKs: 

* [iOS Example App](https://rink.hockeyapp.net/apps/94be274769854f85b557eb417f15fa1a)
* [Android Example App](https://rink.hockeyapp.net/apps/741e62bec0e0487eae3686e9b89f03a9)

To get access to source and to integrate the SDKs please contact us to join the private beta.

## Special request values

When making a request for loan offers there are special dollar amounts that will change the behavior of the loan applications response state when you apply for the offer.

Offer Request Amount | Loan Application State | Action(s)
-------------------- | ---------------------- | ---------
$500 | APPLICATION_REJECTED |
$1,000 | PENDING_LENDER_ACTION |
$1,500 | PENDING_BORROWER_ACTION | UPLOAD_PHOTO_ID
$2,000 | FINISH_APPLICATION_EXTERNAL |
$2100 | PENDING BORROWER_ACTION | UPLOAD_BANK_STATEMENT
$2200 | PENDING_BORROWER_ACTION | TEXT
$2300 | PENDING_BORROWER_ACTION | UPLOAD_PROOF_OF_ADDRESS
$2400 | PENDING_BORROWER_ACTION | UPLOAD_PROOF_OF_ADDRESS, UPLOAD_PHOTO_ID, UPLOAD_BANK_STATEMENT
$2,500 | APPLICATION_RECEIVED |
$2,600 | No offers. |
$2700 | Web application via redirect. |
$3,000 | ERROR |
Any other amount | PENDING_BORROWER_ACTION | AGREE_TERMS

# Authentication

There are two types of authentication within the Link API:

1. Developer authentication
2. User authentication (via the User endpoint)

This section explains how to authenticate as a Link developer in order to create users, return offers, and submit applications on the Link network.

Developer authentication is required to create a user and a developer key must be passed in the POST. Create user will return a token that is bound to that user and will be used as an Auth Bearer header for all subsequent user requests.

You can register for a developer key [here](https:link.ledge.me/register).

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

The Loan Purposes endpoint returns a list of valid loan purposes and should be called prior to sending an offer request to the ['requestOffers'](#request-offers) endpoint. In the default UI modules included with the Link SDK, the loan purpose selector is displayed on the same screen as loan amount.


## Housing Types List

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/config/housingTypes

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/config/housingTypes
 -H "Content-Type: application/json"
```

> <h3 class="toc-ignore">Response:</h3>

```json
{
    "type": "list",
    "data": [
        {
            "type": "housing_type",
            "housing_type_id": 1,
            "description": "Rent"
        },
        ...
    ],
    "page": 0,
    "has_more": false,
    "total_count": 4
}
```

The Housing Types endpoint returns a list of valid housing types and should be called prior to sending an offer request to the ['requestOffers'](#request-offers) endpoint. In the default UI modules included with the Link SDK, the housing type selector is displayed on the address screen.


## Employment Statuses List

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/config/employmentStatuses

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/config/employmentStatuses
 -H "Content-Type: application/json"
```

> <h3 class="toc-ignore">Response:</h3>

```json
{
    "type": "list",
    "data": [
        {
            "type": "employment_status",
            "employment_status_id": 1,
            "description": "Employed"
        },
        ...
        {
            "type": "employment_status",
            "employment_status_id": 0,
            "description": "Other"
        }
    ],
    "page": 0,
    "has_more": false,
    "total_count": 6
}
```

The Employment Statuses endpoint returns a list of valid employment statuses and should be called prior to sending an offer request to the ['requestOffers'](#request-offers) endpoint. In the default UI modules included with the Link SDK, the employment status selector is displayed on the same screen as income.


## Salary Frequencies List

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/config/salaryFrequencies

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/config/salaryFrequencies
 -H "Content-Type: application/json"
```

> <h3 class="toc-ignore">Response:</h3>

```json
{
    "type": "list",
    "data": [
        {
            "type": "salary_frequency",
            "salary_frequency_id": 1,
            "description": "Weekly"
        },
        ...
        {
            "type": "salary_frequency",
            "salary_frequency_id": 0,
            "description": "Other"
        }
    ],
    "page": 0,
    "has_more": false,
    "total_count": 6
}
```

The Salary Frequencies endpoint returns a list of valid salary frequencies and should be called prior to sending an offer request to the ['requestOffers'](#request-offers) endpoint. In the default UI modules included with the Link SDK, the salary frequency selector is displayed on the same screen as income.


## Integration disclaimer texts List

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/config/disclaimerTexts

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/config/disclaimerTexts
 -H "Content-Type: application/json"
 -d '{
    "public_developer_key": ""
}'
```

> <h3 class="toc-ignore">Response:</h3>

```json
{
    "type": "list",
    "data": [
        {
            "type": "integration_disclaimer",
            "integration_id": 1,
            "text": "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua"
        },
        {
            "type": "integration_disclaimer",
            "integration_id": 2,
            "text": "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua"
        }
    ],
    "page": 0,
    "has_more": false,
    "total_count": 2
}
```

Fetch the legal disclaimer text to be able to display loan offers.

### POST Parameters

Parameter | Description
--------- | -----------
public_developer_key*|str

## Link disclaimer text

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/config/linkDisclaimer

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/config/linkDisclaimer
 -H "Content-Type: application/json"
 -d '{
    "public_developer_key": "asdasdpiuqweoiquwe786123="
}'
```

> <h3 class="toc-ignore">Response:</h3>

```json
{
    "type": "link_disclaimer",
    "text": "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua"
}
```

Fetch the legal link disclaimer text to be able to start the flow.

### POST Parameters

Parameter | Description
--------- | -----------
public_developer_key*|str

#Users

## Create User

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/user/create

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/user/create
 -H "Content-Type: application/json"
 -d '{
    "first_name": "Michael",
    "last_name": "Bluth",
    "ssn": "123-45-6789",
    "phone_number": "+1 424-260-8561",
    "income": "100000.01",
    "birthdate": "07-04-1975",
    "email": "developer@ledge.me",
    "credit_range": "1",
    "apt": "#715",
    "state": "CA",
    "street": "2633 Lincoln Blvd",
    "city": "Santa Monica"
    "zip_code": "90405",
    "public_developer_key": "xo23uf3dn380c3nd38c"
    "salary_frequency": "1",
    "monthly_net_income": "4000.01",
    "housing_type": "1",
    "employment_status": "1",
    "salary_frequency": "1"
}'
```

> <h3 class="toc-ignore">Response:</h3>

```json
{
    "token": "example_user_token"
}
```

Create a user that can apply for offers and loans. In the default UI modules included with the Link SDK, this method is called when a user submits a request to get offers after filling out all required info. This method will support posting user data that has been passed into the SDK when a user launches and opt in to Link from within a partner application.

### POST Parameters

Parameter | Description
--------- | -----------
first_name*|User's legal First Name<br>Example: Michael
last_name*|User's legal Last Name<br>Example: Bluth
birthdate*|User's Birthday in format MM-DD-YYYY<br>Example: 07-04-1776
ssn*|User's 9 Digit Social Security Number. XXX-XX-XXXX<br>Example: 123-45-6789
email*|User's email address.<br>Example: developer@ledge.me
phone_number*|User's Phone number in E164 format. <br>Example: +1 424-260-8561
income*|User's annual pretax income expressed as a Numeric input truncated to two decimal places.<br>Example: 100000.001 would become 100000.00
monthly_net_income*|User's monthly after tax income expressed as a Numeric input truncated to two decimal places.<br>Example: 8000.001 would become 8000.00
credit_range*|1 = Excellent Credit (760+), 2 = Good Credit (700+), 3 = Fair Credit (640+), 4 = Poor Credit
street*|User’s mailing address (street + street number)<br>Example: 2633 Lincoln Blvd
apt|User's apartment or unit number<br>Example: #715
city*|User's city<br>Example: Santa Monica
state*|User's US State 2 letter abbreviation.<br>Example: CA
zip_code*|User's ZIP or postal code (5 or 9 digits)<br>Example: 90405
public_developer_key*|Developer's Link key.  This cannot be changed once it has been set.
housing_type*|Please see config housing types.
salary_frequency*|Please see config salary frequency.
employment_status*|Please see config employment status.

## Update User

> <h3 class="toc-ignore">Definition:</h3>

> POST https://link.ledge.me/user/update

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X POST https://link.ledge.me/user/update
 -H "Content-Type: application/json"
 -H "Authorization: Bearer {user_token}"
 -d '{
    "first_name": "Michael",
    "last_name": "Bluth",
    "ssn": "123-45-6789",
    "phone_number": "+1 424-260-8561",
    "income": "100000.01",
    "monthly_net_income": "4000.01",
    "birthdate": "07-04-1975",
    "email": "developer@ledge.me",
    "credit_range": "1",
    "apt": "#715",
    "state": "CA",
    "street": "2633 Lincoln Blvd",
    "city": "Santa Monica"
    "zip_code": "90405",
    "housing_type": "1",
    "employment_status": "1",
    "salary_frequency": "1"
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
    "monthly_net_income": 4000.0,
    "email": "john.smith@corp.com",
    "phone_number": "+12015555555",
    "address": {
        "type": "address",
        "street": "1310 Fillmore st",
        "apt": "123",
        "city": "San Francisco",
        "state": "CA",
        "zip_code": "94115"
    },
    "housing_type": {
        "type": "housing_type",
        "housing_type_id": 1,
        "description": "Rent"
    },
    "employment_status": {
        "type": "employment_status",
        "employment_status_id": 1,
        "description": "Employed"
    },
    "salary_frequency": {
        "type": "salary_frequency",
        "salary_frequency_id": 4,
        "description": "Monthly"
    }
}
```

Update current user's info. In the default UI modules included in the Link SDK, this method is called when a user submits updates to their profile info after previously registering an account. Please note that the developer ID associated with a user is set with the /user/create method and cannot subsequently be updated.

### POST Parameters

Parameter | Description
--------- | -----------
first_name* | User's legal First Name<br>Example: Michael
last_name* | User's legal Last Name<br>Example: Bluth
birthdate*|User's Birthday in format MM-DD-YYYY<br>Example: 07-04-1776
ssn|User's 9 Digit Social Security Number. XXX-XX-XXXX<br>Example: 123-45-6789
email*|User's email address.<br>Example: developer@ledge.me
phone_number*|User's Phone number in E164 format.<br>Example: +1 424-260-8561
income*|User's annual pretax income expressed as a Numeric input truncated to two decimal places.<br>Example: 100000.001 would become 100000.00
monthly_net_income*|User's monthly after tax income expressed as a Numeric input truncated to two decimal places.<br>Example: 8000.001 would become 8000.00
credit_range*|1 = Excellent Credit (760+), 2 = Good Credit (700+), 3 = Fair Credit (640+), 4 = Poor Credit
street*|User’s mailing address (street + street number)<br>Example: 2633 Lincoln Blvd
apt|User's apartment or unit number<br>Example: #715
city*|User's city<br>Example: Santa Monica
state*|User's US State 2 letter abbreviation.<br>Example: CA
zip_code*|User's ZIP or postal code (5 or 9 digits)<br>Example: 90405 or 90230-4124
housing_type*|Please see config housing types.
salary_frequency*|Please see config salary frequency.
employment_status*|Please see config employment status.

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
    "income": 60000.0,
    "monthly_net_income": 4000.0,
    "self_reported_credit_score_range": 3.0,
    "email": "john.smith@corp.com",
    "phone_number": "+12015555555",
    "address": {
        "type": "address",
        "street": "1310 Fillmore st",
        "apt": "123",
        "city": "San Francisco",
        "state": "CA",
        "zip_code": "94115"
    },
    "housing_type": {
        "type": "housing_type",
        "housing_type_id": 1,
        "description": "Rent"
    },
    "employment_status": {
        "type": "employment_status",
        "employment_status_id": 1,
        "description": "Employed"
    },
    "salary_frequency": {
        "type": "salary_frequency",
        "salary_frequency_id": 4,
        "description": "Monthly"
    }
}
```

Get current user's info. In the default UI modules included in the Link SDK, this method is called whenever user data is retrieved and/or displayed; for example, in the case that a user opts to edit their existing profile info, /user/getInfo is called to pre-fill previously submitted profile info. SSN is not be returned via this method.


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
    "loan_amount": "105.33",
    "loan_purpose_id": 2,
    "rows": 1,
    "currency": "USD"
    "loan_category_id": 1
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
                "id": 4171256618,
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
                "interest_rate": 6.0,
                "payment_count": 10,
                "term": {
                    "type": "term",
                    "duration": 10,
                    "unit": 2
                },
                "expiration_date": 1466159368,
                "application_method": "api",
                "application_code": null,
                "offer_details": "Valid until the end of this month"
            },
            {
                "type": "offer",
                "id": 1053266486,
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
                "interest_rate": 8.0,
                "payment_count": 10,
                "term": {
                    "type": "term",
                    "duration": 10,
                    "unit": 2
                },
                "expiration_date": 1466159368,
                "application_method": "web",
                "application_code": "fKE0NSOnub1jyW01xHAneuShIkUNPmHj25lbDK7s7TogUwHIlnCN7XMlT%2Flkd4Hv",
                "offer_details": "Valid until July"
            }
        ],
        "page": 0,
        "has_more": false,
        "total_count": 2
    },
    "offer_request_id": 1340009063
}
```

Submit a request to populate offers for a user and receive a list of pre-qualified offers.  The user should be prompted to consent to share their data with affiliate lenders and authorizing a soft credit pull (which will not affect their credit score) prior to requesting offers; this consent flow is enforced by default in the UI modules included with the Ledge SDK.  Posts to the method will return an array of offers with accompanying loan terms (subject to change when an loan application is submitted).

### POST Parameters

Parameter | Description
--------- | -----------
currency|Three digit currency string. "USD" is only supported currency. <i>(default=USD)</i>
loan_amount*|Numeric input truncated to two decimal places. 10.001 would become 10.00.
loan_purpose_id*|See Config Loan Purpose section.
loan_category_id*|Category of Loan. Only the value 1 is supported (consumer loan)
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
            "id": 2412921100,
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
            "interest_rate": 6.0,
            "payment_count": 10,
            "term": {
                "type": "term",
                "duration": 10,
                "unit": 2
            },
            "expiration_date": 1466159368,
            "application_method": "api",
            "application_code": null,
            "offer_details": "Valid until the end of this month"
        },
        {
            "type": "offer",
            "id": 224744800,
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
            "interest_rate": 8.0,
            "payment_count": 10,
            "term": {
                "type": "term",
                "duration": 10,
                "unit": 2
            },
            "expiration_date": 1466159368,
            "application_method": "web",
            "application_code": "5RTA%2FuFZyECdyQ8l8kdH8OSV7zzbbFSdgsrJiGduyqkn9Pb12x9zgnxdVvOGAz%2FI",
            "offer_details": "Valid until July"
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

## Apply to an offer (api method)

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
    "id": 3370661401,
    "status": "APPLICATION_RECEIVED",
    "create_time": 1465986568,
    "offer": {
        "type": "offer",
        "id": 2661274901,
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
        "interest_rate": 6.0,
        "payment_count": 10,
        "term": {
            "type": "term",
            "duration": 10,
            "unit": 2
        },
        "expiration_date": 1466159368,
        "application_method": "api",
        "application_code": null,
        "offer_details": "offer_details"
    },
    "status_message": "Avant is processing your application.",
    "required_actions": {
        "type": "list",
        "data": [],
        "page": 0,
        "has_more": false,
        "total_count": 0
    }
}
```

Create a loan application for a previously received offer. The user should be prompted to consent to a hard credit pull and formal underwriting decision prior to creating a loan application; the required consent and disclosures are displayed by default in the UI modules included with the Link SDK.

Application status and required actions can be tested by using the special dollar values described above in developer sandbox section.

Application status| Description
------------------|------------------
APPLICATION_RECEIVED|Your application was received and is being processed.
APPLICATION_REJECTED|Your application was rejected by the lender.
PENDING_LENDER_ACTION|Lender is processing your application.
PENDING_BORROWER_ACTION|Borrower must take further action to complete the application. Please review "required_actions" list.
LENDER_REJECTED|After reviewing applicaiton details the lender has rejected the application.
LOAN_APPROVED|Loan was approved.

Required actions list is populated with one or more of the following when loan is in PENDING_BORROWER_ACTION status.

Application required_actions|Description
------------------|------------------
UPLOAD_BANK_STATEMENT|Upload a bank statement.
UPLOAD_PHOTO_ID|Upload a goverment issued photo identification.
UPLOAD_PROOF_OF_ADDRESS|Upload a utility or telephone bill to prove your address.
AGREE_TERMS|Agree to terms and esign application.
FINISH_APPLICATION_EXTERNAL|Follow the url to finish the last steps of application on another site.
TEXT|Follow instructions in text, parse and url to make it clickable.

## Apply to an offer (web method)

> <h3 class="toc-ignore">Definition:</h3>

> GET https://link.ledge.me/application/external/{application_code}

> <h3 class="toc-ignore">Request:</h3>

```shell
curl -X GET https://link.ledge.me/application/external/{application_code}
 -H "Content-Type: application/json"
 -H "Authorization: Bearer {user_token}"
```

Create a loan application for a previously received offer. The user will be redirected to the lender's application page.

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
    "id": 712432881,
    "status": "APPLICATION_RECEIVED",
    "create_time": 1465986568,
    "offer": {
        "type": "offer",
        "id": 2144531476,
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
        "interest_rate": 6.0,
        "payment_count": 10,
        "term": {
            "type": "term",
            "duration": 10,
            "unit": 2
        },
        "expiration_date": 1466159368,
        "application_method": "api",
        "application_code": null,
        "offer_details": "offer_details"
    },
    "status_message": "Avant is processing your application.",
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
    "type": "list",
    "data": [
        {
            "type": "application",
            "id": 2658469844,
            "status": "PENDING_BORROWER_ACTION",
            "create_time": 1465986568,
            "offer": {
                "type": "offer",
                "id": 401511912,
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
                "interest_rate": 6.0,
                "payment_count": 10,
                "term": {
                    "type": "term",
                    "duration": 10,
                    "unit": 2
                },
                "expiration_date": 1466159368,
                "application_method": "api",
                "application_code": null,
                "offer_details": "offer_details"
            },
            "status_message": "Avant needs you to complete your application.",
            "required_actions": {
                "type": "list",
                "data": [
                    {
                        "type": "required_action",
                        "action": "UPLOAD_PHOTO_ID",
                        "message": "Avant needs you to upload a photo ID."
                    }
                ],
                "page": 0,
                "has_more": false,
                "total_count": 1
            }
        },
        {
            "type": "application",
            "id": 4264877714,
            "status": "APPLICATION_RECEIVED",
            "create_time": 1465986568,
            "offer": {
                "type": "offer",
                "id": 3503956131,
                "lender": {
                    "type": "lender",
                    "lender_name": "Credit Shop",
                    "small_image": "http://img.com/asd",
                    "large_image": "http://img.com/asdoiu",
                    "about": "Lorem Ipsum"
                },
                "currency": "USD",
                "loan_amount": 2500.01,
                "payment_amount": 264.53,
                "interest_rate": 6.0,
                "payment_count": 10,
                "term": {
                    "type": "term",
                    "duration": 10,
                    "unit": 2
                },
                "expiration_date": 1466159368,
                "application_method": "api",
                "application_code": null,
                "offer_details": "offer_details"
            },
            "status_message": "Credit Shop is processing your application.",
            "required_actions": {
                "type": "list",
                "data": [],
                "page": 0,
                "has_more": false,
                "total_count": 0
            }
        }
    ],
    "page": 0,
    "has_more": false,
    "total_count": 2
}
```

Retrieve a list of all previously submitted applications made by a user via a partner's Link instance.

### POST Parameters

Parameter | Description
--------- | -----------
page|0 indexed page of results. <i>(default=0)</i>
rows|Maximum number of applications returned per page. <i>(default=10)</i>
