---
uid: mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-8
title: "Partie 8 : Panier d’achat avec les mises à jour Ajax | Documents Microsoft"
author: jongalloway
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 8 couvre le panier d’achat avec les mises à jour Ajax."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/21/2011
ms.topic: article
ms.assetid: 26b2f55e-ed42-4277-89b0-c941eb754145
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-8
msc.type: authoredcontent
ms.openlocfilehash: 75e1dff96f8b56d74c28ff9d522f4766fbad669f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-8-shopping-cart-with-ajax-updates"></a>Partie 8 : Panier avec les mises à jour Ajax
====================
par [Jon Galloway](https://github.com/jongalloway)

> Le magasin de musique MVC est une application du didacticiel qui présente et explique étape par étape comment utiliser ASP.NET MVC et Visual Studio pour le développement web.  
>   
> Le magasin de musique MVC est une implémentation de magasin exemple léger qui vend des albums de musique en ligne et implémente l’administration de site de base, authentification de l’utilisateur et les fonctionnalités de panier d’achat.  
>   
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 8 couvre le panier d’achat avec les mises à jour Ajax.


Nous allons permettre aux utilisateurs de placer des albums dans leur panier d’achat sans l’enregistrer, mais elles devrez inscrire en tant qu’invités à l’extraction terminée. Le processus d’achat et d’extraction est divisée en deux contrôleurs : un contrôleur ShoppingCart, ce qui permet l’ajout d’éléments de façon anonyme dans un panier d’achat et un contrôleur d’extraction qui gère le processus de validation. Nous allons commencer par le panier d’achat dans cette section, puis générer le processus d’extraction dans la section suivante.

## <a name="adding-the-cart-order-and-orderdetail-model-classes"></a>Ajout des classes de modèle de panier, Order et OrderDetail

Nos processus panier d’achat et d’extraction permettront à utiliser de certaines nouvelles classes. Cliquez sur le dossier Modèles et ajouter une classe de panier (Cart.cs) avec le code suivant.

[!code-csharp[Main](mvc-music-store-part-8/samples/sample1.cs)]

Cette classe est très similaire à d’autres personnes que nous avons utilisé jusqu'à présent, à l’exception de l’attribut [clé] pour la propriété RecordId. Les éléments de panier aura un identificateur de chaîne nommé CartID pour autoriser les achats anonymes, mais la table comprend une clé primaire de type entier nommée RecordId. Par convention, Entity Framework Code-First attend que la clé primaire pour une table nommée panier sera CartId ou ID, mais nous pouvons facilement remplacer que via des annotations ou de code si vous souhaitez. Il s’agit d’un exemple de comment nous pouvons utiliser les conventions simples dans Entity Framework Code-First lorsqu’ils conviennent à nous, mais nous n’avons pas contraint par les n’en ont pas.

Ensuite, ajoutez une classe Order (Order.cs) avec le code suivant.

[!code-csharp[Main](mvc-music-store-part-8/samples/sample2.cs)]

Cette classe effectue le suivi des informations de résumé et de livraison d’une commande. **Il ne sera pas compilé encore**, car il a une propriété de navigation OrderDetails qui dépend d’une classe que nous n’avons pas encore créé. Nous allons résoudre maintenant en ajoutant une classe nommée OrderDetail.cs, en ajoutant le code suivant.

[!code-csharp[Main](mvc-music-store-part-8/samples/sample3.cs)]

Nous allons effectuer une dernière mise à jour à la classe pour inclure DbSets qui exposent ces nouvelles classes de modèle, en incluant un DbSet MusicStoreEntities&lt;artiste&gt;. La classe MusicStoreEntities mis à jour apparaît comme ci-dessous.

[!code-csharp[Main](mvc-music-store-part-8/samples/sample4.cs)]

## <a name="managing-the-shopping-cart-business-logic"></a>La gestion de la logique métier de panier d’achat

Ensuite, nous allons créer la classe ShoppingCart dans le dossier de modèles. Le modèle ShoppingCart gère l’accès des données à la table de panier. En outre, il gère la logique métier pour l’ajout et suppression d’éléments de panier d’achat.

Étant donné que nous ne souhaitez pas demander aux utilisateurs de s’inscrire à un compte simplement à ajouter des éléments à leur panier d’achat, nous assignons utilisateurs un identificateur unique temporaire (à l’aide d’un GUID ou un identificateur global unique) lorsqu’ils accèdent au panier d’achat. Nous allons stocker cet ID à l’aide de la classe Session ASP.NET.

*Remarque : La Session ASP.NET est un emplacement pratique pour stocker des informations spécifiques à l’utilisateur expire après le site. Lors de l’utilisation incorrecte de l’état de session peut avoir des implications en matière de performances sur les sites de grande taille, nous utilisons clair fonctionnera également à des fins de démonstration.*

La classe ShoppingCart expose les méthodes suivantes :

**AddToCart** prend un Album en tant que paramètre et l’ajoute au panier de l’utilisateur. Étant donné que la table de panier effectue le suivi de la quantité pour chaque album, il inclut une logique pour créer une nouvelle ligne, si nécessaire, ou simplement incrémenter la quantité si l’utilisateur a déjà commandé une copie de l’album.

**RemoveFromCart** accepte un ID de l’Album et le supprime de panier de l’utilisateur. Si l’utilisateur a uniquement une copie de l’album dans leur panier d’achat, la ligne est supprimée.

**EmptyCart** supprime tous les éléments de panier d’achat d’un utilisateur.

**GetCartItems** récupère une liste de CartItems pour l’affichage ou le traitement.

**GetCount** récupère un nombre total d’albums d’un utilisateur a dans leur panier d’achat.

**GetTotal** calcule le coût total de tous les éléments dans le panier d’achat.

**CreateOrder** convertit le panier d’achat à une commande lors de la phase d’extraction.

**GetCart** est une méthode statique qui permet de nos contrôleurs obtenir un objet panier d’achat. Elle utilise le **GetCartId** méthode pour gérer le CartId lors de la lecture à partir de la session utilisateur. La méthode GetCartId requiert HttpContextBase afin qu’il puisse lire CartId de l’utilisateur à partir de la session de l’utilisateur.

Voici l’ensemble **ShoppingCart classe**:

[!code-csharp[Main](mvc-music-store-part-8/samples/sample5.cs)]

## <a name="viewmodels"></a>ViewModel

Notre contrôleur de panier d’achat doit communiquer des informations complexes à son point de vue qui n’est pas mappé correctement pour les objets de modèle. Nous ne voulons pas modifier les modèles en fonction de nos vues ; Classes de modèle doivent représenter le domaine, et pas l’interface utilisateur. Une solution consisterait à passer les informations à notre affichages à l’aide de la classe ViewBag, comme nous l’avons fait avec les informations de la liste déroulante directeur du magasin, mais en passant un grand nombre d’informations via ViewBag obtient difficile à gérer.

Une solution à ce problème consiste à utiliser le *ViewModel* modèle. Lors de l’utilisation de ce modèle, nous créons des classes fortement typées qui sont optimisés pour nos scénarios d’affichage spécifiques, et qui expose les propriétés pour le valeurs/contenu dynamique nécessaire par nos modèles de vue. Nos classes de contrôleur peuvent remplir, puis passer ces classes optimisées en vue de notre modèle d’affichage à utiliser. Cela permet la sécurité de type, la vérification de la compilation et IntelliSense dans les modèles d’affichage de l’éditeur.

Nous allons créer deux modèles d’affichage pour une utilisation dans notre contrôleur de panier d’achat : le ShoppingCartViewModel contiendra le contenu du panier d’achat de l’utilisateur et le ShoppingCartRemoveViewModel doit servir à afficher des informations de confirmation lorsqu’un utilisateur supprime un élément à partir de leur panier d’achat.

Nous allons créer un nouveau dossier ViewModel dans la racine de votre projet pour organiser les éléments. Cliquez sur le projet, sélectionnez Ajouter / nouveau dossier.

![](mvc-music-store-part-8/_static/image1.jpg)

Nommez le dossier ViewModel.

![](mvc-music-store-part-8/_static/image1.png)

Ensuite, ajoutez la classe ShoppingCartViewModel dans le dossier ViewModel. Il a deux propriétés : une liste des éléments de panier et une valeur décimale pour contenir le prix total de tous les éléments dans le panier d’achat.

[!code-csharp[Main](mvc-music-store-part-8/samples/sample6.cs)]

Maintenant, ajoutez le ShoppingCartRemoveViewModel au dossier ViewModel, avec les quatre propriétés suivantes.

[!code-csharp[Main](mvc-music-store-part-8/samples/sample7.cs)]

## <a name="the-shopping-cart-controller"></a>Le contrôleur de panier d’achat

Le contrôleur de panier d’achat a trois objectifs principaux : ajout d’éléments dans un panier d’achat et supprimer des éléments du panier d’affichage des éléments dans le panier d’achat. Il effectue l’utilisation des trois classes nous venez : ShoppingCartViewModel, ShoppingCartRemoveViewModel et ShoppingCart. Comme dans les StoreController et les StoreManagerController, nous allons ajouter un champ pour stocker une instance de MusicStoreEntities.

Ajoutez un nouveau contrôleur de panier d’achat pour le projet en utilisant le modèle de contrôleur vide.

![](mvc-music-store-part-8/_static/image2.png)

Voici le contrôleur ShoppingCart terminée. Les actions d’Index et d’ajouter un contrôleur doivent ressembler très familiers. Les actions de contrôleur Remove et CartSummary gérer les deux cas spéciaux, nous aborderons dans la section suivante.

[!code-csharp[Main](mvc-music-store-part-8/samples/sample8.cs)]

## <a name="ajax-updates-with-jquery"></a>Mises à jour AJAX avec jQuery

Nous allons ensuite créer une page d’Index de panier d’achat qui est fortement typée pour le ShoppingCartViewModel et utilise le modèle d’affichage de liste à l’aide de la même méthode qu’avant.

![](mvc-music-store-part-8/_static/image3.png)

Toutefois, au lieu d’utiliser un Html.ActionLink pour supprimer des éléments du panier, nous allons utiliser jQuery pour « rattacher » l’événement click pour tous les liens dans cette vue ayant la classe HTML RemoveLink. Plutôt que de la validation de l’écran, ce gestionnaire d’événements click rend simplement un rappel AJAX à notre action de contrôleur RemoveFromCart. La RemoveFromCart retourne un résultat sérialisé au format JSON, qui notre rappel jQuery, puis analyse et effectue quatre mises à jour rapides de la page à l’aide de jQuery :

- 1. Supprime l’album supprimé de la liste
- 2. Met à jour le nombre de panier dans l’en-tête
- 3. Affiche un message de mise à jour à l’utilisateur
- 4. Met à jour le prix total de panier

Étant donné que le scénario de suppression est traitée par un rappel Ajax au sein de la vue de l’Index, nous n’avez pas besoin une vue supplémentaire pour l’action de RemoveFromCart. Voici le code complet pour la vue /ShoppingCart/Index :

[!code-cshtml[Main](mvc-music-store-part-8/samples/sample9.cshtml)]

Pour tester, nous devons être en mesure d’ajouter des éléments à notre panier d’achat. Nous allons mettre à jour notre **détails du magasin** vue à inclure un bouton « Ajouter au panier ». Pendant que nous sommes à elle, nous pouvons inclure certaines informations de l’Album supplémentaires qui nous avons ajouté, car nous avons mis à jour cette vue : Genre, artiste, prix et pochette. Le code de vue Détails de la banque d’informations mis à jour s’affiche comme illustré ci-dessous.

[!code-cshtml[Main](mvc-music-store-part-8/samples/sample10.cshtml)]

Nous pouvons maintenant parcourez le magasin et tester l’ajout et la suppression des Albums vers et à partir de notre panier d’achat. Exécutez l’application et accédez à l’Index.

![](mvc-music-store-part-8/_static/image4.png)

Ensuite, cliquez sur un Genre pour afficher la liste des albums.

![](mvc-music-store-part-8/_static/image5.png)

Cliquant sur un titre d’Album maintenant montre notre vue Détails de l’Album mis à jour, y compris le bouton « Ajouter au panier ».

![](mvc-music-store-part-8/_static/image6.png)

En cliquant sur le bouton « Ajouter au panier » affiche ses Index de panier d’achat avec la liste de résumé du panier d’achat.

![](mvc-music-store-part-8/_static/image7.png)

Après le chargement de votre panier d’achat, vous pouvez cliquer sur la suppression à partir du lien du panier pour voir la mise à jour Ajax à votre panier d’achat.

![](mvc-music-store-part-8/_static/image8.png)

Nous avons mis au point un panier d’achat qui permet d’annuler l’inscription des utilisateurs ajouter des éléments dans leur panier d’achat en cours. Dans la section suivante, nous allons les autoriser à inscrire et terminer le processus de validation.


>[!div class="step-by-step"]
[Précédent](mvc-music-store-part-7.md)
[Suivant](mvc-music-store-part-9.md)
