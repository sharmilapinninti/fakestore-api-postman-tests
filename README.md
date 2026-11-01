# Online Store API Testing: Products Module (Postman + Newman)

API test automation for the **Products** module of the [Fake Store API](https://fakestoreapi.com), built with Postman and run from the command line with Newman. Tests are data-driven from a CSV file and produce an HTML report.

## Highlights

- 11 requests covering GET, POST, PUT, PATCH and DELETE
- Assertions for status codes, response structure, JSON schema, field values, sorting, limit and category filtering
- Data-driven testing: each CSV row runs the whole collection once (20 iterations)
- Newman CLI run with an HTML report (htmlextra)

**Latest run:** 20 iterations, 220 requests, 1000 assertions, 0 failed. See `reports/report.html` for the full results.

<!-- After adding a screenshot of the report summary to a "screenshots" folder, remove this comment line and the closing marker below:
![Newman report summary](screenshots/newman-summary.png)
-->

## Requests covered

| Request | Method | Endpoint |
|---|---|---|
| get all products | GET | `/products` |
| get single product | GET | `/products/{id}` |
| get limited products | GET | `/products?limit=` |
| get sorted products | GET | `/products?sort=` |
| get products with limit/sort | GET | `/products?limit=&sort=` |
| get all categories | GET | `/products/categories` |
| get specific category product | GET | `/products/category/{category}` |
| add product | POST | `/products` |
| update product | PUT | `/products/{id}` |
| partial update product | PATCH | `/products/{id}` |
| delete product | DELETE | `/products/{id}` |

## What is tested

- **Status codes:** 200 for reads, updates and deletes; 201 for create
- **Response structure:** array or object, not empty
- **Schema validation:** JSON schema checks for a single product and for lists
- **Field validation:** response values match the data sent (id, title, price, description, category, image)
- **Limit and sort:** number of items equals the limit; ids are in ascending or descending order
- **Category filter:** every returned product belongs to the requested category

## Project structure

```
.
├── collection/   Postman collection (exported JSON)
├── data/         Test data (CSV) and globals (base_url)
├── docs/         Technical design (Products module) and test cases
├── reports/      Newman HTML report
└── README.md
```

## How the data-driven setup works

- `data/products_data.csv` has one row per product, with the columns `id, title, price, description, category, image, rate, count`.
- A collection-level **pre-request script** reads each row with `pm.iterationData.get(...)` and stores the values in collection variables.
- Request URLs and bodies use those variables (`{{id}}`, `{{title}}`, `{{price}}`, and so on), and the test scripts compare the responses against them.
- `limit` and `sort` are fixed values set in the same script.
- `base_url` comes from `data/globals.json`.

## Run in Postman

1. Download `collection/ProductsModule.postman_collection.json`.
2. In Postman, click **Import** and select the file.
3. Add a global variable `base_url` with the value `https://fakestoreapi.com/`.
4. Open the collection and click **Run**. To run it with the CSV, select `data/products_data.csv` as the data file. Some Postman plans limit data file uploads, and the Newman route below has no such limit.

## Run with Newman

**Prerequisite:** [Node.js](https://nodejs.org) (LTS).

```bash
npm install -g newman newman-reporter-htmlextra
```

**Run the collection** from the project folder:

```bash
newman run collection/ProductsModule.postman_collection.json -d data/products_data.csv -g data/globals.json -r "cli,htmlextra" --reporter-htmlextra-export reports/report.html
```

The quotes around `cli,htmlextra` matter in Windows PowerShell, which would otherwise split the list at the comma.

Open `reports/report.html` in a browser to see the results.

## Notes

- Fake Store API is a mock service. POST, PUT and DELETE requests return realistic responses but do not change the stored data, which keeps the tests repeatable.
- The technical design document is based on course material from Pavan Online Trainings.

## Author

[sharmilapinninti](https://github.com/sharmilapinninti)
