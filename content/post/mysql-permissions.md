+++
date = 2017-03-12
title = "An Introduction to MySQL Permissions"
draft = true
tags = []
+++

> Original material from [Ian Gilfillan](http://www.databasejournal.com/features/mysql/article.php/3311731/An-introduction-to-MySQL-permissions.htm)

*With great power comes great responsibility, and a terrible interface.*

Not always true but some things require me to constantly refer back to the manual, thus the creation of this page. This is a quick cheat-sheet-esque reference with a peppering of explanation where helpful for how to use the [MySQL Command-Line Tool](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) to configure database permissions.

# Version

    $ mysql -u root -p -e 'SHOW VARIABLES LIKE "%version%";'
    +-------------------------+-------------------------+
    | Variable_name           | Value                   |
    +-------------------------+-------------------------+
    | innodb_version          | 5.7.17                  |
    | protocol_version        | 10                      |
    | slave_type_conversions  |                         |
    | tls_version             | TLSv1,TLSv1.1           |
    | version                 | 5.7.17-0ubuntu0.16.04.1 |
    | version_comment         | (Ubuntu)                |
    | version_compile_machine | x86_64                  |
    | version_compile_os      | Linux                   |
    +-------------------------+-------------------------+

# Login

    $ mysql -uroot -p
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 11
    Server version: 5.7.17-0ubuntu0.16.04.1 (Ubuntu)

MySQL access is controlled by the `mysql` database.

    mysql> USE mysql;
    Database changed

    mysql> SHOW TABLES;
    +---------------------------+
    | Tables_in_mysql           |
    +---------------------------+
    | ...                       |
    | user                      |
    +---------------------------+

# The `user` Table

When a user tries to connect to the database, MySQL checks that that particular *username / host / password* combination has permission to connect. Once the connection has been made, before any operations are carried out, MySQL again checks to see whether the user/host combination has the right level of access to carry out that operation. The user table is the first table MySQL checks. All user/host/password combinations must be listed in this table before any access can be granted. Let's look at the table in more detail:

    mysql> DESC user;
    +------------------------+-----------------------------------+------+-----+-----------------------+-------+
    | Field                  | Type                              | Null | Key | Default               | Extra |
    +------------------------+-----------------------------------+------+-----+-----------------------+-------+
    | Host                   | char(60)                          | NO   | PRI |                       |       |
    | User                   | char(32)                          | NO   | PRI |                       |       |
    | Select_priv            | enum('N','Y')                     | NO   |     | N                     |       |
    | Insert_priv            | enum('N','Y')                     | NO   |     | N                     |       |
    | Update_priv            | enum('N','Y')                     | NO   |     | N                     |       |
    | Delete_priv            | enum('N','Y')                     | NO   |     | N                     |       |
    | Create_priv            | enum('N','Y')                     | NO   |     | N                     |       |
    | Drop_priv              | enum('N','Y')                     | NO   |     | N                     |       |
    | Reload_priv            | enum('N','Y')                     | NO   |     | N                     |       |
    | Shutdown_priv          | enum('N','Y')                     | NO   |     | N                     |       |
    | Process_priv           | enum('N','Y')                     | NO   |     | N                     |       |
    | File_priv              | enum('N','Y')                     | NO   |     | N                     |       |
    | Grant_priv             | enum('N','Y')                     | NO   |     | N                     |       |
    | References_priv        | enum('N','Y')                     | NO   |     | N                     |       |
    | Index_priv             | enum('N','Y')                     | NO   |     | N                     |       |
    | Alter_priv             | enum('N','Y')                     | NO   |     | N                     |       |
    | Show_db_priv           | enum('N','Y')                     | NO   |     | N                     |       |
    | Super_priv             | enum('N','Y')                     | NO   |     | N                     |       |
    | Create_tmp_table_priv  | enum('N','Y')                     | NO   |     | N                     |       |
    | Lock_tables_priv       | enum('N','Y')                     | NO   |     | N                     |       |
    | Execute_priv           | enum('N','Y')                     | NO   |     | N                     |       |
    | Repl_slave_priv        | enum('N','Y')                     | NO   |     | N                     |       |
    | Repl_client_priv       | enum('N','Y')                     | NO   |     | N                     |       |
    | Create_view_priv       | enum('N','Y')                     | NO   |     | N                     |       |
    | Show_view_priv         | enum('N','Y')                     | NO   |     | N                     |       |
    | Create_routine_priv    | enum('N','Y')                     | NO   |     | N                     |       |
    | Alter_routine_priv     | enum('N','Y')                     | NO   |     | N                     |       |
    | Create_user_priv       | enum('N','Y')                     | NO   |     | N                     |       |
    | Event_priv             | enum('N','Y')                     | NO   |     | N                     |       |
    | Trigger_priv           | enum('N','Y')                     | NO   |     | N                     |       |
    | Create_tablespace_priv | enum('N','Y')                     | NO   |     | N                     |       |
    | ssl_type               | enum('','ANY','X509','SPECIFIED') | NO   |     |                       |       |
    | ssl_cipher             | blob                              | NO   |     | NULL                  |       |
    | x509_issuer            | blob                              | NO   |     | NULL                  |       |
    | x509_subject           | blob                              | NO   |     | NULL                  |       |
    | max_questions          | int(11) unsigned                  | NO   |     | 0                     |       |
    | max_updates            | int(11) unsigned                  | NO   |     | 0                     |       |
    | max_connections        | int(11) unsigned                  | NO   |     | 0                     |       |
    | max_user_connections   | int(11) unsigned                  | NO   |     | 0                     |       |
    | plugin                 | char(64)                          | NO   |     | mysql_native_password |       |
    | authentication_string  | text                              | YES  |     | NULL                  |       |
    | password_expired       | enum('N','Y')                     | NO   |     | N                     |       |
    | password_last_changed  | timestamp                         | YES  |     | NULL                  |       |
    | password_lifetime      | smallint(5) unsigned              | YES  |     | NULL                  |       |
    | account_locked         | enum('N','Y')                     | NO   |     | N                     |       |
    +------------------------+-----------------------------------+------+-----+-----------------------+-------+
    45 rows in set (0.00 sec)

It is important to understand that the host and user together determine an individual permission for connecting. User `Nosipho` may have access from host A, and not from host B. In fact, user `Nosipho` on host B may be an entirely different person.

A host may be either the *hostname* of the machine, or the *IP*, and may include a *wildcard* (the % sign), meaning any host. It should be rare to allow access from any host. Web applications, for example, typically only allow access to the database server from the web server (or localhost for small setups, where they're on the same machine). The password is stored in an encrypted format using the PASSWORD() function. Let's look at a sample subset from the user table:

    mysql> SELECT host,user FROM user;
    +---------------+------+
    | host          | user |
    +---------------+------+
    | localhost     | mysql|
    | localhost     | mark |
    | 192.168.5.42  | tiki |
    | 192.168.5.%   | mpho |
    | 192.168.5.42  |      |
    | %             | wiki |
    +---------------+------+

In this example, the mysql and mark users can connect from localhost only, while user tiki, and any other user, can connect from the IP 192.168.5.42. User mpho can connect from any IP starting with 192.168.5 (as denoted by the wildcard where the last digit would be). Finally, user wiki has access from any machine. This does not necessarily mean they can do anything, just that they can connect.

To decide whether a user has access to perform a particular operation, MySQL again checks the user table first. The remaining fields, all fairly clearly named, come into play. Select_priv determines whether users can run SELECT queries, Insert_priv INSERT queries, and so on.

| Permission      | Description                                                       |
| --------------- | ----------------------------------------------------------------- |
| Select_priv     | Permission to run SELECT queries                                  |
| Insert_priv     | Permission to run INSERT statements                               |
| Update_priv     | Permission to run UPDATE statements                               |
| Delete_priv     | Permission to run DELETE statements                               |
| Create_priv     | Permission to CREATE tables and databases                         |
| Drop_priv       | Permission to DROP tables and databases                           |
| Reload_priv     | Permission to RELOAD the database (a FLUSH statement for example) |
| Shutdown_priv   | Permission to SHUTDOWN the database server                        |
| Process_priv    | Permission to view or kill PROCESSes.                             |
| File_priv       | Permission to read and write FILEs (for example LOAD DATA INFILE) |
| Grant_priv      | Permission to GRANT available permissions to other users          |
| References_priv | Permissions to create, modify or drop INDEXes                     |
| Index_priv      | Not used by MySQL 4.0.x                                           |
| Alter_priv      | Permission to ALTER table structures.                             |

All are enumerated types, a Y value allowing the operation, and a N value possibly disallowing it. Only possibly, because the user table is the bluntest kind of permission. A Y value in one of these fields always allows that operation to be performed on all databases in the table. It is often good practice to set values to N in the user table, and then allow them for the appropriate database only, as we'll see now. Another sample:

    mysql> SELECT host,user,select_priv,insert_priv FROM user;
    +-----------+------+-------------+-------------+
    | host      | user | select_priv | insert_priv |
    +-----------+------+-------------+-------------+
    | %         | mark | Y           | N           |
    | localhost | mpho | N           | N           |
    +-----------+------+-------------+-------------+

Here user mark can always perform SELECT queries, while for the other operations, MySQL will need to check the other tables first to see, starting with the db table.

# The `db` table

If the user table allows access, but disallows permission for a particular operation, the next table to worry about is the db table. This sets permissions for specific databases.

    mysql> DESC db;
    +-----------------+---------------+------+-----+---------+-------+
    | Field           | Type          | Null | Key | Default | Extra |
    +-----------------+---------------+------+-----+---------+-------+
    | Host            | char(60)      |      | PRI |         |       |
    | Db              | char(32)      |      | PRI |         |       |
    | User            | char(16)      |      | PRI |         |       |
    | Select_priv     | enum('N','Y') |      |     | N       |       |
    | Insert_priv     | enum('N','Y') |      |     | N       |       |
    | Update_priv     | enum('N','Y') |      |     | N       |       |
    | Delete_priv     | enum('N','Y') |      |     | N       |       |
    | Create_priv     | enum('N','Y') |      |     | N       |       |
    | Drop_priv       | enum('N','Y') |      |     | N       |       |
    | Grant_priv      | enum('N','Y') |      |     | N       |       |
    | References_priv | enum('N','Y') |      |     | N       |       |
    | Index_priv      | enum('N','Y') |      |     | N       |       |
    | Alter_priv      | enum('N','Y') |      |     | N       |       |
    +-----------------+---------------+------+-----+---------+-------+

Host and User appear in the same way in this table, but attached to a database, not a password. The same host/user combination appears, with a password, in the user table, which allows the user to connect, but if they do not have permission to perform an operation, MySQL will check this table to see if they can perform it on a particular database. A sample:

    mysql> SELECT host,db,user,select_priv,insert_priv FROM db;
    +-----------+----------+-------+-------------+-------------+
    | host      | db       | user  | select_priv | insert_priv |
    +-----------+----------+-------+-------------+-------------+
    | localhost | news     | mark  | Y           | Y           |
    | localhost | archives | mpho  | N           | N           |
    | localhost | news     | mpho  | Y           | Y           |
    +-----------+----------+-------+-------------+-------------+

Compare this with the previous sample we looked at from the user table. User mark already had Select permission on all databases, but no Insert permissions. Here, he is granted insert permission on the news database only, while user mpho is given select and insert permission on the news database. Most MySQL installations in any kind of multi-user scenario would be best served by denying global permissions, and granting them on the database-level only.

Another possibility exists. The database and user combination were found, but the host was left blank. In this case, MySQL checks the host table. Let's look at what's in there:

    mysql> DESC host;
    +-----------------+---------------+------+-----+---------+-------+
    | Field           | Type          | Null | Key | Default | Extra |
    +-----------------+---------------+------+-----+---------+-------+
    | Host            | char(60)      |      | PRI |         |       |
    | Db              | char(32)      |      | PRI |         |       |
    | Select_priv     | enum('N','Y') |      |     | N       |       |
    | Insert_priv     | enum('N','Y') |      |     | N       |       |
    | Update_priv     | enum('N','Y') |      |     | N       |       |
    | Delete_priv     | enum('N','Y') |      |     | N       |       |
    | Create_priv     | enum('N','Y') |      |     | N       |       |
    | Drop_priv       | enum('N','Y') |      |     | N       |       |
    | Grant_priv      | enum('N','Y') |      |     | N       |       |
    | References_priv | enum('N','Y') |      |     | N       |       |
    | Index_priv      | enum('N','Y') |      |     | N       |       |
    | Alter_priv      | enum('N','Y') |      |     | N       |       |
    +-----------------+---------------+------+-----+---------+-------+

Exactly the same kind of checks occur here. An example:

     mysql> SELECT host,db,select_priv,insert_priv FROM host;
    +---------------+----------+-------------+-------------+
    | host          | db       | select_priv | insert_priv |
    +---------------+----------+-------------+-------------+
    | localhost     | news     | Y           | Y           |
    | localhost     | archives | Y           | N           |
    | 192.168.5.42  | news     | Y           | N           |
    +---------------+----------+-------------+-------------+

If the host had been left blank, permissions are determined here. A user from localhost would have both select and insert permission to the news database, while a user from the host 192.168.5.42 would only have select permission to this database. From localhost, a user would have select privileges only on the archives database.

But there is still more fine-tuning possible. You can assign users permission on a table, or even a column level, with the tables_priv and columns_priv tables, described below:

    mysql> DESC tables_priv;
    +-------------+---------------------------------------------------+------+-----+---------+-------+
    | Field       | Type                                              | Null | Key | Default | Extra |
    +-------------+---------------------------------------------------+------+-----+---------+-------+
    | Host        | char(60)                                          |      | PRI |         |       |
    | Db          | char(60)                                          |      | PRI |         |       |
    | User        | char(16)                                          |      | PRI |         |       |
    | Table_name  | char(60)                                          |      | PRI |         |       |
    | Grantor     | char(77)                                          |      | MUL |         |       |
    | Timestamp   | timestamp(14)                                     | YES  |     | NULL    |       |
    | Table_priv  | set('Select','Insert','Update','Delete','Create', |      |     |         |       |
    |             |'Drop','Grant','References','Index','Alter')       |      |     |         |       |
    | Column_priv | set('Select','Insert','Update','References')      |      |     |         |       |
    +-------------+---------------------------------------------------+------+-----+---------+-------+

    mysql> DESC columns_priv;
    +-------------+----------------------------------------------+------+-----+---------+-------+
    | Field       | Type                                         | Null | Key | Default | Extra |
    +-------------+----------------------------------------------+------+-----+---------+-------+
    | Host        | char(60)                                     |      | PRI |         |       |
    | Db          | char(60)                                     |      | PRI |         |       |
    | User        | char(16)                                     |      | PRI |         |       |
    | Table_name  | char(60)                                     |      | PRI |         |       |
    | Column_name | char(60)                                     |      | PRI |         |       |
    | Timestamp   | timestamp(14)                                | YES  |     | NULL    |       |
    | Column_priv | set('Select','Insert','Update','References') |      |     |         |       |
    +-------------+----------------------------------------------+------+-----+---------+-------+

# A brief recap of the process

The order of precedence of the tables is as follows:

    user: User accounts, global privileges, and other non-privilege columns.

    db: Database-level privileges.

    host: Obsolete. MySQL install operations do not create this table as of MySQL 5.6.7.

    tables_priv: Table-level privileges.

    columns_priv: Column-level privileges.

    procs_priv: Stored procedure and function privileges.

    proxies_priv: Proxy-user privileges.

MySQL checks the user table first, if permission is not granted there, it will check the db and host tables, and, if further confirmation is required, the tables_priv and even the columns_priv tables. Be aware that excessive use of all these tables comes at a performance cost - if before every operation MySQL has to check permissions at a column level, it will be that much slower. Use what you need, no more, no less.

How to GRANT permissions

Hopefully the process has been easy to follow, but the burning question must be, how does anyone set these permissions! There are two ways - directly manipulating the tables, with INSERT, UPDATE and DELETE statements (which require MySQL to be reloaded, or the privileges flushed, for example with the FLUSH PRIVILEGES statement. Note also that if you add a record directly to the password field in the user table, you must use the PASSWORD() function. The alternative, more convenient in my opinion, is using a GRANT statement. The syntax of a GRANT statement is:

    GRANT privilege ON table_or_database_name TO user@hostname IDENTIFIED BY 'password'.

The privileges are:

| Privilege               | Description                                                                                            |
| ----------------------- | ------------------------------------------------------------------------------------------------------ |
| ALL/ALL PRIVILEGES      | All the basic permissions                                                                              |
| ALTER                   | Permission to run ALTER statements                                                                     |
| CREATE                  | Permission to CREATE tables or databases                                                               |
| CREATE TEMPORARY TABLES | Permission to run CREATE TEMPORARY TABLE statements                                                    |
| DELETE                  | Permission to run DELETE statements                                                                    |
| DROP                    | Permission to DROP tables or databases                                                                 |
| EXECUTE                 | Permission to run stored procedures (in MySQL 5)                                                       |
| FILE                    | Permission to read and write files (e.g. LOAD DATA INFILE statements)                                  |
| GRANT                   | Permission to GRANT available permissions to other users                                               |
| INDEX                   | Permission to create, change or drop indexes                                                           |
| INSERT                  | Permission to run INSERT statements                                                                    |
| LOCK TABLES             | Permission to LOCK tables which the user has SELECT access to                                          |
| PROCESS                 | Permission to view or kill MySQL processes                                                             |
| REFERENCES              | Currently unused                                                                                       |
| RELOAD                  | Permission to reload the database (e.g. FLUSH statements)                                              |
| REPLICATION CLIENT      | Permission to ask about replication                                                                    |
| REPLICATION SLAVE       | Permission to replicate from the server                                                                |
| SHOW DATABASES          | Permission to see all databases                                                                        |
| SELECT                  | Permission to run SELECT statements                                                                    |
| SHUTDOWN                | Permission to SHUTDOWN the MySQL server                                                                |
| SUPER                   | Permission to connect, even if the number of connections is exceeded, and perform maintenance commands |
| UPDATE                  | Permission to run UPDATE statements                                                                    |
| USAGE                   | Permission to connect and and perform basic commands only                                              |

Database and Table names in a GRANT statement

| Name           | Description                           |
| -------------- | ------------------------------------- |
| *.*            | All tables in all databases           |
| *              | All tables in the current database    |
| dbname.*       | All tables in the named database      |
| dbname.tbname  | The named table in the named database |

So, some examples of GRANT in action:

    mysql> GRANT SELECT ON *.* TO rushdi@localhost IDENTIFIED BY 'supa_password'
    Query OK, 0 rows affected (0.00 sec)

    mysql> SELECT host,user,password,select_priv,insert_priv FROM user WHERE user = 'rushdi';
    +-----------+--------+------------------+-------------+-------------+
    | host      | user   | password         | select_priv | insert_priv |
    +-----------+--------+------------------+-------------+-------------+
    | localhost | rushdi | 0b3bcd316f1c8020 | Y           | N           |
    +-----------+--------+------------------+-------------+-------------+

Note that the password has been automatically encrypted. The record also only appears in the user table, not the db table, as permission was granted to all databases. Another example:

    mysql> GRANT INSERT ON mysql.* TO suretha@localhost IDENTIFIED BY 'supa_password2';
    Query OK, 0 rows affected (0.00 sec)

    mysql> SELECT host,user,password,select_priv,insert_priv FROM user WHERE user = 'Suretha';
    +-----------+---------+------------------+-------------+-------------+
    | host      | user    | password         | select_priv | insert_priv |
    +-----------+---------+------------------+-------------+-------------+
    | localhost | suretha | 30f59c271b923c47 | N           | N           |
    +-----------+---------+------------------+-------------+-------------+

    mysql> SELECT host,db,user,select_priv,insert_priv FROM db WHERE user='Suretha';
    +-----------+-------+---------+-------------+-------------+
    | host      | db    | user    | select_priv | insert_priv |
    +-----------+-------+---------+-------------+-------------+
    | localhost | mysql | suretha | N           | Y           |
    +-----------+-------+---------+-------------+-------------+

Here records are added to both the user and db tables. To revoke permission, we use the REVOKE statement.

    mysql> REVOKE SELECT ON *.* FROM rushdi@localhost;
    Query OK, 0 rows affected (0.00 sec)

    mysql> SELECT host,user,password,select_priv,insert_priv FROM user WHERE user = 'rushdi';
    +-----------+--------+------------------+-------------+-------------+
    | host      | user   | password         | select_priv | insert_priv |
    +-----------+--------+------------------+-------------+-------------+
    | localhost | rushdi | 0b3bcd316f1c8020 | N           | N           |
    +-----------+--------+------------------+-------------+-------------+

Note that the record still appears in the table, so he can connect, but select_priv has been disabled.

    mysql> REVOKE INSERT ON mysql.* FROM suretha@localhost;
    Query OK, 0 rows affected (0.00 sec)

    mysql> SELECT host,db,user,select_priv,insert_priv FROM db WHERE user='Suretha';
    Empty set (0.00 sec)

    mysql> SELECT host,user,password,select_priv,insert_priv FROM user WHERE user = 'Suretha';
    +-----------+---------+------------------+-------------+-------------+
    | host      | user    | password         | select_priv | insert_priv |
    +-----------+---------+------------------+-------------+-------------+
    | localhost | suretha | 30f59c271b923c47 | N           | N           |
    +-----------+---------+------------------+-------------+-------------+

The record has been deleted from the db table, but still appears, with no permissions, in the user table.

Hopefully you're starting to find MySQL permissions flexible and easy to use. Although there is much more to explore, do not overuse the available options. You will make management more complex, and affect performance. Good luck!

