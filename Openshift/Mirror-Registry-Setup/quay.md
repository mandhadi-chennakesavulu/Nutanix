**LVM Setup for a  folder to disk**
```jsx
mkdir -p mirror-reg
cd mirror-reg

now we create lvm for sdb
lsblk
NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda             8:0    0  100G  0 disk
├─sda1          8:1    0  600M  0 part /boot/efi
├─sda2          8:2    0    1G  0 part /boot
└─sda3          8:3    0 98.4G  0 part
  ├─rhel-root 253:0    0 60.8G  0 lvm  /
  ├─rhel-swap 253:1    0  7.9G  0 lvm  [SWAP]
  └─rhel-home 253:2    0 29.7G  0 lvm  /home
sdb             8:16   0  250G  0 disk
sr0            11:0    1 11.9G  0 rom  /run/media/root/RHEL-9-6-0-BaseOS-x86_64

------------------------------------------
LVM Setup for the mirror-reg folder
 
step 1: create physical volume
pvcreate /dev/sdb

step 2 : create volume group  
vgcreate vg_mirror /dev/sdb

step 3 : create local_volume 100%
lvcreate -l 100%FREE -n lv_mirror vg_mirror

step 4 : format the logical volume
mkfs.xfs /dev/vg_mirror/lv_mirror

ouptut: 
meta-data=/dev/vg_mirror/lv_mirror isize=512    agcount=4, agsize=16383744 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=1 inobtcount=1 nrext64=0
data     =                       bsize=4096   blocks=65534976, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=31999, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
Discarding blocks...Done.

step 5 : Now Mount the Directary to the lvm you already created /root/mirror-reg
mount /dev/vg_mirror/lv_mirror /root/mirror-reg

step 6 : Make permanent entry to /etc/fstab 
   - Find UUID:
   - blkid /dev/vg_mirror/lv_mirror
   output: 
   /dev/vg_mirror/lv_mirror: UUID="ef7524e3-8707-46d5-9d98-c10cc1682328" TYPE="xfs"
- Then edit file:

vi /etc/fstab
add the above output in the bottom of the fstab
/dev/vg_mirror/lv_mirror: UUID="ef7524e3-8707-46d5-9d98-c10cc1682328" TYPE="xfs"

verify:
df -h

ouput: 
Filesystem                       Size  Used Avail Use% Mounted on
devtmpfs                         4.0M     0  4.0M   0% /dev
tmpfs                            7.7G     0  7.7G   0% /dev/shm
tmpfs                            3.1G  9.7M  3.1G   1% /run
efivarfs                         256K   30K  222K  12% /sys/firmware/efi/efivars
/dev/mapper/rhel-root             61G  4.8G   57G   8% /
/dev/sda2                        960M  356M  605M  38% /boot
/dev/mapper/rhel-home             30G  244M   30G   1% /home
/dev/sda1                        599M  7.1M  592M   2% /boot/efi
tmpfs                            1.6G  100K  1.6G   1% /run/user/0
/dev/sr0                          12G   12G     0 100% /run/media/root/RHEL-9-6-0-BaseOS-x86_64
/dev/mapper/vg_mirror-lv_mirror  250G  1.8G  249G   1% /root/mirror-reg

lsblk

output:
NAME                  MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda                     8:0    0  100G  0 disk
├─sda1                  8:1    0  600M  0 part /boot/efi
├─sda2                  8:2    0    1G  0 part /boot
└─sda3                  8:3    0 98.4G  0 part
  ├─rhel-root         253:0    0 60.8G  0 lvm  /
  ├─rhel-swap         253:1    0  7.9G  0 lvm  [SWAP]
  └─rhel-home         253:2    0 29.7G  0 lvm  /home
sdb                     8:16   0  250G  0 disk
└─vg_mirror-lv_mirror 253:3    0  250G  0 lvm  /root/mirror-reg
sr0                    11:0    1 11.9G  0 rom  /run/media/root/RHEL-9-6-0-BaseOS-x86_64

You should now see /root/mirror-reg mounted from LVM volume.
```

