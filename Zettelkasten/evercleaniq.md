202407181435
Status: #idea
Tags: #work #programming #database 

# evercleaniq
- Built with [[elixir]] and [[phoeix]]
- The [[render]] deployment environment uses [[Postgresql]] 15. As ubuntu 22.04 only supports PG 14 out of the box, something else is going to be required.
- Instructions for installing postgres 15 alongside postgres 14 [can be found here](https://wiki.postgresql.org/wiki/Apt#Manual_Repository_Configuration)
	- This is likely to create some additional weirdness with the db stuff I set up for everclean, but we'll cross that bridge when we come to it.
- need to figure out how to work with npm/node_modules for this project. geotiff.js not working.

# TODO
List of things to review/fix for the future
- The `seed.exs` fails if we run `mix setup` more than once, because the generated SQL can't handle inserts with duplicate PKs
- The `Dockerfile` is only set up for PROD, and not integrated with our docker tools. 
	- Using our docker tools as-is will probably overwrite the existing `Dockerfile`
# References

