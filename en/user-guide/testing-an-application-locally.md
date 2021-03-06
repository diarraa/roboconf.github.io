---
title: "Testing an Application Locally"
layout: page
cat: "ug-last"
id: "testing-an-application-locally"
menus: [ "users", "user-guide" ]
---

This page gives hints about how to test a Roboconf application locally.  
As usual, several strategies exist, and it also depends on the scope of the *testing* word.


## Simulating Deployments

This scenario relies on the [in-memory target handler](target-in-memory.html).  

* Deploy your application in the DM.
* Make sure the **in-memory** handler is deployed in the DM.
* Create a target with the following content and associate it with your application (and its root instances).

```properties
# Configuration file for in-memory
handler = in-memory
name = Simulation
```

You can then manage the life cycle of the Roboconf instances.  
Deploy them, start them, verify dependency management works, etc.

With this mode, the application recipes are not executed.  
It is an interesting solution to discover the way Roboconf works and validate the
structure of your Roboconf application.


## Deploying with a Real Agent

This scenario relies on the [embedded target handler](target-embedded.html).  
It consists in deploying a real Roboconf agent (e.g. the Karaf distribution), configuring it by hand
and starting it. With the embedded handler, Roboconf assumes the agent is launched by hand. Unlike with other
targets, it does not create it.

* Install, configure and start the agent.  
You must set the application name, the scoped instance path, etc. by hand.
* Deploy your application in the DM.
* Make sure the **embedded** handler is deployed in the DM.
* Manage the life cycle of your instances through the web console. Recipes will be executed for real.

> Although this approach works, it is not recommended to use it.

First, it requires quite a lot of steps. Second, it is limited to a single agent. 
And most of all, there might be port conflicts with the DM. You can avoid them by updating the configuration files.
You can even deploy the **roboconf-agent** bundle in the same Karaf distribution than the DM. But again, it is a little
bit complicated to set up.


## Deploying for Real with in-memory Agents

This scenario is a mix of the two previous ones.  
It relies on the [in-memory target handler](target-in-memory.html). It works in fact the same way than
simulated deployments, except we ask the in-memory agents to execute the real recipes.

> Notice you will need to install agent plug-ins in the DM so that recipes are correctly executed.

```properties
# Install the in-memory handler
roboconf:target in-memory

# Install the agent plug-ins you need. Example below.
bundle:install mvn:net/roboconf/roboconf-plugin-script/0.9
```

Be careful, since all the in-memory agents are located on the same machine, all the installed software
will be deployed on it too.

* Deploy your application in the DM.
* Make sure the **in-memory** handler is deployed in the DM.
* Create a target with the following content and associate it with your application (and its root instances).

```properties
# Configuration file for in-memory
handler = in-memory
name = Simulation
in-memory.execute-real-recipes = true
```

You can then manage the life cycle of the Roboconf instances.  
Deploy them, start them, verify dependency management works, etc.
You can also follow deployments steps by steps, watch Roboconf logs output, etc.

This solution is well-adapted to deployments whose recipes deal with Docker containers.  
All the installed software are isolated in containers and do not corrupt your host system.

> When dealing with Docker containers, you may consider to force the IP address of the agent
> in the **in-memory** configuration.

Example:

```properties
# Configuration file for in-memory
handler = in-memory
name = Simulation
in-memory.execute-real-recipes = true
in-memory.ip-address-of-the-agent = your.ip.address
```

Generally, Docker sets up a local network interface which is a kind of *localhost* alias, but 
restricted to the same sub-net than Docker containers. Generally, this interface is associated with
the **172.17.0.1** IP address (172.17.42.1 in former Docker versions). So, if you only deploy Docker containers, you can force the IP address to 172.17.42.1.
See [this page](http://docs.docker.com/engine/userguide/networking/) for more details about Docker network
configuration.

**Notice:** when executing real recipes with in-memory agents, recipes are executed sequentially.  
Usually, agents are located on different machines and thus run concurrently. When in memory, there
a super agent that manages them all to orchestrate the recipes executions.
