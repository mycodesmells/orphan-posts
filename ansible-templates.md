# Ansible Templates

After playing with Ansible commands and creating a simple playbook, it's time to take another step, this time with templates. This is a very easy concept, so it's absolutely crucial you add it to your arsenal next time you automate your work.

### Copying vs Templating

The most classic example of a file that should be generated from a template is any type of configuration. You basically have its raw version and want to fill it with some real data so that your application works in a certain way. For example, you can keep information about a MongoDB connection details:

    # configuration.yaml
    username: databaseUser
    password: SECRET_PASSWORD
    host: databaseHost
    name: databaseName

Now what if your configuration file has dozens of other properties, but your application is deployed to three environments (eg. _development_, _test_ and _production_), you may want to have some of them have the same value all the time, others (eg. that DB config) needs to differ for each of them.

One solution would be to have multiple, separate copies of the same configuration with a couple of specific fields changed. But this feels like a huge amount of duplication, right?

### Template module

Instead of copying, we can define a task that will generate a file (which is automatically moved to a remote location) based on our template file and variables. First, we need to create a template:

    # configuration.yaml.j2
    ...
    username: {{ database_user }}
    password: {{ database_pass }}
    host: {{ database_host }}
    name: {{ database_name }}
    ...

Then we need to make sure that those variables are stored in a file (that we'll inject into a playbook):

    # vars.yaml
    database_user: databaseUser
    database_pass: SECRET_PASSWORD
    database_host: databaseHost
    database_name: databaseName

Finally, we can use it in our playbook:

    # playbook.yaml
    - hosts: webserver
      vars_files:
      - vars.yaml
      gather_facts: False
      tasks:
      ...
      - name: Create a configuration file from template
        template: src=local/path/to/configuration.yaml.j2 dest=remote/path/to/configuration.yaml
      ...

And that's it! Could it be any easier? Anyway, storing our password as a plain text is a huge security issue, which we will tackle next time. Stay tuned!
