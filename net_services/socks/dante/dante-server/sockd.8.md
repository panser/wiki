# sockd.8
## DESCRIPTION
**Dante** implements the socks standard and can function as a firewall between networks.

**Dante** relays TCP and UDP both from outside the network and in, and from inside and out.

## SYNOPSIS
    sockd [-DLVdhnv] [-N number] [-f file] [-p file]
  * **-D**     Dante will detach from the controlling terminal and run in the background as a system daemon.
  * *-L*     Displays the license Dante comes under.
  * **-Nnumber**  Dante  will  fork  of number "main servers" when starting.  This can be used for servers that are extremely
busy, or to provide a (very)  poor mans redundancy setup, as Dante will try to make these "main servers"
as independent of each others as possible.
  * **-V**     Verifies config file and exits.
  * **-dnumber**  Sets the level of debugging, `zero` to disable.
  * *-ffile* Dante will read its configuration from file.
  * *-h*     Shows the currently valid options.
  * **-n**     Disables TCP keep-alive messages.  Normally Dante enables TCP keep-alive messages so that
connections from machines that have crashed or  for  other  reasons  no  longer  can  be  reached  time  out.
Note that this can take several hours.  See also the "`timeout`" option in  sockd.conf(5).
  * *-pfile* Dante will write the pid of the main process to this file.  file.
  * **-v**     Displays Dante version.
  
## SIGNALS
  * *SIGHUP* Reload the configuration file.  Will also reopen logfiles.
  * **SIGINFO/SIGUSR1**     Print some information about the servers current state and clients.
