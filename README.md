Intro to PostgreSQL
===================

## Overview

In this lesson, we will introduce you to the PostgreSQL database.

## Objectives

By the end of this lesson you will be able to:
1. Explain the key differences between SQLite and PostgreSQL.
2. Discuss with a client why they might want to use PostgreSQL over SQLite.
3. Install PostgresSQL on your system.
4. Use the command line and the psql interactive terminal to perform basic operations on your database.

## What is PostgreSQL?

PostgreSQL is a database. But it's not just any database: it is among the most widely used, highly regarded, and robust database solutions in existence, so we'll be getting to know a real powerhouse technology here, a technology that you will very likely run into again and again throughout your career.

So it's powerful and well-known, but what kind of database is it? This is a good question. As technologists, we shouldn't just be impressed by the popularity of a technology. All technologies have specific charactersitics, specific strengths and weaknesses. To answer this question, we'll place PostgreSQL in the universe of database technologies, and then compare it to the database that we've most commonly been using: SQLite.

Currently, we have two main types of databases that we can use: Relational (SQL) database systems, and document-based NoSQL database systems.

Relational databases, which have been around since the 1970s, are very popular because they use models to define with great specificity the way data is related. This makes it possible to query data in complex ways to answer different kinds of questions. It also places useful limits on how data can be entered into the system, ensuring data consistency and integrity. By contrast, NoSQL databases such as MongoDB, remove some of the limits imposed by maintaining relational rules. These databases have also been around for a long time, but have only recently become popular because of the advantages in speed, scaling and flexiblity they can sometimes offer in the context of real-time live web applications. This is all you really need to know for now. If you want to dig a bit deeper, you can take a look at [this](https://www.digitalocean.com/community/tutorials/understanding-sql-and-nosql-databases-and-different-database-models) nice explanation written up by the people at DigitalOcean.

So where does Postgres fit here? Well, Postgres is absolutely an example of a relational database. In fact, it is arguably the standard-bearer of this type of database because, among all the prominent relational database systems out there, it is *the* one that most fully implements the international standards for this kind of database. It is not necessarily the most widely used relational database -- that honor goes to MySQL -- but it is the most robust and standards-compliant. Because it is so compliant and because its functionality so fully developed, it is therefore a good database to recommend to our clients when what they need is a high level of data integrity and reliability.

## How Does PostgreSQL Compare to SQLite?

That then is how we'd define PostgreSQL on the global level among the whole universe of database solutions. But how does it compare with the SQLite? Here the comparison is a bit more detailed. Both are relational databases, and both follow the SQL standard, and that makes designing data structures within them and querything them a similar sort of experience, but beyond that a number of significant differences exist.

Probably the most striking difference between them, is that SQLite is a file-based database. When you query SQLite, it manipulates and fetches data by making direct calls to a file holding the data. That file, essentially, *is* the SQLite database. Postgres works very differently: it fetches data through an interface of ports and sockets that connect to a database server that must be installed in the environment in which the application is running.

The fact that SQLite is just a file makes it an insanely lightweight and fast solution. So SQLite is a great technology to use when what is needed is speed and portability, and when the application in question is relatively simple. When, however, an application is larger; when, in particular, multiple parties (or users) may need to access the database at the same time, then SQLite begins to show its limitations. Because the SQLite database is just a file there's no mechanism for maintaining multiple database users who may be accessing the database at the same time. In SQLite there's just one user and only one write process can take place at one time. For many large-scale applications, this limitation is simply prohibitive. In this scenario, many software engineers will turn to a full-fledged relational database like Postgres.

## Installing PostgreSQL

Now that we have an idea about the characteristics of Postgres, let's get it installed. Yes it needs to be installed! Here, of course, we encounter one of the limitations of Postgres compared to SQLite. It's definitely a bit more of an involved process to get it going on our systems.  Nonetheless, we can do it!

One of the reason that installing Postgres can be complex is that it depends on your environment. Setup and configuration, in general, is often frustrating because of the idiosyncacies of each environment. But we'll go through some methods here that should be pretty foolproof.

Before we get started, though, we need to understand the overall process here since each of the installation procedures is aiming at the same goal. In order to complete our installation, we need to achieve the following:

1. Install the database system itself.
2. Ensure that the database server can run (automatically, in most cases).
3. Create a user to match your default user account so that you can access the db directly from your own account.

Note that the last step (#3), involves adding a new database user. Now you see more clearly perhaps what makes Postgres different than SQLite. We need to set up and manage different users in the database. When you install the database, unless something has gone wrong, you a base user with broad privileges is automatically created. This user is almost always called `postgres`. What we will do in Step #3 above is create an additional user with broad privileges that corresponds to your main user account on your operating system.

Okay, let's get started. There are instructions here for Ubuntu and Mac:

**Step #1: Install the Postgres Database System:**

If you are using Ubuntu, this step will go a bit more smoothly than on Mac. On Ubuntu, all you need to do is use the package manager `apt-get` to install postgres, like so:

```bash
$ sudo apt-get update
$ sudo apt-get install postgresql postgresql-contrib
```

At this point, if you are doing an Ubuntu installation, you've basically completed steps #1 and #2. The database should be install and running as a background process that will start when your computer boots up. So, you can skip ahead to Step #3 below.

If you are on a Mac and using brew to install postgres this will take a bit long. To start, you can install the database like so:

```bash
$ brew update
$ brew install postgresql
```

Once this process has completed, you will see some output that indicates that you must take a few additional steps to actually create the database and get it running. If we were to try to start the psql interactive command line now, we'd get an error that looks like the following, indicating that the database server does not yet exist or is not yet running:

```bash
psql: could not connect to server: No such file or directory
Is the server running locally and accepting
connections on Unix domain socket "/tmp/.s.PGSQL.5432"?
```

In our case, both are true: the database does not yet exist; hence it is also not running. So in order to create the database, we need to run this command:

```bash
$ initdb /usr/local/var/postgres -E utf8 -U postgres
```

Note the `-U postgres` option is specifying that the default user should be `postgres`. Once this process completes, your postgres database will be created, but it is still not running! When you ran this command, however, you should have seen a bunch of output, ending with the following instruction regarding how you can start the database:

```bash
Success. You can now start the database server using:

    pg_ctl -D /usr/local/var/postgres -l logfile start
```

So this is our penultimate step, run: `pg_ctl -D /usr/local/var/postgres -l logfile start`. This should start the database running in the background. To test this out, now try to launch the interactive command line with `psql`. Success would mean that you see the following:

```bash
psql: FATAL:  role "<your username>" does not exist
```

Despite the error here, if we see this when we run the `psql` command, we are doing well. This error is just stating that there is no user created for our currently logged-in system user account. This is what we'll take care of in Step #3.

For now, what we need to do to finshing things off is to setup postgres to start every time we bootup our machine. To do this we need to do the following:

```bash
mkdir ~/Library/LaunchAgents    # This directory may already exist, in which case this command is not needed.
cp /usr/local/Cellar/postgresql/9.5.3/homebrew.mxcl.postgresql.plist ~/Library/LaunchAgents/
```

What the above does is place the process file that can start the Postgres process into a directory containing other such files that your Mac system will run on startup. To test that this is working, we should now restart our Mac system, log back in, and try the `psql` command again. If things are still working, we should see the error from above.

Okay, almost there. Onward!

**Step #2: Creating Our Root User**

If we've reached this stage, we now have the Postgres database installed and running in the background. We've not yet sucesfully loaded the psql command line interface, but that's what we'll get working now.

Our first step is to see if we can log into the default user account -- `postgres`. To do this, we'll run the `psql` command again, only this time we'll specify the user:

```
psql -U postgres
```

This should bring you into a command line context that looks like:

```bash
psql (9.3.13)
Type "help" for help.

postgres=#
```

> Note: The version number that you see at the top of the initial prompt could differ. You might see 9.5.1, for example. These differences should be okay..

Just to start finding our way around here, let's try out a few commands. What if we want to look at all the databases that currently exist. The command for that is `\l`. Try that and you should see a list of tables. These are just default tables. We don't need to know what they are for at the moment.

Now let's try examining which users are registered on the database. In Postgres, user's are listed in a publicly visible "view" (again we don't need to know what a view is yet) that we can query using a simple SQL statement: `select * from pg_user`. This command simply asks to see all the columns and rows in the `pg_user` view. You should see output like this:

```bash
 usename  | usesysid | usecreatedb | usesuper | userepl | usebypassrls |  passwd  | valuntil | useconfig
----------+----------+-------------+----------+---------+--------------+----------+----------+-----------
 postgres |       10 | t           | t        | t       | t            | ******** |          |
(1 row)
```

As you can see, at the moment there's only one user: `postgres`, and that's as it should be. But let's add another! There are a couple of way of doing this. We could, for example, use a SQL command like `CREATE USER <username>` to create our user, but let's instead use some convenience commands that we can issue from our system shell instead.

To get back to the shell, let's quit the psql interface by typing `\q`. Once you are back at the command line, let's use the postgres user to create an additional user that will be the same as your system user account. Since the `postgres` user is a superuser with broad privileges to create and add databases as well as new users, we can use this account to create the new user.

To do this run the command `createuser -U postgres --interactive`. Once we run it, this command will ask us for the name of the "role" (i.e. user), and then ask if we want the user to be a "superuser". You should enter your main username, and say yes to the superuser option.

Once that is done, you'll need to issue one last command to create a user table for the new user. (In Postgres, each user has a database). To do that issue this follwing command: `createdb -U postgres <username>`.

Now, we should have created a new user with superuser privileges. This means that we can now run Postgres command line commands without including the `-U postgres` option. To test this out, just issue the `psql` command all by itself. If everything is working, this should take you directly into the command line, no questions asked!

Congrats! Your database is setup.

## Getting SQL Warmed UP

Now that we've configured our postgres database, let's do a litte playing around just to get oriented. We've included in this lesson directory an sql file filled with dummy data. Let's create a test database, import the data, and test out a query. To create our test database and import the data, do the following:

```bash
$ createdb test
$ psql test < users.sql
```

At this point, we should have a database `test` that contains a single table `users`. Let's start up the psql interface and examine our data. Begin, of course, by typing `psql`. Now, let's check that our database is there by typeing `\l` to list the databases. We should now see the `test` database included in our list.

Next, let's connect to the database, and look around. To connect to our database type `\connect test` and hit enter. Then, once, you've done that, type `\d` to get a list of the tables in the test database. We should see just one:

```
       List of relations
 Schema | Name  | Type  | Owner
--------+-------+-------+-------
 public | users | table | ethan
```

Now, in order to query our table, let's examine what columns this table includes. To discover the structure of the table, type `\d users` and hit enter:

```
              Table "public.users"
   Column   |         Type          | Modifiers
------------+-----------------------+-----------
 id         | integer               |
 first_name | character varying(50) |
 last_name  | character varying(50) |
 email      | character varying(50) |
 gender     | character varying(50) |
 ip_address | character varying(20) |
```

Okay, so now we see our options. Let's think of a good question. One obvious one, could be about gender. Let's say we want to know how many male users we have. How would we do that? Well, we'd want to count the number of user rows where the gender attribute is set to "Male". In SQL, that looks like:

```
SELECT COUNT(*) FROM users WHERE gender = 'Male';
```

Paste the above sql statement into the psql interface and hit enter. And Wa-Lah, we should have our answer:

```
 count
-------
   489
(1 row)
```

Now this kind of query is obviously pretty basic, but it should get us started. And already, perhaps, you can see how powerful this database is that we've setup. It will definitely serve us well!

## Resources
* PostgreSQL documentation: https://www.postgresql.org/docs/
* ["Understanding SQL And NoSQL Databases And Different Database Models"](https://www.digitalocean.com/community/tutorials/understanding-sql-and-nosql-databases-and-different-database-models)

<p class='util--hide'>View <a href='https://learn.co/lessons/node-js-intro-to-postgresql'>Intro To PostgreSQL</a> on Learn.co and start learning to code for free.</p>
