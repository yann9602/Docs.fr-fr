---
uid: mvc/mvc5
title: ASP.NET MVC 5 | Documents Microsoft
author: rick-anderson
description: "ASP.NET MVC 5 ASP.NET MVC 5 est une infrastructure pour générer des applications web évolutive basée sur des normes, à l’aide de modèles de conception bien établis et la puissance de AS...."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/20/2014
ms.topic: article
ms.assetid: f79fbf7f-59e5-4279-a832-c1a0294630f4
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/mvc5
msc.type: content
ms.openlocfilehash: e57163469ae4606df0fc17e3e054b7696782a084
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-mvc-5"></a>ASP.NET MVC 5
====================
## <a name="whats-new-in-aspnet-mvc-5"></a>Quelles sont les nouveautés dans ASP.NET MVC 5

### <a name="one-aspnet"></a>Un seul ASP.NET

Les modèles de projet Web MVC s’intégrer sans problème avec la nouvelle expérience One ASP.NET. Vous pouvez personnaliser votre projet MVC et configurer l’authentification à l’aide de l’Assistant Création de projet One ASP.NET. Vous trouverez un didacticiel d’introduction à ASP.NET MVC 5 à [mise en route avec ASP.NET MVC 5](overview/getting-started/introduction/getting-started.md).

Pour plus d’informations sur la mise à niveau des projets MVC 4 à 5 de MVC, consultez [la mise à niveau un ASP.NET MVC 4 et le projet d’API Web ASP.NET MVC 5 et Web API 2](overview/releases/how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2.md).

### <a name="aspnet-identity"></a>ASP.NET Identity

Les modèles de projet MVC ont été mis à jour pour utiliser ASP.NET Identity pour l’authentification et la gestion d’identité. Vous trouverez un didacticiel avec l’authentification Facebook et Google et la nouvelle API d’appartenance à [création d’une application ASP.NET MVC 5 avec Facebook et Google OAuth2 et OpenID Sign-on](overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) et [déployer une application de Secure ASP.NET MVC L’appartenance, OAuth et base de données SQL à un Site Web Windows Azure](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data).

### <a name="bootstrap"></a>programme d’amorçage

