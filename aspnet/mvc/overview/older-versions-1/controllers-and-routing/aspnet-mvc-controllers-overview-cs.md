---
uid: mvc/overview/older-versions-1/controllers-and-routing/aspnet-mvc-controllers-overview-cs
title: "Vue d’ensemble du contrôleur ASP.NET MVC (c#) | Documents Microsoft"
author: StephenWalther
description: "Dans ce didacticiel, Stephen Walther présente les contrôleurs ASP.NET MVC. Vous apprenez à créer de nouveaux contrôleurs et de retourner différents types de res d’action..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/16/2008
ms.topic: article
ms.assetid: b985c49a-3668-455c-a366-f85f6bc64b12
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/controllers-and-routing/aspnet-mvc-controllers-overview-cs
msc.type: authoredcontent
ms.openlocfilehash: 9e4ca745fa068b1813e01b131d53a0199cc47d5a
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-mvc-controller-overview-c"></a>Vue d’ensemble du contrôleur ASP.NET MVC (c#)
====================
par [Stephen Walther](https://github.com/StephenWalther)

> Dans ce didacticiel, Stephen Walther présente les contrôleurs ASP.NET MVC. Vous apprenez à créer de nouveaux contrôleurs et retourner différents types de résultats d’action.


Ce didacticiel explore la rubrique de contrôleurs ASP.NET MVC, les actions de contrôleur et les résultats d’action. Après avoir terminé ce didacticiel, vous saurez comment les contrôleurs sont utilisés pour contrôler la façon dont un visiteur interagit avec un site Web ASP.NET MVC.

## <a name="understanding-controllers"></a>Présentation des contrôleurs

Contrôleurs MVC sont chargés de répondre aux requêtes effectuées par rapport à un site Web ASP.NET MVC. Chaque demande de navigateur est mappée à un contrôleur spécifique. Par exemple, imaginez que vous entrez l’URL suivante dans la barre d’adresses de votre navigateur :

`http://localhost/Product/Index/3`

Dans ce cas, un contrôleur nommé ProductController est appelé. Le ProductController est responsable de la génération de la réponse à la demande de navigateur. Par exemple, le contrôleur peut retourner une vue particulière au navigateur ou le contrôleur peut rediriger l’utilisateur vers un autre contrôleur.

Le listing 1 contient un contrôleur simple nommé ProductController.

**Listing1 - Controllers\ProductController.cs**

[!code-csharp[Main](aspnet-mvc-controllers-overview-cs/samples/sample1.cs)]

Comme vous pouvez le voir à partir de la liste 1, un contrôleur est simplement une classe (une classe Visual Basic .NET ou c#). Un contrôleur est une classe qui dérive de la classe de base System.Web.Mvc.Controller. Un contrôleur hérite de cette classe de base, un contrôleur hérite de plusieurs méthodes utiles gratuitement (aborder ces méthodes dans un instant).

## <a name="understanding-controller-actions"></a>Présentation des Actions de contrôleur

Un contrôleur expose les actions de contrôleur. Une action est une méthode sur un contrôleur qui est appelée lorsque vous entrez une URL particulière dans la barre d’adresse de votre navigateur. Par exemple, imaginez que vous faites une demande pour l’URL suivante :

`http://localhost/Product/Index/3`

Dans ce cas, la méthode Index() est appelée sur la classe ProductController. La méthode Index() est un exemple d’une action de contrôleur.

Une action de contrôleur doit être une méthode publique d’une classe de contrôleur. Méthodes c#, par défaut, sont des méthodes privées. Notez que n’importe quelle méthode publique que vous ajoutez à une classe de contrôleur est exposée comme une action de contrôleur automatiquement (vous devez veiller à ce sujet dans la mesure où une action de contrôleur peut être appelée par une personne dans l’univers simplement en tapant l’URL de droite dans une barre d’adresses du navigateur).

Il existe certaines exigences supplémentaires qui doivent être satisfaites par une action de contrôleur. Une méthode utilisée comme une action du contrôleur ne peut pas être surchargée. En outre, une action du contrôleur ne peut pas être une méthode statique. Reste, vous pouvez utiliser n’importe quelle méthode comme une action de contrôleur.

## <a name="understanding-action-results"></a>Présentation des résultats d’Action

Une action du contrôleur retourne une valeur appelée un *résultat d’action*. Un résultat d’action est qu’une action du contrôleur retourne en réponse à une demande de navigateur.

L’infrastructure ASP.NET MVC prend en charge plusieurs types de résultats d’action, notamment :

1. ViewResult - représente HTML et des balises.
2. EmptyResult - ne représente aucun résultat.
3. RedirectResult - représente une redirection vers une nouvelle URL.
4. JsonResult - représente un résultat de JavaScript Object Notation qui peut être utilisé dans une application AJAX.
5. JavaScriptResult - représente un script JavaScript.
6. ContentResult - représente un résultat de texte.
7. FileContentResult - représente un fichier téléchargeable (avec le contenu binaire).
8. FilePathResult - représente un fichier téléchargeable (avec un chemin d’accès).
9. FileStreamResult - représente un fichier téléchargeable (avec un flux de fichier).

Tous ces résultats action héritent de la classe ActionResult.

Dans la plupart des cas, une action du contrôleur retourne un ViewResult. Par exemple, l’action de contrôleur d’Index dans la liste 2 Retourne un ViewResult.

**Listing 2 - Controllers\BookController.cs**

[!code-csharp[Main](aspnet-mvc-controllers-overview-cs/samples/sample2.cs)]

Une action qui retourne un ViewResult HTML est retourné au navigateur. La méthode Index() dans la liste 2 Retourne une vue nommée des Index dans le navigateur.

Notez que l’action Index() dans la liste 2 ne retourne pas une ViewResult(). Au lieu de cela, la méthode View() de la classe de base du contrôleur est appelée. Normalement, vous ne retournez un résultat d’action directement. Au lieu de cela, vous appelez l’une des méthodes suivantes de la classe de base du contrôleur :

1. Afficher - retourne un résultat d’action ViewResult.
2. Rediriger - retourne un résultat d’action RedirectResult.
3. RedirectToAction - renvoie un résultat d’action RedirectToRouteResult.
4. RedirectToRoute - renvoie un résultat d’action RedirectToRouteResult.
5. JSON - retourne un résultat d’action JsonResult.
6. JavaScriptResult - renvoie un JavaScriptResult.
7. Content - retourne un résultat d’action ContentResult.
8. Fichier - retourne un FileContentResult, FilePathResult ou FileStreamResult selon les paramètres passé à la méthode.

Par conséquent, si vous souhaitez renvoyer un affichage dans le navigateur, vous appelez la méthode View(). Si vous souhaitez rediriger l’utilisateur à partir de l’action d’un contrôleur à un autre, vous appelez la méthode RedirectToAction(). Par exemple, l’action Details() dans la liste 3 affiche une vue ou redirige l’utilisateur vers l’action Index() selon si le paramètre Id a une valeur.

**La liste 3 - CustomerController.cs**

[!code-csharp[Main](aspnet-mvc-controllers-overview-cs/samples/sample3.cs)]

Le résultat d’action ContentResult est spécial. Vous pouvez utiliser le résultat d’action ContentResult pour retourner un résultat d’action en tant que texte brut. Par exemple, la méthode Index() sur la liste 4 retourne un message en texte brut et pas au format HTML.

**La liste 4 - Controllers\StatusController.cs**

[!code-csharp[Main](aspnet-mvc-controllers-overview-cs/samples/sample4.cs)]

Lorsque l’action StatusController.Index() est appelée, une vue n’est pas renvoyée. Au lieu de cela, le texte brut « Hello World ! » est retourné au navigateur.

Si une action du contrôleur retourne un résultat qui n’est pas un résultat d’action - par exemple, une date ou un entier - le résultat est encapsulé dans un ContentResult automatiquement. Par exemple, lorsque l’action Index() de la WorkController dans la liste 5 est appelée, la date est retournée automatiquement comme un ContentResult.

**La liste 5 - WorkController.cs**

[!code-csharp[Main](aspnet-mvc-controllers-overview-cs/samples/sample5.cs)]

L’action dans la liste 5 Index() retourne un objet DateTime. L’infrastructure ASP.NET MVC convertit l’objet DateTime en une chaîne et la valeur DateTime est encapsulé dans un ContentResult automatiquement. Le navigateur reçoit la date et l’heure sous forme de texte brut.

## <a name="summary"></a>Résumé

L’objectif de ce didacticiel est de vous présenter les concepts de contrôleurs ASP.NET MVC, les actions de contrôleur et les résultats d’action de contrôleur. Dans la première section, vous avez appris comment ajouter de nouveaux contrôleurs pour un projet ASP.NET MVC. Ensuite, vous avez appris les méthodes publics d’un contrôleur sont exposées à l’univers en tant qu’actions de contrôleur. Enfin, nous avons abordé les différents types de résultats d’action qui peuvent être retournées à partir d’une action du contrôleur. En particulier, nous avons expliqué comment retourner un ViewResult, RedirectToActionResult et ContentResult à partir d’une action du contrôleur.

>[!div class="step-by-step"]
[Précédent](creating-an-action-vb.md)
[Suivant](creating-custom-routes-cs.md)
