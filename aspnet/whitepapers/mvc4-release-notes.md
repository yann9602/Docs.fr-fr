---
uid: whitepapers/mvc4-release-notes
title: ASP.NET MVC 4 | Documents Microsoft
author: rick-anderson
description: "Ce document décrit la version de ASP.NET MVC 4."
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/09/2011
ms.topic: article
ms.assetid: f014524f-25c0-4094-b8e1-886d99536f00
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /whitepapers/mvc4-release-notes
msc.type: content
ms.openlocfilehash: 399fbe3fa1e71a9ffa7c5e6dfeca7ccab7294d1b
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="aspnet-mvc-4"></a>ASP.NET MVC 4
====================
> Ce document décrit la version de ASP.NET MVC 4.


- [Notes d’installation](#_Toc303253802)
- [Documentation](#_Toc303253803)
- [Support](#_Toc303253804)
- [Configuration logicielle requise](#_Toc303253805)
- [Nouvelles fonctionnalités dans ASP.NET MVC 4](#_Toc303253807)

    - [API Web ASP.NET](#_Toc317096197)
    - [Améliorations apportées aux modèles de projet par défaut](#_Toc303253808)
    - [Modèle de projet mobile](#_Toc303253809)
    - [Modes d’affichage](#_Toc303253810)
    - [jQuery Mobile, le sélecteur de vue et la substitution de navigateur](#_Toc303253811)
    - [Prise en charge de la tâche pour les contrôleurs asynchrones](#_Toc303253813)
    - [Kit de développement logiciel Azure](#_Toc303253814)
    - [Migrations de la base de données](#_Toc303253818)
    - [Modèle de projet vide](#_Toc303253819)
    - [Ajouter un contrôleur à n’importe quel dossier de projet](#_Toc303253820)
    - [Bundles et minimisation](#_Toc303253821)
    - [L’activation des connexions à partir de Facebook et d’autres Sites à l’aide d’OAuth et OpenID](#_Toc303253822)
- [La mise à niveau d’un projet ASP.NET MVC 3 vers ASP.NET MVC 4](#_Toc303253806)
- [Modifications à partir de la version Release Candidate de ASP.NET MVC 4](#_Toc303253817)
- [Problèmes connus et les modifications avec rupture](#_Toc303253815)

<a id="_Toc303253802"></a>
## <a name="installation-notes"></a>Notes d’installation

ASP.NET MVC 4 pour Visual Studio 2010 peut être installé à partir de la [page d’accueil ASP.NET MVC 4](../mvc/mvc4.md) à l’aide de Web Platform Installer.

Nous vous recommandons de désinstaller les aperçus précédemment installées de ASP.NET MVC 4 avant d’installer ASP.NET MVC 4. Vous pouvez mettre à niveau la version bêta d’ASP.NET MVC 4 et une version Release Candidate vers ASP.NET MVC 4 sans désinstaller.

Cette version n’est pas compatible avec les versions préliminaires de .NET Framework 4.5. Doit séparément mise à niveau n’importe quel installé préversions de .NET Framework 4.5 vers la version finale avant d’installer ASP.NET MVC 4.

ASP.NET MVC 4 peut être installé et exécuté côte à côte avec ASP.NET MVC 3.

<a id="_Toc303253803"></a>
## <a name="documentation"></a>Documentation

Documentation pour ASP.NET MVC est disponible sur le site Web MSDN à l’adresse suivante :

[https://go.microsoft.com/fwlink/?LinkID=243043](https://go.microsoft.com/fwlink/?LinkID=243043)

Didacticiels et autres informations sur ASP.NET MVC sont disponibles sur la page MVC 4 du site Web ASP.NET ([https://www.asp.net/mvc/mvc4](../mvc/mvc4.md)).

<a id="_Toc303253804"></a>
## <a name="support"></a>Assistance

ASP.NET MVC 4 est entièrement pris en charge. Si vous avez des questions sur l’utilisation de cette version vous pouvez également de publier les sur le forum ASP.NET MVC ([https://forums.asp.net/1146.aspx](https://forums.asp.net/1146.aspx)), où les membres de la Communauté ASP.NET peuvent souvent fournir un support informel.

<a id="_Toc303253805"></a>
## <a name="software-requirements"></a>Configuration logicielle

Les composants d’ASP.NET MVC 4 pour Visual Studio requièrent PowerShell 2.0 et Visual Studio 2010 avec Service Pack 1 ou Visual Web Developer Express 2010 avec Service Pack 1.

<a id="_Toc303253807"></a>
## <a name="new-features-in-aspnet-mvc-4"></a>Nouvelles fonctionnalités dans ASP.NET MVC 4

Cette section décrit les fonctionnalités qui ont été introduites dans la version d’ASP.NET MVC 4.

<a id="_Toc317096197"></a>
### <a name="aspnet-web-api"></a>API web ASP.NET

ASP.NET MVC 4 inclut ASP.NET Web API, une nouvelle infrastructure pour créer des services HTTP pouvant atteindre un large éventail de clients, y compris les navigateurs et périphériques mobiles. API Web ASP.NET est également une plateforme idéale pour construire des services RESTful.

ASP.NET Web API prend en charge les fonctionnalités suivantes :

- **Modèle de programmation HTTP moderne :** directement accéder et manipuler les requêtes HTTP et des réponses dans vos API Web à l’aide d’un modèle d’objet HTTP nouvelle et fortement typé. Le même modèle et le pipeline HTTP de programmation est symétriquement disponible sur le client via le nouveau *HttpClient* type.
- **Prise en charge pour les itinéraires plein :** ASP.NET Web API prend en charge l’ensemble complet des fonctionnalités d’itinéraire de routage ASP.NET, y compris les paramètres d’itinéraire et des contraintes. En outre, utilisez les conventions simples pour mapper actions aux méthodes HTTP.
- **Négociation de contenu :** le client et le serveur peuvent collaborer pour déterminer le format approprié pour les données retournées à partir d’une API web. API Web ASP.NET fournit la prise en charge par défaut pour XML, JSON, et encodé en URL de formulaire de formats et que vous pouvez étendre cette prise en charge en ajoutant vos propres formateurs, ou même remplacer la stratégie de négociation de contenu par défaut.
- **Liaison et la validation de modèle :** classeurs de modèles fournissent un moyen simple pour extraire des données de différentes parties d’une requête HTTP et convertir les parties de message en objets .NET qui peuvent être utilisées par les actions de l’API Web. La validation est également effectuée sur les paramètres d’action basés sur les annotations de données.
- **Filtres :** API Web ASP.NET prend en charge les filtres, notamment les filtres bien connus tels que le *[Authorize]* attribut. Vous pouvez créer et incorporer vos propres filtres pour les actions, d’autorisation et la gestion des exceptions.
- **Composition de la requête :** utilisez la *[Queryable]* attribut de filtre sur une action qui retourne *IQueryable* pour activer la prise en charge pour l’interrogation de votre API web via les conventions de requête OData.
- **Améliorer la testabilité :** au lieu de définir les détails HTTP dans les objets de contexte statique, web travail des actions API avec des instances de *HttpRequestMessage* et *HttpResponseMessage*. Créer un projet de test unitaire en même temps que votre projet d’API Web pour commencer à écrire rapidement des tests unitaires pour les fonctionnalités de votre API Web.
- **Configuration basée sur le code :** configuration de l’API Web ASP.NET est effectuée uniquement par le biais de code, de nettoyer les fichiers en laissant votre configuration. Utilisez le modèle de recherche de service fourni pour configurer des points d’extensibilité.
- **Prise en charge pour les conteneurs d’Inversion de contrôle (IoC) améliorée :** API Web ASP.NET fournit excellent support pour les conteneurs d’inversion de contrôle via une abstraction de programme de résolution des dépendances améliorée
- **Auto-hébergement :** API Web peut être hébergé dans votre propre processus, en plus de IIS tout en utilisant la puissance d’itinéraires et d’autres fonctionnalités de l’API Web.
- **Créer une aide personnalisée et pages de test :** vous pouvez facilement générer aide personnalisée et pages de test pour votre API web à l’aide de la nouvelle *IApiExplorer* service pour obtenir une description de l’exécution complète de votre API web.
- **Analyse et diagnostics :** API Web ASP.NET fournit désormais l’infrastructure de suivi léger qui le rend facile à intégrer avec des solutions de journalisation existantes telles que System.Diagnostics, ETW et infrastructures de journalisation d’un tiers. Vous pouvez activer le traçage en fournissant une *ITraceWriter* mise en œuvre et en l’ajoutant à votre configuration de l’API web.
- **Lier la génération :** utiliser l’API Web ASP.NET *UrlHelper* pour générer des liens vers des ressources connexes dans la même application.
- **Modèle de projet Web API :** sélectionner le nouveau formulaire de projet l’Assistant Nouveau projet MVC 4 pour obtenir rapidement en cours d’exécution avec l’API Web ASP.NET Web API.
- **Génération de modèles automatique :** utilisez la **ajouter un contrôleur** boîte de dialogue rapidement structurez un contrôleur d’API web basé sur un Entity Framework en fonction de type de modèle.

Pour plus d’informations sur l’API Web ASP.NET, consultez [https://www.asp.net/web-api](../web-api/index.md).

<a id="_Toc303253808"></a>
### <a name="enhancements-to-default-project-templates"></a>Améliorations apportées aux modèles de projet par défaut

Le modèle est utilisé pour créer des projets ASP.NET MVC 4 a été mis à jour pour créer un site Web aspect plus moderne :

![](mvc4-release-notes/_static/image1.png)

En plus des améliorations esthétique, il présente des fonctions améliorées dans le nouveau modèle. Le modèle utilise une technique appelée un rendu adaptable pour s’afficher correctement dans les navigateurs de bureau et les navigateurs mobiles sans aucune personnalisation.

![](mvc4-release-notes/_static/image2.png)

Pour afficher un rendu adaptable en action, vous pouvez utiliser un émulateur mobile ou essayez simplement le redimensionnement de la fenêtre de navigateur de bureau pour être plus petite. Lorsque la fenêtre du navigateur obtient suffisamment petite, la disposition de la page change.

<a id="_Toc303253809"></a>
### <a name="mobile-project-template"></a>Modèle de projet mobile

Si vous commencez un nouveau projet et que vous souhaitez créer un site en particulier pour mobile et les navigateurs de votre tablette, vous pouvez utiliser le nouveau modèle de projet d’Application Mobile. Il est basé sur jQuery Mobile, une bibliothèque open source pour la création de l’interface utilisateur des fonctionnalités tactiles :

![](mvc4-release-notes/_static/image3.png)

Ce modèle contient la même structure d’application que le modèle d’Application Internet (et le code du contrôleur est pratiquement identique), mais il s’agit d’un style à l’aide de jQuery Mobile bonnes et se comportent correctement sur les appareils tactiles. Pour plus d’informations sur la structure et le style de l’interface utilisateur mobile, consultez le [site Web de projet Mobile jQuery](http://jquerymobile.com/).

Si vous avez déjà un site orienté sur le bureau que vous souhaitez ajouter des vues mobile optimisé, ou si vous souhaitez créer un site unique qui fait Office de vues stylisés différemment pour les navigateurs de bureau et mobiles, vous pouvez utiliser la nouvelle fonctionnalité de Modes d’affichage. (Consultez la section suivante.)

<a id="_Toc303253810"></a>
### <a name="display-modes"></a>Modes d’affichage

La nouvelle fonctionnalité de Modes d’affichage permet à une application de sélectionner les vues selon le navigateur qui effectue la demande. Par exemple, si un navigateur demande la page d’accueil, l’application peut utiliser le modèle Views\Home\Index.cshtml. Si un navigateur mobile demande à la page d’accueil, l’application peut retourner le modèle Views\Home\Index.mobile.cshtml.

Dispositions et aucun peut également être remplacées pour les types de navigateur particulier. Exemple :

- Si votre dossier Views\Shared contient à la fois le \_Layout.cshtml et \_modèles Layout.mobile.cshtml, par défaut, l’application utilisera \_Layout.mobile.cshtml lors des demandes à partir de navigateurs mobiles et \_Layout.cshtml au cours des autres requêtes.
- Si un dossier contient à la fois \_MyPartial.cshtml et \_MyPartial.mobile.cshtml, l’instruction @Html.Partial(«\_MyPartial ») sera rendue \_MyPartial.mobile.cshtml lors des demandes de mobile navigateurs, et \_MyPartial.cshtml au cours des autres requêtes.

Si vous souhaitez créer des vues plus spécifiques, des dispositions ou des vues partielles pour d’autres périphériques, vous pouvez enregistrer un nouveau *DefaultDisplayMode* instance pour spécifier le nom à rechercher lors d’une demande est conforme aux conditions spécifiques. Par exemple, vous pouvez ajouter le code suivant à la *Application\_Démarrer* méthode dans le fichier Global.asax pour enregistrer la chaîne « iPhone » comme un mode d’affichage qui s’applique lorsque le navigateur Apple iPhone qui effectue une demande :

[!code-csharp[Main](mvc4-release-notes/samples/sample1.cs)]

Une fois ce code s’exécute lorsqu’un navigateur Apple iPhone qui effectue une demande, votre application utilisera le Views\Shared\\_Layout.iPhone.cshtml disposition (si elle existe). Pour plus d’informations sur le Mode d’affichage, consultez [ASP.NET MVC 4 Mobile fonctionnalités](../mvc/overview/older-versions/aspnet-mvc-4-mobile-features.md). Installent des applications à l’aide de DisplayModeProvider les [DisplayModes fixe](http://nuget.org/packages/Microsoft.AspNet.Mvc.FixedDisplayModes) package NuGet. Le [ASP.NET automne 2012 Update](https://go.microsoft.com/fwlink/?LinkID=271322) inclut la [DisplayModes fixe](http://nuget.org/packages/Microsoft.AspNet.Mvc.FixedDisplayModes) package NuGet dans les nouveaux modèles de projet. Consultez [Fixedd de bogue mise en cache ASP.NET MVC 4 Mobile](https://blogs.msdn.com/b/rickandy/archive/2012/09/17/asp-net-mvc-4-mobile-caching-bug-fixed.aspx) pour plus d’informations sur le correctif.

<a id="_Toc303253811"></a>
### <a name="jquery-mobile-and-mobile-features"></a>jQuery Mobile et les fonctionnalités de Mobile

Pour plus d’informations sur la création d’applications mobiles avec ASP.NET MVC 4 à l’aide de jQuery Mobile, consultez le didacticiel [ASP.NET MVC 4 Mobile fonctionnalités](../mvc/overview/older-versions/aspnet-mvc-4-mobile-features.md).

<a id="_Toc303253813"></a>
### <a name="task-support-for-asynchronous-controllers"></a>Prise en charge de la tâche pour les contrôleurs asynchrones

Vous pouvez maintenant écrire des méthodes d’action asynchrones des méthodes comme unique qui retournent un objet de type *tâche* ou *tâche&lt;ActionResult&gt;*.

 Pour plus d’informations, consultez [à l’aide de méthodes asynchrones dans ASP.NET MVC 4](../mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4.md).

<a id="_Toc303253814"></a>
### <a name="azure-sdk"></a>Kit de développement logiciel Azure

ASP.NET MVC 4 prend en charge les versions 1.6 et les versions ultérieures de Windows Azure SDK.

<a id="_Toc303253818"></a>
### <a name="database-migrations"></a>Migrations de la base de données

Les projets ASP.NET MVC 4 incluent désormais Entity Framework 5. Une des fonctionnalités dans Entity Framework 5 est prise en charge pour les migrations de la base de données. Cette fonctionnalité vous permet facilement faire évoluer votre schéma de base de données à l’aide d’une migration axée sur le code tout en conservant les données dans la base de données. Pour plus d’informations sur les migrations de la base de données, consultez [ajoutant un nouveau champ à la Table et au modèle de film](../mvc/overview/older-versions/getting-started-with-aspnet-mvc4/adding-a-new-field-to-the-movie-model-and-table.md) dans les [Introduction au didacticiel d’ASP.NET MVC 4](../mvc/overview/older-versions/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4.md).

<a id="_Toc303253819"></a>
### <a name="empty-project-template"></a>Modèle de projet vide

Le modèle de projet MVC vide est désormais réellement vide afin que vous pouvez démarrer à partir de zéro complètement. La version antérieure du modèle de projet vide a été renommée de base.

<a id="_Toc303253820"></a>
### <a name="add-controller-to-any-project-folder"></a>Ajouter un contrôleur à n’importe quel dossier de projet

Vous pouvez maintenant clic droit et sélectionnez **ajouter un contrôleur** à partir de n’importe quel dossier de votre projet MVC. Cela vous donne plus de souplesse pour organiser vos contrôleurs comme vous le souhaitez, y compris la conservation de vos contrôleurs MVC et les API Web dans des dossiers distincts.

<a id="_Toc303253821"></a>
### <a name="bundling-and-minification"></a>Groupement et minimisation

Le groupement et la minimisation framework vous permet de réduire le nombre de requêtes HTTP qu’une page Web doit effectuer en combinant des fichiers individuels dans un seul fichier fourni pour les scripts et CSS. Il peut ensuite réduire la taille globale de ces demandes par réduire le contenu de l’application. Réduire peut inclure des activités, telles que la suppression des espaces blancs pour raccourcir les noms de variables à même de réduction des sélecteurs CSS en fonction de leur sémantique. Offres groupées sont déclarées et configurées dans code et sont facilement référencés dans les vues via les méthodes d’assistance qui peuvent générer soit un lien vers le groupe ou, lors du débogage, plusieurs liens au contenu de l’offre groupée individuel. Pour plus d’informations, consultez [Bundling and Minification](../mvc/overview/performance/bundling-and-minification.md).

<a id="_Toc303253822"></a>
### <a name="enabling-logins-from-facebook-and-other-sites-using-oauth-and-openid"></a>L’activation des connexions à partir de Facebook et d’autres Sites à l’aide d’OAuth et OpenID

Les modèles par défaut dans le modèle de projet d’Internet ASP.NET MVC 4 inclut désormais la prise en charge de connexion OAuth et OpenID à l’aide de la bibliothèque DotNetOpenAuth. Pour plus d’informations sur la configuration d’un fournisseur OAuth ou OpenID, consultez [prise en charge OAuth/OpenID pour Web Forms, MVC et les pages Web](https://blogs.msdn.com/b/webdev/archive/2012/08/15/oauth-openid-support-for-webforms-mvc-and-webpages.aspx) et [OAuth et OpenID feature in ASP.NET Web Pages de documentation](../web-pages/overview/releases/top-features-in-web-pages-2.md#oauthsetup).

<a id="_Toc303253806"></a>
## <a name="upgrading-an-aspnet-mvc-3-project-to-aspnet-mvc-4"></a>La mise à niveau d’un projet ASP.NET MVC 3 vers ASP.NET MVC 4

ASP.NET MVC 4 peut être installé côte à côte avec ASP.NET MVC 3 sur le même ordinateur, ce qui vous donne la souplesse dans le choix quand mettre à niveau une application ASP.NET MVC 3 vers ASP.NET MVC 4.

La façon la plus simple pour mettre à niveau est pour créer un nouveau projet ASP.NET MVC 4 et la copie toutes les vues, contrôleurs, code et le contenu des fichiers à partir du projet MVC 3 existant vers le nouveau projet et pour mettre à jour de l’assembly de référence dans le nouveau projet pour faire correspondre un modèle non-MVC dans assembiles faisant partie de ce que vous utilisez. Si vous avez apporté des modifications au fichier Web.config dans le projet MVC 3, vous devez également fusionner ces modifications dans le fichier Web.config dans le projet MVC 4.

Pour mettre à niveau une application ASP.NET MVC 3 existante vers la version 4, procédez comme suit :

1. Dans le fichier Web.config de tous les fichiers dans le projet (il existe à la racine du projet, dans le dossier vues et un dans le dossier Views de chaque domaine dans votre projet), remplacer le texte suivant (Remarque : System.Web.WebPages, Version = 1.0.0.0 est introuvable dans projets créés avec Visual Studio 2012) : 

    [!code-console[Main](mvc4-release-notes/samples/sample2.cmd)]

    avec le texte correspondant suivant :

    [!code-console[Main](mvc4-release-notes/samples/sample3.cmd)]
2. Dans le fichier Web.config racine, mettez à jour le *webPages:Version* élément « 2.0.0.0 » et ajouter un nouveau *PreserveLoginUrl* clé qui a la valeur « true » : 

    [!code-xml[Main](mvc4-release-notes/samples/sample4.xml)]
3. Dans l’Explorateur de solutions, avec le bouton droit sur les références, puis sélectionnez Gérer les Packages NuGet. Dans le volet gauche, sélectionnez **source de package officielle Online\NuGet**, puis mettre à jour les éléments suivants :

    - ASP.NET MVC 4
    - (Facultatif) jQuery, jQuery Validation et jQuery UI
    - (Facultatif) Entity Framework
    - (Optonal) Modernizr
4. Dans l’Explorateur de solutions, cliquez sur le nom du projet, puis sélectionnez Décharger le projet. Cliquez à nouveau sur le nom, puis sélectionnez Modifier *nom_projet*.csproj.
5. Recherchez le *ProjectTypeGuids* élément et remplacez {E53F8FEA-EAE0-44A6-8774-FFD645390401} par {E3E379DF-F4C6-4180-9B81-6769533ABE47}.
6. Enregistrer les modifications, fermez le fichier projet (.csproj) que vous avez modifié, cliquez sur le projet, puis sélectionnez recharger le projet.
7. Si le projet fait référence à des bibliothèques tierces qui sont compilés à l’aide de versions précédentes d’ASP.NET MVC, ouvrez le fichier Web.config racine et ajouter les trois suivantes *bindingRedirect* éléments sous le  *configuration* section : 

    [!code-xml[Main](mvc4-release-notes/samples/sample5.xml)]

<a id="_Toc303253817"></a>
## <a name="changes-from-aspnet-mvc-4-release-candidate"></a>Modifications à partir de la version Release Candidate de ASP.NET MVC 4

Vous trouverez ici des notes de publication des ASP.NET MVC 4 Release Candidate :

Principales modifications apportées à partir d’ASP.NET MVC 4 Release Candidate dans cette version sont résumées ci-dessous :

- **Par la configuration du contrôleur :** contrôleurs de l’API Web ASP.NET peuvent être attribués avec un attribut personnalisé qui implémente *IControllerConfiguration* configurer leurs propres formateurs, sélecteur d’action et de classeurs de paramètre . Le *HttpControllerConfigurationAttribute* a été supprimé.
- **Par les gestionnaires de messages de routage :** vous pouvez maintenant spécifier le Gestionnaire de messages final dans la chaîne de requête pour un itinéraire donné. Cela permet la prise en charge des infrastructures le long de gestion utilise le routage pour la distribution à leur propre (non -*IHttpController*) points de terminaison.
- **Notifications de progression :** le *ProgressMessageHandler* génère la notification de progression pour les entités de la demande en cours de téléchargement et des entités de réponse en cours de téléchargement. À l’aide de ce gestionnaire, il est possible d’effectuer le suivi de déterminer jusqu’où vous téléchargement d’un corps de demande ou téléchargement d’un corps de réponse.
- **Contenu transféré :** le *PushStreamContent* classe permet des scénarios où un producteur de données souhaite écrire directement dans la demande ou la réponse (synchrone ou asynchrone) à l’aide d’un flux de données. Lorsque le *PushStreamContent* est prêt à accepter des données, il appelle un délégué d’action avec le flux de sortie. Le développeur peut ensuite écrire dans le flux pour aussi longtemps que nécessaire et fermer le flux de données lors de l’écriture est terminée. Le *PushStreamContent* détecte la fermeture du flux de données et se termine sous-jacent asynchrone *tâche* pour écrire le contenu.
- **Création de réponses d’erreur :** utilisez la *HttpError* type pour représenter constamment des informations d’erreur à partir de tels que les erreurs de validation et d’exceptions tout en respectant encore le *IncludeErrorDetailPolicy* . Utilisez la nouvelle *CreateErrorResponse* les méthodes d’extension pour créer facilement des réponses d’erreur avec *HttpError* en tant que contenu. Le *HttpError* le contenu est entièrement contenu négocié.
- **MediaRangeMapping supprimé :** plages de types de média sont désormais gérés par le négociateur de contenu par défaut.
- **Liaison de paramètre par défaut pour les paramètres de type simple est désormais [FromUri] :** dans les versions précédentes d’ASP.NET Web API la liaison de paramètre par défaut pour les paramètres de type simple utilisé la liaison de modèle. La liaison de paramètre par défaut pour les paramètres de type simple est désormais *[FromUri]*.
- **Sélection d’action respecte les paramètres requis :** Action dans ASP.NET Web API désormais uniquement sélectionne une action si tous les paramètres requis qui proviennent de l’URI sont fournis. Un paramètre peut être spécifié comme étant facultatifs en fournissant une valeur par défaut pour l’argument de la signature de méthode d’action.
- **Personnaliser les liaisons de paramètres HTTP :** utiliser le *ParameterBindingAttribute* pour personnaliser la liaison de paramètre pour un paramètre d’action spécifique, ou utilisez le *ParameterBindingRules* sur le *HttpConfiguration* pour personnaliser les liaisons de paramètres plus large.
- **Améliorations de MediaTypeFormatter :** formateurs ont désormais accès à la version complète *HttpContent* instance.
- **Hôte de sélection de la stratégie de mise en mémoire tampon :** implémenter et configurer le *IHostBufferPolicySelector* service dans ASP.NET Web API pour activer des ordinateurs hôtes déterminer la stratégie quand la mise en mémoire tampon est à utiliser.
- **Accéder à des certificats de client de manière agnostique hôte :** utilisez la *GetClientCertificate* méthode d’extension pour obtenir le certificat client fourni dans le message de demande.
- **Extensibilité de la négociation de contenu :** personnaliser la négociation de contenu en dérivant de la *DefaultContentNegotiator* et en remplaçant tous les aspects de la négociation de contenu que vous souhaitez.
- **Prise en charge pour retourner les 406 réponses non Acceptable :** vous pouvez désormais retourner des réponses non Acceptable 406 dans ASP.NET Web API lorsqu’un formateur approprié est introuvable en créant un *DefaultContentNegotiator* avec la *excludeMatchOnTypeOnly* paramètre la valeur *true*.
- **Lire les données de formulaire en tant que NameValueCollection ou JToken :** vous pouvez lire les données de formulaire dans la chaîne de requête URI ou dans le corps de la demande en tant qu’un *NameValueCollection* à l’aide de la *parseQueryString, qui* et  *ReadAsFormDataAsync* les méthodes d’extension respectivement. De même, vous pouvez lire les données de formulaire dans la chaîne de requête URI ou dans le corps de la demande en tant qu’un *JToken* à l’aide de la *TryReadQueryAsJson* et *ReadAsAsync*&lt;T&gt; les méthodes d’extension respectivement.
- **Améliorations apportées à parties multiples :** il est désormais possible d’écrire un *MultipartStreamProvider* complètement adaptée au type de données MIME en plusieurs parties qu’il peut lire et présente le résultat de façon optimale à l’utilisateur. Vous pouvez également associer une étape de post-traitement sur le *MultipartStreamProvider* qui permet l’implémentation d’effectuer tout post-traitement il veut sur les parties de corps en plusieurs parties MIME. Par exemple, le *MultipartFormDataStreamProvider* implémentation lit des parties de données de formulaire HTML et les ajoute à une *NameValueCollection* afin qu’ils soient faciles à obtenir à partir de l’appelant.
- **Lier des améliorations de génération :** le *UrlHelper* ne dépend plus *HttpControllerContext*. Vous pouvez désormais accéder à la *UrlHelper* à partir de n’importe quel contexte où le *HttpRequestMessage* n’est disponible.
- **Changement d’ordre de l’exécution du Gestionnaire de messages :** gestionnaires de messages sont maintenant exécutées dans l’ordre dans lequel ils sont configurés dans l’ordre inverse à la place de.
- **Application d’assistance pour l’écriture des gestionnaires de messages :** le nouveau *HttpClientFactory* qui peut associer *DelegatingHandlers* et créer un *HttpClient* avec le prêt à passer de pipeline souhaité. Il fournit également des fonctionnalités pour la création avec les autres gestionnaires internes (la valeur par défaut est *HttpClientHandler*) ainsi que pour effectuer le câblage lors de l’utilisation *HttpMessageInvoker* ou un autre  *DelegatingHandler* au lieu de *HttpClient* en tant que le demandeur de haut.
- **Prise en charge pour le CDN dans ASP.NET Web optimisation :** ASP.NET Web optimisation prend désormais en charge pour le CDN autres chemins d’accès vous permettant de spécifier pour chaque regroupement d’une URL supplémentaire qui pointe vers cette ressource même sur un réseau de diffusion de contenu. Prise en charge CDN permet d’obtenir vos groupes de script et le style géographiquement proche aux consommateurs de fin de vos applications Web.
- **Achemine les API Web ASP.NET et de configuration est déplacé vers *WebApiConfig.Register* une méthode statique qui peut être resused dans le code de test.** Itinéraires de l’API Web ASP.NET ont été précédemment ajoutés dans *RouteConfig.RegisterRoutes* , ainsi que le MVC standard route. La valeur par défaut de l’API Web ASP.NET achemine et la configuration sont maintenant gérées dans une fonction *WebApiConfig.Register* méthode pour faciliter le test.

<a id="_Toc303253815"></a>
## <a name="known-issues-and-breaking-changes"></a>Problèmes connus et les modifications avec rupture

- **La version RC et RTM d’ASP.NET MVC 4 retourné incorrectement des vues de bureau mises en cache lorsque les affichages mobiles doivent être retournés.**

    - Consultez [ASP.NET MVC 4 Mobile mise en cache bogue fixe](https://blogs.msdn.com/b/rickandy/archive/2012/09/17/asp-net-mvc-4-mobile-caching-bug-fixed.aspx) pour plus d’informations sur le correctif. Le correctif peut être installé à partir de la [DisplayModes fixe](http://nuget.org/packages/Microsoft.AspNet.Mvc.FixedDisplayModes) package NuGet.
- **Modifications avec rupture dans le moteur d’affichage Razor**. Les types suivants ont été supprimés de *System.Web.Mvc.Razor*: 

    - *ModelSpan*
    - *MvcVBRazorCodeGenerator*
    - *MvcCSharpRazorCodeGenerator*
    - *MvcVBRazorCodeParser*

 Les méthodes suivantes ont été également supprimés : 

    - *MvcCSharpRazorCodeParser.ParseInheritsStatement(System.Web.Razor.Parser.CodeBlockInfo)*
    - *MvcWebPageRazorHost.DecorateCodeGenerator(System.Web.Razor.Generator.RazorCodeGenerator)*
    - *MvcVBRazorCodeParser.ParseInheritsStatement(System.Web.Razor.Parser.CodeBlockInfo)*
- **Lorsque WebMatrix.WebData.dll est inclus dans le répertoire/bin d’une applications ASP.NET MVC 4, il adopte l’URL pour l’authentification par formulaire.** Ajout de l’assembly WebMatrix.WebData.dll à votre application (par exemple, en sélectionnant « ASP.NET Web Pages avec syntaxe Razor » lors de l’utilisation de la boîte de dialogue Ajouter des dépendances déployables) remplace la redirection de connexion d’authentification/compte d’ouverture de session au lieu de / compte de connexion comme prévu par le contrôleur de compte ASP.NET MVC par défaut. Pour éviter ce comportement et utiliser l’URL déjà spécifiée dans la section authentification de web.config, vous pouvez ajouter un appSetting appelé PreserveLoginUrl et affectez-lui la valeur true : 

    [!code-xml[Main](mvc4-release-notes/samples/sample6.xml)]
- **Le Gestionnaire de package NuGet ne parvient pas à installer lors de la tentative d’installation d’ASP.NET MVC 4 pour les installations côte à côte de Visual Studio 2010 et Visual Web Developer 2010.** Pour exécuter Visual Studio 2010 et Visual Web Developer 2010 côte à côte avec ASP.NET MVC 4, vous devez installer ASP.NET MVC 4 après que les deux versions de Visual Studio ont déjà été installées.
- **Désinstallation de ASP.NET MVC 4 échoue si les conditions préalables ont déjà été désinstallés.** Pour désinstaller ASP.NET MVC 4Vous devez désinstaller ASP.NET MVC 4 avant de désinstaller Visual Studio.
- **L’installation d’ASP.NET MVC 4, les applications ASP.NET MVC 3 RTM s’arrête.** Les applications ASP.NET MVC 3 qui ont été créées avec la version RTM de version (pas avec la [ASP.NET MVC 3 Tools Update](https://www.microsoft.com/download/details.aspx?id=1491) release) nécessitent les modifications suivantes afin de fonctionner côte à côte avec ASP.NET MVC 4. Génération du projet sans apporter de ces résultats de mises à jour dans les erreurs de compilation. 

    **Mises à jour requises**

    1. Dans le fichier racine Web.config, ajoutez un nouvel  *&lt;appSettings&gt;*  entrée avec la clé *webPages:Version* et la valeur *1.0.0.0*. 

        [!code-xml[Main](mvc4-release-notes/samples/sample7.xml)]
    2. Dans l’Explorateur de solutions, cliquez sur le nom du projet, puis sélectionnez Décharger le projet. Cliquez à nouveau sur le nom, puis sélectionnez Modifier *nom_projet*.csproj.
    3. Recherchez les références d’assembly suivantes : 

        [!code-xml[Main](mvc4-release-notes/samples/sample8.xml)]

        Remplacez-les par les éléments suivants :

        [!code-xml[Main](mvc4-release-notes/samples/sample9.xml)]
    4. Enregistrer les modifications, fermez le fichier projet (.csproj) modifiiez, puis cliquez sur le projet et choisissez recharger.
- **La modification d’un projet ASP.NET MVC 4 à la cible 4.0 à partir de 4.5 ne met pas à jour la référence d’assembly EntityFramework :** si vous définissez un projet ASP.NET MVC 4 cible 4.0 après ciblé 4.5 pointe toujours vers la référence à l’assembly EntityFramework la version 4.5. Pour résoudre ce problème, désinstallez et réinstallez le package NuGet de EntityFramework.
- **403 Interdit lors de l’exécution d’une application ASP.NET MVC 4 sur Azure après le passage cible 4.0 à partir de 4.5 :** si vous modifiez un projet ASP.NET MVC 4 à cibler 4.0 après ciblé 4.5 et que vous déployez ensuite sur Azure, vous pouvez voir une erreur 403 Interdit lors de l’exécution. Pour contourner ce problème, ajoutez le code suivant à votre fichier web.config :`<modules runAllManagedModulesForAllRequests="true" />`
- **Visual Studio 2012 se bloque quand vous tapez un «\' dans un littéral de chaîne dans un fichier Razor.** Pour utiliser le problème, entrez le guillemet fermant de la chaîne littérale tout d’abord.
- **Navigation à &quot;compte/gérer&quot; dans les résultats du modèle Internet dans une erreur d’exécution pour les langues CHS, TRK et CHT.** Pour résoudre le problème de modifier la page pour séparer des  *@User.Identity.Name*  en le plaçant en tant que seul le contenu dans le  *&lt;fort&gt;*  balise.
- **Google et LinkedIn fournisseurs ne sont pas pris en charge dans les Sites Web Azure.** Utiliser les fournisseurs d’authentification autre lors du déploiement sur les Sites Web Azure.
- **À l’aide de UriPathExtensionMapping avec IIS 8 Express/IIS, vous recevez des 404 erreurs introuvable lorsque vous essayez d’utiliser l’extension.** Le Gestionnaire de fichiers statiques interférera avec des demandes API web qui utilisent *UriPathExtensionMappings*. Définissez *runAllManagedModulesForAllRequests = true* dans le fichier web.config pour contourner le problème.
- **Méthode de Controller.Execute n’est plus appelé.** Tous les contrôleurs MVC sont maintenant toujours exécutées de façon asynchrone.
