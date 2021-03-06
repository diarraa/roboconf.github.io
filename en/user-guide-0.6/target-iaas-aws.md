---
title: "Amazon Web Services Support"
layout: page
cat: "ug-0-6"
id: "target-iaas-aws"
menus: [ "users", "user-guide", "0.6" ]
---

Roboconf has a target implementation for Amazon Web Services (AWS).  
It only supports the creation of *compute* VMs.

To install it, open the DM's interactive mode and use one of the following options.  
With the [roboconf:target](karaf-commands-for-roboconf.html) command:

```properties
# The version will be deduced automatically by the DM
roboconf:target aws
```

Or with the native Karaf commands:

```properties
# Here in version 0.6
bundle:install --start mvn:net.roboconf/roboconf-target-iaas-ec2/0.6
```

Every new VM is associated with a public IP address.  
This address will be used by other components which resolve their dependencies through Roboconf.

Sample **target.properties**.  
Just copy / paste and edit.

```properties
# Configuration file for EC2
handler = iaas-ec2

# Provide a meaningful description of the target
name = 
description = 

# EC2 URL
ec2.endpoint = 

# Credentials to connect
ec2.access.key = 
ec2.secret.key = 

# VM configuration
ec2.ami = 
ec2.instance.type = t1.micro
ec2.ssh.key = 
ec2.security.group =

# Elastic IP address
# ec2.elastic.ip =  

# Block Storage
# ec2.availability-zone = 
# ec2.use-block-storage = true
# ec2.ebs-snapshot-id = 
# ec2.ebs-delete-on-termination = true
# ec2.ebs-size = 2
# ec2.ebs-mount-point = /dev/sdf
# ec2.ebs-type = standard
```

It is also possible to define an elastic IP address.  
It is not a good idea to set it in the **target.properties**, although it works.
Indeed, all the VM instances created from this target configuration will try to use the same elastic IP.
Since Amazon does not allow it, only the last created VM will be associated with this IP. 
**It is much better** to define the elastic IP in the instance definition, as shown below.

<pre><code class="language-roboconf">
instance of VM {
	name: VM1;
	data.ec2.elastic.ip: your-elastic-ip;

	# Put children instances next...
}
</code></pre>

> In a general matter, VM instances can inject target parameters through their definitions.  
> These properties must start with "data." followed by the target property.

Here is a complete description of the parameters for Amazon Web Services.

| Property | Description | Default | Mandatory
| --- | --- | --- | --- |
| handler | Determines the target handler to use | none, must be "iaas-ec2" | yes |
| name | A human-readable name for the target | - | no |
| description | A description of the target. | - | no |
| ec2.endpoint | URL of the compute service (eg. eu-west-1.ec2.amazonaws.com)  | none | yes |
| ec2.access.key | Access key defined in your ec2 account | none | yes |
| ec2.secret.key | Secret key defined in your ec2 account | none | yes |
| ec2.ami | The ID of the VM image used as a template for the VM | none | yes |
| ec2.instance.type | The VM "size" aka. instance type or flavor | t1.micro | no |
| ec2.ssh.key | The name of the ssh key used to connect | none | yes |
| ec2.security.group | The VM security group name. *Caution to not set the security group id*. | default | no |
| ec2.elastic.ip | An elastic IP address | none | no |
| ec2.availability-zone | Availability zone for instances and EBS volumes: necessary to reuse volumes (they must be in the same availability zone as instances they are attached to) | none | no |
| ec2.use-block-storage | Use EBS block storage (create / reuse and mount EBS volume) | false | no |
| ec2.ebs-snapshot-id | Snapshot ID for volume, or volume name that can be reused if delete-on-termination is false | none | no |
| ec2.ebs-delete-on-termination | Delete volume on instance termination, or not | false | no |
| ec2.ebs-size | Volume size (Gb) | 2 | no |
| ec2.ebs-mount-point | Volume mount point | /dev/sdf | no |
| ec2.ebs-type | Volume type. Can be *standard*, *io1* (Provisioned IOPS - SSD) or *gp2* (General Purpose - SSD). | standard | no |
