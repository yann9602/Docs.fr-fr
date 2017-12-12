---
uid: aspnet/overview/owin-and-katana/an-overview-of-project-katana
title: "Une vue d’ensemble du projet Katana | Documents Microsoft"
author: howarddierking
description: "L’infrastructure ASP.NET a été autour pendant plus de dix ans, et la plateforme a activé le développement d’innombrables sites et services Web. En tant qu’application de Web..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/30/2013
ms.topic: article
ms.assetid: 0ee21741-c1bf-4025-a9b0-24580cae24bc
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/owin-and-katana/an-overview-of-project-katana
msc.type: authoredcontent
ms.openlocfilehash: 8f28116f88f3cf5143d3d5c9821519d62c4e5452
ms.sourcegitcommit: 6541c8b11001dd617adf5eb04c814cda165070b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
<a name="an-overview-of-project-katana"></a>Une vue d’ensemble du projet Katana
====================
par [Howard Dierking](https://github.com/howarddierking)

> L’infrastructure ASP.NET a été autour pendant plus de dix ans, et la plateforme a activé le développement d’innombrables sites et services Web. Comme les stratégies de développement d’applications Web ont évolué, l’infrastructure a été en mesure de faire évoluer à l’étape des technologies telles que ASP.NET MVC et API Web ASP.NET. Projet de développement d’applications Web prend sa étape suivante dans le monde du cloud computing, [Katana](https://channel9.msdn.com/Shows/Web+Camps+TV/The-Katana-Project-OWIN-for-ASPNET) fournit le jeu sous-jacent de composants pour les applications ASP.NET, ce qui leur permet d’être flexible et portable légère et offrir de meilleures performances : autrement dit, le projet [Katana](https://channel9.msdn.com/Shows/Web+Camps+TV/The-Katana-Project-OWIN-for-ASPNET) cloud permet d’optimiser vos applications ASP.NET.


## <a name="why-katana--why-now"></a>Pourquoi Katana – pourquoi maintenant ?

 Qu’une traite un produit de framework ou de l’utilisateur final de développeur, il est important de comprendre les motivations sous-jacent pour la création du produit – et la partie qui inclut connaître le produit a été créé pour. ASP.NET a été créé avec les deux clients à l’esprit.   
  
**Le premier groupe de clients a été les développeurs ASP classiques.** À l’heure, ASP a été une des principales technologies pour la création d’applications et des sites Web dynamiques, pilotés par les données par entrelacement de balisage et le script côté serveur. Le runtime ASP fourni un script côté serveur avec un ensemble d’objets qui extrait les principaux aspects du protocole HTTP sous-jacent et serveur Web et autant de l’accès à d’autres services telle session état des applications, mettre en cache, etc. Pendant puissant, les applications ASP classiques est devenue difficile à gérer comme elles ont augmenté en taille et en complexité. Ceci était due en grande partie de l’absence de structure figurant dans des environnements associées à la duplication de code qui résulte de l’entrelacement de code et de balises de script. Afin de profiter des avantages de ASP classique, tout en répondant à certaines de relever ces défis, ASP.NET a tiré parti de l’organisation de code fournie par les langages orientés objet du .NET Framework tout en conservant le modèle de programmation côté serveur pour le ASP classique développeurs avaient pris l’habitude.

**Le deuxième groupe de clients cibles pour ASP.NET a été les développeurs d’applications métier Windows.** Contrairement aux développeurs ASP classiques, qui ont été habitués à l’écriture de code HTML et le code pour générer un balisage HTML plus, les développeurs de WinForms (par exemple, les développeurs VB6 avant eux) ont été habitué à une expérience au moment du design qui incluait une zone de dessin et un ensemble complet de l’utilisateur contrôles d’interface. La première version de ASP.NET – également appelé « Web Forms » fourni une expérience au moment du design similaire avec un modèle d’événement du côté serveur pour les composants d’interface utilisateur et un ensemble de fonctions d’infrastructure (tels que ViewState) pour créer une expérience développeur transparente entre le client et la programmation du côté serveur. Web Forms hid efficacement la nature sans état du site Web sous un modèle d’événement avec état qui était familière aux développeurs de WinForms.

### <a name="challenges-raised-by-the-historical-model"></a>Défis déclenchés par le modèle historique

**Le résultat net est un modèle de programmation de développement et d’exécution déjà rodée, riche en fonctionnalités.** Toutefois, avec qui richesse de fonctionnalité est deux défis importants. Tout d’abord, l’infrastructure a été **monolithique**, avec des unités logiquement disparates de fonctionnalités est étroitement lié dans le même assembly System.Web.dll (par exemple, les objets de base HTTP avec l’infrastructure de formulaires Web). Deuxièmement, ASP.NET a été inclus dans le cadre de la plus grande .NET Framework, ce qui signifiait que la **temps entre les versions a l’ordre des années.** Cela rend difficile pour ASP.NET faire face à toutes les modifications effectuées dans le développement Web en constante évolution. Enfin, System.Web.dll lui-même a été couplée de différentes manières pour un site Web spécifique, option d’hébergement : Internet Information Services (IIS).

### <a name="evolutionary-steps-aspnet-mvc-and-aspnet-web-api"></a>Étapes : ASP.NET MVC et API Web ASP.NET

Et un grand nombre de modifications a été effectuée dans le développement Web ! Les applications Web plus sont développées que comme ayant le focus une série de petits composants plutôt que des infrastructures de grande taille. Le nombre de composants, ainsi que la fréquence à laquelle ils ont été publiées avait augmenté à un rythme jamais plus rapide. Il était clair qu’également suivre le rythme avec le site Web requiert infrastructures pour obtenir des plus petites, découplée et plus ciblée plutôt que de plus grands et plus complet, par conséquent la **équipe ASP.NET a eu plusieurs étapes pour activer ASP.NET en tant qu’une famille de les composants Web enfichables plutôt que d’une infrastructure unique**.

Une des premières modifications a été l’augmentation de la popularité du modèle de conception model-view-controller (MVC) grâce aux infrastructures de développement Web comme Ruby connue sur les Rails. Ce style de la génération d’applications Web a donné le développeur plus grand contrôle sur le balisage de son application tout en conservant la séparation de balisage et la logique métier, qui était un des points de vente initiales pour ASP.NET. Pour répondre à la demande pour ce style de développement d’applications Web, Microsoft a eu la possibilité de se positionner adaptés à l’avenir en **développement d’ASP.NET MVC hors bande** (et non le compris dans le .NET Framework). ASP.NET MVC a été publiée en tant qu’un téléchargement indépendant. L’équipe d’ingénierie a donné la possibilité de fournir des mises à jour plus fréquemment qu’il avait été précédemment possible.

Une autre avancée importante dans le développement d’applications Web ont des pages Web dynamiques générées par le serveur statiques balisage initiale avec les sections dynamiques de la page générée à partir d’un script côté client communiquent **avec le serveur principal API Web via Les requêtes AJAX**. Cette approche architecturale permettait d’assurer la propulsion l’essor des API Web et le développement de l’infrastructure de l’API Web ASP.NET. Comme dans le cas d’ASP.NET MVC, la version de l’API Web ASP.NET fourni une autre opportunité de faire évoluer plus comme une infrastructure plus modulaire de ASP.NET. L’équipe d’ingénierie a tiré parti de l’opportunité et **générés API Web ASP.NET de sorte que qu’elle n’avait aucuns dépendances sur un des types fondamentaux framework trouvés dans System.Web.dll**. Cette option activée deux choses : tout d’abord, elle signifiait que API Web ASP.NET peut évoluer de manière entièrement autonome (et il peut continuer à effectuer une itération rapidement, car il est fourni via NuGet). En second lieu, car il y a aucune dépendance externe à System.Web.dll et par conséquent, aucune dépendance sur IIS, ASP.NET Web API inclus permet de s’exécuter dans un hôte personnalisé (par exemple, une application console, le service Windows, etc.)

### <a name="the-future-a-nimble-framework"></a>L’avenir : Une infrastructure d’agilité

En dissociant les unes des autres composants de l’infrastructure et les libérant puis sur NuGet, infrastructures pourraient désormais **itérer plus rapidement et plus indépendamment**. En outre, la puissance et la flexibilité de la capacité d’auto-hébergement de l’API Web sont révélées très intéressant pour les développeurs qui voulaient une **hôte petit et léger** pour leurs services. Il a été identifié comme si attrayante, que d’autres infrastructures souhaitiez également cette fonctionnalité en fait, et cela exposés un nouveau défi que chaque framework exécuté dans son propre processus d’hôte sur sa propre adresse de base et devait être géré (démarré, arrêté, etc.) indépendamment. Une application Web moderne prend généralement en charge les serveurs de fichiers statiques, la génération de page dynamique, API Web et plus récemment real-time/des notifications push. Attend que chacun de ces services doit-elle être exécuté et géré indépendamment n’était tout simplement pas réaliste.

Ce qui a été nécessaire a été une abstraction d’hébergement unique qui pourraient permettre à un développeur composer une application à partir d’une variété de différents composants et des infrastructures, puis exécuter cette application sur un ordinateur hôte prenant en charge.

## <a name="the-open-web-interface-for-net-owin"></a>L’Interface Web ouverte pour .NET (OWIN)

 Inspirés par les avantages résultant de la [Rack](http://rack.github.io/) dans la Communauté Ruby, plusieurs membres de la Communauté .NET prévue pour créer une abstraction entre les serveurs Web et les composants de l’infrastructure. Deux objectifs de conception pour l’abstraction OWIN ont été qu’il était simple et nécessaire possibles de dépendances sur d’autres types de framework. Ces deux objectifs permettent de garantir :

- Nouveaux composants pourraient plus facilement développés et consommés.
- Applications peuvent être déplacées plus facilement entre les hôtes et potentiellement entière plates-formes/systèmes d’exploitation.

L’abstraction obtenue se compose de deux éléments principaux. Le premier est le dictionnaire d’environnement. Cette structure de données est chargée de stocker l’ensemble de l’état nécessaire pour le traitement d’une requête HTTP et de réponse, ainsi que n’importe quel état du serveur approprié. Le dictionnaire d’environnement est défini comme suit :

[!code-console[Main](an-overview-of-project-katana/samples/sample1.cmd)]

Un serveur compatible OWIN Web est chargé de remplir le dictionnaire d’environnement avec des données telles que le flux du corps et les collections d’en-têtes pour une requête et réponse HTTP. Il est alors la responsabilité des composants d’application ou votre infrastructure pour remplir ou de mettre à jour le dictionnaire avec des valeurs supplémentaires et d’écrire dans le flux du corps de réponse.

En plus de spécifier le type pour le dictionnaire d’environnement, la spécification OWIN définit une liste de paires de valeur de clé de dictionnaire principal. Par exemple, le tableau suivant montre les clés de dictionnaire requis pour une requête HTTP :

| Nom de clé | Description de la valeur |
| --- | --- |
| `"owin.RequestBody"` | Un flux avec le corps de la demande, le cas échéant. Stream.Null peut être utilisé comme espace réservé s’il n’existe aucun corps de la demande. Consultez [corps de la demande](http://owin.org/html/owin.html#34-request-body-100-continue-and-completed-semantics). |
| `"owin.RequestHeaders"` | Un `IDictionary<string, string[]>` des en-têtes de demande. Consultez [en-têtes](http://owin.org/html/owin.html#3-3-headers). |
| `"owin.RequestMethod"` | A `string` contenant la méthode de demande HTTP de la demande (par exemple, `"GET"`, `"POST"`). |
| `"owin.RequestPath"` | A `string` contenant le chemin d’accès de la demande. Le chemin d’accès doit être relatif à la « racine » du délégué de l’application ; consultez [chemins d’accès](http://owin.org/html/owin.html#5-3-paths). |
| `"owin.RequestPathBase"` | A `string` contenant la partie du chemin d’accès de demande correspondant à « root » du délégué de l’application ; consultez [chemins d’accès](http://owin.org/html/owin.html#5-3-paths). |
| `"owin.RequestProtocol"` | A `string` contenant le nom de protocole et la version (par exemple, `"HTTP/1.0"` ou `"HTTP/1.1"`). |
| `"owin.RequestQueryString"` | A `string` contenant le composant de chaîne de requête de l’URI, de la requête HTTP sans espace initial « ? » (par exemple, `"foo=bar&baz=quux"`). La valeur peut être une chaîne vide. |
| `"owin.RequestScheme"` | A `string` contenant le schéma d’URI utilisé pour la demande (par exemple, `"http"`, `"https"`) ; consultez [schéma d’URI](http://owin.org/html/owin.html#5-1-uri-scheme). |

Le deuxième élément clé de OWIN correspond au délégué de l’application. Il s’agit d’une signature de fonction qui sert d’interface principale entre tous les composants dans une application OWIN. La définition du délégué de l’application est la suivante :

`Func<IDictionary<string, object>, Task>;`

Le délégué de l’application est ensuite simplement une implémentation du type de délégué Func où la fonction accepte le dictionnaire d’environnement comme entrée et retourne une tâche. Cette conception a plusieurs conséquences pour les développeurs :

- Il existe un très petit nombre de dépendances de type nécessaires pour écrire des composants de OWIN. Cela augmente considérablement l’accessibilité de OWIN pour les développeurs.
- La conception asynchrone permet l’abstraction être efficace avec la gestion des ressources informatiques, en particulier dans d’autres opérations intensives d’e/s.
- Étant donné que le délégué de l’application est une unité atomique de l’exécution et car le dictionnaire d’environnement est exécuté en tant que paramètre le délégué, composants OWIN peuvent facilement être chaînés ensemble pour créer HTTP complexe pipelines de traitement.

Du point de vue de l’implémentation, OWIN est une spécification ([http://owin.org/html/owin.html](http://owin.org/html/owin.html)). Son objectif est de ne pas à l’infrastructure Web suivante, mais plutôt une spécification pour l’interagissent des infrastructures Web et les serveurs Web.

Si vous avez examiné [OWIN](http://owin.org/) ou [Katana](https://github.com/aspnet/AspNetKatana/wiki), vous aurez peut-être également remarqué la [package NuGet de Owin](http://nuget.org/packages/Owin) et Owin.dll. Cette bibliothèque contient une seule interface, [IAppBuilder](https://github.com/owin/owin/blob/master/src/Owin/IAppBuilder.cs), qui formalise et codifie décrite dans la séquence de démarrage [section 4](http://owin.org/html/owin.html#4-application-startup) de la spécification OWIN. Bien que non obligatoire pour pouvoir créer des serveurs OWIN, le [IAppBuilder](https://github.com/owin/owin/blob/master/src/Owin/IAppBuilder.cs) interface fournit un point de référence concrète, et il est utilisé par les composants du projet Katana.

## <a name="project-katana"></a>Projet Katana

Alors qu’à la fois le [OWIN](http://owin.org/html/owin.html) spécification et *Owin.dll* sont détenus et Communauté exécuter efforts d’ouvrir la source, le [Katana](https://github.com/aspnet/AspNetKatana/wiki) projet représente l’ensemble des OWIN composants qui, lors de la source encore ouverte, sont créées et publiées par Microsoft. Ces composants incluent les composants d’infrastructure, tels que les ordinateurs hôtes et serveurs, ainsi que des composants fonctionnels, tels que les composants d’authentification et de liaisons aux infrastructures telles que [SignalR](../../../signalr/index.md) et [Web ASP.NET API](../../../web-api/overview/getting-started-with-aspnet-web-api/index.md). Le projet possède les trois objectifs de haut niveau suivants : 

- **Portable** – composants doivent être en mesure de remplacer facilement pour les nouveaux composants qu’elles sont disponibles. Cela inclut tous les types de composants, à partir de l’infrastructure pour le serveur et l’hôte. Implication de cet objectif est que les infrastructures de tiers peuvent en toute transparence exécuter sur des serveurs Microsoft tandis que les infrastructures de Microsoft peuvent être exécutés sur des ordinateurs hôtes et des serveurs tiers.
- **Modulaire/flexible**– Contrairement à nombreuses infrastructures qui incluent un large éventail de fonctionnalités qui sont activées par défaut, les composants de projet Katana doivent être petit et ayant le focus, ce qui donne un contrôle au développeur d’applications pour déterminer quels composants de utiliser dans son application.
- **Lightweight/performant/évolutive** : en divisant la notion traditionnelle d’une infrastructure en un ensemble de petites, axée sur les composants qui sont ajoutés de manière explicite par le développeur d’applications, une application Katana résultante peut consommer moins de calcul ressources et par conséquent, gérer plus de charge, à avec d’autres types de serveurs et des infrastructures. Comme la configuration requise de l’application demande davantage de fonctionnalités à partir de l’infrastructure sous-jacente, celles peuvent être ajoutés au pipeline OWIN, mais qui doit être une décision explicite le développeur d’applications. En outre, la possibilité de substitution de composants de niveau inférieur signifie que lorsqu’elles sont disponibles, nouveaux serveurs hautes performances peuvent en toute transparence introduites pour améliorer les performances des applications OWIN sans interrompre les applications.

## <a name="getting-started-with-katana-components"></a>Mise en route avec les composants Katana

Lorsqu’il a été introduite, un aspect de la [Node.js](http://nodejs.org/) framework qui immédiatement exergue populaire a été la simplicité avec laquelle un peut créer et exécuter un serveur Web. Si les objectifs Katana ont été insérées à la lumière de [Node.js](http://nodejs.org/), un peut résumer les en disant que Katana offre un grand nombre des avantages de [Node.js](http://nodejs.org/) (et infrastructures comme il) sans obliger le développeur à lever tout ce dont elle sait sur le développement d’applications Web ASP.NET. Pour cette instruction contenir la valeur est true, mise en route avec le projet Katana doivent être aussi simples de nature à [Node.js](http://nodejs.org/).

## <a name="creating-hello-world"></a>Création de « Hello World ! »

Une différence notable entre JavaScript et le développement .NET est la présence (ou non) d’un compilateur. Par conséquent, le point de départ pour un serveur Katana simple est un projet Visual Studio. Toutefois, nous pouvons commencer avec le minimum de types de projets : l’Application Web ASP.NET vide.

[![](an-overview-of-project-katana/_static/image1.png)](http://nuget.org/packages/Microsoft.Owin.Host.SystemWeb)

Ensuite, nous allons installer le [Microsoft.Owin.Host.SystemWeb](http://nuget.org/packages/Microsoft.Owin.Host.SystemWeb) package NuGet dans le projet. Ce package fournit un serveur OWIN qui s’exécute dans le pipeline de demande ASP.NET. Il se trouve sur le [galerie NuGet](http://nuget.org/packages/Microsoft.Owin.Host.SystemWeb) et peut être installé à l’aide de la boîte de dialogue Gestionnaire de package Visual Studio ou de la console du Gestionnaire de package avec la commande suivante :

[!code-console[Main](an-overview-of-project-katana/samples/sample2.cmd)]

L’installation de le `Microsoft.Owin.Host.SystemWeb` installera quelques packages supplémentaires en tant que dépendances. Un de ces dépendances est `Microsoft.Owin`, une bibliothèque qui fournit plusieurs types d’assistance et des méthodes pour le développement d’applications OWIN. Nous pouvons utiliser ces types permettent d’écrire rapidement le serveur suivant « hello world ».

[!code-csharp[Main](an-overview-of-project-katana/samples/sample3.cs)]

Ce serveur Web très simple peut maintenant être exécuté à l’aide de Visual Studio **F5** commande et inclut la prise en charge complète pour le débogage.

## <a name="switching-hosts"></a>Hôtes de basculement

Par défaut, l’exemple « hello world » précédent s’exécute dans le pipeline de demande ASP.NET, qui utilise System.Web dans le contexte d’IIS. Cette opération peut isolément ajouter exceptionnel car il permet de tirer parti de la souplesse et la composabilité d’un pipeline OWIN avec les fonctionnalités de gestion et de maturité globale des services IIS. Toutefois, il peut arriver où les avantages fournis par les services IIS ne sont pas nécessaires et le désir est pour un ordinateur hôte le plus petit et plus léger. Ensuite, ce qui est nécessaire pour exécuter notre serveur Web simple en dehors d’IIS et System.Web ?

Pour illustrer l’objectif de la portabilité, le déplacement d’un hôte de serveur Web vers un hôte de la ligne de commande nécessite simplement en ajoutant les nouvelles dépendances de serveur et l’hôte pour le dossier de sortie du projet, puis en démarrant l’ordinateur hôte. Dans cet exemple, nous allons héberger notre serveur Web dans un hôte Katana appelé `OwinHost.exe` et utilisera le serveur Katana HttpListener. De même pour les autres composants Katana, ces seront acquis à partir de NuGet à l’aide de la commande suivante :

[!code-console[Main](an-overview-of-project-katana/samples/sample4.cmd)]

À partir de la ligne de commande, nous pouvons puis naviguez vers le dossier racine du projet et d’exécuter simplement le `OwinHost.exe` (lequel a été installé dans le dossier Outils de son package NuGet respectif). Par défaut, `OwinHost.exe` est configuré pour rechercher le serveur HttpListener et par conséquent, aucune configuration supplémentaire n’est nécessaire. Navigation dans un navigateur Web pour `http://localhost:5000/` montre l’application en cours d’exécution via la console.

![](an-overview-of-project-katana/_static/image2.png)

## <a name="katana-architecture"></a>Architecture d’arrivée Katana

 L’architecture du composant Katana divise une application en quatre couches logiques, comme indiqué ci-dessous : *hôte, le serveur, intergiciel (middleware),* et *application*. L’architecture de composant est prises en charge de manière à ce que les implémentations de ces couches peuvent être facilement remplacées, dans de nombreux cas, sans nécessiter de recompilation de l’application.   

![](an-overview-of-project-katana/_static/image3.png)

## <a name="host"></a>Hôte

 L’hôte est responsable de :

- La gestion des processus sous-jacent.
- Orchestration de flux de travail qui entraîne la sélection d’un serveur et de la construction d’un pipeline OWIN via les demandes est traité.

 Actuellement, il existe 3 options d’hébergement principales pour les applications Katana :  
  
**IIS/ASP.NET**: à l’aide des types standard HttpModule et HttpHandler, OWIN pipelines peuvent s’exécutent sur IIS dans le cadre d’un flux de demandes ASP.NET. Hébergement de prise en charge ASP.NET est activée en installant le package NuGet de Microsoft.AspNet.Host.SystemWeb dans un projet d’application Web. En outre, comme IIS agit comme un hôte et un serveur, la différence de serveur/hôte OWIN va de pair dans ce package NuGet, ce qui signifie que si vous utilisez l’hôte SystemWeb, un développeur ne peut pas remplacer une implémentation d’un autre serveur.  
  
**Hôte personnalisé**: suite de composant l’interconnexions donne au développeur la possibilité d’héberger des applications dans son propre processus personnalisé, si c’est une application console, un service Windows, un périphérique. Cette fonctionnalité est similaire à la capacité d’auto-hébergement fournie par l’API Web. L’exemple suivant montre un hôte personnalisé de code de l’API Web :  

[!code-csharp[Main](an-overview-of-project-katana/samples/sample5.cs)]

Le programme d’installation pour une application Katana auto-hébergement est similaire :

[!code-csharp[Main](an-overview-of-project-katana/samples/sample6.cs)]

Une différence notable entre l’API Web et Katana auto-hébergement des exemples est que le code de configuration Web API est manquant dans l’exemple d’auto-hébergement Katana. Pour permettre la portabilité et composabilité, Katana sépare le code qui démarre le serveur à partir du code qui configure le pipeline de traitement des demandes. Le code qui configure l’API Web, puis est contenu dans la classe de démarrage, qui est également spécifié comme paramètre de type dans WebApplication.Start.

[!code-csharp[Main](an-overview-of-project-katana/samples/sample7.cs)]

La classe de démarrage sera abordée plus en détail plus loin dans l’article. Toutefois, le code requis pour démarrer un Katana auto-héberger un processus semble étonnamment similaire au code que vous utilisez peut-être aujourd'hui dans les applications d’auto-hébergement API Web ASP.NET.

**OwinHost.exe**: alors que certains souhaitez écrire un processus personnalisé pour exécuter des applications Web de Katana, nombreux préférez simplement lancer un exécutable avant génération qui peut démarrer un serveur et exécuter l’application. Pour ce scénario, la suite de composant Katana inclut `OwinHost.exe`. Exécutée à partir d’un répertoire de projet racine, ce fichier exécutable est démarrer un serveur (il utilise le serveur HttpListener par défaut) et utilise des conventions pour rechercher et exécuter la classe de démarrage de l’utilisateur. Pour un contrôle plus précis, l’exécutable fournit un nombre de paramètres de ligne de commande supplémentaires.

![](an-overview-of-project-katana/_static/image4.png)

## <a name="server"></a>Serveur

 Alors que l’hôte est chargé de démarrage et de gestion des processus dans lequel l’application s’exécute, la responsabilité du serveur est à ouvrir un socket de réseau, écouter les demandes et les envoyer via le pipeline de composants OWIN spécifié par l’utilisateur (comme vous avez pu le constater, ce pipeline est spécifié dans la classe de démarrage du développeur de l’application). Actuellement, le projet Katana inclut deux implémentations de serveur : 

- **Microsoft.Owin.Host.SystemWeb**: comme indiqué précédemment, IIS de concert avec le pipeline ASP.NET agit comme un hôte et un serveur. Par conséquent, lors du choix de cette option d’hébergement, IIS à la fois gère des problèmes de niveau hôte, tels que l’activation de processus et écoute les requêtes HTTP. Pour les applications Web ASP.NET, il envoie ensuite les demandes dans le pipeline ASP.NET. L’hôte Katana SystemWeb inscrit une ASP.NET HttpModule et un HttpHandler pour intercepter les demandes qu’ils sont acheminées via le pipeline HTTP et les envoyer via le pipeline OWIN spécifié par l’utilisateur.
- **Microsoft.Owin.Host.HttpListener**: comme son nom l’indique, ce serveur Katana utilise HttpListener (classe) du .NET Framework pour ouvrir un socket et envoyer des demandes dans un pipeline OWIN spécifié par le développeur. Il s’agit actuellement de la sélection du serveur par défaut pour les API d’auto-hébergement Katana et OwinHost.exe.

## <a name="middlewareframework"></a>Intergiciel (middleware) / framework

 Comme mentionné précédemment, lorsque le serveur accepte une demande d’un client, il est chargé de passer par un pipeline de composants OWIN, qui sont spécifiées par le code de démarrage du développeur. Ces composants de pipeline sont appelées intergiciel (middleware).  
 À un niveau très simple, un composant d’intergiciel (middleware) OWIN doit simplement implémenter le délégué d’application OWIN afin qu’il soit pouvant être appelé.

[!code-console[Main](an-overview-of-project-katana/samples/sample8.cmd)]

Toutefois, afin de simplifier le développement et la composition des composants d’intergiciel (middleware), Katana prend en charge un certain nombre de conventions et types d’assistance pour les composants d’intergiciel (middleware). La plus courante de ces est la `OwinMiddleware` classe. Un composant d’intergiciel (middleware) personnalisés créé à l’aide de cette classe serait similaire à ce qui suit : 

[!code-csharp[Main](an-overview-of-project-katana/samples/sample9.cs)]

 Cette classe dérive `OwinMiddleware`, implémente un constructeur qui accepte une instance de l’intergiciel (middleware) suivant dans le pipeline en tant qu’un de ses arguments, puis le transmet au constructeur de base. Arguments supplémentaires utilisés pour configurer l’intergiciel (middleware) sont également déclarés en tant que paramètres du constructeur après le paramètre d’intergiciel (middleware) suivant.   
  
Lors de l’exécution, l’intergiciel (middleware) est exécutée via le substituée `Invoke` (méthode). Cette méthode prend un argument unique de type `OwinContext`. Cet objet de contexte est fourni par le `Microsoft.Owin` package NuGet décrites précédemment et fournit un accès fortement typé pour le dictionnaire de demande, de réponse et d’environnement, ainsi que quelques types d’assistance supplémentaires.   
  
La classe de l’intergiciel (middleware) peut être facilement ajoutée au pipeline OWIN dans le code de démarrage d’application comme suit :   

[!code-csharp[Main](an-overview-of-project-katana/samples/sample10.cs)]

Étant donné que l’infrastructure Katana crée simplement un pipeline de composants d’intergiciel (middleware) OWIN, et étant donné que les composants souhaitent prendre en charge le délégué de l’application devant être inclus dans le pipeline, les composants d’intergiciel (middleware) peuvent varier complexes simple enregistreurs d’événements à des infrastructures entières comme ASP.NET, API Web, ou [SignalR](../../../signalr/index.md). Par exemple, ajout d’API Web ASP.NET au pipeline OWIN précédent requiert d’ajouter le code de démarrage suivantes :

[!code-csharp[Main](an-overview-of-project-katana/samples/sample11.cs)]

L’infrastructure Katana générera le pipeline de composants d’intergiciel (middleware) selon l’ordre dans lequel ils ont été ajoutés à l’objet IAppBuilder dans la méthode de Configuration. Puis, dans notre exemple, LoggerMiddleware peut gérer toutes les demandes qui transitent par le pipeline, quelle que soit la façon dont ces demandes sont gérées de finalement. Cela permet de nombreux scénarios où un composant d’intergiciel (middleware) (par exemple, un composant d’authentification) peut traiter les demandes pour un pipeline qui inclut plusieurs infrastructures (par exemple, API Web ASP.NET SignalR et un serveur de fichiers statiques) et les composants.
 
## <a name="applications"></a>Applications

Comme illustré dans les exemples précédents, OWIN et le projet Katana ne doivent pas être considérés comme un nouveau modèle de programmation d’application, mais plutôt comme une abstraction pour séparer les modèles de programmation d’application et des infrastructures de serveur et l’infrastructure d’hébergement. Par exemple, lors de la création d’applications de l’API Web, le framework developer continue à utiliser l’infrastructure API Web ASP.NET, indépendamment de si l’application s’exécute dans un pipeline OWIN à l’aide de composants à partir du projet Katana. Le seul emplacement où code OWIN lié sera visible pour le développeur d’applications sera le code de démarrage d’application, où le développeur compose le pipeline OWIN. Dans le code de démarrage, le développeur enregistre une série d’instructions UseXx, un pour chaque composant d’intergiciel (middleware) qui traitera les demandes entrantes. Cette expérience aura le même effet que l’enregistrement des modules HTTP dans le monde System.Web actuel. En règle générale, une plus grande framework intergiciel (middleware), telles que les API Web ASP.NET ou [SignalR](../../../signalr/index.md) sera inscrit à la fin du pipeline. Composants d’intergiciel (middleware) composites, telles que celles pour l’authentification ou la mise en cache, sont généralement enregistrés jusqu’au début du pipeline afin qu’elles seront traiter les demandes pour toutes les infrastructures et des composants enregistrés plus loin dans le pipeline. Cette séparation des composants d’intergiciel (middleware) de l’autre et à partir des composants d’infrastructure sous-jacente Active les composants d’évoluer à différentes vitesses tout en garantissant que l’ensemble du système reste stable.

## <a name="components--nuget-packages"></a>Composants : les Packages NuGet

Comme de nombreuses bibliothèques actuels et des infrastructures, les composants du projet Katana sont remis en tant qu’ensemble de packages NuGet. Pour la prochaine version 2.0, le graphique de dépendance de package Katana se présente comme suit. (Cliquez sur l’image pour agrandir la vue.)

[![](an-overview-of-project-katana/_static/image6.png)](an-overview-of-project-katana/_static/image5.png)

Presque chaque package dans le projet Katana dépend, directement ou indirectement, le package Owin. Vous vous souvenez peut-être qu’il s’agit du package qui contient l’interface IAppBuilder, qui fournit une implémentation concrète de la séquence de démarrage d’application décrite dans la section 4 de la spécification OWIN. En outre, nombre des packages dépendent de Microsoft.Owin, qui fournit un ensemble de types d’assistance pour l’utilisation des requêtes et réponses HTTP. Le reste du package peut être classé en tant que packages infrastructure (serveurs ou ordinateurs hôtes) hébergement ou intergiciel (middleware). Packages et dépendances qui sont externes au projet Katana sont affichent en orange.

L’infrastructure d’hébergement pour Katana 2.0 inclut le SystemWeb et les serveurs HttpListener, le package OwinHost pour exécuter des applications OWIN à l’aide de OwinHost.exe et le package Microsoft.Owin.Hosting pour l’auto-hébergement applications OWIN dans un hôte personnalisé (par exemple, application console, le service Windows, etc.)

Pour Katana 2.0, les composants d’intergiciel (middleware) sont principalement axés sur les différents moyens d’authentification. Un composant d’intergiciel (middleware) supplémentaires pour les diagnostics est fourni, ce qui permet la prise en charge pour une page de démarrage et d’erreur. Que OWIN augmente dans l’abstraction d’hébergement de fait, augmente également l’écosystème de composants d’intergiciel (middleware), à la fois ceux développés par Microsoft et tiers, en nombre.

## <a name="conclusion"></a>Conclusion

 À partir du début, les objectifs du projet Katana n’a pas été pour créer et ainsi forcer les développeurs pour en savoir encore une autre infrastructure Web. Au lieu de cela, l’objectif a été pour créer une abstraction pour donner aux développeurs d’applications Web .NET plus de choix qu’il a précédemment été possible. En fractionnant les couches logiques d’une pile d’application Web par défaut dans un ensemble de composants remplaçables, le projet Katana, permet aux composants tout au long de la pile pour améliorer à quelle que soit la vitesse de sens pour ces composants. En créant tous les composants de l’abstraction OWIN simple, Katana permet d’infrastructures et les applications conçues pour eux soit portable sur divers ordinateurs hôtes et des serveurs différents. En plaçant le développeur dans le contrôle de la pile, Katana garantit que le développeur, le choix final sur comment léger ou comment riche sa pile Web doit être.  
  

## <a name="for-more-information-about-katana"></a>Pour plus d’informations sur les interconnexions

- Le projet Katana sur GitHub : [https://github.com/aspnet/AspNetKatana/](https://github.com/aspnet/AspNetKatana/).
- Vidéo : [le projet Katana - OWIN pour ASP.NET](https://channel9.msdn.com/Shows/Web+Camps+TV/The-Katana-Project-OWIN-for-ASPNET), par Howard Dierking.

## <a name="acknowledgements"></a>Remerciements

- [Rick Anderson](https://blogs.msdn.com/b/rickandy/): (twitter [ @RickAndMSFT ](http://twitter.com/RickAndMSFT) ) Rick est un writer de programmation senior pour Microsoft en mettant l’accent sur Azure et MVC.
- [Scott Hanselman](http://www.hanselman.com/blog/): (twitter [ @shanselman ](https://twitter.com/shanselman) )
- [Jon Galloway](https://weblogs.asp.net/jgalloway/default.aspx): (twitter [ @jongalloway ](https://twitter.com/jongalloway) )
