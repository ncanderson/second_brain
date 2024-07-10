202312200941
Status: #idea
Tags: #work #programming #docker #postgres

# Postgres and docker
See also [[postgresql dev notes]] and [[seco sbc postgres database]]. This is a fresh start, trying to do a 22.04 docker container for developement. 

#### Start up a 22.04 docker container
run `sudo apt install postgresql-14`
#### Switch to `postgres` user
`sudo su postgres`
#### Restart service
`sudo service postgresql restart`

#### Create a database
Have to `create datatbase mdas;` first.
`sudo -u postgres psql -d mdas -a -f mdas_schema.sql`
#### Install [[libpqxx]]
Install the `libpqxx` library.
`sudo apt install libpqxx-dev`
This is version 6.4.5.
#### Run Seco PCS to verify
```bash
./opt_mdas_seco_pcs --cfg_file=/home/nanderson/repositories/project_opt_mdas/run/config_files/opt_mdas_seco_pcs_config/opt_mdas_seco_pcs_app.yml --lcm_url=udpm://239.255.76.67:12810?ttl=1
```

# References

