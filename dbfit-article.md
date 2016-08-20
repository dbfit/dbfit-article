DbFit

Introduction
============

DbFit is an open source tool for automated database testing. DbFit has
been initially created by Gojko Adzic with the goal to enable efficient
database testing – in order to motivate database developers to use an
automated testing framework. DbFit is based on FitNesse framework which
enables defining tests in readable and easy to maintain tabular format.

DBFit supports multiple relational databases, including Oracle, MS SQL Server,
MySQL, PostgreSQL and others.

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
within a suite and it's also possible to trigger tests execution via REST API, JUnit or a command line
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

This command can be placed directly into a test page or in a parent page in the test suite hierarchy.

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

## Basic cycle of a DbFit test and its execution

1. Set up the input data and other context. DbFit commands typically used at this stage: `Insert`, `Update`, `ExecuteDdl`, `Execute`. If common set up steps are used by several tests - it may be useful to keep them in `SetUp` page which will be automatically included in the beginning of the other tests in the suite.
2. Execute a database operation which we want to test (typically - a stored function or procedure, or an external process such as an ETL tool routine). Useful DbFit commands: `Execute Procedure`.
3. Verify the result - comparing the actual versus the expected result. Useful DbFit commands: `Query`, `Store Query`, `Compare Stored Queries`
4. Tear Down - clean up the changes in order to avoid affecting other tests. Pages named `TearDown` are automatically included at the end of all tests in the suite. In Flow Mode the active transaction is automatically rolled back at the end of the test page so often you won't need any explicit tear down steps (assuming you don't commit transactions in your tests).

Quick reference of the mostly used commands
===========================================

## Query

`Query` command allows you to compare the result of a SQL Query to a given expected output. You should specify query as the first fixture parameter, after the Query command. The second table row contains column names, and all subsequent rows contain data for the expected results. You do not have to list all columns in the result set — just the ones that you are interested in testing. Multiple columns and rows are supported.

    !|Query|select 'test1' as column_one, 'test2' as column_two from dual|
    |column_one |column_two|
    |test1      |test2     |

As output of the `Query` command execution, matches are marked in green and mismatches are highlighted in red. In case of any mismatch - the test is counted as `failing`. Missing or unexpected surplus records are also being reported as mismatch.

`Ordered Query` is a variation of `Query` command which takes into account the exact ordering of rows.

## Store Query, Compare Stored Queries

`Store Query` reads out query results and stores them into a Fixture symbol for later use. Specify the full query as the first argument and the symbol name as the second argument (without >>). You can then use this stored result set as a parameter of the Query command later:

    !|Store Query|select n from (select 1 as n from dual union select 2 from dual union select 3 from dual)|firsttable|

    !|Query|<<firsttable|
    |n                  |
    |1                  |
    |2                  |
    |3                  |

`Compare Stored Queries` compares two previously stored query results. Column structure is specified so that some columns can be ignored during comparison (just don’t list them), and for the partial row-key mapping to work. Put a `?` after the column names that do not belong to the key to make the comparisons better.

    |Store Query|select n, name from testtbl|fromtable|

    |Store Query|!- select 1 n, 'name1' name from dual|fromdual|

    |Compare Stored Queries|fromtable|fromdual|
    |name                  |n?                |

Typically you should strive for fast and focused tests, comparing just a small amount of records. Nevertheless, in some special cases it may be desired to run `DbFit` on top of relatively large number of rows. Please refer to `DbFit` reference guide for some guidelines how to handle such scenarios more efficiently.

## DML and DDL statements

### Insert
`Insert` command allows inserting a set of records into a database table (or view). The view or table name is given as the first fixture parameter. The second row contains column names, and all subsequent rows contain data to be inserted. For example:

    !|Insert|testtbl|
    |id     |name   |
    |1      |NAME1  |
    |3      |NAME3  |
    |2      |NAME2  |

### Update

`Update` allows you to update specified records in a database table or view. Columns ending with `=` are used to update records. Columns without `=` are used to select rows. The following example updates the `name` column where the `id` matches 1 and 2.

    !|Update  |testtbl|
    |name=    |id     |
    |New Name1|1      |
    |New Name2|2      |

You can use multiple columns for both updating and selecting, and even use the same column for both operations.

### Execute and Execute Ddl

`Execute` executes SQL statement provided using the syntax relevant for the database. You can use query parameters in the DB-specific syntax (eg. @paramname for SQLServer and MySQL, and :paramname for Oracle). Currently, all parameters are used as inputs, and there is no option to persist any statement outputs.

    !|Execute|alter session set ddl_lock_timeout=600|

    !|Set Parameter|name|Darth Maul|

    !|Execute|insert into test_table(name, age) values (:name, 10)|


`Execute Ddl` is intended for executing DDL SQL statements (like `create`, `alter, `drop`). It’s similar to Execute with the main difference that it does not support bind variables, and also automatically handles required post-execute activities, if any (e.g. Teradata requires transaction to be closed after each DDL statement).


    !|Execute Ddl|create table tab_with_trigger(x int)|

    !|Execute Ddl|create or replace trigger trg_double_x before insert on tab_with_trigger for each row begin :new.x := :new.x * 2; end;|

    !|Insert|tab_with_trigger|
    |x                       |
    |13                      |

    !|Query|select x from tab_with_trigger|
    |x                                    |
    |26                                   |


## Parameters and fixture symbols

DbFit enables you to use Fixture symbols as global variables during test execution, to store or read intermediate results. In order to store a parameter from a query - you can use `>>parameter` syntax, and `<<parameter` to read the value. In addition, you can use the `Set Parameter` command to explicitly seta parameter value to a string.

    !|Set Parameter|ONE|1|

    !|Query|select sysdate mytime from dual|
    |mytime?                               |
    |>>current_time                        |

    !|Query|select to_char(count(*)) as cnt from dual where sysdate >= :current_time|
    |cnt                                                                            |
    |<<ONE                                                                          |


Contributing to the project
===========================

Check out the project on Github and the published CONTRIBUTING guidelines. The project is open for contributors, you may submit
your issue, pull request to Github or ask a question in the mailing list.


Some limitations
================

* User defined abstract database types are not fully supported.

* The declarative syntax might be insufficient if you need low level control in your tests,
coding some logic, testing behaviour of multiple concurrent connections: DbFit syntax may be insufficient. For more
complicated scenarios you may need to develop your custom fixtures or to use other tools for the purpose.

* Not intended for performance testing. (Though there are some guidelines if you need to compare larger datasets).

* FIT runner is required, Slim is not supported.

Some Benefits
=============

* Support for multiple database types (Oracle, SQL Server, etc.)

* Readable and easy to understand syntax. May enhance communication with non-technical people

* Open-source, open for extension, contribution, suggestions and feedback

* An active community mailing list where you can get support from other users and contributors

* Online documentation with examples enabling you to easily get started

As a general conclusion, when appropriately used DbFit can help improving the quality, design and maintainability of your product. It enables practices like Test-Driven Development, refactoring. DbFit tests may serve as living executable documentation of your system behavior.

