# Configuration Management and the Cloud - Week 3

## Learning Objectives

Dive into the details of the different Cloud services, when it makes sense to use them, and how to get the most out of the cloud deployments by:

* Learn how cloud deployments can help us quickly scale our services
* Learn the differences between when running IT infrastructure on-premise versus running it in the cloud
* Learn how we can use a variety of different tools to manage instances running in the cloud

---

## Cloud Computing

### Cloud Services Overview

A service is running in the Cloud means that the service is running somewhere else either in a data center or in other remote servers that can be __reached over via Internet__.

Cloud providers typically offer different service types such as:

* **Software as a Service** or SaaS, is when a Cloud provider delivers an entire application or program to the customer
* **Platform as a Service** or PaaS, is when a Cloud provider offers a preconfigured platform to the customer
* **Infrastructure as a Service** or IaaS, is when a Cloud provider supplies only the bare-bones computing experience

When setting up Cloud resources, **regions** need to be considered. A **region** is a geographical location containing a number of data centers, regions contain **zones** and zones can contain one or more physical **data centers**.

There are multiple factors that is detremiend based on the selected region,

* Latency
* Legal or political factors
* Other services as dependencies that is needed to run the service

Q: If a bare-bones cloud computing experience is needed as well as a high level of control over the software being run, what Cloud service would one choose? Infrastructure as a Service (IaaS). Infrastructure as a Service (IaaS) provides users with the bare minimum needed to utilize a server’s computational resources, such as a virtual machine. It is the user's responsibility to configure everything else.

### Scaling in the Cloud

capacity is how much the service can deliver.

queries per second or QPS

This capacity change is called scaling,

* Upscaling when the capacity is being increased
* Downscaling when the capacity is being decreased

There are a couple of different ways that we can scale our service in the Cloud,

* Horizontal scaling vs. Vertical scaling
  * Horizontally scaling scales the capacity by adding more nodes into the pool (eg. add more servers)
  * Vertically scaling scales the capacity by making the nodes bigger (eg. upgrade memories, CPU or disk space)
* Automatic scaling vs. Manual scaling
  * Automatic scaling uses metrics to automatically increase or decrease the capacity of the system which is controleld by the Cloud provider
  * Manual scaling are controlled by humans instead of software

Q: Which "direction" are we scaling when we add capacity to our network in order to meet demand? Up. Adding capacity to our network to meet demand—whether vertically or horizontally—is considered to be upscaling.

### Evaluating the Cloud

In the case of cloud solutions, IT team is giving up some of its control to the cloud provider. Therefore, it's important to know what kind of support is available and select the one that fits the needs.

Treat the servers executing the workloads as a **commodity** and always use reasonable judgment to protect the machines that we deploy, whether that's on physical server is running on-premise or on virtual machines in the Cloud.

Q: What are some advantages to using cloud services? (Select all that apply) Offload complicated setups to the provider, outsource troubleshooting and maintenance to the provider and letting the provider worry about complicated security measures

### Migrating to the Cloud

**IaaS** is especially useful to administrators using a **lift and shift strategy**.

When migrating to the Cloud, the process is somewhat similar. But instead of moving the physical server in the back of a truck, you migrate your physical servers running on-premise to a virtual machine running in the Cloud. In this case, you're shifting from one way of running your servers to another. The key thing to note with both approaches, is that **the servers core configurations stay the same**.

**PaaS** is well-suited for when you have a specific infrastructure requirement, but you don't want to be involved in the day-to-day management of the platform

**Containers** are applications that are packaged together with their configuration and dependencies.

There are different types of Cloud services:

* Public Cloud is services provided by the public Cloud providers
* Private Cloud is when one company owns the services and the rest of the infrastructure
* Hybrid Cloud is a mixture of both public and private Clouds
* Multi-Cloud is a mixture of public and/or private Clouds across vendors

Q: What does the phrase lift and shift refer to? Migrating to the Cloud by virtualizing the current configuration. When we migrate from traditional server configurations to the Cloud, we lift the current configuration and shift it to a virtual machine.

### Practice Quiz: Cloud Computing

1. When we use cloud services provided to the general consumer, such as Google Suite or Gmail, what cloud deployment model are we using? Public cloud. A public cloud offers services to the general public, often as SaaS (Software as a Service) offerings.
2. What is a container? A virtualized environment containing applications and configurations that can run quickly and reliably on any computing environment. A container is an OS- and hardware-independent environment that allows for easy migration and compatibility.
3. Select the examples of Managed Web Application Platforms. (Check all that apply) Google App Engine.Amazon Elastic Beanstalk. Microsoft App Service.
4. When a company solely owns and manages its own cloud infrastructure, what type of cloud deployment model are they using? Private cloud. A private cloud deployment is one that is fully owned and operated by a single company or entity.
5. Which "direction" are we scaling when we add RAM or CPU resources to individual nodes? Vertical.

