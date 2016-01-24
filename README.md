## This ansible playbook will install postgresql software + all dependencies on centos 

### Requirements
 
- [Vagrant](https://www.vagrantup.com) 1.7.4 >
- [Ansible](http://www.ansible.com) 1.9.x >
- [VirtualBox](https://www.virtualbox.org)
- [Pgadmin](http://www.pgadmin.org) for administration
- More then 2gb Memory

### Testing 

- Tested PostgreSQL 9.4 DONE with pg_statsinfo 3.2.0, centos 6 and 7 
- Not supported PostgreSQL versions 8.4, 9.0, 9.1, 9.2, 9.3 and OS centos 5

### Features

- Selinux enabled
- PostgreSQL install with configured variables
- [pg_statsinfo](http://sourceforge.net/projects/pgstatsinfo/?source=navbar)
- [PostgreSQL backup script with Continuous Archiving and Point-in-Time Recovery (PITR) ](http://www.postgresql.org/docs/current/static/continuous-archiving.html)

### Roadmap
- Alerting (nagios or alternative)
- pgbouncer for connection pooling 
- Add PostgreSQL 9.5 when official released
- Add docker option

### Vagrant instructions to test

Info password for vagrant user is vagrant

Create customizations file and edit [UNCONFIGURED] and other vars

```
cp -rp group_vars/postgresql customizations
vi customizations
# Variables here are applicable to all hosts

# notification 
hipchat_enabled: false
hipchat_api_token: [UNCONFIGURED]
hipchat_room: [UNCONFIGURED]

# centos
centos_version: "{{ ansible_distribution_major_version }}"

# postgresql vars
postgresql_version: 9.4 # default is 9.4 (is the latest)
postgresql_pkg_version: 94 # default is 94 (is the latest)
postgresql_postgres_password: [UNCONFIGURED] # generate with keepass or passsafe
postgresql_memory: 512MB # use MB, GB, TB
postgresql_backends: 150 # normal postgresql default is 100
postgresql_backup_keep: 3 # keep 3 days backups

# statistics vars
pgstatsinfo_enabled: true
pgreporter_user: pgreport
pgreporter_password: [UNCONFIGURED] 

# cert vars
state: "Zuid Holland"
city: The Hague 
organization: example BV
```

```
vagrant up 
```

<b>When you have issues with vagrant box</b>

Find latest boxes on https://atlas.hashicorp.com/boxes/search?utf8=%E2%9C%93&sort=updated&provider=&q=centos6

and edit ```pgsqlcentos6.vm.box = "jhcook/centos6"``` in Vagrantfile

Find latest boxes on https://atlas.hashicorp.com/boxes/search?utf8=%E2%9C%93&sort=updated&provider=&q=centos7

and edit ```pgsqlcentos7.vm.box = "hfm4/centos7"``` in Vagrantfile

#### Centos 6

<b>PostgreSQL </b>

```
psql -h 192.168.32.9 -p 5432 -U postgres
```

<b>pg_reporter</b>

```
https://192.168.32.9/
```

#### Centos 7

<b>PostgreSQL</b> 

```
psql -h 192.168.32.10 -p 5432 -U postgres 
```

<b>pg_reporter</b>

```
https://192.168.32.10/
```

<b>Go into the vm with ssh to check if everything is healthy</b>

```
vagrant ssh pgsqlcentos6 
vagrant ssh pgsqlcentos7
```

Wait for first pg_statsinfo snapshot or create manualy

pg_statsinfo.snapshot_interval = 30 min  

Create 2 or more snapshots manualy

```
# su - postgres
$ psql -d postgres -c "SELECT statsinfo.snapshot('testsnap')"
```

Check webinterface pg_reporter if there are snapshots available

Backup check 

```
# su - postgres
Last login: Sun Jan 24 10:44:29 CET 2016 on pts/0
-bash-4.2$ /bin/sh /var/lib/pgsql/backup_postgresql.sh -p 5432 -H 192.168.32.10
-bash-4.2$ cd /pgbackup/
-bash-4.2$ ls
lock  log  pgbackup_24-01-2016_10_45_05.tgz  pgwals_24-01-2016_10_45_05.tgz
-bash-4.2$ ls -ltrh
total 8.9M
drwxr-x---. 2 postgres postgres   46 Jan 24 10:45 log
-rw-r--r--. 1 postgres postgres 3.4M Jan 24 10:45 pgbackup_24-01-2016_10_45_05.tgz
-rw-r--r--. 1 postgres postgres 5.5M Jan 24 10:45 pgwals_24-01-2016_10_45_05.tgz
drwxr-x---. 2 postgres postgres    6 Jan 24 10:45 lock
-bash-4.2$ cd log/
-bash-4.2$ ls
pg_backup_log_24-01-2016_10_45_05
-bash-4.2$ cat pg_backup_log_24-01-2016_10_45_05 
Old backup logs are deleted
Start Hot Backup
 pg_start_backup 
-----------------
 0/7000028
(1 row)

Starting tar of base dir and wal files
data/
data/global/
data/global/pg_control
data/global/pg_filenode.map
data/global/12798
data/global/12800
data/global/12801
data/global/12932
data/global/12934
data/global/12936
data/global/12937
data/global/12938
```

After testing is done

```
vagrant destroy 
```

### How to use in production

<b>Edit host_vars for production</b>

```
vi host_vars/pgsqlcentos6prod or host_vars/pgsqlcentos7prod
ansible_host: "set public ip here"
ansible_port: "set port here"
private_ip: "set private ip here"
```

<b>Create customizations file</b> 

```
cp -rp group_vars/postgresql customizations
vi customizations
# Variables here are applicable to all hosts

# notification 
hipchat_enabled: false
hipchat_api_token: [UNCONFIGURED]
hipchat_room: [UNCONFIGURED]

# centos
centos_version: "{{ ansible_distribution_major_version }}"

# postgresql vars
postgresql_version: 9.4 # default is 9.4 (is the latest)
postgresql_pkg_version: 94 # default is 94 (is the latest)
postgresql_postgres_password: [UNCONFIGURED] # generate with keepass or passsafe
postgresql_memory: 512MB # use MB, GB, TB
postgresql_backends: 150 # normal postgresql default is 100
postgresql_backup_keep: 3 # keep 3 days backups

# statistics vars
pgstatsinfo_enabled: true
pgreporter_user: pgreport
pgreporter_password: [UNCONFIGURED] 

# cert vars
state: "Zuid Holland"
city: The Hague 
organization: example BV
```

<b>Run playbook to your server(s)</b>

With ssh key

```
ansible-playbook -i inventory/prod -l [pgsqlcentos6prod or pgsqlcentos7prod] -u [username] --private-key [location ssh key] site.yml -e @customizations
```

With username and password (you need ssh-pass installed on the system)

```
ansible-playbook -i inventory/prod -l [pgsqlcentos6prod or pgsqlcentos7prod] -u [username] -k -K site.yml -e @customizations
```
