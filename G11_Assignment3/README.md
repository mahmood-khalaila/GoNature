# GoNature — Nature Park Reservation System

GoNature is a Java desktop application for booking nature park visits and managing park operations. Travelers can check availability and manage reservations, while park staff handle visitor entry, exit, and administrative workflows.

Developed as an academic team project by **Group 11**. This directory contains the full Assignment 3 application, including client and server source code, packaged JARs, a database script, and design documentation.

## Demo


https://github.com/user-attachments/assets/4d48c2a1-b9fe-44b2-95a4-40efe7a97f06



## Features

- **Visit reservations:** browse parks, check availability, create bookings, confirm reservations, and cancel orders.
- **Waitlist and alternatives:** join a waiting list or request alternative visit slots when a preferred slot is unavailable.
- **Visitor operations:** create walk-in orders, process park entry and exit, and look up bookings by code.
- **Subscriber and guide management:** register subscribers and guides, look up subscribers, and update subscriber profiles.
- **Management workflows:** request changes to park parameters and approve or reject parameter changes and promotions.
- **Reports:** generate and save reports covering visits, cancellations, visitor totals, and park usage.
- **Reservation follow-up:** a background server thread checks reminders and expired confirmations every 60 seconds. Notifications are recorded in the database and exposed through application commands; this should not be interpreted as an external email or SMS integration.
- **Server console:** configure database connectivity and the listening port, view logs, and monitor connected clients.

## Technology Stack

| Component | Technology |
| --- | --- |
| Language | Java |
| Desktop interface | JavaFX, FXML, CSS |
| Client–server communication | OCSF over TCP sockets |
| Database | MySQL |
| Database access | JDBC, MySQL Connector/J |
| Project configuration | Eclipse Java projects |

## Architecture

The JavaFX client sends `ClientServerMessage` objects containing commands and payloads to the OCSF server. The server dispatches requests through `MessageHandler`, performs database operations through `DatabaseController`, and returns responses to the client.

| Layer | Key files | Responsibility |
| --- | --- | --- |
| Client interface | `gui/`, `client/ClientUI.java` | Screens, user input, and application startup |
| Client communication | `client/GoNatureClient.java`, `client/ClientMessageHandler.java` | Connection management and server responses |
| Shared message model | `common/` in both projects | Commands, request/response messages, and domain objects |
| Server | `server/BackEndServer.java`, `server/MessageHandler.java` | Client connections and request handling |
| Persistence | `DB/DatabaseController.java`, `DB/MySqlConnector.java` | SQL operations and the shared JDBC connection |
| Background processing | `server/ReminderThread.java` | Reservation reminders and expiration processing |

The server uses a shared database connection with a `DB_LOCK` synchronization mechanism for concurrent access from client threads.

## Repository Layout

Paths below are relative to `G11_Assignment3/`.

```text
DB/G11_Assignment3_DB.sql          Database schema and seed data
JAR/G11_server.jar                 Packaged server
JAR/G11_client.jar                 Packaged client
PROJECT/G11_Assignment3-Project/
    GoNature_Client/src/           Client source, FXML, CSS, and images
    GoNature_Server/src/           Server source and bundled OCSF classes
    GoNature_Server/sql/setup.sql  Additional database setup script
DOC/G11_Assignment3.vpp            Design project
DOC/G11_Assignment3_JavaDoc.rar     Archived JavaDoc
```

## Getting Started

### Prerequisites

- **JDK 21 or newer** for the supplied JARs, whose application entry classes use Java 21 bytecode.
- A compatible **JavaFX SDK** for your operating system and JDK.
- A running **MySQL** instance and an account allowed to create/import the database.
- A desktop environment to display the client and server JavaFX windows.

The repository uses Eclipse project files rather than a Maven or Gradle build.

### 1. Clone the repository

```bash
git clone https://github.com/Kamar47/project1-.git
cd project1-/G11_Assignment3
```

### 2. Import the database

The following script creates and selects the `gonature` database, defines its tables, and inserts seed data:

```bash
mysql -u YOUR_DB_USER -p < DB/G11_Assignment3_DB.sql
```

Alternatively, open the script in MySQL Workbench and execute it against your local development database. Review the script before importing it into an existing database.

### 3. Launch the server

Replace `/absolute/path/to/javafx-sdk/lib` with your JavaFX SDK library directory:

```bash
java --module-path /absolute/path/to/javafx-sdk/lib \
  --add-modules javafx.controls,javafx.fxml \
  -jar JAR/G11_server.jar
```

Configure the server window:

| Field | Local development value |
| --- | --- |
| Port | `5555` |
| DB URL | `jdbc:mysql://localhost/gonature?serverTimezone=UTC` |
| DB User | Your MySQL username |
| DB Password | Your MySQL password |

Click **Start Server** and confirm that the database connection and server startup succeed in the log.

### 4. Launch the client

In another terminal, from the same directory:

```bash
java --module-path /absolute/path/to/javafx-sdk/lib \
  --add-modules javafx.controls,javafx.fxml \
  -jar JAR/G11_client.jar
```

Connect through the client interface using `localhost` and port `5555` when both applications run on the same machine. For separate machines, use the server address and ensure the chosen port is reachable.

### Running from source

1. Import `GoNature_Client` and `GoNature_Server` from `PROJECT/G11_Assignment3-Project/` as existing Eclipse projects.
2. Configure the JDK and JavaFX dependencies for both projects.
3. Ensure the server includes the supplied `mysql-connector-j-9.5.0.jar` on its build path.
4. Add JavaFX VM arguments with your SDK path: `--module-path /absolute/path/to/javafx-sdk/lib --add-modules javafx.controls,javafx.fxml`.
5. Run `server.ServerApp`, configure and start it, then run `client.ClientUI`.

Keep the `common` message and domain classes compatible across the client and server when modifying the communication protocol.

## Manual Verification

A useful local demonstration covers the complete reservation lifecycle:

1. Start MySQL and the server, then connect a client.
2. Browse parks and check availability for a visit.
3. Create a reservation and verify that it appears in the traveler's orders.
4. Exercise cancellation or a waitlist flow.
5. Use a seeded staff account to process visitor entry and exit.
6. Generate a management report and inspect the result.

No automated test suite was found in this Assignment 3 directory. The launch instructions above were derived from the source, configuration, and JAR manifests; they have not been executed in a desktop/MySQL environment during this documentation review.

## Academic Scope

This is a coursework application with seeded demonstration data and role-specific screens. The client and server use Java object messages over sockets. Production deployment, external payment processing, and external notification delivery are outside the scope documented here.
