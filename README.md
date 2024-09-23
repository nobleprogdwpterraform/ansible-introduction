# Deploying the Virtual Machines

Do all the following from your favourite terminal application

1. Clone this repository

    ```bash
    git clone https://github.com/nobleprogdwpterraform/ansible-introduction.git
    ```

1. Install Oracle Virtual box (optional if you created/cloned your DaDesktop from trainer desktop)
https://www.virtualbox.org/wiki/Downloads

1. Install vagrant (optional if you crearted/cloned your DaDesktop from trainer desktop)
https://developer.hashicorp.com/vagrant/install

1. Install Visual Studio Code IDE (optional if you created/cloned your DaDesktop from trainer desktop)
https://code.visualstudio.com/download

1. Start the virtual machines.

    ```bash
    cd vagrant
    vagrant up
    ```

    When the process completes successfully, it will display the names of the VMs and their IP addresses on the virtual network

    ```text
    192.168.56.11 target1
    192.168.56.12 target2
    ```

    Note that the login credentials for all these machines is

    ```
    Username: vagrant
    Password: vagrant
    ```

4. Test logging in.

    1. Add target1 and target2 entries in hosts
         ssh-keygen -f "/home/student/.ssh/known_hosts" -R "target1"
        ssh-keygen -f "/home/student/.ssh/known_hosts" -R "target2"

        ```
        sudo vi /etc/hosts
        Add below lines at the bottom
        192.168.56.12 target2
        192.168.56.11 target1
        ```

        This login is password-less so you should get directly to the CentOS prompt.

    1. Test the controller can connect to the target machine

        ```
        ssh vagrant@target1
        enter password vagrant
        ssh vagrant@target2
        enter password vagrant
        ```

        You will be asked for the password this time, which is the password above.

    1. Enter `exit` command twice to retun to laptop command prompt.


# Install Ansible


## Install Ansible on Ubuntu VM
$ sudo apt update
$ sudo apt install software-properties-common
$ sudo add-apt-repository --yes --update ppa:ansible/ansible
$ sudo apt install ansible

## Test

Now we will make a small test project

1. Create folder for the project

    ```bash
    mkdir test-project
    cd test-project
    ```

1. Create an inventory file for the target machine `target1`, providing the password to log into it

    ```bash
    echo "target1 ansible_user=vagrant ansible_ssh_pass=vagrant" > inventory.txt
    ```

1. Test connectivity

    We can now run a connectivity test by using a module called `ping`. Run the ansible command specifying the target machine, the ping module and the inventory file

    ```bash
    ansible target1 -m ping -i inventory.txt
    ```

    You should see an ouput like this

    ```
    target1 | SUCCESS => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python3"
        },
        "changed": false,
        "ping": "pong"
    ```

    If you get an error like the following, it means that you did not test the login from `ansiblecontroller` to `target1` and Ansible is not able to do the key validation.

    > Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.

    If you see this, then first log into the target machine and answer `yes` to the question, then enter the password...

    ```
    [vagrant@ansiblecontroller test-project]$ ssh target1
    The authenticity of host 'target1 (192.168.56.12)' can't be established.
    ED25519 key fingerprint is SHA256:1s1Ud+oggJCN32n1bdoJjgqeov0cSN4H44uQv+o5lJQ.
    This key is not known by any other names
    Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
    Warning: Permanently added 'target1' (ED25519) to the list of known hosts.
    vagrant@target1's password:
    Last login: Wed Jul 31 06:26:54 2024 from 10.0.2.2
    [vagrant@target1 ~]$ exit
    logout
    Connection to target1 closed.
    ```

    Retry the ansible command. It should now work.

