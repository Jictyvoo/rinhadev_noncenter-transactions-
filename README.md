# Rinhadev - 2024/Q1

## What Needs to Be Done?

To participate, you need to develop an HTTP API with the following endpoints:

### Transactions

**Request**

`POST /clientes/[id]/transacoes`

```json
{
  "valor": 1000,
  "tipo": "c",
  "descricao": "description"
}
```

Where

- `[id]` (in the URL) should be an integer representing the client's identification.
- `valor` should be a positive integer representing cents (we won't work with fractions of cents). For example, $10 is
  1000 cents.
- `tipo` should be either `c` for credit or `d` for debit.
- `descricao` should be a string of 1 to 10 characters.

All fields are mandatory.

**Response**

`HTTP 200 OK`

```json
{
  "limite": 100000,
  "saldo": -9098
}
```

Where

- `limite` should be the client's registered limit.
- `saldo` should be the new balance after the transaction is completed.

*The HTTP status code for successful transaction requests must be 200!*

**Rules**
A debit transaction **must never** leave the client's balance less than their available limit. For example, a client
with a limit of 1000 ($10) should never have a balance less than -1000 ($-10). In this case, a balance of -1001 or less
means inconsistency in the Backend Rumble!

If a debit request would make the balance inconsistent, the API must return HTTP Status Code 422 without completing the
transaction! The response body in this case will not be tested, and you can choose how to represent it.

If the `[id]` attribute from the URL refers to a non-existing client identification, the API must return HTTP Status
Code 404. The response body in this case will not be tested, and you can choose how to represent it. If the API returns
something like HTTP 200 indicating that the client was not found in the response body or HTTP 204 with no body, I will
be extremely depressed, and the Rumble will be canceled forever.

## Statement

**Request**

`GET /clientes/[id]/extrato`

Where

- `[id]` (in the URL) should be an integer representing the client's identification.

**Response**

`HTTP 200 OK`

```json
{
  "saldo": {
    "total": -9098,
    "data_extrato": "2024-01-17T02:34:41.217753Z",
    "limite": 100000
  },
  "ultimas_transacoes": [
    {
      "valor": 10,
      "tipo": "c",
      "descricao": "description",
      "realizada_em": "2024-01-17T02:34:38.543030Z"
    },
    {
      "valor": 90000,
      "tipo": "d",
      "descricao": "description",
      "realizada_em": "2024-01-17T02:34:38.543030Z"
    }
  ]
}
```

Where

- `saldo`
    - `total` should be the current total balance of the client (not just the following transactions).
    - `data_extrato` should be the date/time of the statement query.
    - `limite` should be the client's registered limit.
- `ultimas_transacoes` is an ordered list by date/time of transactions in descending order containing up to the 10
  latest transactions with the following:
    - `valor` should be the transaction amount.
    - `tipo` should be `c` for credit and `d` for debit.
    - `descricao` should be the description provided during the transaction.
    - `realizada_em` should be the date/time of the transaction.

**Rules**
If the `[id]` attribute from the URL refers to a non-existing client identification, the API must return HTTP Status
Code 404. The response body in this case will not be tested, and you can choose how to represent it. You already know
what happens if your API returns something in the 2XX range, right? Appreciated.

## Initial Client Registration

To emphasize concurrency during testing, few clients should be registered and tested. Therefore, only five clients, with
the following IDs, limits, and initial balances, must be previously registered for testing – this is essential!

| id | limit    | initial balance |
|----|----------|-----------------|
| 1  | 100000   | 0               |
| 2  | 80000    | 0               |
| 3  | 1000000  | 0               |
| 4  | 10000000 | 0               |
| 5  | 500000   | 0               |

Note: Do not register a client with the ID 6 specifically because part of the test is to verify if the client with ID 6
really does not exist, and the API returns HTTP 404!

### Artifact, Process, and Delivery Deadline

To participate, simply make a pull request to this repository including a subdirectory in [participants](./participants)
with the following files:

- `docker-compose.yml` - a `docker-compose` interpretable file containing the declaration of services that compose
