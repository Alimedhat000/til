## installation

### Step 1: Make sure the system is up to date

Before installing PostgreSQL, it’s a good idea to make sure the operating system is up to date. To update our system, run this command:

```bash
sudo apt update && sudo apt upgrade
```

### Step 2: Install the PostgreSQL packages

After our system is up to date, we will install the packages for PostgreSQL.

```bash
sudo apt install postgresql postgresql-contrib libpq-dev
```

After installation is complete, let’s start the server using this command:

```bash
sudo systemctl start postgresql.service && systemctl status postgresql.service
```

If `postgresql` is active, you can press `Q` to quit the status screen and move on to the next step.

### Step 3: Setting up PostgreSQL

PostgreSQL is now running, but we have to configure it in order to be able to use it with our local Express applications.

#### 3.1 PostgreSQL roles
PostgreSQL authenticates via roles. A role is like a user, which is how we interact with the service. The default PostgreSQL installation has set up a `postgres` role that we can use. This is great, but that would mean having to switch to that role every time we wanted to do something with the database server.

Instead, we will set up our own role to avoid switching to the `postgres` role all the time.

#### 3.2 Creating a new role

We will be creating a new role with the same name as our Linux username. If you’re not sure of your Linux username, you can run the command `whoami` in your terminal to get it. Once you have that information ready, let’s create a role in PostgreSQL. The command to do so is:

```bash
sudo -i -u postgres createuser --interactive
```

Remember that we want the role name to be the same as our Linux user name and be sure to make that new role a superuser. Setting up a role like this means we can leverage “peer authentication” making using the local database very easy.

#### 3.3 Creating the role database

One other important step in setting up PostgreSQL is that each role must have its own database of the same name. Without it, the role we just created will not be able to log in or interact with PostgreSQL.

You can try to run `psql` now, but you will get an error that the database does not exist. Not to worry, let’s create one to fix this:

If your username has any capital letters, you must surround it in quotes when running the below command.

```bash
sudo -i -u postgres createdb <linux_username>
```

Now our role is fully set up: we’ve got `<role_name>` and that role has a database.

#### 3.4 Securing our new role

One important thing that we have to do is to set up a password for our new role so that the data is protected. Now that our role is set up, we can actually use it to administer PostgreSQL. All you have to do is enter this command to get into the PostgreSQL prompt:

```bash
psql
```

You should see the PostgreSQL prompt come up with the new role we just created, like so:

```sql
role_name=#
```

