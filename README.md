# PostgreSQL Ansible

This repository contains [ansible playbook](https://docs.ansible.com/ansible/latest/user_guide/playbooks.html)
to provision and manage PostgreSQL database in Ubuntu 18.04.


## Best Practices
This is list of recommended best practices from PostgreSQL expert. This best
practices that I wrote is only relevant for Ubuntu 18.04 64-bit.

All configurations are set in `/etc/postgresql/12/main/postgresql.conf`.

1. Don't do OLAP (Online Analytical Processing) where you OLTP (Online
   Transactional Processing).

   Slow query in specific table will cause lock
   queue, any other query for that table will wait and slow down.

   It's recommended to create read-replica or build the data pipeline outside
   postgresql database.

   OLAP is where you do the agregation (SUM, COUNT, etc) from large of rows.
   OLTP is where you insert/update/get few rows in real time.
2. Set `listen_address = "*"` and then control who can and cannot connect via
   the `pg_hba.conf` file.
3. Set `max_connections` based on node RAM resources. To many client
   connections will use your RAM.
4. Set `shared_buffers` to 25% of node RAM.
5. Set `effective_cache_size` to `50%` of node RAM.
6. Set `default_statistics_target` to 100
7. Set `work_mem=(50% * total_ram)/max_connections` for starter.
   Make sure `max_connections` times `work_mem` is not larger
   than 50% of total RAM.
8. `log_temp_files` can be used to log sorts, hashes, and temp files which
    can be useful in figuring out if sorts are spilling to disk instead of
    fitting in memory. Increase `work_mem` if this log exists.
9. Set `maintenance_work_mem` to 64MB
10. Disable `autovacumm` when loading bulk of data.


Identifying slow queries and fixing them:
1. Set `log_checkpoints = on` to tell postgresql to log checkpoints activities.
   Query slow maybe the query happen when postgresql writing all the data to
   the checkpoints.
2. Set `log_connections = on` and `log_disconnections = on`. If we have so many
   short-lived connection is very bad for performance.
3. Set `log_lock_waits = on` to make sure that slow query is not because
   deadlock.
4. Set `log_min_duration=300` to tell postgresql to log all queries that
   take more than 300ms
5. Set `log_temp_files = 0` to find if there is any query that use temporary files.
   Query that use temporary files tend to be slow.


References:
- [Chris Sinjakli : Lessons Learned the Hard Way / Postgres in Production at GoCardless - PGDayUK'16](https://www.youtube.com/watch?v=Tu-cf-Jki60)
- [Tuning Your PostgreSQL Server](https://wiki.postgresql.org/wiki/Tuning_Your_PostgreSQL_Server)
- [Postgres Open 2016 - Identifying Slow Queries and Fixing Them!](https://www.youtube.com/watch?v=yhOkob2PQFQ)



## Setup

Clone this repository:

    git clone https://github.com/pyk/postgresql-ansible.git
    cd postgresql-ansible/

Create new python environment:

    python3 -m venv venv

Activate the environment:

    source venv/bin/activate

Install the dependencies:

    pip install -r requirements.txt


## Inventory

Create new `hosts` file with the following content:

    [nodes]
    NODE_NAME ansible_host=IP_ADDRESS ansible_user=root ansible_python_interpreter=/usr/bin/python3

## Provision

Run the following command to provision new PostgreSQL database:

    ansible-playbook -i hosts provision.yaml

