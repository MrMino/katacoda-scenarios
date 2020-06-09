# Few useful commands to get you started

* Check version information

    ```
    ansible --version
    ```

* Ping your playground

    ```
    ansible all -m ping -i "localhost:12345,"
    ```

    * `all` - a [**pattern**](https://docs.ansible.com/ansible/latest/user_guide/intro_patterns.html) argument for the inventory, which we'll create in the
    next step. `all` means "do for every host in the inventory".
    * `-m ping` - Ansible module to use
    * `-i "localhost:12345,"` - inventory host list

        **Mind the comma!** - without it, the argument specifies a path to
        inventory file.

    Check what happens, if you supply a wrong port number.
