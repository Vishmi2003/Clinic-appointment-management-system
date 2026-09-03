# Sunrise Dental Clinic Management System

A simple, menu-driven JSP/Servlet web application created for the Sunrise Dental Clinic assessment. It supports secure staff login, MySQL appointment storage, appointment lookup, automatic billing, printable receipts, staff help, and safe logout.

## Demo login

- Username: `admin`
- Password: `Sunrise@123`

The password is stored as a SHA-256 hash rather than plain text. For a real deployment, accounts should be moved to a database with salted password hashing and the site should use HTTPS.

## Main functions

1. Authorized staff login with a 30-minute session.
2. Register patient and appointment details.
3. Reject duplicate appointment numbers and dentist double-bookings.
4. Search and display a complete record using the appointment number.
5. Calculate consultation plus treatment fees and print a receipt.
6. Step-by-step help guide for new staff.
7. Safe exit that invalidates the session.

## Assumptions and reasons

- Clinic hours are 08:00–18:00 so invalid time slots can be rejected consistently.
- The consultation fee is LKR 1,500 and is added to every selected treatment.
- Three dentists and seven common treatment types are predefined, preventing spelling-related billing errors.
- Appointment numbers use `APT-1001` format and the next number is suggested automatically.
- A dentist cannot be booked twice at the same date and time, directly addressing the scenario’s double-booking problem.
- Appointment data is stored in the MySQL `dental_clinic.appointments` table.
- Five sample appointments are provided in `src/main/resources/data/appointments.sql` and must be imported before Tomcat is started.
- “Exit system” logs the staff user out safely. A web application should not force-close the user’s browser.

## Project structure

```text
src/main/java             Java models, services, store and servlets
src/main/resources/data   MySQL schema and seed appointment data
src/main/webapp           JSP pages and the single CSS file
src/test/java             JUnit test cases
docs/screenshots          Screenshots of functions and test results
```

## Build and run

Requirements: JDK 17 or newer, Maven 3.9+, and Apache Tomcat 8.5.96 or newer in the Tomcat 8.5/9 family.

```powershell
mvn clean test package
```

The existing XAMPP context descriptor can point directly to the exploded build directory:

```xml
<Context docBase="C:\Users\ASAM\Downloads\sunrise\target\sunrise-dental" path="/sunrise"/>
```

With that descriptor, start Tomcat and visit `http://localhost:8080/sunrise/`.

Alternatively, without a custom context descriptor, deploy and rename the WAR so its filename becomes the context path:

```powershell
Copy-Item target\sunrise-dental.war C:\xampp\tomcat\webapps\sunrise.war
```

## MySQL database

The web application connects to:

```text
mysql://127.0.0.1:3306/dental_clinic
```

The connection details are in `src/main/resources/database.properties`. They can be overridden at deployment time with Java system properties: `sunrise.db.url`, `sunrise.db.username`, and `sunrise.db.password`.

Import the supplied schema and seed data once before deploying:

```powershell
Get-Content -Raw src\main\resources\data\appointments.sql |
    & 'C:\Program Files\MySQL\MySQL Server 8.0\bin\mysql.exe' -u root -p
```

The script recreates the `appointments` table, so do not run it after production records have been added unless you intend to replace them.

## Treatment charges

| Treatment | Treatment fee (LKR) | Total with consultation (LKR) |
|---|---:|---:|
| Dental Consultation | 0.00 | 1,500.00 |
| Teeth Cleaning | 4,500.00 | 6,000.00 |
| Dental Filling | 6,000.00 | 7,500.00 |
| Tooth Extraction | 7,500.00 | 9,000.00 |
| Root Canal Treatment | 18,000.00 | 19,500.00 |
| Teeth Whitening | 12,000.00 | 13,500.00 |
| Orthodontic Consultation | 2,500.00 | 4,000.00 |

See [docs/TEST_CASES.md](docs/TEST_CASES.md) for the test cases and [docs/SCREENSHOTS.md](docs/SCREENSHOTS.md) for the complete visual evidence.
