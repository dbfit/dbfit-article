DbFit

Introduction
============

DbFit is an open source tool for automated database testing. DbFit has
been initially created by Gojko Adzic with the goal to enable efficient
database testing – in order to motivate database developers to use an
automated testing framework. DbFit is based on FitNesse framework which
enables defining tests in readable and easy to maintain tabular format.

Web site: <http://dbfit.github.io/dbfit/>

Version tested: 3.2.0

License and Pricing: Open Source, GPL v2

Support: dbfit google group (<http://groups.google.com/group/dbfit>) and
GitHub issue tracker (<https://github.com/dbfit/dbfit/issues>)


Getting Started
===============

## Installation

In order to run DbFit you need Java Runtime Environment (JRE) 7 or
higher which you may get from Oracle's web site. Also you’ll need
to ensure the database accounts and connectivity to the databases
which you want to access with DbFit.

To install DbFit you can download it from DbFit web site
<http://dbfit.github.io/dbfit/> unpack the archive and run
startFitnesse.sh or startFitnesse.bat (depending on your operating
system). It may take it a few seconds to start the server and then you
can access it using your browser at <http://localhost:8085/>.

## Creating a new test page

Open <http://localhost:8085/HelloWorldTest> in your browser. You should see an
editor where you can create and run your test page.

Test pages are described in FitNesse Wiki syntax which is essentially a simple
text format for describing commands in terms of tables. The premise is that this
notation is close to relational model, and you don't need to know Java or other
lower-level programming language to create DbFit tests.

Test pages may be grouped in suites which may be organized in hierarchical structure. The
names of test pages and suites should be CamelCase.

Although FitNesse Wiki syntax is really simple, you do not have to use it to write scripts. You can write your tables in Excel (or almost any other spreadsheet program), and then just copy or import them into the FitNesse page editor.

Test pages are stored on the file system in simple text format. You can create and edit them directly
using your favorite text editor.


## Running tests

To run a test page you can click `Test` test button in FitNesse. It's possible to run all tests
within a suite and it's also possible to trigger tests execution via REST API or via JUnit,
allowing you to automate DbFit tests execution and integration with a CI server.

## Configuration

For details about configuring different port number, project folder
or other settings you may consult FitNesse documentation.

Due to licensing restrictions the drivers of some of the supported databases
are not being shipped with DbFit so you'll need to also provide the required
JDBC driver jar files by copying them into DbFit lib folder.

In order to load the DbFit extension into FitNesse, your test pages have to load
the correct libraries by including the following command:


    !path lib/*.jar

Connecting to the database
==========================

DbFit supports two modes of operation: Flow and Standalone. By default, each individual
test page in flow mode is executed in a transaction that is automatically rolled back after
the test. In standalone mode, you are responsible for overall transaction control. Most of the
commands are common for both modes but there are some limitations in standalone mode and also
the syntax for opening a connection differs.

Here is how to connect to a MySQL database in flow mode:

    !|dbfit.MySqlTest|

    !|Connect|localhost|dbfit_user|password|dbfit|

Notice the `MySqlTest` in the first line above. That tells DbFit which type of database driver to
use. The second line defines connection properties. These two lines will typically be the first
on every test page.

And here is how to open a connection in standalone mode:

    |import fixture|
    |dbfit.fixture|

    !|DatabaseEnvironment|MySql|
    |Connect|localhost|dbfit_user|password|dbfit|


There is support for configuring the connection details in separate file. Encryption of the database
password is also supported so that you can avoid storing it in plain text.

## Supported databases

Following database systems are currently supported by DbFit: Oracle, SQL Server, MySQL, Postgres, Derby, HSQLDB, DB2, DB2i, Teradata, Netezza, Informix.

Quick reference of the mostly used commands
===========================================

## DML and DDL statements

## Query, Copmpare Stored Queries

## Working with parameters

Some limitations
================

* User defined abstract database types are not fully supported.

* The declarative syntax might be insufficient if you need low level control in your tests,
coding some logic, testing behaviour of multiple concurrent connections: DbFit syntax may be insufficient. For more
complicated scenarios you may need to develop your custom fixtures or to use other tools for the purpose.

* Not intended for performance testing. (Though there are some guidelines if you need to compare larger datasets).

* FIT runner is required, Slim is not supported.


Contributing to the project
===========================

Check out the project on Github and the published CONTRIBUTING guidelines. The project is open for contributors, you may submit
your issue, pull request to Github or ask a question in the mailing list.
