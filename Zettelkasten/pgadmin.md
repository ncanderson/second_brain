202403211013
Status: #idea
Tags: #postgres #programming #database 

# pgadmin
Setup and configuration of pgadmin for [[postgresql dev notes]].
1. Connect to a server. Need to have:
	- the host name (probably localhost)
	- port (default is 5432)
	- username (default is `postgres`)
	- password
		- I had to do: `alter user postgres PASSWORD 'postgres';`. Not sure why.
2. Create databases
	- `mdas_telemetry`
	- `mdas_asset`
3. As of now, we'll just use the `public` schema I guess

### Saving Ad-hoc Queries
By default, ad hoc queries will save here. There may not be a way to change this (?):
```bash
nanderson ~ () $ sudo ls /var/lib/pgadmin/storage/nanderson_greenseaiq.com

```


# References

