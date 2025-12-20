```
apiVersion: v1
baseDomain: devcluster.openshift.com
compute:
  - architecture: amd64
    hyperthreading: Enabled
    name: worker
    platform: {}
    replicas: 0
arbiter: 
  architecture: amd64
  hyperthreading: Enabled
  replicas: 1 
  name: arbiter 
  platform:
    baremetal: {}
controlPlane: 
  architecture: amd64
  hyperthreading: Enabled
  name: master
  platform:
    baremetal: {}
  replicas: 2 
platform:
  baremetal:
# ...
    hosts:
      - name: cluster-master-0
        role: master
# ...
      - name: cluster-master-1
        role: master
        ...
      - name: cluster-arbiter-0
        role: arbiter
# ...
```