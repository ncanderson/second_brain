202308241555
Status: #idea
Tags: #programming #linux 

# Valgrind

Good tool for resolving [[segfault|segfaults]].
Scott Hara: Does anyone have recommended flags for running `valgrind` on an application?
Shay: depends on what you're trying to learn about the application
- `valgrind --tool=helgrind ...` is good for finding threading issues
- `valgrind --track-origins=yes ...` is what I usually use if I suspect some sort of undefined behavior related to bad memory access or uninitialized variables
- [https://avdongre.wordpress.com/2014/08/13/suppressions-in-valgrind/](https://avdongre.wordpress.com/2014/08/13/suppressions-in-valgrind/)
- and then `valgrind --leak-check=full ...` is what I usually use for memory leaks
- `valgrind --tool=memcheck --leak-check=full <app_name and args>

## Sample Command
```bash
valgrind --track-origins=yes --leak-check=full ./gss_ec_coverage --cleaning_meta_cfg_file=/home/nanderson/repositories/project_everclean_topside/run/config_files/gss_ec_coverage_config/cleaning_meta.yml --cfg_file=/home/nanderson/repositories/project_everclean_topside/run/config_files/gss_ec_coverage_config/gss_ec_coverage_app.yml --logging_level=0 &> valgrind.txt
```

# References

