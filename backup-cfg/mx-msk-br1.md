```
## Last commit: 2024-09-25 16:44:54 UTC by moro
version 14.1R4.8;
system {
    host-name mx-msk-br1;
    root-authentication {
        encrypted-password "$1$eVd09Ln.$UP12TBvO9cdvtrjLUj7no0"; ## SECRET-DATA
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
                encrypted-password "$1$vJOuY6gJ$pRy6ENQQsP6qcitjXF5NS1"; ## SECRET-DATA
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
        flexible-vlan-tagging;
        mtu 2000;
        encapsulation flexible-ethernet-services;
        unit 100 {
            vlan-id 100;
            family inet {
                address 198.18.255.0/31;
            }
        }
    }
    ge-0/0/1 {
        description "## -B- | to mx-msk-br2 ge-0/0/0";
        mtu 2000;
        unit 0 {
            family inet {
                address 10.12.99.8/31;
            }
        }
    }
    ge-0/0/2 {
        description "## -B- | to mx-msk-br3 ge-0/0/0";
        mtu 2000;
        unit 0 {
            family inet {
                address 10.12.99.6/31;
            }
        }
    }
    ge-0/0/3 {
        description "## -B- | to mx-msk-br4 ge-0/0/0";
        mtu 2000;
        unit 0 {
            family inet {
                address 10.12.99.0/31;
            }
        }
    }
    em0 {
        description "## -M- | MGMT";
        unit 0 {
            family inet {
                address 10.253.6.12/16;
            }
        }
    }
    lo0 {
        description "## -M- | Loop internal";
        unit 0 {
            family inet {
                address 10.255.99.1/32;
            }
        }
    }
}
routing-options {
    aggregate {
        defaults {
            preference 200;
            discard;
        }
        route 2.2.2.0/24;
    }
    router-id 10.255.99.1;
    autonomous-system 2222;
}
protocols {
    bgp {
        group iBGP {
            type internal;
            local-address 10.255.99.1;
            family inet {
                unicast;
            }
            export POL-BGP-IBGP-OUT;
            vpn-apply-export;
            neighbor 10.255.99.2;
            neighbor 10.255.99.3;
            neighbor 10.255.99.4;
        }
        group eBGP {
            type external;
            import [ POL-BGP-SANITY-CHECK POL-BGP-EBGP-IN ];
            family inet {
                unicast;
            }
            export POL-BGP-EBGP-OUT;
            neighbor 198.18.255.1 {
                description "## MSK ISP ##";
                local-address 198.18.255.0;
                peer-as 1111;
            }
        }
    }
    ospf {
        no-rfc-1583;
        area 0.0.0.0 {
            interface lo0.0 {
                passive;
            }
            interface ge-0/0/1.0;
            interface ge-0/0/2.0 {
                metric 100;
            }
            interface ge-0/0/3.0 {
                metric 100;
            }
        }
    }
    lldp {
        interface all;
    }
}
policy-options {
    prefix-list PL-ISP {
        1.1.1.0/24;
    }
    policy-statement POL-BGP-EBGP-IN {
        term T-ISP {
            from {
                prefix-list PL-ISP;
            }
            then accept;
        }
        then reject;
    }
    policy-statement POL-BGP-EBGP-OUT {
        term T-AGGREGATE {
            from protocol aggregate;
            then accept;
        }
        term T-ANY {
            then accept;
        }
    }
    policy-statement POL-BGP-IBGP-OUT {
        term T-DIRECT {
            from protocol direct;
            then {
                next-hop self;
                accept;
            }
        }
        term T-BGP {
            from protocol bgp;
            then accept;
        }
        then reject;
    }
    policy-statement POL-BGP-SANITY-CHECK {
        term long-prefixes-reject {
            from {
                route-filter 0.0.0.0/0 prefix-length-range /25-/32 reject;
            }
        }
        then next policy;
    }
}
```
