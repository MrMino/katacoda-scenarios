# Have your hosts in one place

Ansible inventory is a file that specifies the hosts that we will automate.
There are two possible formats for it: YAML and INI. We'll use INI, as it's
easier to edit.

You can read up about different inventory formats [here](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html#inventory-basics-formats-hosts-and-groups)

## Our inventory

In the editor, open the file `inventory.ini`.

 * `[example-group]` - this line specifies a **group**. Groups are a way of
   putting hosts together. This way, you can run your automation at many of
   them at once.

   There are two special groups used by Ansible: `all` and `ungrouped`. You've
   seen example usage of the former in the previous step. `ungrouped` means
   "every host that is in no other group than all".

 * `this.is.my.host.com` - this is how you normally specify a
   host. The default port is 22 (SSH).

 * `my-alias ansible_host=some.host.com ansible_port=123` - this example shows
   the usage of host aliases and inventory variables.
   
   Variables are a way of specifying additional information about hosts.
   The `ansible_host` and `ansible_port` are special ones (see
   [docs](https://docs.ansible.com/ansible/latest/reference_appendices/special_variables.html#connection-variables)).
   They specify the address for SSH connection, which Ansible will use.

   By using a host alias, you supply a different name for your host, which you
   can then use as an argument to `ansible` command (e.g. `ansible -i
   'inventory.ini' -m ping my-alias`).

   If you use an alias, you also have to use `ansible_host` parameter.


Let's check how the inventory works, by issuing a few Ansible commands:

```
ansible -i inventory.ini -m ping all
ansible -i inventory.ini -m ping example-group
ansible -i inventory.ini -m ping my-alias
```

## Add your playground to the inventory

Let's add our container to the inventory, so that we don't have to specify it
every time.

We'll put it into `production` group, alias it as `webapp`, set it's address,
and tell Ansible to connect to it via `root` user.

Add the following to `inventory.ini`:

```
[production]
webapp ansible_host=127.0.0.1 ansible_port=12345 ansible_user=root
```

Let's use another module,
[`setup`](https://docs.ansible.com/ansible/latest/modules/setup_module.html),
to see some basic information about our playground.

```
ansible -i inventory.ini -m setup webapp
```

<details>
<summary>Click here, to see how your inventory should look like now.</summary>
<p>
```
[example-group]
this.is.my.host.com
my-alias ansible_host=some.host.com ansible_port=123

[production]
webapp ansible_host=127.0.0.1 ansible_port=12345 ansible_user=root
```
</p>
</details>
