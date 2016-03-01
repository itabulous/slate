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

The Link API is architected around REST, using standard HTTP verbs to communicate and HTTP response codes to indicate status and errors. All responses come in standard JSON. The Link API is served over HTTPS to ensure data privacy; HTTP is not supported. Every request must include your `developer_id`.

# Authentication

There are two types of authentication within the Link API:

1. Developer authentication
2. User authentication (via the User endpoint)

This section explains how to authenticate as a Link developer in order to create users, return offers, and submit applications on the Link network.

Developer authentication is required to access any of the Link API endpoints, and each endpoint requires a `developer_id` be passed as a POST parameter with all requests.

You can register for a developer ID [here](https:link.ledge.me/register).

#Users
### HTTP Request

`POST https://link.ledge.me/user/create`

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
apt|str
city*|str
state*|US State 2 letter abbreviation.
zip_code*|str
developer_id*|int

# Config

Ledge provides various endpoints to populate user input fields that contain a pre-defined list of options.  These config endpoints are already integrated into the drop-in modules included with the Link SDKs; however, if you are building your own custom UI, it is important to integrate the config endpoints appropriately.

## Available Loan Purposes

> To get the list of available loan purposes, use this code:

```shell
curl -X POST http://localhost:8080/config/loanPurposes
  -d '{"developer_id":"meowmeowmeow"}'
  -H "Content-Type: application/json"
  
# Make sure to replace `meowmeowmeow` with your developer id.
# The above command returns JSON structured like this:

{
  "loan_purposes": 
  [
    {
	  "loan_purpose_id": 1, 
	  "description": "Debt Consolidation"
    }, 
	{
	  "loan_purpose_id": 2, 
	  "description": "Home Improvement"
    }	
  ]
}
```

```swift
import LedgeLink

let manager = LedgeLink.defaultManager()
manager.initializeWithDeveloperId("meowmeowmeow", sandbox: false)
manager.offerPurposesList { result in
  switch result {
    case .Failure(let error):
	  // Your error handler here
      break
    case .Success(let purposes):
	  // Your data handler here
      break
    }
}

# Make sure to replace `meowmeowmeow` with your developer id.
# Your data handler will receive a list of objects of type LoanPurpose:

Property | Description
--------- | -----------
loanPurposeId | The loan purpose id
description | The loan purpose's text that should be shown to the user


```

The Loan Purposes endpoint returns a list of valid loan purposes and should be called prior to sending an offer request to the get_offers endpoint.  In the default interface included with the Link SDK, the loan purpose selector is displayed on the same screen as loan amount.

<aside class="notice">Only the loan purposes defined here will be accepted by the link api.</aside>

### HTTP Request

`POST https://link.ledge.me/config/loanPurposes`

#Offers

### HTTP Request

`POST https://link.ledge.me/offer/requestOffers`

### POST Parameters

Parameter | Description
--------- | -----------
loan_amount*|Numeric input truncated to two decimal places. 10.001 would become 10.00.
loan_purpose_id*| See CONFIG #TODO: link
credit_range*| 1 = Excellent Credit (760+), 2 = Good Credit (700+), 3 = Fair Credit (640+), 4 = Poor Credit
currency|Three digit currency string. "USD" is only supported currency. <i>(default=USD)</i>
rows|int <i>(default=10)</i>

### HTTP Request

`POST https://link.ledge.me/offer/getOffers/{offer_request_id}`

### POST Parameters

Parameter | Description
--------- | -----------
offer_request_id*|int
page|int <i>(default=0)</i>
rows|int <i>(default=10)</i>

#Applications

### HTTP Request

`POST https://link.ledge.me/loan/apply/{offer_id}`

### POST Parameters

Parameter | Description
--------- | -----------
offer_id*|int 
bank_name*|str 
account_holder_name*|str
account_number*|Any numeric input of digits. ex. 010101
routing_number*|Any numeric input of digits. ex. 010101

TO-DO

#*EXAMPLE STUFF*

# Kittn Authentication

> To authorize, use this code:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve
