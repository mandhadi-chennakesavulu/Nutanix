```
cat agent-config.yaml

apiVersion: v1beta1
kind: AgentConfig
metadata:
  name: dynaport
rendezvousIP: 10.171.134.42
additionalNTPSources:
  - 172.25.99.100
  - 10.25.4.100
hosts:
  - hostname: INHYDSP4PNCMS42.dynaport.india.tcs.com
    role: master
    interfaces:
      - name: eno8303
        macAddress: b4:e9:b8:fa:5d:15
    rootDeviceHints:
      wwn: "0x6c0470e0e59eea00307a774b47728c73"
    networkConfig:
      interfaces:
        - name: eno8303
          type: ethernet
          state: up
          mac-address: b4:e9:b8:fa:5d:15
          ipv4:
            enabled: true
            address:
              - ip: 10.171.134.42
                prefix-length: 24
            dhcp: false
      dns-resolver:
        config:
          server:
            - 10.171.134.41
            - 172.25.99.100
      routes:
        config:
          - destination: 0.0.0.0/0
            next-hop-address: 10.171.134.15
            next-hop-interface: eno8303
            table-id: 254
  - hostname: INHYDSP4PNCMS43.dynaport.india.tcs.com
    role: master
    interfaces:
      - name: eno8303
        macAddress: b4:e9:b8:fa:3c:3d
    rootDeviceHints:
      wwn: "0x6c0470e0e59eaf00307a7b5982185c5c"
    networkConfig:
      interfaces:
        - name: eno8303
          type: ethernet
          state: up
          mac-address: b4:e9:b8:fa:3c:3d
          ipv4:
            enabled: true
            address:
              - ip: 10.171.134.43
                prefix-length: 24
            dhcp: false
      dns-resolver:
        config:
          server:
            - 10.171.134.41
            - 172.25.99.100
      routes:
        config:
          - destination: 0.0.0.0/0
            next-hop-address: 10.171.134.15
            next-hop-interface: eno8303
            table-id: 254
  - hostname: INHYDSP4PNCMS44.dynaport.india.tcs.com
    role: arbiter
    interfaces:
      - name: ens18
        macAddress: bc:24:11:f7:5f:63
    rootDeviceHints:
      deviceName: /dev/sda
    networkConfig:
      interfaces:
        - name: ens18
          type: ethernet
          state: up
          mac-address: bc:24:11:f7:5f:63
          ipv4:
            enabled: true
            address:
              - ip: 10.171.134.44
                prefix-length: 24
            dhcp: false
      dns-resolver:
        config:
          server:
            - 10.171.134.41
            - 172.25.99.100
      routes:
        config:
          - destination: 0.0.0.0/0
            next-hop-address: 10.171.134.15
            next-hop-interface: ens18
            table-id: 254
```