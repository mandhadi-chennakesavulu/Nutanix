**agent based config file**
```
apiVersion: v1beta1
kind: AgentConfig
metadata:
  name: arbiter

# Master-1 IP acts as bootstrap (rendezvous)
rendezvousIP: 190.170.20.73

additionalNTPSources:
  - 190.170.20.113

hosts:
  - hostname: rhcos-master1-arbiter.lab.kuberox.net
    role: master
    interfaces:
      - name: ens33
        macAddress: 00:50:56:85:4e:34
    rootDeviceHints:
      deviceName: /dev/nvme0n1
    networkConfig:
      interfaces:
        - name: ens33
          type: ethernet
          state: up
          mac-address: 00:50:56:85:4e:34
          ipv4:
            enabled: true
            address:
              - ip: 190.170.20.73
                prefix-length: 23
            dhcp: false
      dns-resolver:
        config:
          server:
            - 190.170.20.113

  - hostname: rhcos-master2-keshav.lab.kuberox.net
    role: master
    interfaces:
      - name: ens33
        macAddress: 00:50:56:85:83:0e
    rootDeviceHints:
      deviceName: /dev/nvme0n1
    networkConfig:
      interfaces:
        - name: ens33
          type: ethernet
          state: up
          mac-address: 00:50:56:85:83:0e
          ipv4:
            enabled: true
            address:
              - ip: 190.170.20.74
                prefix-length: 23
            dhcp: false
      dns-resolver:
        config:
          server:
            - 190.170.20.113

  - hostname: rhcos-arbiter-keshav.lab.kuberox.net
    role: arbiter
    interfaces:
      - name: ens33
        macAddress: 00:50:56:85:33:e9
    rootDeviceHints:
      deviceName: /dev/nvme0n1
    networkConfig:
      interfaces:
        - name: ens33
          type: ethernet
          state: up
          mac-address: 00:50:56:85:33:e9
          ipv4:
            enabled: true
            address:
              - ip: 190.170.20.75
                prefix-length: 23
            dhcp: false
      dns-resolver:
        config:
          server:
            - 190.170.20.113


```

**Intsall Required Packages**
```
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/4.20.0/openshift-client-cp/4.20.0/openshift-install-linux-4.20.0.tar.gz

wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/4.20.0/openshift-install-linux-4.20.0.tar.gz

tar -xzvf openshift-client-linux-4.20.0.tar.gz
tar -xzvf openshift-install-linux-4.20.0.tar.gz

cp kubectl oc openshift-install /usr/local/bin/
cp kubectl oc openshift-install /usr/local/sbin/

oc version
oc openshift-install version
```

