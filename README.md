# Basic Install Steps
The basic process is download a Ubuntu pre-installed server image [19.10](http://cdimage.ubuntu.com/releases/19.10/release/ubuntu-19.10.1-preinstalled-server-arm64+raspi3.img.xz), flash it onto a microSD card using balenaEtcher, customize it and then use it to boot the Raspberry Pi's. Once the Pi's are booted, you can use ansible-playbook to update the OS and install Kubernetes.

## Configure the user-data file
1. In the users section replace the darkmane user with your user information, make sure you have at least 1 value under ssh_authorized_keys. Check .ssh/id_rsa.pub for a value
1. Also set the plain_text_passwd to a secure password
1. Update the /etc/wpa_supplicant/wpa_supplicant.conf entry in the write_files section to allow it to connect to your Wi-Fi network
1. Set the hostname field to a unique value. 

## Download Image
Choose the version of Ubuntu Server you want to run [18.04.4 LTS](http://cdimage.ubuntu.com/releases/18.04.4/release/ubuntu-18.04.4-preinstalled-server-arm64+raspi3.img.xz) [19.10](http://cdimage.ubuntu.com/releases/19.10/release/ubuntu-19.10.1-preinstalled-server-arm64+raspi3.img.xz)
1. Download the image
1. Start [balenaEtcher](https://www.balena.io/etcher/)
1. Select the image, drive, and presh flash

## Customize the microSD card
1. Copy nobtcmd.txt to the microSD card. This enables cgroup_memory which is required by Kubernetes.
1. Copy user-data to the microSD card, it will configure your OS on first boot. Adding the user account, configuring sshd to only allow authentication via public key.

## Update your inventory.yml
1. update etc/ansible/inventory.yml
    1. 1 entry under control_plane. If you want a High Availability cluster, the total number of hosts should be odd.
    1. 1 or more workers in the worker section. 

## Validate the Pi booted.
1. Check your Wi-Fi router, make sure the hostname appears
1. ssh <hostname> should succeed

## Configure Control plane
1. ansible-playbook -i etc/ansible/inventory.yml control-plane-playbook.yml

## Configure Workers
1. ansible-playbook -i etc/ansible/inventory.yml worker-playbook.yml
