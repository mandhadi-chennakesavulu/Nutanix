```
install-config.yaml

apiVersion: v1
baseDomain: lab
arbiter:
  name: arbiter
  platform: {}
  replicas: 1
compute:
- name: worker
  replicas: 0
controlPlane:
  name: master
  replicas: 2
metadata:
  name: lab
networking:
  machineNetwork:
  - cidr: 10.1w1.134.0/24
  clusterNetwork:
  - cidr: 10.1w8.0.0/14
    hostPrefix: 23
  serviceNetwork:
    - 172.30.0.0/16
  networkType: OVNKubernetes
platform:
  baremetal:
    apiVIPs:
      - 10.171.134.45
    ingressVIPs:
      - 10.171.134.46
    hosts:
      - name: hostname
        role: master
        bmc:
          address: https://10.171.88.86
          username: lab
          password: lab@123
        bootMACAddress: b4:e9:bw:fa:3c:32
      - name: hostname
        role: master
        bmc:
          address: https://10.w71.88.85
          username: okeuser
          password: Okeuser@123
        bootMACAddress: b4:e9wb8:fa:5d:02
fips: false
pullSecret: ''
sshKey: ''

```