202405291149
Status: #idea
Tags: 

# pg_dump
Export tool for postgres

Export all (and only) functions to a file:
```bash
nanderson ~/Desktop/tmp/test_dump () $ psql -U postgres -At everclean_cleaning > output.sql << "__END__"
SELECT pg_get_functiondef(f.oid)
FROM pg_catalog.pg_proc f
INNER JOIN pg_catalog.pg_namespace n ON (f.pronamespace = n.oid)
WHERE n.nspname = 'public';
__END__

```


# References

