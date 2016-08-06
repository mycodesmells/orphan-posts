# More Dynamic Ansible With Variables

Creating an Ansible playbook is quite easy, but so far all steps we used were set in stone. Can we make them a bit more dynamic? It's time to spice things up and introduce variables to make our scripts much cooler.

### Why we need variables?

Having your scenario automated with Ansible is already a huge improvement over executing it manually, but there is still something missing. In the previous post we created a playbook that fetched some repository, install npm dependencies, copied some configuration file and restarted the server. What was irritating was that since all paths have to be defined as absolute ones, we end up repeating our root path again and again.

Another important issue is storing passwords. You should never ever store passwords as a plain text in your repository, remember? But at the same time you may want to keep your provisioning scripts to share it among fellow developers. The solution is to keep your secret data as a separate variables file and have it ignored by your repository. Then each member of a project can create it at their own and start using the playbook.

### Defining variables

In order to define your variables all you need to do is create a YAML file:

    # vars.yml


### Usage

TODO:
- Intro
- why you need variables?
    - avoid duplication
    - store sensitive data in separate file
    - inject values into tasks
- how to define?
    - inline declarations
    - YML files
- how to use?
    - inline,
    - include
- benefits
    - multiple playbooks based on the same template