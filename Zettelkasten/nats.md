202312111342
Status: #idea
Tags: #programming #work 

# nats https://nats.io/
Scalable data streaming platform. Uses basic request-reply pattern. 
Services vs streams. Pub/sub paradigm.
There will be a server, and any number of clients. Is the MDAS NATS server managed by the seco?

### NATS Core
standard messaging API

### NATS JetStream
Persitance, streaming, materialized views of your data

### NATS CLI
Can do everything here. Guide was in mac:
`brew install nats-io/nats-tools/nats`
`nats server run`
`nats context select nats_development` Dev user. May need to create a user.
40 Client libs. Basic download is the server, client libs control listeners.
`nats reply hello.jeremy` "Hi"
# References

