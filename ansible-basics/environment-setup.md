# First things first...

Let's start by installing everything that we'll require in this session.

Right now, you have an Ubuntu terminal opened.

 1. Run aptitude update

    Let's make sure, that whatever we install is up to date. This isn't
    necessarily required, but is a good practice.

    ```
    sudo apt update
    ```

 2. Install Ansible

    There are at least two ways of installing Ansible, that are available for
    us right now:

    * Using distribution's package manager
    * Using pip - *why this one might be better?*

    For convenience, let's install it with `apt` (Ubuntu's package manager).

    ```
    sudo apt install ansible
    ```

 3. Install Docker... *not*?

    Actually, in this environement, docker is already installed.
    Should you need to install it in Ubuntu 20.04, the commands are as follows:

    ```
    sudo apt install docker.io
    sudo systemctl start docker
    ```

    **NOTE: the name here is `docker.io`, not `docker`. The later is a totally
    separate project, the name of which predates "Docker", the container
    management tool.**

    We will use it to set up a playground that we can test things on, without
    worrying about changing our OS.

 4. Add an SSH key for the node

    We need a way of connecting to our playground via SSH. To make this process
    cleaner, let's generate an SSH key. This key will be automatically embedded
    into the image upon building it (see the `Dockerfile` in the editor above).
    **When asked for a pasphrase by `ssh-keygen`, push <kbd>Enter</kbd> twice.**

    ```
    ssh-keygen -t rsa -b 4096 -f ./id_rsa -C "root@ansible-node"
    ```

 5. Build the docker image we'll use to set up the playground

    The image provided will be mine, although it's trivial to create your own.

    Let's build it first...

    ```
    docker build --tag ansible-node .
    ```

    This image contains dependencies required by Ansible, so that it can manage
    it (mainly OpenSSH and Python3).
    You can look up the recipe for the image in `Dockerfile`

 6. Start the node with correct ports exposed

    Let's start up the image.

    ```
    docker run -p 12345:12345 -p 80:80 -it ansible-node
    ```

    Arguments explained:
    * `docker run` - run an image stored locally (`docker images` to list the
      images)
    * `-p container:host` - bind port on *container* to the port on the *host*
      on which you're running it
    * `-i` - run interactively, i.e. keep STDIN open even if if it's not
      attached - this is required, so that <kbd>ctrl</kbd>+<kbd>c</kbd> works
      as expected
    * `-t` - allocate a pseudo-TTY - again, not really required, but without it
      there's nothing that parses keyboard events
    * `ansible-node` - name of the image for the container.

 7. Test connection

    Let's test the container set up by issuing a command via SSH on it.
    * Open another terminal window (+ sign above the current one -> "Open New
      Terminal").
    * Start an SSH agent. Add the SSH key we created in previous step to it, so
      that we don't need to specify it every time.

        ```
        eval $(ssh-agent)
        ssh-add id_rsa
        ```

    * Issue the following command to test it:

        ```
        ssh root@localhost -p 12345 -C "echo I\'m alive\!"
        ```

    You should see "I'm alive!" in the output.

### Now, we're ready to go!
