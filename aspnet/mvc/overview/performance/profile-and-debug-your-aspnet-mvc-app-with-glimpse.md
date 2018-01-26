---
uid: mvc/overview/performance/profile-and-debug-your-aspnet-mvc-app-with-glimpse
title: "Profiler et déboguer votre application ASP.NET MVC avec Glimpse | Documents Microsoft"
author: Rick-Anderson
description: "Aperçu est plein essor et augmente la famille de packages de NuGet open source qui fournit des performances détaillés, débogage et des informations de diagnostic pour ASP.NET un..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/26/2015
ms.topic: article
ms.assetid: c205805f-efdd-4fa7-9616-f26eab180611
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/performance/profile-and-debug-your-aspnet-mvc-app-with-glimpse
msc.type: authoredcontent
ms.openlocfilehash: 9cfdced21251b482ca527dda9c3a698de77cc8ca
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="profile-and-debug-your-aspnet-mvc-app-with-glimpse"></a>Profiler et déboguer votre application ASP.NET MVC avec aperçu
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> Aperçu est plein essor et augmente la famille de packages de NuGet open source qui fournit des performances détaillés, débogage et des informations de diagnostic pour les applications ASP.NET. Il est très facile à installer, léger et ultra rapide et affiche les mesures de performances clés au bas de chaque page. Il vous permet d’approfondir votre application lorsque vous avez besoin savoir ce qui se passe au niveau du serveur. Aperçu fournit des informations précieuses bien que nous vous recommandons de que vous l’utilisez dans votre cycle de développement, y compris de votre environnement de test Azure. Alors que [Fiddler](http://www.telerik.com/fiddler) et [outils de développement F-12](https://msdn.microsoft.com/library/ie/gg589512(v=vs.85).aspx) fournissent un côté client vue Aperçu fournit une vue détaillée du serveur. Ce didacticiel se concentrera sur l’utilisation de l’aperçu ASP.NET MVC et les packages EF, mais de nombreux autres packages sont disponibles. Lorsque cela est possible de lier sera à approprié [visualiser docs](http://getglimpse.com/Docs/) qui vous aider à gérer. Aperçu est un projet open source, vous pouvez trop contribuer au code source et les documents.


- [L’installation d’aperçu](#ig)
- [Activer l’aperçu pour localhost](#eg)
- [L’onglet de la chronologie](#Time)
- [Liaison de données](#mb)
- [Routes](#route)
- [À l’aide de Glimpse dans Azure](#da)
- [Ressources supplémentaires pour MSBuild](#addRes)

<a id="ig"></a>
## <a name="installing-glimpse"></a>L’installation d’aperçu

Vous pouvez installer aperçu à partir de la console Gestionnaire de package NuGet ou de la **gérer les Packages NuGet** console. Pour cette démonstration, vous allez installer les packages Mvc5 et EF6 :

![installer l’aperçu de NuGet Dlg](profile-and-debug-your-aspnet-mvc-app-with-glimpse/_static/image1.png)

Recherchez *Glimpse.EF*

![Glimpse.EF de NuGet installation dlg](profile-and-debug-your-aspnet-mvc-app-with-glimpse/_static/image2.png)

En sélectionnant **les packages installés**, vous pouvez voir les modules dépendants aperçu installés :

![Dans les packages de Glimpse installés à partir de DLg](profile-and-debug-your-aspnet-mvc-app-with-glimpse/_static/image3.png)

Les commandes suivantes installent les modules aperçu MVC5 et EF6 à partir de la console du Gestionnaire de package :

[!code-console[Main](profile-and-debug-your-aspnet-mvc-app-with-glimpse/samples/sample1.cmd)]

<a id="eg"></a>
## <a name="enable-glimpse-for-localhost"></a>Activer l’aperçu pour localhost

Accédez à http://localhost :&lt;# port&gt;/glimpse.axd et cliquez sur le **activer l’aperçu sur** bouton.

![Page d’aperçu axd](profile-and-debug-your-aspnet-mvc-app-with-glimpse/_static/image4.png)

Si vous avez affiché votre barre des Favoris, faites glisser et déposez les boutons d’aperçu et les ajouter comme bookmarklets :

![Internet Explorer avec Glimpse boookmarklets](profile-and-debug-your-aspnet-mvc-app-with-glimpse/_static/image5.png)

Vous pouvez désormais accéder votre application et le **têtes d’affichage** (HUD) est indiqué en bas de la page.

![Page Gestionnaire de contacts avec HUD](profile-and-debug-your-aspnet-mvc-app-with-glimpse/_static/image6.png)

Le [page d’aperçu HUD](http://getglimpse.com/Docs/Heads-up-Display) toutes les informations de minutage indiquées ci-dessus. L’affiche de données le HUD performances discrète peut vous avertir d’un problème immédiatement - avant d’arriver au cycle de test. En cliquant sur le &quot;g&quot; dans l’angle inférieur droit affiche le volet d’aperçu :

![Volet d’aperçu](profile-and-debug-your-aspnet-mvc-app-with-glimpse/_static/image7.png)

Dans l’image ci-dessus, le [onglet exécution](http://getglimpse.com/Docs/Execution-Tab) est sélectionnée, qui affiche les détails de minutage des actions et des filtres dans le pipeline. Vous pouvez voir mon [horloge pour arrêter la surveillance filtre](http://www.nuget.org/packages/StopWatch/) commencent à l’étape 6 du pipeline. Alors que mon minuteur léger peut fournir utile profil/synchronisation des données, il n’arrive pas tout le temps passé dans l’autorisation et de rendu de l’affichage. Vous pouvez lire sur mon minuterie à [de profil et l’heure de votre application ASP.NET MVC à Azure](https://blogs.msdn.com/b/webdev/archive/2014/07/29/profile-and-time-your-asp-net-mvc-app-all-the-way-to-azure.aspx). Le [onglets](http://getglimpse.com/Docs/Tabs) page fournit des liens vers des informations détaillées sur chaque onglet.

<a id="Time"></a>
## <a name="the-timeline-tab"></a>L’onglet de la chronologie

J’ai modifié de Tom Dykstra en suspens [didacticiel de 6/MVC 5 EF](../getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md) modifier au contrôleur instructeurs avec le code suivant :

[!code-csharp[Main](profile-and-debug-your-aspnet-mvc-app-with-glimpse/samples/sample2.cs?highlight=1,20-31)]

Le code ci-dessus me permet de passer dans la chaîne de requête (`eager`) contrôle eager ou explicite le chargement de données. Dans l’image ci-dessous, le chargement explicite est utilisé et la page de synchronisation affiche chaque inscription chargée dans le `Index` méthode d’action :

![chargement explicite](profile-and-debug-your-aspnet-mvc-app-with-glimpse/_static/image8.png)

Dans le code suivant, eager est spécifié, et chaque inscription est atteinte après la `Index` vue est appelée :

![Eager est spécifié.](profile-and-debug-your-aspnet-mvc-app-with-glimpse/_static/image9.png)

Vous pouvez pointer sur un segment de temps pour obtenir des informations de temporisation détaillées :

![pointage pour voir de temporisation détaillées](profile-and-debug-your-aspnet-mvc-app-with-glimpse/_static/image10.png)

<a id="mb"></a>
## <a name="model-binding"></a>Liaison de modèle

Le [onglet de liaison de modèle](http://getglimpse.com/Docs/Model-Binding-Tab) fournit une mine d’informations pour vous aider à comprendre comment vos variables de formulaire sont liés et la raison pour laquelle certaines ne sont pas liés comme l’attend. L’image ci-dessous montre les **?** icône, vous pouvez cliquer sur pour afficher la page d’aide Aperçu pour cette fonctionnalité.

![visualiser la vue de modèle de liaison](profile-and-debug-your-aspnet-mvc-app-with-glimpse/_static/image11.png)

<a id="route"></a>
## <a name="routes"></a>Routes

 L’onglet Aperçu itinéraires sera peuvent vous aider à déboguer et comprendre le routage. Dans l’image ci-dessous, l’itinéraire de produit est sélectionné (et elle est affichée en vert, une convention d’aperçu). ![nom du produit sélectionné](profile-and-debug-your-aspnet-mvc-app-with-glimpse/_static/image12.png) jetons contraintes, les zones et les données d’itinéraire sont également affichés. Consultez [Glimpse itinéraires](http://getglimpse.com/Docs/Routes-Tab) et [attribut routage dans ASP.NET MVC 5](https://blogs.msdn.com/b/webdev/archive/2013/10/17/attribute-routing-in-asp-net-mvc-5.aspx) pour plus d’informations. 

<a id="da"></a>
## <a name="using-glimpse-on-azure"></a>À l’aide de Glimpse dans Azure

La stratégie de sécurité par défaut aperçu autorise uniquement les données d’aperçu s’affiche à partir de l’hôte local. Vous pouvez modifier cette stratégie de sécurité afin de pouvoir consulter ces données sur un serveur distant (par exemple, une application web sur Azure). Pour les environnements de test sur Azure, ajoutez la marque en surbrillance jusqu’au bas de la *web.confg* fichier pour activer l’aperçu :

[!code-xml[Main](profile-and-debug-your-aspnet-mvc-app-with-glimpse/samples/sample3.xml?highlight=2-6)]

Cette modification uniquement, tout utilisateur peut consulter vos données d’aperçu sur un site distant. Envisagez d’ajouter le balisage ci-dessus pour un profil de publication afin qu’il a déployé uniquement un appliqué lorsque vous utilisez ce profil de publication (par exemple, votre proifle test Azure.) Pour restreindre les données d’aperçu, nous allons ajouter le `canViewGlimpseData` rôle et autorise uniquement les utilisateurs de ce rôle pour afficher les données d’aperçu.

Supprimez les commentaires de la *GlimpseSecurityPolicy.cs* de fichiers et de modifier le [IsInRole](https://msdn.microsoft.com/library/system.security.principal.iprincipal.isinrole(v=vs.110).aspx) appeler à partir de `Administrator` à la `canViewGlimpseData` rôle :

[!code-csharp[Main](profile-and-debug-your-aspnet-mvc-app-with-glimpse/samples/sample4.cs?highlight=6)]

> [!WARNING]
> Sécurité - les données enrichies fournies par l’aperçu peut exposer la sécurité de votre application. Microsoft n’a pas effectué un audit de sécurité de Glimpse pour une utilisation sur les applications de l’environnement de production.


Pour plus d’informations sur l’ajout de rôles, consultez Mes [déployer une application web de Secure ASP.NET MVC 5 avec l’appartenance, OAuth et base de données SQL Azure](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/) didacticiel.

<a id="addRes"></a>
## <a name="additional-resources"></a>Ressources supplémentaires

- [Déployer une application sécurisée ASP.NET MVC 5 avec l’appartenance, OAuth et base de données SQL Azure](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/)
- [Visualiser la Configuration](http://getglimpse.com/Docs/Configuration) -page de documentation sur la configuration des onglets, stratégie d’exécution, la journalisation et bien plus encore.
