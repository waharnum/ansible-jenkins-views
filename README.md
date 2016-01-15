Role Name
=========

Creates and loads basic regex-based view configurations to a Jenkins server.

Covers only the following at the moment:
- List Views
- Nested Views (with the Nested Views plugin)

This is pretty much a task-oriented role right now that exists to avoid having to do a lot of manual work managing views.

Requirements
------------

- Jenkins on the server to load the views to
- a Jenkins username/password that can administer views

Role Variables
--------------

See defaults/main.yml for what's configurable.

In the current state, you'll likely want to prompt for `jenkins_user` and `jenkins_password` as part of the playbook.

Dependencies
------------

The Jenkins server must have the Nested Views plugin installed if you wish to use Nested Views.

Example Playbook
----------------

```
- hosts: jenkins
  remote_user: vagrant
  become: yes

  vars_prompt:
    - name: jenkins_user
      prompt: "Enter Jenkins user name"

    - name: jenkins_password
      prompt: "Enter Jenkins password"
      private: yes

  roles:
    - role: jenkins-views
      jenkins_nested_views:
        - name: "Container Jobs"
          child_views:
            - name: "Build Containers"
              regex: ".*build-container.*"
            - name: "Deploy Containers"
              regex: ".*deploy-container.*"
        - name: "Admin Jobs"
          description: "Administrative jobs for doing administrative things"
          child_views:
            - name: "Admin Jobs"
              regex: ".*admin.*"
        - name: "Websites"
          description: "Various website jobs"
          child_views:
            - name: "IDRC"
              description: "idrc.ocad sites"
              regex: ".*idrc\\.ocad.*"
            - name: "Floe"
              regex: ".*floeproject\\.org.*"
      jenkins_global_views:
        - name: "All Container Jobs"
          description: "All container jobs"
          regex: ".*container.*"
        - name: "All Non-Container Jobs"
          regex: "(?!.*container).*"
```

License
-------

MIT
