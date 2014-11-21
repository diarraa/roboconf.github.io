---
title: "Tutoriel ECOM"
layout: page
id: "ug.snapshot.tutoriel-ecom"
menus: [ "users", "user-guide" ]
---

Ce tutoriel vous propose de déployer sur Amazon Web Services (AWS) une pile logicielle 
incluant un équilibreur de charge (Apache avec ModJK), un serveur d'application (Glassfish)
et une base de données (MySQL).

La version de Roboconf utilisée ici est la 0.1.


## Paramétrer AWS

Si vous n'en avez pas déjà, créez-vous un compte sur [AWS](http://aws.amazon.com/fr/).    
Choisissez l'offre basique. La première année d'utilisation est gratuite, à condition que vous respectiez certains quotas.
Vous êtes en effet limités à des machines virtuelles **micro** et vous ne devez pas dépasser 750 
heures d'utilisation par mois. Ce sera amplement suffisant dans le cadre de ce projet.

Allez ensuite dans la console de gestion d'AWS.  
Une fois arrivé(e), changez de zone géographique (en haut à droite) et choisissez EU (Ireland).

Allez ensuite dans les paramètres de votre compte pour éditer vos paramètres de sécurité.  
Créez un jeu de clés d'accès (**access keys**). Téléchargez ces informations sous la forme d'un fichier CSV.
Celui-ci contient l'identifiant de votre clé, ainsi qu'une clé privée qui sera nécessaire plus tard.

> Ces paramètres de sécurité seront utilisés dans un fichier de Roboconf (le **iaas.properties**).  
> Il est crucial de prendre les précautions adéquates pour que ces informations ne se retrouvent
> pas en public (sur GitHub par exemple). Des indications vous seront données plus loin à ce sujet.

