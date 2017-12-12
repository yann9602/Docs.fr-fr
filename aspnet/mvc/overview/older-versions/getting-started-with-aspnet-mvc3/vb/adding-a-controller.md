---
uid: mvc/overview/older-versions/getting-started-with-aspnet-mvc3/vb/adding-a-controller
title: "Ajout d’un contrôleur (VB) | Documents Microsoft"
author: Rick-Anderson
description: "Ce didacticiel, vous allez apprendre les principes fondamentaux de la création d’une application Web ASP.NET MVC à l’aide de Microsoft Visual Web Developer 2010 Express Service Pack 1, qui est en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/12/2011
ms.topic: article
ms.assetid: 741259e1-54ac-4f71-b4e8-2bd5560bb950
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-aspnet-mvc3/vb/adding-a-controller
msc.type: authoredcontent
ms.openlocfilehash: 74113d76a74b1da27a7f9a33a13038a0c36ad036
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="adding-a-controller-vb"></a>Ajout d’un contrôleur (VB)
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> Ce didacticiel, vous allez apprendre les principes fondamentaux de la création d’une application Web ASP.NET MVC à l’aide de Microsoft Visual Web Developer 2010 Express Service Pack 1, qui est une version gratuite de Microsoft Visual Studio. Avant de commencer, assurez-vous que vous avez installé les composants requis répertoriés ci-dessous. Vous pouvez installer tous les en cliquant sur le lien suivant : [Web Platform Installer](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack). Vous pouvez également installer individuellement les conditions préalables à l’aide des liens suivants :
> 
> - [Conditions préalables requises de Visual Studio Web Developer Express SP1](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack)
> - [Mettre à jour des outils ASP.NET MVC 3](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=MVC3)
> - [SQL Server Compact 4.0](https://www.microsoft.com/web/gallery/install.aspx?appid=SQLCE;SQLCEVSTools_4_0)(runtime + outils prennent en charge)
> 
> Si vous utilisez Visual Studio 2010 au lieu de Visual Web Developer 2010, installez les composants requis en cliquant sur le lien suivant : [composants requis de Visual Studio 2010](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=VS2010SP1Pack).
> 
> Un projet de Visual Web Developer avec code VB.NET source est disponible pour accompagner cette rubrique. [Télécharger la version VB.NET](https://code.msdn.microsoft.com/Introduction-to-MVC-3-10d1b098). Si vous préférez c#, basculez vers le [c# version](../cs/adding-a-controller.md) de ce didacticiel.


MVC est l’acronyme *model-view-controller*. MVC est un modèle pour le développement d’applications de telle sorte que chaque partie possède une responsabilité distincte :

- Modèle : Les données de votre application.
- Vues : Les fichiers de modèle de votre application utilisera pour générer dynamiquement des réponses HTML.
- : Contrôleurs de Classes qui gèrent les demandes d’URL entrantes à l’application, récupérer des données de modèle, puis spécifiez les modèles d’affichage qui restituent une réponse au client.

Nous allons être couvrant tous ces concepts dans ce didacticiel et vous montrent comment les utiliser pour générer une application.

Créer un nouveau contrôleur en double-cliquant sur le *contrôleurs* dossier **l’Explorateur de solutions** , puis en sélectionnant **ajouter un contrôleur**.

[![AddController](adding-a-controller/_static/image2.png "AddController")](adding-a-controller/_static/image1.png)

Nom de votre nouveau contrôleur &quot;HelloWorldController&quot; et cliquez sur **ajouter**.

[![2AddEmptyController](adding-a-controller/_static/image4.png "2AddEmptyController")](adding-a-controller/_static/image3.png)

Notez que dans **l’Explorateur de solutions** sur la droite qu’un nouveau fichier a été créé pour vous nommé *HelloWorldController.cs* et que le fichier est ouvert dans l’IDE.

À l’intérieur de la nouvelle `public class HelloWorldController` de bloc, créez deux nouvelles méthodes qui ressemble au code suivant. Nous allons retourner une chaîne HTML directement à partir du contrôleur, par exemple.

[!code-vb[Main](adding-a-controller/samples/sample1.vb)]

Votre contrôleur est nommé `HelloWorldController` et votre nouvelle méthode est nommé `Index`. Exécutez l’application (appuyez sur F5 ou Ctrl + F5). Une fois que votre navigateur ait démarré, ajouter &quot;HelloWorld&quot; pour le chemin d’accès dans la barre d’adresses. (Sur un ordinateur, il a `http://localhost:43246/HelloWorld`) votre navigateur doit ressembler à la capture d’écran ci-dessous. Dans la méthode ci-dessus, le code a renvoyé une chaîne directement. Nous avons dit le système de simplement retourner du code HTML et il l’a fait !

![](adding-a-controller/_static/image5.png)

ASP.NET MVC appelle des classes de l’autre contrôleur (et méthodes d’action différentes au sein de celles-ci) en fonction de l’URL entrante. La logique de mappage par défaut utilisée par ASP.NET MVC utilise un format comme suit pour contrôler le code qui est appelé :

`/[Controller]/[ActionName]/[Parameters]`

La première partie de l’URL détermine la classe de contrôleur à exécuter. Par conséquent, */HelloWorld* mappe à la `HelloWorldController` classe. La deuxième partie de l’URL détermine la méthode d’action sur la classe à exécuter. Par conséquent, */HelloWorld/Index* provoquerait le `Index` méthode de la `HelloWorldController` classe à exécuter. Notez que nous avons dû uniquement à visiter */HelloWorld* ci-dessus et `Index` méthode a été utilisée par défaut. Il s’agit, car une méthode nommée `Index` est la méthode par défaut qui sera appelée sur un contrôleur s’il n’est pas explicitement spécifié.

Accédez à `http://localhost:xxxx/HelloWorld/Welcome`. Le `Welcome` méthode s’exécute et retourne la chaîne &quot;c’est la méthode d’action accueil... &quot;. Le mappage de MVC par défaut est `/[Controller]/[ActionName]/[Parameters]`. Pour cette URL, le contrôleur est `HelloWorld` et `Welcome` est la méthode. Nous n’avons pas utilisé le `[Parameters]` dans le cadre de l’URL encore.

![](adding-a-controller/_static/image6.png)

Nous allons modifier légèrement l’exemple afin que nous pouvons transmettre des informations de paramètre dans à partir de l’URL pour le contrôleur (par exemple, */HelloWorld/Bienvenue ? nom = Scott&amp;numtimes = 4*). Modifier votre `Welcome` méthode pour inclure les deux paramètres comme indiqué ci-dessous. Notez que nous avons utilisé la fonctionnalité de paramètre facultatif VB pour indiquer que le `numTimes` paramètre par défaut 1 si aucune valeur n’est passée pour ce paramètre.

[!code-vb[Main](adding-a-controller/samples/sample2.vb)]

Exécuter votre application et accédez à `http://localhost:xxxx/HelloWorld/Welcome?name=Scott&numtimes=4` **.** Vous pouvez essayer différentes valeurs pour `name` et `numtimes`. Le système mappe automatiquement les paramètres nommés dans votre chaîne de requête dans la barre d’adresses à des paramètres dans votre méthode.

![](adding-a-controller/_static/image7.png)

Dans ces deux exemples le contrôleur a fait la partie VC de MVC, qui est le travail de la vue et contrôleur. Le contrôleur retourne HTML directement. En général, nous ne voulons contrôleurs renvoyant du HTML directement, étant donné que qui devient très lourde au code. Au lieu de cela, nous allons utiliser généralement un fichier de modèle de vue séparé afin de générer la réponse HTML. Voyons comment nous pouvons faire cela.

>[!div class="step-by-step"]
[Précédent](intro-to-aspnet-mvc-3.md)
[Suivant](adding-a-view.md)