---

## Managing Instances in the Cloud

### Spinning up VMs in the Cloud

There are different Cloud providers each with some specific advantages depending on what we are trying to achieve. But usually Cloud service providers implement a console to manage the services.

Regardless of the service proivder, following paramaters needs to be set when creating a VM running in the Cloud

* Name of the instance
* Region and zone where the instance will be running
* CPU, memory and boot disk options for the VM

Cloud service proivders also porvides the command line interface, which allows for us to specify what we want once, and then use the same parameters many times. __Using the command line interface lets us create, modify, and even delete virtual machines from our scripts.__

**Reference images** store the contents of a machine in a reusable format, while **templating** is the process of capturing all of the system configuration to let us create VM in a repeatable way. That exact format of the reference image will depend on the vendor. But often, the result is a file called a **disk image**. A disk image is a snapshot of a virtual machine's disk at a given point in time.

Q: If we want to reuse an exact copy of a virtual machine, we might save a snapshot to use as a reference image later. What is this snapshot called? A disk image. A disk image is a snapshot of a virtual machine’s disk, and is an exact copy of the virtual machine at the time of the snapshot.

### Creating a New VM Using the GCP Web UI

Q: Using the web interface, what is an easy way to create a virtual machine identical to the one we've just configured? Copy and reuse the command line. By clicking the link labeled “Command line”, we can see the exact command used to create the virtual machine.

### Customizing VMs in GCP

Remember that a **reference image** is just a file or configuration that we can deploy repeatedly and with automated tools. 

It’s important to know where to copy our systemd service file on Linux in order to configure our scripts as services. Do you remember the location of the systemd directory? /etc/systemd/system/ . etc/systemd/system/ is the default systemd directory in many Linux distros, including Red Hat Linux.

### Templating a Customized VM

What does the gcloud init command do? Authenticates the connection between our virtual machine and Google Cloud. The gcloud init command sets up the authentication procedure between our virtual machine and Google Cloud.

### Reading: Managing VMs in GCP

Over the last few videos we learned how to create and use virtual machines running on GCP. We then explored how we can use one VM as a template for creating many more VMs with the same setup. You can find a lot more information about this in the following tutorials:

* https://cloud.google.com/compute/docs/quickstart-linux
* https://cloud.google.com/compute/docs/instances/create-vm-from-instance-template
* https://cloud.google.com/sdk/docs

### Practice Quiz: Managing Instances in the Cloud

1. What is templating? The process of capturing the entire system configuration to enable us to reproduce virtual machines. Effective templating software allows you to capture an entire virtual machine configuration and use it to create new ones.
2. Why is it important to consider the region and zone for your cloud service? To ensure bandwidth and reliability for users. Generally, you're going to want to choose a region that is close to your users so that you can deliver better performance.
3. What option is used to determine which OS will run on the VM? The boot disk from which the VM boots will determine what operating system runs on the VM.
4. When setting up a new series of VMs using a reference image, what are some possible options for upgrading services running on our VM at scale? Create a new reference image each update and use a configuration management system like Puppet.
5. When using gcloud to manage VMs, what two parameters tell gcloud that a) we want to manage our VM resources and b) that we want to deal with individual VMs? (Check two) compute and instances

---

### Automating Cloud Deployments

## Cloud Scale Deployments

A **load balancer** ensures that each node receives a balanced number of requests. When a request comes in, the load balancer picks a node to serve the response. There's a bunch of different strategies load balancer uses to select the node. The simplest one is just to give each node one request called round robin. More complex strategies include always selecting the same node for requests coming from the same origin, selecting the node that's closest to the requester, and selecting the one with the least current load.

As we called out, instance groups like these are usually configured to spin up more nodes when there's more demand, and to shut some nodes down when the demand falls. This capability is called **autoscaling**. It allows the service to increase or reduce capacity as needed while the service owner only pays for the cost of the machines that are in use at any given time. Since some nodes will shut down when demand is lower, their local disks will also disappear and should be considered ephemeral or short-lived. If you need data persistence, you'll have to create separate storage resources to hold that data and connect that storage to the nodes. That's why the services that we run in the Cloud are usually connected to a database which is also running in the Cloud.

In a small-scale application, the entry point could be the web server that serves the pages, and that would be it. For large applications where speed and availability matter, there will be a couple of **layers** in between the entry point and the actual web service.

