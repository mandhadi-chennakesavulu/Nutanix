**Foundation is the official deployment software for Nutanix.**

```
A Nutanix node can join a cluster only if its hypervisor version and AOS version are officially supported together, as listed in the Nutanix Compatibility Matrix.
```
[Software Compatibility Matrix — Nutanix](https://portal.nutanix.com/page/documents/compatibility-interoperability-matrix/software)

```
Foundation is a Nutanix deployment tool
It is used to install (image) or configure Nutanix nodes.

*** You can use Foundation in two ways ***

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

```
Part 1: Adding nodes to an existing cluster

If a Nutanix cluster is already running
You must NOT use Foundation to add new nodes.
Use Prism → Expand Cluster option
Prism is the Nutanix management UI.
Expand Cluster is the correct and supported method.
What Expand Cluster can do
It can re-image a node if:
The node’s hypervisor/AOS version is different from the cluster
OR the node is running DiscoveryOS only
DiscoveryOS
A temporary OS used to discover and prepare new nodes
Prism can re-image DiscoveryOS into the correct hypervisor + AOS
```
```
Part 2: Factory-installed software on Nutanix nodes

Nodes come pre-installed from the factory
Nutanix and OEM partners install software before shipping
For shipments inside the USA
Nodes usually arrive with:
AHV hypervisor
AOS installed

For OEM vendor nodes
The hypervisor may vary (vendor choice)
But AOS is always installed
```

```
1️⃣ How nodes are shipped
Case A: USA shipment
Node already has:
Hypervisor
AOS
✅ Almost ready to use

Case B: Outside USA shipment
Node has only DiscoveryOS
DiscoveryOS = temporary, very small OS
Purpose: just to detect the node
❌ Cannot join cluster yet

Case C: Non-OEM vendor node
Node has nothing installed
No OS at all
This is called bare-metal
```

```
👉 DiscoveryOS node + existing cluster
Use Prism → Expand Cluster
It will install hypervisor + AOS
Node joins cluster ✅

👉 Bare-metal node + existing cluster
Use Foundation → install hypervisor + AOS
Then use Prism → Expand Cluster
Node joins cluster ✅

👉 New cluster (no cluster exists yet)
Use Foundation
Image all nodes
Create cluster
```

```
Foundation installs software. Expand Cluster adds nodes. DiscoveryOS is only for detection. Bare-metal needs Foundation first.
```
```
SIMPLE FLOWCHART (How to add a node)
```
```
Start
  |
  v
Do you already have a running Nutanix cluster?
  |
  |-- NO --> Use FOUNDATION
  |           - Install Hypervisor
  |           - Install AOS
  |           - Create Cluster
  |
  |-- YES --> What software is on the new node?
              |
              |-- Hypervisor + AOS (version mismatch)
              |       --> Prism → Expand Cluster
              |
              |-- DiscoveryOS only
              |       --> Prism → Expand Cluster
              |
              |-- No software (Bare-metal)
                      |
                      v
                  Use FOUNDATION first
                      |
                  Install Hypervisor + AOS
                      |
                  Then use Prism → Expand Cluster

```

```
| Situation                                   | Tool to Use                     | Why |
|--------------------------------------------|----------------------------------|-----|
| New cluster (no cluster exists)             | Foundation                       | Installs hypervisor and AOS |
| Node has DiscoveryOS only                  | Prism → Expand Cluster           | Can image DiscoveryOS nodes |
| Node has Hypervisor + AOS (version mismatch)| Prism → Expand Cluster           | Re-images to match cluster |
| Node has no software (bare-metal node)     | Foundation → then Expand Cluster | Expand Cluster can't image bare-metal |
| Adding node to an existing cluster         | Prism → Expand Cluster           | Supported way to scale cluster |

```