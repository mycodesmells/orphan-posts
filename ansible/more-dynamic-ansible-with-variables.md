# More Dynamic Ansible With Variables

Creating an Ansible playbook is quite easy, but so far all steps we used were set in stone. Can we make them a bit more dynamic? It's time to spice things up and introduce variables to make our scripts much cooler.

### Why we need variables?

Having your scenario automated with Ansible is already a huge improvement over executing it manually, but there is still something missing. In the previous post we created a playbook that fetched some repository, install npm dependencies, copied some configuration file and restarted the server. What was irritating was that since all paths have to be defined as absolute ones, we end up repeating our root path again and again.

Another important issue is storing passwords. You should never ever store passwords as a plain text in your repository, remember? But at the same time you may want to keep your provisioning scripts to share it among fellow developers. The solution is to keep your secret data as a separate variables file and have it ignored by your repository. Then each member of a project can create it at their own and start using the playbook.

### Defining variables

In order to define your variables all you need to do is create a YAML file:

    # vars.yml
    root: /path/to/project
    repo: https://github.com/mycodesmells/simple-nodejs-example

As you can see, we extracted a root path from the playbook, as well as repository location. Now we can replace those values with appropriate variable name wrapped in `{{ }}`:

    # playbook.yml
    ...
      tasks:
      - name: Remove node_modules
        file: path={{root_path}}/node_modules state=absent
      - name: Checkout git repository
        git: repo={{repo}} dest={{root_path}} version=master
      - name: Install npm dependencies
        npm: path={{root_path}}
      - name: Copy configuratio file
        copy: src=/home/user/project/secret.json dest={{root_path}}/secret.json
      - name: Restart the application
        shell: {{root_path}}s/restart-project.sh

### Usage

What now? How do we bind all this together? This is the simplest step, as all we need to do is add another property in `playbook.yml` called `vars_files`. Our complete playbook file should look like this:

    - hosts: webserver
      vars_files:
      - vars.yml
      gather_facts: False
      tasks:
      - name: Remove node_modules
        file: path={{root_path}}/node_modules state=absent
      - name: Checkout git repository
        git: repo={{repo}} dest={{root_path}} version=master
      - name: Install npm dependencies
        npm: path={{root_path}}
      - name: Copy configuration file
        copy: src=/home/user/project/secret.json dest={{root_path}}/secret.json
      - name: Restart the application
        shell: "{{root_path}}/restart-project.sh"

*Note* that if some value (such as an argument to `shell` module in the last task) starts with a variable, it must be in quotes(`""`).

Running our refreshed playbook should result in an output similar to this:

    $ ansible-playbook -i hosts playbook.yml

    PLAY [webserver] ***************************************************************

    TASK [Remove node_modules] *****************************************************
    changed: [12.34.56.78]

    TASK [Checkout git repository] *************************************************
    ok: [12.34.56.78]

    TASK [Install npm dependencies] ************************************************
    changed: [12.34.56.78]

    TASK [Copy configuration file] **************************************************
    ok: [12.34.56.78]

    TASK [Restart the application] *************************************************
    changed: [12.34.56.78]

    PLAY RECAP *********************************************************************
    12.34.56.78             : ok=5    changed=3    unreachable=0    failed=0

As you can see, enhancing our playbooks with variables made our playbooks a bit simpler, and we are talking about as simple example as you can imagine. What if you needed to fetch multiple repositories, juggle dozens of paths and so on? Plus, with variables you can quickly create another playbook that will deploy our application to different path, etc.

Next step is creating configuration files by merging templates and variables. Stay tuned!
