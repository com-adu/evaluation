# Coding Challenge: Junior Developer Evaluation

## Overview

Build a program that allows a non-technical user to import a spreadsheet via a web interface and load it into a MySQL database table.

---

## Web Interface Requirements

- A simple UI is acceptable — no elaborate design needed
- No authentication or authorization required; URL access implies authorization
- Must include:
  - A file upload control
  - An **Import** button
  - Feedback indicating success or displaying errors

---

## Input File Format

- Format: Excel (`.xlsx`)
- Structure: Simple table with a header row and data rows
- The header row may **not** be the first row
- Some headers may use **merged cells**
- Column order may vary — use column names, not positions
- Process **every sheet** in the workbook; skip sheets that don't contain the required headers
- Ignore any columns not needed in the database
- use the supplied `item-export-test.xlsx` as a reference for the format and to test against

---

## Database Table

Assume the following table exists as part of a larger schema:

```sql
CREATE TABLE avb_closeout (
  avb_sku        VARCHAR(255) PRIMARY KEY,
  closeout_id    VARCHAR(255) NOT NULL,
  linq_id        INT(10)      NOT NULL,
  avb_status     VARCHAR(255) NOT NULL,
  closeout_type  VARCHAR(255) NOT NULL,
  avb_brand      VARCHAR(255) NOT NULL
);
```

Target MySQL version: **5.7 and above**

---

## Column Mapping

| Spreadsheet Column | Database Column |
|--------------------|-----------------|
| `SKU` | `avb_sku` |
| `Brand` | `avb_brand` |
| `Product Linq ID` | `linq_id` |
| `CLOSEOUT_ID` | `closeout_id` |
| `CLOSEOUT_TYPE` | `closeout_type` |
| `Active Inactive Status-Default` | `avb_status` |

All six columns are **required**. Any additional spreadsheet columns should be ignored.

---

## Data Processing Rules

- **Upsert behavior**: Records with an existing primary key should update the remaining columns; new records should be inserted.
- **`linq_id` parsing**: Be lenient — if the value is stored as text, strip any commas before parsing as an integer.
- **Text trimming**: Trim all text values before saving to the database.
- **Row validation**: Process all properly formatted rows; skip and warn on rows that are improperly formatted or missing required columns.

---

## Error Reporting

The application should report the following:

| Condition | Message |
|-----------|---------|
| Success | Confirmation with the number of records imported |
| Missing columns | List of which required columns are absent |
| Improper data format | Row and column of the offending value |
| Missing data | Row and column of the missing value |

---

## Configuration

The application must support configuration for the database connection:

- DSN
- Schema name
- Username
- Password

Use any values you like during development.

---

## Technical Requirements

- **Primary language**: Java
- **Build system**: Maven (the project must build and run successfully via `mvn`)
- **Deliverable**: Codebase committed to a Git repository

### Recommended Dependencies

```xml
<!-- Excel parsing -->
<dependency>
  <groupId>org.apache.poi</groupId>
  <artifactId>poi</artifactId>
  <version>5.5.1</version>
</dependency>

<!-- MySQL connector -->
<dependency>
  <groupId>com.mysql</groupId>
  <artifactId>mysql-connector-j</artifactId>
  <version>9.6.0</version>
</dependency>
```

---

## Bonus Points

- **AI tooling**: Use of AI coding tools is *encouraged*. Extra points for documenting the prompts used.
- **Docker**: Extra points for a standalone Docker container.
- **Second implementation**: Extra points for a version in a different language that improves on the Java version (e.g., faster to develop, smaller codebase).
