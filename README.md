# Software Factory Adidas
======

###### /!\ *THIS IS STILL WIP BUT PLEASE READ THE DOC DESIGN TO HAVE AN IDEA OF WHAT HAVE BEEN IMPLEMENTED SO FAR*

### Subject
------
As you might already now, we mainly develop on Java so we would like to propose you a challenge to be solved.

#### Question is easy:

Can you design and implement a CI/CD environment for the code placed at  https://github.com/guipal/apiSampleJava ?
You just need to fork this repository and wrap around whatever ci/cd solution available in the market.

#### Just some hints:

+ Think about efficiency
+ We love to test things before going into production
+ Did we mentioned we love to test in isolated environments?
+ Build once run anywhere
+ How easy would it be to check what is going on in your environment?
+ We want to see what would be your workflow for a new feature development in your environment from committing into the svc till running in production.
+ Everything as code is our MOTO

### Doc Design
------

In order to introduce you the solution exposed I've done a doc design. Please Read it, it contains all answers
[doc design link](./doc-design.md)

### Automation of the software factory
------

We want the software factory to automate all our task so of course It'll be automated itself. How ?
We use Ansible to configure our servers and docker compose to deploy all our stack.
It's not perfect because right now all ip are hardcoded, we don't use domain name, we could have a dynamic inventory connecting to scaleway's api or even better using a Kubernetes cluster to deploy all our application, however we want to keep it simple for the poc and we will only use static VMs that will be configured by Ansible and docker-compose deploy applications.

### Additional note
------



### ToDo list
------

+ Clean Ansible and install right docker-compose.yml
+ Plug runner <- Installed not plugged
+ Write gitlab-ci.yml <- not yet
+ Finish documentation almost done
