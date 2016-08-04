# Playing with Ansible Playbooks

As you could have seen in the [previous post](http://mycodesmells.com/post/ansible-basics/), playing with Ansible is pretty easy, but powerful at the same time. But do you have to run each and every command by itself? Naturally there is a possibility of having all tasks executed in a batch - with playbooks.

When running each command separately you might as well log into the remote host and run the commands manually, right? You still need to remember what steps you need, what is their order and so on. One missed step may lead to a catastrophe. This is what Ansible playbooks are for - you can define all steps in one place and start the execution with a single command.

### Sample playbook

Imagine that you are working on a Node JS application and want to deploy in on your server. The steps you need to perform are:

- remove `node_modules` directory (to always have a clear, CI-like environment)
- pull the latest changes from remote repository (always keep your code in some repo!)
- install dependencies defined in `package.json`
- copy configuration file with eg. database passwords from local filesystem (never commit passwords to the repo!)
- start the application (if not started automatically)

Now the idea is to describe all those actions with Ansible tasks, that is with some modules and their parameters. First, we need to define what host should the playbook be exectuted on:

    - hosts: webserver
    ...

*Side note* - If your host has some kind of a specific configuration (happened to me with some shared-hosting service), Ansible can fail on a step called _gathering facts_. If so, you need to specify

    - hosts: webserver
      gather_facts: False
    ...

Now let's define our tasks one by one.

### Task with modules

We define as a list of steps in a YAML file. You can limit yourself to just calling specific modules, but it is highly recommended to add `name` field, so that it's easier to know what is going on during execution. Our steps are:

1. Removing files: `file` module


    - name: Remove node_modules
      file: path=/path/to/project/node_modules state=absent


2. Pulling changes from repo: `git` module


    - name: Checkout git repository
      git: repo=https://github.com/mycodesmells/simple-nodejs-example dest=/path/to/project version=master


3. Installing dependencies: `npm` module


    - name: Install npm dependencies
      npm: path=/path/to/project


4. Copying configuration file: `copy` module


    - name: Copy configuratio file
      copy: src=/home/user/project/config/secret.json dest=/path/to/project/config/secret.json


5. Starting application: `command` module in my case


    - name: Restart the application
      shell: /path/to/restart-project.sh


### Running

In the end, our playbooks should look like this

    playbook code

We can run it (remmeber about existing `hosts` file with the definition of your servers) with `ansible-playbook` command:

    - hosts: webserver
      vars_files:
      - vars/common.yaml
      - vars/dev.yaml
      gather_facts: False
      tasks:
      - name: Remove node_modules
        file: path=/path/to/project/node_modules state=absent
      - name: Checkout git repository
        git: repo=https://github.com/mycodesmells/simple-nodejs-example dest=/path/to/project version=master
      - name: Install npm dependencies
        npm: path=/path/to/project
      - name: Copy configuratio file
        copy: src=/home/user/project/config/secret.json dest=/path/to/project/config/secret.json
      - name: Restart the application
        shell: /path/to/restart-project.sh

If we configured everything correctly, our results should like similar to this:

    $ ansible-playbook -i hosts playbook.yml
    
    PLAY [webserver] ************************************************************** 
    
    TASK: [Remove node_modules] *************************************************** 
    changed: [12.34.56.78]
    
    TASK: [Checkout git repository] *********************************************** 
    ok: [12.34.56.78]
    
    TASK: [Install npm dependencies] ********************************************** 
    changed: [12.34.56.78]
    
    TASK: [Copy configuratio file] ************************************************ 
    changed: [12.34.56.78]
    
    TASK: [Restart the application] *********************************************** 
    changed: [12.34.56.78]
    
    PLAY RECAP ******************************************************************** 
    12.34.56.78             : ok=5    changed=4    unreachable=0    failed=0   

### Summary

As you can see, we already extended the range of things we can do with Ansible, just by putting all tasks in a single file. But this is still far from over, as we can enhance our playbooks with variables so that we can easily create multiple similar playbooks just by replacing variables values. Stay tuned!
