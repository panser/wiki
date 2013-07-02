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
    * *none*
    * *gssapi*
    * *rfc931* ("ident")
    * *pam*  
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
  * **external.rotation**  If more than one `external` address is given, this governs which of the given addresses
is selected as the source address for a given outgoing connections/packet.
Note that regardless of what sort of external rotation you use, all addresses you want to choose from must be
listed via the external keyword first.
    * **none** the default. indicates the first address on the list of external addresses should be used.
    * **route**  indicates the `kernels routing table` should be consulted to find out what the source address for
a given destination will  be,  and might  require  you to set `user.privileged to root`.
    * **same-same** indicates the source address for a given destination should be the same address
as the Dante server accepted the client’s connection on.
  * **internal**  The  internal  addresses.   Connections  will  only  be accepted on these addresses.
The address given may be either a IP address or an interface name.
  * *libwrap.hosts_access*  If the server is compiled with libwrap support, determines whether the hosts_access()
function should be used for access  control.  When enabled  by setting this value to yes, the libwrap library
determines if TCP connections or UDP packets should be immediately dropped or not,
typically by consulting `/etc/hosts.allow and /etc/hosts.deny`. These checks are applied to all traffic,
before the  rule  processing starts. The *default value is no (disabled)*.
  * **logoutput**        This value controls where the server sends logoutput.  It can be set to 
    * *syslog[/facility]*
    * *stdout*
    * *stderr*
    * **filename**
    * *combination*
*The default is nowhere*. Note that if errorlog is also set, there will be a overlap between what is logged there
(errors only), and what will be logged here (errors, and everything else).
  * **method** A list of acceptable authentication methods for `socks-rules`, in order of preference
    * *bsdauth*
    * *gssapi*
    * **none**
    * **pam**
    * *rfc931*
    * **username**
The *default is no methods*, which means all socks-requests will be blocked.
If a method is not  set  in  this  list  it  will  never  be selected.
  * *socket* This  keyword  allows  you  to configure a few low-level socket options that relate to the socket buffers
the Dante server will use when communicating with clients and target hosts.
Note that these buffers will normally use kernel memory and the kernel may or may not honour the Dante server’s request.

Normally there should be no need to set these values, and the default values should suffice.

The keywords you can set are  *socket.recvbuf.udp, socket.sendbuf.udp, socket.recvbuf.tcp, socket.sendbuf.tcp*.

The numeric argument given to the keyword indicates the size of the socket buffer *in bytes*.
  * *srchost*   This  keyword  allows you to configure a few options that relate to the srchost, i.e.,
the host the Dante server accepts the connections from.
    * *nodnsmismatch* keyword, the server will not accept connections from addresses having a mismatch
between DNS IP address and hostname.  *Default is to accept them*.
    * *nodnsunknown* keyword, the server will not accept connections from addresses without a DNS record.
*Default is to accept them.*
    * *checkreplyauth* keyword, the server will check that the authentication on bind-replies and udp-replies
matches that which is set in the rule and global method.  Normally, authentication is not desired on these replies,
as they are replies sent to the socks-clients,  from non-socks clients, and thus only a limited set of
authentication methods are possible.  These methods are the methods not involving the socks protocol;
*rfc931* and *pam* (using only the ipaddress and portnumber).  *Default is not to check the authentication on replies*.
  * *timeout.connect*     The number of seconds the server will wait for a connect initiated on behalf of
the socks-client to complete.  The *default is 30*.   Setting it to `0` will use the systems default.
  * *timeout.io*  The  number  of seconds an established connection can be idle. The *default is 86400 (24 hours)*.  
Set it to `0` for forever. Individual timeouts can be set for TCP and UDP by suffixing io with ".<protocolname>",
i.e. `timeout.io.tcp or timeout.io.udp`.
  * *timeout.negotiate*     The number of seconds a client can spend negotiating with the Dante server for a socks
session before Dante will close the connection to the client.  The *default is 30*.  
Set it to 0 for forever, though that is strongly discouraged.
  * *timeout.tcp_fin_wait*  The timeout for the equivalent of TCP’s FIN-WAIT-2.  The *default is 0*,
which means use the systems default (normally, no timeout).
  * **udp.connectdst**   Enables or disables whether the server should attempt connecting UDP sockets to the destination. 
The *default is yes*, which improves UDP performance, but may not be compatible with some UDP-based
application protocols as it means  the server can only receive packets from the destination address.
  * *Userids*   On  platforms  providing  a privilege-model supported by Dante, the Dante server does not
use userid-switching via the seteuid(2) system call.  On other platforms, it is prudent to set the userid
to be used by the Dante server to appropriate values.  The Dante  server  can use two different userids,
or three if compiled with libwrap support.  They are as follows:
    * **user.privileged**   Username which will be used for doing privileged operations. 