Le modèle de projet MVC a été mis à jour pour utiliser [Bootstrap](http://getbootstrap.com/) pour fournir une élégante et réactive apparence que vous pouvez facilement personnaliser. Pour plus d’informations, consultez [Bootstrap dans les modèles de projet web Visual Studio 2013](../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap).

### <a name="authentication-filters"></a>Filtres d’authentification

[Filtres d’authentification](http://www.dotnetcurry.com/showarticle.aspx?ID=957) sont un nouveau type de filtre dans ASP.NET MVC qui s’exécutent avant les filtres d’autorisation dans le pipeline ASP.NET MVC et vous permettent de spécifier l’authentification logique par action, par contrôleur, ou globalement pour tous les contrôleurs. Filtres d’authentification traitent les informations d’identification dans la demande et fournissent une identité correspondante. Filtres d’authentification peuvent également ajouter les stimulations d’authentification en réponse aux demandes non autorisées. Consultez [filtres d’authentification ASP.NET MVC 5](http://www.dotnetcurry.com/showarticle.aspx?ID=957), [filtres d’authentification dans ASP.NET MVC 5](http://theshravan.net/blog/authentication-filters-in-asp-net-mvc-5/) et [enfin la nouvelle ASP.NET MVC 5 filtres d’authentification !](http://hackwebwith.net/finally-the-new-asp-net-mvc-5-authentication-filters/).

### <a name="filter-overrides"></a>Filtrer les remplacements

Vous pouvez maintenant remplacer les filtres à appliquer à une méthode d’action donné ou un contrôleur en spécifiant un [substitue](http://www.davidhayden.me/blog/filter-overrides-in-asp-net-mvc-5). Les filtres de remplacement de spécifier un ensemble de types de filtre ne doit pas être exécutée pour une étendue donnée (action ou contrôleur). Cela vous permet de vous permet de configurer des filtres qui s’appliquent globalement mais puis exclure certains filtres globaux de l’application à des actions spécifiques ou les contrôleurs. Consultez [se substitue à nouveau filtre de fonctionnalité dans ASP.NET MVC 5 et ASP.NET Web API 2](https://weblogs.asp.net/imranbaloch/archive/2013/09/25/new-filter-overrides-in-asp-net-mvc-5-and-asp-net-web-api-2.aspx), [comment utiliser la fonctionnalité de remplacements de filtre ASP.NET MVC 5](http://hackwebwith.net/how-to-use-the-asp-net-mvc-5-filter-overrides-feature/), et [filtre substitue dans ASP.NET MVC 5](http://www.davidhayden.me/blog/filter-overrides-in-asp-net-mvc-5)

### <a name="attribute-routing"></a>Routage d’attributs

ASP.NET MVC prend en charge [attribut routage](https://blogs.msdn.com/b/webdev/archive/2013/10/17/attribute-routing-in-asp-net-mvc-5.aspx), grâce à une contribution par Tim McCall, l’auteur de [http://attributerouting.net](http://attributerouting.net). Avec le routage de l’attribut, vous pouvez spécifier votre itinéraires par vos actions et les contrôleurs d’annotation.

## <a name="new-web-project-experience"></a>Nouvelle expérience de projet Web

Nous avons amélioré l’expérience de création de projets web dans Visual Studio 2013. Dans le **nouveau projet Web ASP.NET** boîte de dialogue vous pouvez sélectionner le type de projet, vous souhaitez configurez n’importe quelle combinaison de technologies (Web Forms, MVC, Web API), configurez les options d’authentification et ajoutez un projet de test unitaire.

![Nouveau projet ASP.NET](mvc5/_static/image1.png)

La nouvelle boîte de dialogue permet de modifier les options d’authentification par défaut pour la plupart des modèles. Par exemple, lorsque vous créez un projet Web Forms ASP.NET vous pouvez sélectionner une des options suivantes :

- Aucune authentification
- Comptes d’utilisateur individuels (l’appartenance d’ASP.NET ou journal fournisseur sociaux dans)
- Comptes d’organisation (Active Directory dans une application internet)
- Authentification Windows (Active Directory dans une application intranet)

![Options d’authentification](mvc5/_static/image2.png)

Pour plus d’informations sur le nouveau processus de création de projets web, consultez [création de projets Web ASP.NET dans Visual Studio 2013](../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md). Pour plus d’informations sur les options d’authentification, consultez [ASP.NET Identity](../identity/overview/index.md).

<a id="scaffold"></a>
### <a name="aspnet-scaffolding"></a>Génération de modèles automatique ASP.NET

Génération de modèles automatique ASP.NET est une infrastructure de génération de code pour les applications Web ASP.NET. Elle rend faciles à ajouter du code réutilisable à votre projet interagit avec un modèle de données.

Dans les versions précédentes de Visual Studio, la structure a été limitée aux projets ASP.NET MVC. Avec Visual Studio 2013, vous pouvez maintenant utiliser la structure d’un projet ASP.NET, y compris les Web Forms. Visual Studio 2013 ne prend pas en charge les pages de génération d’un projet Web Forms, mais vous pouvez toujours utiliser la structure des Web Forms en ajoutant des dépendances MVC au projet. Prise en charge pour la génération de pages pour Web Forms sera ajoutée dans une prochaine mise à jour.

Lorsque vous utilisez la structure, nous Assurez-vous que tous les requises dépendances sont installées dans le projet. Par exemple, si vous démarrez avec un projet Web Forms ASP.NET et puis utilisez la structure pour ajouter un contrôleur d’API Web, les packages NuGet requis et les références sont ajoutés à votre projet automatiquement.

Pour ajouter la structure de MVC à un projet Web Forms, ajoutez un **un nouvel élément structuré** et sélectionnez **MVC 5 dépendances** dans la fenêtre de la boîte de dialogue. Il existe deux options pour la génération de modèles automatique MVC ; Minimale et complète. Si vous sélectionnez Minimal, seuls les packages NuGet et les références pour ASP.NET MVC sont ajoutés à votre projet. Si vous sélectionnez l’option complet, les dépendances minimale sont ajoutés, ainsi que les fichiers de contenu requis pour un projet MVC.

Prise en charge de la génération de modèles automatique async contrôleurs utilise les nouvelles fonctionnalités asynchrones à partir de l’Entity Framework 6.

Pour plus d’informations et des didacticiels, consultez [vue d’ensemble de la génération de modèles automatique ASP.NET](../visual-studio/overview/2013/aspnet-scaffolding-overview.md).

### <a name="getting-help-and-reporting-issues"></a>Obtention d’aide et de signaler des problèmes

- [Problèmes connus et la liste de modifications avec rupture](../visual-studio/overview/2013/release-notes.md#knownissues)
- Obtenir de l’aide et de discuter de ASP.NET MVC 5 dans le [forums](https://forums.asp.net/1146.aspx)
- [Signaler un bogue dans ASP.NET MVC 5](https://github.com/aspnet/AspNetWebStack/issues)
- [Effectuer une demande de fonctionnalité](http://aspnet.uservoice.com/forums/41201-asp-net-mvc)

### <a name="upgrading-from-aspnet-mvc-4"></a>La mise à niveau d’ASP.NET MVC 4

Consultez [comment mettre à niveau un ASP.NET MVC 4 et Web de projet d’API à ASP.NET MVC 5 et Web API 2](overview/releases/how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2.md)
