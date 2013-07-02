# socks.conf.5
## DESCRIPTION
The configuration file for the **socks client** library allow control over logging and server selection.

## FORMAT
  * debug  Setting this field to 1 turns on debugging.
  * logoutput   This value controls where the client library sends logoutput.  It can be either syslog, stdout, stderr, a filename,  or  a  combination.  The default is no logging.
  * resolveprotocol          The protocol used to resolve hostnames.  Valid values are udp, tcp and fake.  The default is udp.
  * route.badexpire        How long the "bad" marking of a route should remain set before it is removed.  Default is 300 seconds.
  * route.maxfail    How many times a route can fail before it is marked as bad.  Default is 1.
  * timeout.connect         The  number  of  seconds  the  client  will wait for a connect to the proxy server to complete.  The default is 0, indicating the client  should use the systems default.

## ROUTES
The routes are specified with a **route keyword**.  Inside a pair of parenthesis (**{}**) a set of keywords control the behavior of  the  route.   
Each route  can  contain  three address specifications; **from**, **to** and **via**.

When searching for a route to match the clients request, the library will first look for a direct route.
Then for a **socks_v4 route**, a **socks_v5 route**, a **http route**, and lastly for a **upnp route**.

```
...
```
