**Install chrony**
```
sudo dnf install -y chrony
chronyc tracking
```

**Configure chrony on Bastion**
```
sudo vi /etc/chrony.conf

# Upstream NTP servers (use your corporate / reachable NTP)
server 172.25.99.100 iburst
server 10.25.4.100 iburst

# Allow OpenShift nodes subnet
allow 190.170.20.0/24

# Act as local NTP source if upstream is temporarily unreachable
local stratum 10

# Drift file
driftfile /var/lib/chrony/drift

# Log directory
logdir /var/log/chrony

```

**Start & enable chrony**
```
sudo systemctl enable chronyd --now
systemctl status chronyd

```
**Open firewall for NTP (VERY IMPORTANT)**
```
sudo firewall-cmd --add-service=ntp --permanent
sudo firewall-cmd --reload
sudo firewall-cmd --list-services

```
**Verify Bastion is synced**
```
chronyc tracking

Expected:
Leap status : Normal
System time : 0.x seconds fast/slow
No errors
```
**Check sources:**
```
chronyc sources -v
```
