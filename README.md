## Table of Contents

- [Getting Started](#getting-started)
  - [Base URL](#base-url)
  - [Logging In](#logging-in)
  - [Making Authenticated Calls](#making-authenticated-calls)
- [GET](#get)
  -  [People](#people)
      - [All Active People](#all-active-people)    
   - [Departments](#departments)
      - [All Active Departments](#all-active-departments)
   - [Funds](#funds)
      - [All Valid Funds](#all-valid-funds)
   - [Orgs](#orgs)
      - [All Orgs](#all-orgs)
 - [POST](#post)
    - [Recharges](#recharges)
      - [Insert Recharge Items and Post to Accounting](#insert-recharge-items-and-post-to-accounting)   

### Getting Started

#### Base URL

The notation `BASE_URL` shoud be either `saw.salk.edu` or `saw-test.salk.edu`

#### Logging In

A login will look something like

```bash
curl https://BASE_URL/api/login/ -d "username=test&password=password"
```
And the response will look like
```javascript
{ status: "success", data: {authToken: "f2KpRW7KeN9aPmjSZ", userId: fbdpsNf4oHiX79vMJ} }
```
If using another client be sure to specify the correct header (POST) and the content type `application/x-www-form-urlencoded` when logging in.

You'll need to save the `userId` and `token` on the client, for subsequent authenticated requests.

#### Making Authenticated Calls

All endpoints require authentication, you must include the `userId` and
`authToken` (received from logging in above) with each request under the following headers:
- X-User-Id
- X-Auth-Token

```bash
curl -H "X-Auth-Token: f2KpRW7KeN9aPmjSZ" -H "X-User-Id: fbdpsNf4oHiX79vMJ" https://BASE_URL/api/people/active
```

### GET

#### People

##### All Active People

```bash
curl -H "X-Auth-Token: f2KpRW7KeN9aPmjSZ" -H "X-User-Id: fbdpsNf4oHiX79vMJ" https://BASE_URL/api/people/active
```

#### Departments

##### All Active Departments

```bash
curl -H "X-Auth-Token: f2KpRW7KeN9aPmjSZ" -H "X-User-Id: fbdpsNf4oHiX79vMJ" https://BASE_URL/api/departments/active
```

#### Funds

##### All Valid Funds

```bash
curl -H "X-Auth-Token: f2KpRW7KeN9aPmjSZ" -H "X-User-Id: fbdpsNf4oHiX79vMJ" https://BASE_URL/api/funds/all-valid-short
```

#### Orgs

##### All Orgs

```bash
curl -H "X-Auth-Token: f2KpRW7KeN9aPmjSZ" -H "X-User-Id: fbdpsNf4oHiX79vMJ" https://BASE_URL/api/orgs
```
### POST

#### Recharges

##### Insert Recharge Items and Post to Accounting

Use this method to post recharge items for a single recharge center and recharge period, i.e. 'FCCF' 'June 2017'.

Arguments:
```javascript
//Takes an array of json objects.
//Property required unless 'Maybe' then optional in that format.
[{
	//Can be one of FCCF,BPHO currently
	recharge_center: checkRechargeCenters,
	recharge_type: Match.Maybe(String),
	po_number: Match.Maybe(String),
	associated_request: String,
	fund_code: String,
	dept_code: String,
	org: String,
	requestor_name: String,
	base_amount: String,
	shipping_amount: Match.Maybe(String),
	ubi_rate: Match.Maybe(String),
	ubi_amount: Match.Maybe(String),
	idc_rate: Match.Maybe(String),
	idc_amount: Match.Maybe(String),
	sales_tax_rate: Match.Maybe(String),
	sales_tax_amount: Match.Maybe(String),
	for_profit: Match.Maybe(Boolean),
	//JUNE 2017
	recharge_period: checkRechargePeriod,
	//This is the total after adding all supplemental charges (tax,rates,etc)
	charge_amount:String,
	charge_description: String,
	charge_date: String,
	charge_minutes: Match.Maybe(String),
	charge_start_time: Match.Maybe(String),
	charge_quantity: Match.Maybe(Number)
}]
```
Example:
```
curl -X POST --header "Content-Type:*/*;charset=UTF-8" --data @sample_data/recharges-insert-api-sample.json https://BASE_URL/api/recharges/insert-recharge-items-and-post

```
Response:
```json
{
  "status": "success",
  "data": {
    "recharge_id": "QeEZym8qcrczD54Yy",
    "records_inserted": 3
  }
}
```

See the sample-data files in this repo.
