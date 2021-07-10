# Configuration Management and the Cloud - Week 4

## Building Software for the Cloud

### Storing Data in the Cloud

Cloud providers give us a lot of **storage** options. Picking the right solution for data storage will depend on what service you're building. You'll need to consider a bunch of factors, like; how much data you want to store, what kind of data that is, what geographical locations you'll be using it in, whether you're mostly writing or reading the data, how often the data changes, or what your budget is.

When choosing a storage solution in the Cloud, you might opt to go with the traditional storage technologies, like **block storage**, or you can choose newer technologies, like **object or blob storage**.

As we saw in an earlier video, when we create a VM running in the Cloud, it has a local disk attached to it. These local disks are an example of **block storage**. This type of storage closely resembles the physical storage that you have on physical machines using physical hard drives. Block storage in the Cloud acts almost exactly like a **hard drive**. The operating system of the virtual machine will create and manage a file system on top of the block storage just as if it were a physical drive. There's a pretty cool difference though. These are virtual disks, so we can easily move the data around. For example, we can migrate the information on the disk to a different location, attach the same disk image to other machines, or create snapshots of the current state. All of this without having to ship a physical device from place to place. Our block storage can be either persistent or ephemeral. **Persistent storage** is used for instances that are long lived, and need to keep data across reboots and upgrades. On the flip side, **ephemeral storage** is used for instances that are only temporary, and only need to keep local data while they're running. Ephemeral storage is great for temporary files that your service needs to create while it's running, but you don't need to keep. This type of storage is especially common when using containers, but it can also be useful when dealing with virtual machines that only need to store data while they're running.

If you're looking to share data across instances, you might want to look into some **shared file system solutions**, that Cloud providers offer using the platform as a service model. When using these solutions, the data can be accessed through network file system protocols like **NFS or CIFS**. This lets you connect many different instances or containers to the same file system with no programming required. Block storage and shared file systems work fine when you're managing servers that need to access files. But if you're trying to deploy a Cloud app that needs to store application data, you'll probably need to look into other solutions like **objects storage**, which is also known as **blob storage**.

**Object storage** lets you place in retrieve objects in a storage bucket. These objects are just generic files like photos or cat videos, encoded and stored on disk as binary data. These files are commonly called blobs, which comes from binary large object, and as we called out, these blobs are stored in locations known as **buckets**. Everything that you put into a storage bucket has a unique name. There's no file system. You place an object into storage with a name, and if you want that object back, you simply ask for it by name. To interact with an object store, you need to use an API or special utilities that can interact with the specific object store that you're using.

On top of this, we've called out in earlier videos that most Cloud providers offer databases as a service. These come in two basic flavors, SQL and NoSQL. **SQL databases**, also known as relational, use the traditional database format and query language. Data is stored in tables with columns and rows that can be indexed, and we retrieve the data by writing SQL queries. A lot of existing applications already use this model, so it's typically chosen when migrating an existing application to the Cloud. **NoSQL databases** offer a lot of advantages related to scale. They're designed to be distributed across tons of machines and are super fast when retrieving results. But instead of a unified query language, we need to use a specific API provided by the database. This means that we might need to rewrite the portion of the application that accesses the DB.

The performance of a storage solution is influenced by a number of factors, including throughput, IOPS, and latency.

* Throughput is the amount of data that you can read and write in a given amount of time.
* IOPS or input/output operations per second measures how many reads or writes you can do in one second, no matter how much data you're accessing.
* Latency is the amount of time it takes to complete a read or write operation.

When choosing the storage class to use, you might come across terms like hot and cold.

* Hot data is accessed frequently and stored in hot storage
* Cold data is accessed infrequently, and stored in cold storage.

These two storage types have different performance characteristics. For example, hot storage back ends are usually built using solid state disks, which are generally faster than the traditional spinning hard disks. So how do you choose between one and the other? Say you want to keep all the data you're service produces for five years, but you don't expect to regularly access data older than one year. You might choose to keep the last one year of data in hot storage so you have fast access to it, and after a year, you can move your data to cold storage where you can still get to it, but it will be slower and possibly costs more to access.

Q: Which form of cloud data storage is based on objects instead of traditional file system hierarchies? Blob storage.  Blobs are pieces of data that are stored as independent objects, and require no file system.

### Load Balancing

No matter the reason, we use orchestration tools and techniques to make sure that the instances are repeatable. And once we've set up replicated machines, we'll want to distribute the requests across instances. We called out earlier that this is where **load balancing** comes into play.

Load balancing methods:

* Round-Robin DNS

Say your service needs to keep track of the actions that a user has taken up till now. In this case, you'll want your load balancer to use **sticky sessions**. Using sticky sessions means all requests from the same client always go to the same back end server.

How do you make sure that clients connect to the servers that are closest to them? You can use **GeoDNS** and **GeoIP**. These are DNS configurations that will direct your clients to the closest geographical load balancer.

There are some providers dedicated to bringing the contents of your services as close to the user as possible. These are the content delivery networks or **CDNs**. They make up a network of physical hosts that are geographically located as close to the end user as possible.

