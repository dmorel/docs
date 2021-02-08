## 2016-09-20

* machine is a scaleway C2 with 16GB ram

* downloaded kvm image from Cloudera: <https://downloads.cloudera.com/demo_vm/kvm/cloudera-quickstart-vm-5.8.0-0-kvm.zip>, unzipped, and put in /home/david/kvm

* followed instructions located here:
    * <http://rabexc.org/posts/how-to-get-started-with-libvirt-on>
    * <http://wiki.libvirt.org/page/Networking> 
    * <http://libvirt.org/firewall.html>
    * [Virtio KVM Redhat notes](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Virtualization_Host_Configuration_and_Guest_Installation_Guide/form-Virtualization_Host_Configuration_and_Guest_Installation_Guide-Para_virtualized_drivers-Using_KVM_para_virtualized_drivers_for_existing_devices.html)
    * Fix URI default in shell: <http://libvirt.org/uri.html#URI_default>
    * <https://gist.github.com/s-leroux/b1d6948a9b75bce2146d>
    * Other details to check out <http://abhijitv.blogspot.fr/2015/02/cloudera-hadoop-installation-on-kvm-vms.html?m=1>


* Note the -c qemu... part can be omitted once LIBVIRT_DEFAULT_URI is set in the environment

        sudo apt-get install libvirt-bin virtinst qemu-kvm
        sudo usermod -a -G libvirt david
        
        # install network and check
        virsh -c qemu:///system net-autostart default
        virsh -c qemu:///system net-start default
        virsh -c qemu:///system net-list --all
        
        # install storage and check
        mkdir /home/david/kvm
        virsh -c qemu:///system pool-define-as default dir --target /home/david/kvm
        virsh -c qemu:///system pool-autostart default
        virsh -c qemu:///system pool-start default

        virsh -c qemu:///system pool-list --all
        
        unzip cloudera-quickstart-vm-5.8.0-0-kvm.zip
        mv cloudera-quickstart-vm-5.8.0-0-kvm/cloudera-quickstart-vm-5.8.0-0-kvm.qcow2 kvm/
        
        virt-install --connect qemu:///system --ram 8192 -n cloudera-58 \
            --os-type=linux --os-variant=rhel6 \
            --disk vol=default/cloudera-quickstart-vm-5.8.0-0-kvm.qcow2,device=disk,format=qcow2 --vcpus=4 \
            --vnc --import
        virsh -c qemu:///system start cloudera-58
        virsh -c qemu:///system poweroff cloudera-58
        
* The vga resolution is limited to 1024x768, fix it according to the instructions on <http://blog.bodhizazen.net/linux/how-to-improve-resolution-in-kvm/> (this simply requires installing a custom xorg.conf file). _I struggled a bit trying to make qxd + spice work, didn't succeed quickly enough for my taste, and reverted to the old vga driver_.

## old notes

* KVM is Centos 6.4 based for CDH 5.5 (create kvm with 4GB ram, x86_64, proper Centos version)

* enable avahi to ssh in easily with `ssh quickstart.local` from other machines

        sudo yum -y install avahi
        perl -pi -e 's/^\[server\]/[server]\ndisallow-other-stacks=yes/' \
            /etc/avahi/avahi-daemon.conf
        service avahi-daemon start

* disable eth0 in network manager (no idea what it was supposed to be, eth1 is the right one bridged on br0) and restart NetworkManager service

* set timezone on UTC

        rm /etc/localtime && ln -s /usr/share/zoneinfo/UTC /etc/localtime

* change permissions of cloudera user home dir or ssh key auth (`/home/cloudera/.ssh/authorized_keys`) won't work
        
        chmod g-r /home/cloudera
        
