---
uid: mvc/overview/older-versions-1/unit-testing/creating-unit-tests-for-asp-net-mvc-applications-cs
title: "Création de Tests unitaires pour les Applications ASP.NET MVC (c#) | Documents Microsoft"
author: StephenWalther
description: "Découvrez comment créer des tests unitaires pour les actions de contrôleur. Dans ce didacticiel, Stephen Walther montre comment tester si une action du contrôleur retourne une section..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/19/2008
ms.topic: article
ms.assetid: d3a270b9-d7b1-47f2-8775-fc3beb518b5c
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/unit-testing/creating-unit-tests-for-asp-net-mvc-applications-cs
msc.type: authoredcontent
ms.openlocfilehash: 56c981363f1905c1c9869dbaf2adb6b5ac1c28a5
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-unit-tests-for-aspnet-mvc-applications-c"></a>Création de Tests unitaires pour les Applications ASP.NET MVC (c#)
====================
par [Stephen Walther](https://github.com/StephenWalther)

[Télécharger le PDF](http://download.microsoft.com/download/8/4/8/84843d8d-1575-426c-bcb5-9d0c42e51416/ASPNET_MVC_Tutorial_07_CS.pdf)

> Découvrez comment créer des tests unitaires pour les actions de contrôleur. Dans ce didacticiel, Stephen Walther montre comment tester si une action du contrôleur retourne une vue particulière, retourne un jeu de données particulier ou un autre type de résultat d’action.


L’objectif de ce didacticiel est d’illustrer le comment écrire des tests unitaires pour les contrôleurs dans votre MVC ASP.NET applications. Nous expliquent comment générer trois différents types de tests unitaires. Vous apprenez à la vue retournée par une action de contrôleur de test afficher les données retournées par une action de contrôleur de test et à tester si une action de contrôleur vous redirige vers une deuxième action de contrôleur.

## <a name="creating-the-controller-under-test"></a>Création du contrôleur de Test

Commençons par créer le contrôleur que nous voulons tester. Le nom du contrôleur, le `ProductController`, est contenue dans la liste 1.

**La liste 1 :`ProductController.cs`**

[!code-csharp[Main](creating-unit-tests-for-asp-net-mvc-applications-cs/samples/sample1.cs)]

Le `ProductController` contient deux méthodes d’action `Index()` et `Details()`. Les deux méthodes d’action retournent une vue. Notez que le `Details()` action accepte un paramètre nommé ID.

## <a name="testing-the-view-returned-by-a-controller"></a>La vue de test retourné par un contrôleur

Imaginez que vous voulez tester ou non la `ProductController` retourne la vue de droite. Nous voulons que quand le `ProductController.Details()` action est appelée, l’affichage des détails est retourné. La classe de test dans la liste 2 contienne un test unitaire pour tester la vue retournée par le `ProductController.Details()` action.

**Liste 2 :`ProductControllerTest.cs`**

[!code-csharp[Main](creating-unit-tests-for-asp-net-mvc-applications-cs/samples/sample2.cs)]

La classe de liste 2 inclut une méthode de test nommée `TestDetailsView()`. Cette méthode contient trois lignes de code. La première ligne de code crée une nouvelle instance de la `ProductController` classe. La deuxième ligne de code appelle du contrôleur `Details()` méthode d’action. Enfin, la dernière ligne de contrôle de code ou non la vue retournée par le `Details()` action est le mode Détails.

Le `ViewResult.ViewName` propriété représente le nom de la vue retourné par un contrôleur. Un avertissement sur le test de cette propriété. Il existe deux façons qu’un contrôleur peut retourner une vue. Un contrôleur peut retourner explicitement une vue comme suit :

[!code-csharp[Main](creating-unit-tests-for-asp-net-mvc-applications-cs/samples/sample3.cs)]

Vous pouvez également le nom de la vue peut être déduit à partir du nom de l’action de contrôleur comme suit :

[!code-csharp[Main](creating-unit-tests-for-asp-net-mvc-applications-cs/samples/sample4.cs)]

Cette action de contrôleur retourne également une vue nommée `Details`. Toutefois, le nom de la vue est déduit à partir du nom d’action. Si vous souhaitez tester le nom de la vue, vous devez retourner explicitement le nom de la vue à partir de l’action du contrôleur.

Vous pouvez exécuter le test unitaire dans la liste 2 en entrant la combinaison de touches **Ctrl + R, A** ou en cliquant sur le **exécuter tous les Tests de la Solution** bouton (voir Figure 1). Si le test réussit, vous verrez la fenêtre Résultats des tests dans la Figure 2.


[![Exécuter tous les Tests dans la Solution](creating-unit-tests-for-asp-net-mvc-applications-cs/_static/image2.png)](creating-unit-tests-for-asp-net-mvc-applications-cs/_static/image1.png)

**Figure 01**: exécuter tous les Tests de la Solution ([cliquez pour afficher l’image en taille réelle](creating-unit-tests-for-asp-net-mvc-applications-cs/_static/image3.png))


[![Opération réussie](creating-unit-tests-for-asp-net-mvc-applications-cs/_static/image5.png)](creating-unit-tests-for-asp-net-mvc-applications-cs/_static/image4.png)

**Figure 02**: opération réussie ! ([Cliquez pour afficher l’image en taille réelle](creating-unit-tests-for-asp-net-mvc-applications-cs/_static/image6.png))


## <a name="testing-the-view-data-returned-by-a-controller"></a>La vue de données de test retourné par un contrôleur

Contrôleur MVC transmet des données à une vue à l’aide d’un élément appelé  *`View Data`* . Par exemple, imaginez que vous souhaitez afficher les détails d’un produit particulier quand vous appelez le `ProductController Details()` action. Dans ce cas, vous pouvez créer une instance d’un `Product` classe (défini dans votre modèle) et passez l’instance à la `Details` vue en tirant parti des `View Data`.

Modifié `ProductController` dans la liste 3 inclut une mise à jour `Details()` action qui retourne un produit.

**La liste 3 :`ProductController.cs`**

[!code-csharp[Main](creating-unit-tests-for-asp-net-mvc-applications-cs/samples/sample5.cs)]

Tout d’abord, le `Details()` action crée une nouvelle instance de la `Product` classe qui représente un ordinateur portable. Ensuite, l’instance de la `Product` classe est passée en tant que second paramètre de la `View()` (méthode).

Vous pouvez écrire des tests unitaires pour vérifier si les données attendues seront contenue dans la vue données. Le test unitaire dans les tests de liste 4 ou non un produit qui représente un ordinateur portable est retourné lorsque vous appelez le `ProductController Details()` méthode d’action.

**La liste 4 –`ProductControllerTest.cs`**

[!code-csharp[Main](creating-unit-tests-for-asp-net-mvc-applications-cs/samples/sample6.cs)]

Dans la liste 4, le `TestDetailsView()` méthode teste les données d’affichage retournée en appelant le `Details()` (méthode). Le `ViewData` est exposée en tant que propriété sur le `ViewResult` retournée en appelant le `Details()` (méthode). Le `ViewData.Model` propriété contient le produit passé à la vue. Le test vérifie simplement que le produit contenu dans les données d’affichage a le nom d’ordinateur portable.

## <a name="testing-the-action-result-returned-by-a-controller"></a>Le résultat d’Action de test retourné par un contrôleur

Une action de contrôleur plus complexe peut-être retourner différents types de résultats d’action en fonction des valeurs de paramètres transmis à l’action du contrôleur. Une action de contrôleur peut retourner une variété de types de résultats d’action, notamment une `ViewResult`, `RedirectToRouteResult`, ou `JsonResult`.

Par exemple, la modification `Details()` action dans la liste 5 retourne le `Details` afficher quand vous passez un Id de produit valide à l’action. Si vous passez un produit non valide Id--un Id avec une valeur inférieure à 1, alors que vous êtes redirigé vers la `Index()` action.

**La liste 5 :`ProductController.cs`**

[!code-csharp[Main](creating-unit-tests-for-asp-net-mvc-applications-cs/samples/sample7.cs)]

Vous pouvez tester le comportement de la `Details()` action avec le test unitaire dans la liste 6. Le test unitaire dans la liste 6 vérifie que vous êtes redirigé vers la `Index` afficher lorsqu’un Id avec la valeur -1 est passé à la `Details()` (méthode).

**La liste 6 :`ProductControllerTest.cs`**

[!code-csharp[Main](creating-unit-tests-for-asp-net-mvc-applications-cs/samples/sample8.cs)]

Lorsque vous appelez le `RedirectToAction()` méthode dans une action de contrôleur, l’action du contrôleur retourne un `RedirectToRouteResult`. Les contrôles de test si le `RedirectToRouteResult` redirige l’utilisateur à une action de contrôleur nommée `Index`.

## <a name="summary"></a>Résumé

Dans ce didacticiel, vous avez appris à créer des tests unitaires pour les actions de contrôleur MVC. Tout d’abord, vous avez appris comment vérifier si la vue de droite est retournée par une action de contrôleur. Vous avez appris à utiliser le `ViewResult.ViewName` propriété pour vérifier le nom d’une vue.

Ensuite, nous avons examiné comment vous pouvez tester le contenu de `View Data`. Vous avez appris comment vérifier si le produit de droite a été retourné dans `View Data` après l’appel à une action du contrôleur.

Enfin, nous avons expliqué comment vous pouvez tester si les différents types de résultats d’action sont renvoyées à partir d’une action du contrôleur. Vous avez appris comment tester si un contrôleur retourne un `ViewResult` ou `RedirectToRouteResult`.

>[!div class="step-by-step"]
[Next](creating-unit-tests-for-asp-net-mvc-applications-vb.md)
