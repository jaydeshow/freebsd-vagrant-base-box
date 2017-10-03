# Create a FreeBSD Vagrant Box File

Despite there are several [Vagrant boxes](http://www.vagrantbox.es/) available which run FreeBSD out of the box, it's good to know how to create such a box by yourself. An advantage might be, that the box comes with only those packages installed you really need. That's why I decided to create a minimal FreeBSD box with only as much packages installed to run Ansible. With Ansible installed it's quite easy to perfectly adapt the box afterwards.

### Prerequisites

1. VirtualBox installed, [download page](https://www.virtualbox.org/wiki/Downloads)

2. Vagrant installed, [download page](https://www.vagrantup.com/downloads.html)

### Create a VB virtual machine

1. Create an virtual machine with NAT enabled for the first network adapter
and add set the adapter type to `virtio-net`. Make sure the size of the
configured hard disk is at least 8GB (choose a disk size that will suite your
needs ~120GB).

### Virtualbox Settings 虛擬硬體設定

Create a new Virtual Machine (minimal 1024MB Memory) with the following settings:

- System -> Motherboard -> **Hardware clock in UTC time**
- System -> Acceleration -> **VT/x/AMD-V**
- System -> Acceleration -> **Enable Nested Paging**
- Storage -> Attach a **.vdi** disk (this one we can minimize later)
- Network -> Adapter 1 -> Attached to -> NAT
- Network -> Adapter 1 -> Advanced -> Adapter Type -> **Paravirtualized Network (virtio-net)**
- Network -> Adapter 2 -> Advanced -> Attached to -> **Host-Only Adapter**
- Network -> Adapter 2 -> Advanced -> Adapter Type -> **Paravirtualized Network (virtio-net)**

I would also recommend to disable all the things you are not using, such as
*audio* and *usb*.

2. Add the [downloaded mfsBSD ISO](http://mfsbsd.vx.sk/) (any edition) 
as bootable CD device.

recommend 11.1-RELEASE-amd64 special edition (191MB)

3. Boot.

4. Login with `root` and `mfsroot`.

5. Install FreeBSD.

### Installation from mfsBSD ISO

Attach the ISO as a CD and boot it. You can login with `root` and password
`mfsroot`. After logging in, start the base installation with:

#ee /etc/ssh/sshd_config
+PermitRootLogin yes
#service sshd restart

add a ssh port forwarding
root@mfsbsd:~ # mount_cd9660 /dev/cd0 /media/
root@mfsbsd:~ # zfsinstall -d /dev/ada0 -u /media/11.1-RELEASE-amd64 -s 4G

8. Stop the VM.

When the installation is done, you can `poweroff` and **remove the CD from
boot order in the settings.**

root@mfsbsd:~ # poweroff

9. Remove attached ISO
10. Boot the VM, login with `root` and execute the following commands.

        dhclient vtnet0
        fetch -o - --no-verify-peer \
         https://raw.githubusercontent.com/JoergFiedler/freebsd-vagrant-base-box/master/update.sh \
         | sh | tee out.log

Note: You may use this short link instead: `http://bit.ly/1lEHtrx`.

10. Halt the VM.

### Create the Box File

1. To create the box file just run the following command line.

        vagrant package --base my-virtual-machine

2. The file `package.box` can be referenced by `vm.box_url` configuration
parameter within your `Vagrantfile`.


### Box File

#### FreeBSD 11.0

Download the final box file here: http://vastland.moumantai.de/FreeBSD/vagrant-box/FreeBSD-11.0-vagrant-fe6134d.box

SHA256 Checksum: `fe6134d5cd5c1d5bce705ded1333445236b135c20929c8dde9592e73d0ea001d`

#### FreeBSD 10.3

Download the final box file here: http://vastland.moumantai.de/FreeBSD/vagrant-box/FreeBSD-10.3-vagrant-697852c.box

SHA256 Checksum: `697852c81fb7c2121eaa73fd45cdef841105d8c100e2c5d7ccbe3339ee1e1cb2`

#### FreeBSD 10.2

Download the final box file here: http://vastland.moumantai.de/FreeBSD/vagrant-box/FreeBSD-10.2-vagrant-abe25b9.box

SHA256 Checksum: `abe25b9b2b934053a624632323f7c62c1fe6dce0fd7453fb484cc2aba17f4056`

#### FreeBSD 10.1
Download the final box file here: http://vastland.moumantai.de/FreeBSD/vagrant-box/FreeBSD-10.1-vagrant-base.box

SHA256 Checksum: `67f6d3e60d26ae811b685f13ddd3683a0cc7280ac8a104203006573ea831e699`

#### FreeBSD 10.0
Download the final box file here: http://vastland.moumantai.de/FreeBSD/vagrant-box/FreeBSD-10-vagrant-base.box

SHA256 Checksum: `3418526d3a67313d3763ac75eb5b46c2d5d90837342e9d4fdef46dc387128735`

### Links
1. mfsBSD: https://www.freebsd.org/doc/en/articles/remote-install/preparation.html
2. mfsBSD Image: http://mfsbsd.vx.sk/
3. Vagrant HowTo - Creating a Base Box: https://docs.vagrantup.com/v2/virtualbox/boxes.html
4. FreeBSD VB information: https://wiki.freebsd.org/VirtualBox
5. http://unix.stackexchange.com/questions/39524/sharing-folder-from-windows-host-to-freebsd-guest/155373#155373

### Installed Packages

`pkg info`

    bash                           The GNU Project's Bourne Again SHell
    ca_root_nss                    Root certificate bundle from the Mozilla Project
    gettext-runtime                GNU gettext runtime libraries and programs
    indexinfo                      Utility to regenerate the GNU info page index
    iocage                         Full featured, no dependency Jail container manager
    libffi                         Foreign Function Interface
    pkg                            Package manager
    python                         The "meta-port" for the default version of Python interpreter
    python2                        The "meta-port" for version 2 of the Python interpreter
    python27                       Interpreted object-oriented programming language
    sudo                           Allow others to run commands as root
