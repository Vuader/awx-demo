# awx-demo

Just some Demo playbooks showcasing how to manage Network devices with AWX

Notes on BNG playbook
---------------------
Example freeradius return attributes that work with this config:

users file:

    steve   Cleartext-Password := "testing"
        Service-Type = Framed-User,
        Framed-Protocol = PPP,
        Framed-MTU = 1500,
        ERX-Address-Pool-Name = PPPOE-POOL

clients.conf (update with applicable radius source-address):

    client vBNG1 {
            ipaddr          = 192.0.2.1
            secret          = testing123
    }
    
Example Mikrotic CPE config:

    /interface pppoe-client
    add add-default-route=yes disabled=no interface=ether1 name=pppoe-out1 password=testing user=steve
    /ipv6 settings
    set accept-router-advertisements=yes
    /ip dhcp-client
    add disabled=no interface=ether2
    
Remember for v6 to work the v6 package must be enabled.

     /system package print
     
To see the number for ipv6

    /system package enable 3
