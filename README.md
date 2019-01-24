# notes
##route table + port forwarding
route table
```
sudo nano /private/etc/hosts
127.0.0.1       localhost
255.255.255.255 broadcasthost
::1             localhost
10.0.0.1    whatyouwant.new
```
port forwarding
```
sudo ifconfig lo0 10.0.0.1 alias
echo "rdr pass on lo0 inet proto tcp from any to 10.0.0.1 port 80 -> 127.0.0.1 port 3000" | sudo pfctl -ef -
```
