This ansible playbook will install postgresql software + all dependencies on centos
===================================================================================

STILL IN PROGRESS

<b>Requirements</b>
 
- [Vagrant 1.7.4 >](https://www.vagrantup.com)
- [Ansible 1.9.x >](http://www.ansible.com)
- [VirtualBox](https://www.virtualbox.org)
- [Pgadmin for administration](http://www.pgadmin.org) 
- More then 2gb Memory

<b>Testing</b>

- Tested PostgreSQL 9.4 DONE with pg_statsinfo 3.2.0, centos 6 and 7 
- Not supported PostgreSQL versions 8.4, 9.0, 9.1, 9.2, 9.3 and OS centos 5


<b>Features</b>

- PostgreSQL install with configured variables
- [pg_statsinfo](http://sourceforge.net/projects/pgstatsinfo/?source=navbar)
- [PostgreSQL backup script with Continuous Archiving and Point-in-Time Recovery (PITR) ](http://www.postgresql.org/docs/current/static/continuous-archiving.html)


<b>ROADMAP</b>
- Alerting (nagios or alternative)
- pgbouncer for connection pooling 
- Add PostgreSQL 9.5 when official released
- Add docker option

<b>How to use in production</b>

Create inventory file and host_vars

```

```

Create customizations file 

```

```

Run playbook to your server(s)

```

```

<b>Vagrant instructions to test</b>

Info password for vagrant user is vagrant

Create customizations file 

```

```

```
vagrant up 
```

Centos 6 

PostgreSQL
```
psql -h 192.168.32.9 -p 5432 -U postgres

```

pg_reporter

```
https://192.168.32.9/
```

Centos 7

```
psql -h 192.168.32.10 -p 5432 -U postgres 
```

pg_reporter

```
https://192.168.32.10/
```

Go into the vm with ssh to check if everything is healthy

```
vagrant ssh pgsqlcentos6 
vagrant ssh pgsqlcentos7
```

After testing is done

```
vagrant destroy 
```
