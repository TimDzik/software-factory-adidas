# Software Factory Adidas ~ Doc Design
======

## Statement (scope and context)


##### We are willing to create a new whole CD. Why? What are the problems we are trying to solve?
---
In a nutshell we're trying to solve deployment of compute product. Why ?


- Consistency, we are trying to unify the CD
- Automation
- Speed of deployment and test
- More flexibility
- Trigger deployment through UI ==. Anyone can deploy.
- Complicated to follow deployment (spam in #scaleway-cd channel)
- Rollback is complicated we want to leverage that problem
Brief description of the background of the situation, the context and the
scope of the design document.

#### Why should we solve these problems?
---

We want to increase team's velocity and leverage all problems listed above.

## Objective


The end result is to have a fully consistent backuped, HA, harmonized,resilient CD.



### Solution/Design
------

In order to solve that CI/CD problem we are going to use a full CI/CD stack. In order to not overkill the solution we will implement an easy solution but in this doc design we will describe a full solution. That would require more time and a more complex architecture. 

Here is the ideal CI/CD system (in order to not be messy we've not display the links between the nexus and the different environements).
![Ideal CI-CD](/Users/tim/Documents/ideal-ci-cd-schema.png) 

### Differents components: 

#### AWX 
_______

AWX is a web-based solution that makes Ansible even more easy to use for IT teams of all kinds. It’s designed to be the hub for all of our automation tasks.

##### How AWX works

AWX is built to run on top of the Ansible project, enhancing the already powerful automation engine. AWX adds a web-based user interface, job scheduling, inventory management, reporting, workflow automation, credential sharing, and tooling to enable delegation.

##### A Gitlab repository with playbook (Not mandatory but good practise)

In this gitlab repository we will have roles that cvan be share through the whole company to not reinvent the wheel every time we want to deploy a new application. Very useful to gather all companies knowledges/stacks/infos.

###### Delegate

Central to AWX is the ability to create users, and group them into teams. You can then assign access and rules to inventory, credentials, and playbooks at an individual level or team level. This makes it possible to setup push-button access to complex automation, and control who can use it, and where they can run it.

###### Access Reporting

Now that you have users, you’ll need tools for tracking playbook runs and troubleshooting problems. AWX adds a custom callback plugin to Ansible playbook runs that captures event and output data in real time. A high-level summary of the stored output is presented on a dashboard, providing an overview of job executions, failures, and successes, as well as a breakdown of inventory successes and failures. You’ll immediately know what playbooks have run and any trouble spots needing attention, and you can immediately click into the details.

###### Schedule Workflow

You’ll bring together credentials, playbooks and inventory by creating a job template. The template represents the command line execution of ‘ansible-playbook,’ except you no longer need to touch the command line. Instead, you can run the job template on demand, and watch real-time playbook output in your web browser. Or, schedule it to run later, or even on a recurring basis, and get full access to the output whenever you need it.

###### Automate Through an API

At the heart of AWX is a powerful restful API. In fact, the user interface communicates with the backend entirely through the API, demonstrating its power and completeness. Anything you can do through the web browser, you can affect through API calls.


#### Nexus
_______

Definition of Google

#### Gitlab (Gitlab-ci)
_______

Definition od google

#### Kubernetes
_______

Defintion of google

#### Differents env
_______

* Tmp runners

Runners are temporary environments were you can launch the entire stack. As we are using containers and privates/public images, It will be easy to launch the entire stack. Of course If we have a complex microservice or a big monolith, It can be a bit long to launch the entire stack, however you'll be able to have end to end test. 
In the implemented design It's only Vm launching docker containers (that inside launch your stack) but in the ideal design It's linked to a K8s cluster that allows you to launch as much runners as you want. Of course using "static" VM you'll need to limit the concurrency of jobs running on the runner (If you link a VM/Server gitlab CI It will launch a container or a bash process, and you can set how much process/container you launch in parrallel, that's something you pick after benchmarking the capacity of your runners according to the project running, that's why using k8s is a good thing as you don't have to worty about how many jobs you can run in parrallel because It's horizontally scalable)

* Staging 

Staging is a fix environment that you can access, the goal of that environment is to run end to end/appplciative/unit/integration test, we want to make sure we've been tested eveything on that environment beforedeploying to production. We can also run front test using tools like celenium, or having manual process like checkout processes that can't be 100% automated.


* Production 

Production, the holy land, where you deploy (with no downtime) when the code developed by dev passed all test and all environments. 

Of course this is only the simple gitflow enviroment, but we are open to have a more complex environment, and that's something our current system supports. (Having recets, gatling test environments, chaos monkey ?.. )

#### Monitoring
_______

What is an awesome CI/CD environments without monitoring ? (Spoiler nothing) in order to follow our CI/CD worflow we will use some community tools :

- Prometheus

*DEFINITION*
Prometheus will be able to scrape all metrics from the differents environements/components of the CI/CD process.
eg: Scrapping cadvisor workers that retrieves health of the differents runners runnign on the machine

- Grafana

Grafana is a simple tool that you can use to display differents metrics pPrometheus has been gathering on the different machine, example showing you the CPU infos for the last 48 hours, or the current running container riunning on yout machine .


### Working Together:

### An example of the implemented CI-CD:  


![Ideal CI-CD](/Users/tim/Documents/implemented-ci-cd-schema.png) 



### Resolving asked criterias:
------

+ Think about efficiency

As we will ideally use K8s, we will not have efficiency problem, which means If we have more and more project to onboard, the K8s cluster on AWS or GCP will just pop new pods in order to satisfy all the demands. It's a strength of k8s, you don't have to care anymore about scalability, you juste have to care about the bill at the end of the month :)

+ We love to test things before going into production

As we have a multi test pipelines, teams can test whatever they want, of course we will provide base template in order to have a test/build/release template.
For the testing part, we will advise the dev to write and run unit test locally, those test will still be run when they push on their own branch, popping a new environment on a tmp runner that will run the unit and integration test. 
When a pull request is open, we will spawnb the entire stack on the runner and have som unit/integration and application test in order to cover the entire application, we can also have some user hand test using automation test tools like celenium etc...

+ Did we mentioned we love to test in isolated environments?

Of course it will be isolated. We ain't fools, We know that the motto testing is doubtin but as we said we ain't fools. we are not twitter driven test :p 

+ Build once run anywhere

Using docker images will allow us to build once and run it anywhere as long as the docker daemon is running. 

+ How easy would it be to check what is going on in your environment?

When you'll run test either on your branch, develop you'll be able to follow the test on the gitlab environments or on staging you'll be able to connect to your staging environment in order to test the new features. 


+ We want to see what would be your workflow for a new feature development in your environment from committing into the svc till running in production.

As we have a simple application we will use a simple gitflow workflow. Of course the system we will implement supports other worflow as the gitlab-ci is 100% customizable through a yaml. (I know I know more yaml but yaml is good for you)

The current workflow: 


+ Everything as code is our MOTO

We can redeploy the stack at any time as everything id containerized and automatized, the only part is the gitlab], we can't automate the coding part, we need to leave some work to the dev ahah. 

## Scaling

There is a scalability problem addresses to us, our current park is growing and when we will have to deploy on a lot of nodes at the same time. We need to benchmark our current implementation and make sure it handles 1000 nodes at the same time

## Use cases

2 type of Use cases:

- Full CI/CD

After the whole CI passes, it automatically triggers CD on your production environement, just a simple call to the AWX server will trigger the new deployment of your application through Ansible

- Semi-CD

After your CI passes you can make sure everything is right on staging and then triggers a deployment through the AWX UI.



## Risks

The only risk I see is the update, Apparently the update of AWX to a newer version is tricky and can cause data loss

### Impacts

This new architecture will impact the entire company's workflow. They will have to change their current workflow to fit in the new Architecture

## Corner cases

Our only corner case would be application under a K8s cluster that can directly be deployed with gitlab-ci

## Who will be impacted

List all the component(s) or team(s) that would be impacted by the following
document/design:

- All teams

## Contributors

List of (co)auther(s) of the document.

- [Timothee Dzik](mailto:timodzik@gmail.com)

## References


