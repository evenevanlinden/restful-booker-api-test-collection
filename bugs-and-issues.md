# Restful Booker: Bugs and Issues Found

---

## 1. Date Fields Validation Issues

**Problems:**

- Both JSON and XML requests with empty or invalid date values (e.g., `"checkout": "2019-81-01"`, `"checkout": "never"`, or empty strings) result in the API responding with `"0NaN-aN-aN"` instead of proper error or null/empty values.
- This invalid date string causes client parsing failures.

**Environment:**

- API: [https://restful-booker.herokuapp.com/](https://restful-booker.herokuapp.com/)
- Endpoint: POST /booking (also PUT, PATCH)
- Content-Types: application/json, text/xml
- Date: 2025-06-05

**Expected Behavior:**

- Reject invalid or empty mandatory dates with HTTP 400 error, or
- Accept and consistently return null/empty values.

**Actual Behavior:**

- HTTP 200 OK with `"0NaN-aN-aN"` in date fields.

**Suggested Fix:**

- Implement strict validation rejecting invalid/empty dates with HTTP 400, or
- Allow null/empty values consistently.

---

## 2. Numeric Fields Validation Issues

**Problems:**

- XML requests with empty or invalid numeric fields (e.g., `<totalprice></totalprice>`, `<totalprice>true</totalprice>`, `<totalprice>High</totalprice>`) return `"NaN"` instead of an error or valid value.
- JSON requests with invalid `totalprice` data (e.g., `"totalprice": "invalid-string"`) return `"totalprice": null` instead of an error.

**Environment:**

- API: [https://restful-booker.herokuapp.com/](https://restful-booker.herokuapp.com/)
- Endpoint: POST /booking (also PUT, PATCH)
- Content-Types: text/xml, application/json
- Dates: 2025-06-05, 2025-06-12

**Expected Behavior:**

- Reject invalid/empty numeric fields with HTTP 400 error, or
- Accept and properly handle null/empty values.

**Actual Behavior:**

- HTTP 200 OK with invalid `"NaN"` or `null` values returned.

**Suggested Fix:**

- Enforce strict numeric validation and reject invalid inputs with HTTP 400.

---

## 3. Boolean Field Parsing Issues (`depositpaid`)

**Problems:**

- XML: Empty `<depositpaid></depositpaid>` returns `false` silently. Non-boolean values (e.g., `<depositpaid>123</depositpaid>`, `<depositpaid>"no"</depositpaid>`) are interpreted as `true`.
- JSON: Numeric values like `0` are interpreted as `false`, any non-zero number (e.g., `1`, `-99`,`4,7`) as `true`. Strings like `"false"` are accepted but converted to `true`.
- Invalid or ambiguous boolean values are accepted and coerced rather than rejected.

**Environment:**

- API: [https://restful-booker.herokuapp.com/](https://restful-booker.herokuapp.com/)
- Endpoint: POST /booking (also PUT, PATCH)
- Content-Types: application/json, text/xml
- Dates: 2025-06-05, 2025-06-12

**Expected Behavior:**

- Accept only proper boolean types (`true`/`false`).
- Reject invalid types (numbers, strings) with HTTP 400 error.
- Handle empty booleans consistently (error or default).

**Actual Behavior:**

- Invalid types are accepted and coerced, leading to inconsistent responses.

**Suggested Fix:**

- Implement strict type validation for boolean fields and reject invalid inputs with HTTP 400.

---

## 4. Empty String Fields Handling (XML)

**Problem:**

- Empty string fields (`firstname`, `lastname`, `additionalneeds`) in XML requests return empty tags in responses rather than error, null, or omission, causing inconsistent client-side handling.

**Environment:**

- API: [https://restful-booker.herokuapp.com/](https://restful-booker.herokuapp.com/)
- Endpoint: POST /booking (also PUT, PATCH)
- Content-Type: text/xml
- Date: 2025-06-05

**Expected Behavior:**

- Return HTTP 400 for empty mandatory fields, or
- Omit empty fields or return explicit null/empty string.

**Actual Behavior:**

- Empty XML tags are returned, causing confusion.

**Suggested Fix:**

- Standardize empty string field handling in XML responses.

---

## 5. Content-Type and Parsing Issues with `application/x-www-form-urlencoded`

**Problems:**

- POST /booking requests with `application/x-www-form-urlencoded` content type fail with a server error message `"formurlencoded is not a function"`.
- Response `Content-Type` header is incorrectly set to `text/html; charset=utf-8` instead of a relevant type.
- Response body contains runtime error, making requests unusable.

**Environment:**

- API: [https://restful-booker.herokuapp.com/](https://restful-booker.herokuapp.com/)
- Endpoint: POST /booking (also PUT, PATCH)
- Content-Type: application/x-www-form-urlencoded
- Date: 2025-06-05 and 2025-06-12

**Expected Behavior:**

- Properly parse and respond to URL-encoded requests without errors.
- Response `Content-Type` should match response data format.

**Actual Behavior:**

- HTTP 200 OK with error message `"formurlencoded is not a function"`.
- Incorrect response `Content-Type` header.

**Suggested Fix:**

- Add proper URL-encoded parsing middleware (e.g., `express.urlencoded({ extended: true })`).
- Fix response `Content-Type` headers.
- Gracefully handle or reject invalid content types.

---

## 6. Filtering Bookings by Exact Dates Fails

**Problem:**

- Filtering bookings via GET /booking with exact `checkin` and `checkout` dates does not return expected results unless the date range is expanded by one day on both ends, causing inaccurate filtering.

**Environment:**

- API: [https://restful-booker.herokuapp.com/](https://restful-booker.herokuapp.com/)
- Endpoint: GET /booking (with `checkin` and `checkout` query params)
- Date: 2025-06-05

**Steps to Reproduce:**

- Query with exact dates excludes matching bookings.
- Query with expanded date range includes expected bookings.

**Expected Behavior:**

- Exact date filtering should return bookings matching those dates precisely.

**Actual Behavior:**

- Filter applies an inclusive offset, causing confusion and inefficiency.

**Suggested Fix:**

- Adjust filter logic to treat dates as exact matches or clearly document behavior.

---

## 7. Authentication Endpoint Returns 200 Instead of 401 on Bad Credentials

**Problem:**

- `POST /auth` with invalid credentials returns HTTP 200 OK with body `{"reason":"Bad credentials"}` instead of HTTP 401 Unauthorized.

**Environment:**

- API: [https://restful-booker.herokuapp.com/](https://restful-booker.herokuapp.com/)
- Endpoint: POST /auth
- Date: 2025-06-08

**Expected Behavior:**

- Return HTTP 401 Unauthorized with error message on authentication failure.

**Actual Behavior:**

- Returns HTTP 200 OK, potentially confusing clients.

**Suggested Fix:**

- Update to return HTTP 401 Unauthorized status on bad credentials.

---

## 8. Response Code Inconsistencies Across Endpoints

**Problems:**

- **DELETE /booking/:id** and **GET /ping** respond with HTTP 201 Created instead of 200 OK for success.
- Deleting or updating non-existent bookings returns 405 Method Not Allowed instead of 404 Not Found.
- Creating bookings with invalid/missing fields returns 500 Internal Server Error instead of 400 Bad Request.
- Responses lack meaningful error messages.

**Environment:**

- API: [https://restful-booker.herokuapp.com/](https://restful-booker.herokuapp.com/)
- Various endpoints
- Date: 2025-06-05

**Expected Behavior:**

- Use standard REST status codes:
    - 200 OK or 204 No Content for successful ops.
    - 404 Not Found for missing resources.
    - 400 Bad Request for invalid input.
- Include clear error messages.

**Actual Behavior:**

- Status codes inconsistent and misleading.

**Suggested Fix:**

- Align status codes with REST conventions and improve error messaging.

---

## 9. Content-Type Mismatch on XML Responses

**Problem:**

- Requests with `Accept: application/xml` and `Content-Type: text/xml` return XML body correctly, but response header `Content-Type` is `text/html; charset=utf-8` instead of `text/xml`.

**Environment:**

- API: [https://restful-booker.herokuapp.com/](https://restful-booker.herokuapp.com/)
- Endpoint: POST /booking
- Date: 2025-06-12

**Expected Behavior:**

- Response `Content-Type` header matches XML content (`text/xml` or `application/xml`).

**Actual Behavior:**

- Response header incorrectly set to `text/html`, causing client parsing issues.

**Suggested Fix:**

- Fix response header to correctly indicate XML content type.

---

# Summary

The API exhibits multiple validation, parsing, and response consistency issues, particularly in:

- Date and numeric field validation in JSON and XML inputs.
- Boolean field coercion leading to unexpected results.
- Handling of empty string fields in XML.
- URL-encoded request parsing failure.
- Filtering logic that fails to return exact matches on date filters.
- Authentication failure responses using improper HTTP status codes.
- General inconsistency in response status codes and error messaging.
- Content-Type mismatches affecting XML and form-urlencoded responses.