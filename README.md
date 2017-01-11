# KernelCi Builder Config

Ansible-based configuration for Linux kernel builders.

Some necessary files/directories are missing from this repository since they
contain secrets. In order to make the ansible commands work, those files are
necessary and need to be linked in the repository.

The host names in the [hosts](./hosts) file are define and used only internally
for the ansible configurations. The real host names and/or their IP addresses
are stored in either the `group_vars/` or `host_vars/` directory.

## Run the Configuration

To just go through all the tasks, useful to setup a new builder:

    ansible-playbook -i hosts site.yml

This assumes that you have configured the `host_vars/` and `group_vars/`
directories with all the necessary parameters from the [builder-config-data](https://github.com/kernelci/builder-config-data) repository.

### Run only some specific tasks

If you need to run only a subset of all the tasks, you can limit the ones to run
using the available tags:

    ansible-playbook -i hosts site.yml --tags=$TAG,$TAG

For example:

    ansible-playbook -i hosts site.yml --tags=mips,mirror

### Run only on some builders

If you want to run the tasks only on a subset of the builders, you can limit
which one ansible will access:

    ansible-playbook -i hosts site.yml -l $HOST_NAME

For example:

    ansible-playbook -i hosts site.yml -l kbuilder6,kbuilder7

## Other Useful Commands

To check the [facts](http://docs.ansible.com/ansible/setup_module.html) on a host:

    ansible -m setup -i hosts $HOST_GROUP_NAME

### Running a simple command remotely

If you need to run a simple one-time command on the managed hosts:

    ansible -i hosts -m raw -a "$SHELL_COMMAND" $HOST_GROUP_NAME

For example:

    ansible -i hosts -m raw -a "rm -rf /srv/mirrors" linaro
    ansible -i hosts -m raw -a "rm -rf /srv/mirrors" linaro -l kbuilder6,kbuilder7

The latter is limited to `kbuilder6` and `kbuilder7` only.

Other examples:

    ansible -i hosts -m apt -a "name=$PACKAGE_NAME" linaro # Installs a single package across all "linaro" hosts

The `-m` switch enables to use a single ansible module from all the
[available ones](http://docs.ansible.com/ansible/list_of_all_modules.html).

### Notes

* The `$HOST_GROUP_NAME` name is the name given, between square brackets, in
the [hosts](./hosts) file to a group of hosts (i.e. `[linaro]`).
