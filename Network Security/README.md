### Network Security  
#### 1.1 Information Gathering
_1.1.1 Passive Recon_  

WhoIs Look-ups
```
$ whois domain.com
or
$ sudo nmap --script whois-domain domain.com -sn
```
DNS Enumeration  
Queries w/ Nslookup
```
$ Nslookup -query=<Arg(any/MX/NS)> domain.com
or interactive
$ nslookup
>set q=<Arg(A/MX/NS)>
>domain.com
```
Queries and Zonetransfers w/ Dig
```
$ Dig +nocmd domain.com <Arg(MX/NS/A)> +noall +answer
$ Dig +nocmd domain.com AXFR +noall +answer @domain.com // Zone Transfer
```
Other Options for DNS Enumeration
```
$ fierce -dns domain.com
$ dnsrecon -d domain.com
$ dnsmap domain.com
```

_1.1.2 Host Discovery_  
Nmap
```
$ sudo nmap -sn targetIP --disable-arp-ping
$ sudo nmap -sn -P<Arg(S/A)> targetIP --disable-arp-ping // TCP packet with SYN or ACK flag
$ sudo nmap -sn -PE targetIP --disable-arp-ping // ICMP echo request
$ sudo nmap -sn -PP targetIP --disable-arp-ping // Timestamp request
```
FPing
```
$ fping -A targetIP -e  // send ICMP echo packet, check if alive
$ fping -q -a -g target/24 -r 0 -e // send ICMP echo to subnet with no retries in quiet mode, only show if alive
```
HPing3
```
$ sudo hping3 -F -P -U target -c 3 // Xmas discovery scan, RA flag
$ sudo hping3 -1 192.168.1.x --rand-dest -I eth2 // host discovery on subnet on specificed interface
```

_1.1.3 Active Recon_  
**Basic**  
Nmap
```
$ sudo nmap -sS target -n // SYN scan, no hostname resolution
$ sudo nmap -sS -n -Pn -iL target.txt // SYN scan, from list of known live hosts
$ sudo nmap -sT target -F // TCP connect scan
$ sudo nmap -sU target -p 21,53,80,111,137 // UDP scan on specificed ports
$ sudo nmap -sX target --top-ports 200 // Xmas scan on top 200 ports
```
**Advanced**  
Nmap
```
Bypass firewall with fragmentation  
$ sudo nmap -f -sS targetIP -n -p 80,21,153,443 --disable-arp-ping -Pn --data-length 48
Bypass firewall by spoofing MAC or vendor 
$ sudo nmap --spoof-mac apple targetIP -p 80 -Pn --disable-arp-ping -n 
Bypass firewall with source port
$ sudo nmap --source-port 53 targetIP -sS
Bypass firewall with Decoys
$ sudo nmap -sS -DdecoyIP,decoyIP,ME,decoyIP targetIP -n
```
Nmap NSE (Location: /usr/share/nmap/scripts/)
```
$ sudo nmap --script-updatedb // Update NSE DB
$ sudo nmap --script-help "smb*" and discovery // search for script
$ sudo nmap --script auth targetIP // all authentication scripts, loud
$ sudo nmap --script smb-os-discovery -p 445 targetIP // SMB OS discovery
$ sudo nmap --script smb-enum-shares targetIP -p 445 // Enumerate SMB shares
```
Idle Scans w/ Nmap & Hping3
```
$ sudo nmap --script ipidseq targetIP -p 135 // Check if IP good zombie candidate, use known open port for accuracy
$ sudo nmap -sI zombieIP:135 targetIP -p 23 -Pn // Idle scan remote host with zombie IP on specified port

$ sudo hping3 -S -r targetIP -p 135 // Check if host good zombie candidate, look for id increment by 1
$ sudo hping3 -a zombieIP -S targetIP -p 23 // Spoof source to zombie IP, if increments by 2 then port is open
```
#### 1.2 Enumeration  
_1.2.1 NetBIOS Enumeration_  
Enum4Linux & SMBClient
```
$ enum4linux -a -v targetIP // NetBIOS enumeration scan
$ smbclient -L targetIP // List share names
$ smbclient \\\\targetIP\\Folder // Access share folder
smb:> get filename.txt /home/root/Desktop/filename.txt
```