Allez ensuite dans l'interface d'EC2, menu **Groupes de Sécurité**.  
Créez-en un nouveau que vous appelerez **open**. Laissez les connexions sortantes telles quelles.
Pour les connexions entrantes, ajoutez 2 règles qui autorisent des connexions de n'importe où en mode TCP et UDP.
Rajoutez aussi une règle pour SSH. Sauvegardez le groupe (vous pourrez l'optimiser plus tard).

Enfin, allez dans le menu **Paire de Clés** et créez une nouvelle paire de clés.  
Cette clé sera utilisée quand vous vous connecterez en SSH sur des VM Amazon.  
Sauvegardez le fichier **pem** en local. Vous le passerez en paramètres des commandes SSH.

Voilà, vous êtes maintenant prêts à installer Roboconf.


## Installation de Roboconf

	Pour vos machines virtuelles, utilisez Ubuntu Server comme image de base.  
	Vous aurez aussi (parfois) à vous connecter en SSH. Pour rappel, la commande à utiliser est...
	
	ssh -i /path/to/your.pem ubuntu@ip-address

Si vous êtes plusieurs, vous pouvez paralléliser les 3 tâches suivantes.  
Les détails pour l'installation de chaque brique sont disponibles dans 
[le guide utilisateur](/en/user-guide-0.1/user-guide.html) (en anglais seulement).

1. Créez une machine virtuelle (VM) sur AWS et connectez-vous en SSH dessus.  
Installez RabbitMQ, puis créez une image depuis cette VM. La prochaine fois que vous aurez à utiliser
Roboconf, vous n'aurez ainsi qu'à instancier cette image plutôt que de tout réinstaller. Laissez la VM
tourner, vous allez l'utiliser par la suite. 

2. Deux options sont possibles pour l'installation du DM.  
Si votre machine peut accéder à un port exotique (celui de RabbitMQ), il est préférable d'installer le DM sur votre
machine. Autrement, créez une nouvelle VM sur AWS et connectez-vous en SSH dessus. Installez un serveur Apache Tomcat 
et [installez le DM](../telecharger.html) sur ce serveur. Encore une fois, référez-vous au 
[guide utilisateur](/en/user-guide-0.1/user-guide.html)  pour l'installation et la **configuration préalable** du DM.

3. Créez une machine virtuelle (VM) sur AWS et connectez-vous en SSH dessus.  
[Téléchargez l'agent](../telecharger.html) sur cette VM.

```
wget http://agent-url/...
```

Configurez le système pour que l'agent se lance au démarrage.  
Pour vérifiez que votre configuration est bonne, tapez **sudo reboot** et vérifiez qu'un fichier de log a bien été
créé dans le dossier **/logs** de l'agent. **Il est normal que le démarrage de l'agent échoue**. En mode normal,
le DM de Roboconf va positionner des informations que l'agent va lire au démarrage. Ces informations étant absentes
pour le moment, l'agent indique une erreur lorsqu'on le lance. Si vous comptez également utiliser [Puppet](http://puppetlabs.com/)
comme plug-in Roboconf, pensez à l'installer sur la même machine que l'agent. Enfin, pour vous faciliter la vie pour la suite
du TP, modifiez le fichier **logging.properties** de l'agent et modifiez le niveau de log à **FINEST** au lieu d'**INFO**.

Une fois votre machine avec l'agent opérationnelle, créez une image, puis détruisez la VM.


## Premiers Pas

Afin de comprendre ce qui va se passer, commencez par lire ces 2 pages.

* [Understanding Roboconf - part 1](/en/user-guide/lamp-example-part-1.html)
* [Understanding Roboconf - part 2](/en/user-guide/lamp-example-part-2.html)

Une fois cela fait, récupérez les sources des [exemples Roboconf](https://github.com/roboconf/roboconf-examples).  
Etudiez les projets **apache-tomcat-webapp** et **apache-tomcat-bash**. Vous allez commencer par en déployer un
pour vous familiariser avec Roboconf et son fonctionnement.

* Mettez à jour les fichiers **iaas.properties** pour y ajouter vos accès AWS.
* Celui-ci doit reprendre le [modèle pour EC2](/en/user-guide/iaas-aws.html).
* Spécifiez les propriétés de vos machines virtuelles (ID de l'image, groupe de sécurité...).
* Assurez-vous que le dossier contenant le **iaas.properties** englobe aussi un fichier *.gitignore*
ou *.svnignore* (selon l'endroit où vous allez sauvegarder les sources de votre projet).

> Cette dernière étape est très importante.  
> Elle évitera que d'autres n'utilisent votre compte pour créer des VM.

* Vous pouvez ensuite créer une archive qui englobe les trois dossiers **descriptor**, **instances**
et **graph**.
* Vous pouvez aussi utiliser Maven pour réaliser cette tâche. Non seulement le plug-in Maven pour
Roboconf va créer cette archive, mais il effectuera aussi une validation du projet. Pour utiliser Maven,
il vous suffit de vous inspirer de ce 
[pom.xml](https://github.com/roboconf/roboconf-maven-plugin/blob/master/src/test/projects/project--valid/pom.xml).
Pensez aussi à mettre à jour la structure des dossiers (**src/main/model**).

Mettez également à jour votre fichier ~/.m2/settings.xml.  
Vous devez activer un dépôt particulier pour que Maven sache où récupérer
le plug-in pour Roboconf.

```xml
<profiles>
	<profile>
		<repositories>
			<repository>
				<id>sonatype-snpashots</id>
				<name>Sonatype Snpashots</name>
				<snapshots>
					<enabled>true</enabled>
					<updatePolicy>never</updatePolicy>
					<checksumPolicy>fail</checksumPolicy>
				</snapshots>
				<url>https://oss.sonatype.org/content/repositories/snapshots</url>
			</repository>
		</repositories>
	</profile>
</profiles>
```

> Si vous utilisez le plug-in Maven, essayez de passer vos paramètres de sécurité AWS dans le **iaas.properties**,
> au travers de variables d'environnement et de Maven. Cela vous évitera de les écrire en dur. C'est
> typiquement ce qui est fait dans de vrais environnements de production.


Ouvrez ensuite l'interface d'administration de Roboconf. Celle-ci est embarquée par le DM.  
Si vous avez installé le DM sur votre machine, l'adresse par défaut est 
[http://localhost:8080/roboconf-dm-webapp/client](http://localhost:8080/roboconf-dm-webapp/client).

Chargez votre application et déployez-la via l'interface graphique de Roboconf.  
Vérifiez dans l'interface de AWS que des machines virtuelles ont bien été créées.  Assurez-vous que
l'application déployée fonctionne bien. Pour cela, connectez-vous sur le port 80 de la VM qui héberge le *load balancer* (Apache).

> En cas de problème, regardez les logs des divers agents Roboconf.

Une fois cette tâche réalisée, désinstallez les instances racines via Roboconf.  
Cela devrait détruire les machines virtuelles dans AWS. Assurez-vous en.


## Passage à Glassfish

Refaîtes le même exercice en remplaçant Tomcat par Glassfish.  
Vous pouvez soit utiliser Bash, soit utiliser Puppet pour l'installer.

Faîtes également évoluer le graphe pour y ajouter une 3ème niveau.  
Dans les exemples précédents, l'application web venait avec le serveur Tomcat. On installait donc, et Tomcat,
et une application web. Modifiez ce comportement en les séparant.

En résumé, vous devez enlever Tomcat de votre graphe, et y ajouter deux autres noeuds : Glassfish et WebApp, où
WebApp désigne l'application que vous réalisez dans le cadre du projet ECOM. Faîtes en sorte que le load balancer
ne soit reconfiguré que lorsque vous démarrerez cette application web. Choisissez l'extension de Roboconf que vous 
souhaitez utiliser (Bash ou Puppet) et compléter les scripts nécessaires.


## Pour Aller plus Loin

* Faîtes évoluer votre graphe pour déployer les autres briques nécessaires à votre projet.
* Retravaillez vos groupes de sécurité AWS pour limiter le nombre de ports accessibles.
* Améliorez votre image virtuelle avec l'agent en configurant **iptables** pour que seuls les ports nécessaires soient ouverts (SSH, RabbitMQ...).
* Retravaillez les scripts Bash ou Puppet de votre projet pour ouvrir ou fermer des ports via **iptables** lorsqu'une 
application démarre ou s'arrête.