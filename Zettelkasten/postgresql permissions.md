202404081243
Status: #idea
Tags: #programming #database #work 

# postgresql permissions
### Owner vs. Privileges
- https://stackoverflow.com/a/60000149/5543374
- Privileges can (and should) be generated when creating an object:
![[Pasted image 20240408124532.png]]

### User/role permissions paradigm
postgres - standard superuser
mdas - standard user:
  - connect
  - execute permissions
  - insert
  - select
  - temporary
  - update

Based on script output, we'll need to run the 'create' scripts as a superuser.

### Unix socket authentication
[See here](https://www.postgresql.org/docs/7.4/auth-methods.html) for info.

On the Seco, the `pg_ident.conf` file is at `/etc/postgresql/14/main/pg_ident.conf`

Mapping the system user to `mdas` may be sufficient.
# References

