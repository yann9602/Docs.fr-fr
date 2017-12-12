---
uid: mvc/overview/older-versions-1/views/passing-data-to-view-master-pages-cs
title: "Passer des données à afficher les Pages maîtres (c#) | Documents Microsoft"
author: microsoft
description: "L’objectif de ce didacticiel est d’expliquer comment vous pouvez passer des données à partir d’un contrôleur à une page maître de vue. Nous allons examiner deux stratégies pour passer des données à une vue m..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/16/2008
ms.topic: article
ms.assetid: 5fee879b-8bde-42a9-a434-60ba6b1cf747
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/views/passing-data-to-view-master-pages-cs
msc.type: authoredcontent
ms.openlocfilehash: b8bc8ce0690d2e45877be75011d8883facbc74a7
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="passing-data-to-view-master-pages-c"></a>Passage de données à afficher les Pages maîtres (c#)
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le PDF](http://download.microsoft.com/download/e/f/3/ef3f2ff6-7424-48f7-bdaa-180ef64c3490/ASPNET_MVC_Tutorial_13_CS.pdf)

> L’objectif de ce didacticiel est d’expliquer comment vous pouvez passer des données à partir d’un contrôleur à une page maître de vue. Nous allons examiner deux stratégies pour passer des données à une page maître de vue. Tout d’abord, nous abordons une solution simple qui résulte dans une application qui est difficile à maintenir. Nous allons examiner une bien meilleure solution qui requiert un peu plus de travail initial, mais les résultats dans une application beaucoup plus facile à gérer.


## <a name="passing-data-to-view-master-pages"></a>Passage de données à afficher les Pages maîtres

L’objectif de ce didacticiel est d’expliquer comment vous pouvez passer des données à partir d’un contrôleur à une page maître de vue. Nous allons examiner deux stratégies pour passer des données à une page maître de vue. Tout d’abord, nous abordons une solution simple qui résulte dans une application qui est difficile à maintenir. Nous allons examiner une bien meilleure solution qui requiert un peu plus de travail initial, mais les résultats dans une application beaucoup plus facile à gérer.

### <a name="the-problem"></a>Le problème

Imaginez que vous générez une application de base de données de film et que vous souhaitez afficher la liste des catégories de film sur chaque page de votre application (voir Figure 1). En outre, imaginez que la liste des catégories de film est stockée dans une table de base de données. Dans ce cas, il serait logique pour récupérer les catégories à partir de la base de données et de restituer la liste des catégories de film au sein d’une page maître de vue.


[![Affichage des catégories de films dans une page maître de vue](passing-data-to-view-master-pages-cs/_static/image2.png)](passing-data-to-view-master-pages-cs/_static/image1.png)

**Figure 01**: affichage des catégories de films dans une page maître de vue ([cliquez pour afficher l’image en taille réelle](passing-data-to-view-master-pages-cs/_static/image3.png))


Voici le problème. Comment pour récupérer la liste des catégories de film dans la page maître ? Il est tentant d’appeler directement les méthodes de vos classes de modèle dans la page maître. En d’autres termes, il est tentant d’inclure le code de récupération des données à partir de la droite de la base de données dans votre page maître. Toutefois, en ignorant vos contrôleurs MVC pour accéder à la base de données enfreindrait la séparation claire des préoccupations qui est un des principaux avantages de la création d’une application MVC.

Dans une application MVC, vous voulez que toutes les interactions entre les vues MVC et votre modèle MVC d’être gérée par vos contrôleurs MVC. Cette séparation des préoccupations des résultats dans une application plus facile à gérer, testable et adaptable.

Dans une application MVC, toutes les données passées à une vue, y compris une page maître de vue, doivent être passées à une vue par une action de contrôleur. En outre, les données doivent être passées en tirant parti des données d’affichage. Dans le reste de ce didacticiel, j’examine deux méthodes permettent de passer des données de la vue à une page maître de vue.

### <a name="the-simple-solution"></a>La Solution Simple

Commençons par la solution la plus simple pour passer des données de la vue à partir d’un contrôleur à une page maître de vue. La solution la plus simple consiste à passer des données d’affichage de la page maître dans chaque action du contrôleur.

Envisagez le contrôleur dans la liste 1. Il expose deux actions nommées `Index()` et `Details()`. Le `Index()` méthode d’action retourne chaque projet dans la table de base de données de films. Le `Details()` méthode d’action retourne chaque projet dans une catégorie particulière de film.

**La liste 1 :`Controllers\HomeController.cs`**

[!code-csharp[Main](passing-data-to-view-master-pages-cs/samples/sample1.cs)]

Notez que le Index() et les actions Details() ajouter deux éléments pour afficher les données. L’action Index() ajoute deux clés : catégories et des films. La clé de catégories représente la liste des catégories de film affiché par la page maître de vue. La clé de films représente la liste de films affiché par la page Vue d’Index.

L’action Details() ajoute également les deux clés nommé catégories et des films. La clé de catégories, représente une fois encore, la liste des catégories de film affiché par la page maître de vue. La clé de films représente la liste de films dans une catégorie particulière, affiché par la page de vue Détails (voir Figure 2).


[![L’affichage des détails](passing-data-to-view-master-pages-cs/_static/image5.png)](passing-data-to-view-master-pages-cs/_static/image4.png)

**Figure 02**: afficher les détails ([cliquez pour afficher l’image en taille réelle](passing-data-to-view-master-pages-cs/_static/image6.png))


La vue Index se trouve dans la liste 2. Il parcourt simplement la liste de films représenté par l’élément de films dans les données d’affichage.

**Liste 2 :`Views\Home\Index.aspx`**

[!code-aspx[Main](passing-data-to-view-master-pages-cs/samples/sample2.aspx)]

L’affichage de page maître est contenue dans la liste 3. La page maître de vue effectue une itération et restitue toutes les catégories de film représentés par l’élément de catégories à partir des données de la vue.

**La liste 3 :`Views\Shared\Site.master`**

[!code-aspx[Main](passing-data-to-view-master-pages-cs/samples/sample3.aspx)]

Toutes les données est passé à l’affichage et la page maître de vue des données d’affichage. Qui est la méthode correcte pour passer des données à la page maître.

Par conséquent, quel est le problème avec cette solution ? Le problème est que cette solution viole le principe de sec (ne pas se répéter). Chaque action du contrôleur doit ajouter la très même liste de catégories de film pour afficher les données. Des doublons de code dans votre application, votre application beaucoup plus difficile à gérer, d’adapter et de modifier.

### <a name="the-good-solution"></a>La Solution appropriée

Dans cette section, nous examinons une solution de remplacement et mieux, pour passer des données à partir d’une action de contrôleur à une page maître de vue. Au lieu d’ajouter les catégories de film pour la page maître de chaque action du contrôleur, nous ajoutons les catégories de film aux données d’affichage qu’une seule fois. Toutes les données d’affichage utilisées par la page maître de vue est ajouté dans un contrôleur de l’Application.

La classe ApplicationController est contenue dans la liste 4.

**La liste 4 –`Controllers\ApplicationController.cs`**

[!code-csharp[Main](passing-data-to-view-master-pages-cs/samples/sample4.cs)]

Il existe trois choses que vous devez remarquer sur le contrôleur de l’Application sur la liste 4. Tout d’abord, notez que la classe hérite de la classe de base System.Web.Mvc.Controller. Le contrôleur de l’Application est une classe de contrôleur.

En second lieu, notez que la classe de contrôleur d’Application est une classe abstraite. Une classe abstraite est une classe qui doit être implémentée par une classe concrète. Parce que le contrôleur de l’Application est une classe abstraite, vous ne pouvez pas appeler pas toutes les méthodes définies directement dans la classe. Si vous essayez d’appeler directement de la classe d’Application vous obtenez un message d’erreur ressource est introuvable.

Enfin, notez que le contrôleur de l’Application contient un constructeur qui ajoute la liste des catégories de film pour afficher les données. Chaque classe de contrôleur qui hérite de contrôleur d’Application appelle automatiquement les constructeur du contrôleur de l’Application. Chaque fois que vous appelez n’importe quelle action sur n’importe quel contrôleur qui hérite de contrôleur d’Application, les catégories de film est automatiquement inclus dans les données d’affichage.

Le contrôleur de films de liste 5 hérite de contrôleur d’Application.

**La liste 5 :`Controllers\MoviesController.cs`**

[!code-csharp[Main](passing-data-to-view-master-pages-cs/samples/sample5.cs)]

Le contrôleur de films, tout comme le contrôleur Home présenté dans la section précédente, expose deux méthodes d’action `Index()` et `Details()`. Notez que la liste des catégories de film affiché par la page maître de vue n’est pas ajouté à afficher des données dans le `Index()` ou `Details()` (méthode). Étant donné que le contrôleur de films hérite de contrôleur d’Application, la liste des catégories de film est ajoutée pour afficher les données automatiquement.

Notez que cette solution à l’ajout de données d’affichage pour une page maître de vue ne viole pas le principe sec (ne pas se répéter). Le code pour ajouter la liste des catégories de film pour afficher des données est contenu dans un seul emplacement : le constructeur pour le contrôleur de l’Application.

### <a name="summary"></a>Résumé

Dans ce didacticiel, nous avons abordé les deux approches pour passer des données de la vue à partir d’un contrôleur à une page maître de vue. Tout d’abord, nous avons examiné simple, mais difficile à maintenir l’approche. Dans la première section, nous avons indiqué comment vous pouvez ajouter des données d’affichage pour une page maître de vue dans chaque chaque action du contrôleur dans votre application. Nous avons conclu qu’il s’agissait d’une mauvaise approche car elle ne respecte pas le principe sec (ne pas se répéter).

Ensuite, nous avons examiné une bien meilleure stratégie pour l’ajout de données requises par une page maître de vue pour afficher les données. Au lieu d’ajouter les données d’affichage de chaque action du contrôleur, nous avons ajouté les données d’affichage en une seule fois, au sein d’un contrôleur de l’Application. De cette façon, vous pouvez éviter le code en double lors du passage des données à une page maître de vue dans une application ASP.NET MVC.

>[!div class="step-by-step"]
[Précédent](creating-page-layouts-with-view-master-pages-cs.md)
[Suivant](asp-net-mvc-views-overview-vb.md)