**Setup registry using binaries**

```jsx
Get the required binaries in the folder with the below link 

cd mirror-reg

wget https://developers.redhat.com/content-gateway/file/pub/openshift-v4/clients/mirror-registry/1.3.11/mirror-registry.tar.gz

untar the packages
tar -xzvf mirror-registry.tar.gz

install quay using below command
./mirror-registry install --quayHostname quay.lab.kuberox.net --quayRoot /root/mirror-reg/ --quayStorage /root/mirror-reg/ --initPassword KubePods@321

   __   __
  /  \ /  \     ______   _    _     __   __   __
 / /\ / /\ \   /  __  \ | |  | |   /  \  \ \ / /
/ /  / /  \ \  | |  | | | |  | |  / /\ \  \   /
\ \  \ \  / /  | |__| | | |__| | / ____ \  | |
 \ \/ \ \/ /   \_  ___/  \____/ /_/    \_\ |_|
  \__/ \__/      \ \__
                  \___\ by Red Hat
 Build, Store, and Distribute your Containers

INFO[2025-11-25 06:31:05] Install has begun
INFO[2025-11-25 06:31:05] Found execution environment at /root/mirror-reg/execution-environment.tar
INFO[2025-11-25 06:31:05] Loading execution environment from execution-environment.tar
INFO[2025-11-25 06:31:11] Detected an installation to localhost
INFO[2025-11-25 06:31:11] Found SSH key at /root/.ssh/quay_installer
INFO[2025-11-25 06:31:11] Attempting to set SELinux rules on /root/.ssh/quay_installer
INFO[2025-11-25 06:31:11] Found image archive at /root/mirror-reg/image-archive.tar
INFO[2025-11-25 06:31:11] Detected an installation to localhost
INFO[2025-11-25 06:31:11] Unpacking image archive from /root/mirror-reg/image-archive.tar
INFO[2025-11-25 06:31:14] Loading pause image archive from pause.tar
INFO[2025-11-25 06:31:21] Loading redis image archive from redis.tar
INFO[2025-11-25 06:31:31] Loading postgres image archive from postgres.tar
INFO[2025-11-25 06:31:50] Loading Quay image archive from quay.tar
INFO[2025-11-25 06:32:14] Attempting to set SELinux rules on image archive
INFO[2025-11-25 06:32:14] Running install playbook. This may take some time. To see playbook output run the installer with -v (verbose) flag.
INFO[2025-11-25 06:32:15] Detected an installation to localhost

.
.
.
.
PLAY RECAP ***************************************************************************************************************************************************************************************************
root@quay.lab.kuberox.net  : ok=56   changed=32   unreachable=0    failed=0    skipped=15   rescued=0    ignored=0

INFO[2025-11-25 06:34:45] Quay installed successfully, config data is stored in /root/mirror-reg/
INFO[2025-11-25 06:34:45] Quay is available at https://quay.lab.kuberox.net:8443 with credentials (init, KubePods@321)

0--------------------------------------

Update the certificate to the anchors
cp /root/mirror-reg/quay-rootCA/rootCA.pem /etc/pki/ca-trust/source/anchors/
update-ca-trust 

--------------------------
Now you can broowse the quay by below command : 

https://quay.lab.kuberox.net:8443/repository/
Login page will appear here 
username : init
password : KubePods@321
```

**Now Push the Images to above registry** 

