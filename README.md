# Restful Booker API Postman Collection

This repository contains a comprehensive Postman test collection for the [Restful Booker API](https://restful-booker.herokuapp.com/). It includes automated coverage of both standard workflows and extensive edge cases.

## ✅ What's Inside

- Full test coverage of all [documented API endpoints](https://restful-booker.herokuapp.com/apidoc/index.html) (CRUD operations, auth, filters, etc.)
- Negative tests for invalid inputs, missing fields, and unsupported formats
- Full booking lifecycle tests: creation, verification, update, partial update, deletion, and validation
- Flexible auth handling: token + fallback to Basic Auth if needed
- Three organized folders:
  - **Default (All endpoints)** – positive tests for all documented endpoints
  - **Negative Tests** – checks for invalid inputs, known bugs, and API inconsistencies
  - **Full Booking Lifecycle Test** – realistic scenario: one booking is created, validated, updated, and deleted

## 🧰 How to Use

1. Import the `Restful Booker Test Collection.postman_collection.json` into Postman.
2. (Optional) Set up the required environment variables:
   - `baseUrl` (defaults to `https://restful-booker.herokuapp.com`)
   - `username` / `password` (defaults to `admin` / `password123`)
3. Run individual folders or the entire collection using Postman Collection Runner or Newman.

## 🚨 Known Issues

- Many invalid or malformed requests return `200 OK` instead of `400 Bad Request`
- Invalid data types in JSON return silent coercion (`NaN`, `null`, or incorrect booleans)
- XML parsing issues: wrong boolean handling, invalid dates as `0NaN-aN-aN`
- `application/x-www-form-urlencoded` is not supported by the API
- Some endpoints return `201 Created` where `200 OK` is expected
- See [bugs-and-issues.md](./bugs-and-issues.md) for a list of identified bugs. Or check them out here → 📄 [Notion: Restful Booker – Bugs and Issues Found](https://www.notion.so/Restful-Booker-Bugs-and-Issues-Found-20b20b03203180cfaa31c38f3c88e895?source=copy_link)

## 🔍 Test Coverage Highlights

- Status code validation for each request
- Schema validation (JSON response matches expected shape)
- Edge cases for booleans, numbers, empty fields, and malformed data
- Fail-fast logic and cleanup included in lifecycle flows
- Smart variable management (e.g. dynamically sets and reuses `bookingId` or `createdbookingId`)

---

Maintained with care by [evanlinden](https://github.com/evenevanlinden) 💙

For any questions regarding this test collection, please contact: evenevanlinden@gmail.com
