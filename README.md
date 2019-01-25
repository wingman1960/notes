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


for more then 2 ips:
ref: https://gist.github.com/kujohn/7209628

### Port Forwarding in Mavericks

----

Since Mavericks stopped using the deprecated `ipfw` (as of Mountain Lion), we'll be using `pf` to allow port forwarding.


####1. anchor file
Create an anchor file under `/etc/pf.anchors/<anchor file>` with your redirection rule like:
```
rdr pass on lo0 inet proto tcp from any to 127.0.0.2 port 80 -> 127.0.0.1 port 40070
```

####2. Test the anchor file
Parse and test your anchor file to make sure there are no errors:
```
sudo pfctl -vnf <anchor file>
```

####3. Reference the anchor in pf.conf
`/etc/pf.conf` is the main configuration file that `pf` loads at boot.
We'll need to load the anchor file we previously created:
```
rdr-anchor "forwarding"
load anchor "forwarding" from "/etc/pf.anchors/<anchor file>"
```

Make sure to add these entries to the appropriate spot.

####4. Load and enabling pf
`pf` is not enabled by default in Mavericks, few ways to enable this:

* Manually load and enable from a pf.conf file via `sudo pfctl -ef <pf.conf file>`

* Auto enable by creating a launch daemon via [this doc](https://developer.apple.com/library/mac/documentation/macosx/conceptual/bpsystemstartup/Chapters/CreatingLaunchdJobs.html) to run `pfctl -ef <pf.conf file>` on boot.

* Auto enable by adding an `-e`(enable) to the `pfctl` ProgramArgument in `/System/Library/LaunchDaemons/com.apple.pfctl.plist` like this:
```
<key>ProgramArguments</key>
<array>
<string>pfctl</string>
<string>-e</string>
<string>-f</string>
<string>/etc/pf.conf</string>
</array>
```

####5. Forwarding across interfaces
By default, `pf` does not forward between interfaces. Here's a snippet from man for `pfctl` with help from [2sidedfigure](https://github.com/2sidedfigure):

```
The packet filter does not itself forward packets between interfaces.  Forwarding can be enabled by setting the sysctl(8) variables net.inet.ip.forwarding and/or net.inet6.ip6.forwarding to 1.  Set them permanently in sysctl.conf(5).
```
We'll need to enable this by adding to `/etc/sysctl.conf`:
```
net.inet.ip.forwarding=1
net.inet6.ip6.forwarding=1
```

#### Caution
There is the possibility that `pf.conf` will be overriden with updates to the OS. It might be best to create your own pf config file and load them in additon to the main `pf.conf` to prevent this.

anybody else who gets "Rules must be in order" error on a default Yosemite setup, the two lines can be placed immediately after the line: "rdr-anchor "com.apple/*" (note rdr-anchor, not anchor) You can't just append them to the end of the file.
