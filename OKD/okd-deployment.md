```
export OKD_VERSION=4.21.0-okd-scos.ec.9
export ARCH=x86_64

```
```
curl -L https://github.com/okd-project/okd/releases/download/4.21.0-okd-scos.ec.9/openshift-client-linux-4.21.0-okd-scos.ec.9.tar.gz -o oc.tar.gz
```

```
curl -L https://github.com/okd-project/okd/releases/download/4.21.0-okd-scos.ec.9/openshift-install-linux-4.21.0-okd-scos.ec.9.tar.gz -o openshift-install-linux.tar.gz
```

```
tar -xzvf oc.tar.gz
tar -xzvf openshift-install-linux.tar.gz
```
```
sudo cp oc kubectl openshift-install /usr/local/bin/
sudo chmod +x /usr/local/bin/oc /usr/local/bin/kubectl /usr/local/bin/openshift-install
```
**Retrieve FCOS ISO URL**
```
export ISO_URL=$(openshift-install coreos print-stream-json \
  | jq -r '.architectures."'$ARCH'".artifacts.metal.formats.iso.disk.location')


echo "$ISO_URL"

output: 
https://rhcos.mirror.openshift.com/art/storage/prod/streams/c10s/builds/10.0.20251103-0/x86_64/scos-10.0.20251103-0-live-iso.x86_64.iso

```
**Download FCOS Live ISO**
```
curl -L $ISO_URL -o fcos-live.iso

Verify: 
ls -lh fcos-live.iso
[root@rke-master snp-okd-cluster]# ls -lh fcos-live.iso
-rw-r--r--. 1 root root 876M Dec 23 09:12 fcos-live.iso
```

