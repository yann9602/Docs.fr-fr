---
uid: visual-studio/overview/2013/release-notes
title: ASP.NET et Web Tools pour Visual Studio 2013 Release Notes | Documents Microsoft
author: microsoft
description: "Ce document décrit la version de ASP.NET et Web Tools pour Visual Studio 2013."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/17/2013
ms.topic: article
ms.assetid: 08815768-2702-42ae-ae85-0a59934a11d1
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /visual-studio/overview/2013/release-notes
msc.type: authoredcontent
ms.openlocfilehash: 10835c39d3bca752ed3068a23fecaaab56449e41
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-and-web-tools-for-visual-studio-2013-release-notes"></a>ASP.NET et Web Tools pour les Notes de publication Visual Studio 2013
====================
par [Microsoft](https://github.com/microsoft)

> Ce document décrit la version de ASP.NET et Web Tools pour Visual Studio 2013.


## <a name="contents"></a>Sommaire

- [Notes d’installation](#TOC1)
- [Documentation](#TOC2)
- [Configuration logicielle requise](#TOC4)

### <a name="new-features-in-aspnet-and-web-tools-for-visual-studio-2013"></a>Nouvelles fonctionnalités dans ASP.NET et Web Tools pour Visual Studio 2013

- [Un seul ASP.NET](#TOC6)
- [Nouvelle expérience de projet Web](#newproj)
- [Génération de modèles automatique ASP.NET](#scaffold)
- [Lien du navigateur](#browser-link)
- [Améliorations de l’éditeur de Visual Studio Web](#web-editor)
- [Prise en charge des applications Azure App Service Web dans Visual Studio](#waws)
- [Améliorations de la publication sur le Web](#publish)
- [NuGet 2.7](#nuget)
- [Web Forms ASP.NET](#TOC9)
- [ASP.NET MVC 5](#TOC10)
- [API Web ASP.NET 2](#TOC11)
- [ASP.NET SignalR](#TOC13)
- [Identité ASP.NET](#TOC8)
- [Composants Microsoft OWIN](#TOC7)
- [Entity Framework 6](#ef6)
- [ASP.NET Razor 3](#TOC14)
- [Suspension de l’application ASP.NET](#TOC15)
- [Problèmes connus et les modifications avec rupture](#knownissues)

<a id="TOC1"></a>
## <a name="installation-notes"></a>Notes d’installation

ASP.NET et Web Tools pour Visual Studio 2013 sont regroupés dans le programme d’installation principal et peut être téléchargé [ici](https://www.asp.net/downloads).

<a id="TOC2"></a>
## <a name="documentation"></a>Documentation

Didacticiels et autres informations sur ASP.NET et Web Tools pour Visual Studio 2013 sont disponibles à partir de la [site web ASP.NET](https://www.asp.net/).

<a id="TOC4"></a>
## <a name="software-requirements"></a>Configuration logicielle

ASP.NET et outils Web nécessite Visual Studio 2013.

<a id="TOC5"></a>
## <a name="new-features-in-aspnet-and-web-tools-for-visual-studio-2013"></a>Nouvelles fonctionnalités dans ASP.NET et Web Tools pour Visual Studio 2013

Les sections suivantes décrivent les fonctionnalités qui ont été introduites dans la version.

<a id="TOC6"></a>
## <a name="one-aspnet"></a>Un seul ASP.NET

Avec la version de Visual Studio 2013, nous avons pris une étape à unifier l’expérience de l’utilisation de technologies d’ASP.NET, afin que vous pouvez facilement combiner et correspondent à ceux que vous souhaitez. Par exemple, vous pouvez démarrer un projet à l’aide de MVC et facilement ajouter ultérieurement des pages Web Forms au projet ou structurer les API Web dans un projet Web Forms. Un seul ASP.NET est à rendre plus facile pour vous en tant que développeur faire ce que vous aimez dans ASP.NET. Quel que soit la choix de la technologie, vous pouvez avoir confiance que vous générez sur l’infrastructure sous-jacente approuvée d’un ASP.NET.

<a id="newproj"></a>
## <a name="new-web-project-experience"></a>Nouvelle expérience de projet Web

Nous avons amélioré l’expérience de création de projets web dans Visual Studio 2013. Dans le **nouveau projet Web ASP.NET** boîte de dialogue vous pouvez sélectionner le type de projet, vous souhaitez configurez n’importe quelle combinaison de technologies (Web Forms, MVC, Web API), configurez les options d’authentification et ajoutez un projet de test unitaire.

![Nouveau projet ASP.NET](release-notes/_static/image1.png)

La nouvelle boîte de dialogue permet de modifier les options d’authentification par défaut pour la plupart des modèles. Par exemple, lorsque vous créez un projet Web Forms ASP.NET vous pouvez sélectionner une des options suivantes :

- Aucune authentification
- Comptes d’utilisateur individuels (l’appartenance d’ASP.NET ou journal fournisseur sociaux dans)
- Comptes d’organisation (Active Directory dans une application internet)
- Authentification Windows (Active Directory dans une application intranet)

![Options d’authentification](release-notes/_static/image2.png)

Pour plus d’informations sur le nouveau processus de création de projets web, consultez [création de projets Web ASP.NET dans Visual Studio 2013](creating-web-projects-in-visual-studio.md). Pour plus d’informations sur les options d’authentification, consultez [ASP.NET Identity](#TOC8) plus loin dans ce document.

<a id="scaffold"></a>
## <a name="aspnet-scaffolding"></a>Génération de modèles automatique ASP.NET

Génération de modèles automatique ASP.NET est une infrastructure de génération de code pour les applications Web ASP.NET. Elle rend faciles à ajouter du code réutilisable à votre projet interagit avec un modèle de données.

Dans les versions précédentes de Visual Studio, la structure a été limitée aux projets ASP.NET MVC. Avec Visual Studio 2013, vous pouvez maintenant utiliser la structure d’un projet ASP.NET, y compris les Web Forms. Visual Studio 2013 ne prend pas en charge les pages de génération d’un projet Web Forms, mais vous pouvez toujours utiliser la structure des Web Forms en ajoutant des dépendances MVC au projet. Prise en charge pour la génération de pages pour Web Forms sera ajoutée dans une prochaine mise à jour.

Lorsque vous utilisez la structure, nous Assurez-vous que tous les requises dépendances sont installées dans le projet. Par exemple, si vous démarrez avec un projet Web Forms ASP.NET et puis utilisez la structure pour ajouter un contrôleur d’API Web, les packages NuGet requis et les références sont ajoutés à votre projet automatiquement.

Pour ajouter la structure de MVC à un projet Web Forms, ajoutez un **un nouvel élément structuré** et sélectionnez **MVC 5 dépendances** dans la fenêtre de la boîte de dialogue. Il existe deux options pour la génération de modèles automatique MVC ; Minimale et complète. Si vous sélectionnez Minimal, seuls les packages NuGet et les références pour ASP.NET MVC sont ajoutés à votre projet. Si vous sélectionnez l’option complet, les dépendances minimale sont ajoutés, ainsi que les fichiers de contenu requis pour un projet MVC.

Prise en charge de la génération de modèles automatique async contrôleurs utilise les nouvelles fonctionnalités asynchrones à partir de l’Entity Framework 6.

Pour plus d’informations et des didacticiels, consultez [vue d’ensemble de la génération de modèles automatique ASP.NET](aspnet-scaffolding-overview.md).

<a id="browser-link"></a>
## <a name="browser-link--signalr-channel-between-browser-and-visual-studio"></a>Lien du navigateur : canal SignalR entre le navigateur et de Visual Studio

La nouvelle [lien du navigateur](using-browser-link.md) fonctionnalité vous permet de connecter plusieurs navigateurs pour Visual Studio et de les actualiser tout en cliquant sur un bouton dans la barre d’outils. Vous pouvez connecter plusieurs navigateurs à votre site de développement, y compris les émulateurs mobiles et cliquez sur Actualiser pour actualiser tous les navigateurs tous en même temps. Lien du navigateur expose également une API pour permettre aux développeurs d’écrire des extensions de lien de navigateur.

![](release-notes/_static/image3.png)

En permettant aux développeurs de tirer parti de l’API de lien de navigateur, il devient possible de créer des scénarios très avancés qui dépasse les limites entre Visual Studio et n’importe quel navigateur qui est connecté. Web Essentials tire parti de l’API pour créer une expérience intégrée entre Visual Studio et les outils de développement du navigateur, à distance le contrôle des émulateurs mobiles et bien plus encore.

<a id="web-editor"></a>
## <a name="visual-studio-web-editor-enhancements"></a>Améliorations de l’éditeur de Visual Studio Web

Visual Studio 2013 inclut un nouvel éditeur HTML pour les fichiers HTML et les fichiers Razor dans les applications web. Le nouvel éditeur HTML fournit un seul schéma unifié basé sur HTML5. Saisie semi-automatique des accolades automatique, de jQuery UI et AngularJS IntelliSense d’attribut, l’attribut de regroupement d’IntelliSense, ID et nom de classe Intellisense et autres améliorations, y compris les meilleures performances, la mise en forme et les balises actives.

La capture d’écran suivante illustre l’utilisation de démarrage attribut IntelliSense dans l’éditeur HTML.

![IntelliSense dans l’éditeur HTML](release-notes/_static/image4.png)

Visual Studio 2013 est également fourni avec les deux CoffeeScript et moins éditeurs intégrés. MOINS l’éditeur est fourni avec toutes les fonctionnalités intéressantes à partir de l’éditeur CSS et a Intellisense spécifique pour les variables et mixins sur tous les moins documents dans le @import chaîne.

<a id="waws"></a>
## <a name="azure-app-service-web-apps-support-in-visual-studio"></a>Prise en charge des applications Azure App Service Web dans Visual Studio

Dans Visual Studio 2013 avec le SDK Azure pour .NET 2.2, vous pouvez utiliser **l’Explorateur de serveurs** d’interagir directement avec vos applications web à distance. Vous pouvez vous connecter à votre compte Azure, créer de nouvelles applications web, configurer des applications, afficher les journaux en temps réel et bien plus encore. Entrée peu après SDK 2.2 est publiée, vous serez en mesure d’exécuter en mode débogage à distance dans Azure. La plupart des nouvelles fonctionnalités pour les applications Azure App Service Web fonctionne également dans Visual Studio 2012 lorsque vous installez la version actuelle de Windows Azure SDK pour .NET.

Pour plus d'informations, reportez-vous aux ressources suivantes :

- [Créer une application web ASP.NET dans Azure App Service](https://azure.microsoft.com/en-us/documentation/articles/web-sites-dotnet-get-started/)
- [Résoudre les problèmes d’une application web dans Azure App Service à l’aide de Visual Studio](https://azure.microsoft.com/en-us/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/)

<a id="publish"></a>
## <a name="web-publish-enhancements"></a>Améliorations de la publication sur le Web

Visual Studio 2013 inclut des fonctionnalités nouvelles et améliorées de publication Web. Voici quelques-uns :

- Facilement [automatiser le chiffrement des fichiers Web.config](https://go.microsoft.com/fwlink/?LinkId=325529). (Ce lien et les deux point à la documentation sur MSDN n’est peut-être pas disponible tant que vers la fin de la journée sur 10/17).
- Facilement [automatiser prenant une application en mode hors connexion durant le déploiement](https://go.microsoft.com/fwlink/?LinkId=325530).
- Configurer Web Deploy pour [utiliser checksum de fichier au lieu de la date de dernière modification](https://go.microsoft.com/fwlink/?LinkId=325531) pour déterminer quels fichiers doivent être copiés sur le serveur.
- Publier rapidement des fichiers individuels sélectionnés (y compris le fichier Web.config) lorsque vous utilisez le protocole FTP ou de méthodes de publication du système de fichiers ainsi qu’avec Web Deploy.

Pour plus d’informations sur le déploiement de web ASP.NET, consultez [le site ASP.NET](https://go.microsoft.com/fwlink/?LinkId=322027).

<a id="nuget"></a>
## <a name="nuget-27"></a>NuGet 2.7

NuGet 2.7 comprend un ensemble complet de nouvelles fonctionnalités qui sont décrites en détail à [Notes de version 2.7 NuGet](http://docs.nuget.org/docs/release-notes/nuget-2.7).

Cette version de NuGet supprime également la nécessité de fournir un consentement explicite pour la fonctionnalité de restauration de package de NuGet à télécharger les packages. Consentement (et la case à cocher correspondante dans la boîte de dialogue Préférences de NuGet) sont maintenant accordées par l’installation de NuGet. Restauration des packages simplement fonctionne désormais par défaut.

<a id="TOC9"></a>
## <a name="aspnet-web-forms"></a>ASP.NET Web Forms

### <a name="one-aspnet"></a>Un seul ASP.NET

Les modèles de projet Web Forms s’intégrer sans problème avec la nouvelle expérience One ASP.NET. Vous pouvez ajouter MVC API Web prennent en charge à votre projet Web Forms, et vous pouvez configurer l’authentification à l’aide de l’Assistant Création de projet One ASP.NET. Pour plus d’informations, consultez [création de projets Web ASP.NET dans Visual Studio 2013](creating-web-projects-in-visual-studio.md).

### <a name="aspnet-identity"></a>ASP.NET Identity

Les modèles de projet Web Forms prend en charge la nouvelle infrastructure d’identité ASP.NET. En outre, les modèles prennent désormais en charge la création d’un projet Web Forms d’intranet. Pour plus d’informations, consultez [les méthodes d’authentification](creating-web-projects-in-visual-studio.md#auth) dans **création de projets Web ASP.NET dans Visual Studio 2013**.

### <a name="bootstrap"></a>programme d’amorçage

Les modèles Web Forms utilisent [Bootstrap](http://twitter.github.io/bootstrap/) pour fournir une élégante et réactive apparence que vous pouvez facilement personnaliser. Pour plus d’informations, consultez [Bootstrap dans les modèles de projet web Visual Studio 2013](creating-web-projects-in-visual-studio.md#bootstrap).

<a id="TOC10"></a>
## <a name="aspnet-mvc-5"></a>ASP.NET MVC 5

### <a name="one-aspnet"></a>Un seul ASP.NET

Les modèles de projet Web MVC s’intégrer sans problème avec la nouvelle expérience One ASP.NET. Vous pouvez personnaliser votre projet MVC et configurer l’authentification à l’aide de l’Assistant Création de projet One ASP.NET. Vous trouverez un didacticiel d’introduction à ASP.NET MVC 5 à [mise en route avec ASP.NET MVC 5](../../../mvc/overview/getting-started/introduction/getting-started.md).

Pour plus d’informations sur la mise à niveau des projets MVC 4 à 5 de MVC, consultez [la mise à niveau un ASP.NET MVC 4 et le projet d’API Web ASP.NET MVC 5 et Web API 2](../../../mvc/overview/releases/how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2.md).

### <a name="aspnet-identity"></a>ASP.NET Identity

Les modèles de projet MVC ont été mis à jour pour utiliser ASP.NET Identity pour l’authentification et la gestion d’identité. Vous trouverez un didacticiel avec l’authentification Facebook et Google et la nouvelle API d’appartenance à [création d’une application ASP.NET MVC 5 avec Facebook et Google OAuth2 et OpenID Sign-on](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) et [créer une application ASP.NET MVC avec l’authentification et Base de données SQL et le déployer vers Azure App Service](https://azure.microsoft.com/en-us/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/).

### <a name="bootstrap"></a>programme d’amorçage

Le modèle de projet MVC a été mis à jour pour utiliser [Bootstrap](http://getbootstrap.com/) pour fournir une élégante et réactive apparence que vous pouvez facilement personnaliser. Pour plus d’informations, consultez [Bootstrap dans les modèles de projet web Visual Studio 2013](creating-web-projects-in-visual-studio.md#bootstrap).

### <a name="authentication-filters"></a>Filtres d’authentification

Filtres d’authentification sont un nouveau type de filtre dans ASP.NET MVC qui s’exécutent avant les filtres d’autorisation dans le pipeline ASP.NET MVC et vous permettent de spécifier l’authentification logique par action, par contrôleur, ou globalement pour tous les contrôleurs. Filtres d’authentification traitent les informations d’identification dans la demande et fournissent une identité correspondante. Filtres d’authentification peuvent également ajouter les stimulations d’authentification en réponse aux demandes non autorisées.

### <a name="filter-overrides"></a>Filtrer les remplacements

Vous pouvez maintenant remplacer les filtres à appliquer à une méthode d’action donné ou un contrôleur en spécifiant un filtre de remplacement. Les filtres de remplacement de spécifier un ensemble de types de filtre ne doit pas être exécutée pour une étendue donnée (action ou contrôleur). Cela vous permet de vous permet de configurer des filtres qui s’appliquent globalement mais puis exclure certains filtres globaux de l’application à des actions spécifiques ou les contrôleurs.

### <a name="attribute-routing"></a>Routage d’attributs

ASP.NET MVC prend désormais en charge le routage d’attributs, grâce à une contribution par Tim McCall, l’auteur de [http://attributerouting.net](http://attributerouting.net). Avec le routage de l’attribut, vous pouvez spécifier votre itinéraires par vos actions et les contrôleurs d’annotation.

<a id="TOC11"></a>
## <a name="aspnet-web-api-2"></a>API web ASP.NET 2

### <a name="attribute-routing"></a>Routage d’attributs

API Web ASP.NET prend désormais en charge le routage d’attributs, grâce à une contribution par Tim McCall, l’auteur de [http://attributerouting.net](http://attributerouting.net). Avec le routage de l’attribut, vous pouvez spécifier des itinéraires de votre API Web en annotant vos actions et les contrôleurs comme suit :

[!code-csharp[Main](release-notes/samples/sample1.cs)]

Routage d’attributs vous donne davantage de contrôle sur les URI de votre API web. Par exemple, vous pouvez facilement définir une hiérarchie de ressources à l’aide d’un seul contrôleur d’API :

[!code-csharp[Main](release-notes/samples/sample2.cs)]

Attribut routage également fournit une syntaxe permettant de spécifier des paramètres facultatifs, les valeurs par défaut et les contraintes d’itinéraire :

[!code-csharp[Main](release-notes/samples/sample3.cs)]

Pour plus d’informations sur le routage de l’attribut, consultez [routage d’attributs dans l’API Web 2](../../../web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2.md).

### <a name="oauth-20"></a>OAuth 2.0

Les modèles de projet d’API Web et Application à Page unique prennent désormais en charge l’autorisation à l’aide d’OAuth 2.0. OAuth 2.0 est une infrastructure d’autorisation d’accès client aux ressources protégées. Elle fonctionne pour de nombreux clients, y compris les navigateurs et périphériques mobiles.

Prise en charge d’OAuth 2.0 est basé sur la nouvelle sécurité intergiciel (middleware) fournis par les composants Microsoft OWIN pour l’authentification du support et de mise en œuvre le rôle de serveur d’autorisation. Vous pouvez également, les clients peuvent être autorisés à l’aide d’un serveur d’autorisation d’organisation, tels que Azure Active Directory ou AD FS dans Windows Server 2012 R2.

### <a name="odata-improvements"></a>Améliorations apportées à OData

**Prise en charge pour $select, $développer, $batch et $value**

ASP.NET Web API OData prend désormais en charge complète pour $select, $expand et $value. Vous pouvez également utiliser $batch pour la demande de traitement par lot et le traitement des ensembles de modifications.

$Select et $développez options vous permettent de changer la forme des données qui sont retournées à partir d’un point de terminaison OData. Pour plus d’informations, consultez [présentation $select et $développer la prise en charge dans OData d’API Web](../../../web-api/overview/odata-support-in-aspnet-web-api/using-select-expand-and-value.md).

**Extensibilité améliorée**

Les formateurs OData sont maintenant extensibles. Vous pouvez ajouter des métadonnées de l’entrée Atom, prennent en charge des entrées de lien de flux de données et de support nommées, ajouter des annotations d’instance et personnaliser la façon dont les liens sont générés.

**Prise en charge sans type**

Vous pouvez maintenant générer des services OData sans avoir à définir les types CLR pour vos types d’entité. Au lieu de cela, vos contrôleurs OData peuvent avoir ou retournent des instances de **IEdmObject**, qui sont les formateurs OData de sérialisation/désérialisation.

**Réutiliser un modèle existant**

Si vous disposez déjà d’un entity data model (EDM) existant, vous pouvez maintenant réutiliser il directement, au lieu de générer un nouveau. Par exemple, si vous utilisez Entity Framework, vous pouvez utiliser le modèle EDM EF génère pour vous.

### <a name="request-batching"></a>Demande de traitement par lot

Demande de traitement par lot combine plusieurs opérations en une seule requête HTTP POST, pour réduire le trafic réseau et de fournir une lisse, moins l’interface utilisateur bavardes. API Web ASP.NET prend désormais en charge plusieurs stratégies de demande de traitement par lot :

- Utilisez le point de terminaison $batch d’un service OData.
- Package de plusieurs requêtes en une seule requête en plusieurs parties MIME.
- Utiliser un format personnalisé de traitement par lot.

Pour activer la demande de traitement par lots, ajoutez simplement un itinéraire avec un gestionnaire de traitement par lot à votre configuration de l’API Web :

[!code-csharp[Main](release-notes/samples/sample4.cs)]

Vous pouvez également contrôler si les demandes ou exécutées séquentiellement ou dans n’importe quel ordre.

### <a name="portable-aspnet-web-api-client"></a>Client de l’API Web ASP.NET portable

Vous pouvez maintenant utiliser le Client de API Web ASP.NET pour créer des bibliothèques de classes portables qui fonctionnent dans vos applications Windows Store et Windows Phone 8. Vous pouvez également créer des formateurs portables qui peuvent être partagées entre le client et le serveur.

### <a name="improved-testability"></a>Testabilité améliorée

Web API 2 rend beaucoup plus faciles à unité vos contrôleurs de l’API de test. Simplement instancier votre contrôleur d’API avec votre message de demande et de la configuration, puis appelez la méthode d’action que vous souhaitez tester. Il est également facile de simuler la **UrlHelper** (classe), pour les méthodes d’action qui effectuent la génération du lien.

### <a name="ihttpactionresult"></a>IHttpActionResult

Vous pouvez désormais implémenter IHttpActionResult pour encapsuler le résultat de méthodes d’action de votre API Web. Un IHttpActionResult retourné à partir d’une méthode d’action API Web est exécutée par l’exécution de l’API Web ASP.NET pour produire le message de réponse résultante. Un IHttpActionResult peut être retourné à partir de toute action de l’API Web pour simplifier l’unité de test de votre implémentation de l’API Web. Pour plus de commodité que plusieurs implémentations de IHttpActionResult sont fournies prêtes à l’emploi, y compris les résultats pour retourner des codes d’état spécifique, mise en forme de contenu ou la négociation de contenu des réponses.

### <a name="httprequestcontext"></a>HttpRequestContext

La nouvelle **HttpRequestContext** effectue le suivi de tout état qui n’est liée à la demande, mais n’est pas immédiatement disponible à partir de la demande. Par exemple, vous pouvez utiliser la **HttpRequestContext** pour obtenir les données d’itinéraire, le principal associé à la demande, le certificat client, le **UrlHelper** et la racine du chemin d’accès virtuel. Vous pouvez facilement créer un **HttpRequestContext** pour l’unité à des fins de test.

Étant donné que l’entité de sécurité pour la demande est transmise à la demande au lieu d’utiliser **Thread.CurrentPrincipal**, le principal est désormais disponible pendant toute la durée de la demande s’il est dans le pipeline de l’API Web.

### <a name="cors"></a>CORS

Grâce à une autre grande contribution à partir de Brock Allen, ASP.NET prend désormais entièrement en charge Cross-Origin demander partage (CORS).

Sécurité du navigateur empêche une page web d’effectuer des demandes AJAX vers un autre domaine. [CORS](http://www.w3.org/TR/cors/) est une norme W3C qui permet à un serveur d’abaisser la stratégie de même origine. À l’aide de CORS, un serveur peut autoriser explicitement les certaines demandes cross-origin lors du refus d’autres.

API Web 2 prend désormais en charge les règles CORS, y compris la gestion automatique des demandes de contrôle en amont. Pour plus d’informations, consultez [l’activation des demandes Cross-Origin dans ASP.NET Web API](../../../web-api/overview/security/enabling-cross-origin-requests-in-web-api.md).

### <a name="authentication-filters"></a>Filtres d’authentification

Filtres d’authentification sont un nouveau type de filtre dans les API Web ASP.NET qui s’exécutent avant les filtres d’autorisation dans le pipeline de l’API Web ASP.NET et vous permettent de spécifier l’authentification logique par action, par contrôleur, ou globalement pour tous les contrôleurs. Filtres d’authentification traitent les informations d’identification dans la demande et fournissent une identité correspondante. Filtres d’authentification peuvent également ajouter les stimulations d’authentification en réponse aux demandes non autorisées.

### <a name="filter-overrides"></a>Filtrer les remplacements

Vous pouvez maintenant remplacer les filtres à appliquer à une méthode d’action donné ou un contrôleur, en spécifiant un filtre de remplacement. Les filtres de remplacement de spécifier un ensemble de types de filtre ne doit pas s’exécuter pour une étendue donnée (action ou contrôleur). Cela vous permet à ajouter des filtres globaux, mais puis exclure certains à partir des actions spécifiques ou les contrôleurs.

### <a name="owin-integration"></a>Intégration de OWIN

ASP.NET Web API entièrement prend désormais OWIN et peut être exécuté sur n’importe quel hôte compatible OWIN. Est également inclus un **HostAuthenticationFilter** qui fournit l’intégration avec le système d’authentification OWIN.

Avec l’intégration de OWIN, vous pouvez auto-hébergement API Web dans votre propre processus en même temps que les autres intergiciel (middleware) OWIN, telles que SignalR. Pour plus d’informations, consultez [OWIN utilisez Self-Host ASP.NET Web API](../../../signalr/overview/deployment/tutorial-signalr-self-host.md).

<a id="TOC13"></a>
## <a name="aspnet-signalr-20"></a>SignalR d’ASP.NET 2.0

Les sections suivantes décrivent les fonctionnalités de SignalR 2.0.

- [Reposant sur OWIN](#builtonowin)
- [MapHubs et MapConnection sont désormais MapSignalR](#MapSignalR)
- [Prise en charge des domaines](#crossdomain)
- [iOS et Android prennent en charge via MonoTouch et MonoDroid](#mobile)
- [Client .NET portable](#portable)
- [Nouveau Package auto-hébergement](#selfhost)
- [Prise en charge du serveur de compatibilité descendante](#backwardcompat)
- [Supprimer la prise en charge de serveur pour le .NET 4.0](#remove40)
- [Envoi d’un message à une liste de clients et de groupes](#messagelist)
- [Envoi d’un message à un utilisateur spécifique](#sendtouser)
- [Meilleure prise en charge de la gestion des erreurs](#errorhandling)
- [Unité plus facile de test de hubs](#unittesting)
- [La gestion des erreurs JavaScript](#javascripterror)

Pour obtenir un exemple de la mise à niveau un projet existant de 1.x SignalR 2.0, consultez [la mise à niveau un SignalR 1.x projet](../../../signalr/overview/releases/upgrading-signalr-1x-projects-to-20.md).

<a id="builtonowin"></a>
### <a name="built-on-owin"></a>Reposant sur OWIN

SignalR 2.0 repose entièrement sur [OWIN (l’Interface Web ouverte pour .NET)](http://owin.org/). Cette modification rend le processus d’installation pour SignalR beaucoup plus cohérente entre les applications SignalR auto-hébergé et hébergé sur le web, mais elle exige également un nombre de modifications de l’API.

<a id="MapSignalR"></a>

### <a name="maphubs-and-mapconnection-are-now-mapsignalr"></a>MapHubs et MapConnection sont désormais MapSignalR

Pour la compatibilité avec les normes OWIN, ces méthodes ont été renommées `MapSignalR`. `MapSignalR`appelée sans paramètres mappera tous les concentrateurs (en tant que `MapHubs` dans la version 1.x) ; pour mapper des **persistentconnection ne** objets, spécifiez le type de connexion en tant que paramètre de type et l’extension d’URL pour la connexion en tant que le premier argument.

Le `MapSignalR` méthode est appelée dans une classe de démarrage Owin. Visual Studio 2013 contient un nouveau modèle pour une classe de démarrage Owin ; Pour utiliser ce modèle, procédez comme suit :

1. Avec le bouton droit sur le projet
2. Sélectionnez **ajouter**, **un nouvel élément...**
3. Sélectionnez **classe de démarrage Owin**. Nommez la nouvelle classe **Startup.cs**.

Dans un **application Web,** Owin démarrage classe contenant la `MapSignalR` méthode est ensuite ajoutée au processus de démarrage de Owin à l’aide d’une entrée dans le nœud de paramètres d’application du fichier Web.Config, comme indiqué ci-dessous.

Dans un **auto-hébergé application**, la classe de démarrage est passée comme paramètre de type de la `WebApp.Start` (méthode).

**Mappage des concentrateurs et les connexions SignalR 1.x (du fichier d’application globale dans une application web) :** 

[!code-csharp[Main](release-notes/samples/sample5.cs)]

**Concentrateurs de mappage et les connexions SignalR version 2.0 (à partir d’un fichier de classe de démarrage Owin) :** 

[!code-csharp[Main](release-notes/samples/sample6.cs)]

Dans un **auto-hébergé application**, la classe de démarrage est passée comme paramètre de type pour le `WebApp.Start` méthode, comme indiqué ci-dessous.

[!code-csharp[Main](release-notes/samples/sample7.cs)]

<a id="crossdomain"></a>

### <a name="cross-domain-support"></a>Prise en charge des domaines

SignalR 1.x, des requêtes entre domaines ont été contrôlé par un indicateur EnableCrossDomain unique. Cet indicateur contrôlé demandes JSONP et CORS. Pour plus de souplesse, toutes les règles CORS prennent en charge a été retiré le composant serveur du SignalR (JavaScript langue toujours utiliser CORS normalement s’il est détecté que le navigateur prend en charge), et nouveau intergiciel (middleware) OWIN a été rendue disponible pour prendre en charge ces scénarios.

SignalR 2.0 si JSONP est requis sur le client (pour prendre en charge les demandes entre domaines dans les anciens navigateurs), il doit être activée explicitement en définissant `EnableJSONP` sur la `HubConfiguration` objet `true`, comme illustré ci-dessous. JSONP est désactivé par défaut, car il est moins sécurisée que CORS.

Pour ajouter la nouvelle intergiciel (middleware) CORS dans SignalR 2.0, ajoutez le `Microsoft.Owin.Cors` bibliothèque à votre projet, puis appelez `UseCors` avant votre intergiciel (middleware) SignalR, comme indiqué dans la section ci-dessous.

**Ajout de Microsoft.Owin.Cors à votre projet**: pour installer cette bibliothèque, exécutez la commande suivante dans la Console du Gestionnaire de Package :

[!code-powershell[Main](release-notes/samples/sample8.ps1)]

Cette commande ajoute le 2.0.0 version du package pour votre projet.

**Appel de UseCors**

Les extraits de code suivants montrent comment implémenter des connexions inter-domaines dans SignalR 1.x et 2.0.

**Implémentation des demandes inter-domaines dans SignalR 1.x (à partir du fichier d’application globale)**

[!code-csharp[Main](release-notes/samples/sample9.cs)]

**Implémentation des demandes inter-domaines dans 2.0 SignalR (à partir d’un fichier de code c#)**

Le code suivant montre comment activer les CORS ou JSONP dans un projet SignalR 2.0. Cet exemple de code utilise `Map` et `RunSignalR` au lieu de `MapSignalR`, de sorte que l’intergiciel (middleware) CORS s’exécute uniquement pour les demandes de SignalR qui requièrent la prise en charge CORS (plutôt que pour tout le trafic sur le chemin d’accès spécifié dans `MapSignalR`.) `Map` peut également être utilisée pour n’importe quel autre intergiciel (middleware) qui doit s’exécuter pour un préfixe d’URL spécifique, plutôt que pour l’application entière.

[!code-csharp[Main](release-notes/samples/sample10.cs)]

<a id="mobile"></a>

### <a name="ios-and-android-support-via-monotouch-and-monodroid"></a>iOS et Android prennent en charge via MonoTouch et MonoDroid

Prise en charge a été ajoutée pour iOS et Android clients à l’aide de composants MonoTouch et MonoDroid à partir de la [Xamarin bibliothèque](https://xamarin.com/). Pour plus d’informations sur la façon de les utiliser, consultez [à l’aide des composants Xamarin](https://github.com/SignalR/SignalR/wiki/Building-Mono.Mobile.sln). Ces composants seront disponibles dans le [Xamarin Store](https://store.xamarin.com/) lorsque la mise en production SignalR RTW est disponible.

<a id="portable"></a>### Portable client .NET

Pour mieux facilitent le développement multiplateforme, le Silverlight, WinRT et les clients Windows Phone ont été remplacés par un seul client .NET portable qui prend en charge les plateformes suivantes :

- NET 4.5
- Silverlight 5
- WinRT (.NET pour les applications du Windows Store)
- Windows Phone 8

<a id="selfhost"></a>

### <a name="new-self-host-package"></a>Nouveau Package auto-hébergement

Il existe désormais un package NuGet pour faciliter la prise en main SignalR l’auto-hébergement (applications SignalR qui sont hébergées dans un processus ou autre application, plutôt que hébergé sur un serveur web). Pour mettre à niveau un projet d’auto-hébergement généré avec SignalR 1.x, supprimer le package Microsoft.AspNet.SignalR.Owin et ajoutez le package Microsoft.AspNet.SignalR.SelfHost. Pour plus d’informations sur la mise en route avec le package d’auto-hébergement, consultez [didacticiel : SignalR auto-hébergement](../../../signalr/overview/deployment/tutorial-signalr-self-host.md).

<a id="backwardcompat"></a>

### <a name="backward-compatible-server-support"></a>Prise en charge du serveur de compatibilité descendante

Dans les versions précédentes de SignalR, les versions de package SignalR utilisé dans le client et le serveur doit être identique. Pour prendre en charge les applications client lourd qui seraient difficiles à mettre à jour, SignalR 2.0 prend désormais en charge l’à l’aide d’une version plus récente du serveur avec un ancien client. **Remarque : SignalR 2.0 ne prend pas en charge les serveurs intégrés avec les versions antérieures des clients plus récents.**

<a id="remove40"></a>

### <a name="removed-server-support-for-net-40"></a>Supprimer la prise en charge de serveur pour le .NET 4.0

SignalR 2.0 a supprimé la prise en charge pour l’interopérabilité de serveur avec le .NET 4.0. .NET 4.5 doit être utilisé avec les serveurs SignalR 2.0. Il existe encore un client .NET 4.0 pour SignalR 2.0.

<a id="messagelist"></a>

### <a name="sending-a-message-to-a-list-of-clients-and-groups"></a>Envoi d’un message à une liste de clients et de groupes

Dans SignalR 2.0, il est possible d’envoyer un message à l’aide d’une liste de client et ID de groupe. Les extraits de code suivants montrent comment effectuer cette opération.

**Envoi d’un message à une liste de clients et de groupes à l’aide de persistentconnection n'**

[!code-csharp[Main](release-notes/samples/sample11.cs)]

**Envoi d’un message à une liste de clients et de groupes à l’aide de concentrateurs**

[!code-csharp[Main](release-notes/samples/sample12.cs)]

<a id="sendtouser"></a>

### <a name="sending-a-message-to-a-specific-user"></a>Envoi d’un message à un utilisateur spécifique

Cette fonctionnalité permet aux utilisateurs de spécifier quel est le nom d’utilisateur basé sur un IRequest via une nouvelle interface IUserIdProvider :

**L’interface IUserIdProvider**

[!code-csharp[Main](release-notes/samples/sample13.cs)]

Par défaut il y aura une implémentation qui utilise IPrincipal.Identity.Name l’utilisateur comme nom d’utilisateur.

Dans des concentrateurs, vous serez en mesure d’envoyer des messages à ces utilisateurs via une API :

**À l’aide de l’API Clients.User**

[!code-csharp[Main](release-notes/samples/sample14.cs)]

<a id="errorhandling"></a>

### <a name="better-error-handling-support"></a>Meilleure prise en charge de la gestion des erreurs

Les utilisateurs peuvent désormais lever **HubException** à partir de n’importe quel appel de concentrateur. Le constructeur de la **HubException** peut prendre un message de chaîne et un objet des données d’erreur supplémentaires. SignalR auto-sérialiser l’exception et l’envoyer au client où il sera utilisé pour l’appel de méthode de concentrateur de rejeter ou d’échec.

Le **afficher les exceptions détaillées hub** paramètre n’a aucune incidence sur **HubException** envoyé au client ou non ; il est toujours envoyé.

**Code côté serveur montrant l’envoi d’une HubException au client**

[!code-csharp[Main](release-notes/samples/sample15.cs)]

**Code JavaScript client démontrant répond à une HubException envoyée depuis le serveur**

[!code-html[Main](release-notes/samples/sample16.html)]

**Code client .NET démontrant répond à une HubException envoyée à partir du serveur**

[!code-csharp[Main](release-notes/samples/sample17.cs)]

<a id="unittesting"></a>

### <a name="easier-unit-testing-of-hubs"></a>Unité plus facile de test de hubs

SignalR 2.0 inclut une interface appelée `IHubCallerConnectionContext` sur les concentrateurs qui rend plus facile de créer des appels du côté client fictive. Les extraits de code suivants illustrent l’utilisation de cette interface avec des tests populaires [xUnit.net](https://github.com/xunit/xunit) et [moq](https://code.google.com/p/moq/).

**Tests unitaires SignalR avec xUnit.net**

[!code-csharp[Main](release-notes/samples/sample18.cs)]

**Tests unitaires SignalR avec moq**

[!code-csharp[Main](release-notes/samples/sample19.cs)]

<a id="javascripterror"></a>

### <a name="javascript-error-handling"></a>La gestion des erreurs JavaScript

Dans SignalR 2.0, tous les rappels de la gestion des erreurs JavaScript retournent des objets d’erreur JavaScript au lieu de chaînes brutes. Cela permet de SignalR transmettre des informations plus détaillées à vos gestionnaires d’erreurs. Vous pouvez obtenir l’exception interne à partir de la `source` la propriété de l’erreur.

**Code JavaScript client qui gère l’exception Start.Fail**

[!code-javascript[Main](release-notes/samples/sample20.js)]

<a id="TOC8"></a>
## <a name="aspnet-identity"></a>ASP.NET Identity

### <a name="new-aspnet-membership-system"></a>Nouveau système d’appartenance ASP.NET

ASP.NET Identity est le nouveau système d’appartenance pour les applications ASP.NET. ASP.NET Identity rend facile à intégrer les données de profil d’utilisateur avec les données d’application. Identité ASP.NET vous permet également de choisir le modèle de persistance pour les profils utilisateur dans votre application. Vous pouvez stocker les données dans une base de données SQL Server ou une autre banque de données, y compris les banques de données NoSQL telles que les Tables de stockage Azure. Pour plus d’informations, consultez [comptes d’utilisateur individuels](creating-web-projects-in-visual-studio.md#indauth) dans **création de projets Web ASP.NET dans Visual Studio 2013**.

### <a name="claims-based-authentication"></a>Authentification basée sur les revendications

ASP.NET prend désormais en charge l’authentification basée sur les revendications, où l’identité de l’utilisateur est représentée comme un ensemble de revendications à partir d’un émetteur approuvé. Les utilisateurs peuvent être authentifiés à l’aide d’un nom d’utilisateur et un mot de passe mis à jour dans une base de données de l’application ou à l’aide de fournisseurs d’identité sociaux (par exemple : Accounts Microsoft, Facebook, Google, Twitter), ou à l’aide de comptes de société via Azure Active Directory ou Active Directory Federation Services (ADFS).

### <a name="integration-with-azure-active-directory-and-windows-server-active-directory"></a>Intégration avec Azure Active Directory et Active Directory Windows Server

Vous pouvez désormais créer des projets ASP.NET qui utilisent Azure Active Directory ou Windows Server Active Directory (AD) pour l’authentification. Pour plus d’informations, consultez [comptes professionnels](creating-web-projects-in-visual-studio.md#orgauth) dans **création de projets Web ASP.NET dans Visual Studio 2013**.

### <a name="owin-integration"></a>Intégration de OWIN

Authentification ASP.NET repose désormais sur un intergiciel (middleware) OWIN qui peut être utilisé sur n’importe quel hôte OWIN. Pour plus d’informations sur OWIN, consultez la rubrique suivante [des composants Microsoft OWIN](#TOC7) section.

<a id="TOC7"></a>
## <a name="microsoft-owin-components"></a>Composants Microsoft OWIN

[Ouvrir l’Interface Web pour .NET](http://owin.org/) (OWIN) définit une abstraction entre les serveurs web .NET et des applications web. OWIN dissocie l’application web à partir du serveur, rendre web applications hôte indépendant. Par exemple, vous pouvez héberger une application web basée sur les OWIN dans IIS ou auto-hébergée dans un processus personnalisé.

Modifications introduites dans les composants Microsoft OWIN (également connu sous le projet Katana) comprennent les nouveaux composants de serveur et l’hôte, nouvelles bibliothèques d’assistance et intergiciel (middleware) de nouveau intergiciel (middleware) d’authentification.

Pour plus d’informations sur OWIN et Katana, consultez [Nouveautés OWIN et Katana](../../../aspnet/overview/owin-and-katana/index.md).

**Remarque : [OWIN](../../../aspnet/overview/owin-and-katana/an-overview-of-project-katana.md) applications ne peut pas s’exécuter en mode classique d’IIS ; ils doivent être exécutés en mode intégré.**

**Remarque : [OWIN](../../../aspnet/overview/owin-and-katana/an-overview-of-project-katana.md) applications doivent être exécutées avec une confiance totale.**

### <a name="new-servers-and-hosts"></a>Ordinateurs hôtes et les nouveaux serveurs

Avec cette version, les nouveaux composants ont été ajoutées pour activer des scénarios d’auto-hébergement. Ces composants incluent les packages NuGet suivants :

- **Microsoft.Owin.Host.HttpListener**. Fournit un serveur OWIN qui utilise **HttpListener** pour écouter les requêtes HTTP et de les acheminer au pipeline OWIN.
- **Microsoft.Owin.Hosting** fournit une bibliothèque pour les développeurs qui souhaitent auto-héberger un pipeline OWIN dans un processus personnalisé, comme une application console ou un service Windows.
- **OwinHost**. Fournit un fichier exécutable autonome qui encapsule `Microsoft.Owin.Hosting` et vous permet d’auto-hébergement un pipeline OWIN sans avoir à écrire une application hôte personnalisée.

En outre, le `Microsoft.Owin.Host.SystemWeb` package permet désormais d’intergiciel (middleware) fournir des indications pour la **SystemWeb** serveur, indiquant que l’intergiciel (middleware) doit être appelée au cours d’une étape du pipeline ASP.NET spécifique. Cette fonctionnalité est particulièrement utile pour l’authentification intergiciel (middleware), qui doit s’exécuter très tôt dans le pipeline ASP.NET.

### <a name="helper-libraries-and-middleware"></a>Intergiciel (middleware) et des bibliothèques d’assistance

Bien que vous pouvez écrire des composants OWIN uniquement les définitions de fonction et le type de la spécification OWIN, à l’aide de la nouvelle `Microsoft.Owin` package fournit un ensemble plus convivial des abstractions. Ce package combine plusieurs packages antérieures (par exemple, `Owin.Extensions`, `Owin.Types`) dans un modèle d’objet bien structurés unique qui peut ensuite être facilement utilisé par d’autres composants OWIN. En fait, la plupart des composants Microsoft OWIN désormais utiliser ce package.

> [!NOTE]
> [OWIN](http://www.owin.org) applications ne peut pas s’exécuter en mode classique d’IIS ; ils doivent être exécutés en mode intégré.

> [!NOTE]
> [OWIN](http://www.owin.org) applications doivent être exécutées avec une confiance totale.

Cette version inclut également le package Microsoft.Owin.Diagnostics, qui inclut l’intergiciel (middleware) pour valider une application OWIN en cours d’exécution, ainsi que des intergiciels (middleware) de la page d’erreur pour aider à identifier les échecs.

### <a name="authentication-components"></a>Composants d’authentification

Les composants d’authentification suivants sont disponibles.

- **Microsoft.Owin.Security.ActiveDirectory**. Active l’authentification à l’aide des services d’annuaire sur site ou en nuage.
- **Microsoft.Owin.Security.Cookies** Active l’authentification à l’aide de cookies. Ce package a été précédemment nommé `Microsoft.Owin.Security.Forms`.
- **Microsoft.Owin.Security.Facebook** Active l’authentification à l’aide des services de Facebook OAuth.
- **Microsoft.Owin.Security.Google** Active l’authentification à l’aide des services de Google OpenID.
- **Microsoft.Owin.Security.Jwt** Active l’authentification à l’aide de jetons JWT.
- **Microsoft.Owin.Security.MicrosoftAccount** Active l’authentification à l’aide de comptes Microsoft.
- **Microsoft.Owin.Security.OAuth**. Fournit un serveur d’autorisation OAuth ainsi que des intergiciels (middleware) pour l’authentification des jetons de support.
- **Microsoft.Owin.Security.Twitter** Active l’authentification à l’aide des services de Twitter OAuth.

Cette version inclut également le `Microsoft.Owin.Cors` package qui contient l’intergiciel (middleware) pour le traitement des demandes HTTP de cross-origin.

> [!NOTE]
> Prise en charge pour la signature de jetons Web JSON a été supprimé dans la version finale de Visual Studio 2013.

<a id="ef6"></a>
## <a name="entity-framework-6"></a>Entity Framework 6

Pour obtenir la liste des nouvelles fonctionnalités et d’autres modifications dans Entity Framework 6, consultez [Entity Framework Version historique](https://msdn.com/data/jj574253).

<a id="TOC14"></a>
## <a name="aspnet-razor-3"></a>ASP.NET Razor 3

ASP.NET Razor 3 inclut les nouvelles fonctionnalités suivantes :

- Prise en charge pour la modification de l’onglet. Preivously, le **Document au Format** commande, la mise en retrait automatique et automatique de mise en forme dans Visual Studio n’a pas fonctionné correctement lorsque vous utilisez la **conserver les tabulations** option. Cette modification résout Visual Studio pour le code Razor pour l’onglet mise en forme de mise en forme.
- Prise en charge pour les règles de réécriture d’URL lors de la génération des liens.
- Suppression de l’attribut transparent de sécurité.
 > [!NOTE]
 > Ceci est une modification avec rupture et rend Razor 3 incompatible avec MVC 4 et versions antérieures, tandis que Razor 2 n’est pas compatible avec MVC5 ou des assemblys compilés avec MVC5.

Problèmes de Razor 3 corrigés dans Visual Studio 2013 à partir de versions préliminaires, vous pouvez trouver [ici](https://aspnetwebstack.codeplex.com/workitem/list/advanced?keyword=&status=Resolved%7cClosed&type=All&priority=All&release=All%7cv5.0%2bPreview%7cv5.0%2bRC%7cv5.0%2bRTM&assignedTo=All&component=Web%2bPages%252fRazor&reasonClosed=Fixed&sortField=LastUpdatedDate&sortDirection=Descending&page=0).

<a id="TOC15"></a>
## <a name="aspnet-app-suspend"></a>Suspension de l’application ASP.NET

ASP.NET App Suspend est une fonctionnalité de modification de jeu dans le .NET Framework 4.5.1 qui transforme radicalement l’expérience utilisateur et le modèle économique pour l’hébergement du grand nombre de sites ASP.NET sur un seul ordinateur. Pour plus d’informations, consultez [ASP.NET App Suspend – réactive partagé d’hébergement web .NET](https://blogs.msdn.com/b/dotnet/archive/2013/10/09/asp-net-app-suspend-responsive-shared-net-web-hosting.aspx).

<a id="knownissues"></a>
## <a name="known-issues-and-breaking-changes"></a>Problèmes connus et les modifications avec rupture

Cette section décrit les problèmes connus et les modifications avec rupture dans ASP.NET et Web Tools pour Visual Studio 2013.

### <a name="nuget"></a>NuGet

- [Restauration du package ne fonctionne pas sur Mono lorsque vous utilisez les fichiers SLN](https://nuget.codeplex.com/workitem/3596) – sera résolu dans un téléchargement de nuget.exe à venir et [NuGet.CommandLine package](http://www.nuget.org/packages/NuGet.CommandLine/) mettre à jour.
- [Restauration du package ne fonctionne pas avec les projets Wix](https://nuget.codeplex.com/workitem/3598) – sera résolu dans un téléchargement de nuget.exe à venir et [NuGet.CommandLine package](http://www.nuget.org/packages/NuGet.CommandLine/) mettre à jour.
- [Restauration des packages automatique ne fonctionne pas pour les projets dans un dossier de solution](https://nuget.codeplex.com/workitem/3625) – sera résolu dans NuGet 2.8.

### <a name="aspnet-web-api"></a>API web ASP.NET

1. `ODataQueryOptions<T>.ApplyTo(IQueryable)`ne retourne pas `IQueryable<T>` toujours, que nous avons ajouté la prise en charge de `$select` et `$expand`.

    Nos exemples précédents pour `ODataQueryOptions<T>` toujours convertir la valeur de retour à partir de `ApplyTo` à `IQueryable<T>`. Cela fonctionnait précédemment, car les options de la requête que nous avons pris en charge précédemment (`$filter`, `$orderby`, `$skip`, `$top`) ne modifiez pas la forme de la requête. Maintenant que nous prenons en charge `$select` et `$expand` la valeur de retour à partir de `ApplyTo` ne sera pas `IQueryable<T>` toujours.

    [!code-csharp[Main](release-notes/samples/sample21.cs)]

    Si vous utilisez l’exemple de code précédemment, il continuera de fonctionner si le client n’envoie pas `$select` et `$expand`. Toutefois, si vous souhaitez prendre en charge `$select` et `$expand` vous devez modifier le code pour cela.

    [!code-csharp[Main](release-notes/samples/sample22.cs)]
2. **Request.Url ou RequestContext.Url a la valeur null lors d’une demande de traitement par lots**

    Dans un scénario de traitement par lot, **UrlHelper** a la valeur null lors de l’accès à partir de **Request.Url** ou **RequestContext.Url**.

    Ce problème est suivi actuellement ici : [BatchRequestContext.Url a la valeur null pour la demande de traitement par lot](http://aspnetwebstack.codeplex.com/workitem/1301).

    La solution de contournement pour résoudre ce problème consiste à créer une nouvelle instance de **UrlHelper**, comme dans l’exemple suivant :

    **Création d’une nouvelle instance de UrlHelper**

    [!code-csharp[Main](release-notes/samples/sample23.cs)]

### <a name="aspnet-mvc"></a>ASP.NET MVC

1. Lorsque vous utilisez MVC5 et OrgAuth, si vous avez des affichages qui effectuer la validation de AntiForgerToken, vous pouvez trouver l’erreur suivante lors de la validation de données à la vue :

    **Erreur**:

    *Erreur de serveur dans l’Application '/'.*

    *Une revendication de type 'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier' ou 'http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider' n’était pas présente sur le ClaimsIdentity fourni. Pour activer la prise en charge avec l’authentification basée sur les revendications de jeton anti-contrefaçon, vérifiez que le fournisseur de revendications configurée fournit les deux de ces revendications sur les instances de ClaimsIdentity qu’il génère. Si le fournisseur de revendications configurée utilise à la place un type de revendication différent comme identificateur unique, il peut être configuré en définissant la propriété statique AntiForgeryConfig.UniqueClaimTypeIdentifier.*

    **Solution de contournement**:

    Dans Global.asax pour résoudre ce problème, ajoutez la ligne suivante :

    `AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.Name;`

    Ce problème sera résolu dans la prochaine version.
2. Après la mise à niveau d’une application MVC4 vers MVC5, générez la solution et lancez-le. Vous devez voir l’erreur suivante :

    [A] System.Web.WebPages.Razor.Configuration.HostSection ne peut pas être converti en [B]System.Web.WebPages.Razor.Configuration.HostSection. Le type A provient ' System.Web.WebPages.Razor, Version = 2.0.0.0, Culture = neutral, PublicKeyToken = 31bf3856ad364e35' dans le contexte 'Default' à l’emplacement ' C:\windows\Microsoft.Net\assembly\GAC\_MSIL\System.Web.WebPages.Razor\ v4.0\_2.0.0.0\_\_31bf3856ad364e35\System.Web.WebPages.Razor.dll'. Type B provient « System.Web.WebPages.Razor, Version = 3.0.0.0, Culture = neutral, PublicKeyToken = 31bf3856ad364e35' dans le contexte 'Default' à l’emplacement ' C:\Windows\Microsoft.NET\Framework\v4.0.30319\Temporary ASP.NET Files\root\6d05bbd0\ e8b5908e\assembly\dl3\c9cbca63\f8910382\_6273ce01\System.Web.WebPages.Razor.dll'.

    Pour corriger l’erreur ci-dessus, ouvrez *tous les* les fichiers Web.config (y compris celles figurant dans le dossier Views) dans votre projet, puis effectuez les éléments suivants :

    1. Mettre à jour toutes les occurrences de la version « 4.0.0.0 » de « System.Web.Mvc » à « 5.0.0.0 ».
    2. Mettre à jour toutes les occurrences de la version « 2.0.0.0 » de « System.Web.Helpers », &quot;System.Web.WebPages&quot; et &quot;System.Web.WebPages.Razor&quot; à « 3.0.0.0 »

    Par exemple, après avoir apporté les modifications ci-dessus, les liaisons d’assembly doivent ressembler à ceci :

    [!code-xml[Main](release-notes/samples/sample24.xml)]

    Pour plus d’informations sur la mise à niveau des projets MVC 4 à 5 de MVC, consultez [la mise à niveau un ASP.NET MVC 4 et le projet d’API Web ASP.NET MVC 5 et Web API 2](../../../mvc/overview/releases/how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2.md).
3. Lorsque vous utilisez la validation côté client avec jQuery Validation discrète, le message de validation est parfois incorrect pour un élément d’entrée HTML avec type = 'number'. L’erreur de validation pour une valeur requise (« champ de l’âge est requis ») s’affichent lorsqu’un nombre non valide est entré à la place du message correct, qu’un nombre valid est requis.

    Ce problème est couramment trouvé avec le code de modèle généré automatiquement pour un modèle avec une propriété entière sur les vues de créer et modifier.

    Pour contourner ce problème, modifiez l’application d’assistance de l’éditeur à partir de :

    `@Html.EditorFor(person => person.Age)`

    À :

    `@Html.TextBoxFor(person => person.Age)`
4. ASP.NET MVC 5 ne prend plus en mode de confiance partielle. Projets de liaison pour les binaires MVC ou WebAPI doivent supprimer le [SecurityTransparent](https://msdn.microsoft.com/en-us/library/system.security.securitytransparentattribute.aspx) attribut et la [AllowPartiallyTrustedCallers](https://msdn.microsoft.com/en-us/library/system.security.allowpartiallytrustedcallersattribute.aspx) attribut. Suppression de ces attributs permet d’éliminer les erreurs du compilateur tels que les éléments suivants.

    `Attempt by security transparent method ‘MyComponent' to access security critical type 'System.Web.Mvc.MvcHtmlString' failed. Assembly 'PagedList.Mvc, Version=4.3.0.0, Culture=neutral, PublicKeyToken=abbb863e9397c5e1' is marked with the AllowPartiallyTrustedCallersAttribute, and uses the level 2 security transparency model. Level 2 transparency causes all methods in AllowPartiallyTrustedCallers assemblies to become security transparent by default, which may be the cause of this exception.`

    > Notez que comme un effet secondaire de ce que vous ne pouvez pas utiliser des assemblys 4.0 et 5.0 dans la même application. Vous devez mettre à jour tous les 5.0.

### <a name="spa-template-with-facebook-authorization-may-cause-instability-in-ie-while-the-web-site-is-hosted-in-intranet-zone"></a>Modèle SPA disposant d’autorisations de Facebook peut entraîner une instabilité dans Internet Explorer alors que le site web est hébergé dans la zone intranet

Le modèle SPA fournit un journal externe avec Facebook. Lorsque le projet créé avec le modèle est en cours d’exécution localement, risque d’Internet Explorer bloquer l’ouverture de session.

Solution :

1. Héberger le site web dans la zone internet ; ou

2. Le scénario de test dans un navigateur autre qu’Internet Explorer.

### <a name="web-forms-scaffolding"></a>Web Forms de génération de modèles automatique

La structure de Web Forms VS2013 a été supprimée et sera disponible dans une prochaine mise à jour pour Visual Studio. Toutefois, vous pouvez toujours utiliser la structure dans un projet Web Forms en ajoutant des dépendances MVC et génération de génération de modèles automatique pour MVC. Votre projet contient une combinaison de Web Forms et MVC.

Pour ajouter MVC à votre projet Web Forms, ajouter un nouvel élément de modèle généré automatiquement et sélectionnez **MVC 5 dépendances**. Sélectionnez un minimum ou complète selon que vous devez tous les fichiers de contenu, tels que les scripts. Ensuite, ajoutez un élément généré automatiquement pour MVC, ce qui créera un contrôleur et les vues dans votre projet.

### <a name="mvc-and-web-api-scaffolding---http-404-not-found-error"></a>MVC et structure d’API Web - HTTP 404, erreur introuvable

Si une erreur s’est produite lors de l’ajout d’un élément généré automatiquement à un projet, il est possible que votre projet reste dans un état incohérent. Certaines des modifications apportées à la structure d’être seront restaurée, mais d’autres modifications, telles que les packages NuGet installés, ne seront pas restaurées. Si les modifications de configuration de routage sont annulées, les utilisateurs recevront une erreur HTTP 404 lors de la navigation vers structuré des éléments.

Solution de contournement :

- Pour corriger cette erreur pour MVC, ajouter un nouvel élément de modèle généré automatiquement, puis sélectionnez les dépendances de MVC 5 (complète ou minimale). Ce processus sera ajouter toutes les modifications nécessaires à votre projet.
- Pour corriger cette erreur pour l’API Web :

    1. Ajoutez la classe WebApiConfig à votre projet.

        [!code-csharp[Main](release-notes/samples/sample25.cs)]

        [!code-vb[Main](release-notes/samples/sample26.vb)]
    2. Configurer WebApiConfig.Register dans l’Application\_Start (méthode) dans Global.asax, comme suit :

        [!code-csharp[Main](release-notes/samples/sample27.cs)]

        [!code-vb[Main](release-notes/samples/sample28.vb)]
