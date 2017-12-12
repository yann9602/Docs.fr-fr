---
uid: mvc/overview/older-versions/getting-started-with-aspnet-mvc4/adding-a-controller
title: "Ajout d’un contrôleur | Documents Microsoft"
author: Rick-Anderson
description: "Remarque : Une version mise à jour de ce didacticiel est disponible ici qui utilise ASP.NET MVC 5 et Visual Studio 2013. Il est plus sécurisé, beaucoup plus simple à suivre et de démonstration..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/28/2012
ms.topic: article
ms.assetid: 0267d31c-892f-49a1-9e7a-3ae8cc12b2ca
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-aspnet-mvc4/adding-a-controller
msc.type: authoredcontent
ms.openlocfilehash: 69af91401e51470fbc0b67103345325201b06723
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="adding-a-controller"></a>Ajour d’un contrôleur
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> > [!NOTE]
> > Une version mise à jour de ce didacticiel est disponible [ici](../../getting-started/introduction/getting-started.md) qui utilise ASP.NET MVC 5 et Visual Studio 2013. Il est plus sécurisé, beaucoup plus simple à suivre et illustre plusieurs fonctionnalités.


MVC est l’acronyme *model-view-controller*. MVC est un modèle pour le développement d’applications qui sont bien conçue, testable et facile à gérer. Applications basées sur MVC contiennent :

- **M** odels : les Classes qui représentent les données de l’application et qui utilisent une logique de validation pour appliquer des règles d’entreprise pour que les données.
- **V** entretiens : fichiers de modèle que votre application utilise pour générer dynamiquement des réponses HTML.
- **C** ontrollers : Classes qui gèrent les demandes entrantes de navigateur, récupérer des données de modèle, puis spécifiez les modèles d’affichage qui renvoient une réponse au navigateur.

Nous allons être couvrant tous ces concepts dans cette série de didacticiels et vous montrent comment les utiliser pour générer une application.

Commençons par créer une classe de contrôleur. Dans **l’Explorateur de solutions**, avec le bouton droit le *contrôleurs* dossier, puis sélectionnez **ajouter un contrôleur**.

![](adding-a-controller/_static/image1.png)

Nom de votre nouveau contrôleur &quot;HelloWorldController&quot;. Laissez le modèle par défaut en tant que **contrôleur MVC vide** et cliquez sur **ajouter**.

![Ajouter un contrôleur](adding-a-controller/_static/image2.png)

Notez que dans **l’Explorateur de solutions** un nouveau fichier a été créé nommé *HelloWorldController.cs*. Le fichier est ouvert dans l’IDE.

![](adding-a-controller/_static/image3.png)

Remplacez le contenu du fichier par le code suivant.

[!code-csharp[Main](adding-a-controller/samples/sample1.cs)]

Les méthodes de contrôleur retourne une chaîne HTML par exemple. Le contrôleur est nommé `HelloWorldController` et la première méthode ci-dessus est nommée `Index`. Nous allons appeler à partir d’un navigateur. Exécutez l’application (appuyez sur F5 ou Ctrl + F5). Dans le navigateur, ajoutez &quot;HelloWorld&quot; pour le chemin d’accès dans la barre d’adresses. (Par exemple, dans l’illustration ci-dessous, il `http://localhost:1234/HelloWorld.`) la page dans le navigateur doit ressembler à la capture d’écran suivante. Dans la méthode ci-dessus, le code a renvoyé une chaîne directement. Vous avez indiqué le système de simplement retourner du code HTML, et il l’a fait !

![](adding-a-controller/_static/image4.png)

ASP.NET MVC appelle des classes de l’autre contrôleur (et méthodes d’action différentes au sein de celles-ci) en fonction de l’URL entrante. La logique de routage URL par défaut utilisée par ASP.NET MVC utilise un format comme suit pour déterminer quel code pour appeler :

`/[Controller]/[ActionName]/[Parameters]`

La première partie de l’URL détermine la classe de contrôleur à exécuter. Par conséquent, */HelloWorld* mappe à la `HelloWorldController` classe. La deuxième partie de l’URL détermine la méthode d’action sur la classe à exécuter. Par conséquent, */HelloWorld/Index* provoquerait le `Index` méthode de la `HelloWorldController` classe à exécuter. Notez que nous avons dû uniquement pour accéder à */HelloWorld* et `Index` méthode a été utilisée par défaut. Il s’agit, car une méthode nommée `Index` est la méthode par défaut qui sera appelée sur un contrôleur s’il n’est pas explicitement spécifié.

Accédez à `http://localhost:xxxx/HelloWorld/Welcome`. Le `Welcome` méthode s’exécute et retourne la chaîne &quot;c’est la méthode d’action accueil... &quot;. Le mappage de MVC par défaut est `/[Controller]/[ActionName]/[Parameters]`. Pour cette URL, le contrôleur est `HelloWorld`, et `Welcome` est la méthode d’action. Vous n’avez pas encore utilisé la partie `[Parameters]` de l’URL.

![](adding-a-controller/_static/image5.png)

Nous allons modifier légèrement l’exemple afin que vous pouvez passer des informations de paramètre à partir de l’URL pour le contrôleur (par exemple, */HelloWorld/Bienvenue ? nom = Scott&amp;numtimes = 4*). Modifier votre `Welcome` méthode pour inclure les deux paramètres comme indiqué ci-dessous. Notez que le code utilise la fonctionnalité de paramètre facultatif C# pour indiquer que le `numTimes` paramètre par défaut 1 si aucune valeur n’est passée pour ce paramètre.

[!code-csharp[Main](adding-a-controller/samples/sample2.cs)]

Exécuter votre application et accédez à l’exemple d’URL (`http://localhost:xxxx/HelloWorld/Welcome?name=Scott&numtimes=4)`. Vous pouvez essayer différentes valeurs pour `name` et `numtimes` dans l’URL. Le [système de liaison de modèle ASP.NET MVC](http://odetocode.com/Blogs/scott/archive/2009/04/27/6-tips-for-asp-net-mvc-model-binding.aspx) mappe automatiquement les paramètres nommés à partir de la chaîne de requête dans la barre d’adresses à des paramètres dans votre méthode.

![](adding-a-controller/_static/image6.png)

Dans ces deux exemples le contrôleur a fait la &quot;VC&quot; partie de MVC, autrement dit, le travail de la vue et contrôleur. Le contrôleur retourne HTML directement. En général, vous ne souhaitez contrôleurs renvoyant du HTML directement, étant donné que qui devient très lourde au code. Au lieu de cela, nous allons utiliser généralement un fichier de modèle de vue séparé afin de générer la réponse HTML. Penchons-nous à la façon dont nous pouvons faire cela.

>[!div class="step-by-step"]
[Précédent](intro-to-aspnet-mvc-4.md)
[Suivant](adding-a-view.md)
