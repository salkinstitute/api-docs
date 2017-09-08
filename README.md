## Table of Contents

- [Getting Started](#getting-started)
  - [Base URL](#base-url)
  - [Logging In](#logging-in)
  - [Making Authenticated Calls](#making-authenticated-calls)
- [GET](#get)
  - [People](#people)
      - [All Active People](#all-active-people)    
   - [Departments](#departments)
      - [All Active Departments](#all-active-departments)
   - [Funds](#funds)
      - [All Valid Funds](#all-valid-funds)

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
