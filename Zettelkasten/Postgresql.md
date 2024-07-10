202404161225
Status: #idea
Tags: 

# Postgresql

> This page was compiled from a number of scribbly notes pages.
> - [[Postgres and docker]]
> - [[seco sbc postgres database]]
> - [[libpqxx]]
> - [[postgresql dev notes]]

## Versions
- postgres
  - 22.04: 14.10
  - 18.04: 10.19
- [[libpqxx]]
  - 22.04: 7.8.1
  - 18.04: 7.7.5

## Installation
### Installing Postgres
#### Install postgres 14 on 18.04 
Ubuntu 18.04 only has postgres-10 in the `apt` repo. While 14 could be added, when I tried to install it there were a number of dependencies that I didn't have. I was concerned about bricking my machine, so while it may be possible to install postgres 14 on an 18.04 machine, proceed with caution. 
- [do this](https://stackoverflow.com/a/75779439/5543374)
- I did that, and it only installed 14.8, while the Seco has 14.10 on it. We may need/want to build it from source. Files can be found here: https://www.postgresql.org/ftp/source/v14.10/
- [I also tried this](https://www.postgresql.org/message-id/ZN4OigxPJA236qlg%40msg.df7cb.de), which didn't seem to work for me, but is interesting and suggests we should be getting onto 22.04 ASAP.
- [This page](https://www.postgresql.org/docs/current/install-make.html) details how to build Postgres from source
	- Needs `libreadline`, I think specifically the `-dev`: `sudo apt-get install libreadline-dev`

### Intalling libpqxx
You can do `sudo apt-get install libpqxx-dev`, but this only gets us up to v4, which doesn't seem to play with postgres 14. See also [[libpqxx]].

I forked the `libpqxx` repository, and modified the `CMakeLists.txt` file to build a deb package. Those releases [can be found here](https://github.com/GreenseaSystems/libpqxx). The `cmake` changes were merged to the master branch on the GreenseaSystems fork, so to build another version, checkout the appropriate tag, `meld`/diff in the `cmake` changes from `master`, then do the standard:
```bash
mkdir build
cd build
cmake ..
make package
```
#### Additional Dependencies
At some point, attempting to build libpqxx v7 something required `xmlto`: 
```bash
sudo apt-get install xmlto
```

### Remove Postgres
If you mess something up, postgres can be purged [following this guide](https://askubuntu.com/a/32735/683312).

## Login and Authentication

> Your connection failed because by default `psql` connects over UNIX sockets using `peer` authentication, that requires the current UNIX user to have the same user name as `psql`. So you will have to create the UNIX user `dev` and then login as `dev` or use `sudo -u dev psql test_development` for accessing the database (and `psql` should **not** ask for a password).

[See here](https://stackoverflow.com/a/21889759/5543374)

### Accessing the Postgres Database on the Seco SBC
To log in:
- ssh fconnect@10.1.10.4
- Seco PW: `yees*REEG0zoot9urs`
- psql 'postgres://mdas:2e3j935tj012i50kw9ea21i0@db.opt.fconnect.fathom5.io:5432/mdas?sslmode=disable'

#### Mystery Login Info
I think this may be how to log into the database that's local on the Seco, while the above connection string will log into the database instance in the AWS cloud:
```bash
fconnect@gss-01:~$ cat /etc/mdas/db PG_SUPER_USER=postgres 
POSTGRES_USER=opt 
POSTGRES_DB=opt
POSTGRES_PASSWORD=Capital.idea.old.chap42!
```

### Connection Strings
Sample connection string:
> postgres://mdas:2e3j935tj012i50kw9ea21i0@db.opt.fconnect.fathom5.io:5432/mdas?sslmode=disable
- username: mdas
- password: 2e3j935tj012i50kw9ea21i0 (this is hashed)
- host: db.opt.fconnect.fathom5.io
- port: 5432
- database: mdas
- params: sslmode=disable (there are more optional params that can be added here)

## Configuration 
> Generally, the version number in the path can be updated for the version you're running; postgresql versions will install alongside each other.
- `pg_config`: `/usr/bin/pg_config`
- `postgresql.conf`: `etc/postgresql/10/main/postgresql.conf`
- `pg_hba.conf`: `/etc/postgresql/14/main/pg_hba.conf`

### Linking [[libpqxx]]
- [This link](https://github.com/jtv/libpqxx/blob/master/BUILDING-cmake.md) points to how to include this package. Check the `CMakeLists.txt` file in the `opt_mdas_seco_pcs` repo to see how the lib was linked against.
- This method isn't installing the dependency. For OPT, we included the built debian in the project's dependency folder.

### Debugging Database Connection
By default, postgres will use port `5432`. Use `ss -lntp` to see if port `5432` is open:
```bash
(Docker) nanderson ~ () $ ss -lntp
State          Recv-Q         Send-Q                 Local Address:Port                  Peer Address:Port        Process        
LISTEN         0              128                        127.0.0.1:5939                       0.0.0.0:*                          
LISTEN         0              128                    127.0.0.53%lo:53                         0.0.0.0:*                          
LISTEN         0              128                          0.0.0.0:22                         0.0.0.0:*                          
LISTEN         0              5                          127.0.0.1:631                        0.0.0.0:*                          
LISTEN         0              224                        127.0.0.1:5432                       0.0.0.0:*                          
LISTEN         0              244                        127.0.0.1:5433                       0.0.0.0:*                          
LISTEN         0              100                          0.0.0.0:25                         0.0.0.0:*                          
LISTEN         0              50                           0.0.0.0:10010                      0.0.0.0:*                          
LISTEN         0              511                                *:80                               *:*                          
LISTEN         0              128                             [::]:22                            [::]:*                          
LISTEN         0              5                              [::1]:631                           [::]:*                          
LISTEN         0              100                             [::]:25                            [::]:*                          
```
This can be helpful as postgres will silently grab another port if `5432` is in use which can happen if a docker container and the host machine are both running a postgres service.

## Managing Startup/Runtime Health
For `project_opt_mdas`, I included a `.service` file to manage the service. [File here](https://github.com/gsiq-bayonet/project_opt_mdas/blob/d4758e6ddd4194fa67aba1994ed00a11547eefa0/run/etc/systemd/system/postgresql.service#L1)

Some other things I explored:
- Postgres has a sample startup script included in the repository: 
```bash
cat ~/repositories/postgres/contrib/start-scripts/linux
```
- [This is a simplistic way to manage the startup](https://askubuntu.com/a/715275/683312)
- [Postgres documentation for autostart](https://www.postgresql.org/docs/current/server-start.html)
	- Local path to the startup script: /home/nanderson/repositories/postgres/contrib/start-scripts
- [Startup/restart config](https://www.postgresql.org/docs/14/runtime-config-error-handling.html)
- There is a generator for `.service` scripts. [See this](https://unix.stackexchange.com/a/665032) [and this](https://www.postgresql.org/docs/9.1/libpq-pgservice.html)

### Failover Partners
Postgres supports [failover partners](https://www.postgresql.org/docs/14/warm-standby-failover.html). 

## [[pg_dump]]
The `pg_dump` command can be used to export the database schema.
#### F5 Cloud Database
Accessible from the Seco
```bash
pg_dump --schema-only -h db.opt.fconnect.fathom5.io -U mdas mdas > mdas_schema.sql
```

For my local, dev db: 
```bash
sudo -u postgres pg_dump --schema-only --no-owner everclean_cleaning > create_db_everclean_cleaning.sql
```

# Database Setup and Use
## `psql`
This shows what the `psql` prompt should look like:
![[Pasted image 20231214141915.png]]

### Starting Postgres shell
For the default db:
```bash
sudo -u postgres psql template1 
```

When attempting to use `psql`, one may see this error:
```bash
fconnect@gss-01:~$ sudo -u postgres psql
could not change directory to "/home/fconnect": Permission denied
psql (14.10 (Ubuntu 14.10-0ubuntu0.22.04.1))
```
This is because we're attempting to run `psql` in a directory that the `postgres` user doesn't have permissions on. Doing the following:
```bash
cd ~/postgres/
sudo -u postgres psql
```

takes us to a directory that the `postgres` user does have access to:
```bash
fconnect@gss-01:/var/lib/postgresql$ ll
total 4
drwxr-xr-x 1 postgres postgres   30 Dec 13 14:51 ./
drwxr-xr-x 1 root     root     1294 Apr 20  2023 ../
drwxr-xr-x 1 postgres postgres    8 Apr 20  2023 14/
-rw------- 1 postgres postgres  124 Dec 13 15:52 .psql_history
```


### Create database using a `.sql` file
This command can be used to run `.sql` files, and will be necessary for any project installs.
```bash
sudo -u postgres psql -d mdas -a -f mdas_schema.sql
```

## Creation of Database Artifacts
### Create database
```sql
create database mdas;
```

### Create mdas role
- `sudo -u postgres psql` - start postgres session
- `create database mdas;` - create db
- `create role mdas;` - create mdas user
- `grant postgres to mdas;` - give all `postgres` (ie superuser) permissions to `mdas`
- `alter database mdas owner to mdas;` - change owner of the database
- `alter role mdas with login;` - allow mdas role to login
See also [[postgresql permissions]]
## Postgres Service
To restart the postgres service:
```bash
sudo systemctl restart postgresql
```

### Check Service Status
> Use the `sudo systemctl status postgresql` command to check the PostgreSQL service status in Linux. Users can also use the `sudo systemctl is-active postgresql` and `sudo systemctl is-enabled postgresql` commands to check if the Postgres service is active and enabled.

# References
See also:
- [[uuid-ossp]]
- [[pgadmin]]
- [version control of databases](https://blog.codinghorror.com/get-your-database-under-version-control/)
- record version column?
- version tracking tables