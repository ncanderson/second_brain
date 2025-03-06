202312131538
Status: #idea
Tags: #programming #work 

# seco sbc postgresql database
Accessing the postgres instance on the Seco SBC.

## Log in 
From the MIO (or other device connected to the seco):
- ssh fconnect@10.1.10.4
- Seco PW: `yees*REEG0zoot9urs`
- psql 'postgres://mdas:2e3j935tj012i50kw9ea21i0@db.opt.fconnect.fathom5.io:5432/mdas?sslmode=disable'
## Steps to get started
 `sudo -u postgres psql` - start postgres session
 `create database mdas;` - create db
 `create role mdas;` - create mdas user
`grant postgres to mdas;` - give all permissions to mdas user
`alter database mdas owner to mdas;` - change owner
`alter role mdas with login;` - allow mdas role to login

## Older Notes
Restart the pg service:
```sql
sudo service postgresql restart
```
Login creds:
```bash
fconnect@gss-01:~$ cat /etc/mdas/db PG_SUPER_USER=postgres 
POSTGRES_USER=opt 
POSTGRES_DB=opt
POSTGRES_PASSWORD=Capital.idea.old.chap42!
```

When attempting to use `psql`, one may see this error:
```bash
fconnect@gss-01:~$ sudo -u postgres psql
could not change directory to "/home/fconnect": Permission denied
psql (14.10 (Ubuntu 14.10-0ubuntu0.22.04.1))
```
This is because we're attempting to run `psql` in a directory that the `postgres` user doesn't have permissions on. 

Doing the following:
```sql
# cd ~postgres/
# sudo -u postgres psql
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
`pg_hba.conf` config file:
```bash
/etc/postgresql/14/main/pg_hba.conf
```
# References

