202408121051
Status: #idea
Tags: #programming #elixir #phoenix #work 

# Phoenix
- Built in [[elixir]]
- Liveview is a process, that manages state internally, in a LiveView [[socket]]
- Single page web app
- Runs on 'the BEAM' (Bogdan Erlang Abstract Machine), the Erlang virtual machine. 
On initial page load, a standard [[HTTP]] GET request will get the initial resources. Afterwards, the liveview client side will init a two way [[WebSocket]] connection. 

[Phoenix controllers](https://hexdocs.pm/phoenix/controllers.html) act as intermediary modules. Their functions — called actions — are invoked from the router in response to HTTP requests. The actions, in turn, gather all the necessary data and perform all the necessary steps before invoking the view layer to render a template or returning a JSON response.

LiveView will eliminate the need for a separate server-side application; the framework manages it for you. You don't have to 'teach' the app how to send a message to the server, don't have to trigger page changes, etc. The framework will track diffs, and change page layout accordingly

Router defs specify the URL path and what module will handle it. Those modules will require `use Phoenix.LiveView`.

Last line is automatically returned from a function

[[Ecto]] is generally (for me so far) used for db access.

# References
