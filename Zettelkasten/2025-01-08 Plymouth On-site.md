
- Issue with `gss_system_monitor` made it challenging to log system resource usage. It appears that within that application, there is a call to `chronyc sources` that is taking a long time. The application is waiting until that completes, so we were only getting output from it every 2 minutes or so.
- Overall, minimal impact from loading to the database. Memory issues seem unaffected.
- CPU impact summary:

| Sample Rate | CPU %   |
| ----------- | ------- |
| 0           | ~10-11% |
| 5           | ~13%    |
| 10          | ~14-15% |
| 50          | ~18-19% |

