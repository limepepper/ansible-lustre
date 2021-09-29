Lustre Cluster in vagrant
=========

This is demonstration of a simple lustre cluster using ansible, molecule and
vagrant.

The project creates a network of 4 nodes.

|  node | purpose  |  distro |
|---|---|---|
| server-1  | metadata and manager services  |  centos-7 |
| oss-1  |  object store service  |   centos-7 |
| client-1  |  example lustre client  |   centos-8 |
| client-2  |  example lustre client  |   centos-8 |

Description
------------

This project is mainly for the purposes of research, to determine how useful and
relevant the various clustered filesystems are to normal use cases.

Results
-------

My observations on lusture from this experiment:

### positives

- The lustre filesystem driver is really nice to use on the client.
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
demo uses vagrant with VirtualBox as the backend as it uses block devices for
the lustre object store, (which is difficult to do in docker)


Usage
----------------

Run molecule from the project directory:

    $ molecule create
    $ molecule list

      Instance Name │ Driver Name │ Provisioner Name │ Scenario Name │ Created │ Converged
    ╶───────────────┼─────────────┼──────────────────┼───────────────┼─────────┼───────────╴
      server-1      │ vagrant     │ ansible          │ default       │ true    │ false
      oss-1         │ vagrant     │ ansible          │ default       │ true    │ false
      client-1      │ vagrant     │ ansible          │ default       │ true    │ false
      client-2      │ vagrant     │ ansible          │ default       │ true    │ false

run the project to build the nodes:

    $ molecule converge
    INFO     default scenario test matrix: dependency, create, prepare, converge
    INFO     Performing prerun...
    ...
    TASK [Gathering Facts] *********************************************************
    task path: /home/tomhodder/Sync/ansible/roles/limepepper.lustre/molecule/default/converge.yml:3
    ok: [oss-1]
    ok: [server-1]
    ok: [client-1]
    ok: [client-2]
    ...







License
-------

BSD


