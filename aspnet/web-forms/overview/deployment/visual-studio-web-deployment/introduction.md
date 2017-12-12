---
uid: web-forms/overview/deployment/visual-studio-web-deployment/introduction
title: "Déploiement de Web ASP.NET à l’aide de Visual Studio : présentation | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, à l’utilisation de V..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/15/2013
ms.topic: article
ms.assetid: 24ad086d-865e-433c-9ac9-05f1a553da16
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/introduction
msc.type: authoredcontent
ms.openlocfilehash: e14f3bed001592c85bdbba868f51141bc52a9470
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-web-deployment-using-visual-studio-introduction"></a>Déploiement de Web ASP.NET à l’aide de Visual Studio : Introduction
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet de démarrage](http://go.microsoft.com/fwlink/p/?LinkId=282627)

> Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, à l’aide de Visual Studio 2012 avec Windows Azure SDK pour .NET. La plupart des procédures est similaire pour Visual Studio 2013.
> 
> Vous développez une application web pour le rendre disponible aux personnes sur Internet. Mais les didacticiels de programmation web arrête généralement dès que vous leur avez montré comment obtenir quelque chose que vous travaillez sur votre ordinateur de développement. Cette série de didacticiels commence dans lequel les autres omettre : vous avez créé une application web, testé, et il est prêt à fonctionner. Étapes suivantes Ces didacticiels vous montrent comment déployer d’abord à IIS sur votre ordinateur de développement local pour le test, puis vers Azure ou d’un fournisseur d’hébergement tiers pour intermédiaire et de production. L’exemple d’application que vous allez déployer est un projet d’application web qui utilise Entity Framework, SQL Server et le système d’appartenance ASP.NET. L’exemple d’application utilise ASP.NET Web Forms, mais les procédures indiquées s’appliquent également à ASP.NET MVC et API Web.
> 
> Ces didacticiels supposent que vous savez comment travailler avec ASP.NET dans Visual Studio. Si vous ne le faites pas, un bon point de départ est un [base ASP.NET Web Forms didacticiel](../../older-versions-getting-started/tailspin-spyworks/tailspin-spyworks-part-1.md) ou un [didacticiel de base ASP.NET MVC](../../../../mvc/overview/older-versions/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4.md).
> 
> Si vous avez des questions qui ne sont pas directement liées à ce didacticiel, vous pouvez les valider pour le [Forum sur le déploiement ASP.NET](https://forums.asp.net/26.aspx/1?Configuration+and+Deployment) ou [StackOverflow](http://stackoverflow.com).
> 
> Ce contenu est également disponible sous un livre électronique gratuit dans [la galerie de livres TechNet](https://social.technet.microsoft.com/wiki/contents/articles/11608.e-book-gallery-for-microsoft-technologies.aspx#ASPNETWebDeploymentusingVisualStudio).


## <a name="overview"></a>Vue d'ensemble

Ces didacticiels vous guident dans le processus de déploiement d’une application web ASP.NET qui inclut les bases de données SQL Server. Vous allez tout d’abord déployer à IIS sur votre ordinateur de développement local pour le test, puis pour des applications Web dans Azure App Service et de la base de données SQL Azure pour intermédiaire et de production. Vous allez apprendre à déployer à l’aide de Visual Studio publication en un clic, et vous allez apprendre à déployer à l’aide de la ligne de commande.

Le nombre de didacticiels peut rendre le processus de déploiement sembler décourageant. En fait, les procédures de base sont simples. Toutefois, dans les situations de monde réel, vous devez souvent d’effectuer des tâches de déploiement supplémentaires, par exemple, affecter des autorisations de dossier sur le serveur cible. Nous avons notamment certaines de ces tâches supplémentaires, dans l’espoir que les didacticiels ne laissez pas les informations qui peuvent vous empêcher de déploiement d’une application réelle avec succès.

Les didacticiels sont conçus pour s’exécuter en séquence, et chaque partie repose sur la partie précédente. Vous pouvez ignorer les parties qui ne sont pas applicables à votre situation, puis vous devrez peut-être ajuster les procédures dans des didacticiels ultérieurs.

## <a name="intended-audience"></a>Public visé

Les didacticiels visant les développeurs ASP.NET qui travaillent dans des environnements où :

- L’environnement de production est Azure App Service Web Apps ou un fournisseur d’hébergement tiers.
- Déploiement n’est pas limité à un processus d’intégration continue, mais peut être effectuée directement à partir de Visual Studio.

Déploiement à partir de [contrôle de code source](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control.md) à l’aide un [livraison continue](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery.md) processus n’est pas couverte dans ces didacticiels à l’exception d’un didacticiel qui montre comment déployer à partir de la ligne de commande. Pour plus d’informations sur la diffusion en continu, consultez les ressources suivantes :

- [Intégration continue et livraison continue (génération d’applications Cloud du monde réel avec Windows Azure)](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery.md)
- [Déployer une application web dans Azure App Service](https://azure.microsoft.com/en-us/documentation/articles/web-sites-deploy/)
- [Déployer des Applications Web dans les scénarios d’entreprise](../deploying-web-applications-in-enterprise-scenarios/deploying-web-applications-in-enterprise-scenarios.md) (un ancien ensemble de didacticiels écrits pour Visual Studio 2010, qui dispose toujours des informations utiles pour les environnements d’entreprise.)

## <a name="using-a-third-party-hosting-provider"></a>À l’aide d’un fournisseur d’hébergement tiers

Les didacticiels vous guident tout au long du processus de configuration d’un compte Azure et de déploiement de l’application pour des applications Web dans Azure App Service pour intermédiaire et de production. Toutefois, vous pouvez utiliser les mêmes procédures de base pour le déploiement sur un fournisseur d’hébergement tiers de votre choix. Les didacticiels passez en revue le processus uniques dans Azure, ils qui expliquent et signaler les différences que vous pouvez vous attendre à un fournisseur d’hébergement tiers.

## <a name="deploying-web-app-projects"></a>Déploiement de projets d’application web

L’exemple d’application que vous téléchargez et déployez pour ces didacticiels est un projet d’application web Visual Studio. Toutefois, si vous installez la dernière version [Web publier des mises à jour pour Visual Studio](https://msdn.microsoft.com/tr-tr/library/jj161045.aspx), vous pouvez utiliser les mêmes méthodes de déploiement et les outils pour les projets d’application web.

## <a name="deploying-aspnet-mvc-projects"></a>Déploiement de projets ASP.NET MVC

L’exemple d’application est un projet ASP.NET Web Forms, mais tout ce dont vous apprendre à faire est également applicable à ASP.NET MVC. Un projet Visual Studio MVC est simplement une autre forme de projet d’application web. La seule différence est que si vous déployez vers un fournisseur d’hébergement ne prend pas en charge ASP.NET MVC ou votre version cible de celui-ci, vous devez vous assurer que vous avez installé approprié ([MVC 3](http://nuget.org/packages/AspNetMvc/3.0.20105.0), [MVC 4](http://www.nuget.org/packages/Microsoft.AspNet.Mvc/4.0.30506.0) ou [MVC 5](http://www.nuget.org/packages/Microsoft.AspNet.Mvc)) package NuGet dans votre projet.

## <a name="programming-language"></a>Langage de programmation

L’exemple d’application utilise c#, mais les didacticiels ne nécessitent pas de connaissances du langage c#, et les techniques de déploiement indiquées par les didacticiels ne sont pas spécifiques au langage.

## <a name="database-deployment-methods"></a>Méthodes de déploiement de base de données

Il existe trois méthodes que vous pouvez déployer une base de données SQL Server, ainsi que le déploiement web dans Visual Studio :

- Migrations de Entity Framework Code First
- Le fournisseur Web Deploy dbDacFx
- Le fournisseur de déploiement Web dbFullSql

Dans ce didacticiel, vous allez utiliser les deux premiers de ces méthodes. Le fournisseur de déploiement Web dbFullSql est une méthode héritée qui n’est plus recommandée à l’exception de certains scénarios spécifiques, telles que la migration à partir de SQL Server Compact à SQL Server.

Les méthodes indiquées dans ce didacticiel sont des bases de données SQL Server, pas SQL Server Compact. Pour plus d’informations sur la façon de déployer une base de données SQL Server Compact, consultez [Visual Studio Web déploiement avec SQL Server Compact](../../older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-introduction-1-of-12.md).

Les méthodes indiquées dans ce didacticiel nécessitent que vous utilisez le déploiement Web méthode de publication. Si vous préférez publier d’une autre méthode, telle que FTP, système de fichiers ou extensions serveur FrontPage, consultez [déploiement d’une base de données séparément de déploiement d’applications web](https://go.microsoft.com/fwlink/p/?LinkId=282413#databaseseparate) dans le plan de contenu de déploiement Web pour Visual Studio et ASP.NET.

### <a name="entity-framework-code-first-migrations"></a>Migrations de Entity Framework Code First

Dans Entity Framework version 4.3, Microsoft a introduit des Migrations Code First. Migrations Code First automatise le processus d’apporter des modifications incrémentielles à un modèle de données et propager ces modifications à la base de données. Dans les versions antérieures de Code First, vous permettent généralement de l’Entity Framework supprimer et recréer la base de données chaque fois que vous modifiez le modèle de données. Cela n’est pas un problème dans le développement, car les données de test sont recréées facilement, sans en production vous généralement mettre à jour le schéma de base de données sans supprimer la base de données. Permet à la fonctionnalité Migrations Code First mettre à jour la base de données sans devoir supprimer et recréer. Vous pouvez laisser le premier Code automatiquement décider comment apporter les modifications de schéma requis, ou vous pouvez écrire du code qui personnalise les modifications. Pour une introduction aux Migrations Code First, consultez [Migrations Code First](https://msdn.microsoft.com/library/hh770484.aspx).

Lorsque vous déployez un projet web, Visual Studio peut automatiser le processus de déploiement d’une base de données qui est géré par les Migrations Code First. Lorsque vous créez le profil de publication, vous sélectionnez une case à cocher nommée exécuter fonctionnalité Migrations Code First (s’exécute sur le démarrage de l’application). Ce paramètre oblige le processus de déploiement configurer automatiquement le fichier Web.config application sur le serveur de destination afin qu’utilise le premier Code la `MigrateDatabaseToLatestVersion` classe de l’initialiseur.

Visual Studio ne fait rien avec la base de données pendant le processus de déploiement. Lorsque l’application déployée accède à la base de données pour la première fois après le déploiement, Code First crée automatiquement la base de données ou met à jour le schéma de base de données vers la dernière version. Si l’application implémente une méthode de migration initiale, la méthode s’exécute après la création de la base de données ou le schéma est mis à jour.

Dans ce didacticiel, vous utiliserez les Migrations Code First pour déployer la base de données de l’application.

### <a name="the-dbdacfx-web-deploy-provider"></a>Le fournisseur Web Deploy dbDacFx

Pour une base de données SQL Server qui n’est pas géré par Entity Framework Code First, vous pouvez sélectionner une case à cocher nommée de base de données de mise à jour lorsque vous configurez le profil de publication. Pendant le déploiement initial, le fournisseur dbDacFx crée les tables et autres objets de base de données dans la base de données de destination pour correspondre à la base de données source. Sur les déploiements suivants, le fournisseur détermine quelle est la différence entre les bases de données source et de destination, et il met à jour le schéma de la base de données de destination pour correspondre à la base de données source. Par défaut, le fournisseur n’apportez les modifications qui provoquent la perte de données, telles que lorsqu’une table ou une colonne est supprimée.

Cette méthode ne permettent pas d’automatiser le déploiement de données dans les tables de base de données, mais vous pouvez créer des scripts pour ce faire et configurer Visual Studio pour les exécuter pendant le déploiement. Une autre raison pour exécuter des scripts lors du déploiement est pour apporter des modifications de schéma ne peut pas être effectuées automatiquement, car ils entraîneraient une perte de données.

Dans ce didacticiel, vous utiliserez le fournisseur dbDacFx pour déployer la base de données d’appartenance ASP.NET.

## <a name="troubleshooting-during-this-tutorial"></a>Résolution des problèmes au cours de ce didacticiel

Lorsqu’une erreur se produit pendant le déploiement, ou si le site déployé ne fonctionne pas correctement, les messages d’erreur ne fournissent pas toujours une solution évidente. Pour vous aider à certains problèmes courants, un [résolution des problèmes de la page de référence](troubleshooting.md) n’est disponible. Si vous obtenez un message d’erreur, ou quelque chose ne fonctionne pas lorsque vous parcourez les didacticiels, veillez à consulter la page de dépannage.

## <a name="comments-welcome"></a>Bienvenue dans les commentaires

Commentaires sur les didacticiels sont les bienvenus, et lorsque le didacticiel est mis à jour tous les efforts seront établie à tenir des corrections de compte ou des suggestions d’amélioration est fournies dans les commentaires de didacticiel.

<a id="prerequisites"></a>

## <a name="prerequisites"></a>Conditions préalables

Ce didacticiel a été écrite pour les produits suivants :

- Windows 8 ou Windows 7.
- Visual Studio 2012 ou Visual Studio 2012 Express pour le Web avec [la dernière mise à jour](https://go.microsoft.com/fwlink/?LinkId=272486).
- [Azure SDK pour Visual Studio 2012](https://go.microsoft.com/fwlink/?LinkId=254364)

Vous pouvez suivre le didacticiel à l’aide de Visual Studio 2010 SP1 ou Visual Studio 2013, mais certaines captures d’écran sera différentes, et certaines fonctionnalités seront différentes.

Si vous utilisez Visual Studio 2013, installez [Azure SDK pour Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkID=324322).

Si vous utilisez Visual Studio 2010 SP1, installez les logiciels suivants :

- [Azure SDK pour Visual Studio 2010](https://go.microsoft.com/fwlink/?LinkID=254269)
- [SQL Server Express LocalDB](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=SQLLocalDBOnly_11_0)
- [SQL Server Data Tools](https://msdn.microsoft.com/en-us/library/hh500335.aspx).

Selon le nombre des dépendances du Kit de développement logiciel vous disposez déjà sur votre ordinateur, l’installation du SDK Azure peut prendre beaucoup de temps, de plusieurs minutes au moins une demi-heure. Vous devez le SDK Azure même si vous envisagez de les publier sur un fournisseur d’hébergement tiers au lieu de vers Azure, fonctionnalités de publication, car le kit SDK inclut les dernières mises à jour sur le web de Visual Studio.

> [!NOTE]
> Ce didacticiel a été écrit avec version 1.8.1 du SDK Azure. Depuis des versions plus récentes avec des fonctionnalités supplémentaires ont été publiées. Les didacticiels ont été mis à jour pour indiquer ces fonctionnalités et un lien vers des ressources qui ont plus d’informations sur les.


Les instructions et les captures d’écran sont basées sur Windows 8, mais les didacticiels expliquent les différences pour Windows 7.

Un autre logiciel est requis pour effectuer le didacticiel, mais vous n’êtes pas obligé d’avoir installé encore. Ce didacticiel vous guidera à travers les étapes d’installation lorsque vous avez besoin.

## <a name="download-the-sample-application"></a>Télécharger l’exemple d’application

L’application que vous allez déployer est nommée Contoso University et a déjà été créée pour vous. Il s’agit d’une version simplifiée d’un site web university, faiblement selon de l’application Contoso University décrite dans le [didacticiels Entity Framework sur le site ASP.NET](https://asp.net/entity-framework/tutorials).

Lorsque vous disposez des composants requis, téléchargez le [application web de Contoso University](https://go.microsoft.com/fwlink/p/?LinkId=282627). Le *.zip* fichier contient plusieurs versions du projet. Pour les étapes du didacticiel, commencez avec le projet situé dans le dossier c#. Pour voir à quoi ressemble le projet à la fin des didacticiels, ouvrez le projet dans le dossier ContosoUniversity-End.

Pour préparer le projet en utilisant les étapes du didacticiel, procédez comme suit :

1. Enregistrer les fichiers de solution ContosoUniversity à partir du dossier dans un dossier nommé ContosoUniversity dans le dossier que vous utilisez pour travailler avec des projets Visual Studio c#.

    Par défaut, il est le dossier suivant pour Visual Studio 2012 :

    `C:\Users\<username>\Documents\Visual Studio 2012\Projects`

    (Pour les captures d’écran de ce didacticiel, le dossier du projet se trouve dans le répertoire racine sur le `C`: lecteur.)
2. Démarrez Visual Studio et ouvrez le projet.
3. Dans **l’Explorateur de solutions**, avec le bouton droit de la solution, puis cliquez sur **restauration des packages EnableNuGet**.
4. Générez la solution.
5. Si vous obtenez des erreurs de compilation, restaurer manuellement les packages NuGet :

    1. Dans **l’Explorateur de solutions**, avec le bouton droit de la solution, puis cliquez sur **gérer les Packages NuGet pour la Solution**.
    2. En haut de la **gérer les Packages NuGet** boîte de dialogue vous verrez **les packages NuGet certains sont manquants à partir de cette solution. Cliquez sur Restaurer.** Cliquez sur le **restaurer** bouton.
    3. Régénérez la solution.
6. Appuyez sur CTRL + F5 pour exécuter l’application.

    L’application s’ouvre à la page d’accueil Contoso University.

    ![Développement de la Page d’accueil](introduction/_static/image1.png)

    (Il peut y avoir un délai d’attente pendant que Visual Studio démarre l’instance de SQL Server Express LocalDB, et vous pouvez obtenir une erreur de délai d’attente si que le processus dure trop longtemps. Dans ce cas simplement démarrer le projet à nouveau.)

Les pages de site Web sont accessibles à partir de la barre de menus et vous permettent d’effectuer les fonctions suivantes :

- Afficher les statistiques de l’étudiant (la page à propos de).
- Afficher, modifier, supprimer et ajouter des étudiants.
- Afficher et modifier des cours.
- Afficher et modifier les formateurs.
- Afficher et modifier des services.

Voici les captures d’écran de quelques pages représentatifs.

![Développement de Page étudiants](introduction/_static/image2.png)

![Ajouter les étudiants Page développement](introduction/_static/image3.png)

## <a name="review-application-features-that-affect-deployment"></a>Passez en revue les fonctionnalités qui affectent le déploiement d’applications

Les fonctionnalités suivantes de l’application affectent le déploiement ou que vous avez à faire pour le déployer. Chacune d’elles est expliquée plus en détail dans les didacticiels suivants de la série.

- Université de Contoso utilise une base de données SQL Server pour stocker les données d’application tels que les noms de student et instructor. Lorsque vous déployez pour la production vous devrez exclure les données de test et la base de données contient un mélange de données de test et les données de production.
- L’application utilise le système d’appartenance ASP.NET, qui stocke des informations de compte d’utilisateur dans une base de données SQL Server. L’application définit un utilisateur d’administrateur qui a accès à des informations restreintes. Vous devez déployer la base de données d’appartenance sans comptes de test, mais avec un compte d’administrateur.
- L’application utilise une application tierce journalisation des erreurs et utilitaire de création de rapports. Cet utilitaire est fourni dans un assembly qui doit être déployé avec l’application.
- L’utilitaire de journalisation erreur écrit les informations d’erreur dans des fichiers XML dans un dossier. Vous devez vous assurer que le compte ASP.NET sous lequel s’exécute dans le site déployé a l’autorisation d’écriture à ce dossier, et vous devez exclure ce dossier de déploiement. (Dans le cas contraire, données de journal d’erreur à partir de l’environnement de test peuvent être déployées en production et/ou de fichiers de journal des erreurs de production peuvent être supprimés.)
- L’application inclut certains paramètres qui doivent être modifiées dans déployé *Web.config* selon l’environnement de destination (test, intermédiaire ou production) et d’autres paramètres qui doivent être modifiés en fonction de la build configuration (Debug ou Release).
- La solution Visual Studio inclut un projet de bibliothèque de classes. Uniquement l’assembly généré par ce projet doit être déployé, pas le projet lui-même.

## <a name="summary"></a>Résumé

Dans ce premier didacticiel de la série, vous avez téléchargé l’exemple de projet Visual Studio et passé en revue les fonctionnalités du site qui affectent la façon dont vous déployez l’application. Dans les didacticiels suivants vous préparer pour le déploiement en définissant une partie de ces éléments pour être géré automatiquement. D’autres que vous prenez soin de manuellement.

>[!div class="step-by-step"]
[Next](preparing-databases.md)
