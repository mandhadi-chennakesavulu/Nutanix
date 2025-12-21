**Install config file**
```
apiVersion: v1
baseDomain: lab.kuberox.net
metadata:
  name: arbiter
compute:
  - architecture: amd64
    hyperthreading: Enabled
    name: worker
    platform: {}
    replicas: 0
controlPlane:
  architecture: amd64
  hyperthreading: Enabled
  name: master
  platform:
    baremetal: {}
  replicas: 2
arbiter:
  architecture: amd64
  hyperthreading: Enabled
  replicas: 1
  name: arbiter
  platform:
    baremetal: {}
networking:
  networkType: OVNKubernetes
  machineNetwork:
  - cidr: 190.170.40.0/22
  clusterNetwork:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  serviceNetwork:
    - 172.30.0.0/16
platform:
  baremetal:
    hosts:
      - name: cluster-master-0
        role: master

      - name: cluster-master-1
        role: master

      - name: cluster-arbiter-0
        role: arbiter
pullSecret: '{
  "auths": {
    "quay.arbiter.lab.kuberox.net:8443": {
      "auth": "aW5pdDpLdWJlUG9kc0AzMjE="
    }
  }
}'
sshKey: 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDUNiUqH5lPkcygYwoflz/9LAcOIzz6679DGw1EfinnAEEwwntMa1ey5021gR8/M9Cd0QoCcsMqjwp+2VnsOVOVSygrDc0zdp9ZBvsD60Y/qYw5YHi6DXKGWbge6pDsaeEjW6p4eKhDZ6nshAp3fvNuqPoawEluatOV8dBTIKNR4xKk4U5hx0trCTtqXbRrQQ/44UTBuOHKAZgGbLCFNMoUYp79YDc7zbu/M7DfrTA9IYfTvU1LHox+WUQ8/UrdmUemx8GQeAJ3iHlCgn9kmGyUv5DD+wGU3K+vN94AbEpOEzLFetZbRKW+RXBkdrYdAF8UBjvyKheND3bJCjz2UDIgiEqP8Mqbfx0pFeC/SPi7atJ9xAoHuXdRkjLa0qMfUoNUJizZJFcLL2/TcKhcG82Twi08UHW8f1m3adrSHfHn97zVp3bH8RZmUDhDjF0Fgtu+byEyBOl9lfVngdS4NNDmwRHSAaLhq13J4b2mXFInE9edDbQhlYO7W/FTTj3hRnk= root@quay.arbiter.lab.kuberox.net'
# ✔ Correct mirror configuration
imageContentSources:
- mirrors:
  - quay.lab.kuberox.net:8443/mirror-reg/openshift/release
  source: quay.io/openshift-release-dev/ocp-v4.0-art-dev
- mirrors:
  - quay.lab.kuberox.net:8443/mirror-reg/openshift/release-images
  source: quay.io/openshift-release-dev/ocp-release
  
- mirrors:
  - quay.arbiter.lab.kuberox.net:8443/openshift/release-images
  source: quay.io/openshift-release-dev/ocp-release
- mirrors:
  - quay.arbiter.lab.kuberox.net:8443/openshift/release
  source: quay.io/openshift-release-dev/ocp-v4.0-art-dev
	
# ✔ Explicit release image for DISCONNECTED install
#releaseImage: quay.lab.kuberox.net:8443/openshift/release-images:4.20.4-x86_64
additionalTrustBundle: |
  -----BEGIN CERTIFICATE-----
  MIID+zCCAuOgAwIBAgIUdn1YQYJSPVD8OtMMxE5HH8t0qVUwDQYJKoZIhvcNAQEL
  BQAwdjELMAkGA1UEBhMCVVMxCzAJBgNVBAgMAlZBMREwDwYDVQQHDAhOZXcgWW9y
  azENMAsGA1UECgwEUXVheTERMA8GA1UECwwIRGl2aXNpb24xJTAjBgNVBAMMHHF1
  YXkuYXJiaXRlci5sYWIua3ViZXJveC5uZXQwHhcNMjUxMjIxMTIyNjEzWhcNMjgx
  MDEwMTIyNjEzWjB2MQswCQYDVQQGEwJVUzELMAkGA1UECAwCVkExETAPBgNVBAcM
  CE5ldyBZb3JrMQ0wCwYDVQQKDARRdWF5MREwDwYDVQQLDAhEaXZpc2lvbjElMCMG
  A1UEAwwccXVheS5hcmJpdGVyLmxhYi5rdWJlcm94Lm5ldDCCASIwDQYJKoZIhvcN
  AQEBBQADggEPADCCAQoCggEBANR4ql3IE1lvzmf30z2Tuk7haL5PU1diqsXnoqxS
  yUNo1Cwf+OPrZmMfLg/uQFvL7JU4qCBvwOOrL350EXyjZdivJv2CL8pF6AkFddOo
  ToU7OSZuig3I3mnSjOjXOEEmXo1VaLP7eLlF6SjNTeHBJDbkqGEKV1DBSVUv2pco
  Kf3onJnEKNxGIWTBwbR9zrP74o20+cwSlqZOqjIlhl4P8RJ+g9KsTf7+MZOhgWba
  i8bZE0ZVPiqibu/A4n9Y5sgPEi6uLQjL5rc9eRJu2oZ5AYwVf47/iqCZ/N3ZFoTG
  qlj3v0QTQPdsJ642SPyK9txbHPSZnbvbxPV3QW7HrsRJnhsCAwEAAaOBgDB+MAsG
  A1UdDwQEAwIC5DATBgNVHSUEDDAKBggrBgEFBQcDATAnBgNVHREEIDAeghxxdWF5
  LmFyYml0ZXIubGFiLmt1YmVyb3gubmV0MBIGA1UdEwEB/wQIMAYBAf8CAQEwHQYD
  VR0OBBYEFOiMrnOwXQ2rlrv+nDwgTUpZu0NqMA0GCSqGSIb3DQEBCwUAA4IBAQAt
  4ZsqRj2DIrywhftx0OFvX8sFO99E7tLk0JUm4q8N4/4z3GPLCVfrr/Mb6Ai8FclY
  zqGNuDhc0Hnf27Vc47ko7IAbYWSnDMfhDC3OY1KcjfsIVAb4LRdBM/0hpgcsgFde
  0NM18Im82scB8og9Z5bRgdWyCavgqr7ExJlg83ZlGnspeIc7As1vq9pYMIS1mC3P
  a2QQ8i9TjLchy4wDp9vLRlotJvxcHkIHzwKoUDoIUIKEi3U8qbU1KG1mEtzPn/sN
  +Z6jBBa4kqYzrVTCN0Tmv4na9k+SH377cvytwLMN0vGRaQdU//doNOt+rUYM/I+H
  vhbfK3azpkhmzFOkw2Vz
  -----END CERTIFICATE-----

```

