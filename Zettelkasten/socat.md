202305261513
Status: #idea
Tags: #work #programming #networking

# socat
yeah, I don't think we have a script, I'd probably just do something like have gss_lcm2ascii write to [[udp]] then use socat, e.g. `socat UDP4-LISTEN:<PORT>,reuseaddr FILE:/file/to/write/to,create`

where `<PORT>` is the udp port gss_lcm2ascii is transmitting to


# References

