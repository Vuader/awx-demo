# awx-demo

Just some Demo playbooks showcasing how to manage Network devices with AWX

Notes on BNG playbook
---------------------
Example freeradius return attributes that work with this config:

clients.conf (update with applicable radius source-address):

    client vBNG1 {
            ipaddr          = 192.0.2.1
            secret          = testing123
    }
    
The encrypted version of this secret, to be configured on the BNG, is $9$LLxXVYJZjqPQDiQn9pREKM8XdbYgoGjH


users file:

  routing-instance = RINAME

  Case1: /64 via NDRA for CPE WAN

    steve   Cleartext-Password := "testing"
        Service-Type = Framed-User,
        Framed-Protocol = PPP,
        Framed-MTU = 1500,
        ERX-Virtual-Router-Name = RINAME,
        ERX-Address-Pool-Name = RINAME-v4-POOL,
        ERX-IPv6-Delegated-Pool-Name = RINAME-PD-POOL

    Example Mikrotic CPE config:

        /interface pppoe-client
        add add-default-route=yes interface=ether1 name=pppoe-out1 password=testing user=steve disabled=no
        /ipv6 settings
        set accept-router-advertisements=yes
        /ipv6 address
        add address=::1 from-pool=mypool interface=ether2 advertise=yes
        /ipv6 dhcp-client
        add interface=pppoe-out1 pool-name=mypool request=prefix

  Case2: /128 via DHCPv6 IA_NA from CPE WAN

    steve   Cleartext-Password := "testing"
        Service-Type = Framed-User,
        Framed-Protocol = PPP,
        Framed-MTU = 1500,
        ERX-Virtual-Router-Name = RINAME,
        ERX-Address-Pool-Name = RINAME-v4-POOL,
        Framed-IPv6-Pool = RINAME-V6-POOL,
        ERX-IPv6-Delegated-Pool-Name = RINAME-PD-POOL

    Example Mikrotic CPE config:

        /interface pppoe-client
        add add-default-route=yes interface=ether1 name=pppoe-out1 password=testing user=steve disabled=no
        /ipv6 settings
        set accept-router-advertisements=no
        /ipv6 dhcp-client
        add interface=pppoe-out1 pool-name=mypool request=address,prefix

  Case3: NO IPv6 on CPE WAN!!!!!
         (still works because on BNG PD route next hop is "access" on interface of pppoe session,
          and on CPE we have default route out of pppoe)

    steve   Cleartext-Password := "testing"
        Service-Type = Framed-User,
        Framed-Protocol = PPP,
        Framed-MTU = 1500,
        ERX-Virtual-Router-Name = RINAME,
        ERX-Address-Pool-Name = RINAME-v4-POOL,
        ERX-IPv6-Delegated-Pool-Name = RINAME-PD-POOL

        /interface pppoe-client
        add add-default-route=yes interface=ether1 name=pppoe-out1 password=testing user=steve disabled=no
        /ipv6 settings
        set accept-router-advertisements=no
        /ipv6 address
        add address=::1 from-pool=mypool interface=ether2 advertise=yes
        /ipv6 dhcp-client
        add interface=pppoe-out1 pool-name=mypool request=prefix

    
Remember for v6 to work the v6 package must be enabled.

     /system package print
     
To see the number for ipv6

    /system package enable 3
