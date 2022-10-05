- [How to use Database](#how-to-use-database)
  - [PostgreSQL](#postgresql)
    - [Install](#install)
    - [Admin user](#admin-user)
    - [psql shell](#psql-shell)
    - [Common user](#common-user)
    - [VS Code](#vs-code)
  - [psycopg2](#psycopg2)
    - [Install](#install-1)
    - [connection class](#connection-class)
    - [cursor class](#cursor-class)
    - [Autocommit](#autocommit)
    - [SQL](#sql)
      - [Create](#create)
      - [Insert](#insert)
      - [Query](#query)
      - [Update](#update)
      - [Delete](#delete)
# How to use Database
There are two types of database: SQL database and NoSQL database. SQL and NoSQL are complementary to satisfy kinds of demands for data adminstration. The brief descriptions on SQL and NoSQL refer to [What is SQL and NoSQL? - Part1](https://cloud.tencent.com/developer/article/1132555) and [What is SQL and NoSQL? - Part2](https://cloud.tencent.com/developer/article/1132589).

## PostgreSQL
### Install
I installed postgres guided by the [official document](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-database#install-postgresql).

> To install PostgreSQL on WSL (ie. Ubuntu):
> 
> 1. Open your WSL terminal (ie. Ubuntu).
> 2. Update your Ubuntu packages: `sudo apt update`
> 3. Once the packages have updated, install PostgreSQL (and the -contrib package which has some helpful utilities) with: `sudo apt install postgresql postgresql-contrib`
> 4. Confirm installation and get the version number: `psql --version`

Along with the guide above, we have installed PostgreSQL. There are 3 commands we need to know once PostgreSQL is installed:

- `sudo service postgresql status` to check the status of the database.
- `sudo service postgresql start` to start running the database.
- `sudo service postgresql stop` to stop running the database.

### Admin user
The default admin user, `postgres`, needs a password assigned in order to connect to a database. To set a password:

1. Enter the command: `sudo passwd postgres`
2. We will get a prompt to enter your new password.
3. Close and reopen our terminal.

To see what user accounts have been created on your PostgreSQL installation, use from your WSL terminal: `psql -c "\du"` ...or just `\du` if you have the psql shell open. This command will display columns: Account User Name, List of Roles Attributes, and Member of role group(s). To exit back to the command line, enter: `q`.

### psql shell
To run PostgreSQL with psql shell:

1. Start our postgres service: `sudo service postgresql start`
2. Connect to the postgres service and open the psql shell: `sudo -u postgres psql`
3. Once we have successfully entered the psql shell, we will see our command line change to look like this: `postgres=#`

> `Note`: Alternatively, you can open the psql shell by switching to the postgres user with: `su - postgres` and then entering the command: `psql`.

To exit postgres=# enter: `\q` or use the shortcut key: Ctrl+D

### Common user
Though we have admin user, it not a wise idea to use admin user everywhere. We better create an role for ourselves and create databases for our role so that we can add, delete, update and query data for ourselves in these databaes and avoid any  potential destructive operation to the whole database. Now we will cerate a database user and a linux user with the same name for ourselves and use linux user to administer our databases as the role of linux user.

Open the psql shell:
```
sudo -u postgres psql
```

Create a database user:
```
create user <user_name> with password 'passwd';
```

Create a database:
```
create database <database_name> OWNER <user_name>;
```

Grant all privileges on the database to the user:
```
GRANT ALL PRIVILEGES ON DATABASE <database_name> TO <user_name>;
```

Exit back to the command line:
```
\q
```

Create a linux user:
```
# The name of linux user must be the same as the name of database we have crteated.

sudo adduser <user_name>

sudo passwd <user_name>
```

Connect the database <database_name> as the role <user_name>
```
su - <user_name>

psql -d <database_name>
```

### VS Code
To work with with PostgreSQL databases in VS Code, try the [PostgreSQL extension](https://marketplace.visualstudio.com/items?itemName=ms-ossdata.vscode-postgresql).

## psycopg2
This part is translated from an [essay](https://blog.csdn.net/u011304970/article/details/72771775) in CSDN in the assumption that we have installed and configured PostgreSQL.

### Install
Install psycopg2:
```
apt-get install python-psycopg2
```

Import psycopg2:
```
import psycopg2
```
### connection class
Create a connection class:

psycopg2.connect() create a new session in database and return a connection class.
```
conn = psycopg2.connect(dbname="databae_name", user="user_name", password="passwd", host="127.0.0.1", port="5432")
```

The common methods we use in connection:

- commit() to commit transaction to database;
- rollback() to roll back;
- close() to close database.

### cursor class
Create a cursor class

psycopg2 provides a cursor class to execute commands in session. connection.cursor() creates a cursor class.

```
cur = conn.cursor()
```

The common methods we use in cursor:
- execute(query, vars=None) to execute SQL;
- fetchall() to fetch all outputs of querys and return values as a list containing tuples.

Once we have the cursor class, we could take operation in a database.

### Autocommit
Make autocommit to be true so that each action is committed without having to call conn.commit() after each command. 
```
conn.set_session(autocommit=True)
```

### SQL
#### Create
```
cur.execute(
        'CREATE TABLE Employee ('
        'name    varchar(80),'
        'address varchar(80),'
        'age     int,'
        'date    date'
        ')'
    )
```

#### Insert
```
cur.execute("INSERT INTO Employee "
        "VALUES('Gopher', 'China Beijing', 100, '2017-05-27')")
```

#### Query
```
cur.execute("SELECT * FROM Employee")
rows = cur.fetchall()
for row in rows:
    print('name=' + str(row[0]) + ' address=' + str(row[1]) + 
        ' age=' + str(row[2]) + ' date=' + str(row[3]))
```

#### Update
```
cur.execute("UPDATE Employee SET age=12 WHERE name='Gopher'")
```

#### Delete
```
cur.execute("DELETE FROM Employee WHERE name='Gopher'")
```