Q: Which description fits the Round-robin DNS load balancing method? Each client is served in turn. The Round-robin approach serves clients one at a time, starting with the first, and making rounds until it reaches the beginning again.

### Change Management
Change management is making changes in a controlled and safe way to fix bugs and improve features in our services.

A **continuous integration system** will build and test our code every time there's a change.

We can configure our CD to push to different environments based on some rules. What do we mean by that? In an earlier video, we mentioned that when pushing puppet changes, we should have a **test environment** separate from the production environment (environment means everything needed to run the service). Having them separate lets us validate that changes work correctly before they affect users. The test environment needs to be similar enough to prod that we can use them to check our changes work correctly. You could have your CD system configured to push new changes to the test environment. You can then check that the service is still working correctly there, and then manually tell your deployment system to push those same changes to production.

If the service is complex and there are a bunch of different developers making changes to it, you might set up additional environments where the developers can test their changes in different stages before releasing them. For example, you might have your CD system push all new changes to a development or **dev environment**, then have a separate environment called **pre-prod**, which only gets specific changes after approval. And only after a thorough testing, these changes get pushed to prod. You want to deploy it to one of those testing or development environments to make sure it works correctly before you ship it to prod.

In **A/B testing**, some requests are served using one set of code and configuration, A, and other requests are served using a different set of of code and configuration, B. When you have something that you want to test in production with real customers, you can experiment using A/B testing. In A/B testing, some requests are served using one set of code and configuration, A, and other requests are served using a different set of of code and configuration, B.

This is another place where a load balancer and instance groups can help us out. You can deploy one instance group in your A configuration and a second instance group in your B configuration. Then by changing the configuration of the load balancer, you can direct different percentages of inbound requests to those two configurations. If your A configuration is today's production configuration and your B configuration is something experimental, you might want to start by only directing 1 % of your requests to B. Then you can slowly ramp up the percentage that you check out whether the B configuration performs better than A, or not. Heads up, make sure you have basic monitoring so that it's easy to tell if A or B is performing better or worse.

Q: Automation tools are used to manage the software development phase's build and test functions. Which of the following is the set of development practices focusing on these aspects? Continuous Integration. Continuous Integration means the software is built, uploaded, and tested constantly.

## Understanding Limitations

Some API calls used in Cloud services can be expensive to perform, so most Cloud providers will enforce **rate limits** on these calls to prevent one service from overloading the whole system. For example, there might be a rate limit of one call per second for an expensive API call. On top of that, there are also utilization limits, which cap the total amount of a certain resource that you can provision. These quotas are there to help you avoid unintentionally allocating more resources than you wanted. Imagine you've configured your service to use auto scaling and it suddenly receives a huge spike in traffic. This could mean a lot of new instances getting deployed which can cost a lot of money.

On top of that, there are also **utilization limits**, which cap the total amount of a certain resource that you can provision. These quotas are there to help you avoid unintentionally allocating more resources than you wanted. Imagine you've configured your service to use auto scaling and it suddenly receives a huge spike in traffic.

Q: What is the purpose of a rate limit? To prevent overloading the entire system with one service. Cloud providers will often enforce rate limits on resource-hungry service calls to prevent one service from overloading the entire system.

## Reading: More About Cloud Providers

Here are some links to some common Quotas you’ll find in various cloud providers

* https://cloud.google.com/compute/quotas#understanding_vm_cpu_and_ip_address_quotas
* https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html
* https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#service-specific-limits

## Practice Quiz: Building Software for the Cloud

1. What is latency in terms of Cloud storage? The amount of time it takes to complete a read or write operation. Latency is the amount of time it takes to complete a read or write operation.
2. Which of the following statements about sticky sessions are true? (Select all that apply.) All requests from the same client always go to the same backend server. They should only be used when needed. They can cause problems during migration.
3. If you run into limitations such as rate limits or utilization limits, you should contact the Cloud provider and ask for a (). Quota increase. Our cloud provider can increase our limits that we have set, though it will cost more money.
4. What is the term referring to everything needed to run a service? Enviroment.  Everything used to run the service is referred to as the environment. This includes the machines and networks used for running the service, the deployed code, the configuration management, the application configurations, and the customer data.
5. What is the term referring to a network of hosts spread in different geographical locations, allowing ISPs to be as close as possible to content? Content delivery network. CDNs allow an ISP to select the closest server for the content it is requesting.

---

### Monitoring and Alerting

## Getting Started with Monitoring

**Monitoring** lets us look into the history and current status of a system.

How can we know what the status is? We'll check out a bunch of different **metrics**. These metrics tell us if the service is behaving as expected or not. Well, some metrics are generic, like how much memory an instance is using. Other metrics are specific to the service we want to monitor. If you're running an e-commerce site, you'll care about how many purchases were made successfully and how many failed to complete.

There's a bunch of different **monitoring systems** out there. Some systems like AWS Cloudwatch, Google Stack Driver, or Azure Metrics are offered directly by the Cloud providers. Other systems like Prometheus, Datadog, or Nagios can be used across vendors.

