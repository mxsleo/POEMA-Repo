```
## Last commit: 2024-09-25 16:44:39 UTC by moro
version 14.1R4.8;
system {
    host-name PITER-IX;
    root-authentication {
        encrypted-password "$1$ZnwDdos5$.LM.piO8Ywyz1nsMBYN/u/"; ## SECRET-DATA
    }
    login {
        user admin {
            uid 2001;
            class super-user;
            authentication {
                encrypted-password "$1$HVC/voCG$lj4L4eRazwNXa9RFdwxzO0"; ## SECRET-DATA
            }
        }
        user moro {
            uid 2000;
            class super-user;
            authentication {
                encrypted-password "$1$bdm4w9hs$UBWBoVf1ByTiOpjYT9GFb1"; ## SECRET-DATA
            }
        }
    }
    services {
        ssh {
            protocol-version v2;
        }
        netconf {
            ssh;
        }
    }
    syslog {
        user * {
            any emergency;
        }
        file messages {
            any notice;
            authorization info;
        }
        file interactive-commands {
            interactive-commands any;
        }
    }
}
interfaces {
    ge-0/0/0 {
        description "## -C- | MSK Client";
        flexible-vlan-tagging;
        ##
        ## '300' was inherited from group 'GROUP-IFACE-MTU'
        ##
        mtu 300;
        encapsulation flexible-ethernet-services;
        unit 100 {
            encapsulation vlan-bridge;
            vlan-id 100;
        }
    }
    ge-0/0/1 {
        description "## -C- | RND Client";
        flexible-vlan-tagging;
        ##
        ## '300' was inherited from group 'GROUP-IFACE-MTU'
        ##
        mtu 300;
        encapsulation flexible-ethernet-services;
        unit 100 {
            encapsulation vlan-bridge;
            vlan-id 100;
        }
    }
    em0 {
        description "## -M- | MGMT";
        unit 0 {
            family inet {
                address 10.253.6.10/16;
            }
        }
    }
}
protocols {
    lldp {
        interface all;
    }
}
bridge-domains {
    IX {
        domain-type bridge;
        vlan-id 100;
        interface ge-0/0/1.100;
        interface ge-0/0/0.100;
    }
}
```
