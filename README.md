Lustre Cluster in vagrant
=========

This is demonstration of a simple lustre cluster using ansible, molecule and
vagrant.

The project creates a network of 4 nodes.

|  node | purpose  |  distro | notes |
|---|---|---|---|
| server-1  | metadata and manager services  |  centos-7 | 2x block devices for metadata and mgr |
| oss-1  |  object store service  |   centos-7 | block device per object store |
| client-1  |  example lustre client  |   centos-8 | mounts to /mnt/lustre |
| client-2  |  example lustre client  |   centos-8 | mounts to /mnt/lustre |

Description
------------

This project is mainly for the purposes of research, to determine how useful and
relevant the various clustered filesystems are to normal use cases.

Results
-------

My observations on lusture from this experiment:

### positives

- The lustre filesystem driver is nice to use on the client.
- Once setup, its quite easy to add more storage, and present it to the clients.
- lustre filesystem simplifies away a lot of the pain of mounting nfs/samba or
fuse based filesystems.
- this is obviously quite a mature platform, and you could do some advanced stuff

By far the nicest thing I immediately noticed was that not having to mess about
with NFS or Samba on the client.

### negatives

- limited platform support (only centos-7 for server)
- only seeing packages for centos-7, centos-8 and ubuntu-18 for client
- packages not in distro repos. so have to rely on irregularly updated 3rd party
- server requires specific kernel, and installing this stuff is non-trival
- only block storage for object store. Would have to do some nasty stuff with
loopback devices to create a simple demo.

I think the main drawback is the lack of general support in mainline distros.
All the messing about with kernels, modules and 3rd party package repos seems
very dated. Lots of the documentation is also quite dated.

Requirements
------------

This project was tested on fedora-34 using recent ansible and molecule. This
demo uses vagrant with VirtualBox as the backend as it uses VirtualBox block
devices for the lustre object store, (which is difficult to do in docker)

Other versions will probably work, but here is what I used:

    $ vagrant --version
    Vagrant 2.2.18

    $ VirtualBox
    VirtualBox-6.1.26-2.fc33.x86_64

    $ python --version
    Python 3.9.7



Usage
----------------

create venv, install requirements, then run molecule from the project directory:

```bash
git clone https://github.com/tolland/ansible-lustre.git ; cd ansible-lustre
#...
python3 -m venv .venv
source .venv/bin/activate
(.venv) $ pip install -r requirements.txt
#...

```

run the project to build the nodes:

```bash
(.venv) $ molecule converge
INFO     default scenario test matrix: dependency, create, prepare, converge
INFO     Performing prerun...
TASK [Gathering Facts] *********************************************************
task path: /molecule/default/converge.yml:3
...

PLAY RECAP *********************************************************************
client-1                   : ok=36   changed=19   unreachable=0    failed=0    skipped=5    rescued=0    ignored=0
client-2                   : ok=36   changed=19   unreachable=0    failed=0    skipped=5    rescued=0    ignored=0
oss-1                      : ok=40   changed=23   unreachable=0    failed=0    skipped=3    rescued=0    ignored=0
server-1                   : ok=45   changed=26   unreachable=0    failed=0    skipped=3    rescued=0    ignored=0
...
```

(The build takes quite a while, as there are kernels and modules installed)

Login to each of the clients, create files in `/mnt/lustre`, and watch them
replicate across the cluster:

```
$ molecule login --host client-1
INFO     Running default > login
Last login: Wed Sep 29 06:47:40 2021 from 10.0.2.2

[vagrant@client-1 ~]$ sudo su -
[root@client-1 ~]# cd /mnt/lustre/
[root@client-1 lustre]# touch test
[root@client-1 lustre]# ls -l
total 0
-rw-r--r--  1 root root    0 Sep 29 06:49 test
```

then login to the other client, and confirm that they have appeared:

```
$ molecule login --host client-2
INFO     Running default > login
[root@client-2 ~]# cd /mnt/lustre/

[root@client-2 lustre]# ls
test

[root@client-2 lustre]# stat test
  File: test
  Size: 0               Blocks: 0          IO Block: 4194304 regular empty file
Device: 2c54f966h/743766374d    Inode: 144115205272502273  Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2021-09-29 06:49:20.000000000 +0000
Modify: 2021-09-29 06:49:20.000000000 +0000
Change: 2021-09-29 06:49:20.000000000 +0000
 Birth: -
 ```

Obviously, you can now experiment with some other features of Lustre, including
permissions, encryption etc...

Enjoy!




License
-------

BSD


