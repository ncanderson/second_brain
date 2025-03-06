202403211138
Status: #idea
Tags: #database #programming #postgres 

# uuid-ossp
Extension to generate UUIDs.
```sql
--
-- Name: uuid-ossp; Type: EXTENSION; Schema: -; Owner: -
--
CREATE EXTENSION IF NOT EXISTS "uuid-ossp" WITH SCHEMA public;

--
-- Name: EXTENSION "uuid-ossp"; Type: COMMENT; Schema: -; Owner: 
--
COMMENT ON EXTENSION "uuid-ossp" IS 'generate universally unique identifiers (UUIDs)';
```

Use by:
```sql
select uuid_generate_v4();
```
# References

