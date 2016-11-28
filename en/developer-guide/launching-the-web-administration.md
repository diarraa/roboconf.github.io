---
title: "Launching the Web Administration"
layout: page
cat: "dg-snapshot"
id: "launching-the-web-administration"
menus: [ "developers", "developer-guide", "Snapshot" ]
---

Roboconf's Web Administration is a web application developed with [AngularJS](http://angularjs.org/).  
It comes a separate application and acts as a front-end for the DM. It is in fact a REST client with a web interface.

As explained on the [DM's installation page](../user-guide/installing-the-deployment-manager.html), the DM's web application
embeds this client by default.

However, it can be useful to run it separately (as an example, for test purpose).  
Get the sources of Roboconf's web administration.  

```tcl
git clone git://github.com/roboconf/roboconf-web-administration.git
```

> When the platform is built, it downloads a cached (zipped) distribution of
> the web administration and then wraps it into an OSGi bundle.

For development, it is possible (and recommended) to use a DM on one side, and
a stand-alone version of the web administration.
Read carefully [the console's readme file](https://github.com/roboconf/roboconf-web-administration/blob/master/readme.md).


# Reminder

All the dynamic part is handled by Javascript. This application is only made up
of static files. You could serve them from any web server.

> Do not open directly the *index.html* file into your web browser (file:/...).  
> Navigation will not work. You need the page to be served by a web server.

To test the DM's REST API, you might also prefer using another REST client.  
Remember, this application is just a front-end for the REST API exposed by the Deployment Manager.
Mozilla Firefox and Google Chrome have extensions to invoke REST services.


# Version 0.4 and higher

You must have [NPM](https://www.npmjs.org/) installed.  
Once NPM installed, go into the **roboconf-web-administration** folder, and type in

```properties
# Install Gulp
sudo npm install gulp -g

# Download Javascript dependencies
npm install

# Build the application for DEV mode
gulp watch-dev
```

This will build the application, watch for changes in the sources and start a small web server to serve the files.  
Then, assuming you are running it on your machine, open a web browser and go to

	http://localhost:8000/

Other details about Gulp tasks are given in the [web administration 's readme](https://github.com/roboconf/roboconf-web-administration).


# Older Versions

For versions prior to 0.4, you must have [NodeJS](http://nodejs.org/) and [NPM](https://www.npmjs.org/) installed.  
Once NPM installed, go into the **roboconf-web-administration** folder, and type in

```properties
# Download Javascript dependencies
npm install

# Start the application
npm start
```

This will start a small web server to serve the files.  
Then, assuming you are running it on your machine, open a web browser and go to

	http://localhost:8000/

