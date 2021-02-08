# get LXC up and running on debian jessie in 5 minutes

LXC is really interesting and easy to set up!

- set up a bridged config for the host's NIC (Intel NUC here), in
  `/etc/network/interfaces`:
        
        # no eth0 on this machine, the NIC is enp0s25 
        # this config was set up for KVMs, seems to work fine with lxd too 
        auto enp0s25 
        auto br0 
        iface br0 inet static 
        # give it a "main" ip address to reach the host
        address 192.168.0.200 
        netmask 255.255.255.0 
        gateway 192.168.0.1
        bridge_ports enp0s25
        bridge_stp off
        bridge_fd 0
        bridge_maxwait 0
        # optional: this is to copy the NIC's MAC address on
        # the bridge
        post-up ip link set br0 address b8:ae:ed:75:fc:88
            
- install lxc

        apt-get install lxc

- create a new container (Centos 7 amd64 here):
        
        root@nuc:~# lxc-create -t download -n centos7
        ...  
        root@nuc:~# lxc-ls -f 
        NAME     STATE    IPV4  IPV6  AUTOSTART
        ---------------------------------------
        centos7  STOPPED  -     -     NO

- add network configuration in `/var/lib/lxc/centos7/config`; giving it a
  static HW address will allow to manage persistent leases on the DHCP server.
  The interface won't be visible in ifconfig on the host with this hw address,
  but will register as set on the DHCP server, as listing the leases on the
  server shows (todo: read more on ethernet bridging)
    
        # Network configuration
        lxc.network.type = veth
        lxc.network.flags = up
        lxc.network.link = br0
        lxc.network.name = eth0
        lxc.network.hwaddr = de:ad:be:ef:00:01
        
- start new container in foreground mode
        
        lxc-start -n centos7
        
- on starting, login as root/root. This will prompt for a password change, for
  some reason the prompt was stuck after the change, had to open another
  terminal and run this for the machine to restart fine (password change seems
  to have been taken into account just fine):

        lxc-stop centos7
        lxc-start centos7
        
- install openssh (and set it to start at boot? not sure that last step is
  needed):
        
        yum install openssh-server
        systemctl enable sshd.service

- reboot the guest, login through ssh (on the address provided/registered on
  the DHCP server); for the next runs in background mode, simply use:

        lxc-start centos7 -d

- for debian 8 (jessie) the procedure is almost identical, except the root
  password isn't set on startup; we can solve that by running the container in
  the background and chrooting to it:

        root@nuc:~# lxc-start -name jessie -d
        root@nuc:~# chroot /var/lib/lxc/debian8/rootfs/ 
        root@nuc:/# passwd
        ...  
        # install useful daemons too 
        root@nuc:/# apt-get install openssh-server avahi-daemon

- **TODO** it looks like systemd-journald on the host keeps 1 core 100% busy;
  needs looking into (likely caused by hanging yum on the centos guest)

### Links 

- <https://www.flockport.com/lxc-networking-guide/>
- <http://www.funtoo.org/Linux_Containers>

