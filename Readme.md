***
# Local DNS Server
***
   We will use bind9 to deploy our local server, inside an docker container.

Install Docker & Docker compose, if you not have already.
DNS will use port 53..will use both tcp and udp for port 53 of host.
We need three volumes.. there virtual mounting present in docker-compose file..

## Configuration
   For configuration, we will use config directory files.
   1. named.conf
      It is main configuration file. #Don't miss ; 'semi-colon' at end
      -> first create access control list (acl | which ips can use that server?)
      -> Options:
         @forwarders: if our server can't resolve any name, where to forward further.
         @allow-query: who will access, you can pass acl in it.
      -> Zones:
         @<domain-name>: for which domain we creating zone.
         @IN-option:
            . type: "master": master means that domain must be solved by our dns server.
            . file: "<path/of/zone-file>: configuration of zone file.
   2. <zone-file>
      We will define zone details in that file.
      -> TTL: TTL, we set it to 2 days.
      -> Every new dns zone need two entries.
         1. SOA (start of authority records) 
            => administrative information about zone.
            => @, represent curent dns zone name
            => IN, Inner net (type)
            => SOA, DNS record type
            => <name>, nameserver
            => <administrator-email-address>
            => (
               serial number #set it like date... e.g 20240210
               12h #refresh time
               15m #retry
               3w  #expiry        
               2h #minimal time to live
            ), 

         2. Name server records.

         3. A record option to add ip of dns server
      -> Entries of hosts of type A (which you want to resolve).
      

## How to Run?
As, ubuntu already have some service, which try to resolve local dns..first stop them..

run `nano /etc/systemd/resolved.conf`
and uncommnet `#DNSStubListener=yes` --to-- `DNSStubListener=yes`
and change yes to no `DNSStubListener=yes` --to-- `DNSStubListener=no`

after that restart systemd..using... `sudo systemctl restart systemd-resolved`

after that start container...using... `sudo docker-compose up -d`

## How to Check
to check server responding correctly run 
   `nslookup <some-url> <dns-server-host-ip-where-bind-is-running>`

-----  Add DNS server IP in your router or firewall and now your Local DNS server is ready :) -----

