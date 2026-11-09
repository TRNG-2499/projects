# Project Instructions

## Project Overview

You will build a console-based banking application using Java. The application will allow customers to register, manage bank accounts, deposit and withdraw money, transfer money between accounts, and review their transaction history. It will also allow bank employees to log in, assist customers with account operations (such as wire transfers), and record notes and activity logs on customer accounts.

Your application will use **two databases, each with a distinct responsibility**:

- **PostgreSQL (via JDBC)** stores the core banking data: customers, employees, bank accounts, and transactions. It is the system of record for all balances and money movement.
- **MongoDB (via the MongoDB Java Driver)** stores employee-authored account notes and activity logs, for example a note describing why a wire transfer was performed, or a flag describing suspicious activity an employee noticed on an account. Each note can carry a list of **tags** (short labels such as `suspicious-activity` or `customer-request`) so that notes can be grouped and searched by topic.

You will use the DAO design pattern to separate the application's business logic from its database logic. Each database will have its own set of DAO interfaces and implementations. Service classes must depend only on the DAO interfaces, never on JDBC or MongoDB driver classes directly.

This is a backend-focused project. All user interaction will take place through a command-line or console interface.

## Tools and Technologies Used

Required technologies:
- Java
- Maven or Gradle
- PostgreSQL
- JDBC
- MongoDB
- MongoDB Java Driver
- Git
- GitHub
- JUnit

## Required Concepts
- Object-oriented programming
- Interfaces and abstraction
- Encapsulation
- Collections
- Exception handling
- Input validation
- Layered application architecture
- DAO design pattern
- CRUD operations
- SQL queries and JDBC transactions
- NoSQL document operations and queries, including array fields (e.g., note tags) and multikey indexes
- Polyglot persistence (using a relational and a document database together, each for what it does best)
- Role-based access control (customer vs. employee)
- Externalized configuration
- Unit testing

## Suggested Architecture

Your application should contain the following layers.

### Presentation Layer
The presentation layer manages all console-based interaction with the user.

Responsibilities include:
- Displaying menus and available actions appropriate to the logged-in user's role (customer or employee)
- Reading and validating console input
- Displaying results and error messages
- Sending user requests to the service layer
- Avoiding direct database access and business logic

### Service Layer
The service layer contains the banking rules and application workflows.

Responsibilities include:
- Processing customer registration and authentication
- Processing employee authentication and enforcing employee-only operations
- Managing customer and account operations
- Processing deposits, withdrawals, transfers, and employee-initiated wire transfers
- Validating account ownership, account status, and user role
- Coordinating data access operations across PostgreSQL and MongoDB
- Enforcing banking and transaction rules
- Creating and retrieving transaction records
- Creating, retrieving, and managing employee notes and activity logs
- Validating and normalizing note tags (e.g., trimming, lowercasing, removing duplicates) before notes are saved

### Data Access Layer
The data access layer handles communication with PostgreSQL and MongoDB.

Responsibilities include:
- Defining DAO interfaces for banking data (e.g., customers, employees, accounts, transactions)
- Implementing the banking DAO interfaces for PostgreSQL using JDBC
- Defining DAO interfaces for notes and logs (e.g., account notes, activity logs)
- Implementing the notes/logs DAO interfaces for MongoDB using the MongoDB Java Driver, including queries that filter notes by one or more tags
- Performing create, read, update, and delete operations
- Mapping database rows and documents to Java objects
- Handling database-specific errors and connection logic, and translating them into application-level exceptions

### Model Layer
The model layer represents the application's core business data.

Responsibilities include:
- Defining customers, employees, bank accounts, transactions, account notes (including a list of tags), and activity log entries
- Storing the fields and relationships required by the application
- Encapsulating model data
- Supporting consistent use of data across the service and DAO layers

### Configuration Layer
The configuration layer wires the application together and manages connection settings for both databases.

