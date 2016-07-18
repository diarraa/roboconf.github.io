---
title: "Monitoring"
layout: page
cat: "ug-last"
id: "monitoring-roboconf"
menus: [ "users", "user-guide" ]
---

Roboconf's monitoring means monitoring the DM, the messaging server (RabbitMQ) and agents.  
For the moment, this still needs to be addressed.


## Monitoring the DM and the Agents

The DM and the agent distributions rely on Apache Karaf.  
The OSGi bundles do not embed anything related to monitoring. But Karaf
exposes a JMX interface. You can use it to retrieve information and manage
the OSGi servers.

See [Karaf's web site](http://karaf.apache.org/manual/latest/) for more information.

JMX access can be completed with the web consoles and shell access.  
Once again, you will find information on Karaf's web site.

You can also take a look at [this blog post](http://blog.nanthrax.net/2014/03/apache-karaf-cellar-camel-activemq-monitoring-with-elk-elasticsearch-logstash-and-kibana/).  
It shows how to use [Logstash](http://logstash.net/), [ElasticSearch](http://www.elasticsearch.org) and 
[Kibana](http://www.elasticsearch.org/overview/kibana) to monitor several Karaf instances by aggregating and sorting
data collected from logs and JMX probes. A similar system should be feasible with Roboconf.


## Monitoring with Apache Karaf Decanter

Apache [Karaf Decanter](http://karaf.apache.org/manual/decanter/latest-1/index.html) is a monitoring tool for Karaf solutions and applications running on it.  
So The DM and the agent can be monitored using Decanter, it suffices to install it.  
You can follow this [blog](http://blog.nanthrax.net/2015/07/monitoring-and-alerting-with-apache-karaf-decanter/) to install and configure Decanter.


## Monitoring RabbitMQ

RabbitMQ has a management console which supports some monitoring features.  
See [RabbitMQ's user guide](http://www.rabbitmq.com/management.html) for more information.

You will also find additional information on the web.  
As an example, this [GitHub project](https://github.com/jamesc/nagios-plugins-rabbitmq) provides Nagios checks for RabbitMQ. 
