# Systemd be tamed

In the last step, we've installed Nginx, but it's not running in our playground
yet. Setting Nginx up is simple - we just need to tell SystemD to start the
service.

We'll use
[**service**](https://docs.ansible.com/ansible/latest/modules/service_module.html)
module for that.

---

Open `my-playbook.yml`. Add the following task at the end of the `Setup HTTP
Server` play:

```
      - name: Start Nginx
        service:
            name: nginx
            state: started
```

**Can you explain what this task does?**

Run the playbook using:

```
ansible-playbook -i inventory.ini my-playbook.yml
```

Now, let's open port 80 of our playground. Click on the "+" sign above
terminal, and choose "View HTTP port 80 on Host 1".

You should see Nginx "It's working!" page.


<details>
<summary>
After this step, the playbook should look like this.
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
      - name: Start Nginx
        service:
            name: nginx
            state: started
```

</p>
</details>
