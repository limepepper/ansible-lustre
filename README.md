Lustre Cluster in vagrant
=========

This is demonstration of a simple lustre cluster using ansible, molecule and
vagrant.

The project creates a netwrk of 4 nodes

|  node | purpose  |  distro |
|---|---|---|
| server-1  | metadata and manager services  |  centos-7 |
| oss-1  |  object store  |   centos-7 |
| client-1  |  example lustre client  |   centos-8 |
| client-2  |  example lustre client  |   centos-8 |

Description
------------

This project is mainly for the purposes of research, to determine how useful and 
relevant the various clustered filesystems are to normal use cases.

Results
-------

### positives

- Once setup, its quite easy to add more storage, and present it to the clients.
- the lustre client module and filesystem driver are nice to work with
- lustre filesystem simplifies away a lot of the pain of mounting nfs/samba or
fuse based filesystems.
- this is obviously quite a mature platform, and you could do some advanced stuff

### negatives

- both server and client only have package support for few plaforms. e.g. server
packages only available for centos-7 at the moment. (not 8)
- server requires specific kernel, and installing this stuff is non-trival
- client module support is limited
- only block storage for object store. Would have to do some nasty stuff with
loopback devices to create a simple demo.



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
    ...







License
-------

BSD


