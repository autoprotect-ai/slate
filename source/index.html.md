---
title: AutoComplete Partner API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - python

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>

includes:
  - response_codes
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation the AutoComplete Partner API
---

# Introduction

Welcome to the AutoComplete Partner API! Dealer partners can use this API to create zero-effort insurance-shopping experiences for their customers.

For each customer you submit, AutoComplete will generate and return a personalized insurance-shopping link (e.g. "https://app.autocomplete.io/aBcDeF"). When visiting that link, your customer will see his/her own contact, household, and vehicle details &mdash; in addition to your dealer profile &mdash; already pre-populated.

You can use this personalized shopping link in one of several ways:

1. Embed this link into your digital retail experience,
2. Send this link to your customer through your own CRM system or sales process, or
3. Have AutoComplete engage your customer directly.

We refer to options (1) and (2) as **Dealer-Initiated Messaging**, and option (3) as **AutoComplete-Initiated Messaging**.

## Example Use Case

> Sample submission to `POST /people`:

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
            "lease_duration": 36,
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
    ],
    "source": {
        "dealer_id": "4119799d-215f-4f49-b38a-0b94c2448399",
        "type": "cdk",
        "external_customer_number": "928880",
        "external_deal_number": "41706",
    }
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
    }
}
```

> Sample error:

```json
{
    "status_code": 400,
    "message": "Field 'new_vehicles' cannot be empty."
}
```

Let's say your customer, Jon Snow, wants to trade in an old Toyota Camry for a new Audi A4.

Once that deal is desked, you submit all of Jon's DMS (dealer management system) data to the AutoComplete API using the `POST /people` endpoint.

Using the information you just submitted plus data from third-party partners, AutoComplete creates a personalized shopping link for Jon containing his contact information, household drivers, new and existing vehicles, and prior coverages. The quality of this personalization is determined by the level of detail you submit, which means _you should submit as many available fields as possible_.

If you've configured your integration for **Dealer-Initiated Messaging**, AutoComplete simply returns Jon's personalized link (e.g. "https://app.autocomplete.io/aBcDeF") back to you. On the other hand, if you've configured your integration for **AutoComplete-Initiated Messaging**, AutoComplete will engage with Jon directly via his preferred contact method.

Finally, Jon visits his personalized link, where he confirms his personal information, compares quotes, and purchases a policy.

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

Requests are **rate limited** to one request per second.

# Endpoints

## `POST /people`

> Example with minimum required fields:

```python
import requests

api_key = # <Your API Key>
api_secret = # <Your API Secret>

headers = {'Content-Type': 'application/json'}

data = {
    "first_name": "Jon",
    "last_name": "Snow",
    "date_of_birth": "1986-12-26",
    "phone_numbers": [
        {
            "number": "+15551230000",
            "type": "mobile",
            "sms_consent": True
        }
    ],
    "addresses": [
        {
            "full_address": "555 Main St., Pleasantville, CA 94105"
        }
    ],
    "drivers_license": [
        {
            "number": "D1234567",
            "state": "CA"
        }
    ],
    "new_vehicles": [
        {
            "vin": "WAULH54B0YN028245"
        }
    ],
    "source": {
        "dealer_id": "4119799d-215f-4f49-b38a-0b94c2448399"
    }
}

r = requests.post('https://api.autocomplete.io/people', auth=(api_key, api_secret), headers=headers, json=data)

if r.status_code == 200:
    print(r.text)
