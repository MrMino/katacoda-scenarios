# Shelling apt commands is no more

A very standard operation when provisioning hosts, is to install packages for
them.

Ansible provides modules for different package managers, which you can use to
do just that.

Our playground uses `ubuntu:20.4` as its base image. Ubuntu uses Aptitude -
`apt` - for package management. Therefore, we'll use
[**apt**](https://docs.ansible.com/ansible/latest/modules/apt_module.html) to
automate it.

---

We'll need a few packages for our HTTP server. Let's install them via Ansible!

Open `my-playbook.yml`. Add a new play to it:

```
- name: Setup HTTP server
  hosts: production
  tasks:
      - name: Update apt cache and install Nginx
        apt: name=nginx state=present update-cache=true
```

Run the playbook using `ansible-playbook`:

```
ansible-playbook -i inventory.ini my-playbook.yml
```

# What happened?

We gave `update-cache=true` to apt module, therefore before installing any
packages, the module refreshed the cache.

We told apt, that it should install a package with a `name=nginx`.

`state=present` tells it, that after running the task, the package should be
installed. Another option would be, for example `state=absent`, which would
tell `apt` module, that it should ensure that this package is removed.

Notice, that there's now `changed=1` in Ansible output.

# Module idempotency

Remove the `Hello world` we had in our playbook earlier.

Run the playbook again. How the status line at the end changed?

That's right - now, there's `changed=0` - nothing changed. That's because the
package is already installed (the cache was refreshed, but nothing really
changed because it's too fresh).

This feature of Ansible is called **module idempotency**. Modules should
ensure, that if there's no action required, no action is performed.

That way, steps that need not be performed, will not be.

*Why did `shell` module always report a change?*

# Exercise

In `my-playbook.yml`, add a task that installs `git` package.
Run the playbook again.

<details>
<summary>
For convenience, this is how the playbook should look like after this step.
</summary>
<p>
```
---

- name: Setup HTTP server
  hosts: production
  tasks:
      - name: Update apt cache and install Nginx
        apt: name=nginx state=present update-cache=true
      - name: Update apt cache and install Git
        apt: name=git state=present update-cache=true
```

</p>
</details>

# More Ansible awaits those who push this button...