Responsibilities include:
- Reading PostgreSQL and MongoDB connection settings from an external configuration source (e.g., a properties file excluded from Git, or environment variables)
- Creating and providing the PostgreSQL and MongoDB DAO implementations to the service layer
- Managing database connections and closing them cleanly on application exit
- Keeping credentials and connection values outside the source code

## Instructions

### 1. Set Up the Project

Create a Java application using Maven or Gradle as the build tool.

Initialize a Git repository and commit project changes regularly throughout development (you should have a commit history showing multiple commits).

### 2. Create the Application Models

At minimum, the application should include models for:

- Customers
- Employees
- Bank accounts
- Transactions
- Account notes (with a list of string tags)
- Activity log entries

### 3. Design the PostgreSQL Database

Design a relational database that stores customer, employee, account, and transaction information.

Your design should:
- Use primary keys, foreign keys, and appropriate constraints (e.g., `NOT NULL`, `UNIQUE`, `CHECK` constraints on balances and amounts)
- Distinguish customers from employees (separate tables, or a shared user table with a role column)
- Record which employee, if any, initiated a transaction (e.g., a nullable `initiated_by_employee_id` column)
- Support an account status that employees can change (e.g., `ACTIVE`, `FROZEN`, `CLOSED`)

Include the SQL schema (DDL) script in the repository.

### 4. Design the MongoDB Database

Design a MongoDB database that stores employee notes and activity logs.

At minimum, create collections for:

