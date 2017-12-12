---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
title: "Afficher les données des éléments et les détails | Documents Microsoft"
author: Erikre
description: "Cette série de didacticiels, vous allez apprendre les principes fondamentaux de la création d’une application Web Forms ASP.NET à l’aide de ASP.NET 4.5 et Microsoft Visual Studio Express 2013 pour nous..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/08/2014
ms.topic: article
ms.assetid: 64a491a8-0ed6-4c2f-9c1c-412962eb6006
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
msc.type: authoredcontent
ms.openlocfilehash: 809d7a9c21a3ddf5dfd07d079eb8fe0d1d81712d
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="display-data-items-and-details"></a>Afficher les données des éléments et les détails
====================
Par [Erik Reitan](https://github.com/Erikre)

[Télécharger Wingtip Toys exemple de projet (c#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) ou [télécharger des livres (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)

> Cette série de didacticiels, vous allez apprendre les principes fondamentaux de la création d’une application Web Forms ASP.NET à l’aide de ASP.NET 4.5 et Microsoft Visual Studio Express 2013 pour le Web. Un Visual Studio 2013 [projet avec le code source c#](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) est disponible pour accompagner cette série de didacticiels.


Ce didacticiel explique comment afficher les éléments de données et les détails d’élément de données à l’aide de Web Forms ASP.NET et Entity Framework Code First. Ce didacticiel s’appuie sur le didacticiel précédent, « L’interface utilisateur et la Navigation » et fait partie de la série de didacticiels Wingtip JOUET magasin. Lorsque vous avez terminé ce didacticiel, vous serez en mesure d’afficher les produits sur le *ProductsList.aspx* page et des détails concernant un produit individuel sur le *ProductDetails.aspx* page.

## <a name="what-youll-learn"></a>Ce que vous allez apprendre :

- Comment ajouter un contrôle de données pour afficher les produits à partir de la base de données.
- Comment connecter un contrôle de données pour les données sélectionnées.
- Comment ajouter un contrôle de données pour afficher des détails sur le produit à partir de la base de données.
- Comment récupérer une valeur de la chaîne de requête et utilisez cette valeur pour limiter les données sont récupérées à partir de la base de données.

### <a name="these-are-the-features-introduced-in-the-tutorial"></a>Voici les fonctionnalités introduites dans le didacticiel :

- Liaison de modèle
- Fournisseurs de valeur

## <a name="adding-a-data-control-to-display-products"></a>Ajout d’un contrôle de données pour afficher des produits

Lors de la liaison de données à un contrôle serveur, il existe quelques options différentes, que vous pouvez utiliser. Les options les plus courantes incluent l’ajout d’un contrôle de source de données, ajouter du code à la main ou à l’aide de la liaison de modèle.

### <a name="using-a-data-source-control-to-bind-data"></a>À l’aide d’un contrôle de Source de données pour lier des données

Ajout d’un contrôle de source de données vous permet de lier le contrôle de source de données au contrôle qui affiche les données. Cette approche vous permet de connecter des contrôles côté serveur déclarative directement à des sources de données, au lieu d’utiliser une approche par programmation.

### <a name="coding-by-hand-to-bind-data"></a>Codage manuellement pour lier des données

Ajout de code à la main implique la lecture de la valeur, recherche d’une valeur null, tente de convertir vers le type approprié, de vérifier si la conversion a réussi et enfin, à l’aide de la valeur dans la requête. Vous utiliserez cette approche lorsque vous avez besoin de conserver un contrôle total sur votre logique d’accès aux données.

### <a name="using-model-binding-to-bind-data"></a>À l’aide de la liaison pour lier des données de modèle

À l’aide de la liaison de modèle vous permet de lier les résultats à l’aide de beaucoup moins de code et vous donne la possibilité de réutiliser les fonctionnalités dans votre application. Liaison de modèle vise pour simplifier l’utilisation de la logique d’accès aux données axée sur le code tout en conservant les avantages d’une infrastructure riche, la liaison de données.

## <a name="displaying-products"></a>Affichage des produits

Dans ce didacticiel, vous utiliserez une liaison de modèle pour lier des données. Pour configurer un contrôle de données pour utiliser la liaison de modèle pour sélectionner les données, vous affectez du contrôle `SelectMethod` nom à la propriété d’une méthode dans le code de la page. Le contrôle de données appelle la méthode au moment approprié dans le cycle de vie de page et lie automatiquement les données retournées. Il n’est pas nécessaire d’appeler explicitement la `DataBind` (méthode).

À l’aide de la procédure suivante, vous allez modifier le balisage dans le *ProductList.aspx* page afin que la page peut afficher les produits.

1. Dans **l’Explorateur de solutions**, ouvrez le *ProductList.aspx* page.
2. Remplacez le balisage existant par le balisage suivant :   

    [!code-aspx[Main](display_data_items_and_details/samples/sample1.aspx)]

Ce code utilise un **ListView** contrôle nommé « productList » pour afficher les produits.

[!code-aspx[Main](display_data_items_and_details/samples/sample2.aspx)]

Le **ListView** contrôle affiche les données dans un format que vous définissez à l’aide de modèles et des styles. Il est utile pour les données dans une structure à répétition. Cela **ListView** montre simplement les données à partir de la base de données, toutefois, vous pouvez activer les utilisateurs à modifier, insérer et supprimer des données et pour trier et les données de la page, tout cela sans code.

En définissant le `ItemType` propriété dans le **ListView** contrôler, l’expression de liaison de données `Item` est disponible et le contrôle devienne fortement typé. Comme indiqué dans le didacticiel précédent, vous pouvez sélectionner les détails de l’objet de l’élément à l’aide d’IntelliSense, telles que la spécification du `ProductName`:

![Afficher les données des éléments et les détails - IntelliSense](display_data_items_and_details/_static/image1.png)

En outre, vous utilisez la liaison de modèle pour spécifier un `SelectMethod` valeur. Cette valeur (`GetProducts`) correspond à la méthode que vous ajouterez du code associé pour afficher les produits à l’étape suivante.

### <a name="adding-code-to-display-products"></a>Ajout d’un Code pour afficher les produits

Dans cette étape, vous ajouterez du code pour remplir le **ListView** contrôle avec des données de produit à partir de la base de données. Le code prendra en charge montrant les produits par catégorie individuelle, en indiquant tous les produits.

1. Dans **l’Explorateur de solutions**, avec le bouton droit *ProductList.aspx* puis cliquez sur **afficher le Code**.
2. Remplacez le code existant dans le *ProductList.aspx.cs* fichier avec le code suivant :   

    [!code-csharp[Main](display_data_items_and_details/samples/sample3.cs)]

Ce code montre le `GetProducts` méthode qui est référencé par le `ItemType` propriété de la **ListView** contrôler dans le *ProductList.aspx* page. Pour limiter les résultats à une catégorie spécifique dans la base de données, le code définit les `categoryId` valeur à partir de la valeur de chaîne de requête passée à la *ProductList.aspx* page lorsque le *ProductList.aspx* page est cible de la navigation. Le `QueryStringAttribute` classe dans le `System.Web.ModelBinding` espace de noms est utilisé pour récupérer la valeur de l’id de variable de chaîne de requête. Cela indique à la liaison de modèle pour essayer de lier une valeur de chaîne de requête à la `categoryId` paramètre au moment de l’exécution.

Lorsqu’une catégorie valide est passée comme une chaîne de requête à la page, les résultats de la requête sont limités à ces produits dans la base de données qui correspondent à la `categoryId` valeur. Par exemple, si l’URL pour le *ProductsList.aspx* page est le suivant :

[!code-console[Main](display_data_items_and_details/samples/sample4.cmd)]

La page affiche uniquement les produits où le `category` est égal à `1`.

Si aucune chaîne de requête n’est inclus lorsque vous naviguerez vers le *ProductList.aspx* page, tous les produits seront affiche.

Les sources de valeurs pour ces méthodes sont appelées *valeur fournisseurs* (tel que *QueryString*), et les attributs de paramètre qui indiquent le fournisseur de valeur à utiliser sont appelés valeur attributs du fournisseur (tel que «`id`»). ASP.NET inclut les fournisseurs de valeurs et des attributs correspondants pour toutes les sources courantes des entrées d’utilisateur dans une application Web Forms, telles que la chaîne de requête, les cookies, valeurs de formulaire, contrôles, état d’affichage, l’état de session et les propriétés de profil. Vous pouvez également écrire des fournisseurs de valeurs personnalisées.

### <a name="running-the-application"></a>Exécution de l'application

Exécutez l’application maintenant pour voir comment vous pouvez afficher tous les produits ou simplement un ensemble de produits limité par catégorie.

1. Dans le **l’Explorateur de solutions**, avec le bouton droit le *Default.aspx* page et sélectionnez **afficher dans le navigateur**.  
 Le navigateur s’ouvre et affiche le *Default.aspx* page.
2. Sélectionnez **voitures** dans le menu de navigation de catégorie de produit.  
 Le *ProductList.aspx* page affiche uniquement les produits inclus dans la catégorie « Voitures ». Plus loin dans ce didacticiel, vous affichez des détails sur le produit.  

    ![Afficher les données des éléments et les détails - voitures](display_data_items_and_details/_static/image2.png)
3. Sélectionnez **produits** à partir du menu de navigation en haut.  
 Là encore, la *ProductList.aspx* page s’affiche, mais cette fois, il affiche la liste complète des produits.   

    ![Afficher les données des éléments et les détails - produits](display_data_items_and_details/_static/image3.png)
4. Fermez le navigateur et revenez à Visual Studio.

### <a name="adding-a-data-control-to-display-product-details"></a>Ajout d’un contrôle de données pour afficher les détails du produit

Ensuite, vous allez modifier le balisage dans le *ProductDetails.aspx* page que vous avez ajouté dans le didacticiel précédent afin que la page peut afficher des informations sur les produits.

1. Dans **l’Explorateur de solutions**, ouvrez le *ProductDetails.aspx* page.
2. Remplacez le balisage existant par le balisage suivant :   

    [!code-aspx[Main](display_data_items_and_details/samples/sample5.aspx)]

Ce code utilise un **FormView** contrôle pour afficher plus d’informations sur un produit individuel. Ce balisage utilise des méthodes, telles que celles qui sont utilisées pour afficher des données dans le *ProductList.aspx* page. Le **FormView** contrôle est utilisé pour afficher un seul enregistrement à la fois à partir d’une source de données. Lorsque vous utilisez la **FormView** contrôle, vous créez des modèles pour afficher et modifier des valeurs liées aux données. Les modèles contiennent des contrôles, liaison d’expressions et mise en forme qui définissent l’apparence et les fonctionnalités du formulaire.

Pour connecter le balisage ci-dessus à la base de données, vous devez ajouter du code pour le *ProductDetails.aspx* code.

1. Dans **l’Explorateur de solutions**, avec le bouton droit *ProductDetails.aspx* puis cliquez sur **afficher le Code**.  
 Le *ProductDetails.aspx.cs* fichier s’affiche.
2. Remplacez le code existant par celui-ci :   

    [!code-csharp[Main](display_data_items_and_details/samples/sample6.cs)]

Ce code vérifie un «`productID`« valeur de chaîne de requête. Si une valeur de chaîne de requête valide est trouvée, le produit correspondant s’affiche. Si aucune chaîne de requête n’est trouvé, ou la valeur de chaîne de requête n’est pas valide, aucun produit ne s’affiche sur le *ProductDetails.aspx* page.

### <a name="running-the-application"></a>Exécution de l'application

Vous pouvez maintenant exécuter l’application pour afficher les produits affichés sont basés sur l’id du produit.

1. Appuyez sur **F5** tandis que dans Visual Studio pour exécuter l’application.  
 Le navigateur s’ouvre et affiche le *Default.aspx* page.
2. Sélectionnez « Bateaux » dans le menu de navigation de catégorie.  
 Le *ProductList.aspx* page s’affiche.
3. Sélectionnez le produit « Livre bateau » à partir de la liste des produits.  
 Le *ProductDetails.aspx* page s’affiche.   

    ![Afficher les données des éléments et les détails - produits](display_data_items_and_details/_static/image4.png)
4. Fermez le navigateur.

## <a name="summary"></a>Résumé

Dans ce didacticiel de la série ont ajouter du code pour afficher une liste de produits et pour afficher des détails sur le produit et balisage. Pendant ce processus, vous avez appris à propos des contrôles de données fortement typées, liaison de modèle et les fournisseurs de valeur. Dans l’étape suivante du didacticiel, vous allez ajouter un panier d’achat pour l’exemple d’application Wingtip Toys.

## <a name="additional-resources"></a>Ressources supplémentaires

[La récupération et affichage des données avec la liaison de modèle et les web forms](../../presenting-and-managing-data/model-binding/retrieving-data.md)

>[!div class="step-by-step"]
[Précédent](ui_and_navigation.md)
[Suivant](shopping-cart.md)
