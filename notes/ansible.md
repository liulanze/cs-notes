# Ansible
- What is Ansible (YAML)?
  - A tool to automate IT tasks, which can reduce the tedious operation like
    manually ssh to many hosts to do the same setup or tasks, instead, use one
    yaml file to achieve by the control machine.
- What Ansible can do:
  - Execute tasks from your own machine.
  - Configuration / installation / deployment steps in a single yaml file.
  - Re-use same file multiple times and for different environments.
- Module
  - is one small specific task.
  - get pushed to the target server & do their work and get removed.
  - can be:
    - create or copy file
    - start container
    - install nginx server
    - start nginx server
    - create a cloud instance
- Ansible Playbook
  - Manage module
  - Where should these tasks execute?
    - HOSTS
  - With which user should the tasks execute?
    - REMOTE_USER
  - HOSTS and REMOTE_USER can be defined in Hosts file.
- Inventory: all the machines involved in task executions.
  - One playbook includes:
    - which tasks
    - which hosts
    - which users
- Playbook (yaml file) includes 1 or more plays. play is somwhow similar to the
  task.