- **Account notes** — free-text notes written by employees about a specific account. Each note should include at least: the account ID it refers to, the authoring employee's ID, the note text, a list of one or more tags that categorize the note (see below), a creation timestamp, and optionally a related transaction ID.
- **Activity logs** — records of significant employee actions (e.g., viewing a customer's accounts, initiating a wire transfer, freezing or unfreezing an account, creating a note). Each entry should include at least: the employee ID, the action type, the affected account or customer ID, a timestamp, and a details field.

#### Note Tags

Tags are how notes are categorized. There is no separate category field: a note's tags *are* its categories. Because a note can have several tags, a single note can belong to every category it relates to (for example, a note can be both a `wire-transfer` note and a `customer-request` note). In your design:

- Store tags as an **array of strings** on each note document (e.g., `"tags": ["wire-transfer", "customer-request"]`). Every note should have at least one tag, and may have several.
- Use tags for all note organization, filtering, and searching (e.g., "show all `suspicious-activity` notes on this account").
- Decide on a consistent tag format and apply it automatically. A recommended format is lowercase words separated by hyphens (e.g., `Suspicious Activity` is saved as `suspicious-activity`), with surrounding whitespace removed, empty values rejected, and duplicates removed.
- Create an index on the `tags` field (MongoDB creates a multikey index for array fields) so that searching notes by tag is efficient.

There is no required list of tags. You may define a starter set in your application to suggest to employees, and employees may also create new tags when none of the existing ones fit. Some example tags:

| Tag | Example use |
|---|---|
| `general` | General note that doesn't fit a more specific tag |
| `suspicious-activity` | Unusual transactions or behavior that should be investigated |
| `customer-request` | Action taken because the customer asked for it |
| `wire-transfer` | Note related to a wire transfer an employee performed |
| `identity-verified` | Employee confirmed the customer's identity |
| `fraud-review` | Account or transaction is under fraud review |
| `account-frozen` / `account-unfrozen` | Note explaining a freeze or unfreeze |
| `large-deposit` / `large-withdrawal` | Unusually large movement of money |
| `customer-contact` | Record of a call, visit, or message with the customer |
| `address-change` | Customer updated contact or address information |
| `complaint` | Customer raised a complaint |
| `follow-up-required` | Another employee needs to take further action |
| `resolved` | The issue described in earlier notes has been closed |

For example, a note written when an employee performs a wire transfer at a customer's request might have the tags `["wire-transfer", "customer-request", "identity-verified"]`. A later note flagging that transfer as unusual might have the tags `["suspicious-activity", "wire-transfer", "follow-up-required"]`.

Because notes and logs reference data that lives in PostgreSQL, decide how cross-database references will be stored (e.g., storing the PostgreSQL account ID in each document) and document that decision. Create indexes that support your most common queries (e.g., notes by account ID and date, and notes by tag).

### 5. Define the DAO Interfaces

Create DAO interfaces that define the database operations required by the service layer.

- Banking DAO interfaces should support the customer, employee, account, and transaction operations described in the user stories and checklist.
- Notes/log DAO interfaces should support creating, retrieving, filtering, and (where your rules allow) updating notes and log entries. Note DAO operations should include finding notes by tag (on a single account and across all accounts) and retrieving the distinct set of tags already in use.

Service classes must depend on these interfaces only, so that each implementation can be replaced with a test double (mock or in-memory fake) during unit testing.

### 6. Implement Data Access for PostgreSQL and MongoDB

Implement the banking DAO interfaces for PostgreSQL with JDBC, using prepared statements and proper resource management (e.g., try-with-resources).

Implement the notes/log DAO interfaces for MongoDB with the MongoDB Java Driver. Use MongoDB's array query operators (e.g., `$in` to match notes having any of several tags, `$all` to match notes having all of them) to implement tag searches.

### 7. Implement Configuration

Create a configuration mechanism that supplies connection settings for both databases and constructs the DAO implementations the services need.

Connection URLs, usernames, and passwords must come from configuration, not from source code. Provide a sample configuration file (e.g., `application.properties.example`) with placeholder values, and exclude the real file from Git.

### 8. Implement the Banking and Employee Services

Create service-layer logic that supports all required customer, employee, account, transaction, and note operations.

The service layer should enforce validation, role-based permissions, account ownership, account status, balance requirements, and transaction rules. It should also normalize and validate note tags (according to the format you chose in step 4) before notes are saved, so that the same tag is never stored in two different forms.

Define and implement a strategy for operations that touch both databases. PostgreSQL is the system of record for money movement: a banking transaction must never be rolled back or left incomplete because a MongoDB write failed. If a related note or log write fails, the application should report the problem clearly and handle it in a defined way (e.g., warn the employee and allow the note to be re-entered). Document your strategy in the README.

### 9. Create the Console Interface

Create a clear console interface that allows:

- **Customers** to register, log in, manage accounts, perform transactions, and view transaction history.
- **Employees** to log in, look up customers and accounts, perform wire transfers, freeze or unfreeze accounts, create and view account notes, and view activity logs. When creating a note, employees should be able to enter one or more tags (e.g., as a comma-separated list) and see suggested or existing tags. When viewing notes, each note's tags should be displayed, and employees should be able to search notes by tag.

The interface should provide understandable menus, prompts, results, and error messages, and should only show options the current user is permitted to use.

### 10. Add Validation and Exception Handling

Validate all user input and business operations.

The application should handle invalid input, missing records, duplicate data, unauthorized actions (including customers attempting employee-only actions), insufficient balances, invalid transactions, operations on frozen or closed accounts, and errors from either database without unexpectedly terminating.

### 11. Test the Application

Create unit tests for the most important service-layer, banking, and note/logging rules.

Tests should verify successful operations as well as invalid or rejected operations, including role-based permission checks, tag normalization and validation (e.g., `" Customer Request "` becomes `customer-request`, duplicates are removed, and blank tags are rejected), searching notes by tag, and the behavior when a MongoDB write fails after a successful PostgreSQL operation.

The service layer should be testable without requiring a live PostgreSQL or MongoDB database.

### 12. Document the Project

Create a README.md that explains:

- The application purpose
- Features (customer and employee)
- Technologies used
- Project architecture
- PostgreSQL database design (with schema or ER diagram)
- MongoDB database design (collections, sample documents, indexes)
- Note tag format rules and the suggested tags your application offers
- Why each type of data is stored in the database it is
- How cross-database references and consistency are handled
- Configuration process
- How employee accounts are created or seeded
- Setup and run instructions (including starting both databases)
- Test instructions
- DAO implementation
- Known limitations
- Optional enhancements completed

Do not commit passwords, credentials, or private database connection values to the repository.

## User Stories

### Customer Registration and Authentication

US-01: As a new customer, I want to register for an account so that I can access the banking application.

US-02: As a registered customer, I want to log in securely so that I can access my bank accounts.

US-03: As a customer, I want to update my profile information so that my account details remain accurate.

US-04: As a customer, I want my password or PIN to be stored securely so that my credentials are protected.

### Bank Account Management

US-05: As a customer, I want to open a checking account so that I can manage everyday transactions.

US-06: As a customer, I want to open a savings account so that I can store money separately.

US-07: As a customer, I want to view all of my accounts so that I can understand my banking relationship.

US-08: As a customer, I want to view the balance of an account so that I know how much money is available.

US-09: As a customer, I want to close an eligible account so that I can remove an account I no longer need.

### Deposits and Withdrawals

US-10: As a customer, I want to deposit money into an account so that I can increase its balance.

US-11: As a customer, I want to withdraw money from an account so that I can access my funds.

US-12: As a customer, I want the application to prevent overdrafts so that my account balance does not become invalid.

### Transfers

US-13: As a customer, I want to transfer money between my accounts so that I can organize my funds.

US-14: As a customer, I want to transfer money to another valid account so that I can send funds to another customer.

US-15: As a customer, I want a transfer to complete as one operation so that money is not removed unless it is also deposited successfully.

### Transaction History

US-16: As a customer, I want to view my transaction history so that I can review activity on my account.

US-17: As a customer, I want to filter transactions by type or date so that I can locate specific activity.

US-18: As a customer, I want each transaction to show its amount, date, type, and resulting balance so that I can understand its effect.

### Employee Access

US-19: As a bank employee, I want to log in with employee credentials so that I can access employee-only features.

US-20: As a bank employee, I want to search for a customer and view their accounts, balances, and transaction history so that I can assist them.

US-21: As a bank stakeholder, I want customers to be blocked from employee-only features so that internal tools and information remain protected.

### Employee Account Operations

US-22: As a bank employee, I want to perform a wire transfer on a customer's behalf so that I can process requests the customer cannot complete themselves.

US-23: As a bank employee, I want to be required to enter a note when I perform a wire transfer so that there is a record of why and at whose request it was made.

US-24: As a bank employee, I want to freeze an account I believe is compromised so that no further withdrawals or transfers can be made from it until it is reviewed.

US-25: As a bank employee, I want to unfreeze an account after review, with a note explaining the outcome, so that the customer can resume normal activity.

US-26: As a customer, I want to see a clear message when my account is frozen so that I know to contact the bank.

### Account Notes

US-27: As a bank employee, I want to add a text note to an account so that other employees know about relevant interactions or concerns.

US-28: As a bank employee, I want to categorize a note by tagging it (e.g., `general`, `wire-transfer`, `suspicious-activity`, `customer-contact`) so that notes are easy to organize and find.

US-29: As a bank employee, I want to flag suspicious activity on an account with a note tagged `suspicious-activity`, optionally linking it to a specific transaction, so that it can be investigated.

US-30: As a bank employee, I want to view all notes on an account, newest first, so that I understand its history before taking action.

US-31: As a bank employee, I want to filter notes by tag, author, or date range so that I can find specific notes quickly.

US-32: As a bank employee, I want to attach more than one tag to a note (e.g., both `wire-transfer` and `customer-request`) so that a note that covers several topics appears under each of them.

US-33: As a bank employee, I want to see each note's tags when viewing an account's notes so that I can quickly understand what each note is about.

US-34: As a bank employee, I want to search notes by one or more tags, on a single account or across all accounts, so that I can find related notes (e.g., every note tagged `fraud-review`).

US-35: As a bank employee, I want to see suggested tags and tags already in use when writing a note so that I reuse existing tags instead of creating near-duplicates.

US-36: As a bank stakeholder, I want tags to be saved in a consistent format (e.g., lowercase and hyphen-separated, with no duplicates or blank values) so that the same tag is never recorded in different forms.

US-37: As a bank employee, I want to see a list of accounts with open suspicious-activity flags (e.g., accounts with a `suspicious-activity` note and no later `resolved` note) so that I can prioritize reviews.

US-38: As a bank stakeholder, I want notes to be preserved once written (corrections are added as new notes or tracked edits, not silent overwrites) so that the record can be trusted.

US-39: As a bank stakeholder, I want employee notes to be hidden from customers so that internal observations remain confidential.

### Activity Logs

US-40: As a bank stakeholder, I want significant employee actions (wire transfers, freezing/unfreezing accounts, creating notes, viewing customer accounts) to be logged automatically so that employee activity can be audited.

US-41: As a bank employee, I want to view the activity log for an account so that I can see which employees have acted on it and when.

US-42: As a bank employee, I want to filter activity logs by employee, action type, or date range so that I can investigate specific events.

### Data Persistence

US-43: As a developer, I want banking data stored in PostgreSQL so that balances and transactions benefit from relational integrity and ACID transactions.

US-44: As a developer, I want notes and logs stored in MongoDB so that variable, text-heavy records can be stored and queried flexibly.

US-45: As a developer, I want the service layer to depend only on DAO interfaces so that business logic is not tied to database-specific code and can be tested with test doubles.

US-46: As a developer, I want a failure to save a note or log entry to never undo or corrupt a completed banking transaction, and to be clearly reported, so that the system of record stays correct.

### Reliability and Validation

US-47: As a user, I want clear error messages when an operation fails so that I understand how to correct the problem.

US-48: As a bank stakeholder, I want invalid and unauthorized transactions to be rejected so that customer accounts remain protected.

US-49: As a developer, I want important business rules, including role permissions and note/logging rules, covered by tests so that changes do not introduce unexpected defects.

## Project Requirements Checklist
- Project is built as a Java application using Maven or Gradle and follows a clean, organized package structure with appropriate separation of concerns.
- Application implements appropriate object-oriented design using well-defined model classes and follows the layered architecture (Presentation, Service, Data Access, Model, and Configuration).
- Application uses PostgreSQL (via JDBC) for customers, employees, accounts, and transactions, and MongoDB (via the MongoDB Java Driver) for employee account notes and activity logs, with each accessed only through DAO interfaces.
- Application supports two roles (customer and employee) and enforces role-based access to features and data.
- Employee features include customer/account lookup, wire transfers with a required note, freezing/unfreezing accounts, creating and filtering account notes, flagging suspicious activity, and viewing activity logs.
- Account notes store a list of string tags in a consistent, normalized format. Employees can add tags when creating notes, view tags on existing notes, and search notes by one or more tags, supported by an index on the `tags` field.
- Significant employee actions are automatically recorded in MongoDB activity logs.
- Operations spanning both databases follow a defined, documented consistency strategy in which PostgreSQL remains the system of record.
- Application implements all user stories while enforcing appropriate business rules, validation, and exception handling.
- Application uses secure and appropriate database practices, including prepared statements and JDBC transactions (PostgreSQL), appropriate indexes (MongoDB), proper resource management, securely hashed credentials, and externalized configuration values.
- Console interface provides a clear, intuitive user experience with role-appropriate menus, navigation, input validation, and user-friendly error messages.
- Application demonstrates clean, maintainable, and well-documented code that follows established Java coding standards and best practices.
- Application includes a comprehensive suite of automated tests covering the application's business logic, role permissions, note/logging rules, and critical functionality, runnable without live databases.
- Project includes a complete README with setup instructions, architecture overview, both database designs, configuration, and execution steps.
- Project is maintained in a Git repository with meaningful commit history demonstrating consistent development throughout the project.
