```
## Last commit: 2024-09-25 16:44:19 UTC by moro
version 14.1R4.8;
system {
    host-name mx-msk-br4;
    root-authentication {
        encrypted-password "$1$YiSpcu/D$EPOTeMRPZBncfEOItmQ9g0"; ## SECRET-DATA
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
                encrypted-password "$1$.ROA.l9y$.zgOlLJmpxivBvJ0fMd9f0"; ## SECRET-DATA
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
        description "## -B- | to mx-msk-br1 ge-0/0/3";
        mtu 2000;
        unit 0 {
            family inet {
                address 10.12.99.1/31;
            }
        }
    }
    ge-0/0/1 {
        description "## -B- | to mx-msk-br2 ge-0/0/1";
        mtu 2000;
        unit 0 {
            family inet {
                address 10.12.99.5/31;
            }
        }
    }
    ge-0/0/2 {
        description "## -B- | to mx-msk-br3 ge-0/0/1";
        mtu 2000;
        unit 0 {
            family inet {
                address 10.12.99.3/31;
            }
        }
    }
    ge-0/0/3 {
        description "## -C- | moscow-srv1";
        unit 0 {
            family inet {
                address 2.2.2.0/31;
            }
        }
    }
    em0 {
        description "## -M- | MGMT";
        unit 0 {
            family inet {
                address 10.253.6.15/16;
            }
        }
    }
    lo0 {
        description "## -M- | Loop internal";
        unit 0 {
            family inet {
                address 10.255.99.4/32;
            }
        }
    }
}
routing-options {
    router-id 10.255.99.4;
    autonomous-system 2222;
}
protocols {
    bgp {
        group iBGP {
            type internal;
            local-address 10.255.99.4;
            family inet {
                unicast;
            }
            export POL-BGP-IBGP-OUT;
            vpn-apply-export;
            neighbor 10.255.99.1;
            neighbor 10.255.99.2;
            neighbor 10.255.99.3;
        }
    }
    ospf {
        no-rfc-1583;
        area 0.0.0.0 {
            interface lo0.0 {
                passive;
            }
            interface ge-0/0/1.0 {
                metric 10;
            }
            interface ge-0/0/0.0 {
                metric 100;
            }
            interface ge-0/0/2.0 {
                metric 100;
            }
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