If you need special privileges to read the sockd.conf file or to write the  sockd.pid file (you can create it manually
before starting sockd), have anything in your configuration that requires binding  privileged TCP/UDP ports
(ports below 1024), or use some sort of password-based authentication, this probably needs to be set to `root`.
    * **user.unprivileged**    User  which  the server runs as most of the time.  This should be an id with as
little privileges as possible.  It is recommended that a separate sockd userid is created for this.
If not, you can probably set it to the same value as user.unprivileged.
    * *user.libwrap*  User used to execute libwrap commands.  Normally this should be the same as *user.unprivileged*

### MODULES
The following modules are supported by Dante.  Modules are purchased separately from Inferno Nettverk A/S 
and may add extra functionality  that is not needed by most users
  * **bandwidth** module gives you control over how much bandwidth the Dante server uses on behalf of different clients.
  * **redirect** module gives you control over what addresses the server will use on behalf of the clients,
aswell as allowing you to redirect client requests to a different addresses.
  * **session** module gives you control over the number of sessions that can be created by different socks users.
  * 
### AUTHENTICATION METHODS
  * **none**   This method requires no form of authentication.
  * **username**  This method requires the client to provide a username and password. 
This must match the username and password given in the system password file.
  * **gssapi** This method requires the setup of a Kerberos environment and can provide strong encryption and authentication.
  * *rfc931* This method requires the client host to provide a rfc931 ("ident") reply for the connecting client. 
This must match a username given in the system password file.
  * **pam**    This method requires the available client data to match against the pam database.
  * *bsdauth*       This method requires the available client data to be verified by the BSD Authentication system.

### ADDRESSES
Each `address` field can consist of a 
  * IP address (and where required, a netmask, separated from the IP address by a  ’/’  sign),  
  * a  hostname,
  * a domainname (designated so by the leading ’.’), 
  * an interface name.
Each address can be followed by a optional `port` specifier.

## RULES
There  are two sets of rules and they work at different levels.  
  1. Rules prefixed with `client` are checked first and are used to see if the client is allowed to connect
to the Dante server.  We call them "**client-rules**". It is recommended  that these *do not use hostnames 
but only IP addresses*, both for security and performance reasons.  These rules work  at  the TCP level.
  2. ther  rules,  which we call "**socks-rules**" are a level higher and are checked after the client connection
has been accepted by the *client-rules*.  The socks-rules are used to evaluate the socks request that the client sends.

Both set of rules begin with a **pass or deny** keyword, 
but the client-rules have "client " in front of the  pass/deny  keyword.

Both the client-rules and the socks-rules also specify a **from/to** address pair 
which gives the addresses the rule will match.
  * In the *client-rule context*, to means the address the request is accepted on, i.e., 
a address the Dante server listens on.
  * In the *socks-rule context*, to means the client’s destination address, as expressed in the client’s socks request.
I.e., the address the  Dante server should connect to (for TCP sessions) or send packets to (for UDP session) on behalf of the client.

> Both  set  of  rules are evaluated on a "first match is best match" basis.  
> That means, the first rule matched for a particular client or socks request is the rule that will be used.

There are two forms of keywords; `conditions` and  `actions`. For each rule, 
  * all `conditions` are checked
  * and if they match the request, all `actions` are executed.

The list of **condition* keywords is:
  * *clientcompatibility*
  * *command*
  * *from*
  * *group*
  * *method*
  * *protocol*
  * *proxyprotocol*
  * *to*
  * *user*

The  list  of  **action**  keywords  is:
  * *bandwidth*
  * *libwrap*
  * *log*
  * *maxsessions*
  * *redirect*
  * *timeout.connect*
  * *timeout.negotiate*
  * *timeout.io*
  * *time-out.tcp_fin_wait*
  * *udp.portrange*

The contents of a **client-rule can be:**
  * **bandwidth**  The  clients matching this rule will all share the given amount of bandwidth, 
measured in bytes per second.  Requires the bandwidth module.
  * *clientcompatibility*  Enables certain options for compatibility with broken clients.  Valid values are:
    * *necgssapi*, for compatibility with clients implementing  GSSAPI the NEC socks way.
  * **from**   The rule applies to requests coming from the address given as value.
  * *group*  The user must belong to one of the groups given as value. 
Note  that  if  gssapi-based authentication is used, the username as provided to the Dante server
normally includes the Kerberos domain.  The name must be listed on the same form here and in the system groupfile
(usually /etc/passwd) if it is to be used.
  * *gssapi.enctypeWhich* encryption to enforce for GSSAPI-authenticated communication. Possible values are
    * *clear*
    * *integrity*
    * *confidentiality*
