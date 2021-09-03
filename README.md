# slurm-cluster
How to create a cluster consisting of a head node and multiple compute nodes from scratch.

At the end of this tutorial you'll have a cluster with one head node and multiple compute nodes (e.g. GPU enabled).
Users can ssh into the head node and submit jobs to a resource manager (SLURM).

# Requirements

1. A head node with an Ubuntu server installed on it.
2. Other bare metal nodes without any OS installed on them.

# Install Ubuntu on the compute nodes via PXE boot
1. Disable DHCP on the network switch
2. Install and setup DHCP server on the head node (https://www.linuxfordevices.com/tutorials/ubuntu/dhcp-server-on-ubuntu)[https://www.linuxfordevices.com/tutorials/ubuntu/dhcp-server-on-ubuntu]
