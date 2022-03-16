---
title: AutoComplete Partner API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - python
  - shell
  - ruby
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>

includes:
  - response_codes
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Kittn API
---

# Introduction

Welcome to the AutoComplete Partner API! Dealer partners can use this API to submit customer information into our database.

Once a customer is submitted, AutoComplete will generate and return a personalized verification/shopping link for that customer (e.g. "https://app.autocomplete.io/aBcDeF"). That personalized link will have the customer's contact, vehicle, and coverage details &mdash; in addition to your dealer profile &mdash; already pre-populated.

We support two types of car buyers:

* **Prospective Car Buyers** who are currently shopping for a car, who have expressed interest in a specific vehicle, or who have scheduled a sales appointment. For these customers, AutoComplete focuses driver's license and insurance verification.

* **Committed/Closed Car Buyers** who have either committed to buying a specific vehicle or have completed the purchase.

## Prospective Car Buyers

> An example submission for Arya Stark, who has expressed interest in a Ford F-150:

```json
{
    "first_name": "Arya",
    "last_name": "Stark",
    "phone_numbers": [
        {
            "number": "+15559870000",
            "type": "mobile",
            "sms_consent": true
        }
    ],
    "emails": [
        {
            "email": "arya@stark.net",
            "preferred": true
        }
    ],
    "vehicles_of_interest": [
        {
            "year": 2022,
            "make": "Ford",
            "model": "F-150"
        }
    ]
}
```

> Sample response:

```json
{
    "id": "f57c2a83-872e-4c67-836b-15d37729ca56",
    "first_name": "Arya",
    "last_name": "Stark",
    // Submitted fields truncated for brevity
    // ...
    "flow": {
        "flow_identifier": "aBcDeF",
        "flow_url": "https://app.autocomplete.io/aBcDeF", // Arya's personalized verification link
        "lifecycle": "verify"
    }
}
```

> Response from `GET /people/f57c2a83-872e-4c67-836b-15d37729ca56` after Arya completes the Verification Flow:

```json
{
    "id": "f57c2a83-872e-4c67-836b-15d37729ca56",
    "first_name": "Arya",
    "middle_name": "Minisa",
    "last_name": "Stark",
    "date_of_birth": "1997-04-15",
    "gender": "female",
    // TODO fill out the submitted fields
    "addresses": [
        {
            "type": "physical",
            "address_1": "123 Front Street",
            "city": "Dover",
            "state": "DE",
            "postal_code": "19901",
            "country": "United States"
        }
    ],
    "drivers_license": {
        "number": "D0001234",
        "state": "CA",
        "status": "active",
        "issued_date": "2013-04-15",
        "expiration_date": "2023-04-15"
    },
    "policies": [
        {
            "type": "auto",
            "carrier": "Liberty Mutual",
            "policy_number": "AB-12-CD-34",
            "effective_date": "2022-03-02",
            "expiration_date": "2022-09-02"
        }
    ],
    "flow": {
        "flow_identifier": "aBcDeF",
        "flow_url": "https://app.autocomplete.io/aBcDeF", // Arya's personalized verification link
        "lifecycle": "verify"
    }
}
```

The AutoComplete Verification Flow lets you easily collect legally required license and insurance information about your prospective buyers. This can help you streamline the sales appointment, test drive, and financing processes.

The AutoComplete Verification Flow collects and validates the customer's:

* Legal Name
* Address
* Date Of Birth
* License Status
* Insurance Company
* Insurance Coverage

Additionally, AutoComplete will help uninsured or underinsured customers obtain insurance before they even walk in to your store.

Below are two examples of how to initiate the AutoComplete Verification Flow:

### Dealer-Initiated Messaging

1. Arya Stark schedules an appointment with you, the dealer, to view a Ford F-150.
2. You submit Arya's basic contact information and vehicle of interest to the AutoComplete API using the `POST /people` endpoint.
3. AutoComplete returns to you a personalized verification link, such as `app.autocomplete.io/aBcDeF`.
4. You send that personalized verification link to Arya.
5. Arya visits her personalized link, where she submits her driver's license and insurance information.
6. You retrieve Arya's verified license and insurance data from the AutoComplete API using the `GET /people` endpoint.

### AutoComplete-Initiated Messaging

1. Arya Stark schedules an appointment with you, the dealer, to view a Ford F-150.
2. You submit Arya's basic contact information and vehicle of interest to the AutoComplete API using the `POST /people` endpoint.
3. AutoComplete messages Arya directly with her personalized verification link.
4. Arya visits her personalized link, where she submits her driver's license and insurance information.
5. AutoComplete submits Arya's verified license and insurance data directly into your CRM.

## Committed/Closed Car Buyers

> An example submission for Jon Snow, who has just traded in his old Toyota Camry for a new Audi A4:

