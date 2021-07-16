---
title: "Database"
category: "Connectors"
description: "Describes the configuration and usage of the Database connector, which is available in the Mendix Marketplace."
tags: ["marketplace",  "marketplace component", "database connector", "jdbc", "hikari", "query" ]
#If moving or renaming this doc file, implement a temporary redirect and let the respective team know they should update the URL in the product. See Mapping to Products for more details. 
---

## 1 Introduction

Use the [Database](https://appstore.home.mendix.com/link/app/2888/) connector to incorporate your external data directly in your Mendix application. This connector enables seamlessly connecting to external databases without being limited in your choice of database or SQL dialect.

This document focuses on executing an SQL `SELECT` query and SQL statements on external relational databases.

The **Execute query** action (which is present in the connector) provides a consistent environment for Mendix apps to perform an arbitrary `SELECT` SQL query on relational external databases.  A Java database connectivity (JDBC) API (which is a standard Java API) is used when the Java action attempts to connect with a relational database for which a JDBC driver exists.

The **Execute statement** action works internally in the same manner as the **Execute query** action. However, it is used for `INSERT`, `UPDATE`, `DELETE`, `STORED PROCEDURE`, or `DDL` statements.

### 1.1 Dependencies

* [HikariCP](https://github.com/brettwooldridge/HikariCP), a high-performance JDBC connection pool

## 2 Prerequisites

These are the prerequisites for using this connector:

* A database URL address that points to your database
* The user name for logging into the database, relative to the database URL address
* The password for logging into the database, relative to the database URL address
* The JDBC driver libraries (*.jar* extension) for the databases you want to connect to must be placed inside the **userlib** directory of your Mendix application
	* For example, if you want to connect to Amazon RDS PostgreSQL database (`jdbc:postgresql://xyz-rds-instance.ccnapcvoeosh.eu-west-1.rds.amazonaws.com:5432/postgres`), you need to place the PostgreSQL JDBC driver *.jar* inside the **userlib** folder
	* For more information, see the [Common JDBC Drivers](#links) section below
* Specific to the `Execute` query action: an entity in the domain model that can be used for the results of the executed query
	* For example, a query like `select name, number from stock` has two columns (of the string and integer type, respectively), so in order to use the **Execute query** action, you have to add an entity in the domain model that has the same attributes as the columns in the query

## 3 Getting Started

Once you have imported the Database Connector into your app, you will have the **Database Connector** category available in the **Toolbox**. The connector supports five actions: **Execute query**, **Execute statement**, **Execute parameterized query**, **Execute parameterized statement** and **Execute callable statement**.

![](attachments/database/database-connector-in-toolbox.png)

### 3.1 Executing Queries And Statements
#### 3.1.1 Usage

 To use any of **Execute query**, **Execute statement**, **Execute parameterized query** or **Execute parameterized statement** in your Mendix application, drag them into your microflow. Next, provide all the arguments for the selected action and choose the output result name.
 
 The **Execute query** and **Execute parameterized query** actions should be used for querying objects with a `SELECT` SQL command. The **Execute statement** and **Execute parameterized statement** actions should be used for all other commands (for instance, `INSERT`, `UPDATE`, or `DELETE`).
 
 For both queries and statements, the difference between the parameterized and regular versions are that the parameterized version takes a string template parameter, while the regular version takes a fully formed SQL command string with no placeholders.
 
 {{% alert type="info" %}}
 The parameterized actions are only available with Database Connector versions 3.0.0 and above. For these, it is necessary to use Mendix [8.6.0](/releasenotes/studio-pro/8.6#860).
 {{% /alert %}}

#### 3.1.2 Results

These are the results of the actions:

* **Execute query** and **Execute parameterized query** – a list of objects of the row type, which is also the output of the `SELECT` SQL query
* **Execute statement** and **Execute parameterized statement** – either an integer or a long value, which usually represents the amount of affected rows

### 3.2 Executing Callable Statements

The **Execute callable statement** microflow action is used to execute stored procedures. This action has no output and uses an input object of type **DatabaseConnector.Statement**.

![](attachments/database/callable-statement-action.png)

The selected **DatabaseConnector.Statement** object should contain the statement body (SQL content) in the **contents** attribute. It should also define, if applicable, a list of **DatabaseConnector.Parameter** objects to represent the input and output parameters of the stored procedure. 

![](attachments/database/statement-parameter-diagram.png)

A **DatabaseConnector.Parameter** object will refer to its name or position in the stored procedure using the **Name** or **Position** attributes respectively. It will also indicate its type using the **ParameterMode** attribute:

- **DatabaseConnector.ParameterMode.INPUT**: if it's an *IN* parameter.
- **DatabaseConnector.ParameterMode.OUTPUT**: if it's an *OUT* parameter.
- **DatabaseConnector.ParameterMode.INOUT**: if it's an *INOUT* parameter.

**DatabaseConnector.Parameter** Specializations:

#### Examples
##### primitive types

##### object type

##### lists

##### ref-cursor


## 4 Best Practices

* Avoid having a user input as part of your dynamic SQL queries and statements (in the future, using parameters with queries or statements will be supported)
* Avoid fetching large amounts of data, which can lead to memory issues (as all the **ResultSet** data is loaded into memory at once)

## 5 Common JDBC Drivers {#links}

* [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/mgmt/configure-jdbc-connection.html#download-jdbc-driver)
* [Apache Derby](http://db.apache.org/derby/derby_downloads.html)
* [Firebird](http://www.firebirdsql.org/en/jdbc-driver/)
* [H2 Database Engine](http://www.h2database.com/html/main.html)
* [HSQLDB](https://sourceforge.net/projects/hsqldb/files/)
* [IBM DB2](https://www.ibm.com/support/pages/download-initial-version-115-clients-and-drivers)
* [IBM Informix](https://www.ibm.com/products/informix?mhsrc=ibmsearch_a&mhq=informix)
* [MariaDB](https://downloads.mariadb.org/connector-java/)
* [Microsoft SQL Server/SQL Database](https://www.microsoft.com/en-us/download/details.aspx?id=11774)
* [MySQL](https://dev.mysql.com/downloads/connector/j/)
* [Oracle Database](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html)
* [OrientDB](https://orientdb.org/)
* [PostgreSQL](https://jdbc.postgresql.org/download.html)
* [Presto](https://prestodb.github.io/docs/current/installation/jdbc.html)
* [SQLite](https://bitbucket.org/xerial/sqlite-jdbc/downloads/)

{{% alert type="info" %}}
If you intend to connect to SQL Server using integrated security, please be aware that the JDBC driver in the **userlib** folder needs to match the version supplied with the Mendix Platform (via the **runtime/bundles/** folder inside the Mendix installation directory).
{{% /alert %}}

## 6 Developing This Marketplace Component

1. Clone [https://github.com/mendix/database-connector.git](https://github.com/mendix/database-connector.git).
2. Open the *DatabaseConnector.mpr* in the Desktop Modeler or Studio Pro.
3. Use the **Deploy for Eclipse** option (<kbd>F6</kbd>) and then import this connector as an Eclipse project to the Eclipse IDE.

## 7 Read More

* [How to Implement Best Practices for App Security](/howto/security/best-practices-security)