The **first layer** will be a pool of **web caching** servers with a load balancer to distribute the requests among them. One of the most popular applications for this caching is called **Varnish**, but of course it's not the only one. The **Nginx** web server and software also includes this caching functionality.

When a request is made, the caching servers first check if the content is already stored in their memory. If it's there, they respond with the contents, if it's not, they ask their configured backend for the content and then store it so that it's present for future requests. This configured backend is the actual web service that generates the webpages for the site, and it will also normally be a pool of nodes running under a load balancer.

To get any necessary data, this service will connect to a database. But because getting data from a database can be slow, there's usually an extra layer of caching, specific for the database contents. The most popular applications for this level of caching are Memcached and Redis.

Q: What does a load balancer do? Ensures each node receives a reasonable number of requests. Load balancers reroute requests in order to balance and reduce network load.

## What is orchestration?

**Orchestration** is the automated configuration and coordination of complex IT systems and services. In other words, orchestration means automating a lot of different things that need to talk to each other.

Q: What is the difference between automation and orchestration? Automation refers to a single task; orchestration arranges tasks to optimize a workflow. Automation is when we set up a single step in a process to require no oversight, while orchestration refers to automating the entire process.

## Cloud Infrastructure as Code

An option that's becoming really popular in the Orchestration field, is called **Terraform**. Similar to Puppet, Terraform uses its own Domain-specific language which lets us specify what we want our Cloud infrastructure to look like. The cool thing about Terraform is that it knows how to interact with a lot of different Cloud providers and automation vendors. So you can write your Terraform rules to deploy something on one Cloud provider, and then use very similar rules to deploy the service to a different Cloud provider. Terraform uses each Cloud provider's API to accomplish this.

Q: Do you remember what Infrastructure as Code (IaC) is? Using machine-readable files to automate configuration. IaC uses special machine-readable config files to automate configuration management.

Finally, let's spend a moment talking about the contents of the nodes or instances managed by the **Orchestration** tools. When dealing with nodes in the Cloud, there are basically two options. Either they're **long-lived** and their contents need to be periodically updated, or they are **short-lived** and updates are made by deleting the old instances and deploying new ones. **Long-lived** instances are typically servers that are not expected to go away. Things like your company's internal mail server or internal document sharing servers, will manage these instances using a configuration management system like Puppet, which can deploy any necessary changes to the machines while they're running. This keeps them updated to the latest state. On the flip side, **short-lived** instances come and go very quickly. For these cases, it makes less sense to apply changes while they're running. Instead, we normally apply the configuration that we want the instances to have when they start, and we deploy any future changes by replacing the instances with new ones. We can still use Puppet for the initial setup, but we don't need to run the agent periodically, only at the start.

## Reading: More About Cloud & GCP

Check out the following links for more information:

* Getting started on GCP with Terraform
* Creating groups of unmanaged instances
* Official documentation is here: https://cloud.google.com/load-balancing/docs/https/
* https://geekflare.com/gcp-load-balancer/

Interesting articles about hybrid setups:

* https://blog.inkubate.io/create-a-centos-7-terraform-template-for-vmware-vsphere/
* https://www.terraform.io/docs/enterprise/before-installing/reference-architecture/gcp.html
* https://www.hashicorp.com/resources/terraform-on-premises-hybrid-cloud-wayfair

## Practice Quiz: Automating Cloud Deployments

1. In order to detect and correct errors before end users are affected, what technique(s) should we set up? Monitoring and alerting. Monitoring and alerting allows us to monitor and correct incidents or failures before they reach the end user.
2. When accessing a website, your web browser retrieves the IP address of a specific node in order to load the site. What is this node called? Entry point. When you connect to a website via the Internet, the web browser first receives an IP address. This IP address identifies a particular computer: the entry point of the website.
3. What simple load-balancing technique just assigns to each node one request at a time? Round Robin.  Round-robin load balancing is a basic way of spreading client requests across a server group. In turn, a client request will be forwarded to each server. The load balancer is directed by the algorithm to go back to the top of the list and repeat again.
4. Which cloud automation technique spins up more VMs into instance groups when demand increases, and shuts down VMs when demand decreases? Autoscaling. Autoscaling helps us save costs by matching resources with demand automatically.
5. Which of the following are examples of orchestration tools used to manage cloud resources as code? (Check all that apply) Terraform, CloudFormation and Azure Resource Manager.

---
## Credit

* [Coursera - Configuration Management Cloud Week 3](https://www.coursera.org/learn/configuration-management-cloud/home/week/3)
