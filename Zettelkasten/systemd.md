202407151324
Status: #idea
Tags: 

# systemd
- service has to be manually enabled and then reloaded, then it can be started

# systemctl
A tool to manage services. This can cause some confusion for [[Postgresql]] which uses several nested services. Generally, postgres will use a single umbrella service, which will manage sub-services. 

#### Service Status
To view the status of the umbrella service, do `sudo systemctl status postgresql`:
```bash
● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)
     Active: active (exited) since Tue 2024-07-23 09:27:50 EDT; 59min ago
    Process: 2547 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
   Main PID: 2547 (code=exited, status=0/SUCCESS)
        CPU: 1ms

Jul 23 09:27:50 nanderson-p1g3-ubu systemd[1]: Starting PostgreSQL RDBMS...
Jul 23 09:27:50 nanderson-p1g3-ubu systemd[1]: Finished PostgreSQL RDBMS.
```

#### Review Services
Doing this `sudo systemctl status 'postgresql*'` will show all the sub-process that are automatically created by the postgres install. You can see that there are services for postgres 14 and 15:
```bash
● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)
     Active: active (exited) since Tue 2024-07-23 09:27:50 EDT; 1h 7min ago
    Process: 2547 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
   Main PID: 2547 (code=exited, status=0/SUCCESS)
        CPU: 1ms

Jul 23 09:27:50 nanderson-p1g3-ubu systemd[1]: Starting PostgreSQL RDBMS...
Jul 23 09:27:50 nanderson-p1g3-ubu systemd[1]: Finished PostgreSQL RDBMS.

● postgresql@14-main.service - PostgreSQL Cluster 14-main
     Loaded: loaded (/lib/systemd/system/postgresql@.service; enabled-runtime; vendor preset: enabled)
     Active: active (running) since Tue 2024-07-23 09:27:50 EDT; 1h 7min ago
    Process: 1480 ExecStart=/usr/bin/pg_ctlcluster --skip-systemctl-redirect 14-main start (code=exited, status=0/SU>
   Main PID: 1603 (postgres)
      Tasks: 7 (limit: 37845)
     Memory: 43.0M
        CPU: 930ms
     CGroup: /system.slice/system-postgresql.slice/postgresql@14-main.service
             ├─1603 /usr/lib/postgresql/14/bin/postgres -D /var/lib/postgresql/14/main -c config_file=/etc/postgresq>
             ├─1643 "postgres: 14/main: checkpointer " "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" ">
             ├─1644 "postgres: 14/main: background writer " "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "">
             ├─1645 "postgres: 14/main: walwriter " "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" ">
             ├─1646 "postgres: 14/main: autovacuum launcher " "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" >
             ├─1647 "postgres: 14/main: stats collector " "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" ">
             └─1648 "postgres: 14/main: logical replication launcher " "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" >

Jul 23 09:27:43 nanderson-p1g3-ubu systemd[1]: Starting PostgreSQL Cluster 14-main...
Jul 23 09:27:50 nanderson-p1g3-ubu postgresql@14-main[1480]: Warning: connection to the database failed, disabling s>
Jul 23 09:27:50 nanderson-p1g3-ubu postgresql@14-main[1480]: psql: error: connection to server on socket "/var/run/p>
Jul 23 09:27:50 nanderson-p1g3-ubu systemd[1]: Started PostgreSQL Cluster 14-main.

● postgresql@15-main_15.service - PostgreSQL Cluster 15-main_15
     Loaded: loaded (/lib/systemd/system/postgresql@.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2024-07-23 09:27:50 EDT; 1h 7min ago
    Process: 1481 ExecStart=/usr/bin/pg_ctlcluster --skip-systemctl-redirect 15-main_15 start (code=exited, status=0>
   Main PID: 1601 (postgres)
      Tasks: 6 (limit: 37845)
     Memory: 40.7M
        CPU: 850ms
     CGroup: /system.slice/system-postgresql.slice/postgresql@15-main_15.service
             ├─1601 /usr/lib/postgresql/15/bin/postgres -D /var/lib/postgresql/15/main_15 -c config_file=/etc/postgr>
             ├─1626 "postgres: 15/main_15: checkpointer " "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" ">
             ├─1627 "postgres: 15/main_15: background writer " "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "">
             ├─1638 "postgres: 15/main_15: walwriter " "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" ">
             ├─1639 "postgres: 15/main_15: autovacuum launcher " "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" >
             └─1640 "postgres: 15/main_15: logical replication launcher " "" "" "" "" "" "" "" "" "" "" "" "" "" "" >

Jul 23 09:27:43 nanderson-p1g3-ubu systemd[1]: Starting PostgreSQL Cluster 15-main_15...
Jul 23 09:27:50 nanderson-p1g3-ubu postgresql@15-main_15[1481]: Warning: connection to the database failed, disablin>
Jul 23 09:27:50 nanderson-p1g3-ubu postgresql@15-main_15[1481]: psql: error: connection to server on socket "/var/ru>
Jul 23 09:27:50 nanderson-p1g3-ubu systemd[1]: Started PostgreSQL Cluster 15-main_15.
```

#### Manage Services
- To stop all servies, run `sudo systemctl stop postgresql.service`
- To see whether a postgres cluster is running (which is a good way to review the results of running `systemctl` commands), us `pg_lsclusters`, which will show the status of the database clusters:
```bash
nanderson ~ () $ pg_lsclusters 
Ver Cluster Port Status Owner    Data directory                 Log file
14  main    5432 down   postgres /var/lib/postgresql/14/main    /var/log/postgresql/postgresql-14-main.log
15  main_15 5433 down   postgres /var/lib/postgresql/15/main_15 /var/log/postgresql/postgresql-15-main_15.log
```

### Useful Commands
- `systemctl list-units`: show all service units.
- Doing `systemctl list-units | grep postgres` gives the following:
```bash
postgresql.service          loaded active exited    PostgreSQL RDBMS
postgresql@14-main.service  loaded active running   PostgreSQL Cluster 14-main
system-postgresql.slice     loaded active active    Slice /system/postgresql
```
In this case, `postgresql.service` is an umbrella service that will manage the rest. [Here for reference](https://dba.stackexchange.com/a/320672)
- To get details about all the postgresql services, run:
```bash
sudo systemctl status 'postgresql*'
```
- To get log output from service restarts, run:
```bash
journalctl -xeu postgresql.service
```

### postgresql service debug
Some sort of collision with the `postmaster.pid` file. Maybe the `.service` file I made is creating duplicate records? 
I removed the `.service` file I made, stopped the service, reloaded the daemon and then restarted postgres, and it was able to restart. I think that there is a service re-start mechanism that's already in place, so when I added a new one it's attempting to trigger a service restart but because there is already a `postmaster.pid` file, we get an error.


# References
https://dba.stackexchange.com/questions/320575/what-does-postgresql-status-active-exited-mean