**Agent config file**
```

```

**HaProxy file**
```
cat /etc/haproxy/haproxy.cfg
global
  log         127.0.0.1 local2
  pidfile     /var/run/haproxy.pid
  maxconn     4000
  daemon
defaults
  mode                    http
  log                     global
  option                  dontlognull
  option http-server-close
  option                  redispatch
  retries                 3
  timeout http-request    10s
  timeout queue           1m
  timeout connect         10s
  timeout client          1m
  timeout server          1m
  timeout http-keep-alive 10s
  timeout check           10s
  maxconn                 3000
listen api-server-6443
  bind *:6443
  mode tcp
  option  httpchk GET /readyz HTTP/1.0
  option  log-health-checks
  balance roundrobin
  server master1 master1.arbiter.lab.kuberox.net:6443 weight 1 verify none check check-ssl inter 10s fall 2 rise 3
  server master2 master2.arbiter.lab.kuberox.net:6443 weight 1 verify none check check-ssl inter 10s fall 2 rise 3
  server arbiter arbiter.arbiter.lab.kuberox.net:6443 weight 1 verify none check check-ssl inter 10s fall 2 rise 3
listen machine-config-server-22623
  bind *:22623
  mode tcp
  server master1 master1.arbiter.lab.kuberox.net:22623 check inter 1s
  server master2 master2.arbiter.lab.kuberox.net:22623 check inter 1s
  server arbiter arbiter.arbiter.lab.kuberox.net:22623 check inter 1s
listen ingress-router-443
  bind *:443
  mode tcp
  balance source
  server master1 master1.arbiter.lab.kuberox.net:443 check inter 1s
  server master2 master2.arbiter.lab.kuberox.net:443 check inter 1s
  server arbiter arbiter.arbiter.lab.kuberox.net:443 check inter 1s
listen ingress-router-80
  bind *:80
  mode tcp
  balance source
  server master1 master1.arbiter.lab.kuberox.net:80 check inter 1s
  server master2 master2.arbiter.lab.kuberox.net:80 check inter 1s
  server arbiter arbiter.arbiter.lab.kuberox.net:80 check inter 1s
#stats
listen stats
    bind *:9000
    stats uri /stats
    stats refresh 1000ms
```

