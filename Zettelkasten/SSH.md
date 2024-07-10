202401031417
Status: #idea
Tags: #programming #work 

# SSH
Secure shell

### Copy files to host
https://unix.stackexchange.com/a/106482

`scp <source> <destination>`
```
scp gss@34.200.227.190:/home/gss/workspace_output.log ./
```

#### SCP From the Seco
To get files off the Seco and onto the Mio, use `scp`:
```bash
scp fconnect@10.1.10.4:/home/fconnect/gss_updates/mdas_schema.sql [path to dest on mio]
```
# References