```json
{
    "first_name": "Jon",
    "middle_name": "Aegon",
    "last_name": "Snow",
    "date_of_birth": "1986-12-26",
    "gender": "male",
    "marital_status": "married",
    "education": "bachelors",
    "employment_status": "employed",
    "residence_ownership_type": "own",
    "age_first_licensed": 16,
    "phone_numbers": [
        {
            "number": "+15551230000",
            "type": "mobile",
            "sms_consent": true
        },
        {
            "number": "+15551231111",
            "type": "home"
        }
    ],
    "emails": [
        {
            "email": "jon.snow@nightswatch.com",
            "preferred": true
        }
    ],
    "addresses": [
        {
            "type": "physical",
            "full_address": "555 Main St., Pleasantville, CA 94105"
        },
        {
            "type": "mailing",
            "address_1": "PO Box 100",
            "city": "San Francisco",
            "state": "CA",
            "postal_code": "94105",
            "country": "United States"
        }
    ],
    "drivers_license": {
        "number": "D1234567",
        "state": "CA",
        "status": "active",
        "issued_date": "2015-11-11",
        "expiration_date": "2025-11-11"
    },
    "new_vehicles": [
        {
            "vin": "WAULH54B0YN028245",
            "year": 2022,
            "make": "Audi",
            "model": "A4",
            "trim": "45 TFSI",
            "ownership_start": "2022-03-09",
            "ownership_type": "leased",
            "lease_term": 36,
            "annual_mileage": 12000,
            "odometer": 57,
            "cost_new": 3990000,
            "lienholder": "Toyota Financial Services",
            "primary_use": "pleasure",
            "rideshare": false,
            "addresses": [
                {
                    "type": "garaging",
                    "full_address": "555 Main St., Pleasantville, CA 94105"
                },
                {
                    "type": "lienholder",
                    "full_address": "1 Toyota Way, Plano, TX 75024"
                }
            ]
        }
    ],
    "trade_ins": [
        {
            "vin": "5TD2A23C645204309",
            "year": 2015,
            "make": "Toyota",
            "model": "Camry",
            "odometer": 78001
        }
    ],
    "policies": [
        {
            "type": "auto",
            "carrier": "Geico",
            "policy_number": "604-80-35-635",
            "effective_date": "2021-12-20",
            "expiration_date": "2022-05-20"
        }
    ],
    "related_people": [
        {
            "first_name": "Ygritte",
            "last_name": "Wildling",
            "date_of_birth": "1987-02-09",
            "marital_status": "married",
            "relationship": "spouse"
        }
    ]
}
```

> Sample response:

```json
{
    "id": "ec694d9a-a3f8-4a55-95ff-de97d1bc945a",
    "first_name": "Jon",
    "last_name": "Snow",
    // Submitted fields truncated for brevity
    //...
    "flow": {
        "flow_identifier": "uVwXyZ",
        "flow_url": "https://app.autocomplete.io/uVwXyZ", // Jon's personalized shopping link
        "lifecycle": "shop"
    }
}
```

The AutoComplete Shop Flow helps your committed or closed car buyer instantly compare insurance quotes across all our partner carriers. When you submit the buyer's information to the AutoComplete API, AutoComplete generates a personalized insurance-shopping link for that customer.

Using the information you submit in conjunction with third-party data sources, AutoComplete will pre-populate this personalized link with the buyer's contact information, household drivers, new and existing vehicles, and existing coverages. The quality of the personalization is determined by the level of detail you submit.

This means _you should submit as many of the allowed fields as possible_. Nevertheless, _every field is optional_. AutoComplete will collect any missing information directly from the buyer.

Below are two examples of how to initiate the AutoComplete Shop Flow:

### Dealer-Initiated Messaging

1. Jon Snow trades in an old Toyota Camry for a new Audi A4.
2. Upon desking, you submit all of Jon's DMS data to the AutoComplete API using the `POST /people` endpoint.
3. AutoComplete returns to you a personalized shopping link, such as `app.autocomplete.io/uVwXyZ`.
4. You send that personalized shopping link to Jon.
5. Jon visits his personalized link, where he confirms his personal information, compares quotes, and purchases a policy.

### AutoComplete-Initiated Messaging

1. Jon Snow trades in an old Toyota Camry for a new Audi A4.
2. Upon desking, you submit all of Jon's DMS data to the AutoComplete API using the `POST /people` endpoint.
3. AutoComplete messages Jon directly with his personalized shopping link.
4. Jon visits his personalized link, where he confirms his personal information, compares quotes, and purchases a policy.

# Technical Overview

> Example Request:

```python
import requests

api_key = # <Your API Key>
api_secret = # <Your API Secret>

headers = {'Content-Type': 'application/json'}

r = requests.get('https://api.autocomplete.io/people', auth=(api_key, api_secret), headers=headers)
```

