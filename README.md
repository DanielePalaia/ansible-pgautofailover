# ansible-pgautofailover

This ansibles script is intended to be a simple script in order to install the vmware postgresql package and configure the pg_autofailover framework on the machine. </br>

Pg autofailvoer is an automatic failover mechanism for postgres supported by vmware. It is based on the concept of monitor, primary and replica node. Have a look here for further details: </br>

https://github.com/citusdata/pg_auto_failover</br>


## How to use the script

### Set up python virtual environment

The script uses a python virtual env so you need first to:

```
virtualenv venv
. venv/bin/activate
pip install --upgrade setuptools pip
pip install -r requirements.txt
```

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

the main script uses the following variables:

```
vmw_postgres_package: /Users/dpalaia/projects/ansible/ansible-101/bin/vmware-postgres-13.2-0.el7.x86_64.rpm
postgres_systemd: /Users/dpalaia/projects/ansible/ansible-101/conf/postgresql.service
pgautofailover_systemd: /Users/dpalaia/projects/ansible/ansible-101/conf/pgautofailover.service
data_dir: /var/lib/pgsql/datanew
host0: 192.168.12.138
host1: 192.168.12.137
host2: 192.168.12.136
```

host0, host1 and host2 should be the same you defined in the host file. </br>
vmw_postgres_package is the postgresql vmware distro to install </br>
postgres_systemd and pgautofailover_systemd are two generic systemd file for postgres and pgautofailover in order to register a systemd file on the machine.</br>
data_dir: is the datadir to use for the postgresql install process </br>

Once you set up these variables you can simply run the script:

```
ansible-playbook --user osboxes pg-autofailover.yml --ask-become-pass
```

as user you can choose a user able to do sudo on the system.

```
These are the steps done by the script:

(venv) dpalaia@dpalaia-a02 ansible-101 % ansible-playbook --user osboxes simple-postgres.yml --ask-become-pass 
BECOME password: 

PLAY [PgAutofailover and Vmware postgresql installation process] *************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************************************************************************************
ok: [host0]
ok: [host2]
ok: [host1]

TASK [mkdir /home/osboxes/postgres] ******************************************************************************************************************************************************************************************************************************************
ok: [host2]
ok: [host0]
ok: [host1]

TASK [copy vmware postgresql RPMs to /home/osboxes/postgres] *****************************************************************************************************************************************************************************************************************
ok: [host2] => (item=/Users/dpalaia/projects/ansible/ansible-101/./vmware-postgres-11.7-3.el7.x86_64.rpm)
ok: [host1] => (item=/Users/dpalaia/projects/ansible/ansible-101/./vmware-postgres-11.7-3.el7.x86_64.rpm)
ok: [host0] => (item=/Users/dpalaia/projects/ansible/ansible-101/./vmware-postgres-11.7-3.el7.x86_64.rpm)

TASK [local RPMs not found] **************************************************************************************************************************************************************************************************************************************************
skipping: [host0]
skipping: [host1]
skipping: [host2]

TASK [set_fact] **************************************************************************************************************************************************************************************************************************************************************
ok: [host0]
ok: [host1]
ok: [host2]

TASK [install RPMs] **********************************************************************************************************************************************************************************************************************************************************
ok: [host0]
ok: [host2]
ok: [host1]

TASK [copy generic postgresql systemd file in /etc/systemd/system] ***********************************************************************************************************************************************************************************************************
ok: [host2]
ok: [host0]
ok: [host1]

TASK [copy generic pgautofailover systemd file in /etc/systemd/system] *******************************************************************************************************************************************************************************************************
changed: [host2]
changed: [host0]
changed: [host1]

TASK [Initialize pgautofailover create the monitor] **************************************************************************************************************************************************************************************************************************
changed: [host0 -> 192.168.12.138]

TASK [Pause for 5 minutes to build app cache] ********************************************************************************************************************************************************************************************************************************
Pausing for 120 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
Press 'C' to continue the play or 'A' to abort 
ok: [host0]

TASK [Initialize pgautofailover create the primary] **************************************************************************************************************************************************************************************************************************
changed: [host0 -> 192.168.12.137]

TASK [Pause for 5 minutes to build app cache] ********************************************************************************************************************************************************************************************************************************
Pausing for 120 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
Press 'C' to continue the play or 'A' to abort 
ok: [host0]

TASK [Initialize pgautofailover create the replica] **************************************************************************************************************************************************************************************************************************
changed: [host0 -> 192.168.12.136]

TASK [Restart service pgautofailover on centos, in all cases, also issue daemon-reload to pick up config changes] ************************************************************************************************************************************************************
changed: [host0]
changed: [host1]
changed: [host2]

PLAY RECAP *******************************************************************************************************************************************************************************************************************************************************************
host0                      : ok=13   changed=5    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
host1                      : ok=8    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
host2                      : ok=8    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
```