If you don’t see a similar prompt, then reach out in [our Discord server](https://discord.gg/V75WSQG) for some help. If you **do** see a similar prompt, then we can create a password for the role like so:

```sql
\password <role_name>
```

You’ll be prompted to enter a password and to verify it. Once you are done, the prompt will return to normal. Now, we will configure the permissions for our new role (note the semicolon at the end):

```sql
GRANT ALL PRIVILEGES ON DATABASE <role_database_name> TO <role_name>;
```

Remember that you should change the `<role_database_name>` and `<role_name>` (they should both be the same)! If you see `GRANT` in response to the command, then you can type `\q` to exit the prompt.

#### 3.5 Saving access information in the environment

After finishing our configuration, the last step is save it into the environment to access later.

In order to save our password to the environment, we can run this command:

```bash
echo 'export DATABASE_PASSWORD="<role_password>"' >> ~/.bashrc
```

Note here the name we’ve chosen for our environment variable: `DATABASE_PASSWORD`. Also, remember to update `<role_password>` in the command to what was set above!

Now, this variable lives in our environment for us to use. As the variable is new, we’ll want to reload the environment so that we can access it. To reload the environment, you can close and re-open your terminal or

```bash
source ~/.bashrc
```

Once that’s done, we can move to testing it out!



## Setting up database for app

### 1. Using cmd

Enter the PostgreSQL shell by running `psql` in your terminal. You can view the current dbs using the `\l` command. Let’s create a new db by running the following SQL statement:

```sql
CREATE DATABASE top_users;
```

`\l` again to see if the db was created. Now let’s connect to the db:

```sql
\c top_users
```

Verify that the `psql` prompt should be:

```sql
top_users=#
```

Now create a table and its columns to store `username` data:

```sql
CREATE TABLE usernames (
   id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
   username VARCHAR ( 255 ) 
);
```

Verify that the table has been created by running `\d`. You should see the following two tables in the output (we’ve skipped some output details for brevity):

```bash
|       Name       |   Type   |
+ -----------------+----------+
| usernames        | table    |
| usernames_id_seq | sequence |
```
#### Identity column

Wait a minute, what’s this `usernames_id_seq` thing?

The `GENERATED ALWAYS AS IDENTITY` clause is the culprit. It defined the `id` column as an [identity column](https://www.postgresql.org/docs/current/sql-createtable.html#SQL-CREATETABLE-PARMS-GENERATED-IDENTITY). PostgreSQL now automatically generates a value for this column. By default it starts at 1 and increments by 1 for each new row. Additionally, PostgreSQL implicitly creates `usernames_id_seq`, which is a sequence object, that keeps track of the next value to be used.

Woohoo, we now have a db and a table… a lonely table. Not for long:

```sql
INSERT INTO usernames (username)
VALUES ('Mao'), ('nevz'), ('Lofty');
```

Verify with:

```sql
SELECT * FROM usernames;
```


## 2. Using a script

You might have noticed how cumbersome it is to create a table and populate it with data. Luckily, we have the power of c(n)ode by our side, let’s automate it via a script. Create a new file `db/populatedb.js`.

```javascript
#! /usr/bin/env node

const { Client } = require("pg");

const SQL = `
CREATE TABLE IF NOT EXISTS usernames (
  id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  username VARCHAR ( 255 )
);

INSERT INTO usernames (username) 
VALUES
  ('Bryan'),
  ('Odin'),
  ('Damon');
`;

async function main() {
  console.log("seeding...");
  const client = new Client({
    connectionString: "postgresql://<role_name>:<role_password>@localhost:5432/top_users",
  });
  await client.connect();
  await client.query(SQL);
  await client.end();
  console.log("done");
}

main();
```

You can then run this script via `node db/populatedb.js`, or add it as a [script in package.json](https://stackoverflow.com/a/36433748).

Do note that the script is designed to be ran only once.

## Using node-postgres in Express

We can work with PostgreSQL in our Express application through [`node-postgres`](https://github.com/brianc/node-postgres) (or `pg` for short). It is a library that we’ll use to interface with the PostgreSQL db. Install it with:

```bash
npm install pg
```

We can then initialize it in our application with the necessary connection information. Create a `db` folder, and a new file `db/pool.js` with:

```javascript
const { Pool } = require("pg");

// All of the following properties should be read from environment variables
// We're hardcoding them here for simplicity
module.exports = new Pool({
  host: "localhost", // or wherever the db is hosted
  user: "<role_name>",
  database: "top_users",
  password: "<role_password>",
  port: 5432 // The default port
});
```

An alternative to defining the connection information is through a [Connection URI](https://node-postgres.com/features/connecting#connection-uri). You’ll likely be using connection URIs when connecting with a hosted database service. Here’s what it would look like based on the above properties:

```javascript
const { Pool } = require("pg");

// Again, this should be read from an environment variable
module.exports = new Pool({
  connectionString: "postgresql://<role_name>:<role_password>@localhost:5432/top_users"
});
```

#### Two ways of connecting with pg

`pg` has two ways to connect to a db: a client and a pool.

Client is an individual connection to the DB, which you manually manage. You open a connection, do your query, then close it. This is fine for one-off queries, but can become expensive if you’re dealing with a lot of queries. Wouldn’t this problem be alleviated if we could somehow hold onto a client? Yes!

Enter pool. As the name suggests, it’s a pool of clients. A pool holds onto connections. And when you query, it’ll programmatically open a new connection unless there’s an existing spare one. Perfect for web servers.

### Querying with pg

With our initialized `Pool`, we can use the `query` method. Create a new `db/queries.js` file. Upon revising our project requirements, we understand we need two db interactions: getting all usernames and inserting a new username. Let’s define these functions:

```javascript
const pool = require("./pool");

async function getAllUsernames() {
  const { rows } = await pool.query("SELECT * FROM usernames");
  return rows;
}

async function insertUsername(username) {
  await pool.query("INSERT INTO usernames (username) VALUES ($1)", [username]);
}

module.exports = {
  getAllUsernames,
  insertUsername
};
```

#### Parameterization

What’s with the `$1` in the insert query?

Alternatively, the query could look like:

```javascript
await pool.query("INSERT INTO usernames (username) VALUES ('" + username + "')");
```

We’re passing user entered value i.e. `username` directly into our query. A nefarious user could enter something like `sike'); DROP TABLE usernames; --` and wreak havoc. Scary stuff. This is called [SQL injection](https://en.wikipedia.org/wiki/SQL_injection).

`pg` provides [query parameterization](https://node-postgres.com/features/queries#parameterized-query) to prevent this. Instead of passing user input directly, we pass it in an array as the second argument. `pg` handles the rest.


#### Don't use `pool.query` inside a transaction?

A **transaction** in a database (like PostgreSQL) is a **sequence of operations** that are executed as a **single unit of work**. The key idea is that **either all the operations succeed together, or none of them do**. This ensures **data integrity** — especially important in situations like money transfers, inventory updates, etc.

##### Why not use `pool.query` inside a transaction?

Because when you use `pool.query`, you're asking the **pool to give you any available client**, and it might give a different one for each query. But transactions in PostgreSQL are **client-specific** — meaning all queries in the transaction **must go through the same client**.

If you do:
```js
pool.query('BEGIN'); 
pool.query('UPDATE accounts SET balance = balance - 100 WHERE id = 1'); 
pool.query('UPDATE accounts SET balance = balance + 100 WHERE id = 2');
pool.query('COMMIT');
```

Each of these might go to **a different client**, and the transaction will break.

##### Correct way: Use a **client** from the pool

```js
const client = await pool.connect();

try {
  await client.query('BEGIN');
  await client.query('UPDATE accounts SET balance = balance - 100 WHERE id = 1');
  await client.query('UPDATE accounts SET balance = balance + 100 WHERE id = 2');
  await client.query('COMMIT');
} catch (e) {
  await client.query('ROLLBACK'); // if anything goes wrong, undo changes
  throw e;
} finally {
  client.release(); // return the client back to the pool
}
```

Where `pool.connect()` Acquires a client from the pool. Then release it using `client.release()`.

Don't Forget to release after you are finished with it!