**DNS - Forward**
```
cat /var/named/dns.db
$TTL 1W
@       IN      SOA     ns1.arbiter.lab.kuberox.net.   root.arbiter.lab.kuberox.net (
                        202304      ; serial
                        3H              ; refresh (3 hours)
                        30M             ; retry (30 minutes)
                        2W              ; expiry (2 weeks)
                        1W )            ; minimum (1 week)
        IN      NS      ns1.arbiter.lab.kuberox.net.
;
;
ns1.arbiter.lab.kuberox.net.     IN      A       190.170.40.168
;
; The api points to the IP of your load balancer
api             IN      A       190.170.40.168
api-int         IN      A       190.170.40.168
;
; The wildcard also points to the load balancer
*.apps          IN      A       190.170.40.168
;
; Create entry for the bootstrap-arbiter.lab-08 host
quay         IN      A       190.170.40.168
;
; Create entries for the master hosts
master1         IN      A       190.170.40.80
master2         IN      A       190.170.40.81
;  Create entries for the worker host
arbiter       IN      A       190.170.40.82

;EOF
```
**DNS - Revrese**
```
cat /var/named/dns-rev.db
$ORIGIN 40.170.190.in-addr.arpa.
$TTL 1W
@       IN      SOA     ns1.arbiter.lab.kuberox.net.       root.arbiter.lab.kuberox.net (
                        202304  ; serial
                        3H      ; refresh (3 hours)
                        30M     ; retry (30 minutes)
                        2W      ; expiry (2 weeks)
                        1W )    ; minimun (1 week)
        IN      NS      ns1.arbiter.lab.kuberox.net.
;
168     IN      PTR     api.arbiter.lab.kuberox.net.
168     IN      PTR     api-int.arbiter.lab.kuberox.net.
;
;
168     IN      PTR     quay.arbiter.lab.kuberox.net.
;
80     IN      PTR     master1.arbiter.lab.kuberox.net.
81     IN      PTR     master2.arbiter.lab.kuberox.net.
82     IN      PTR     arbiter.arbiter.lab.kuberox.net.
;
;EOF

```
**Edit DNS config file**
```
cat /etc/named.conf
//
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//

options {
        listen-on port 53 { any; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        secroots-file   "/var/named/data/named.secroots";
        recursing-file  "/var/named/data/named.recursing";
        allow-query     { any; };

        /*
         - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
         - If you are building a RECURSIVE (caching) DNS server, you need to enable
           recursion.
         - If your recursive DNS server has a public IP address, you MUST enable access
           control to limit queries to your legitimate users. Failing to do so will
           cause your server to become part of large scale DNS amplification
           attacks. Implementing BCP38 within your network would greatly
           reduce such attack surface
        */
        recursion yes;

        dnssec-validation yes;

        managed-keys-directory "/var/named/dynamic";
        geoip-directory "/usr/share/GeoIP";

        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";

        /* https://fedoraproject.org/wiki/Changes/CryptoPolicy */
        include "/etc/crypto-policies/back-ends/bind.config";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

zone "." IN {
        type hint;
        file "named.ca";
};
zone "arbiter.lab.kuberox.net" IN {
        type master;
        file "dns.db";
};
#
zone "40.170.190.in-addr.arpa." IN {
        type master;
        file "dns-rev.db";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";

```
