---
uid: mvc/overview/older-versions-1/nerddinner/introducing-the-nerddinner-tutorial
title: "Présentation du didacticiel de NerdDinner | Documents Microsoft"
author: shanselman
description: "La meilleure façon d’apprendre une nouvelle infrastructure doit créer quelque chose avec lui. Ce didacticiel vous montre comment créer une application petite mais complet, à l’aide d’ASP.ne...."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/27/2010
ms.topic: article
ms.assetid: 397522d5-0402-4b94-b810-a2fb564f869d
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/nerddinner/introducing-the-nerddinner-tutorial
msc.type: authoredcontent
ms.openlocfilehash: 57eedb224e26867c78cc399b89f91b95f722074d
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="introducing-the-nerddinner-tutorial"></a>Présentation du didacticiel de NerdDinner
====================
par [Scott Hanselman](https://github.com/shanselman)

[Télécharger le PDF](http://aspnetmvcbook.s3.amazonaws.com/aspnetmvc-nerdinner_v1.pdf)

> La meilleure façon d’apprendre une nouvelle infrastructure doit créer quelque chose avec lui. Ce didacticiel vous montre comment générer un petit, mais se termine, application à l’aide d’ASP.NET MVC 1 et présente certains des principaux concepts derrière lui.
> 
> Si vous utilisez ASP.NET MVC 3, nous vous recommandons de suivre les [mise en route a démarré avec MVC 3](../../older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) ou [magasin de musique MVC](../../older-versions/mvc-music-store/mvc-music-store-part-1.md) didacticiels.


## <a name="nerddinner-tutorial"></a>Didacticiel de NerdDinner

La meilleure façon d’apprendre une nouvelle infrastructure doit créer quelque chose avec lui. Ce didacticiel vous montre comment générer un petit, mais se termine, application à l’aide d’ASP.NET MVC et présente certains des principaux concepts derrière lui.

L’application que nous allons créer est appelée « NerdDinner ». NerdDinner offre un moyen facile pour les personnes rechercher et organiser préparés en ligne :

![](introducing-the-nerddinner-tutorial/_static/image1.png)

NerdDinner permet de créer, modifier et supprimer préparés les utilisateurs inscrits. Il applique un jeu cohérent de règles de validation et d’entreprise dans l’application :

![](introducing-the-nerddinner-tutorial/_static/image2.png)

Visiteurs peuvent d’effectuer un mappage basé sur AJAX pour rechercher préparés à venir qui se trouve près d’eux :

![](introducing-the-nerddinner-tutorial/_static/image3.png)

Un clic sur un dîner mène à une page de détails où ils peuvent en savoir plus :

![](introducing-the-nerddinner-tutorial/_static/image4.png)

S’ils sont intéressé par le dîner peuvent se connecter ou s’inscrire sur le site :

![](introducing-the-nerddinner-tutorial/_static/image5.png)

Ils peuvent puis cliquez sur une liaison basée sur AJAX de RSVP pour participer à l’événement :

![](introducing-the-nerddinner-tutorial/_static/image6.png)

![](introducing-the-nerddinner-tutorial/_static/image7.png)

### <a name="implementing-nerddinner"></a>Implémentation de NerdDinner

Nous allons commencer notre application NerdDinner à l’aide du fichier -&gt;commande Nouveau projet dans Visual Studio pour créer un projet ASP.NET MVC tout nouveau. Nous allons ajouter puis incrémentielle de fonctionnalités. Tout au long du processus, nous aborderons :

1. [Comment créer un nouveau projet ASP.NET MVC](# "créer un nouveau projet ASP.NET MVC")
2. [Comment créer une base de données](# "créer une base de données")
3. [Comment créer un modèle avec des validations de règle d’entreprise](# "générer un modèle avec les Validations de règle d’entreprise")
4. [L’utilisation de contrôleurs et les vues pour implémenter une interface utilisateur/détails annonce](# "utiliser des contrôleurs et des vues pour implémenter une interface utilisateur/détails de l’annonce")
5. [Comment fournir CRUD (créer, lire, mettre à jour, supprimer) prise en charge de l’entrée de données de formulaires](# "fournir CRUD (création, lecture, mise à jour, suppression) données formulaire entrée prend en charge")
6. [Comment utiliser ViewData et implémenter des classes ViewModel](# "ViewData d’utiliser et d’implémenter des Classes ViewModel")
7. [Comment réutiliser l’interface utilisateur à l’aide de pages maîtres et aucun](# "réutilisation d’interface utilisateur à l’aide des Pages maîtres et aucun")
8. [Comment implémenter la pagination des données efficace](# "implémenter de données efficace la pagination")
9. [Comment sécuriser des applications à l’aide de l’authentification et l’autorisation](# "Applications sécurisées à l’aide de l’authentification et autorisation")
10. [Comment utiliser AJAX pour fournir des mises à jour dynamiques](# "utiliser AJAX pour fournir des mises à jour dynamiques")
11. [Comment utiliser AJAX pour implémenter des scénarios de mappage](# "utiliser AJAX pour implémenter les scénarios de mappage")
12. [Comment activer le test unitaire automatisé](# "activer les tests unitaires automatisés")

Vous pouvez générer votre propre copie de NerdDinner à partir de zéro à la fin de chaque étape nous procédure pas à pas dans ce chapitre. Vous pouvez également télécharger une version complète du code source ici : [NerdDinner sur GitHub](https://github.com/AspNetMVPSamples/NerdDinner). Vous pouvez également éventuellement également [télécharger une version PDF de ce didacticiel](http://aspnetmvcbook.s3.amazonaws.com/aspnetmvc-nerdinner_v1.pdf) si vous souhaitez lire le didacticiel hors connexion.

Pour générer l’application, vous pouvez utiliser Visual Studio 2008 ou la libre Visual Web Developer 2008 Express. Vous pouvez utiliser SQL Server ou la version gratuite SQL Server Express pour la base de données.

Vous pouvez installer ASP.NET MVC, Visual Web Developer 2008 Express et SQL Server Express (gratuit) ce utilisant V2 de la [Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx)

### <a name="now-lets-get-started"></a>Maintenant nous pouvons commencer...

Maintenant que nous avons couvert NerdDinner What ' s, nous allons Relevez nos manches et écrire du code.

Nous allons commencer à l’aide de fichier -&gt;nouveau projet dans Visual Studio pour créer l’application de NerdDinner.

>[!div class="step-by-step"]
[Next](create-a-new-aspnet-mvc-project.md)
