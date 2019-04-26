Molecule Tutorial
=================

This project provides a test environment for using [Molecule]() to implement and test Ansible Cookbooks. It provides a Vagrant box with all necessary tools installed and pre-configured so that you can jump directly into implementing your playbooks.


Prerequisites
-------------

* VirtualBox
* Vagrant
* Set a `http_proxy` env var pointing to a local proxy before running `vagrant up`


What you get
------------

A Vagrant box

* based on `ubuntu/bionic64 18.04`
* with Docker installed
* with molecule and all Python packages required to run it
* with Docker configured so that it runs with molecule out of the box


How to use it
-------------

Run

```bash
vagrant up
vagrant reload
```

The reload is necessary for configuring the HTTP proxy for Docker - see https://github.com/tmatilai/vagrant-proxyconf/issues/97

Afterwards you can get into the box via

```bash
vagrant ssh
```

Clone a Ansible role that uses a Molecule configuration (in this case the [Docker role from geerlingguy](https://github.com/geerlingguy/ansible-role-docker) and run `molecule test`:

```bash
git clone https://github.com/geerlingguy/ansible-role-docker.git geerlingguy.docker
cd  geerlingguy.docker
MOLECULE_DISTRO=ubuntu1804 molecule test
```


Further References
------------------

* https://www.jeffgeerling.com/blog/2018/testing-your-ansible-roles-molecule
* https://github.com/geerlingguy/ansible-role-docker
* https://molecule.readthedocs.io/en/stable/installation.html
* https://testinfra.readthedocs.io/en/latest/index.html
* https://docs.docker.com/install/linux/docker-ce/ubuntu/
* https://renaudmarti.net/posts/molecule-proxy-support/
