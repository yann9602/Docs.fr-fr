---
uid: overview
title: "Vue d’ensemble d’ASP.NET | Documents Microsoft"
author: rick-anderson
description: "Introduction à ASP.NET, une infrastructure libre pour la création de sites Web, les applications web et les API web."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/12/2010
ms.topic: article
ms.assetid: 3a309468-f1ca-4e51-b9c3-536af79d7a8b
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: 
msc.type: content
ms.openlocfilehash: 3d4c34a35e2e34ed78f481c759eda3718edb4da6
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="aspnet-overview"></a>Vue d’ensemble d’ASP.NET

ASP.NET est une infrastructure web gratuite pour la création de sites Web attrayants et applications web à l’aide de HTML, CSS et JavaScript. Vous pouvez également créer des API Web et d’utiliser des technologies en temps réel comme Web Sockets.

[ASP.NET Core](https://docs.microsoft.com/aspnet/core/) est une alternative à ASP.NET.  Consultez le [des conseils sur le choix entre ASP.NET et ASP.NET Core](https://docs.microsoft.com/aspnet/core/choose-aspnet-framework).

## <a name="get-started"></a>Prise en main

[Télécharger Visual Studio 2015](https://go.microsoft.com/fwlink/?LinkId=826064), un libre IDE pour ASP.NET sur Windows.

## <a name="websites-and-web-applications"></a>Sites et applications web

 ASP.NET propose trois infrastructures pour la création d’applications web : les Pages Web ASP.NET Web Forms et ASP.NET MVC. Toutes les trois infrastructures sont stables et mature, et vous pouvez créer des applications web excellent avec un d'entre eux. Quel que soit le framework que vous choisissez, vous obtenez tous les avantages et les fonctionnalités d’ASP.NET partout.

Chaque infrastructure cible d’un style de développement différents. Votre choix dépend d’une combinaison de vos ressources de programmation (base de connaissances, compétences et expérience de développement), le type d’application que vous créez et vous êtes familiarisé avec l’approche du développement.

Voici une vue d’ensemble de chacune des structures et des idées pour savoir comment choisir entre eux. Si vous préférez une vidéo de présentation, consultez [qui effectue des sites Web avec ASP.NET](https://channel9.msdn.com/Blogs/ASP-NET-Site-Videos/Making-Websites-with-ASPNET) et [Nouveautés des outils Web ?](https://channel9.msdn.com/Blogs/ASP-NET-Site-Videos/what-is-web-tools)

|   | Si vous avez aucune expérience | Style de développement | Expertise | 
|-----------|----------------------|-----------------------------------------------------|----------------|
| Web Forms | Win Forms, WPF, .NET | Développement rapide à l’aide d’une riche bibliothèque de contrôles qui encapsulent le balisage HTML | RAD de niveau intermédiaire, Avancé |
| MVC       | Ruby on Rails, .NET  | Contrôle total sur le balisage HTML, code et de balise séparé et facile d’écrire des tests. Le meilleur choix pour les applications mobiles et de page unique (SPA). | Niveau intermédiaire, Avancé |
| Pages web  | Classic ASP, PHP     | Balisage HTML et votre code ensemble dans le même fichier | Nouveau, le niveau intermédiaire |

### <a name="web-forms"></a>Web Forms

Avec ASP.NET Web Forms, vous pouvez créer des sites Web dynamiques selon un modèle familier de glisser-déplacer, pilotée par événements. Une aire de conception et des centaines de contrôles et composants vous permettent de créer rapidement des sites sophistiqués, puissants piloté par l’interface utilisateur avec accès aux données. 

[En savoir plus sur les Web Forms](web-forms/index.md)

### <a name="mvc"></a>MVC

ASP.NET MVC offre un moyen puissant, basé sur des modèles pour générer des sites Web qui permet une séparation claire des préoccupations et qui vous donne un contrôle total sur le balisage pour un développement agile agréable. ASP.NET MVC inclut de nombreuses fonctionnalités qui permettent un développement TDD rapide et convivial rapide pour la création d’applications sophistiquées qui utilisent les normes web les plus récentes. 

[En savoir plus sur MVC](mvc/index.md)

### <a name="aspnet-web-pages"></a>Pages web ASP.NET

Les Pages Web ASP.NET et la syntaxe Razor fournissent un moyen rapide et abordable léger pour combiner du code serveur avec du code HTML à créer du contenu web dynamique. Se connecter aux bases de données, ajouter de la vidéo, créer un lien vers les sites de réseaux sociaux et incluent la plupart des fonctionnalités supplémentaires qui vous aident à créer des sites attrayants conformes aux dernières normes web.

[En savoir plus sur les Pages Web](web-pages/index.md)

### <a name="notes-about-web-forms-mvc-and-web-pages"></a>Remarques sur les Pages Web, Web Forms et MVC

Toutes les infrastructures d’ASP.NET trois sont basés sur le .NET Framework et partagent des fonctionnalités essentielles de .NET et d’ASP.NET. Par exemple, toutes les trois infrastructures offrent un modèle de sécurité de connexion basé sur l’appartenance et les trois partagent les mêmes installations pour la gestion des demandes, la gestion des sessions, etc. qui font partie des principales fonctionnalités d’ASP.NET.

En outre, les infrastructures de trois ne sont pas entièrement indépendants, et en choisissant une n’exclut pas une autre. Étant donné que les infrastructures peuvent coexister dans la même application web, il n’est pas rare de voir les différents composants d’applications écrites à l’aide de différentes infrastructures. Par exemple, aux clients des parties d’une application peuvent être développées dans MVC pour optimiser le balisage, tandis que l’accès aux données et les parties d’administration sont développées dans les formulaires Web pour tirer parti des contrôles de données et l’accès aux données simple.

## <a name="web-apis"></a>API web

API Web ASP.NET est une infrastructure qui permet de facilement créer des services HTTP qui atteignent un large éventail de clients, y compris les navigateurs et périphériques mobiles. L'API Web ASP.NET est une plate-forme idéale pour générer des applications RESTful sur le .NET Framework.

[En savoir plus sur l’API Web](web-api/index.md)

<!-- Put first under Web API TOC:  Watch video (9 minutes) https://channel9.msdn.com/Blogs/ASP-NET-Site-Videos/services-and-aspnet -->

## <a name="real-time-technologies"></a>Technologies en temps réel

ASP.NET SignalR est une nouvelle bibliothèque pour les développeurs ASP.NET qui facilite le développement des fonctionnalités web en temps réel. SignalR permet la communication bidirectionnelle entre le serveur et client. Serveurs peuvent pousser le contenu aux clients connectés instantanément dès que possible. Prend en charge les WebSockets SignalR et revient à d’autres techniques compatibles pour les navigateurs plus anciens. SignalR inclut les API pour la gestion des connexions (par exemple, vous connecter et événements de déconnexion), regroupement de connexions et autorisation.

[En savoir plus sur SignalR](signalr/index.md)

<!-- Put first under SignalR TOC:  Watch video (6 minutes) https://channel9.msdn.com/Blogs/ASP-NET-Site-Videos/signalr-and-the-real-time-web -->

## <a name="mobile-apps-and-sites"></a>Sites et applications mobiles 

ASP.NET peut mettre des applications mobiles natives avec un principal de l’API Web, ainsi que des sites web mobiles à l’aide d’infrastructures de conception réactive comme amorçage de Twitter. Si vous générez une application mobile native, il est facile de créer une API Web JSON pour le handle de l’accès aux données, l’authentification et des notifications push pour votre application. Si vous créez un site pour mobiles réactif, vous pouvez utiliser une infrastructure CSS ou un système de grille ouvrir vous préférez, ou sélectionnez un système puissant mobile comme jQuery Mobile ou Sencha et des applications mobiles avec PhoneGap.

[En savoir plus sur le développement de site et d’application mobile](mobile/index.md)

<!-- Put first under mobile TOC:  Watch video (11 minutes) https://channel9.msdn.com/Blogs/ASP-NET-Site-Videos/aspnet-and-mobile -->

## <a name="single-page-applications"></a>Applications à page unique 

Application à Page unique de ASP.NET (SPA) vous permet de générer des applications qui incluent des interactions côté client importantes à l’aide de HTML 5, 3 de CSS et JavaScript. Visual Studio comprend un modèle pour la création d’applications à page unique à l’aide de knockout.js et API Web ASP.NET. Outre le modèle SPA intégré, créés par la Communauté SPA modèles sont également disponibles en téléchargement.

[En savoir plus sur le développement d’applications de page unique](single-page-application/index.md)

## <a name="webhooks"></a>WebHooks

WebHooks est un modèle HTTP léger en fournissant un modèle simple pub/sub pour associer ensemble les services API Web et SaaS. Lorsqu’un événement se produit dans un service, une notification est envoyée sous la forme d’une requête HTTP POST aux abonnés inscrits. La demande de publication contient des informations sur l’événement qui rend possible pour le récepteur d’agir en conséquence.

WebHooks sont exposées par un grand nombre de services, y compris l’échange, GitHub, Instagram, MailChimp, PayPal, marge, Trello et bien plus encore. Par exemple, un WebHook peut indiquer qu’un fichier a été modifié dans l’échange, une modification du code a été validée dans GitHub, ou un paiement a été initié dans PayPal ou une carte a été créée dans Trello.

[En savoir plus sur le WebHooks](webhooks/index.md)





<!--
Create Deployment TOC based on https://www.asp.net/aspnet/overview/deployment
Copy deployment content map to MVC, WebForms, Web Pages, Web API sections.
Copy Web Deployment in Enterprise from WebForms to MVC
Move under ASP.NET Best practices
    What not to do in ASP.NET, and what to do instead https://review.docs.microsoft.cus/aspnet/aspnet/overview/web-development-best-practices/what-not-to-do-in-aspnet-and-what-to-do-instead
    Async and await https://channel9.msdn.com/Blogs/ASP-NET-Site-Videos/async-and-await
    Building Real World Cloud Apps with Azure https://review.docs.microsoft.com/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction
    Hands on Lab: Maintainable Azure Websites: Managing Change and Scale https://review.docs.microsoft.com/aspnet/aspnet/overview/developing-apps-with-windows-azure/maintainable-azure-websites-managing-change-and-scale

-->
