```
Configuring a local arbiter node 

You can configure an OpenShift Container Platform cluster with **two control plane nodes** and **one local arbiter node** to retain high availability (HA) while reducing infrastructure costs for your cluster.

A local arbiter node is a lower-cost, co-located machine that participates in control plane quorum decisions. Unlike a standard control plane node, the arbiter node does not run the full set of control plane services. You can use this configuration to maintain HA in your cluster with only two fully provisioned control plane nodes instead of three.

This feature is now Generally Available.
```
[arbiter cluster Document Reference](https://docs.redhat.com/en/documentation/openshift_container_platform/4.20/html-single/release_notes/index#ocp-release-notes-api_release-notes)

```
A two-node OpenShift cluster with fencing provides high availability (HA) with a reduced hardware footprint. This configuration is designed for distributed or edge environments where deploying a full three-node control plane cluster is not practical.

A two-node cluster does not include compute nodes. The two control plane machines run user workloads in addition to managing the cluster.
```

```
You can deploy a two-node OpenShift cluster with fencing by using either the user-provisioned infrastructure method or the installer-provisioned infrastructure method.
```

```
To deploy a cluster with two control plane nodes and one local arbiter node, you must define the following nodes in the install-config.yaml file:

2 control plane nodes
1 arbiter node
```

```
The arbiter node must meet the following minimum system requirements:

2 vCPUs
8 GB of RAM
50 GB of SSD or equivalent storage
The arbiter node must be located in a network environment with an end-to-end latency of less than 500 milliseconds, including disk I/O. In high-latency environments, you might need to apply the etcd slow profile.

The control plane nodes must meet the following minimum system requirements:

4 vCPUs
16 GB of RAM
120 GB of SSD or equivalent storage
Additionally, the control plane nodes must also have enough storage for the workload.
```