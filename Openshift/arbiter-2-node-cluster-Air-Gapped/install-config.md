```
install-config.yaml

apiVersion: v1
baseDomain: india.tcs.com
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
  name: dynaport
networking:
  machineNetwork:
  - cidr: 10.171.134.0/24
  clusterNetwork:
  - cidr: 10.128.0.0/14
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
      - name: INHYDSP4PNCMS43
        role: master
        bmc:
          address: https://10.171.88.86
          username: okeuser
          password: Okeuser@123
        bootMACAddress: b4:e9:b8:fa:3c:37
      - name: INHYDSP4PNCMS42
        role: master
        bmc:
          address: https://10.171.88.85
          username: okeuser
          password: Okeuser@123
        bootMACAddress: b4:e9:b8:fa:5d:0f
fips: false
pullSecret: ''
sshKey: ''

```