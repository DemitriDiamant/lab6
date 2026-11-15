# Lab 6: PHP, PDO, and SQLite Integration

## Part 1: Database Setup

I created the SQLite database using the following command:

`sqlite3 database/tutoring.db < database/setup-database.sql`

I verified the database using the `.tables` command. Both required tables, `students` and `tutoring_requests`, appeared successfully.
## Part 2: Run the Application

I started the PHP development server using `php -S 0.0.0.0:8000` and opened the Peer Tutoring Hub through my GitHub Codespaces forwarded URL.

**Application URL:** [Paste your actual forwarded URL here]

I submitted a tutoring request using the test student name "Test Student" for IS 6465. The request was successfully saved and displayed as Request #2 with a status of Open.

**Screenshot:** [Insert the screenshot of Request #2 here]
## Part 3: Application Workflow Testing

| Test | What I Did | Expected Result | Actual Result | Pass or Fail |
|---|---|---|---|---|
| Submit a valid request | Submitted a tutoring request for Test Student in IS 6465. | A new request is created with Open status. | Request #2 was created with Open status. | Pass |
| View all requests | Opened the All Requests page. | The new request appears in the list. | Request #2 appeared alongside Request #1. | Pass |
| Filter requests | Filtered by IS 6465 and Open status. | Only matching requests appear. | Both matching requests appeared. | Pass |
| Edit an Open request | Edited the details of Request #2. | Updated information is saved and displayed. | The new description about PHP, PDO, and SQLite appeared. | Pass |
| Cancel an Open request | Cancelled Request #2. | The status changes to Cancelled. | Request #2 displayed Cancelled. | Pass |
| Attempt to edit or cancel a cancelled request | Checked Request #2 after cancellation and attempted to open its edit page directly. | The application prevents further changes. | Edit and Cancel buttons disappeared. Direct navigation to the edit page displayed Invalid ID. | Pass — UI restriction confirmed; no change observed |

## Testing Summary

I tested the main workflows of the Peer Tutoring Hub application. The application successfully created, displayed, filtered, edited, and cancelled tutoring requests. After cancelling Request #2, the Edit and Cancel buttons were no longer available. The application also displayed an error when I attempted to access the edit page directly.
## Part 4: Request Submission Workflow

**1. Which file displays the tutoring request form?**

`php/request-form.php` displays the form where students enter their tutoring request information.

**2. Which file receives the submitted form data?**

`php/process-request.php` receives the information submitted through the form.

**3. Which file opens the PDO connection to SQLite?**

`config/db.php` creates the PDO connection to the SQLite database.

**4. Which file coordinates validation and the database transaction?**

`services/requestService.php` coordinates validation and the database transaction, using validation functions from `includes/validation.php`.

**5. Which files contain the SQL statements?**

`repositories/studentPDO.php` and `repositories/requestPDO.php` contain the SQL statements used to work with the database.

**6. Which table stores student information?**

The `students` table stores student information, including student names and email addresses.

**7. Which table stores tutoring requests?**

The `tutoring_requests` table stores tutoring requests, including the course, topic, preferred date, preferred time, and status.

**8. How does a tutoring request connect to the student who submitted it?**

Each tutoring request has a `student_id` that connects it to the matching student in the `students` table. This relationship allows the application to retrieve the student information associated with a request.

### Why SQL Statements Belong in Repository Files

SQL statements belong in repository files because it keeps the database code separate from the form and website pages. This makes the application easier to organize, test, and maintain. If a database query needs to change, developers can update the repository without changing the form page.
## Part 5: Integration Tests

I ran the integration tests using the following command:

`php tests/integration-tests.php`

### Complete Test Output

```text
PASS UC01 create and Open
PASS Lab 4 checkbox values preserved
PASS Duplicate email reuses student
PASS UC02 course and status filter
PASS Invalid reason rejected
PASS Invalid submission writes nothing
PASS Edit Open request
PASS Updated reason persisted
PASS Cancel Open request
PASS Cancelled row retained
PASS Second cancellation rejected
PASS Unrelated request unchanged
PASS FK enforced
PASS UC02 empty filter result
All integration checks passed
```

### Test Results

All 14 integration checks passed successfully. The tests verified request creation, filtering, validation, editing, cancellation, and database integrity.

The initial run had three failures because the database setup script included sample records. After clearing the sample records from the integration test database, all checks passed.
## Part 6: AI Skills Pack Process

### Activity 1: Application Builder

**AI Prompt:**

Explain how a tutoring request moves from php/request-form.php through PHP, PDO, and SQLite in this project. Identify the responsibility of each file.

**AI Response:**

The process begins in `php/request-form.php`, where a student fills out the tutoring request form. The form sends the information to `php/process-request.php`, which receives the submitted data. The information is then handled by `services/requestService.php`, which coordinates validation and the database transaction. The application uses `config/db.php` to establish a PDO connection to SQLite. The repository files, `repositories/studentPDO.php` and `repositories/requestPDO.php`, contain the SQL statements that save student and tutoring request information. The data is stored in the `students` and `tutoring_requests` tables.

**My Evaluation:**

One accurate statement is that `config/db.php` establishes the PDO database connection. I verified this by checking the database connection code in that file. The explanation also matches the workflow provided in the lab instructions.

### Activity 2: Traceability Auditor

**AI Prompt:**

Trace the requirement "a student can submit a tutoring request" through the PHP files, service, repositories, SQLite tables, and integration tests in this project.

**AI Response:**

The tutoring request starts on the request form and is submitted to the processing page. The service handles the request and uses the repository files to save information through PDO. SQLite stores the student and request records, while the integration tests check that a request can be created successfully.

**Traceability Table:**

| Requirement | Application Page | Service or Validation | Repository or SQL | Database Table | Test Evidence |
|---|---|---|---|---|---|
| Student submits a tutoring request | `php/request-form.php` and `php/process-request.php` | `services/requestService.php` and `includes/validation.php` | `repositories/studentPDO.php` and `repositories/requestPDO.php` | `students` and `tutoring_requests` | `tests/integration-tests.php` — UC01 create and Open passed |

**My Evaluation:**

I checked the application files and integration test results to evaluate the AI response. The application successfully created Request #2 with an Open status. The UC01 integration test also passed, supporting the requirement that a student can submit a tutoring request.

### Activity 3: Test Harness Generator

**AI Prompt:**

Suggest one useful test case for the Peer Tutoring Hub application. The test must relate to request creation, filtering, editing, cancellation, validation, or database integrity.

**AI Response:**

A useful test would be checking whether a cancelled tutoring request can be edited. The test would create a request, cancel it, and then attempt to edit it. The expected result is that the application prevents the cancelled request from being changed.

**Is this already covered?**

The integration tests already cover cancellation and reject a second cancellation. They also test editing an Open request. However, the test output does not explicitly show a check for editing a cancelled request.

**How I would test it:**

I would create a test request, cancel it, and attempt to access its edit page. I would also check the database afterward to make sure the cancelled request was not modified.

**My Evaluation:**

This is an appropriate test because cancelled requests should not be editable. It would help verify that the application protects the request status and prevents unauthorized changes to cancelled records.

### Skills Pack Reflection

ChatGPT helped me understand how the different PHP files work together to save tutoring requests in SQLite. It also helped me trace the request submission process and identify useful test cases. I learned that AI can make understanding code easier, but its explanations are not always guaranteed to be correct. Developers need to compare AI responses with the actual code and assignment requirements to make sure the information is accurate.
## Part 7: Inspect the Database

I opened the SQLite database using `sqlite3 database/tutoring.db` and ran the SQL JOIN query provided in the lab instructions.

### Explanation of the SQL JOIN

The JOIN connects the `tutoring_requests` table with the `students` table using their matching `student_id` values. This allows the query to display student information, such as their name and email, alongside the details of their tutoring request. Without the JOIN, the request table would not provide the student's name and email directly.

### Query Results

The query successfully displayed two tutoring requests. Request #1 belonged to Maria Lopez, and Request #2 belonged to Test Student. Both requests were associated with IS 6465.

The results confirmed that the student information and tutoring requests were correctly connected in the SQLite database.