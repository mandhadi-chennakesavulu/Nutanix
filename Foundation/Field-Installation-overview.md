**Foundation is the official deployment software for Nutanix.**

```
A Nutanix node can join a cluster only if its hypervisor version and AOS version are officially supported together, as listed in the Nutanix Compatibility Matrix.
```
[Software Compatibility Matrix — Nutanix](https://portal.nutanix.com/page/documents/compatibility-interoperability-matrix/software)

```
Foundation is a Nutanix deployment tool
It is used to install (image) or configure Nutanix nodes.
You can use Foundation in two ways
Configure a pre-imaged node
👉 Node already has a hypervisor and AOS installed.
Image a fresh node
👉 Foundation installs the hypervisor + AOS you choose.
All nodes must have the same versions
Hypervisor version must be same on all nodes
AOS version must be same on all nodes
Cluster creation options
You can create a cluster without re-imaging (if versions already match)
Or re-image nodes to make versions match
Foundation tool availability
Foundation can be downloaded from Nutanix (Foundation portal)
```