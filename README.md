# Nova Poshta API Postman Collection

This repository contains a Postman collection for the basic Nova Poshta API workflow:

1. Find a sender city.
2. Get sender warehouses.
3. Get sender counterparties.
4. Get sender contact persons.
5. Create a recipient.
6. Find a recipient city.
7. Get recipient warehouses.
8. Create an Internet Document, also known as a TTN.
9. Track the created TTN.

No application code is required. The whole task is done in Postman by sending JSON requests to the Nova Poshta API.

## Repository structure

```text
.
├── README.md
└── novaposhta-postman
    └── Nova Poshta API.postman_collection.json
```

## API endpoint

All requests are sent to the same endpoint:

```text
POST https://api.novaposhta.ua/v2.0/json/
```

Each request uses this general JSON format:

```json
{
  "apiKey": "{{NP_API_KEY}}",
  "modelName": "Address",
  "calledMethod": "getCities",
  "methodProperties": {}
}
```

Only `modelName`, `calledMethod`, and `methodProperties` change between requests.

## How to use the collection

1. Install Postman: <https://www.postman.com/>.
2. Create a Nova Poshta account: <https://my.novaposhta.ua/>.
3. Create an API key in your Nova Poshta account settings.
4. Open Postman and import:

   ```text
   novaposhta-postman/Nova Poshta API.postman_collection.json
   ```

5. Open the imported collection and go to the `Variables` tab.
6. Put your API key into the `Current value` of `NP_API_KEY`.
7. Do not put your real API key into `Initial value` and do not commit it.
8. Run the requests in order from `01` to `11`.

## Collection variables

The collection uses variables so that the workflow is easier to follow:

| Variable | Purpose |
| --- | --- |
| `NP_API_KEY` | Your Nova Poshta API key. Keep it local in Postman only. |
| `SENDER_CITY_NAME` | City name used to find the sender city. |
| `SENDER_CITY_REF` | Sender city `Ref` returned by `01 - Find Sender City`. |
| `SENDER_WAREHOUSE_REF` | Sender warehouse `Ref` returned by `02 - Get Sender Warehouses`. |
| `SENDER_REF` | Sender counterparty `Ref` returned by `03 - Get Sender Counterparties`. |
| `SENDER_CONTACT_REF` | Sender contact person `Ref` returned by `04 - Get Sender Contact Persons`. |
| `SENDER_PHONE` | Sender phone number in international format, for example `380XXXXXXXXX`. |
| `RECIPIENT_*` | Recipient name, phone, city, warehouse, counterparty, and contact variables. |
| `CARGO_WEIGHT` | Shipment weight. |
| `SEATS_AMOUNT` | Number of seats/parcels. |
| `CARGO_DESCRIPTION` | Cargo description. |
| `CARGO_COST` | Declared cargo cost. |
| `DOCUMENT_NUMBER` | TTN number returned by `10 - Create Internet Document`. |

Some request tests save the first returned `Ref` into collection variables automatically. Still verify the response manually and choose the correct city, warehouse, counterparty, or contact person before creating a real TTN.

## Workflow

### 1. Find sender city

Run `01 - Find Sender City`.

Check `data[]` in the response and verify the correct city. Copy its `Ref` into `SENDER_CITY_REF` if needed.

### 2. Get sender warehouses

Run `02 - Get Sender Warehouses`.

Choose the warehouse you want to ship from and put its `Ref` into `SENDER_WAREHOUSE_REF`.

### 3. Get sender data

Run:

- `03 - Get Sender Counterparties`
- `04 - Get Sender Contact Persons`

Use the returned values to set:

- `SENDER_REF`
- `SENDER_CONTACT_REF`
- `SENDER_PHONE`

These values depend on your Nova Poshta account and cannot be safely stored in a public repository.

### 4. Create recipient

Set recipient variables in Postman:

- `RECIPIENT_FIRST_NAME`
- `RECIPIENT_MIDDLE_NAME`
- `RECIPIENT_LAST_NAME`
- `RECIPIENT_PHONE`

Run `05 - Create Recipient`.

The response should return a recipient `Ref` and usually a contact person `Ref`. The collection test tries to save them into:

- `RECIPIENT_REF`
- `RECIPIENT_CONTACT_REF`

### 5. Find recipient city and warehouse

Run:

- `06 - Find Recipient City`
- `07 - Get Recipient Warehouses`

Verify and set:

- `RECIPIENT_CITY_REF`
- `RECIPIENT_WAREHOUSE_REF`

### 6. Create Internet Document / TTN

Before running `10 - Create Internet Document`, verify these variables:

- `SENDER_CITY_REF`
- `SENDER_WAREHOUSE_REF`
- `SENDER_REF`
- `SENDER_CONTACT_REF`
- `SENDER_PHONE`
- `RECIPIENT_CITY_REF`
- `RECIPIENT_WAREHOUSE_REF`
- `RECIPIENT_REF`
- `RECIPIENT_CONTACT_REF`
- `RECIPIENT_PHONE`
- `CARGO_WEIGHT`
- `SEATS_AMOUNT`
- `CARGO_DESCRIPTION`
- `CARGO_COST`

Run `10 - Create Internet Document`.

If the request is successful, Nova Poshta returns the TTN number as `IntDocNumber`. The collection test saves it into `DOCUMENT_NUMBER`.

### 7. Track the TTN

Run `11 - Track Internet Document`.

It uses:

- `DOCUMENT_NUMBER`
- `RECIPIENT_PHONE`

## Important: HTTP 200 is not enough

Nova Poshta can return HTTP `200 OK` even when the API operation failed. Always check the response body:

```json
{
  "success": true
}
```

means the operation worked.

```json
{
  "success": false,
  "errors": ["..."]
}
```

means the operation failed. Read `errors` to understand what needs to be fixed.

The collection includes Postman tests that explicitly check:

- HTTP status is `200`.
- Nova Poshta response field `success` is `true`.

## Safety checklist before publishing

Before pushing this repository to a public Git service, verify that the collection does not contain:

- real API keys;
- real customer phone numbers;
- real customer names;
- private account-specific sender data;
- exported Postman local environment files.

This repository intentionally stores `NP_API_KEY` as an empty collection variable. Put the real value only into Postman's local `Current value`.

## Official documentation

Nova Poshta API documentation:

<https://developers.novaposhta.ua/documentation>