```jsx
create a new folder for pushing the images

mkdir image-push
cd image-push

now get url from mirror.openshift.com for version 4.20 - which we match it with cluster version
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/stable-4.20/oc-mirror.tar.gz

untar the extracted zip file using below command
tar -xzvf oc-mirror.tar.gz

Now We Gona create a Imageset-Config file to upload the operators to the cluster required
[https://access.redhat.com/labs/ocpouic/?upgrade_path=4.18 to 4.19](https://access.redhat.com/labs/ocpouic/?upgrade_path=4.18%20to%204.19)        #URL to get the operators and its versions  

Create 

vi imageset-config.yaml

apiVersion: mirror.openshift.io/v2alpha1
kind: ImageSetConfiguration
mirror:
  platform:
    channels:
      - name: stable-4.20
        minVersion: 4.20.0
        maxVersion: 4.20.0
        type: ocp
        shortestPath: true
  operators:
    - catalog: registry.redhat.io/redhat/redhat-operator-index:v4.20
      packages:
        - name: cluster-logging
          channels:
            - name: stable-6.4
              minVersion: 6.4.0
              maxVersion: 6.4.0
        - name: odf-operator
          channels:
            - name: stable-4.20
              minVersion: 4.20.0-rhodf
              maxVersion: 4.20.0-rhodf
        - name: loki-operator
          channels:
            - name: stable-6.4
              minVersion: 6.4.0
              maxVersion: 6.4.0
        - name: redhat-oadp-operator
          channels:
            - name: stable
              minVersion: 1.5.3
              maxVersion: 1.5.3
  additionalImages:
    - name: registry.redhat.io/ubi9/ubi:latest
    - name: registry.redhat.io/rhel9/support-tools:latest
  helm: {}

execute the above file using below command 1st give permissions to execute the oc-mirror

chmod +x oc-mirror

podman login registry.redhat.io
Username: chennakesavulu@kuberox.com
Password:
Login Succeeded!

podman login quay.lab.kuberox.net:8443
username:
password:
Login Succeeded!

get the pull secret from the url: https://console.redhat.com/openshift/downloads
vi pull-secret.txt
cp pull-secret.txt /root/pullsecret.json

Merge properly

jq -s '.[0].auths + .[1].auths | {auths: .}' \
  /run/user/0/containers/auth.json \
  /root/pullsecret.json \
  > /run/user/0/containers/auth-merged.json

-----
✔ Notice we use + instead of *
✔ This avoids the null error
✔ This cleanly merges both auth maps
---
Replace old auth file
mv /run/user/0/containers/auth-merged.json /run/user/0/containers/auth.json

Now get the auth file:
[root@quay image-push]# cat /run/user/0/containers/auth.json
output:
{
  "auths": {
    "quay.lab.kuberox.net:8443": {
      "auth": "aW5pdDpLdWJlUG9kc0AzMjE="
    },
    "registry.redhat.io": {
      "auth": "fHVoYy1wb29sLWJmZTBkMjhiLTcxNWItNDMzNS05NGRjLTdlYTQ4ZTE4NWIyOTpleUpoYkdjaU9pSlNVelV4TWlKOS5leUp6ZFdJaU9pSTBZVGRqWWpka05UZG1PRGcwT0dNMk9ESXpObVpsT0RkaFl6a3lNV0ppTXlKOS5CMVhBZjBLXzNhTzdLVHMxLXVsb1BuLUllZ1QtazFJY1RodzU1dS1vcGNvMlViMTg4b2IxT0xHSDd2Tm5KZTU0WEpDVnVIZXJNYmdZbFV0S0dtakg3bi1WdzcyMkdVY1UycDVIcEFwdGRuY1QxZk1aai1uaGlDY0U5eEc3MzNZcXAycWpyQ3FUSWdmWVNfZkRkTGxQa3EtUTVSOXEwenc3ZHQ0c09jX2ZuMzRuYW9hSjNoNGsxSkcyNkhTeTI3RWlqdkxXNThOSFN1R0FKeUhmN3ctUnNmVXQ3Q09qMDlQQzB1RVVzZnhOTktRUkVJMXpZMVdIT3UwemdkVzNNMGNqT09fRDVDMlFsMDVYcmFWV2huUUt4d1BqVmtHa3N3bnJqSXBIcU1JY0VzUFNlbnFGQjFNclFiMkQxclFES0E4bU5QR21EZTNKdGNvdkNYYnlrUGdMRm1DNnZrMEMzU0ZPLWY0WUpUMmNUZ29GQVBHVE1iVXE1ZnZGMHlzek1pU0tENzJuVU5ZemxFLW5yMlhJSDlMbXVjTnEwMnNuZENFWVJZbmZBUWVXemc5SF9XOTdTY29iNjR1eFpkS090UFJXcDdBenpTWHRLMm9hQVFPUVpPTkwtc2lab3ZCUF9KSUx2NkpaaElQWnNsbWJ1UjZ4M0pTc1dNTndEWnlCN3ZEUWhIX2Y3SzNzVnZtbXphTXJCdTFOVWxRNURaNm9zbV9LQngwZUFiVE91d19LNGRiZU9jSmljenRYYUdHMmlpLWpkQXRyVUh0QnB3R0ZQRHVHTEE5ZVdoQVlmX29Fb3hJYjZqUlpVMUNhVVpVV3FGUzZtTWJRT0RDQVhrbVB6eTB3TnVtRmZHTkdkN05BQXlYQ1E3aVpSMUtoMTFZSFloUFZJc21PWDJuczl2aw==",
      "email": "chennakesavulu@kuberox.com"
    },
    "cloud.openshift.com": {
      "auth": "b3BlbnNoaWZ0LXJlbGVhc2UtZGV2K29jbV9hY2Nlc3NfNjkyNGM3MTQyNzVjNGM0ZGFiZjcwMTRkNTVhM2M2MWI6RkVPVjBFOVBZSUVJODFVUUI4WVRaSEI3NkRIT0VWVE9ZNFZUVVgxOVQ4MVpKSUZSMFhZOVhBUzlDVTUzNFo2MQ==",
      "email": "chennakesavulu@kuberox.com"
    },
    "quay.io": {
      "auth": "b3BlbnNoaWZ0LXJlbGVhc2UtZGV2K29jbV9hY2Nlc3NfNjkyNGM3MTQyNzVjNGM0ZGFiZjcwMTRkNTVhM2M2MWI6RkVPVjBFOVBZSUVJODFVUUI4WVRaSEI3NkRIT0VWVE9ZNFZUVVgxOVQ4MVpKSUZSMFhZOVhBUzlDVTUzNFo2MQ==",
      "email": "chennakesavulu@kuberox.com"
    },
    "registry.connect.redhat.com": {
      "auth": "fHVoYy1wb29sLWJmZTBkMjhiLTcxNWItNDMzNS05NGRjLTdlYTQ4ZTE4NWIyOTpleUpoYkdjaU9pSlNVelV4TWlKOS5leUp6ZFdJaU9pSTBZVGRqWWpka05UZG1PRGcwT0dNMk9ESXpObVpsT0RkaFl6a3lNV0ppTXlKOS5CMVhBZjBLXzNhTzdLVHMxLXVsb1BuLUllZ1QtazFJY1RodzU1dS1vcGNvMlViMTg4b2IxT0xHSDd2Tm5KZTU0WEpDVnVIZXJNYmdZbFV0S0dtakg3bi1WdzcyMkdVY1UycDVIcEFwdGRuY1QxZk1aai1uaGlDY0U5eEc3MzNZcXAycWpyQ3FUSWdmWVNfZkRkTGxQa3EtUTVSOXEwenc3ZHQ0c09jX2ZuMzRuYW9hSjNoNGsxSkcyNkhTeTI3RWlqdkxXNThOSFN1R0FKeUhmN3ctUnNmVXQ3Q09qMDlQQzB1RVVzZnhOTktRUkVJMXpZMVdIT3UwemdkVzNNMGNqT09fRDVDMlFsMDVYcmFWV2huUUt4d1BqVmtHa3N3bnJqSXBIcU1JY0VzUFNlbnFGQjFNclFiMkQxclFES0E4bU5QR21EZTNKdGNvdkNYYnlrUGdMRm1DNnZrMEMzU0ZPLWY0WUpUMmNUZ29GQVBHVE1iVXE1ZnZGMHlzek1pU0tENzJuVU5ZemxFLW5yMlhJSDlMbXVjTnEwMnNuZENFWVJZbmZBUWVXemc5SF9XOTdTY29iNjR1eFpkS090UFJXcDdBenpTWHRLMm9hQVFPUVpPTkwtc2lab3ZCUF9KSUx2NkpaaElQWnNsbWJ1UjZ4M0pTc1dNTndEWnlCN3ZEUWhIX2Y3SzNzVnZtbXphTXJCdTFOVWxRNURaNm9zbV9LQngwZUFiVE91d19LNGRiZU9jSmljenRYYUdHMmlpLWpkQXRyVUh0QnB3R0ZQRHVHTEE5ZVdoQVlmX29Fb3hJYjZqUlpVMUNhVVpVV3FGUzZtTWJRT0RDQVhrbVB6eTB3TnVtRmZHTkdkN05BQXlYQ1E3aVpSMUtoMTFZSFloUFZJc21PWDJuczl2aw==",
      "email": "chennakesavulu@kuberox.com"
    }
  }
}

./oc-mirror --config=imageset-config.yaml --workspace file:///cache docker://quay.lab.kuberox.net:8443 --v2
or 
./oc-mirror --config=imageset-config.yaml --workspace file:///cache docker://quay.lab.kuberox.net:8443 --v2 --authfile /run/user/0/containers/auth.json --dest-skip-tls

221 / 221 (6m42s) [====================================================================================================] 100 %
 ✓   (1m40s)  support-tools:latest ➡️  quay.lab.kuberox.net:8443/rhel9/
2025/11/25 12:50:51  [INFO]   : === Results ===
2025/11/25 12:50:51  [INFO]   :  ✓  192 / 192 release images mirrored successfully
2025/11/25 12:50:51  [INFO]   :  ✓  27 / 27 operator images mirrored successfully
2025/11/25 12:50:51  [INFO]   :  ✓  2 / 2 additional images mirrored successfully
2025/11/25 12:50:51  [INFO]   : 📄 Generating IDMS file...
2025/11/25 12:50:51  [INFO]   : /cache/working-dir/cluster-resources/idms-oc-mirror.yaml file created
2025/11/25 12:50:51  [INFO]   : 📄 Generating ITMS file...
2025/11/25 12:50:51  [INFO]   : /cache/working-dir/cluster-resources/itms-oc-mirror.yaml file created
2025/11/25 12:50:51  [INFO]   : 📄 Generating CatalogSource file...
2025/11/25 12:50:51  [INFO]   : /cache/working-dir/cluster-resources/cs-redhat-operator-index-v4-20.yaml file created
2025/11/25 12:50:51  [INFO]   : 📄 Generating ClusterCatalog file...
2025/11/25 12:50:51  [INFO]   : /cache/working-dir/cluster-resources/cc-redhat-operator-index-v4-20.yaml file created
2025/11/25 12:50:51  [INFO]   : 📄 Generating Signature Configmap...
2025/11/25 12:50:51  [INFO]   : /cache/working-dir/cluster-resources/signature-configmap.json file created
2025/11/25 12:50:51  [INFO]   : /cache/working-dir/cluster-resources/signature-configmap.yaml file created
2025/11/25 12:50:51  [INFO]   : mirror time     : 6m47.694795502s
2025/11/25 12:50:51  [INFO]   : 👋 Goodbye, thank you for using oc-mirror

----------------------------------------------------------------------

quay.lab.kuberox.net:8443 (YOUR LOCAL QUAY)
Needed because oc-mirror pushes images to your internal registry
Authentication:
→ quayadmin / your password

registry.redhat.io (Red Hat registry)
Needed because your operators come from here
Example operators:
cluster-logging
odf
loki
oadp
These images require:
Red Hat subscription
Red Hat Customer Portal login

Why do we need the Red Hat pull-secret to mirror images?
Because OpenShift release and operator images are stored in protected Red Hat/quay.io repositories, and the pull-secret provides the credentials to access them.

```

