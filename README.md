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
   - [Tax](#tax)
   	-[Current Tax Rate](#current-tax-rate)
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

Endpoint: https://BASE_URL/api/people/active

```bash
curl -H "X-Auth-Token: f2KpRW7KeN9aPmjSZ" -H "X-User-Id: fbdpsNf4oHiX79vMJ" https://BASE_URL/api/people/active
```

#### Departments

##### All Active Departments

Endpoint: https://BASE_URL/api/departments/active

```bash
curl -H "X-Auth-Token: f2KpRW7KeN9aPmjSZ" -H "X-User-Id: fbdpsNf4oHiX79vMJ" https://BASE_URL/api/departments/active
```

#### Funds

##### All Valid Funds

Endpoint: https://BASE_URL/api/funds/all-valid-short

```bash
curl -H "X-Auth-Token: f2KpRW7KeN9aPmjSZ" -H "X-User-Id: fbdpsNf4oHiX79vMJ" https://BASE_URL/api/funds/all-valid-short
```

#### Orgs

##### All Orgs

Endpoint:  https://BASE_URL/api/orgs

```bash
curl -H "X-Auth-Token: f2KpRW7KeN9aPmjSZ" -H "X-User-Id: fbdpsNf4oHiX79vMJ" https://BASE_URL/api/orgs
```
#### Tax

##### Current Tax Rate

Endpoint:  https://BASE_URL/api/current-tax-rate

```bash
curl -H "X-Auth-Token: f2KpRW7KeN9aPmjSZ" -H "X-User-Id: fbdpsNf4oHiX79vMJ" https://BASE_URL/api/current-tax-rate
```

### POST

#### Recharges

##### Insert Recharge Items and Post to Accounting

Endpoint:  https://BASE_URL/api/recharges/insert-recharge-items-and-post

Use this method to post recharge items for a single recharge center and recharge period, i.e. 'FCCF' 'June 2017'.

Arguments:
```javascript
/*
	POST BODY requires array of JSON objects in this format:
	If an existing recharge period and set of items is found that has not already been 
	processed it will be overwritten. If the recharge period has already been processed 
	(in accounting) the POST will fail and return an error that the period has already 
	been processed.
*/
[ 
	{
		//varchar(20) 
		//One of 'FCCF' or 'BPHO' presently. 
		recharge_center: 'BPHO',
		//varchar(100)
		//One of 'Services','Products','Controlled Substances'
		recharge_type: 'Products',
		//varchar(20)
		//Optional, for external users. Their PO Number with Salk. '90709501'
		po_number: '90709501',
		//varchar(250)
		//Your application's single or concatenated identifier for this charge
		//In the case of PPMS it is/was: 'PPMS3-FCCF-20170701 REF#74346'
		associated_request: 'PPMS3-FCCF-20170701 REF#74346',
		//varchar(20)
		//'626075'
		//Valid fund_code, reference https://github.com/salkinstitute/api-docs#all-valid-funds
		fund_code: '626075',
		//varchar(20)
		//'GEL-B'
		//Valid dept_code, reference https://github.com/salkinstitute/api-docs#all-active-departments
		dept_code: 'GEL-B',
		//varchar(200)
		//Valid org https://github.com/salkinstitute/api-docs#all-orgs
		//For all internal, 'Salk'
		org: 'Salk',
		//varchar(200)
		//'Fred Flintstone','Barney Rubbel','Donald Drumpf'
		requestor_name: 'Fred Flintstone',
		//float
		//total amount before adding any additional taxes, shipping or external-rate amounts (ubi,idc).
		//will be the same as charge_amount for all internal.
		//'100.00' US Money Style, .00 required.
		base_amount: '100.00',
		//float
		//Optional, US Money Style, .00 required.
		shipping_amount: '19.22',
		//float
		//Optional, UBI (unrelated business income tax) Rate
		//Default values for dept_code seen in departments/api return
		//Interpreted as a percent to be multiplied by and added to base_rate, .00 required
		ubi_rate: '100.00',
		//float
		//Optional, UBI (unrelated business income tax) Amount
		//Resulting amount from (ubi_rate % 100) X base_rate, .00 required
		ubi_amount: '100.00',
		//float
		//Optional, IDC (indirect cost rate, same as overhead, or markup) Rate
		//'Interpreted as a percent to be multiplied by and added to base_rate, .00 required
		idc_rate: '50.00',
		//float
		//Optional, IDC (indirect cost rate, same as overhead, or markup) Amount
		//Resulting amount from (idc_rate % 100) X base_rate, .00 required
		idc_amount: '50.00',
		//float
		//Optional, Sales Tax Rate, pull this value from the api here:
		// reference https://github.com/salkinstitute/api-docs#current-tax-rate
		//Interpreted as a percent, .00 required
		sales_tax_rate: '7.75',
		//float 
		//Optional, Sales Tax Amount - ONLY APPLIED TO TANGIBLE GOODS - recharge_type = 'Products'
		//Resulting amount from (sales_tax_rate % 100) X (base_amount + ubi_amount + idc_amount), .00 required
		sales_tax_amount: '20.86',
		//bool
		//Optional,'true' lookup to org api for_profit value
		for_profit: true,
		//varchar(100)
		//Format of the monthly recharge period
		//Only one value should occur throughout the POST data.
		recharge_period: 'June 2017',
		//float
		//Total amount of base_rate + any other ubi,idc,sales,shipping costs.
		//'138.92'
		charge_amount:'290.08',
		//varchar(250)
		//Whatever you want to describe the charge 'Flyfood','Zeiss Microscope',etc
		//Should be similar to invoice line items description.
		charge_description: 'Zeiss Microscope',
		//float
		//Whenever your business process wants to indicate the charge occurred.
		//'2017/06/07'
		charge_date: '2017/12/31',
		//number
		//Optional, 1-10000
		charge_minutes: '360',
		//time
		//Optional, "17:00" 24 hour style
		charge_start_time: '17:00',
		//number
		//Optional, '17' not used in any calculation - more for a reporting aspect.
		charge_quantity: '17',
		//varchar(40)
		//Optional, '11-0032' reference to work done on/for Iacuc protocol.
		protocol_number: '11-0032' 

	},
	{...}
];
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
