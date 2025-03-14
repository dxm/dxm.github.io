# Libvirt

## DNS

Setup DNS resolution from the host.

> Since 10.1.0 the optional register attribute can be used to request
> registering the DNS server for resolving this domain with the host's
> DNS resolver. When set to "yes", the host resolver will forward all
> requests for domain names from this domain to the DNS server created
> for this virtual network. To avoid DNS loops localOnly has to be set
> to "yes" as well. This feature requires systemd-resolved to be running
> on the host.

- [libvirt: Network XML format](https://libvirt.org/formatnetwork.html#id3)

network.xml:

```xml
<network>
  <name>dev</name>
  <forward mode='nat'>
    <nat>
      <port start='1024' end='65535'/>
    </nat>
  </forward>
  <domain name='dev.lan' localOnly='yes' register='yes'/>
  <bridge name='virbr1' stp='on' delay='0'/>
  <ip address='192.168.125.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.125.2' end='192.168.125.254'/>
    </dhcp>
  </ip>
</network>
```

Define and enable the network:

```bash
$ virsh net-define --file network.xml
$ virsh net-start dev
$ virsh net-autostart dev
```

## Provision

Background the install and force reboot on completion.

> --noautoconsole
>
> Note, virt-install exits quickly when this option is specified. If
> your command requested a multistep install, like --cdrom or
> --location, after the install phase is complete the VM will be
> shutoff, regardless of whether a reboot was requested in the VM. If
> you want the VM to be rebooted, virt-install must remain running. You
> can use '--wait' to keep virt-install alive even if --noautoconsole is
> specified.

```bash
$ virt-install \
--name myhost \
--hvm \
--vcpus 2 \
--memory 4096 \
--disk path=/var/lib/libvirt/images/myhost.img,size=10 \
--network network=dev \
--graphics=none \
--location /var/lib/libvirt/images/RHEL.iso \
--extra-args console=ttyS0 \
--extra-args='inst.ks=file:kickstart.cfg' \
--initrd-inject=/tmp/kickstart.cfg \
--wait=-1 \
--noautoconsole &
```

## SSH

Disable host key checks for domain.

```
# ~/.ssh/config

Host *.dev.lan
    UserKnownHostsFile /dev/null
    StrictHostKeyChecking no
```
