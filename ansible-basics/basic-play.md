# The fun starts here

In the editor, open file `hellow-world.yml`.

This is a basic Ansible **playbook**. A playbook is a list of **plays** that
run on a set of hosts.

A play is a list of **tasks**.

The order of execution follows the ordering in the file.

Playbooks are defined using YAML format.

---

Let's examine the playbook contents.

 * `---` - this is something specific to YAML itself, and has nothing to do
   with Ansible. In YAML, three dashes separate "directives" from document
   "contents".
 * `- name: Hello world` - the name of the play, for documentary / output
   purposes. This playbook contains only one play, named "Hello world".
 * `  hosts: production` - inventory pattern. The one used here says "run this
   on all hosts in production group".
 * `tasks:` - the list of tasks to execute in this play. Tasks are executed in
   the order they are specified in this list.

Right now, there's a single task in `Hello world` play: 

```
    - name: Echo-out the message on the remote
      shell: echo "Hello world!"
```

This task uses `shell` module to execute a command in the shell.
The name of a task should describe what it does, although it is optional.

This task shows an example usage of the **shell** module, which you can read
about
[here](https://docs.ansible.com/ansible/latest/modules/shell_module.html).

To execute a playbook, use `ansible-playbook`:

```
ansible-playbook -i inventory.ini hello-world.yml
```

# Where's my output?

In the example above, Ansible executed the command, but didn't show it's output
to us.

The `shell` module returns a few useful variables to us (see
[shell module docs](https://docs.ansible.com/ansible/latest/modules/shell_module.html#return-values)),
we just need to access them and print them out.

That's where `register` keyword comes in.

# Register the output

Add the following line at the end of the task:

```
      register: my_task_return_data
```

This tells Ansible, that it should remember values returned by the task in a
variable called `my_task_return_data`.

# Show it!

Next, add the following task at the end of your playbook:

```
    - debug: var=my_task_return_data.stdout
```

This is an example of usage of
[debug](https://docs.ansible.com/ansible/latest/modules/debug_module.html)
module, which you can use to print arbitrary output or variables.

We take the data registered in the previous task, and print `stdout` field from
it.

Note: in this task we omit the name, and use `key=value` notation, instead of:

```
key:
    value
```

<details>
<summary>
For convenience, this is how the finished playbook should look like.
</summary>
<p>
```
---

- name: Hello world
  hosts: production
  tasks:
    - name: Echo-out the message on the remote
      shell: echo "Hello world!"
      register: my_task_return_data
    - debug: var=my_task_return_data.stdout
```

</p>
</details>

# But there's more!
