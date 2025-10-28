# Database Concepts

In short:

* **Driver**: communication interface.
* **Connection**: active link.
* **Pool**: reusable set of links.


### Connection
A live link between an application and a database server. It represents an active session where SQL queries can be executed. Each connection consumes resources (memory, threads, network sockets). Creating and closing them repeatedly is expensive.

### Connection Pool
A cache of pre-established database connections maintained for reuse. Instead of opening a new connection each time, the application borrows one from the pool and returns it when done.

* Reduces latency from connection creation.
* Controls maximum concurrent connections to prevent overload.
* Enables better concurrency management.

### Driver
The software interface that lets the application communicate with the database. It translates generic API calls (e.g., from Node.js, Java, Python) into database-specific protocol commands.

Examples:

* `mysql2` or `pg` in Node.js
* `JDBC` in Java
* `psycopg2` in Python for PostgreSQL

### Connection String / DSN (Data Source Name)
A string specifying how to connect to a database. It includes:

* Hostname or IP
* Port
* Database name
* User credentials
* Optional parameters like SSL or timeout
  Example:
  `mysql://user:password@localhost:3306/mydb`

### Connection Pool Configuration
Common parameters:

* `max`: maximum concurrent connections in the pool
* `min`: minimum connections kept open
* `idleTimeout`: time a connection stays open when idle
* `acquireTimeout`: how long to wait for a free connection before failing

### Transaction

A group of multiple operations as a single unit of work that ensures atomicity (all succeed or none). Requires maintaining a single connection during the transaction lifecycle.

### ORM (Object Relational Mapper)

A layer on top of drivers that maps database tables to code objects (e.g., Sequelize, TypeORM). Handles connections internally and provides abstraction over raw SQL.

### Connection Lifecycle

1. Driver uses connection string to establish connection.
2. Pool manages multiple connections.
3. Application acquires and releases connections for queries.
4. When idle, the pool may close unused connections.
