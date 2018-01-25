---
uid: whitepapers/aspnet-and-web-tools-20122-release-notes
title: ASP.NET et Web 2012.2 Notes de version des outils | Documents Microsoft
author: rick-anderson
description: Notes de publication pour ASP.NET et Web Tools 2012.2.
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/14/2013
ms.topic: article
ms.assetid: bdb18d02-9f61-4676-836d-6fdea94f9282
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /whitepapers/aspnet-and-web-tools-20122-release-notes
msc.type: content
ms.openlocfilehash: 52559a47f86e572f873d4eaaab50e87eb51722fd
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="aspnet-and-web-tools-20122-release-notes"></a>Notes de version de ASP.NET et Web Tools 2012.2
====================
> Ce document décrit la version de ASP.NET et Web Tools 2012.2. Il est une mise à jour pour les outils de Visual Studio Web et ASP.NET.


- [Notes d’installation](#_Installation)
- [Documentation](#_Documentation)
- [Support](#_Support)
- [Configuration logicielle requise](#_Software_Requirements)
- [Nouvelles fonctionnalités dans ASP.NET et Web Tools 2012.2](#_New_Features_in)

    - [Outillage](#_Tooling)
    - [Publication Web](#_Web_Publishing)
    - [Modèles ASP.NET MVC](#_Templates)
    - [API Web ASP.NET](#_ASP.NET_Web_API)

    - [ASP.NET SignalR](#_ASP.NET_SignalR)
    - [URL conviviales ASP.NET](#_ASP.NET_Friendly_URLs)
- [Problèmes connus et les modifications avec rupture](#_Known_Issues_and)

<a id="_Installation"></a>
## <a name="installation-notes"></a>Notes d’installation

ASP.NET et 2012.2 des outils Web pour Visual Studio 2012 peuvent être installé à l’aide de [Web Platform installer](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=ASPDOTNETandWebTools2012_2). Il s’agit d’une mise à jour pour Visual Studio 2012 ou Visual Studio Express 2012 pour le Web, ce qui est nécessaire. Si vous n’avez pas installé Visual Studio, Visual Studio Express 2012 pour le Web sera installé.

Vous pouvez également installer manuellement ASP.NET et Web Tools 2012.2. Vous devez disposer de Visual Studio 2012 ou Visual Studio Express 2012 pour le Web installé. Puis suivez les instructions suivantes : 

1. Télécharger [ASP.NET et Web Frameworks 2012.2](https://download.microsoft.com/download/6/5/6/6562AFBE-9503-4E64-970C-1427133FCD73/AspNetWebTools2012Setup.exe) programme d’installation à partir du centre de téléchargement.
2. Lorsque demandées par invite cliquez sur Exécuter. Vous pouvez également enregistrer le fichier pour l’exécuter ultérieurement.
3. Vérifier la version de Visual Studio, vous mettrez à jour. Pour cela, lancez Visual Studio que vous souhaitez mettre à jour. Cliquez ensuite sur l’élément de menu d’aide.   
    ![](aspnet-and-web-tools-20122-release-notes/_static/image1.jpg)
4. Si vous voyez l’élément de menu &quot;sur Microsoft Visual Studio 2012 pour Web&quot; puis téléchargez [2012.2 outils de développement Web - Visual Studio Express 2012 pour Web](https://go.microsoft.com/fwlink/?LinkID=282228). Sinon, téléchargez [2012.2 outils de développement Web - Visual Studio 2012](https://go.microsoft.com/fwlink/?LinkID=282228).
5. Lorsque demandées par invite cliquez sur Exécuter. Vous pouvez également enregistrer le fichier pour l’exécuter ultérieurement.

> [!NOTE]
> Version d’ASP.NET et Web Tools 2012.2 n’inclut pas de SQL Server Data Tools. SQL Server et les bases de données SQL Windows Azure fournit un ensemble plus riche de base de données pour les outils, notamment le développement de reposant sur un projet hors connexion, de comparaison de schémas et de fonctionnalités de déploiement de base de données améliorée. Pour plus d’informations ou pour installer SQL Server Data Tools visitez [https://go.microsoft.com/fwlink/?LinkID=237127](https://go.microsoft.com/fwlink/?LinkID=237127).

<a id="_Documentation"></a>
## <a name="documentation"></a>Documentation

Didacticiels et autres informations sur ASP.NET et Web Tools 2012.2 sont disponibles à partir du site web ASP.NET (https://www.asp.net).

<a id="_Support"></a>
## <a name="support"></a>Assistance

ASP.NET et Web Tools 2012.2 est officiellement publié et pris en charge. Vous pouvez utiliser votre canal de prise en charge normale. Vous pouvez également poser des questions sur les forums ASP.NET ([https://forums.asp.net/](https://forums.asp.net/)), où les membres de la Communauté ASP.NET peuvent souvent fournir un support informel.

<a id="_Software_Requirements"></a>
## <a name="software-requirements"></a>Configuration logicielle

ASP.NET et Web Tools 2012.2 nécessite Visual Studio 2012 ou Visual Studio Express 2012 pour le Web.

<a id="_New_Features_in"></a>
## <a name="new-features-in-aspnet-and-web-tools-20122"></a>Nouvelles fonctionnalités dans ASP.NET et Web Tools 2012.2

Cette section décrit les fonctionnalités qui ont été introduites dans la version d’ASP.NET et Web Tools 2012.2.

<a id="_Tooling"></a>
### <a name="tooling"></a>Outillage

- Inspecteur de page 

    - Prend en charge JavaScript mappage de sélection permettant l’inspecteur de Page mapper les éléments qui ont été ajoutés dynamiquement à la page sur le code JavaScript correspondant.
    - La possibilité de voir les mises à jour CSS en temps réel.
    - Pour plus d’informations, consultez [la synchronisation automatique CSS et JavaScript de sélection de mappage dans l’inspecteur de Page](https://blogs.msdn.com/b/webdev/archive/2012/12/14/css-auto-sync-and-javascript-selection-mapping-in-page-inspector.aspx).
- Éditeur 

    - Prend en charge la mise en surbrillance de syntaxe pour CoffeeScript, Mustache, guidons et JsRender.
    - L’éditeur HTML fournit la fonctionnalité Intellisense pour les liaisons de masquage.
    - MOINS modification et le compilateur prend en charge pour activer la génération dynamique CSS à l’aide d’inférieur.
    - Coller le code JSON comme une classe .NET. À l’aide de cette commande Collage spécial pour coller des JSON dans un langage c# ou VB.NET fichier de code et Visual Studio génère automatiquement les classes .NET à partir de l’objet JSON.
- Prise en charge de l’émulateur mobile ajoute raccordements d’extensibilité de sorte que les émulateurs de tiers peuvent être installés comme une extension VSIX. Les émulateurs installés seront afficheront dans la liste déroulante F5, afin que les développeurs peuvent afficher un aperçu de leurs sites Web sur une variété d’appareils mobiles. En savoir plus sur cette fonctionnalité dans l’entrée de blog de Scott Hanselman sur [la nouvelle BrowserStack intégration avec Visual Studio](http://www.hanselman.com/blog/CrossBrowserDebuggingIntegratedIntoVisualStudioWithBrowserStack.aspx).

<a id="_Web_Publishing"></a>
### <a name="web-publishing"></a>Publication Web

- Projets de site Web ont maintenant la même expérience de publication en tant que projets d’Application Web, y compris la publication pour les Sites Web Windows Azure.
- Sélective Publier &#8211; pour un ou plusieurs fichiers, vous pouvez effectuer les actions suivantes (après la publication à un point de terminaison Web Deploy) : 

    - Publier les fichiers sélectionnés.
    - Voir la différence entre un fichier local et un fichier distant.
    - Mettre à jour le fichier local avec le fichier distant ou mettre à jour le fichier distant avec le fichier local.

<a id="_Templates"></a>
### <a name="aspnet-mvc-templates"></a>Modèles ASP.NET MVC

- Le nouveau modèle d’Application Facebook facilite l’écriture de canevas Facebook facilement des applications. En quelques étapes simples, vous pouvez créer une application Facebook qui obtient des données à partir d’un utilisateur connecté et s’intègre à ses amis. Le modèle inclut une nouvelle bibliothèque pour prendre en charge de tous les raccordements qu’implique la création d’une application Facebook, notamment l’authentification, les autorisations, l’accès aux données Facebook et bien plus encore. Pour plus d’informations sur l’utilisation du modèle d’Application Facebook [https://go.microsoft.com/fwlink/?LinkID=269921](https://go.microsoft.com/fwlink/?LinkID=269921).
- Un nouveau modèle MVC d’Application à Page unique permet aux développeurs de créer des applications web côté client interactives à l’aide de HTML 5, CSS 3 et prisées Knockout et jQuery JavaScript bibliothèques, en plus de l’API Web ASP.NET. Le modèle inclut une application de liste de « todo » qui montre les pratiques courantes de création d’une application JavaScript HTML5 qui utilise une API de serveur RESTful. Vous pouvez en savoir plus à [https://www.asp.net/single-page-application](../single-page-application/index.md).
- Vous pouvez maintenant créer une extension VSIX qui ajoute de nouveaux modèles à la boîte de dialogue projet ASP.NET MVC. Découvrez comment ici : [https://go.microsoft.com/fwlink/?LinkId=275019](https://go.microsoft.com/fwlink/?LinkId=275019)
- FixedDisplayModes package &#8211; Modèles de projet MVC ont été mis à jour pour inclure le nouveau package NuGet de 'FixedDisplayModes', qui contient une solution de contournement pour un bogue dans MVC 4. Pour plus d’informations sur le correctif contenu dans le package, consultez ce billet de blog ([https://blogs.msdn.com/b/rickandy/archive/2012/09/17/asp-net-mvc-4-mobile-caching-bug-fixed.aspx](https://blogs.msdn.com/b/rickandy/archive/2012/09/17/asp-net-mvc-4-mobile-caching-bug-fixed.aspx)) à partir de l’équipe MVC.

<a id="_ASP.NET_Web_API"></a>
### <a name="aspnet-web-api"></a>API web ASP.NET

API Web ASP.NET a été amélioré avec plusieurs nouvelles fonctionnalités :

- ASP.NET Web API OData
- Suivi de l’API Web ASP.NET
- Page d’aide de l’API Web ASP.NET

#### <a name="aspnet-web-api-odata"></a>ASP.NET Web API OData

API Web ASP.NET OData vous donne la flexibilité que nécessaire pour générer des points de terminaison OData avec une logique métier riches sur n’importe quelle source de données. ASP.NET Web API OData vous permet de contrôler la quantité de sémantique OData que vous souhaitez exposer. ASP.NET Web API OData est fourni avec les modèles de projet ASP.NET MVC 4 et est également disponible à partir de NuGet ([http://www.nuget.org/packages/microsoft.aspnet.webapi.odata](http://www.nuget.org/packages/microsoft.aspnet.webapi.odata)).

ASP.NET Web API OData prend actuellement en charge les fonctionnalités suivantes :

- Activer la sémantique de la requête OData en appliquant l’attribut [Queryable].
- Valider les requêtes OData et restreindre l’ensemble des options de requête pris en charge, les opérateurs et fonctions facilement.
- Liaison de paramètre pour ODataQueryOptions directement pour obtenir une représentation d’arborescence de syntaxe abstraite de la requête qui peut être validée et appliquée en IQueryable ou IEnumerable.
- Activer la pagination orientée sur le service et nouvelle génération de lien de page en spécifiant le nombre de résultats sur l’attribut de [Queryable].
- Demander un nombre inline du nombre total de ressources correspondants à l’aide de $inlinecount.
- Contrôle la propagation null.
- Un ou tous les opérateurs $filter.
- Déduire un entity data model par convention ou explicitement personnaliser un modèle de manière très similaire à Entity Framework Code en premier.
- Entité d’exposition définit en dérivant de EntitySetController.
- Conventions de simples et personnalisables pour exposer les propriétés de navigation, manipuler des liens et l’implémentation des actions OData.
- Simplifié de routage à l’aide de la méthode d’extension MapODataRoute.
- Prise en charge pour le contrôle de version en exposant plusieurs modèles EDM.
- Exposer $metadata et document de service vous pouvez générer des clients (.NET, Windows Phone, Windows Store, etc.) pour votre API Web.
- Prise en charge pour les formats documentés OData Atom, JSON et JSON.
- Créer, mettre à jour, partiellement mise à jour (PATCH) et supprimer des entités.
- Interroger et manipuler des relations entre des entités.
- Créer des liens de relation associer vos itinéraires.
- Types complexes.
- Héritage de Type d’entité.
- Propriétés de la collection.
- Enums.
- Actions OData.
- Basé sur les mêmes fondations que WCF Data Services, à savoir ODataLib ([http://www.nuget.org/packages/microsoft.data.odata](http://www.nuget.org/packages/microsoft.data.odata)).

Pour plus d’informations sur OData d’API Web ASP.NET, consultez [https://go.microsoft.com/fwlink/?LinkId=271141](https://go.microsoft.com/fwlink/?LinkId=271141).

#### <a name="aspnet-web-api-tracing"></a>Suivi de l’API Web ASP.NET

API Web ASP.NET Tracing intègre des données de suivi de votre API web avec le suivi de .NET. Il est maintenant activée par défaut dans le modèle de projet d’API Web. Données de suivi pour votre site web API est envoyé à la fenêtre Sortie et est rendu disponible via IntelliTrace. Le suivi d’API Web ASP.NET vous permet des informations de trace sur votre API Web lorsqu’il est hébergé dans Windows Azure grâce à une intégration [Diagnostics Windows Azure](https://msdn.microsoft.com/library/windowsazure/hh411529.aspx). Vous pouvez également installer et activer le suivi d’API Web ASP.NET dans les applications qui utilisent le package NuGet de traçage ASP.NET Web API ([http://www.nuget.org/packages/microsoft.aspnet.webapi.tracing](http://www.nuget.org/packages/microsoft.aspnet.webapi.tracing)).

Pour plus d’informations sur la configuration et à l’aide d’ASP.NET Web API Tracing [https://go.microsoft.com/fwlink/?LinkID=269874](https://go.microsoft.com/fwlink/?LinkID=269874).

#### <a name="aspnet-web-api-help-page"></a>Page d’aide de l’API Web ASP.NET

La Page d’aide ASP.NET Web API est désormais incluse par défaut dans le modèle de projet d’API Web. La Page d’aide ASP.NET Web API génère automatiquement la documentation pour le web API, y compris les points de terminaison HTTP, les méthodes HTTP prises en charge, les paramètres et les charges de message de demande et de réponse exemple. Documentation est automatiquement extraite de commentaires dans votre code. Vous pouvez également ajouter la Page d’aide ASP.NET Web API pour les applications qui utilisent le package NuGet de Page aide ASP.NET Web API ([http://www.nuget.org/packages/microsoft.aspnet.webapi.helppage](http://www.nuget.org/packages/microsoft.aspnet.webapi.helppage)).

Pour plus d’informations sur la configuration et la personnalisation de la voir Page d’aide ASP.NET Web API [https://go.microsoft.com/fwlink/?LinkId=271140](https://go.microsoft.com/fwlink/?LinkId=271140).

<a id="_ASP.NET_SignalR"></a>
### <a name="aspnet-signalr"></a>SignalR ASP.NET

ASP.NET SignalR facilite ajouter des fonctionnalités web en temps réel à votre application ASP.NET, à l’aide du protocole WebSocket s’il est disponible et automatiquement revenir à d’autres techniques lorsqu’il n’est pas.

Pour plus d’informations sur l’utilisation d’ASP.NET SignalR [https://go.microsoft.com/fwlink/?LinkId=271271](https://go.microsoft.com/fwlink/?LinkId=271271).

<a id="_ASP.NET_Friendly_URLs"></a>
### <a name="aspnet-friendly-urls"></a>URL conviviales ASP.NET

ASP.NET FriendlyURLs facilite pour les développeurs web forms Générer recherche nettoyeur des URL (sans l’extension .aspx). Il nécessite peu d’aucune configuration et peut être utilisé avec les applications ASP.NET v4.0 existantes. La fonctionnalité FriendlyURLs facilite également aux développeurs d’ajouter la prise en charge mobile à leurs applications, en prenant en charge le basculement entre les vues de bureau et mobiles.

Pour plus d’informations sur l’installation et à l’aide des URL conviviales ASP.NET [http://www.hanselman.com/blog/IntroducingASPNETFriendlyUrlsCleanerURLsEasierRoutingAndMobileViewsForASPNETWebForms.aspx](http://www.hanselman.com/blog/IntroducingASPNETFriendlyUrlsCleanerURLsEasierRoutingAndMobileViewsForASPNETWebForms.aspx).

<a id="_Known_Issues_and"></a>
## <a name="known-issues-and-breaking-changes"></a>Problèmes connus et les modifications avec rupture

Cette section décrit les problèmes connus et les modifications avec rupture qui se trouvent dans la version d’ASP.NET et Web Tools 2012.2.

### <a name="installation-issues"></a>Problèmes d’installation

#### <a name="out-of-order-installs-of-visual-studio-2012"></a>Installe hors de Visual Studio 2012

Installation d’un supplémentaires référence (SKU) de Visual Studio 2012 après que l’installation d’ASP.NET et Web Tools 2012.2 nécessite une opération de réparation. Considérez la séquence suivante :

1. Installer Visual Studio 2012 Express pour le Web
2. Installez ASP.NET et Web Tools 2012.2
3. Installer Visual Studio 2012 Professional, Premium ou Ultimate

Étape 2 entraînerait uniquement l’installation des mises à jour pour Express pour le Web. Pour vous assurer que la référence (SKU) supplémentaire installé lors de l’étape 3 contienne la mise à jour, vous devrez réparer ASP.NET et Web Tools 2012.2 pour installer les mises à jour pour la dernière version. Cela s’applique également si les références (SKU) à l’étape 1 et 3 sont inversés.

#### <a name="installing-microsoft-aspnet-and-web-tools-20122-when-visual-studio-is-open"></a>L’installation de Microsoft ASP.NET et Web Tools 2012.2 lorsque Visual Studio est ouvert

Si VS est ouverte pendant l’installation de Microsoft ASP.NET et Web Tools 2012.2, Visual Studio peut finir dans un état incorrect. Il est recommandé aux utilisateurs de fermer toutes les instances de Visual Studio avant de procéder à l’installation.

#### <a name="canceling-aspnet-and-web-tools-20122-setup-in-the-middle-of-installation"></a>Annulation de l’installation d’ASP.NET et Web Tools 2012.2 au milieu de l’installation

L’annulation d’ASP.NET et Web Tools 2012.2 le programme d’installation au milieu de l’installation de laisser Visual Studio dans un état incorrect. Pour résoudre ce problème de suivre ces étapes : 

- Accédez à Ajout/Suppression programmes
- Désinstallez Microsoft ASP.NET et Web Tools 2012.2, le cas échéant.
- Réinstallez Microsoft ASP.NET et Web Tools 2012.2

#### <a name="after-uninstalling-aspnet-and-web-tools-20122-the-aspnet-mvc-4-templates-and-razor-v2-web-site-templates-are-missing"></a>Après la désinstallation de ASP.NET et Web Tools 2012.2 ASP.NET MVC 4 modèles et des modèles de Site Web Razor v2 sont manquants

Désinstallation de ASP.NET et Web Tools 2012.2 désinstallera également tous les ASP.NET MVC 4 et les modèles de sites Web Razor v2 à partir de Visual Studio 2012.

La solution consiste à réparer votre installation de Visual Studio 2012 pour réinstaller ASP.NET MVC 4 et les modèles de sites Web Razor v2.

### <a name="tooling-issues"></a>Problèmes d’outils

#### <a name="nuget-error-reported-during-project-creation"></a>Erreur de NuGet signalée lors de la création du projet

Après avoir installé ASP.NET et Web Tools 2012.2, vous pouvez voir l’erreur suivante lors de la création d’un projet MVC 4

![](aspnet-and-web-tools-20122-release-notes/_static/image1.png)

ASP.NET et Web Tools 2012.2 fourni NuGet 2.1 et mettre à l’extension de Visual Studio 2012. Dans certains cas, le programme d’installation VSIX ne pourront pas correctement mise à jour de l’extension VSIX. Les étapes suivantes permettent de résoudre le problème :

1. Démarrez Visual Studio 2012 en tant qu’administrateur
2. Accédez à outils -&gt;Extensions et mises à jour et désinstaller NuGet.
3. Fermez Visual Studio.
4. Accédez au dossier d’installation ASP.NET et Web Tools 2012.2 :

    1. Pour Visual Studio 2012 : **programme Files\Microsoft ASP.NET\ASP.NET Web Stack\Visual Studio 2012**
    2. Pour Visual Studio 2012 Express pour le Web : **Program Files\Microsoft ASP.NET\ASP.NET Web Stack\Visual Studio Express 2012 pour le Web**
5. Cliquez deux fois sur le NuGet.Tools.vsix réinstaller NuGet

### <a name="web-api-issues"></a>Problèmes d’API Web

#### <a name="parsing-issues-in-filter-and-datetime-literals"></a>L’analyse des problèmes dans $filter et les littéraux de date/heure

L’analyseur URI OData ne parvient pas à analyser correctement les littéraux datetime partielle. Par exemple, $filter = valeur datetime de début eq « 2012-12-31T12:00' ne parvient pas à analyser correctement. Une solution de contournement consiste à utiliser le $filter complète littéral, = valeur datetime de début eq « 2012-12-31T12:00:00 ».

#### <a name="odata-doesnt-support-case-insensitive-property-names"></a>OData ne prend pas en charge les noms de propriété pas la casse.

OData ne prend pas en charge les noms de propriétés de la casse dans les requêtes OData et le chemin d’accès odata. Consultez les éléments de travail :

- [http://aspnetwebstack.codeplex.com/workitem/366](http://aspnetwebstack.codeplex.com/workitem/366)
- [http://aspnetwebstack.codeplex.com/workitem/704](http://aspnetwebstack.codeplex.com/workitem/704)

Si les utilisateurs disposent d’une casse différente sur le côté serveur et côté client javascript, il seront probablement rencontrer ce problème. Ce problème est inhérent dans le protocole odata. Toutefois, de nombreux utilisateurs signale ce problème. Pour le contourner, les utilisateurs doivent corriger leur cas de l’URL.

#### <a name="default-odata-routing-conventions-doesnt-support-postput-on-navigation-property"></a>OData par défaut conventions de routage ne prend pas en charge POST/PUT sur la propriété de navigation.

OData par défaut conventions de routage ne prend pas en charge POST/PUT sur la propriété de navigation. Consultez l’élément de travail [http://aspnetwebstack.codeplex.com/workitem/366](http://aspnetwebstack.codeplex.com/workitem/366). Nous sommes pas cette convention couramment utilisée dans les conventions utilisées par défaut.

Pour le contourner, les utilisateurs ont besoin d’étendre la nouvelle convention de routage pour prendre en charge.

### <a name="facebook-template-issues"></a>Problèmes de modèle de Facebook

#### <a name="facebook-application-template-only-works-using-net-45"></a>Modèle d’Application Facebook fonctionne uniquement à l’aide de .NET 4.5

Vous devez sélectionner le .NET 4.5 dans la liste déroulante de framework dans la boîte de dialogue Nouveau projet pour voir le modèle d’Application Facebook dans ASP.NET MVC 4.

#### <a name="real-time-update-controller"></a>Contrôleur de mise à jour en temps réel

Le modèle d’Application Facebook permet à utilisateur facilement créer un contrôleur d’API Web pour gérer les mises à jour en temps réel à partir de Facebook. Si votre ordinateur de développement est derrière un NAT, votre contrôleur peuvent ne pas fonctionne sans configuration de réseau supplémentaire. Pour plus d’informations, consultez ici : [http://facebook.stackoverflow.com/questions/5259467/can-a-computer-behind-a-nat-router-receive-realtime-updates-from-facebook](http://facebook.stackoverflow.com/questions/5259467/can-a-computer-behind-a-nat-router-receive-realtime-updates-from-facebook)

#### <a name="query-string-values-conflict-with-facebook-oauth-parameters"></a>Requête de valeurs de chaîne sont en conflit avec les paramètres Facebook OAuth

Les champs suivants sont en conflit avec l’appel de la boîte de dialogue Facebook OAuth sauvegarder URL. N’ajoutez pas de vos propres valeurs de chaîne de requête avec les noms suivants : code, erreur, erreur\_description, l’erreur\_raison.

#### <a name="using-page-inspector-with-facebook-template"></a>À l’aide de l’inspecteur de Page avec le modèle pour Facebook

Vous ne pouvez pas utiliser la fonctionnalité de l’inspecteur de Page dans Visual Studio 2012 pendant le débogage de votre Application Facebook. L’inspecteur de Page ne prend pas en charge les iframes.

### <a name="single-page-application-template-issues"></a>Problèmes de modèle d’Application Page unique

#### <a name="with-jquery-19knockout-221-update-when-running-default-mvc-spa-project-new-todo-item-edit-enter-focus-event-is-not-handled-properly"></a>Avec JQuery 1.9/Knockout 2.2.1 mise à jour lors de l’exécution du projet de MVC SPA par défaut, nouvelle édition d’élément todo Entrez les événements de focus ne sont pas gérée correctement.

JQuery 1.9/Knockout 2.2.1 de mise à jour, lorsque vous exécutez le projet de MVC SPA par défaut, nouvelle édition d’élément todo entrez n’est plus le focus vers la zone d’édition pour les éléments todo après avoir entré le nouvel élément de tâche dans la liste de tâches.

Référence de la solution de contournement [http://knockoutjs.com/documentation/hasfocus-binding.html](http://knockoutjs.com/documentation/hasfocus-binding.html)et assurez-vous de correctif similaire à l’exemple de code suivant :

Fichier todo.model.js  
 fonction todolist(data), ajoutez suivantes :  
 **self.isSelected = ko.observable(false);**

fonction todoList.prototype.addTodo, ajoutez le texte blacked suivant :  
 **self.isSelected(true);**  
 self.newTodoTitle(&quot;&quot;);

Index.cshtml, ajoutez le texte blacked suivant :  
 &lt;écran de lier aux données =&quot;soumettre : addTodo&quot;&gt;  
 &lt;entrée de classe =&quot;addTodo&quot; type =&quot;texte&quot; lier aux données =&quot;valeur : newTodoTitle, espace réservé : 'Type ici pour ajouter', blurOnEnter : la valeur est true, **hasfocus : isSelected**, événement : {flou : addTodo}&quot; /&gt;  
 &lt;/form&gt;
