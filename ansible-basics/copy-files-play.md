# File management without scp

In Ansible, there are multiple modules that can manage files on remote hosts.
There's
[**synchronize**](https://docs.ansible.com/ansible/latest/modules/synchronize_module.html)
and
[**copy**](https://docs.ansible.com/ansible/latest/modules/copy_module.html).
For basic operations,like changing permissions or removing things, there's
[**file**](https://docs.ansible.com/ansible/latest/modules/file_module.html).
Should you need to sync from a git repository,
[**git**](https://docs.ansible.com/ansible/latest/modules/git_module.html)
module has you covered.

In this step we'll cover the latter two.

---

After installing Nginx, the package created `/var/www/html` directory for us.
This the directory is where, by default, Nginx hosts a webpage from.

Default contents of this directory are unnecessary. Let's discard them using
file module:

```
      - name: Remove default www
        file:
            path: "/var/www/html/"
            state: absent
```

Now, let's use git module to pull a static webpage from a repository. We'll use
[**cloudcademy/static-website-example**](https://github.com/cloudacademy/static-website-example)
(which is an amazing template BTW) for this purpose.

Add the following to the playbook:

```
      - name: Sync www
        git:
            repo: https://github.com/mdn/beginner-html-site-styled.git
            dest: "/var/www/html/"
```

Run the playbook with... know what? Do you remember what was the command for it?
If you do, write it by hand. Or look at the previous step, if you need a hint.

---

Open port 80 on Host 1. Congratulations - you've managed to put a static web
page using Ansible!

# Hold on a minute...

We have 2 problems in our playbook right now:

1. We have two places where we specify path to `www`. Should the directory
   change for some reason (e.g. due to Nginx configuration), we'll need to
   change it in multple places, which is error prone.
2. We're removing the default `www` directory every time we run the playbook.
   We need to remove it after installation of Nginx, because git won't sync
   into existing, non-git directory, but we shouldn't do it every time we run
   the playbook. A better solution would be to run it only if we actually
   installed Nginx. *Run the playbook again to see the removal step do it's
   thing again.*

*Note on #2: Well... it would actually be even better to do an error recovery
if git sync fails, that removes the directory as a recovery step, but it's out
of scope for this tutorial. The solution in this tutorial comes with a large
issue, that would make git module fail in certain cases. Can you spot it?*

# Variables

Let's deal with #1 first. In Ansible, you can specify
[**variables**](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html)
in your playbook. Let's do that to remove duplication of `/var/www/html/`
path.

Add the following lines below `hosts: production`:

```
  vars:
      www_path: "/var/www/html/"
```

Congratulations! You've just specified a `www_path` variable, and set its value
to `/var/www/html/`! Now, let's use it.

Change the following two lines in `Remove default www` and `Sync www` tasks:

```
path: "/var/www/html/" ->  path: "{{www_path}}"
dest: "/var/www/html/" ->  dest: "{{www_path}}"
```

That's it! We've now moved the path to our webpage files into a variable.

*Note for the curious kind: Ansible uses Jinja2 templating for variables. These
have a looooot of additional functionality. See
[docs](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#transforming-variables-with-jinja2-filters)*

# The "when" statement

Sometimes you may want to add conditions to your playbooks. This is where
[**conditionals**](https://docs.ansible.com/ansible/latest/user_guide/playbooks_conditionals.html)
come into play (pun intended).

In our case, we wan't to skip a task (removing default `www` directory), if
Nginx was already on the host (we ran the playbook previously).

We can use
[**when**](https://docs.ansible.com/ansible/latest/user_guide/playbooks_conditionals.html#the-when-statement)
statement for that.

We'll register whether the Nginx installation step changed something. Only if
it did, we'll remove `www` directory.

Add the following to Nginx reinstallation step:

```
        register: nginx_installed
```

Now, add the following `when` statement to "Remove default www" task:

```
        when: nginx_installed is changed
```

This tells Ansible to run this task *only* if the task that installed Nginx
changed something.

Run the playbook. Did the removal step run this time?

Let's trigger a change in Nginx installation task:

```
ssh root@localhost -p 12345 -C 'apt remove nginx'
```

Run the playbook again. The removal step should run this time.



---

# Aaaand scene...
