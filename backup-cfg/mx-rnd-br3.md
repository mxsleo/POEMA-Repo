```
## Last commit: 2024-09-25 16:36:12 UTC by moro
version 14.1R4.8;
system {
    host-name mx-rnd-br3;
    root-authentication {
        encrypted-password "$1$UGfywuqp$/Zas3p8zo/BaddtV/MwAu/"; ## SECRET-DATA
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
                encrypted-password "$1$lVlCdVCJ$C8yZlWRRmoyi2NaD/7ud/1"; ## SECRET-DATA
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
        description "## -B- | to mx-rnd-br1 ge-0/0/2";
        mtu 2000;
        unit 0 {
            family inet {
                address 10.12.61.7/31;
            }
        }
    }
    ge-0/0/1 {
        description "## -B- | to mx-rnd-br4 ge-0/0/2";
        mtu 2000;
        unit 0 {
            family inet {
                address 10.12.61.2/31;
            }
        }
    }
    em0 {
        description "## -M- | MGMT";
        unit 0 {
            family inet {
                address 10.253.6.4/16;
            }
        }
    }
    lo0 {
        description "## -M- | Loop internal";
        unit 0 {
            family inet {
                address 10.255.61.3/32;
            }
        }
    }
}
routing-options {
    router-id 10.255.61.3;
    autonomous-system 1111;
}
protocols {
    bgp {
        group iBGP {
            type internal;
            local-address 10.255.61.3;
            family inet {
                unicast;
            }
            export POL-BGP-IBGP-OUT;
            vpn-apply-export;
            neighbor 10.255.61.1;
            neighbor 10.255.61.2;
            neighbor 10.255.61.4;
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