**idms & itms ouput:** 

```jsx
cat /cache/working-dir/cluster-resources/idms-oc-mirror.yaml
---
apiVersion: config.openshift.io/v1
kind: ImageDigestMirrorSet
metadata:
  annotations:
    createdAt: Tuesday, 25-Nov-25 17:50:51 UTC
    createdBy: oc-mirror v2
    oc-mirror_version: 4.20.0-202511102026.p2.gb598121.assembly.stream.el9-b598121
  name: idms-release-0
spec:
  imageDigestMirrors:
  - mirrors:
    - quay.lab.kuberox.net:8443/openshift/release
    source: quay.io/openshift-release-dev/ocp-v4.0-art-dev
  - mirrors:
    - quay.lab.kuberox.net:8443/openshift/release-images
    source: quay.io/openshift-release-dev/ocp-release
status: {}
---
apiVersion: config.openshift.io/v1
kind: ImageDigestMirrorSet
metadata:
  annotations:
    createdAt: Tuesday, 25-Nov-25 17:50:51 UTC
    createdBy: oc-mirror v2
    oc-mirror_version: 4.20.0-202511102026.p2.gb598121.assembly.stream.el9-b598121
  name: idms-operator-0
spec:
  imageDigestMirrors:
  - mirrors:
    - quay.lab.kuberox.net:8443/oadp
    source: registry.redhat.io/oadp
  - mirrors:
    - quay.lab.kuberox.net:8443/odf4
    source: registry.redhat.io/odf4
  - mirrors:
    - quay.lab.kuberox.net:8443/openshift-logging
    source: registry.redhat.io/openshift-logging
status: {}

```
**ITMS Output:**
```
cat /cache/working-dir/cluster-resources/itms-oc-mirror.yaml
---
apiVersion: config.openshift.io/v1
kind: ImageTagMirrorSet
metadata:
  annotations:
    createdAt: Tuesday, 25-Nov-25 17:50:51 UTC
    createdBy: oc-mirror v2
    oc-mirror_version: 4.20.0-202511102026.p2.gb598121.assembly.stream.el9-b598121
  name: itms-release-0
spec:
  imageTagMirrors:
  - mirrors:
    - quay.lab.kuberox.net:8443/openshift/release-images
    source: quay.io/openshift-release-dev/ocp-release
status: {}
---
apiVersion: config.openshift.io/v1
kind: ImageTagMirrorSet
metadata:
  annotations:
    createdAt: Tuesday, 25-Nov-25 17:50:51 UTC
    createdBy: oc-mirror v2
    oc-mirror_version: 4.20.0-202511102026.p2.gb598121.assembly.stream.el9-b598121
  name: itms-operator-0
spec:
  imageTagMirrors:
  - mirrors:
    - quay.lab.kuberox.net:8443/openshift4
    source: registry.redhat.io/openshift4
status: {}
---
apiVersion: config.openshift.io/v1
kind: ImageTagMirrorSet
metadata:
  annotations:
    createdAt: Tuesday, 25-Nov-25 17:50:51 UTC
    createdBy: oc-mirror v2
    oc-mirror_version: 4.20.0-202511102026.p2.gb598121.assembly.stream.el9-b598121
  name: itms-generic-0
spec:
  imageTagMirrors:
  - mirrors:
    - quay.lab.kuberox.net:8443/ubi9
    source: registry.redhat.io/ubi9
  - mirrors:
    - quay.lab.kuberox.net:8443/rhel9
    source: registry.redhat.io/rhel9
status: {}
```

![alt text](<images/Screenshot 2025-12-21 125515.png>)

[operator-hub](https://access.redhat.com/labs/ocpouic/?upgrade_path=4.18 to 4.19)