The  default is to accept whatever the client offers.
  * *gssapi.keytab*        Value for keytab to use.  The default is "`FILE:/etc/sockd.keytab`".
  * *gssapi.servicename*  Which servicename to use when involving GSSAPI.  *Default is "rcmd"*.
  * *libwrap*    The server will pass the line to libwrap for execution.
  * **log**    Used to control logging.  Accepted keywords are 
    * **connect**
    * **disconnect**
    * **data**
    * **error**
    * **iooperation**
The *default is no logging*.
  * **maxsessions**          Limit the number of active sessions allowed by this rule to the given value.  
Requires the session module.
  * **method** Require that the connection be "authenticated" using one of the given methods.
  * *pam.servicename*         Which servicename to use when involving pam.  *Default is "sockd"*.
  * *port*   Parameter  to from, to and via.  Accepts the keywords `eq/=, neq/!=, ge/>=, le/<=, gt/>, lt/<` 
followed by a number.  A portrange can also  be given as "`port <start #> - <end #>`", 
which will match all port numbers within the range <`start #`> and <`end #`>.The *default is all ports*.
  * *redirect*  The source and/or destination can be redirected using the redirect statement. 
Requires the redirect module. The syntax of the redirect statement is as follows:  `redirect from: ADDRESS`
  * *timeout.negotiate*      See the global timeout.negotiate option.
  * **to**     The rule applies to requests going to the address given as value.
  * **user**   The user must match one of the names given as value.  
If no user value is given for a rule requiring usernames, 
the effect will  be  the  same as listing every user in the password file. 
Note  that  if  gssapi-based authentication is used, the username as provided to the Dante server 
normally includes the Kerberos domain.  The name must be listed on the same form here if it is to be used.

The contents of a **socks-rule can be:**
  * **bandwidth**   The clients matching this rule will all share the given amount of bandwidth, 
measured in bytes per second.  Requires the bandwidth  module.
  * *bsdauth.stylename*   The name of the BSD authentication style to use. 
The default is to not specify a value, causing the default system style to be used. 
  * **command**  The  rule  applies  to the given commands.  Valid commands are 
    * *bind*
    * *bindreply*
    * *connect*
    * *udpassociate*
    * *udpreply*
Can be used instead  of, or to complement, protocol.  The *default is all commands* valid for the protocols allowed by the rule.
  * **from**   The rule applies to requests coming from the address given as value.
  * *group*  The user must belong to one of the groups given as value.
  * *libwrap*         The server will pass the line to libwrap for execution.
  * **log**    Used to control logging.  Accepted keywords are 
    * **connect**
    * **disconnect**
    * **data**
    * **iooperation**
  * *maxsessions*         Limit the number of active sessions allowed by this rule to the given value. 
Requires the session module.
  * *method* Require that the connection be established using one of the given methods.  
method always refers to the source part of the rule.   Valid  values are the same as in the global method line.
  * *pam.servicename*   What servicename to use when involving pam.  *Default is "sockd"*.
  * *port*   Parameter  to from, to and via.  Accepts the keywords `eq/=, neq/!=, ge/>=, le/<=, gt/>, lt/<` 
followed by a number.  A portrange can also  be given as "`port <start #>` - `<end #>`", 
which will match all port numbers within the range <`start #`> and <`end #`>. The *default is all ports*.
  * *protocol*  The rule applies to the given protocols.  Valid values are *tcp and udp*.  
The default is all supported protocols that can  apply  to  the  given commands.
  * **proxyprotocol**  The  rule applies to requests using the given proxy protocol.  
Valid proxy protocols are *socks_v4 and socks_v5*.  The *default is all* supported proxy protocols.
  * *redirect*    The source and/or destination can be redirected using the redirect statement. 
Requires the redirect module.  The syntax of the redirect statement is as follows:
    redirect from: ADDRESS
    redirect to: ADDRESS
  * *timeout.connect*     See the global timeout.connect option.
  * *timeout.io*      See the global timeout.io option.
  * *timeout.tcp_fin_wait*      See the global timeout.tcp_fin_wait option.
  * **to**     The rule applies to requests going to or using the address given as value.  
Note that the meaning of this address is  affected  by  command.
  * *udp.portrange*     The  argument  to this keyword is two portnumbers, separated by a dash (’-’).  
They specify the UDP port-range that will be used between  the socks-client and the Dante-server for UDP packets.  
Note that this has no relation to the UDP port-range  used  between  the  Dante-server and external, non-socks, 
clients/servers.
  * **user**   The  user  must match one of the names given as value.  If no user value is given for a rule 
requiring usernames, the effect will be the  same as listing every user in the password file.

## ROUTES
The routes are specified with a **route keyword**.  Inside a pair of parenthesis (`{}`) a set of keywords
control the behavior  of  the  route. This is used to perform so-called "*server-chaining*",
where one socks-server connects to another socks-server further upstream.
> Note that at present, server-chaining is only supported for the connect command.
