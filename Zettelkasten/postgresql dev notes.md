202312141031
Status: #idea
Tags: #work #programming #fathom5 #opt #database

# postgresql dev notes
We're using postgres 14.

> Note - using the same PW in dev as was used on the Seco for simplicity. 

See also [[seco sbc postgres database]]; these two pages got a little muddled, but the other page isn't going to be super useful.
See also [[Postgres and docker]].

A note on database login:
> Your connection failed because by default `psql` connects over UNIX sockets using `peer` authentication, that requires the current UNIX user to have the same user name as `psql`. So you will have to create the UNIX user `dev` and then login as `dev` or use `sudo -obsidianuobsidianobsidianobsidian dev psql test_development` for accessing the database (and `psql` should **not** ask for a password).

[See here](https://stackoverflow.com/a/21889759/5543374)

## Ubuntu Setup
### Seco SBC
Apparently there is no db on the Seco, but we can access the AWS db instance through fconnect and the Seco.

To log in:
- ssh fconnect@10.1.10.4
- Seco PW: `yees*REEG0zoot9urs`
- psql 'postgres://mdas:2e3j935tj012i50kw9ea21i0@db.opt.fconnect.fathom5.io:5432/mdas?sslmode=disable'

#### Postgres connection string
To access the Seco SBC database instance, use: psql 'postgres://mdas:2e3j935tj012i50kw9ea21i0@db.opt.fconnect.fathom5.io:5432/mdas?sslmode=disable' 
- username: mdas
- password: 2e3j935tj012i50kw9ea21i0
- host: db.opt.fconnect.fathom5.io
- port: 5432
- database: mdas
- params: sslmode=disable
#### Postgres config files
pg_config: `/usr/bin/pg_config`
postgresql.conf: `etc/postgresql/10/main/postgresql.conf`
> use the version in the path

#### pg_dump
The `pg_dump` command can be used to export the database schema.
```bash
pg_dump --schema-only -h db.opt.fconnect.fathom5.io -U mdas mdas > mdas_schema.sql
```

For my local, dev db: 
```bash
sudo -u postgres pg_dump --schema-only --no-owner mdas_asset > mdas_asset_database.sql
```

#### SCP From the Seco
To get files off the Seco and onto the Mio, use `scp`:
```bash
scp fconnect@10.1.10.4:/home/fconnect/gss_updates/mdas_schema.sql [path to dest on mio]
```

### Installing Postgres
- Normally, 18.04 only has postgres-10 in the apt repo. To add 14, [do this](https://stackoverflow.com/a/75779439/5543374)
- I did that, and it only installed 14.8, while the Seco has 14.10 on it. We may need/want to build it from source. Files can be found here: https://www.postgresql.org/ftp/source/v14.10/
- [I also tried this](https://www.postgresql.org/message-id/ZN4OigxPJA236qlg%40msg.df7cb.de), which didn't seem to work for me, but is interesting and suggests we should be getting onto 22.04 ASAP.
- [This page](https://www.postgresql.org/docs/current/install-make.html) details how to build Postgres from source
	- Needs `libreadline`, I think specifically the `-dev`: `sudo apt-get install libreadline-dev`

### Intalling libpqxx
`sudo apt-get install libpqxx-dev`
This only gets us up to v4, which doesn't seem to play with postgres 14
### Building libpqxx
[Link to docs](https://github.com/jtv/libpqxx/blob/master/BUILDING-configure.md)
Need to tell the compiler what C++ version to user:
```bash
./configure CXXFLAGS=-std=c++17 --disable-shared
```
attempting lib v 7. Requires `xmlto`: 
```bash
sudo apt-get install xmlto
```
I have had quite a bit of trouble building newer versions of this library on 18.04, and I'm not entirely sure why. As a means to just get the library installed and running I checked out and installed `6.0.0` on my local machine. I am able to perform a very basic query, which should be sufficient until I figure out how to build, run and debug a C++ application in [[VSCode]].

### Linking [[libpqxx]]
- [This link](https://github.com/jtv/libpqxx/blob/master/BUILDING-cmake.md) points to how to include this package. Check the `CMakeLists.txt` file in the `opt_mdas_seco_pcs` repo to see how the lib was linked against.
- This method isn't installing the dependency, so we'll need to get that dependency into the docker container another way. Downloading and building the lib may be appropriate rather than relying on `apt-get`.

### Create database
```sql
create database mdas;
```
### Create mdas role
```sql
create role mdas;
grant postgres to mdas;
grant all privileges on database mdas to mdas;
```
### Create database using a .sql file
`sudo -u postgres psql -d mdas -a -f mdas_schema.sql`


### Starting Postgres shell
`sudo -u postgres psql template1` (this is the default db)
This shows what the psql prompt should look like:
![[Pasted image 20231214141915.png]]

### Restart Postgres Service
`sudo systemctl restart postgresql`

### Check Service Status
Use the `sudo systemctl status postgresql` command to check the PostgreSQL service status in Linux. Users can also use the `sudo systemctl is-active postgresql` and `sudo systemctl is-enabled postgresql` commands to check if the Postgres service is active and enabled.

## Managing Startup/Runtime Health
Still trying to figure it out. Postgres has a sample startup script included in the repository - we may want to bring that into the project and install it as part of the general project install. That script is here: `cat ~/repositories/postgres/contrib/start-scripts/linux`
- [This is a simplistic way to manage the startup](https://askubuntu.com/a/715275/683312)
- [Postgres documentation for autostart](https://www.postgresql.org/docs/current/server-start.html)
	- Local path to the startup script: /home/nanderson/repositories/postgres/contrib/start-scripts
- [Startup/restart config](https://www.postgresql.org/docs/14/runtime-config-error-handling.html)
- There is a generator for `.service` scripts. [See this](https://unix.stackexchange.com/a/665032) [and this](https://www.postgresql.org/docs/9.1/libpq-pgservice.html)
Postgres also supports [failover partners](https://www.postgresql.org/docs/14/warm-standby-failover.html). We'll likely look at this post-MVP, using the cloud as the backup node.
## Remove Postgres
If you mess something up, postgres can be purged [following this guide](https://askubuntu.com/a/32735/683312).

## Debugging Connection
`ss -lntp` to see if port `5432` is open.
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
## To Dos:
- Figure out how to download and install the database software
- Determine how to manage the `libpqxx` dependency from the project.
- Create a provisioning script that will initialize the database and set up users in addition to the database

# References





