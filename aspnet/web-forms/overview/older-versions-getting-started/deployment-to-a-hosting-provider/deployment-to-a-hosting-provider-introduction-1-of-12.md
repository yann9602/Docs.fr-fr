---
uid: web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-introduction-1-of-12
title: "Déploiement d’Application Web ASP.NET avec SQL Server Compact à l’aide de Visual Studio : Introduction - 1 de 12 | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels vous montre comment déployer (publier) ASP.NET projet d’application web qui inclut une base de données SQL Server Compact à l’aide de Visual Stu..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/17/2011
ms.topic: article
ms.assetid: a2d7f33b-8c4a-4b48-9fb1-9139cf9b9878
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-introduction-1-of-12
msc.type: authoredcontent
ms.openlocfilehash: 7c03453e64cfc065d9f424702cc5af373e9bf536
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="deploying-an-aspnet-web-application-with-sql-server-compact-using-visual-studio-introduction---1-of-12"></a>Déploiement d’Application Web ASP.NET avec SQL Server Compact à l’aide de Visual Studio : Introduction - 1 de 12
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet de démarrage](http://code.msdn.microsoft.com/Deploying-an-ASPNET-Web-4e31366b)

> Cette série de didacticiels vous montre comment déployer (publier) ASP.NET projet d’application web qui inclut une base de données SQL Server Compact à l’aide de Visual Studio 2012 RC ou Visual Studio Express 2012 RC pour le Web. Vous pouvez également utiliser Visual Studio 2010 si vous installez la mise à jour de publication Web.
> 
> Pour obtenir un didacticiel qui montre les fonctionnalités de déploiement introduites après la version RC de Visual Studio 2012, montre comment déployer des éditions de SQL Server autre que SQL Server Compact et montre comment déployer dans Azure App Service Web Apps, consultez [déploiement de Web ASP.NET à l’aide de Visual Studio](../../deployment/visual-studio-web-deployment/introduction.md).
> 
> Ces didacticiels vous guident dans le processus de déploiement d’abord à IIS sur votre ordinateur de développement local pour le test, puis vers un fournisseur d’hébergement tiers. L’application que vous allez déployer utilise une base de données d’application et une base de données d’appartenance ASP.NET. Vous commencez à l’aide de SQL Server Compact et le déploiement de SQL Server Compact, et les didacticiels suivants vous montrent comment déployer les modifications de la base de données et la migration vers SQL Server.
> 
> Les didacticiels supposent que vous savez comment travailler avec ASP.NET dans Visual Studio. Si vous ne le faites pas, un bon point de départ est un [base ASP.NET Web Forms didacticiel](../tailspin-spyworks/tailspin-spyworks-part-1.md) ou un [didacticiel de base ASP.NET MVC](../../../../mvc/overview/older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md).
> 
> Si vous avez des questions qui ne sont pas directement liées à ce didacticiel, vous pouvez les valider pour le [Forum sur le déploiement ASP.NET](https://forums.asp.net/26.aspx/1?Configuration+and+Deployment).


## <a name="overview"></a>Vue d'ensemble

Ces didacticiels vous guident dans le processus de déploiement d’abord à IIS sur votre ordinateur de développement local pour le test, puis vers un fournisseur d’hébergement tiers. L’application que vous allez déployer utilise une base de données d’application et une base de données d’appartenance ASP.NET. Vous commencez à l’aide de SQL Server Compact et le déploiement de SQL Server Compact, et les didacticiels suivants vous montrent comment déployer les modifications de la base de données et la migration vers SQL Server.

Le nombre de didacticiels – 11 dans toutes les plus une page Dépannage – risque de rendre le processus de déploiement sembler décourageant. En fait, les procédures de base pour déployer un site forment une relativement petite partie de l’ensemble du didacticiel. Toutefois, dans les situations de monde réel, vous devez souvent plus d’informations sur certains aspects supplémentaires légère mais importante du déploiement, par exemple, affecter des autorisations de dossier sur le serveur cible. Nous avons inclus plusieurs de ces techniques supplémentaires dans les didacticiels, avec l’espoir que les didacticiels ne laissez pas les informations qui peuvent vous empêcher de déploiement d’une application réelle avec succès.

Les didacticiels sont conçus pour s’exécuter en séquence, et chaque partie repose sur la partie précédente. Toutefois, vous pouvez ignorer les parties qui ne sont pas applicables à votre situation. (Ignorer les parties peut-être vous amener à ajuster les procédures dans des didacticiels ultérieurs.)

## <a name="intended-audience"></a>Public visé

Les didacticiels visant les développeurs ASP.NET qui travaillent dans les petites organisations ou d’autres environnements où :

- Un processus d’intégration continue (les générations automatisées et déploiement) n’est pas utilisé.
- L’environnement de production est un fournisseur d’hébergement tiers.
- En règle générale, une personne remplit plusieurs rôles (la même personne développe, teste et déploie).

Dans les environnements d’entreprise, il est plus courant de mettre en œuvre des processus d’intégration continue et l’environnement de production est généralement hébergé par les serveurs de la société. En outre, différentes personnes jouent des rôles différents. Pour plus d’informations sur le déploiement de l’entreprise, consultez [déploiement d’Applications Web dans les scénarios d’entreprise](../../deployment/deploying-web-applications-in-enterprise-scenarios/deploying-web-applications-in-enterprise-scenarios.md).

Organisations de toutes tailles peuvent également déployer des applications web vers Azure, et la plupart des procédures indiquées dans ces didacticiels s’appliquent également à Azure App Services Web Apps. Pour une introduction à Azure, consultez [https://azure.microsoft.com](https://azure.microsoft.com).

## <a name="the-hosting-provider-shown-in-the-tutorials"></a>Le fournisseur d’hébergement indiqué dans les didacticiels

Les didacticiels vous guident tout au long du processus de configuration d’un compte avec une société d’hébergement et de déploiement de l’application à ce fournisseur d’hébergement. Une société d’hébergement spécifique a été choisie de sorte que les didacticiels pourraient illustrent l’expérience complète de déploiement sur un site Web actif. Chaque société d’hébergement propose des fonctions différentes, et l’expérience de déploiement sur leurs serveurs varie légèrement ; Toutefois, le processus décrit dans ce didacticiel est généralement utilisé pour l’ensemble du processus.

Le fournisseur d’hébergement pour ce didacticiel, Cytanium.com, est une des nombreuses méthodes qui sont disponibles, et son utilisation dans ce didacticiel ne constitue pas une approbation ou la recommandation.

## <a name="deploying-web-site-projects"></a>Déploiement de projets de Site Web

Université de Contoso est un projet d’application web Visual Studio. La plupart des méthodes de déploiement et des outils présentés dans ce didacticiel ne s’appliquent pas aux [projets de Site Web](https://msdn.microsoft.com/en-us/library/dd547590.aspx). Pour plus d’informations sur la façon de déployer des projets de site web, consultez [ASP.NET Deployment Content Map](https://msdn.microsoft.com/en-us/library/bb386521.aspx#deployment_for_web_site_projects).

## <a name="deploying-aspnet-mvc-projects"></a>Déploiement de projets ASP.NET MVC

Pour ce didacticiel, vous déployez un projet ASP.NET Web Forms, mais tout ce dont vous apprendre à faire est également applicable à ASP.NET MVC. Un projet Visual Studio MVC est simplement une autre forme de projet d’application web. La seule différence est que si vous déployez vers un fournisseur d’hébergement ne prend pas en charge ASP.NET MVC ou votre version cible de celui-ci, vous devez vous assurer que vous avez installé approprié ([MVC 3](http://nuget.org/packages/AspNetMvc/3.0.20105.0) ou [MVC 4](http://nuget.org/packages/aspnetmvc)) Package NuGet dans votre projet.

## <a name="programming-language"></a>Langage de programmation

L’exemple d’application utilise c#, mais les didacticiels ne nécessitent pas de connaissances du langage c#, et les techniques de déploiement indiquées par les didacticiels ne sont pas spécifiques au langage.

## <a name="troubleshooting-during-this-tutorial"></a>Résolution des problèmes au cours de ce didacticiel

Lorsqu’une erreur se produit pendant le déploiement, ou si le site déployé ne fonctionne pas correctement, les messages d’erreur ne fournissent pas toujours une solution. Pour vous aider à certains problèmes courants, un [résolution des problèmes de la page de référence](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12.md) n’est disponible. Si vous obtenez un message d’erreur, ou quelque chose ne fonctionne pas lorsque vous parcourez les didacticiels, veillez à consulter la page de dépannage.

## <a name="comments-welcome"></a>Bienvenue dans les commentaires

Commentaires sur les didacticiels sont les bienvenus, et lorsque le didacticiel est mis à jour tous les efforts seront établie à tenir des corrections de compte ou des suggestions d’amélioration est fournies dans les commentaires de didacticiel.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer, assurez-vous que vous avez Windows 7 ou version ultérieure et qu’un des produits suivants installés sur votre ordinateur :

- [Visual Studio 2010 SP1](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=VS2010SP1Pack)
- [Visual Web Developer Express 2010 SP1](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=VWD2010SP1Pack)
- [Visual Studio 2012 RC ou Visual Studio Express 2012 RC pour le Web](https://go.microsoft.com/fwlink/?LinkId=240162)

Si vous avez Visual Studio 2010 SP1 ou Visual Web Developer Express 2010 SP1, installez également les produits suivants :

- [Azure SDK pour .NET (VS 2010 SP1)](https://go.microsoft.com/fwlink/?LinkID=208120) (y compris la mise à jour Web de publication)
- [Microsoft Visual Studio 2010 SP1 Tools pour SQL Server Compact 4.0](https://www.microsoft.com/web/gallery/install.aspx?appid=SQLCEVSTools)

Un autre logiciel est requis pour effectuer le didacticiel, mais vous n’êtes pas obligé d’avoir qui encore chargé. Ce didacticiel vous guidera à travers les étapes d’installation lorsque vous avez besoin.

## <a name="downloading-the-sample-application"></a>Télécharger l’exemple d’Application

L’application que vous allez déployer est nommée Contoso University et a déjà été créée pour vous. Il s’agit d’une version simplifiée d’un site web university, faiblement selon de l’application Contoso University décrite dans le [didacticiels Entity Framework sur le site ASP.NET](https://asp.net/entity-framework/tutorials).

Lorsque vous disposez des composants requis, téléchargez le [application web de Contoso University](https://code.msdn.microsoft.com/Deploying-an-ASPNET-Web-4e31366b). Le *.zip* fichier contient plusieurs versions du projet et un fichier PDF qui contient tous les didacticiels de 12. Pour les étapes du didacticiel, commencez avec ContosoUniversity-début. Pour voir à quoi ressemble le projet à la fin des didacticiels, ouvrez ContosoUniversity-End. Pour voir à quoi ressemble le projet avant la migration vers SQL Server dans le didacticiel 10, ouvrez ContosoUniversity-AfterTutorial09.

Pour préparer à effectuer les étapes du didacticiel, début de l’enregistrement ContosoUniversity-pour le dossier que vous utilisez pour travailler avec des projets Visual Studio. Par défaut, c’est le dossier suivant :

`C:\Users\<username>\Documents\Visual Studio 2012\Projects`

(Pour les captures d’écran de ce didacticiel, le dossier du projet se trouve dans le répertoire racine sur le `C`: lecteur.)

Démarrez Visual Studio, ouvrez le projet, appuyez sur CTRL-F5 pour l’exécuter.

[![Home_Page](deployment-to-a-hosting-provider-introduction-1-of-12/_static/image2.png)](deployment-to-a-hosting-provider-introduction-1-of-12/_static/image1.png)

Les pages de site Web sont accessibles à partir de la barre de menus et vous permettent d’effectuer les fonctions suivantes :

- Afficher les statistiques de l’étudiant (la page à propos de).
- Afficher, modifier, supprimer et ajouter des étudiants.
- Afficher et modifier des cours.
- Afficher et modifier les formateurs.
- Afficher et modifier des services.

Voici les captures d’écran de quelques pages représentatifs.

[![Students_Page](deployment-to-a-hosting-provider-introduction-1-of-12/_static/image4.png)](deployment-to-a-hosting-provider-introduction-1-of-12/_static/image3.png)

[![Add_Students_Page](deployment-to-a-hosting-provider-introduction-1-of-12/_static/image6.png)](deployment-to-a-hosting-provider-introduction-1-of-12/_static/image5.png)

## <a name="reviewing-application-features-that-affect-deployment"></a>Examen des fonctionnalités qui affectent le déploiement d’applications

Les fonctionnalités suivantes de l’application affectent le déploiement ou que vous avez à faire pour le déployer. Chacune d’elles est expliquée plus en détail dans les didacticiels suivants de la série.

- Université de Contoso utilise une base de données SQL Server Compact pour stocker les données d’application tels que les noms de student et instructor. Lorsque vous déployez pour la production vous devrez exclure les données de test et la base de données contient un mélange de données de test et les données de production. Plus loin dans la série de didacticiels, vous allez migrer à partir de SQL Server Compact à SQL Server.
- L’application utilise le système d’appartenance ASP.NET, qui stocke des informations de compte d’utilisateur dans une base de données SQL Server Compact. L’application définit un utilisateur d’administrateur qui a accès à des informations restreintes. Vous devez déployer la base de données d’appartenance sans comptes de test, mais avec un compte d’administrateur.
- Étant donné que la base de données de l’application et la base de données d’appartenance utilisent SQL Server Compact comme moteur de base de données, vous devez déployer le moteur de base de données pour le fournisseur d’hébergement, ainsi que les bases de données eux-mêmes.
- L’application utilise des fournisseurs d’appartenances universels ASP.NET afin que le système d’appartenance peut stocker ses données dans une base de données SQL Server Compact. L’assembly qui contient les fournisseurs d’appartenances universelle doit être déployé avec l’application.
- L’application utilise Entity Framework 5.0 pour accéder aux données dans la base de données de l’application. L’assembly qui contient l’Entity Framework 5.0 doit être déployé avec l’application.
- L’application utilise une application tierce journalisation des erreurs et utilitaire de création de rapports. Cet utilitaire est fourni dans un assembly qui doit être déployé avec l’application.
- L’utilitaire de journalisation erreur écrit les informations d’erreur dans des fichiers XML dans un dossier. Vous devez vous assurer que le compte ASP.NET sous lequel s’exécute dans le site déployé a l’autorisation d’écriture à ce dossier, et vous devez exclure ce dossier de déploiement. (Dans le cas contraire, données de journal d’erreur à partir de l’environnement de test peuvent être déployées en production et/ou de fichiers de journal des erreurs de production peuvent être supprimés.)
- L’application inclut certains paramètres qui doivent être modifiées dans déployé *Web.config* selon l’environnement de destination (test ou production) et d’autres paramètres qui doivent être modifiés en fonction de la build configuration (Debug ou Release).
- La solution Visual Studio inclut un projet de bibliothèque de classes. Uniquement l’assembly généré par ce projet doit être déployé, pas le projet lui-même.

Dans ce premier didacticiel de la série, vous avez téléchargé l’exemple de projet Visual Studio et passé en revue les fonctionnalités du site qui affectent la façon dont vous déployez l’application. Dans les didacticiels suivants vous préparer pour le déploiement en définissant une partie de ces éléments pour être géré automatiquement. D’autres que vous prenez soin de manuellement.

>[!div class="step-by-step"]
[Next](deployment-to-a-hosting-provider-deploying-sql-server-compact-databases-2-of-12.md)