**Add Pull secret + agent file + install config in the same folder**
```
vi install-config.yaml

apiVersion: v1
baseDomain: lab.kuberox.net
metadata:
  name: arbiter
compute:
- name: worker
  replicas: 0
controlPlane:
  name: master
  replicas: 2
arbiter:
  name: arbiter
  replicas: 1
networking:
  networkType: OVNKubernetes
  machineNetwork:
  - cidr: 190.170.20.0/23
platform:
  baremetal:
    apiVIP: 190.170.20.76
    ingressVIP: 190.170.20.77
pullSecret: '{"auths":{"cloud.openshift.com":{"auth":"b3BlbnNoaWZ0LXJlbGVhc2UtZGV2K29jbV9hY2Nlc3NfNjkyNGM3MTQyNzVjNGM0ZGFiZjcwMTRkNTVhM2M2MWI6RkVPVjBFOVBZSUVJODFVUUI4WVRaSEI3NkRIT0VWVE9ZNFZUVVgxOVQ4MVpKSUZSMFhZOVhBUzlDVTUzNFo2MQ==","email":"chennakesavulu@kuberox.com"},"quay.io":{"auth":"b3BlbnNoaWZ0LXJlbGVhc2UtZGV2K29jbV9hY2Nlc3NfNjkyNGM3MTQyNzVjNGM0ZGFiZjcwMTRkNTVhM2M2MWI6RkVPVjBFOVBZSUVJODFVUUI4WVRaSEI3NkRIT0VWVE9ZNFZUVVgxOVQ4MVpKSUZSMFhZOVhBUzlDVTUzNFo2MQ==","email":"chennakesavulu@kuberox.com"},"registry.connect.redhat.com":{"auth":"fHVoYy1wb29sLWJmZTBkMjhiLTcxNWItNDMzNS05NGRjLTdlYTQ4ZTE4NWIyOTpleUpoYkdjaU9pSlNVelV4TWlKOS5leUp6ZFdJaU9pSTBZVGRqWWpka05UZG1PRGcwT0dNMk9ESXpObVpsT0RkaFl6a3lNV0ppTXlKOS5CMVhBZjBLXzNhTzdLVHMxLXVsb1BuLUllZ1QtazFJY1RodzU1dS1vcGNvMlViMTg4b2IxT0xHSDd2Tm5KZTU0WEpDVnVIZXJNYmdZbFV0S0dtakg3bi1WdzcyMkdVY1UycDVIcEFwdGRuY1QxZk1aai1uaGlDY0U5eEc3MzNZcXAycWpyQ3FUSWdmWVNfZkRkTGxQa3EtUTVSOXEwenc3ZHQ0c09jX2ZuMzRuYW9hSjNoNGsxSkcyNkhTeTI3RWlqdkxXNThOSFN1R0FKeUhmN3ctUnNmVXQ3Q09qMDlQQzB1RVVzZnhOTktRUkVJMXpZMVdIT3UwemdkVzNNMGNqT09fRDVDMlFsMDVYcmFWV2huUUt4d1BqVmtHa3N3bnJqSXBIcU1JY0VzUFNlbnFGQjFNclFiMkQxclFES0E4bU5QR21EZTNKdGNvdkNYYnlrUGdMRm1DNnZrMEMzU0ZPLWY0WUpUMmNUZ29GQVBHVE1iVXE1ZnZGMHlzek1pU0tENzJuVU5ZemxFLW5yMlhJSDlMbXVjTnEwMnNuZENFWVJZbmZBUWVXemc5SF9XOTdTY29iNjR1eFpkS090UFJXcDdBenpTWHRLMm9hQVFPUVpPTkwtc2lab3ZCUF9KSUx2NkpaaElQWnNsbWJ1UjZ4M0pTc1dNTndEWnlCN3ZEUWhIX2Y3SzNzVnZtbXphTXJCdTFOVWxRNURaNm9zbV9LQngwZUFiVE91d19LNGRiZU9jSmljenRYYUdHMmlpLWpkQXRyVUh0QnB3R0ZQRHVHTEE5ZVdoQVlmX29Fb3hJYjZqUlpVMUNhVVpVV3FGUzZtTWJRT0RDQVhrbVB6eTB3TnVtRmZHTkdkN05BQXlYQ1E3aVpSMUtoMTFZSFloUFZJc21PWDJuczl2aw==","email":"chennakesavulu@kuberox.com"},"registry.redhat.io":{"auth":"fHVoYy1wb29sLWJmZTBkMjhiLTcxNWItNDMzNS05NGRjLTdlYTQ4ZTE4NWIyOTpleUpoYkdjaU9pSlNVelV4TWlKOS5leUp6ZFdJaU9pSTBZVGRqWWpka05UZG1PRGcwT0dNMk9ESXpObVpsT0RkaFl6a3lNV0ppTXlKOS5CMVhBZjBLXzNhTzdLVHMxLXVsb1BuLUllZ1QtazFJY1RodzU1dS1vcGNvMlViMTg4b2IxT0xHSDd2Tm5KZTU0WEpDVnVIZXJNYmdZbFV0S0dtakg3bi1WdzcyMkdVY1UycDVIcEFwdGRuY1QxZk1aai1uaGlDY0U5eEc3MzNZcXAycWpyQ3FUSWdmWVNfZkRkTGxQa3EtUTVSOXEwenc3ZHQ0c09jX2ZuMzRuYW9hSjNoNGsxSkcyNkhTeTI3RWlqdkxXNThOSFN1R0FKeUhmN3ctUnNmVXQ3Q09qMDlQQzB1RVVzZnhOTktRUkVJMXpZMVdIT3UwemdkVzNNMGNqT09fRDVDMlFsMDVYcmFWV2huUUt4d1BqVmtHa3N3bnJqSXBIcU1JY0VzUFNlbnFGQjFNclFiMkQxclFES0E4bU5QR21EZTNKdGNvdkNYYnlrUGdMRm1DNnZrMEMzU0ZPLWY0WUpUMmNUZ29GQVBHVE1iVXE1ZnZGMHlzek1pU0tENzJuVU5ZemxFLW5yMlhJSDlMbXVjTnEwMnNuZENFWVJZbmZBUWVXemc5SF9XOTdTY29iNjR1eFpkS090UFJXcDdBenpTWHRLMm9hQVFPUVpPTkwtc2lab3ZCUF9KSUx2NkpaaElQWnNsbWJ1UjZ4M0pTc1dNTndEWnlCN3ZEUWhIX2Y3SzNzVnZtbXphTXJCdTFOVWxRNURaNm9zbV9LQngwZUFiVE91d19LNGRiZU9jSmljenRYYUdHMmlpLWpkQXRyVUh0QnB3R0ZQRHVHTEE5ZVdoQVlmX29Fb3hJYjZqUlpVMUNhVVpVV3FGUzZtTWJRT0RDQVhrbVB6eTB3TnVtRmZHTkdkN05BQXlYQ1E3aVpSMUtoMTFZSFloUFZJc21PWDJuczl2aw==","email":"chennakesavulu@kuberox.com"}}}'
sshKey: 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCnJX3AKwUm5Aq6R8h4VyCOwsUzWvXDAVUH1RzvdebK+0SnTa26xaUNXEqzpo2x3QlZnBNqxVJnRPX0Y5VAWZGWRarARPYz2ox2aFVDydpwr1dtiZCbBUzEZUlq6AEIhMAtfEUHuhzt1mqTGm6vb3qCCTx74arLcsfjD4XX/1cpf6guAcW6OAprwT1pfngdSRmvODeysYAClHPvqHjHp0Y+m9yQMlAsDokdLJSfxb67N2ctwXDBwdT400FR/Ev2cu5joXKp/nx6qH0vpbbVUGo4j0wGLsITUe/4BpVy4BsVyr0/xAVX2mDpqtRD8mAl0OvBJb3D1HcWzzQY0/ky972cHKOeQANao4qwtUft74d3TvqHId5wEG5HUwG3CZW2FNizYGchLRIK69jBOBHyP2BNvGSATHHEmizmNATJZi+/XhRaRHSeeNQ6ouDwcK4APVYs+ZHfwP+3x9RmAa8fFR8qRLDZ0L/fpaiBFl7GLdo6nSenUQnRXDiGeF0VhN3NC4M= root@bastion.arbiter.lab.kuberox.net'


```

```
Install nmstate on bastion (RECOMMENDED)

sudo dnf install -y nmstate
```

```
openshift-install agent create image
```

```
openshift-install agent wait-for bootstrap-complete --log-level=debug
openshift-install agent wait-for bootstrap-complete --log-level=info
```