**Pro tip:** you only want to store the metrics that you care about, since storing all of these metrics in the system takes space, and storage space costs money.

Q: Which of the following monitoring models is being used if our monitoring system requires our service to actively send metrics? Push Model. When **push** monitoring is used, the service being monitored actively sends metrics to the monitoring system.

**Whitebox monitoring** checks the behavior of the system from the inside, it's when we collect metrics from inside a system, like how much storage space the service is currently using or how long it takes to process a request, this is called whitebox monitoring

On the flip side, **blackbox monitoring** checks the behavior of the system from the outside. This is typically done by making a request to the service and then checking that the actual response matches the expected response. We can use this to do a very simple check to know if the service is up and to verify if the service is responding from outside your network. Or we could use it to see how long it takes for a client in a different part of the world to get a response from the system.

## Getting Alerts When Things Go Wrong

The most basic approach is to run a job periodically that checks the health of the system and sends out an email if the system isn't healthy. On a Linux system, we could do this using **cron**, which is the tool to schedule periodic jobs. We'd pair this with a simple Python script that checks the service and sends any necessary emails. This is an extremely simplified version of an alerting system, but it shares the same principles. Is all alerting systems, no matter how complex and advanced.

**Raising an alert signals** that something is broken and a human needs to respond. For example, you can set up your system to raise alerts if the application is using more than 10 gigabytes of RAM, or if it's responding with too many 500 errors, or if the queue of requests waiting to get processed gets too long.

Type of alert:

* Those that need immediate attention are called **pages**, which comes from a device called a pager.

One thing to highlight is that all alerts should be **actionable**. If you get a bug or a page and there's nothing for you to do, then the alert isn't actionable and it should be changed or it shouldn't be there at all. Otherwise, it's just noise.

To set up good alerts, we need to figure out which situations should page, which ones should create bugs, and which ones we just don't care about.

Q: What do we call an alert that requires immediate attention? Page. Pages are alerts that need immediate human attention, and are often in the form of SMS or email.

## Service-Level Objectives

No system is ever available 100% of the time, it's just not possible. But depending on how critical the service is, it can have different service level objectives, or **SLOs**. SLOs are pre-established performance goals for a specific service. Setting these objectives helps manage the expectations of the service users, and the targets also guide the work of those responsible for keeping the service running. **SLOs need to be measurable**, which means that there should be metrics that track how the service is performing and let you check if it's meeting the objectives or not.

Availability targets like this one are commonly named by their **number of nines**. Our 99% example would be a two 9 service, 99.9% availability is a three 9 service, 99.999% availability is a five 9 service. **Five nine** services promised a total down time of up to five minutes in a year. Five nines is super high availability, reserved only for the most critical systems. A **three nine** service, aiming for a maximum of eight hours of downtime per year, is fine for a lot of IT systems. Five nine services usually require a much **larger team** of engineers to maintain it.

Any service can have a bunch of different service level objectives like these, they tell its users what to expect from it. Some services, like those that we pay for, also have more strict promises in the form of **service level agreements**, or **SLAs**. A service level agreement is a commitment between a provider and a client.

Q: If our service has a Service Level Objective (SLO) of four-nines, what is our error budget measured in downtime percentage? .01% . If we have an SLO of 99.99%, that gives us an error budget of .01%.

## Basic Monitoring in GCP

For this demonstration, we'll use the monitoring tool called **Stackdriver**, which is part of the overall offering.

It's common practice to use **time windows of one, five, or even 10 minutes when dealing with the alerting**. We don't want to get an alert for a small spike that lasted only a few seconds and then went away. We want to get alerted when there's an actual problem that requires our attention.

Q: What type of policy requires us to set up a condition which notifies us when it’s triggered? Alerting policy. An Alerting Policy specifies the conditions that trigger alerts, and the actions to be taken when these alerts are triggered, like sending an email address notification.

## Reading: More Information on Monitoring and Alerting

Check out the following links for more information:

* https://www.datadoghq.com/blog/monitoring-101-collecting-data/
* https://www.digitalocean.com/community/tutorials/an-introduction-to-metrics-monitoring-and-alerting
* https://en.wikipedia.org/wiki/High_availability
* https://landing.google.com/sre/books/

## Practice Quiz: Monitoring & Alerting

1. What is a Service Level Agreement? A  strict commitment between a provider and a client. A service-level agreement is an arrangement between two or more parties, one being the client and the other being service providers.
2. What is the most important aspect of an alert? It must be actionable. If an alert notification is not actionable, it should not be an alert at all.
3. Which part of an HTTP message from a web server is useful for tracking the overall status of the response and can be  monitored and logged? The response code in the server's message. We can log and monitor these response codes, and even use them to set alert conditions.
4. To set up a new alert, we have to configure the () that triggers the alert. Condition. We must define what occurence or metric threshold will serve as a conditional trigger for our alert.
5. When we collect metrics from inside a system, this is known as () monitoring. White-Box. A white-box monitoring system is one that collects metrics internally, from within the system being monitored.


## Credit

* [Coursera - Configuration Management Cloud Week 4](https://www.coursera.org/learn/configuration-management-cloud/home/week/4)
