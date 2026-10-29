# PostmanReports

[![Newman Tests](https://github.com/gillersja1/PostmanReports/actions/workflows/github-actions-demo.yml/badge.svg)](https://github.com/gillersja1/PostmanReports/actions/workflows/github-actions-demo.yml)

A Postman collection for the [Restful Booker](https://restful-booker.herokuapp.com) API, run in GitHub Actions with Newman.

## What it covers

`RestfulBookerBVT.postman_collection.json` sends 37 requests with 53 assertions:

- **Get Bookings**: list all bookings, and search by first name, last name, both names, checkout date, and with no match
- **Get Booking by ID**: existing and non-existent IDs
- **Create Booking**: a valid booking, and a null first name
- **Modify Booking / Patch Modify Booking**: valid and invalid updates
- **Ping** and **Auth**

The collection's pre-request script generates random booking data (names, prices, dates) for each run. Tests that need a booking create their own first.

The base URL and login (`rb_url`, `rb_username`, `rb_password`) live in `Production.postman_environment.json`, not in the requests. The login is Restful Booker's public demo account.

## Known defects

The `Known Defects` folder holds tests that assert correct behaviour but fail against Restful Booker today. Restful Booker is a practice API with deliberate bugs:

| Request | Expected | Actual |
|---|---|---|
| Search By Checkin | Booking checking in on that date is returned | It's missing from the results |
| Update Invalid Date | PUT with an invalid date is rejected | Accepted (2xx) |
| Invalid Patch | PATCH with invalid data is rejected and the booking is unchanged | Accepted, and `firstname` becomes `null` |
| Delete Booking | `200 OK` | `201 Created` |

CI runs these in a separate **non-blocking** step, so they show up in the reports without failing the build.

## CI reports

Each push, pull request or manual run produces:

- **HTML reports** (`newman-reporter-htmlextra`) and **JUnit XML** for both steps, uploaded as the `newman-reports` artifact
- **Check-run summary** of the main suite, shown on the commit and PR

## Running locally

```bash
# everything, including known defects
npx newman run RestfulBookerBVT.postman_collection.json -e Production.postman_environment.json

# known defects only
npx newman run RestfulBookerBVT.postman_collection.json -e Production.postman_environment.json --folder "Known Defects"
```
