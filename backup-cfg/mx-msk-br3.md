```
## Last commit: 2024-09-25 16:42:05 UTC by moro
version 14.1R4.8;
system {
    host-name mx-msk-br3;
    root-authentication {
        encrypted-password "$1$B6VTsDKC$7w0mRcg9PiIeuxSzapRQ0/"; ## SECRET-DATA
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
                encrypted-password "$1$QKWQVH1E$UFVOplv303eIxgzF8fV98/"; ## SECRET-DATA
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
        description "## -B- | to mx-msk-br1 ge-0/0/2";
        mtu 2000;
        unit 0 {
            family inet {
                address 10.12.99.7/31;
            }
        }
    }
    ge-0/0/1 {
        description "## -B- | to mx-msk-br4 ge-0/0/2";
        mtu 2000;
        unit 0 {
            family inet {
                address 10.12.99.2/31;
            }
        }
    }
    em0 {
        description "## -M- | MGMT";
        unit 0 {
            family inet {
                address 10.253.6.14/16;
            }
        }
    }
    lo0 {
        description "## -M- | Loop internal";
        unit 0 {
            family inet {
                address 10.255.99.3/32;
            }
        }
    }
}
routing-options {
    router-id 10.255.99.3;
    autonomous-system 2222;
}
protocols {
    bgp {
        group iBGP {
            type internal;
            local-address 10.255.99.3;
            family inet {
                unicast;
            }
            export POL-BGP-IBGP-OUT;
            vpn-apply-export;
            neighbor 10.255.99.1;
            neighbor 10.255.99.2;
            neighbor 10.255.99.4;
        }
    }
    ospf {
        no-rfc-1583;
        area 0.0.0.0 {
            interface lo0.0 {
                passive;
            }
            interface ge-0/0/0.0;
            interface ge-0/0/1.0;
        }
    }
    lldp {
        interface all;
    }
}
policy-options {
    policy-statement POL-BGP-IBGP-OUT {
        term T-DIRECT {
            from protocol direct;
            then {
                next-hop self;
                accept;
            }
        }
        then reject;
    }
}
```
