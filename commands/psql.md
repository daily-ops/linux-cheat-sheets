### Login to a database

```
psql -U <USER> -h <HOST> -p <PORT> -d <DATABASE>
```

### Update system settings

```
ALTER SYSTEM SET <property name>=<value>
```

### Grant permissions

Super user

```
GRANT ALL PRIVILEGES ON DATABASE A_DATABASE TO SUPER_USER;
```

Read-only

```
GRANT USAGE ON SCHEMA public TO A_READONLY_USER;
```

|Privilege|	Abbreviation|	Applicable Object Types|
|-|-|-|
|SELECT|	r (“read”)|	LARGE OBJECT, SEQUENCE, TABLE (and table-like objects), table column|
|INSERT|	a (“append”)|	TABLE, table column|
|UPDATE|	w (“write”)|	LARGE OBJECT, SEQUENCE, TABLE, table column|
|DELETE|	d|	TABLE|
|TRUNCATE|	D|	TABLE|
|REFERENCES|	x|	TABLE, table column|
|TRIGGER|	t|	TABLE|
|CREATE|	C|	DATABASE, SCHEMA, TABLESPACE|
|CONNECT|	c|	DATABASE|
|TEMPORARY|	T|	DATABASE|
|EXECUTE|	X|	FUNCTION, PROCEDURE|
|USAGE|	U|	DOMAIN, FOREIGN DATA WRAPPER, FOREIGN SERVER, LANGUAGE, SCHEMA, SEQUENCE, TYPE|
|SET|	s|	PARAMETER|
|ALTER SYSTEM|	A|	PARAMETER|
|MAINTAIN|	m|	TABLE|

##### Reference

https://www.postgresql.org/docs/current/ddl-priv.html


|--|--|
|--|--|
|\l or \l+| List all databases|
|\du or \du+| List all users|
|\dp| List sequences|
|\dp| List tables|
|\db+| List tablespaces|
|\dn+| List schemas|
|\df+| List functions/procedures|


### EXPLAIN

The EXPLAIN command in PostgreSQL is an essential tool for analyzing and optimizing SQL queries. By providing insights into the query execution plan, EXPLAIN shows how PostgreSQL processes a query, helping identify performance bottlenecks and inefficiencies. When combined with the ANALYZE keyword, EXPLAIN provides real execution times, making it even more powerful for troubleshooting slow queries and fine-tuning database performance.

#### Syntax:

```
EXPLAIN [ANALYZE] [VERBOSE] <query>;
```

Here:

`ANALYZE`: Executes the query and provides actual runtime statistics.

`VERBOSE`: Adds more detailed information about the execution plan.

#### Example Usage of EXPLAIN in PostgreSQL

##### Example 1: Basic EXPLAIN Usage

Suppose you want to understand the execution plan for a simple SELECT query:

Code:

```
EXPLAIN SELECT * FROM orders WHERE customer_id = 123;
```

Explanation:

This command shows the execution plan for fetching records from the orders table where customer_id is 123.
The output reveals details like the type of scan used (e.g., sequential scan or index scan) and the estimated cost.

##### Example 2: Using EXPLAIN ANALYZE for Real Execution Stats

To get a more accurate view of the query’s performance, you can add ANALYZE:

Code:

```
EXPLAIN ANALYZE SELECT * FROM orders WHERE customer_id = 123;
```

Explanation:

This query not only displays the execution plan but also runs the query to gather actual runtime statistics.
The output includes details on the time taken at each stage, making it ideal for in-depth performance analysis.

##### Example 3: Detailed Execution Plan with VERBOSE

For even more insights, you can include the VERBOSE keyword:

Code:

```
EXPLAIN VERBOSE ANALYZE SELECT * FROM orders WHERE customer_id = 123;
```

Explanation:

With VERBOSE, the command provides additional details about the structure and flow of the execution plan, such as column and table specifics.
This level of detail is helpful when debugging complex queries or working with views.

Understanding the EXPLAIN Output:

The EXPLAIN output includes several key metrics:

- Seq Scan / Index Scan: Shows the type of scan (e.g., sequential or index).
- Cost: An estimate of the computational cost, with the format startup cost..total cost.
- Rows: The estimated number of rows expected to be returned.
- Width: Average size of each row in bytes.
- Actual Time (when ANALYZE is used): Shows the time taken for each step in milliseconds.

Key Concepts in Query Plans:

- Sequential Scan: A full table scan, generally slower for large tables.
- Index Scan: Faster for selective queries, leveraging an index.
- Nested Loop, Hash Join, Merge Join: Join types that PostgreSQL chooses based on data and index availability.
