# Ansible basics

Have you ever had to deploy your application somewhere else than your local environment? Have you ever had to this manually? That can be quite painful, as you have so many steps to reproduce, so many things can go wrong and after all you need to check if you did everything right yourself. What if I tell you that there is a slick tool that can make all this much much easier? Say hello to Ansible.

### What is it?

Ansible is an automation tool that is built with Python and this first piece of information is very important. Thanks to Python's widespread popularity and the fact that it's preinstalled on most UNIX systems, using Ansible rarely requires any configuration on your destination servers. But let's not get ourselves too far ahead too quickly. First we need to understand how Ansible works.

The main idea is to execute some Python scripts on a remote machine, while having this configuration on a local one. That doesn't sound too complicated and indeed it's not. As you will see in a second, for many tasks that need to be executed, you just need to define the address of your server, provide some authentication details and see the world kneel before your power.

### Building blocks

To make basic use of Ansible powers you two basic elements: _inventory_ and _tasks_.

You need to start with an _inventory_ which is a definition of your (remote) host machines. in this file you not only provide the IP addresses, but also some information about authentication, like usernames, passwords (highly advised against) or paths to private keys, etc. You can also group your machines if for example you have separate application  and database servers, so that you might want to make different action with each group.

    # hosts
    [webserver]
    12.34.56.78 ansible_username=SOME_USERNAME ansible_ssh_private_key_file=PATH/TO/KEY

After creating your inventory, you need to understand what a _task_ is. A task is a task, and we may stop right here - it defines what action should be executed, alongside with some provided arguments. Actions are described with modules (and there are quite a lot of them), so most of things you'd ever need is provided out of the box.

### Your first call

After successfull install ([read more here](http://docs.ansible.com/ansible/intro_installation.html)), you are good to go.

Using Ansible is very easy, as all you you need to do is call it with your inventory file, server group and some command. To perform some action you need to know a name of the module (`-m`) and what arguments it needs (`-a`).

Your first step should always be calling `ping` command to see if the connection can be established at all:

    $ ansible webserver -i hosts -m ping
    12.34.56.78 | SUCCESS => {
        "changed": false,
        "ping": "pong"
    }


Running a custom command looks like this:

    $ ansible webserver -i hosts -m command -a uptime
    12.34.56.78 | SUCCESS | rc=0 >>
     6:12PM  up 12 days, 21:33, 0 users, load averages: 8.80, 11.38, 10.96

As you can see, we asked our host about its `uptime` and got the response with requested data and status (`SUCCESS`).

Sending files is just as easy, but this time we need to use `copy` module:

    $ ansible webserver -i hosts -m copy -a "src=test.txt dest=/PATH/TO/SOMEWHERE/"
    12.34.56.78 | SUCCESS => {
        "changed": true,
        "checksum": "da39a3ee5e6b4b0d3255bfef95601890afd80709",
        "dest": "/PATH/TO/SOMEWHERE/test.txt",
        "gid": 1000,
        "group": "1000",
        "md5sum": "d41d8cd98f00b204e9800998ecf8427e",
        "mode": "0644",
        "owner": "pawelslomka",
        "size": 0,
        "src": "/PATH/TO/.ansible/tmp/ansible-tmp-1469981873.75-48129555445953/source",
        "state": "file",
        "uid": 1109
    }

### Shared hosting

If your remote host is a shared hosting, you might not have the access to its system's default Python executable. If this is the case for you, you probably get an error like this:

    $ ansible webserver -i hosts -m ping
    12.34.56.78 | FAILED! => {
        "changed": false,
        "failed": true,
        "module_stderr": "",
        "module_stdout": "/usr/bin/python: not found\r\n",
        "msg": "MODULE FAILURE",
        "parsed": false
    }

The solution here is to log into the machine and check where is an executable Python you actually can access:

    remote$ which python
    /usr/local/bin/python

Having this information you need to add it to your inventory file:

    #[webserver]
    12.34.56.78 ansible_username=SOME_USERNAME ansible_ssh_private_key_file=PATH/TO/KEY ansible_python_interpreter=/usr/local/bin/python

Now calling `ping` should succeed:

    $ ansible webserver -i hosts -m ping
    12.34.56.78 | SUCCESS => {
        "changed": false,
        "ping": "pong"
    }

### Summary

This quick introduction should already show how Ansible can help you with automating your deployment process. But this is just a tiny bit of what the tool is capable of. You can create complicated playbooks, generating files from templates and using variables. Stay tuned!