**Deployment**
```
mkdir sno
cd sno
vi install-config.yaml
```
```
cat install-config.yaml

apiVersion: v1
baseDomain: lab.kuberox.net
metadata:
  name: sno-okd
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
  platform: {}
  replicas: 1
networking:
  networkType: OVNKubernetes
  clusterNetwork:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  machineNetwork:
  - cidr: 190.170.20.0/23
  serviceNetwork:
  - 172.30.0.0/16
platform:
  none: {}
pullSecret: '{"auths":{"cloud.openshift.com":{"auth":"b3BlbnNoaWZ0LXJlbGVhc2UtZGV2K29jbV9hY2Nlc3NfNjkyNGM3MTQyNzVjNGM0ZGFiZjcwMTRkNTVhM2M2MWI6RkVPVjBFOVBZSUVJODFVUUI4WVRaSEI3NkRIT0VWVE9ZNFZUVVgxOVQ4MVpKSUZSMFhZOVhBUzlDVTUzNFo2MQ==","email":"chennakesavulu@kuberox.com"},"quay.io":{"auth":"b3BlbnNoaWZ0LXJlbGVhc2UtZGV2K29jbV9hY2Nlc3NfNjkyNGM3MTQyNzVjNGM0ZGFiZjcwMTRkNTVhM2M2MWI6RkVPVjBFOVBZSUVJODFVUUI4WVRaSEI3NkRIT0VWVE9ZNFZUVVgxOVQ4MVpKSUZSMFhZOVhBUzlDVTUzNFo2MQ==","email":"chennakesavulu@kuberox.com"},"registry.connect.redhat.com":{"auth":"fHVoYy1wb29sLWJmZTBkMjhiLTcxNWItNDMzNS05NGRjLTdlYTQ4ZTE4NWIyOTpleUpoYkdjaU9pSlNVelV4TWlKOS5leUp6ZFdJaU9pSTBZVGRqWWpka05UZG1PRGcwT0dNMk9ESXpObVpsT0RkaFl6a3lNV0ppTXlKOS5CMVhBZjBLXzNhTzdLVHMxLXVsb1BuLUllZ1QtazFJY1RodzU1dS1vcGNvMlViMTg4b2IxT0xHSDd2Tm5KZTU0WEpDVnVIZXJNYmdZbFV0S0dtakg3bi1WdzcyMkdVY1UycDVIcEFwdGRuY1QxZk1aai1uaGlDY0U5eEc3MzNZcXAycWpyQ3FUSWdmWVNfZkRkTGxQa3EtUTVSOXEwenc3ZHQ0c09jX2ZuMzRuYW9hSjNoNGsxSkcyNkhTeTI3RWlqdkxXNThOSFN1R0FKeUhmN3ctUnNmVXQ3Q09qMDlQQzB1RVVzZnhOTktRUkVJMXpZMVdIT3UwemdkVzNNMGNqT09fRDVDMlFsMDVYcmFWV2huUUt4d1BqVmtHa3N3bnJqSXBIcU1JY0VzUFNlbnFGQjFNclFiMkQxclFES0E4bU5QR21EZTNKdGNvdkNYYnlrUGdMRm1DNnZrMEMzU0ZPLWY0WUpUMmNUZ29GQVBHVE1iVXE1ZnZGMHlzek1pU0tENzJuVU5ZemxFLW5yMlhJSDlMbXVjTnEwMnNuZENFWVJZbmZBUWVXemc5SF9XOTdTY29iNjR1eFpkS090UFJXcDdBenpTWHRLMm9hQVFPUVpPTkwtc2lab3ZCUF9KSUx2NkpaaElQWnNsbWJ1UjZ4M0pTc1dNTndEWnlCN3ZEUWhIX2Y3SzNzVnZtbXphTXJCdTFOVWxRNURaNm9zbV9LQngwZUFiVE91d19LNGRiZU9jSmljenRYYUdHMmlpLWpkQXRyVUh0QnB3R0ZQRHVHTEE5ZVdoQVlmX29Fb3hJYjZqUlpVMUNhVVpVV3FGUzZtTWJRT0RDQVhrbVB6eTB3TnVtRmZHTkdkN05BQXlYQ1E3aVpSMUtoMTFZSFloUFZJc21PWDJuczl2aw==","email":"chennakesavulu@kuberox.com"},"registry.redhat.io":{"auth":"fHVoYy1wb29sLWJmZTBkMjhiLTcxNWItNDMzNS05NGRjLTdlYTQ4ZTE4NWIyOTpleUpoYkdjaU9pSlNVelV4TWlKOS5leUp6ZFdJaU9pSTBZVGRqWWpka05UZG1PRGcwT0dNMk9ESXpObVpsT0RkaFl6a3lNV0ppTXlKOS5CMVhBZjBLXzNhTzdLVHMxLXVsb1BuLUllZ1QtazFJY1RodzU1dS1vcGNvMlViMTg4b2IxT0xHSDd2Tm5KZTU0WEpDVnVIZXJNYmdZbFV0S0dtakg3bi1WdzcyMkdVY1UycDVIcEFwdGRuY1QxZk1aai1uaGlDY0U5eEc3MzNZcXAycWpyQ3FUSWdmWVNfZkRkTGxQa3EtUTVSOXEwenc3ZHQ0c09jX2ZuMzRuYW9hSjNoNGsxSkcyNkhTeTI3RWlqdkxXNThOSFN1R0FKeUhmN3ctUnNmVXQ3Q09qMDlQQzB1RVVzZnhOTktRUkVJMXpZMVdIT3UwemdkVzNNMGNqT09fRDVDMlFsMDVYcmFWV2huUUt4d1BqVmtHa3N3bnJqSXBIcU1JY0VzUFNlbnFGQjFNclFiMkQxclFES0E4bU5QR21EZTNKdGNvdkNYYnlrUGdMRm1DNnZrMEMzU0ZPLWY0WUpUMmNUZ29GQVBHVE1iVXE1ZnZGMHlzek1pU0tENzJuVU5ZemxFLW5yMlhJSDlMbXVjTnEwMnNuZENFWVJZbmZBUWVXemc5SF9XOTdTY29iNjR1eFpkS090UFJXcDdBenpTWHRLMm9hQVFPUVpPTkwtc2lab3ZCUF9KSUx2NkpaaElQWnNsbWJ1UjZ4M0pTc1dNTndEWnlCN3ZEUWhIX2Y3SzNzVnZtbXphTXJCdTFOVWxRNURaNm9zbV9LQngwZUFiVE91d19LNGRiZU9jSmljenRYYUdHMmlpLWpkQXRyVUh0QnB3R0ZQRHVHTEE5ZVdoQVlmX29Fb3hJYjZqUlpVMUNhVVpVV3FGUzZtTWJRT0RDQVhrbVB6eTB3TnVtRmZHTkdkN05BQXlYQ1E3aVpSMUtoMTFZSFloUFZJc21PWDJuczl2aw==","email":"chennakesavulu@kuberox.com"}}}'
sshKey: 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC2RCyQWHzeiFWGyUhE1oiVGgn6ZSKNgw/eKDogXir3cvEmqrxhG7yfVRol8p1RGPEHHvchRPRLJ0j4jPvfovhwBItoJrkESETnuKDcAJB8mIJw2xM10wp+qZuC+wI9N39G9AfRGvCtP25iekIUY7khnfYnb/8gGxZzZQFRyWeoqpN1godezf9gN2zYTW604jE1WBlZfzTAiFEW2VMf7gyOV/7GuGwa7afTRog2OcWwpHzXOEt9+ecR43n5363ryTJj9fBf8tAeCb6KX/XKXAgYU9u8iyHfRqa7gHyJTquxjCeWSL/DtfYpkeR3+uBpaAGl6pGfpaVjFkNzIsEUhvzDiDwZZ6UUfinl9q0/CEqHP06/H3jBcIQZ/DwakYBrMkkMH8KAxEOqohZhmIMlJ0cPIkawnpXoqEh+HhlMSTrbQgv3E8IINwNYD83wE2HGFvZSy9N4KqgNj50t3bBiAWpHz+7gIwuBHrRpaomsSe69FwfUtR9GokQcPrYNzG/79Ek= root@bastion.sno-okd.lab.kuberox.net'

```
**agent-config**
```
apiVersion: v1alpha1
kind: AgentConfig

rendezvousIP: 190.170.20.174

hosts:
  - hostname: master.sno-okd.lab.kuberox.net
    role: master

    interfaces:
      - name: eno1
        macAddress: e4:43:4b:4b:80:d0
      - name: eno2
        macAddress: e4:43:4b:4b:80:d1

    networkConfig:
      interfaces:
        # Physical NICs
        - name: eno1
          type: ethernet
          state: up

        - name: eno2
          type: ethernet
          state: up

        # Bond interface
        - name: bond0
          type: bond
          state: up
          mtu: 1500
          ipv4:
            enabled: false
          ipv6:
            enabled: false
          link-aggregation:
            mode: 802.3ad
            options:
              miimon: "100"
            port:
              - eno1
              - eno2

        # VLAN on bond
        - name: bond0.20
          type: vlan
          state: up
          vlan:
            base-iface: bond0
            id: 20
          ipv4:
            enabled: true
            dhcp: false
            address:
              - ip: 190.170.20.174
                prefix-length: 23
          ipv6:
            enabled: false

      dns-resolver:
        config:
          server:
            - 190.170.20.107

      routes:
        config:
          - destination: 0.0.0.0/0
            next-hop-address: 190.170.20.1
            next-hop-interface: bond0.20

```

**add /ete/hosts**
```
190.170.20.172  bastion.sno-okd.lab.kuberox.net
190.170.20.174  master.sno-okd.lab.kuberox.net
190.170.20.174  api.sno-okd.lab.kuberox.net
190.170.20.174  api-int.sno-okd.lab.kuberox.net
190.170.20.173  *.apps.sno-okd.lab.kuberox.net

```
```
openshift-install agent create image
```
**Validated from bastion**
```
openshift-install agent wait-for install-complete
openshift-install agent wait-for bootstrap-complete

```
![alt text](image.png)



