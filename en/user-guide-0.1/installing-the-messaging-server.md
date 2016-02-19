---
title: "Installing the Messaging Server"
layout: page
cat: "ug-0-1"
id: "installing-the-messaging-server"
menus: [ "users", "user-guide", "0.1" ]
---

The messaging server is a crucial element so that Roboconf can work.  
Roboconf uses [RabbitMQ](https://www.rabbitmq.com/) as its messaging server. It means you must install an Erlang container and
RabbitMQ itself. Follow the instructions on [RabbitMQ's web site](https://www.rabbitmq.com/download.html).

It is recommended to install the version 3.3.3.  
Versions 3.x come with a [management plug-in](https://www.rabbitmq.com/management.html) which may be very useful.
You **should** follow the instructions on [RabbitMQ's web site](http://www.rabbitmq.com/download.html) to install it.

> The messaging server should be accessible through a public IP.  
> The most simple solution is generally to put it in a public cloud infrastructure.

RabbitMQ needs to be configured with a user and a password.  
Otherwise, client connections will be refused. See this page about [access control](http://www.rabbitmq.com/access-control.html) with RabbitMQ.

> You should clear the guest/guest credentials.  
> The user name and password are the one you set in the Deployment Manager's configuration.

Here is a short snippet to quickly configure RabbitMQ.  
You must first connect to the machine hosting RabbitMQ and then execute these commands.

```properties
# Create a new user called Batman
rabbitmqctl add_user Batman BatmanPassword

# Grant read/write/access permissions to our new user
rabbitmqctl set_permissions Batman ".*" ".*" ".*"

# Check permissions
rabbitmqctl list_user_permissions Batman

# Delete the guest user
rabbitmqctl delete_user guest
```
  
To adjust the access permissions, you may want to look at [the man page](http://www.rabbitmq.com/man/rabbitmqctl.1.man.html) of RabbitMQ.
