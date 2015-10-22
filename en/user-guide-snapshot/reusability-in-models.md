---
title: "Reusability in Models"
layout: page
cat: "ug-snapshot"
id: "reusability-in-models"
menus: [ "users", "user-guide", "Snapshot" ]
---

This page explains several ways of creating reusable graph portions.  
It is quite helpful to use and write reusable recipes.
As a user, it is convenient to not have to write ALL your application description, and ALL
the recipes. Roboconf has solutions to ease reusability.


## Component Extensions

The first option is that a component can extend another one.  
It means the extending component will inherit all the properties and the recipe
of the component it extends.

Let's take an example.

<pre><code class="language-roboconf">
MySQL {
	exports: ip, port = 3306;
	installer: puppet;
	# There is a directory called MySQL with a Puppet module.
}

My-Client-Database {
	extends: MySQL;
}
</code></pre>

In this example, we have two components.  
There are exactly the same, except they do not have the same name. This helps to distinguish
them in terms of roles and behavior. We can associate different rules about monitoring and autonomic.

An extending component can also override property values.

<pre><code class="language-roboconf">
MySQL {
	exports: ip, port = 3306;
	installer: puppet;
	# There is a directory called MySQL with a Puppet module.
}

My-Client-Database {
	extends: MySQL;
	exports: port = 3307;
}
</code></pre>

Here, the default value of the port variable will be 3307 instead of 3306.  
And as usual, instances can override the default values of their component.

Extending components can also define new variables.

<pre><code class="language-roboconf">
MySQL {
	exports: ip, port = 3306;
	installer: puppet;
	# There is a directory called MySQL with a Puppet module.
}

My-Client-Database {
	extends: MySQL;
	exports: my-own-variable = something;
}
</code></pre>

This can help in quite a lot of situations.  
Read [this page](inheritance-and-variables.html) for more information about how inheritance impacts variable exports.


## Imports

It is possible to split graph definitions into several files.  
For every application, there is one main file. By using the **import** keyword,
this main file can import other definitions.

These definitions can be in the same application.  
Or they can be in another Roboconf project. You can include other Roboconf projects
by using Roboconf's **Maven plug-in**.

Local imports are defined as follows...

<pre><code class="language-roboconf">
import file-name;
import dir1/dir2/file-name;
</code></pre>

Remote imports are only handled through the Roboconf Maven plug-in.

<pre><code class="language-roboconf">
import application-artifact-id/file-name;
import application-artifact-id/dir1/dir2/file-name;
</code></pre>


## Good Practices

If an application part (graph definition + recipe) aims at being reusable,
there are some good practices to promote them and ease their reuse.

1. Create a dedicated application / Maven module.
2. Choose a significant artifact ID.


## Use Facets

Facets are abstract types.  
They allow to draw links between graph components very easily.

An example will be easier to understand.  
Let's assume with a first application that defines a load balancer based on Apache and ModJK.  
Let's call this project **load-balancer--puppet--apache-mod-jk**. This is also the application's name.
Let's assume the application's artifact ID is **load-balancer--puppet--apache-mod-jk**.

<pre><code class="language-roboconf">
lb--apache-mod-jk--puppet {
	exports: ip, port = 80;
	imports: load-balance-able.*;
	installer: puppet;
}

facet load-balance-able {
	exports: ip, port = 80;
}
</code></pre>

The graph definition includes a component, associated with a recipe (a Puppet module), and a facet.
The facet designates abstract elements. The only thing we know about these elements, is that they **MUST**
export an IP address and a port (assumed to be 80, by default).

This is a first project.  
Now, let's assume I have another project in which I want to reuse a load balancer.
Here is how we would proceed (assuming the Roboconf's Maven plug-in can resolve dependencies).

<pre><code class="language-roboconf">
import load-balancer--puppet--apache-mod-jk/main.graph;

my-application {
	installer: bash;
	facets: load-balance-able;
}
</code></pre>

This graph definition declares a single component.  
It is installed with Bash. And by indicating it has the **load-balance-able** facet,
we know two things: first, it exports **ip** and **port** variables. Second, it will be
resolved by the load balancer.

Roboconf will draw the right links between components just with these declarations.  
Read [this page](inheritance-and-variables.html) for more information about how facets impact variable exports.


## Scopes

Reusable parts can be shared inside our community.  
But it is also possible to define its own reusable parts (e.g. in a company that would like to enforce
some practices and configurations). There is quite a lot of possibilities offered by this system.


## Sharing

Roboconf recipes can be defined and maintained anywhere.  
Official recipes are hosted on [Github](http://github.com/roboconf-recipes), every recipe having its own Git repository.

It is up to you to determine whether you want to use them, create new ones, or even, create your own recipe repository.
This is indeed an option some organizations may retain.


## About Maven

Remote imports rely on Maven artifacts and repositories.  
This may seem constraining. However, Maven is a usual build tool, widely used and with 
lots of integration here and there.

An import thing to highlight is that remote imports are resolved and copied in the application
**at build time**. So, a packaged application contains all the definitions and the recipes it needs.

This guarantees that if you release your Roboconf configuration, you will be able to take it in 10 years
and it will still be working. No matter if the repositories of remote imports disappeared.
