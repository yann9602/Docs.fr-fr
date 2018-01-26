---
uid: mvc/overview/getting-started/introduction/getting-started
title: Mise en route avec ASP.NET MVC 5 | Documents Microsoft
author: Rick-Anderson
description: "Remarque : Une version mise à jour de ce didacticiel est disponible ici à l’aide de Visual Studio 2015. Le nouveau didacticiel utilise ASP.NET Core MVC 6, qui fournit de nombreuses improvem..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/28/2015
ms.topic: article
ms.assetid: f3d8adbe-55e7-4fd4-84a8-7155bc45c676
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/introduction/getting-started
msc.type: authoredcontent
ms.openlocfilehash: 1616b238612fa9f519418f583c40a46b9d81d8ce
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="getting-started-with-aspnet-mvc-5"></a>Bien démarrer avec ASP.NET MVC 5
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

[!INCLUDE[consider RP](../../../../includes/razor.md)]

 
 Ce didacticiel, vous allez apprendre les principes fondamentaux de la création d’une application web ASP.NET MVC 5 avec [Visual Studio 2017](https://www.visualstudio.com/). Dernière Source pour le didacticiel située sur [GitHub](https://github.com/aspnet/Docs/tree/master/aspnet/mvc/overview/getting-started/introduction/sample/MvcMovie/MvcMovie)
 
 
 Ce didacticiel a été écrit par [Scott Guthrie](https://weblogs.asp.net/scottgu/) (twitter[ @scottgu ](https://twitter.com/scottgu) ), [Scott Hanselman](http://www.hanselman.com/blog/) (twitter : [ @shanselman ](https://twitter.com/shanselman) ) , et [Rick Anderson](https://twitter.com/RickAndMSFT) ( [ @RickAndMSFT ](https://twitter.com/#!/RickAndMSFT) )
 
 Vous avez besoin d’un compte Azure pour déployer cette application sur Azure :
 
 - Vous pouvez [ouvrir un compte Azure gratuitement](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) -vous obtenez des crédits que vous pouvez utiliser pour tester les services Azure payants et même après qu’ils soient utilisés jusqu'à, vous pouvez conserver le compte et libérer de l’utilisation des services Azure.
 - Vous pouvez [activer les abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) -votre abonnement MSDN vous donne des crédits chaque mois que vous pouvez utiliser pour les services Azure payants.


## <a name="getting-started"></a>Prise en main

Commencez par installer et exécuter [Visual Studio 2017](https://www.visualstudio.com/).

Visual Studio est un environnement de développement intégré ou IDE. Tout comme vous utilisez Microsoft Word pour écrire des documents, vous utiliserez un bus IDE pour créer des applications. Dans Visual Studio, il existe une liste au bas affichant les différentes options disponibles pour vous. Il existe également un menu qui fournit un autre moyen pour effectuer des tâches dans l’IDE. (Par exemple, au lieu de sélectionner **nouveau projet** à partir de la **Démarrer** page, vous pouvez utiliser le menu et sélectionnez **fichier** &gt; **denouveauprojet**.)

   
![](getting-started/_static/image1.png)  
 

## <a name="creating-your-first-application"></a>Créer votre première Application

Cliquez sur **nouveau projet**, puis sélectionnez Visual c# sur la gauche, puis **Web** , puis sélectionnez **ASP.NET Web Applications (.NET Framework)**. Nommez votre projet « MvcMovie », puis **OK**.

![](getting-started/_static/image2.png)

Dans le **nouveau projet ASP.NET** boîte de dialogue, cliquez sur **MVC** puis cliquez sur **OK**.

![](getting-started/_static/image3.png)

Visual Studio a utilisé un modèle par défaut pour le projet ASP.NET MVC que vous venez de créer, afin que vous ayez une application fonctionne maintenant sans rien faire ! Il s’agit d’un simple « Hello World ! » projet et il l’un bon point de départ de votre application.

![](getting-started/_static/image4.png)

Cliquez sur F5 pour démarrer le débogage. F5 provoque Visual Studio Démarrer [IIS Express](https://www.iis.net/learn/extensions/introduction-to-iis-express/iis-express-overview) et exécuter votre application web. Ensuite, Visual Studio lance un navigateur et ouvre la page d’accueil de l’application. Notez que la barre d’adresses du navigateur indique `localhost:port#` et pas quelque chose comme `example.com`. C’est parce que `localhost` pointe toujours sur votre ordinateur local, qui dans ce cas est exécutée dans l’application que vous venez de créer. Lorsque Visual Studio est exécuté un projet web, un port aléatoire est utilisé pour le serveur web. Dans l’image ci-dessous, le numéro de port est 1234. Lorsque vous exécutez l’application, vous verrez un numéro de port différent.

![](getting-started/_static/image5.png)

Emploi ce modèle par défaut vous donne les pages Accueil, Contact et sur. L’image ci-dessus n’affiche pas les **accueil**, **sur** et **Contact** des liens. Selon la taille de la fenêtre du navigateur, vous devrez peut-être cliquer sur l’icône de navigation pour afficher ces liens.

![](getting-started/_static/image6.png)  

L’application fournit également la prise en charge pour inscrire et se connecter. L’étape suivante consiste à modifier le fonctionnement de cette application et en savoir un peu de ASP.NET MVC. Fermez l’application ASP.NET MVC et nous allons modifier du code.

Pour obtenir la liste de didacticiels actuelles, consultez [MVC recommandé articles](../mvc-learning-sequence.md).

## <a name="see-this-app-running-on-azure"></a>Consultez cette application en cours d’exécution sur Azure

Vous voulez voir le site terminé en cours d’exécution comme une application web dynamique ? Vous pouvez déployer une version complète de l’application à votre compte Azure en cliquant simplement sur le bouton suivant.

[![](https://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/?repository=https://github.com/aspnet/Docs/tree/master/aspnet/mvc/overview/getting-started/introduction/sample/MvcMovie&amp;WT.mc_id=deploy_azure_aspnet)

Vous avez besoin d’un compte Azure pour déployer cette solution sur Azure. Si vous n’avez pas déjà un compte, vous disposez des options suivantes :

- [Ouvrir un compte Azure gratuitement](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) -vous obtenez des crédits que vous pouvez utiliser pour tester les services Azure payants et même après qu’ils soient utilisés jusqu'à, vous pouvez conserver le compte et libérer de l’utilisation des services Azure.
- [Activer les abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) -votre abonnement MSDN vous donne des crédits chaque mois que vous pouvez utiliser pour les services Azure payants.

>[!div class="step-by-step"]
[Next](adding-a-controller.md)
