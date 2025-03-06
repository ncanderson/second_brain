202404182101
Status: #idea
Tags: #database 

# database migrations

See also [[database version control]] [here](https://blog.codinghorror.com/get-your-database-under-version-control/).

Relevant for [[Postgresql]].

Writing idempotent migrations: https://haacked.com/archive/2006/07/05/bulletproofsqlchangescriptsusinginformation_schemaviews.aspx/

## Desired Workflow
1. Make changes against currently installed database, by scripting any schema changes 
## Steps for DB Source Control and Migrations
- Clone/pull the database repo
- Run a build/install script. This script will:
- [ ] Check the local database version from the version table
- [ ] Run all migration scripts necessary to bring the local version up to the most recent (idempotently)
- [ ] Run a data init script that will initialize the database with the necessary inserts
- [ ] Do a full pg_dump, to create an up to date extract of EVERYTHING necessary for a brand new install in production
- [ ] Like libgss_types, this will mean you have to run the build prior to checking in your changes

So your work will entail:
- [ ] Writing any schema migrations necessary to support your feature
- [ ] Modifying the data init script to include any inserts necessary to support your feature (ie lookup tables)
- [ ] Inserting a new record into the version change table (there should be a script for this)
- [ ] Re-building everything and checking it all in. This should include:
	- [ ] Migrations
	- [ ] Data inserts
	- [ ] Re-run master create script

Additionally:
- [ ] Determine how to manage test data. We should have a script that will bulk insert test data, and this scripts should be checked in, but we'll need a mechanism to ensure that we don't run this in prod
- [ ] Determine how to manage users
- [ ] Determine how to source control the pg_hba.conf file to make it easier for a non-dba to run the psql commands (although maybe if we just wrap those in `sudo -u postgres psql ...`) and include them in a script, we don't need to do that.
- [ ] how do we downgrade the database and not lose data? since some data in the tables will require a certain schema, we can't necessarily put newer data into an older schema


Even if things are in flux right now we can still get to work on the baseline and then do local commits that mock changes to get the scripts right. 
# References
- https://medium.com/walkin/database-migration-writing-scripts-best-practices-3634c2134782
- https://dbup.readthedocs.io/en/latest/philosophy-behind-dbup/
- https://www.red-gate.com/simple-talk/databases/sql-server/t-sql-programming-sql-server/when-database-source-control-goes-bad/