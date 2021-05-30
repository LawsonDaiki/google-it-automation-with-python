# Crash Course of Python - Week 2

## Learning Objectives

Dive deeper into baisc congfiguration management concepts and Puppet by:

* Learn how to install Puppet on your computer and how to use a simple test setup to check your rules work as expected
* Learn how to configure the typical client-server set-up with Puppet clients connecting and authenticating to the Puppet server to get the rules that they should apply
* Learn how to use testing techniques and releasing best practices to safely deploy changes to clients of our configuration management system

---

## Deploying Puppet

### Applying Rules Locally

The **manifest** is a file with .pp extention where we'll store the rules that we want to apply.

Checkout tools.pp file as an example.

The **catalog** is the list of rules that are generated for one specific computer once the server has evaluated all variables, conditionals, and functions.

Q: Which of the following file extensions does the manifest file need to end with in Puppet? '.pp'. Manifest files are where we store the rules to be applied.

### Managing Resource Relationships

The Puppet manifests that we use to manage computers in our fleet usually include a bunch of different resources that are related to each other. You're not going to configure a package that's not installed and you don't want to start a service until both the package and the configuration are in place. Puppets lets us control this with **resource relationships**.

Example:

```
class ntp {
    package { 'ntp':
        ensure => latest,
    }
    file { '/etc/ntp.conf':
        source => 'home/user/ntp.conf',
        replace => true,
        require => Package['ntp'],
        notify => Package['ntp'],
    }
    service {
        enable => true,
        ensure => running,
        require => File['/etc/ntp.conf'],
    }
}
include ntp
```

We write resources types in lowercases when declaring them, but **capitalize** them when referring to them from another resource's attribute.

To apply this rules locally, run the command:

```
sudo puppet apply -v ntp.pp
```

Q: When we declare a resource type, how do we differentiate between the original resource type and the name of a resource relationship being referenced in another resource? Use lowercase for the original, and capitalize the resource name when referencing a relationship.


Checkout ntp.pp file as an example.

### Organizing Your Puppet Modules

In puppet, manifests are organized into **modules**. A module is a collection of manifests and associated data.

* Manifest directory which stores manifest and init.pp where it defines a class with the same name as the created module
* Templates directory which stores any files that stores rules to be used

For example, to install the Apache module provided by Puppet Labs to check out how this works, run the command:
```
sudo apt install puppet-module-puppetlabs-apache
```

To create a manifest file that includes the module we've just installed. Just create a manifest like this:
```
include ::apache
```

Here, we're telling Puppet to include the Apache module. The double colon before the module name, let's puppet know that this is a global module. Let's save this file now and apply it using Puppet apply like we did before.

---

## Deploying Puppet to Clients

### Puppet Nodes

In Puppet terminology, a **node** is any system where a Puppet agent can run. To apply different rules to different systems is to use separate node definitions.

Below example is the default node with two classes, the sudo class and the ntp class.

```puppet
node default {
    class { 'sudo': }
    # sets an additional servers parameter which can be used to get the network time
    class { 'ntp':
            servers => [ 'ntp1.example.com', 'ntp2.example.com' ]
    }
}
```

Below example shows how to apply some settings to only some specific nodes by adding more node definitions.

```puppet
node default {
    class { 'sudo': }
    # sets an additional servers parameter which can be used to get the network time
    class { 'ntp':
            servers => [ 'ntp1.example.com', 'ntp2.example.com' ]
    }
    class { 'apache': }
}
```

Below example shows how specific nodes in the fleet are identified by their FQDNs, or fully qualified domain names.

```puppet
node webserver.example.com {
    class { 'sudo': }
    # sets an additional servers parameter which can be used to get the network time
    class { 'ntp':
            servers => [ 'ntp1.example.com', 'ntp2.example.com' ]
    }
    class { 'apache': }
}
```

### Puppet's Certificate Infrastructure

Puppet uses __public key infrastructure (PKI)__, __secure sockets layer (SSL)__, to establish secure connections between the server and the clients.

__Puppet comes with its own certificate authority__, which can be used to create certificates for each clients.

why do we care so much about the identity of the nodes? There's a bunch of reasons.

One of the reason why identity of the nodes matter is that the Puppet rules can sometimes include confidential information.

* Automatic sign all requests feature is available in Puppet, it should be limited to test deployment and never used for real computers being used by real users

---

## Updating Deploymnets

### Modifying and Testing Manifests

There are few ways of test changes made on Puppet

1. puppet parser validate command with noop paramter
   * Checks that the syntax of the manifests is correct
   * noop parameter which comes from no operations makes puppet simulate what it would do without actually doing it
2. Seperate test machines that are used only for testing out changes
3. Automated testing via R-Spec

Below example sets the facts involved different values and checks that the catalog ends up stating the expected 

```puppet
describe 'gksu', :type => :class do
    let (:facts) { { 'is_virtual' => 'false' } }
    it { should contain_package('gksu').with_ensure('latest') }
end
```

### Safely Rolling out Changes and Validating Them

Even if you've tested the change on your computer or on a test computer and it worked just fine, __it doesn't mean that the change will work correctly on all machines running in production__.

In an infrastructure context, **production** is the parts of the infrastructure where a service is executed and served to its users.

In order to roll out changes safely,

1. Always run them through a test environment first
2. Push changes in in batches
3. Make the change to be small and self-contained

---

## Credit

* [Coursera - Configuration Management Cloud Week 2](https://www.coursera.org/learn/configuration-management-cloud/home/week/2)
