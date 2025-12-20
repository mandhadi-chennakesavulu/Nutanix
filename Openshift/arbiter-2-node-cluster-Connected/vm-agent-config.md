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
                prefix-length: 24
            dhcp: false
      dns-resolver:
        config:
          server:
            - 190.170.20.113

  - hostname: rhcos-master2-keshav.lab.kuberox.net
    role: master
    interfaces:
      - name: eno8303
        macAddress: 00:50:56:85:83:0e
    rootDeviceHints:
      deviceName: /dev/nvme0n1
    networkConfig:
      interfaces:
        - name: eno8303
          type: ethernet
          state: up
          mac-address: 00:50:56:85:83:0e
          ipv4:
            enabled: true
            address:
              - ip: 190.170.20.74
                prefix-length: 24
            dhcp: false
      dns-resolver:
        config:
          server:
            - 190.170.20.113

  - hostname: rhcos-arbiter-keshav.lab.kuberox.net
    role: arbiter
    interfaces:
      - name: ens18
        macAddress: 00:50:56:85:33:e9
    rootDeviceHints:
      deviceName: /dev/nvme0n1
    networkConfig:
      interfaces:
        - name: ens18
          type: ethernet
          state: up
          mac-address: 00:50:56:85:33:e9
          ipv4:
            enabled: true
            address:
              - ip: 190.170.20.75
                prefix-length: 24
            dhcp: false
      dns-resolver:
        config:
          server:
            - 190.170.20.113