The AutoComplete Partner API is served exclusively over **HTTPS**, requests are authenticated using **Basic Authentication** with your **API Key** and **API Secret**, and all request bodies and responses are **JSON-encoded**. We also use standard HTTP headers, verbs, and [response codes](#response-codes).

# Models

## Person

This is the primary model to represent all prospective or recent car buyers.

| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| **first_name** | string |  |
| **middle_name** | string |  |
| **last_name** | string |  |
| **date_of_birth** | string | "YYYY-MM-DD" format |
| **gender** | string | Valid options: "male", "female", "non_binary" |
| **marital_status** | string | Valid options: "single", "married", "other" |
| **relationship** | string | Required when the **Person** is an element of an array, such as in the `.related_people` field. Valid options: "self", "spouse", "sibling", "parent", "parent_in_law", "child", "child_in_law", "housemate", "other" |
| **education** | string | Highest level of education achieved. Valid options: "associates", "bachelors", "doctorate", "no_high_school_diploma", "high_school", "masters", "medical_school", "law_school", "vocational_certificate", "other" |
| **employment_status** | string | Valid options: "employed", "retired", "student", "unemployed" |
| **residence_ownership_type** | string | Valid options: "own", "rent" |
| **age_first_licensed** | integer |  |
| **phone_numbers** | Array<**[PhoneNumber](#phonenumber)**> |  |
| **emails** | Array<**[Email](#email)**> |  |
| **addresses** | Array<**[Address](#address)**> |  |
| **drivers_license** | <**DriversLicense**> |  |
| **policies** | Array<**Policy**> |  |
| **new_vehicles** | Array<**Vehicle**> | Used only when submitting newly purchased (including leased and financed) vehicles of recent car buyers |
| **trade_ins** | Array<**Vehicle**> | Used only when submitting trade-ins of recent car buyers |
| **vehicles_of_interest** | Array<**Vehicle**> | Used only when submitting vehicles-of-interest for prospective car buyers |
| **vehicles** | Array<**Vehicle**> | Returned from the `GET /people` endpoint containing the vehicles known to be owned by this **Person** |
| **related_people** | Array<**Person**> |  |
| **flow** | <**[Flow](#flow)**> |  |

## PhoneNumber

> Example:

```json
{
    "number": "+15551230000",
    "type": "mobile",
    "sms_consent": true
}
```

| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| **number** | string | Formatted as "+\[Country Code\]\[Number\]", e.g. "+15551230000" |
| **type** | string | Valid options: "mobile", "home", "work" |
| **sms_consent** | boolean |  |

## Email

> Example:

```json
{
    "email": "john.doe@gmail.com",
    "preferred": true
}
```

| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| **email**  <br> | string | e.g. "[john@gmail.com](mailto:john@gmail.com)" |
| **preferred** | boolean |  |

## Address

> Example of a parsed address:

```json
{
    "type": "mailing",
    "address_1": "1650 17th St NW",
    "address_2": "Suite 100",
    "city": "Washington",
    "state": "DC",
    "postal_code": "20500"
}
```

> Example of an unparsed address:

```json
{
    "type": "physical",
    "full_address": "931 Thomas Jefferson Parkway, Charlottesville, VA 22902"
}
```

Addresses can be submitted either fully parsed (i.e. broken down into `address_1`, `city`, `state`, etc. constituent parts) or as unparsed text using the `full_address` field. If both are submitted, the parsed fields will take precedence.

| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| **type** | string | Valid options: "mailing", "physical", "garaging", "lienholder" |
| **address_1** | string | The first line of the address, e.g. "530 Howard St" |
| **address_2** | string | The second line of the address, e.g. "Unit 470" |
| **city** | string | e.g. "San Francisco" |
| **state** | string | Two-letter postal abbreviation, e.g. "CA" |
| **postal_code** | string | e.g. "94105" or "94105-0907" |
| **country** | string | The only supported country is "United States". Will default to "United States" |
| **full_address** | string | e.g. "530 Howard St, Unit 470, San Francisco, CA 94105-0907" |

## Flow

> Example:

```json
{
    "flow_identifier": "aBcDeF",
    "flow_url": "https://apps.autocomplete.io/aBcDeF",
    "lifecycle": "verify"
}
```

This encapsulates information about the customer lifecycle. The `flow_url` is the personalized verify/shop link for that customer.

| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| **flow_identifier** | string | e.g. "aBcDeF" |
| **flow_url** | string | e.g. "https://apps.autocomplete.io/aBcDeF" |
| **lifecycle** | string | Valid options: "verify", "shop" |

# Enums

### What's a smaller title?

### Is this something?

## Carrier

`AAA`

`Allstate`

## Occupation

## Phone Number Type

## Policy Type

## Vehicle Make

## Vehicle Use

```json
["work", "pleasure", "school", "business", "agricultural"]
```

`work`, `pleasure`, `school`, `business`, `agricultural`

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
curl "http://example.com/api/kittens" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
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
curl "http://example.com/api/kittens/2" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
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

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2" \
  -X DELETE \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

