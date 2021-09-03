# slurm-cluster
How to create a cluster consisting of a head node and multiple compute nodes from scratch.

At the end of this tutorial you'll have a cluster with one head node and multiple compute nodes (e.g. GPU enabled).
Users can ssh into the head node and submit jobs to a resource manager (SLURM).

# Requirements

1. A head node with an Ubuntu server installed on it.
2. Other bare metal nodes without any OS installed on them.

# Install Ubuntu on the compute nodes via PXE boot

### 1. Disable DHCP on the network switch

### 2. Install and configure DHCP and TFTP server

2.1. Install `dnsmasq` (simpler than `isc-dhcp-server`)
```
sudo apt-get install dnsmasq
```

2.2. Set your computer to use a static IP

2.3. Configure dnsmasq add these lines to /etc/dnsmasq.conf
```
interface=eth0
bind-interfaces
dhcp-range=192.168.99.10,192.168.99.254
dhcp-boot=grubnetx64.efi.signed
enable-tftp
tftp-root=/srv/tftp/
```

2.4. Reload dnsmasq
```
systemctl restart dnsmasq.service
```

2.5. Check the status of dnsmasq. It should show `active (running)`.
```
systemctl status dnsmasq.service
```
