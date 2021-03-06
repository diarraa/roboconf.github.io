---
title: "Release Management"
layout: page
cat: "dg-snapshot"
id: "release-management"
menus: [ "developers", "developer-guide", "Snapshot" ]
---

Unlike continuous integration, releases must be performed **locally**.  
Performing a release means running specific Maven commands **on a Git repository**.  
We do not release a single Maven module, but a set of coherent projects. A Git repository defines the bounds of this coherence.


## Prerequisites

> It is assumed releases are performed from a secured machine.

1. You must have an account at [Sonatype.org](https://oss.sonatype.org/).
2. You must have been added as a manager on Roboconf @ Sonatype. See [this ticket](https://issues.sonatype.org/browse/OSSRH-11576).
3. You must have a signing key, generated with PGP. See [these instructions](http://central.sonatype.org/pages/working-with-pgp-signatures.html).
4. You must have [declared the server and your Sonatype credentials](http://maven.apache.org/plugins/maven-deploy-plugin/usage.html) in your `~/.m2/settings.xml` file.

```xml
<servers>
	<server>
		<id>sonatype-release-staging</id>
		<!--
		    You can also reference environment variables
		    for your credentials (e.g. ${MY_USER_NAME})
		-->
		<username>your username @ Sonatype</username>
		<password>your password @ Sonatype</password>
	</server>
</servers>
```


## Release Scripts

A project with release scripts is available on [Github](https://github.com/roboconf/roboconf-release-scripts).  
Clone it on your local repository. Scripts are numbered to indicate in which order they should be executed.

These scripts will check out the project from Github in the temporary directory.  
It is assumed the release is performed under Linux (Debian systems essentially), with a JDK 7 or JDK 8.

1\. **0.help.sh** indicates the process to follow.  
2\. Copy **1.conf.bintray.sh-sample** to **1.conf.bintray.sh**.  
Add your Bintray credentials in it. This file is already listed in the *.gitignore* file.

3\. Copy **1.conf.dockerhub.sh-sample** to **1.conf.dockerhub.sh**.  
Add your credentials for Docekr Hub in this file. It is already listed in the *.gitignore* file.

4\. Update the **1.conf.sh** file with the new release information.  
5\. Release the web administration first (**2.web-administration.sh**).  
6\. Release the platform then.

> Use **3.platform.sh** for major and minor releases (e.g. 0.6, 0.7, etc).  
> Use **3.platform.maintenance.sh** for maintenance releases (e.g. 0.6.1, 0.6.2, etc).

Then go to [http://oss.sonatype.org](http://oss.sonatype.org). Login and check the staging repositories.
You should find one about Roboconf. Verify the content and then click **close**. Sonatype will then verify
some quality rules about your upload (sources JAR, javadoc JAR...). Once this is done, click **release**.

7\. Wait for the platform to be available on [Maven Central](http://repo1.maven.org/maven2/net/roboconf).  
This can take some minutes (generally about 20 minutes). Next scripts can be executed in any order.
The order we defined is somehow arbitrary.


## Notes

The platform is the only project that stores releases on Maven.  
Some projects store their snapshots on Maven but not for releases (e.g. system installers).

For details about the manual release process, please refer to [this page](release-management-by-hand.html).
