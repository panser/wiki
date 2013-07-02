```
# cat /etc/sockd.conf|grep -v "^#"|grep -v "^$"
logoutput: /var/log/sockd.log
internal: eth0 port = 20001
external: eth0
method: username none #rfc931
user.privileged: root
user.unprivileged: nobody
client pass {
from: 0.0.0.0/0 to: 0.0.0.0/0
log: disconnect error
}
pass {
from: 0.0.0.0/0 to: 0.0.0.0/0
command: connect udpassociate
log: connect disconnect error iooperation
}
```
