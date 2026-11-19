# Water Bill Management System

A desktop application built with **Java (AWT/Swing)**, **JDBC** and **MySQL** to manage water-connection
customers, record meter readings, calculate bills using predefined slab rates, and track payments.

## Features

- **Customer management** - add, update, delete and search customers (name, address, phone, meter no, connection type)
- **Meter reading entry** - previous reading is picked up automatically; enter the current reading to get the units consumed
- **Automatic bill calculation** - slab-wise rates per connection type (Domestic / Commercial / Industrial) plus a fixed service charge
- **Bill preview and generation** - one bill per customer per month (duplicates are blocked)
- **Payments** - list/search bills, view the receipt, mark bills as paid, see outstanding amount
- **Tariff screen** - shows the predefined rates stored in the database

## Tariff used (editable in the `rates` / `service_charges` tables)

| Connection | 0-100 units | 101-300 units | Above 300 | Service charge |
|------------|-------------|---------------|-----------|----------------|
| Domestic   | Rs. 5       | Rs. 8         | Rs. 12    | Rs. 50         |
| Commercial | Rs. 10      | Rs. 15        | Rs. 20    | Rs. 150        |
| Industrial | Rs. 15      | Rs. 22 (101-500) | Rs. 30 (above 500) | Rs. 300 |

Example: a Domestic customer using 150 units pays `100 x 5 + 50 x 8 = 900` + `50` service = **Rs. 950**.

## Project structure

```
WaterBillManagementSystem
|-- database/schema.sql              # tables + predefined rates
|-- pom.xml                          # Maven build (MySQL driver dependency)
`-- src/main
    |-- resources/db.properties      # database URL / user / password
    `-- java/com/waterbill
        |-- Main.java
        |-- db/DBConnection.java
        |-- model/   Customer, Bill, Slab
        |-- dao/     CustomerDAO, BillDAO, RateDAO   (all JDBC code)
        |-- util/    BillCalculator, BillFormatter, Dialogs
        `-- ui/      MainFrame, CustomerPanel, BillingPanel, PaymentPanel, TariffPanel
```

## Requirements

- JDK 11 or newer
- MySQL 5.7+ / 8.x
- Maven 3.6+ (or an IDE such as IntelliJ / Eclipse / NetBeans)

## Setup and run

1. **Create the database** - run the script in MySQL:
   ```bash
   mysql -u root -p < database/schema.sql
   ```
   (or open `database/schema.sql` in MySQL Workbench and execute it)
2. **Configure the connection** - edit `src/main/resources/db.properties` and set your MySQL user and password.
3. **Run the application**
   ```bash
   mvn compile exec:java
   ```
   or import the folder as a Maven project in your IDE and run `com.waterbill.Main`.

### Without Maven

Download the MySQL Connector/J jar (`mysql-connector-j-8.x.jar`) and run:
```bash
mkdir out
javac -d out -cp mysql-connector-j-8.4.0.jar $(find src/main/java -name "*.java")
cp src/main/resources/db.properties out/
java -cp "out:mysql-connector-j-8.4.0.jar" com.waterbill.Main     # on Windows use ; instead of :
```

## How to use

1. **Customers tab** - add a customer (meter number must be unique; set the initial meter reading).
2. **Enter Reading / Generate Bill tab** - choose the customer and month, type the current reading,
   click *Calculate / Preview*, then *Generate Bill*.
3. **Bills & Payments tab** - find the bill, click *View Bill*, and *Mark as Paid* when the customer pays.

> Do not commit your real database password. Keep `db.properties` with a placeholder on GitHub.
