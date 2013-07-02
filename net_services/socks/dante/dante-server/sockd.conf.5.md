# sockd.conf.5
## DESCRIPTION
The configuration file for the **Dante server** controls both access controls and logging.

It is divided into three parts
  1. **server settings**
  2. **rules**
  3. **routes**

## SERVER SETTINGS
The server settings control the generic behaviour of the server.  Each keyword is separated from it’s value
by a `’:’` character.
  * *child.maxidle*      Maintains a rough maximum on how many children of each type can remain idle waiting for clients. 
The default is `yes` (maintain a maximum on how many processes can be idle), which is the recommended value for
optimal resource usage. 

It can be set to `no` if it is desirable to never shut down idle processes.  This can be used if client bursts
are  frequent  and  it  is  important  to serve them as fast as possible, with no delay waiting for new server
processes to be created, or to see how many processes were used to handle the peak load.
  * *clientmethod*   A list of acceptable authentication methods for `client-rules`, in order of preference.
It is thus important that you  specify  these  in the correct order, normally with the more secure methods first.
    * none
    * gssapi
    * rfc931 ("ident")
    * pam  
The  *default value is all methods* that may be necessary for the later socks-based authentication,
as specified in the socks-method line.
  * *compatibility*
    * *sameport*  the server attempts to use the same port on the server’s external side as the client
used  on  the  server’s internal side.  This is normally the *default*,
but when this option is given it will be done with privileged ports also.
    * *draft-5.05* enable usage of parts of the `socks v5-05 draft`.  The only feature from this draft that
Dante supports is the `"USECLIENTSPORT" extension`.  Note that there is a conflicting interpretation of this extension,
so enabling  it  might  prevent  clients  using the conflicting interpretation from working correctly.
This only affects UDP.
  * **debug**  Print debug info to the logs.  The value sets the debug level.
  * **errorlog**  This  value  can  be set to receive only error-related logoutput.  Note that this does not include
client-specific errors, but only more serious "global" errors. The possible values are the same as for the **logoutput**
keyword mentioned below. The default  is  to  not have any special place to log errors.
  * **external**   The  address to be used for outgoing connections.  The address given may be either a `IP address` or an `interface name`.
Can be given multiple times for different addresses.
  * 
