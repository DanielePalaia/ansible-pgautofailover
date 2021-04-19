# ansible-pgautofailover

This ansibles script is intended to be a simple script in order to install the vmware postgresql package and configure the pg_autofailover framework on the machine. </br>

Pg autofailvoer is an automatic failover mechanism for postgres supported by vmware. It is based on the concept of monitor, primary and replica node. Have a look here for further details: </br>

https://github.com/citusdata/pg_auto_failover</br>


## How to use the script


### Set up the hosts

First step is to setup the host you want the postgresql distro + pgautofailover to be installed.

you can start editing the hosts file:

```
[targets]
host0 ansible_host=192.168.12.138 
host1 ansible_host=192.168.12.137
host2 ansible_host=192.168.12.136
```

use host0 for the host you would like to install the monitor, host1 for the primary and host2 for the replica.

### Exchange the keys

Use the script copykey.yaml in order to copy the public key from your director machine to the 3 target machines in order to allow the next script to be executed ssh passwordless

```
ansible-playbook --user osboxes  copykey.yaml --ask-become-pass
```

the root password will be asked for the copy exchange

### Run the main script


