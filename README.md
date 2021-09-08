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
interface=eth0 # change this depending on your machine
bind-interfaces
dhcp-range=192.168.99.10,192.168.99.254  # change this depending on your network or VLAN
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

### 3. Create boot files (+ autoinstall)

1. Install the following packages:
```bash
sudo apt install pxelinux syslinux-efi syslinux-common
```

Use this unofficial script to create the boot: (https://github.com/dannf/ubuntu-server-netboot)[https://github.com/dannf/ubuntu-server-netboot]

2. Create the autoinstall config file

```yaml
autoinstall:
  version: 1
  # use interactive-sections to avoid an automatic reboot
  #interactive-sections:
  #  - locale
  apt:
    # even set to no/false, geoip lookup still happens
    #geoip: no
    preserve_sources_list: false
    primary:
    - arches: amd64
      uri: http://us.archive.ubuntu.com/ubuntu
    - arches: [default]
      uri: http://ports.ubuntu.com/ubuntu-ports
  # r00tme
  identity: 
    hostname: node 
    username: varadmin
    password: $PASSWARD 
  keyboard: 
    layout: us 
    variant: ''
  locale: en_US.UTF-8
  # interface name will probably be different
  ssh:
    allow-pw: true
    authorized-keys: []
    install-server: true
  late-commands:
    - curtin in-target --target=/target -- apt update           
    - curtin in-target --target=/target -- apt upgrade -y

```

2.1 Use the following command to hash the desired password
```bash
sudo apt install whois
mkpasswd -m sha-512
```

2. Create the boot file

3. Copy the content to `/srv/tftp/`

### Boot the compute nodes (Ubuntu will be automatically installed on them)
