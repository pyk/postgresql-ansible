# PostgreSQL Ansible

This repository contains [ansible playbook](https://docs.ansible.com/ansible/latest/user_guide/playbooks.html)
to provision and manage PostgreSQL database in Ubuntu 18.04.


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