```

Upload all customer information using this endpoint. _All fields are preferred, only some fields are required_.

### Minimum Required Fields

| **Parameter** | **Notes** |
| --- | --- |
| **first_name** |  |
| **last_name** |  |
| **date_of_birth** |  |
| **phone_numbers** | Must contain at least one valid phone number, with `sms_consent: true`, when configured for **AutoComplete-Initiated Messaging** |
| **addresses** | Must contain at least one valid address |
| **drivers_license** | Must include `number` and `state` |
| **new_vehicles** | Must contain at least one vehicle, with `vin` |
| **source** | Indicates the dealer of origin |

For all preferred fields, see [Models - Person](#person).

# Models

## Person

This is the model used to represent a customer.

| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| **first_name** | string |  |
| **middle_name** | string |  |
| **last_name** | string |  |
| **date_of_birth** | string | "YYYY-MM-DD" format |
| **gender** | string | Valid options: `male`, `female`, `non_binary` |
| **marital_status** | string | Valid options: `single`, `married`, `other` |
| **relationship** | string | Required when the **Person** is an element of an array, such as in the `.related_people` field. Valid options: `spouse`, `sibling`, `parent`, `parent_in_law`, `child`, `child_in_law`, `housemate`, `other` |
| **education** | string | Highest level of education achieved. Valid options: `associates`, `bachelors`, `doctorate`, `no_high_school_diploma`, `high_school`, `masters`, `medical_school`, `law_school`, `vocational_certificate`, `other` |
| **employment_status** | string | Valid options: `employed`, `retired`, `student`, `unemployed` |
| **residence_ownership_type** | string | Valid options: `own`, `rent` |
| **age_first_licensed** | integer |  |
| **phone_numbers** | Array<**[PhoneNumber](#phonenumber)**> |  |
| **emails** | Array<**[Email](#email)**> |  |
| **addresses** | Array<**[Address](#address)**> |  |
| **drivers_license** | <**[DriversLicense](#driverslicense)**> |  |
| **policies** | Array<**[Policy](#policy)**> |  |
| **new_vehicles** | Array<**[Vehicle](#vehicle)**> | Used only when submitting to `POST /people` |
| **trade_ins** | Array<**[Vehicle](#vehicle)**> | Used only when submitting to `POST /people` |
| **vehicles** | Array<**[Vehicle](#vehicle)**> | Never submitted. Only available when reading from `GET /people`. |
| **related_people** | Array<**Person**> |  |
| **flow** | <**[Flow](#flow)**> | Never submitted |
| **source** | <**[Source](#source)**> | Used only when submitting to `POST /people` |

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
| **type** | string | Valid options: `mobile`, `home`, `work` |
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
| **type** | string | Valid options: `mailing`, `physical`, `garaging`, `lienholder` |
| **address_1** | string | The first line of the address, e.g. "530 Howard St" |
| **address_2** | string | The second line of the address, e.g. "Unit 470" |
| **city** | string | e.g. "San Francisco" |
| **state** | string | Two-letter postal abbreviation, e.g. "CA" |
| **postal_code** | string | e.g. "94105" or "94105-0907" |
| **country** | string | The only supported country is "United States". Will default to "United States" |
| **full_address** | string | e.g. "530 Howard St, Unit 470, San Francisco, CA 94105-0907" |

## DriversLicense

> Example:

``` json
{
    "number": "D123456",
    "state": "CA",
    "status": "active",
    "issued_date": "2018-10-25",
    "expiration_date": "2028-10-25"
}
```

| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| **number** | string | Alphanumeric, e.g. "D1234567" |
| **state** | string | Two-letter postal abbreviation, e.g. "CA" |
| **status** | string | Valid options: `active`, `suspended`, `revoked`, `expired` |
| **issued_date** | string | "YYYY-MM-DD" format |
| **expiration_date** | string | "YYYY-MM-DD" format |

## Policy

> Example:

```json
{
    "type": "auto",
    "carrier": "Geico",
    "policy_number": "604-80-35-635",
    "effective_date": "2021-12-20",
    "expiration_date": "2022-05-20"
}
```

| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| **type** | string | Valid options: see **[Enums - Policy Type](#policy-type)** |
| **carrier** | string | Valid options: see **[Enums - Carrier](#carrier)** |
| **policy_number** | string | e.g. "6048035635" |
| **effective_date** | string | "YYYY-MM-DD" format |
| **expiration_date** | string | "YYYY-MM-DD" format |

## Vehicle

> Example:

```json
{
    "vin": "2T3RWRFV3KW021971",
    "year": 2019,
    "make": "Toyota",
    "model": "RAV4",
    "trim": "XLE",
    "ownership_start": "2019-08-21",
    "ownership_type": "leased",
    "lease_duration": 36,
    "finance_duration": null,
    "annual_mileage": 12000,
    "odometer": 48,
    "cost_new": 3999500,
    "primary_use": "work",
    "lienholder": "Toyota Financial Services",
    "anti_lock_brakes": true,
    "anti_theft_device": true,
    "rideshare": false,
    "addresses": [
        {
            "type": "garaging",
            "address_1": "530 Howard St",
            "address_2": "Ste 470",
            "city": "San Francisco",
            "state": "CA",
            "postal_code": "94105"
        },
        {
            "type": "lienholder",
            "address_1": "PO BOX 105386",
            "city": "Atlanta",
            "state": "GA",
            "postal_code": "30348-5386"
        }
    ]
}
```

| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| **vin** | string | 17 alphanumeric characters |
| **year** | integer | e.g. 2021 |
| **make** | string | Valid options: see **[Enums - Vehicle Make](#vehicle-make)** |
| **model** | string | e.g. "RAV4" |
| **trim** | string | e.g. "XLE" |
| **ownership_start** | string | "YYYY-MM-DD" format |
| **ownership_type** | string | Only available when reading from `GET /people`. Valid options: `owned`, `financed`, `leased`, `trade_in`, `other` |
| **lease_duration** | integer | Only applicable when `.type == "leased"`. In months |
| **finance_duration** | integer | Only applicable when `.type == "financed"`. In months |
| **annual_mileage** | integer | In miles |
| **odometer** | integer | In miles |
| **cost_new** | integer | In cents |
| **primary_use** | string | Valid options: `work`, `pleasure`, `school`, `business`, `agricultural` |
| **lienholder** | string | e.g. "Toyota Financial Services" |
| **anti_lock_brakes** | boolean |  |
| **anti_theft_device** | boolean |  |
| **rideshare** | boolean |  |
| **addresses** | Array<**[Address](#address)**> |  |

## Flow

> Example:

```json
{
    "flow_identifier": "aBcDeF",
    "flow_url": "https://apps.autocomplete.io/aBcDeF", 
}
```

The `flow_url` is the personalized insurance-shopping link for that customer.

| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| **flow_identifier** | string | e.g. "aBcDeF" |
| **flow_url** | string | e.g. "https://apps.autocomplete.io/aBcDeF" |

## Source

> Example describing a customer originating from CDK:

```json
{
    "dealer_id": "4119799d-215f-4f49-b38a-0b94c2448399", // AutoComplete-assigned
    "type": "cdk",
    "external_customer_number": "928880", // CDK refers to this field as <CustNo>
    "external_deal_number": "41706", // CDK refers to this field as <DealNo>
}
```

> Example describing a customer originating from DealerTrack:

```json
{
    "dealer_id": "88753dfc-5ede-49cd-92b4-7b63d235d16c", // AutoComplete-assigned
    "type": "dealertrack",
    "external_customer_number": "1028648", // DealerTrack refers to this field as <CustomerNumber>
    "external_deal_number": "6302", // DealerTrack refers to this field as <DealNumber>
}
```

Informs AutoComplete about the origin of customer information. This is usually a specific combination of dealer, DMS type, and customer number.

| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| **dealer_id** | string | AutoComplete's _internal_ UUID of the origin dealer, e.g. "4119799d-215f-4f49-b38a-0b94c2448399" |
| **type** | string | Valid options: `automate`, `cdk`, `dealertrack`, `reynolds` |
| **external_customer_number** | string | The customer identifier used by the DMS |
| **external_deal_number** | string | The deal identifier used by the DMS |

# Enums

When submitting, all values are case-insensitive. Spaces and underscores are interchangeable.

For example, `"american_family"`, `"american family"`, and `"AMERICAN FAMILY"` will all be accepted as a valid alternatives for `"American Family"`. However, responses returned from the API will always be formatted as shown.

## Carrier

```json
"AAA"
"Allstate"
"American Family"
"American National"
"Amica Mutual"
"Auto Owners"
"Bear River"
"Chubb"
"Country Financial"
"Encompass"
"Erie"
"Esurance"
"Farmers"
"Foremost"
"Geico"
"Hanover"
"Heritage"
"Hippo"
"Homesite"
"Kemper"
"Kentucky Farm Bureau"
"Liberty Mutual"
"Mercury"
"Metlife"
"National General"
"Nationwide"
"Progressive"
"Safeco"
"Shelter"
"State Auto"
"State Farm"
"Travelers"
"Universal"
"USAA"
```

## Policy Type

```json
"auto"
"boat"
"business_owners"
"combo"
"commercial_auto"
"commercial_umbrella"
"condo"
"earthquake"
"farmowner"
"fire"
"flood"
"homeowners"
"landlord"
"life"
"motorcycle"
"personal_articles"
"recreational_vehicle"
"renters"
"snowmobile"
"term_life"
"trailer"
"umbrella"
"universal_life"
"whole_life"
```

## Vehicle Make

```json
"Acura"
"Alfa Romeo"
"Aston Martin"
"Audi"
"BMW"
"Bentley"
"Bollinger"
"Bugatti"
"Buick"
"Cadillac"
"Canoo"
"Chevrolet"
"Chrysler"
"Dodge"
"Ferrari"
"Fiat"
"Fisker"
"Ford"
"GMC"
"Genesis"
"Honda"
"Hummer"
"Hyundai"
"Infiniti"
"Jaguar"
"Jeep"
"Karma"
"Kia"
"Lamborghini"
"Land Rover"
"Lexus"
"Lincoln"
"Lordstown"
"Lotus"
"Lucid"
"Maserati"
"Maybach"
"Mazda"
"McLaren"
"Mercedes"
"Mercury"
"Mini"
"Mitsubishi"
"Nikola"
"Nissan"
"Oldsmobile"
"Polestar"
"Pontiac"
"Porsche"
"Plymouth"
"Ram"
"Rivian"
"Rolls Royce"
"Saab"
"Saturn"
"Scion"
"Smart"
"Subaru"
"Suzuki"
"Tesla"
"Toyota"
"Volkswagen"
"Volvo"
```