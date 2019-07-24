
bastion:
```
Host <alias>
    User <user_name>
    HostName <taget_host>
    IdentityFile ~/.ssh/<your_key>
    ProxyCommand ssh -i ~/.ssh/<your_key> <user_name>@<proxy_host> -W %h:%p
```
