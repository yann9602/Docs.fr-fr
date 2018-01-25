---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/shopping-cart
title: "Panier d’achat | Documents Microsoft"
author: Erikre
description: "Cette série de didacticiels, vous allez apprendre les principes fondamentaux de la création d’une application Web Forms ASP.NET à l’aide de ASP.NET 4.5 et Microsoft Visual Studio Express 2013 pour nous..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/08/2014
ms.topic: article
ms.assetid: 6898c601-6c31-432f-8388-e6843f8a17cb
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/shopping-cart
msc.type: authoredcontent
ms.openlocfilehash: 9fe6f28685d6a423b03f9c7abe753283b89344e1
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="shopping-cart"></a>Panier d’achat
====================
Par [Erik Reitan](https://github.com/Erikre)

[Télécharger Wingtip Toys exemple de projet (c#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) ou [télécharger des livres (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)

> Cette série de didacticiels, vous allez apprendre les principes fondamentaux de la création d’une application Web Forms ASP.NET à l’aide de ASP.NET 4.5 et Microsoft Visual Studio Express 2013 pour le Web. Un Visual Studio 2013 [projet avec le code source c#](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) est disponible pour accompagner cette série de didacticiels.


Ce didacticiel décrit la logique métier nécessaire pour ajouter un panier d’achat à l’application de Web Forms ASP.NET exemple Wingtip Toys. Ce didacticiel s’appuie sur le didacticiel précédent « Affichage des éléments et détails de données » et fait partie de la série de didacticiels Wingtip JOUET magasin. Lorsque vous avez terminé ce didacticiel, les utilisateurs de votre exemple d’application sera en mesure de les ajouter, supprimer et modifier les produits dans leur panier d’achat.

## <a name="what-youll-learn"></a>Ce que vous allez apprendre :

1. Comment créer un panier d’achat pour l’application web.
2. Comment permettre aux utilisateurs d’ajouter des éléments au panier d’achat.
3. Comment ajouter un [GridView](https://msdn.microsoft.com/library/system.web.ui.webcontrols.gridview(v=vs.110).aspx#introduction) contrôle pour afficher les détails du panier d’achat.
4. Comment calculer et afficher le total de la commande.
5. Comment supprimer et mettre à jour des éléments dans le panier d’achat.
6. Explique comment inclure un compteur de panier d’achat.

## <a name="code-features-in-this-tutorial"></a>Fonctionnalités de code dans ce didacticiel :

1. Entity Framework Code First
2. Annotations de données
3. Fortement typée les contrôles de données
4. Liaison de modèle

## <a name="creating-a-shopping-cart"></a>Création d’un panier d’achat

Plus haut dans cette série de didacticiels, vous avez ajouté des pages et le code pour afficher les données de produit à partir d’une base de données. Dans ce didacticiel, vous allez créer un panier d’achat pour gérer les produits que les utilisateurs intéressés par l’achat. Les utilisateurs pourront parcourir et ajouter des éléments au panier d’achat même s’ils ne sont pas inscrits ou connectés. Pour gérer l’accès de panier d’achat, vous allez attribuer aux utilisateurs une unique `ID` à l’aide d’un identificateur global unique (GUID) lorsque l’utilisateur accède à l’analyse de panier pour la première fois. Vous allez enregistrer cet `ID` à l’aide de l’état de Session ASP.NET.

> [!NOTE] 
> 
> L’état de Session ASP.NET est un emplacement pratique pour stocker des informations spécifiques à l’utilisateur expire une fois que l’utilisateur quitte le site. Lors de l’utilisation incorrecte de l’état de session peut avoir des implications en matière de performances sur les sites de grande taille, clair l’utilisation de la session de l’état fonctionne correctement à des fins de démonstration. L’exemple de projet Wingtip Toys montre comment utiliser l’état de session sans un fournisseur externe, où l’état de session est stockée in-process sur le serveur web qui héberge le site. Pour les sites de grande taille qui fournissent plusieurs instances d’une application ou pour les sites qui exécutent plusieurs instances d’une application sur différents serveurs, envisagez d’utiliser **Service de Cache de Windows Azure**. Ce Service de Cache fournit un service de mise en cache distribué qui est le site web externe et résout le problème de l’état de session in-process. Pour plus d’informations, consultez [l’état de Session ASP.NET avec les Sites Web Windows Azure](https://docs.microsoft.com/azure/redis-cache/cache-aspnet-session-state-provider).


### <a name="add-cartitem-as-a-model-class"></a>Ajouter CartItem comme une classe de modèle

Plus haut dans cette série de didacticiels, vous défini le schéma pour les données de catégorie et de produit en créant le `Category` et `Product` des classes dans le *modèles* dossier. Maintenant, ajoutez une nouvelle classe pour définir le schéma pour le panier d’achat. Plus loin dans ce didacticiel, vous allez ajouter une classe pour gérer l’accès aux données le `CartItem` table. Cette classe fournit la logique métier pour ajouter, supprimer et mettre à jour des éléments dans le panier d’achat.

1. Avec le bouton droit le *modèles* et sélectionnez **ajouter**  - &gt; **un nouvel élément**. 

    ![Panier d’achat - nouvel élément](shopping-cart/_static/image1.png)
2. La boîte de dialogue **Ajouter un nouvel élément** s’affiche. Sélectionnez **Code**, puis sélectionnez **classe**. 

    ![Panier - ajouter la boîte de dialogue Nouvel élément](shopping-cart/_static/image2.png)
3. Nommez cette nouvelle classe *CartItem.cs*.
4. Cliquez sur **Ajouter**.  
 Le nouveau fichier de classe s’affiche dans l’éditeur.
5. Remplacez le code par défaut par le code suivant :   

    [!code-csharp[Main](shopping-cart/samples/sample1.cs)]

La `CartItem` classe contient le schéma qui définit chaque produit un utilisateur ajoute dans le panier d’achat. Cette classe est semblable à d’autres classes de schéma que vous avez créé plus haut dans cette série de didacticiels. Par convention, Entity Framework Code First s’attend que la clé primaire de la `CartItem` table sera `CartItemId` ou `ID`. Toutefois, le code substitue le comportement par défaut à l’aide de l’annotation de données `[Key]` attribut. Le `Key` attribut de la propriété ItemId Spécifie que le `ItemID` propriété est la clé primaire.

Le `CartId` propriété spécifie le `ID` de l’utilisateur qui est associé à l’article à acheter. Vous ajouterez du code pour créer l’utilisateur `ID` lorsque l’utilisateur accède au panier d’achat. Cela `ID` seront également stockés comme une variable de Session ASP.NET.

### <a name="update-the-product-context"></a>Mettre à jour le contexte de produit

En plus d’ajouter le `CartItem` (classe), vous devez mettre à jour de la classe de contexte de base de données qui gère les classes d’entité et qui fournit l’accès à la base de données. Pour ce faire, vous allez ajouter nouvellement créé `CartItem` à la classe de modèle la `ProductContext` classe.

1. Dans **l’Explorateur de solutions**, recherchez et ouvrez le *ProductContext.cs* de fichiers dans le *modèles* dossier.
2. Ajoutez le code en surbrillance à le *ProductContext.cs* de fichiers comme suit :  

    [!code-csharp[Main](shopping-cart/samples/sample2.cs?highlight=14)]

Comme mentionné précédemment dans cette série de didacticiels, le code dans le *ProductContext.cs* fichier ajoute le `System.Data.Entity` espace de noms afin que vous ayez accès à toutes les fonctionnalités principales d’Entity Framework. Cette fonctionnalité inclut la possibilité d’interroger, insérer, mettre à jour et supprimer des données à l’aide des objets fortement typés. Le `ProductContext` classe ajoute l’accès à récemment ajouté `CartItem` classe de modèle.

### <a name="managing-the-shopping-cart-business-logic"></a>La gestion de la logique métier de panier d’achat

Ensuite, vous allez créer le `ShoppingCart` classe dans une nouvelle *logique* dossier. Le `ShoppingCart` classe gère l’accès aux données le `CartItem` table. La classe inclut également la logique métier pour ajouter, supprimer et mettre à jour des éléments dans le panier d’achat.

Contient la logique de panier d’achat que vous allez ajouter les fonctionnalités permettant de gérer les actions suivantes :

1. Ajout d’éléments dans le panier d’achat
2. Suppression d’éléments de panier d’achat
3. Obtention de l’ID du caddie
4. Récupération des éléments depuis le panier d’achat
5. Total de la quantité de tous les éléments du panier d’achat
6. Mise à jour les données de panier d’achat

Une page de panier d’achat (*ShoppingCart.aspx*) et la classe de panier d’achat est utilisée ensemble pour accéder aux données de panier d’achat. La page de panier d’achat affiche tous les éléments de que l’utilisateur ajoute au panier d’achat. Outre l’analyse de panier page et la classe, vous allez créer une page (*AddToCart.aspx*) pour ajouter des produits dans le panier d’achat. Vous allez également ajouter le code pour le *ProductList.aspx* page et le *ProductDetails.aspx* page qui fournit un lien vers le *AddToCart.aspx* page, afin que l’utilisateur peut ajouter produits dans le panier d’achat.

Le diagramme suivant illustre le processus de base qui se produit lorsque l’utilisateur ajoute un produit dans le panier d’achat.

![Panier - ajouter au panier d’achat](shopping-cart/_static/image3.png)

Lorsque l’utilisateur clique sur le **Add To Cart** lien soit le *ProductList.aspx* page ou le *ProductDetails.aspx* page, l’application permet d’accéder à la *AddToCart.aspx* page, puis automatiquement à la *ShoppingCart.aspx* page. Le *AddToCart.aspx* page ajoutera le produit dans le panier d’achat en appelant une méthode dans la classe ShoppingCart. Le *ShoppingCart.aspx* page affiche les produits qui ont été ajoutés dans le panier d’achat.

#### <a name="creating-the-shopping-cart-class"></a>Création de la classe de panier d’achat

La `ShoppingCart` classe est ajoutée à un dossier distinct dans l’application afin qu’il y aura une distinction claire entre le modèle (dossier de modèles), les pages (dossier racine) et la logique (dossier logique).

1. Dans **l’Explorateur de solutions**, avec le bouton droit le **WingtipToys**de projet et sélectionnez **ajouter**-&gt;**nouveau dossier**. Nommez le nouveau dossier *logique*.
2. Avec le bouton droit le *logique* dossier, puis sélectionnez **ajouter**  - &gt; **un nouvel élément**.
3. Ajouter un nouveau fichier de classe nommé *ShoppingCartActions.cs*.
4. Remplacez le code par défaut par le code suivant :   

    [!code-csharp[Main](shopping-cart/samples/sample3.cs)]

Le `AddToCart` méthode permet à des produits individuels à inclure dans le panier d’achat en fonction du produit `ID`. Le produit est ajouté au panier d’achat, ou si le panier contient déjà un élément de ce produit, la quantité est incrémentée.

Le `GetCartId` méthode retourne le panier `ID` pour l’utilisateur. Le panier `ID` est utilisé pour suivre les éléments dont dispose un utilisateur dans leur panier d’achat. Si l’utilisateur ne dispose pas d’un panier existant `ID`, un panier d’achat nouvelle `ID` est créée pour eux. Si l’utilisateur est connecté en tant qu’un utilisateur enregistré, le panier `ID` est défini sur son nom d’utilisateur. Toutefois, si l’utilisateur n’est pas signé dans le panier `ID` est définie sur une valeur unique (GUID). Un GUID garantit que seul panier d’achat est créé pour chaque utilisateur, en fonction de la session.

Le `GetCartItems` méthode retourne une liste des articles du panier pour l’utilisateur. Plus loin dans ce didacticiel, vous verrez que la liaison de modèle est utilisée pour afficher les éléments de panier dans le panier d’achat à l’aide du `GetCartItems` (méthode).

### <a name="creating-the-add-to-cart-functionality"></a>Création de la fonctionnalité Ajouter au panier

Comme mentionné précédemment, vous allez créer une page de traitement nommée *AddToCart.aspx* qui servira à ajouter de nouveaux produits dans le panier d’achat de l’utilisateur. Cette page appellera la `AddToCart` méthode dans la `ShoppingCart` classe que vous venez de créer. Le *AddToCart.aspx* page qui attend un produit `ID` lui est transmise. Ce produit `ID` sera utilisé lors de l’appel du `AddToCart` méthode dans la `ShoppingCart` classe.

> [!NOTE] 
> 
> Vous allez modifier le code-behind (*AddToCart.aspx.cs*) de cette page, et non la page de l’interface utilisateur (*AddToCart.aspx*).


#### <a name="to-create-the-add-to-cart-functionality"></a>Pour créer l’ajouter au panier fonctionnalités :

1. Dans **l’Explorateur de solutions**, avec le bouton droit le **WingtipToys**de projet, cliquez sur **ajouter**  - &gt; **un nouvel élément**.  
 La boîte de dialogue **Ajouter un nouvel élément** s’affiche.
2. Ajouter une nouvelle page standard (formulaire Web) pour l’application nommée *AddToCart.aspx*. 

    ![Panier - ajouter le formulaire Web](shopping-cart/_static/image4.png)
3. Dans **l’Explorateur de solutions**, cliquez sur le *AddToCart.aspx* page, puis cliquez sur **afficher le Code**. Le *AddToCart.aspx.cs* fichier code-behind est ouvert dans l’éditeur.
4. Remplacez le code existant dans le *AddToCart.aspx.cs* code-behind par le code suivant :   

    [!code-csharp[Main](shopping-cart/samples/sample4.cs)]

Lorsque le *AddToCart.aspx* page est chargée, le produit `ID` sont récupérées à partir de la chaîne de requête. Ensuite, une instance de la classe de panier d’achat est créée et utilisée pour appeler le `AddToCart` méthode que vous avez ajouté précédemment dans ce didacticiel. Le `AddToCart` méthode, contenu dans le *ShoppingCartActions.cs* de fichiers, inclut la logique pour ajouter le produit sélectionné dans le panier d’achat ou augmenter la quantité de produit du produit sélectionné. Si le produit n’a pas été ajouté au panier d’achat, le produit est ajouté à la `CartItem` table de la base de données. Si le produit a déjà été ajouté dans le panier d’achat et l’utilisateur ajoute un élément supplémentaire du même produit, la quantité du produit est incrémentée de la `CartItem` table. Enfin, la page redirige vers le *ShoppingCart.aspx* page que vous ajouterez dans l’étape suivante, où l’utilisateur voit une liste actualisée des éléments dans le panier d’achat.

Comme mentionné précédemment, un utilisateur `ID` est utilisé pour identifier les produits qui sont associés à un utilisateur spécifique. Cela `ID` est ajouté à une ligne dans le `CartItem` chaque fois que l’utilisateur ajoute un produit dans le panier d’achat de la table.

### <a name="creating-the-shopping-cart-ui"></a>Création de l’interface utilisateur de panier d’achat

Le *ShoppingCart.aspx* page affiche les produits dont l’utilisateur a ajouté à leur panier d’achat. Elle fournit également la possibilité d’ajouter, supprimer et mettre à jour des éléments dans le panier d’achat.

1. Dans **l’Explorateur de solutions**, avec le bouton droit **WingtipToys**, cliquez sur **ajouter**  - &gt; **un nouvel élément**.  
 La boîte de dialogue **Ajouter un nouvel élément** s’affiche.
2. Ajouter une nouvelle page (formulaire Web) qui inclut une page maître en sélectionnant **Web Form avec Page maître**. Nommez la nouvelle page *ShoppingCart.aspx*.
3. Sélectionnez **Site.Master** pour attacher la page maître à la nouvelle *.aspx* page.
4. Dans le *ShoppingCart.aspx* page, remplacez la balise existante par le balisage suivant :   

    [!code-aspx[Main](shopping-cart/samples/sample5.aspx)]

Le *ShoppingCart.aspx* page inclut un **GridView** contrôle nommé `CartList`. Ce contrôle utilise la liaison de modèle pour lier les données de panier d’achat à partir de la base de données pour le **GridView** contrôle. Lorsque vous définissez la `ItemType` propriété de la **GridView** contrôler, l’expression de liaison de données `Item` est disponible dans le balisage du contrôle et le contrôle devienne fortement typé. Comme mentionné plus haut dans cette série de didacticiels, vous pouvez sélectionner les détails de la `Item` de l’objet à l’aide d’IntelliSense. Pour configurer un contrôle de données pour utiliser la liaison de modèle pour sélectionner les données, vous définissez le `SelectMethod` propriété du contrôle. Dans le balisage ci-dessus, vous définissez la `SelectMethod` d’utiliser la méthode GetShoppingCartItems qui renvoie une liste de `CartItem` objets. Le **GridView** contrôle de données appelle la méthode au moment approprié dans le cycle de vie de page et lie automatiquement les données retournées. Le `GetShoppingCartItems` méthode doit encore être ajoutée.

#### <a name="retrieving-the-shopping-cart-items"></a>Récupérer les éléments de panier d’achat

Ensuite, vous ajoutez le code à la *ShoppingCart.aspx.cs* code-behind pour extraire et remplir l’interface utilisateur de panier d’achat.

1. Dans **l’Explorateur de solutions**, cliquez sur le *ShoppingCart.aspx* page, puis cliquez sur **afficher le Code**. Le *ShoppingCart.aspx.cs* fichier code-behind est ouvert dans l’éditeur.
2. Remplacez le code existant par le code ci-dessous :  

    [!code-csharp[Main](shopping-cart/samples/sample6.cs)]

Comme indiqué ci-dessus, le `GridView` de contrôle de données appelle la `GetShoppingCartItems` méthode au moment approprié dans la vie de la page cycle et lie automatiquement les données retournées. Le `GetShoppingCartItems` méthode crée une instance de la `ShoppingCartActions` objet. Ensuite, le code utilise cette instance pour retourner les éléments dans le panier d’achat en appelant le `GetCartItems` (méthode).

### <a name="adding-products-to-the-shopping-cart"></a>Ajout de produits dans le panier d’achat

Lorsque soit la *ProductList.aspx* ou *ProductDetails.aspx* page s’affiche, l’utilisateur sera en mesure d’ajouter le produit dans le panier d’achat à l’aide d’un lien. Lorsqu’ils cliquent sur le lien, l’application accède à la page de traitement nommée *AddToCart.aspx*. Le *AddToCart.aspx* page appellera la `AddToCart` méthode dans la `ShoppingCart` classe que vous avez ajouté précédemment dans ce didacticiel.

À présent, vous allez ajouter un **ajouter au panier** lien à la fois à la *ProductList.aspx* page et le *ProductDetails.aspx* page. Ce lien inclut le produit `ID` qui est récupéré à partir de la base de données.

1. Dans **l’Explorateur de solutions**, recherchez et ouvrez la page nommée *ProductList.aspx*.
2. Ajoutez le balisage mis en surbrillance en jaune pour le *ProductList.aspx* page afin que l’ensemble de la page s’affiche comme suit :  

    [!code-aspx[Main](shopping-cart/samples/sample7.aspx?highlight=50-54)]

### <a name="testing-the-shopping-cart"></a>Test du panier d’achat

Exécutez l’application pour voir comment ajouter des produits dans le panier d’achat.

1. Appuyez sur **F5** pour exécuter l’application.  
 Une fois le projet recrée la base de données, le navigateur s’ouvre et affiche le *Default.aspx* page.
2. Sélectionnez **voitures** à partir du menu de navigation de catégorie.  
 Le *ProductList.aspx* page affiche uniquement les produits inclus dans la catégorie « Voitures ». 

    ![Panier d’achat - voitures](shopping-cart/_static/image5.png)
3. Cliquez sur le **ajouter au panier** lien en regard de la première produit répertorié (la voiture convertible).   
 Le *ShoppingCart.aspx* page s’affiche, indiquant la sélection dans votre panier d’achat. 

    ![Panier d’achat - panier](shopping-cart/_static/image6.png)
4. Afficher des produits supplémentaires en sélectionnant **plans** à partir du menu de navigation de catégorie.
5. Cliquez sur le **ajouter au panier** lien en regard de la première produit répertorié.  
 Le *ShoppingCart.aspx* page s’affiche avec l’élément supplémentaire.
6. Fermez le navigateur.

### <a name="calculating-and-displaying-the-order-total"></a>Calcul et affichage du Total de la commande

En plus de l’ajout de produits dans le panier d’achat, vous allez ajouter un `GetTotal` méthode à la `ShoppingCart` classe et afficher le montant total des commandes dans la page du panier.

1. Dans **l’Explorateur de solutions**, ouvrez le *ShoppingCartActions.cs* de fichiers dans le *logique* dossier.
2. Ajoutez le code suivant `GetTotal` méthode mis en surbrillance en jaune à la `ShoppingCart` classe, afin que la classe s’affiche comme suit :   

    [!code-csharp[Main](shopping-cart/samples/sample8.cs?highlight=85-97)]

Tout d’abord, la `GetTotal` méthode obtient l’ID de panier d’achat pour l’utilisateur. La méthode obtient ensuite le panier d’achat total en multipliant le prix du produit par la quantité du produit pour chaque produit répertorié dans le panier d’achat.

> [!NOTE] 
> 
> Le code ci-dessus utilise le type nullable «`int?`». Types Nullable peuvent représenter toutes les valeurs d’un type sous-jacent et également comme une valeur null. Pour plus d’informations, consultez [à l’aide des Types Nullable](https://msdn.microsoft.com/library/2cf62fcy(v=vs.110).aspx).


### <a name="modify-the-shopping-cart-display"></a>Modification de l’affichage du panier d’achat

Ensuite, vous allez modifier le code pour le *ShoppingCart.aspx* page pour appeler le `GetTotal` (méthode) et l’affichage total sur le *ShoppingCart.aspx* page lorsque la page se charge.

1. Dans **l’Explorateur de solutions**, avec le bouton droit le *ShoppingCart.aspx* page et sélectionnez **afficher le Code**.
2. Dans le *ShoppingCart.aspx.cs* de fichiers, mettez à jour le `Page_Load` gestionnaire en ajoutant le code suivant est mis en surbrillance en jaune :   

    [!code-csharp[Main](shopping-cart/samples/sample9.cs?highlight=16-31)]

Lorsque le *ShoppingCart.aspx* chargement de la page, il charge l’objet panier d’achat et récupère ensuite le total de panier d’achat en appelant le `GetTotal` méthode de la `ShoppingCart` classe. Si le panier d’achat est vide, un message s’affiche à cet effet.

### <a name="testing-the-shopping-cart-total"></a>Le Total de panier d’achat de test

Exécutez l’application maintenant pour voir comment pas uniquement ajouter un produit dans le panier d’achat, mais vous pouvez consulter le total de panier d’achat.

1. Appuyez sur **F5** pour exécuter l’application.  
 Le navigateur s’ouvre et affiche le *Default.aspx* page.
2. Sélectionnez **voitures** à partir du menu de navigation de catégorie.
3. Cliquez sur le **Add To Cart** lien en regard du produit en premier.   
 Le *ShoppingCart.aspx* page s’affiche avec le total de la commande. 

    ![Panier d’achat - Total de panier](shopping-cart/_static/image7.png)
4. Ajouter d’autres produits (par exemple, un plan) dans le panier d’achat.
5. Le *ShoppingCart.aspx* page s’affiche avec un total de mises à jour pour tous les produits que vous avez ajouté. 

    ![Panier d’achat - plusieurs produits](shopping-cart/_static/image8.png)
6. Arrêter l’application en cours d’exécution en fermant la fenêtre du navigateur.

### <a name="adding-update-and-checkout-buttons-to-the-shopping-cart"></a>Ajout de mise à jour et les boutons d’extraction dans le panier d’achat

Pour permettre aux utilisateurs de modifier le panier d’achat, vous allez ajouter un **mise à jour** bouton et un **extraction** bouton à la page de panier d’achat. Le **extraction** bouton n’est pas utilisé jusqu'à ce que plus loin dans cette série de didacticiels.

1. Dans **l’Explorateur de solutions**, ouvrez le *ShoppingCart.aspx* page à la racine du projet d’application web.
2. Pour ajouter le **mise à jour** bouton et le **extraction** bouton à la *ShoppingCart.aspx* , ajoutez le balisage mis en surbrillance en jaune au balisage, comme indiqué dans les code suivant :   

    [!code-aspx[Main](shopping-cart/samples/sample10.aspx?highlight=36-45)]

Lorsque l’utilisateur clique sur le **mise à jour** bouton, le `UpdateBtn_Click` Gestionnaire d’événements est appelé. Ce gestionnaire d’événements appelle le code que vous ajouterez à l’étape suivante.

Ensuite, vous pouvez mettre à jour le code contenu dans le *ShoppingCart.aspx.cs* fichier en boucle les éléments du panier et appelez le `RemoveItem` et `UpdateItem` méthodes.

1. Dans **l’Explorateur de solutions**, ouvrez le *ShoppingCart.aspx.cs* dans le fichier racine du projet d’application web.
2. Ajoutez les sections suivantes de code mis en surbrillance en jaune pour le *ShoppingCart.aspx.cs* fichier :   

    [!code-csharp[Main](shopping-cart/samples/sample11.cs?highlight=9-11,33,44-89)]

Lorsque l’utilisateur clique sur le **mise à jour** bouton sur le *ShoppingCart.aspx* page, la méthode UpdateCartItems est appelée. La méthode UpdateCartItems Obtient les valeurs mises à jour pour chaque élément dans le panier d’achat. Ensuite, la méthode UpdateCartItems appelle le `UpdateShoppingCartDatabase` (méthode) (ajouté et expliqué dans l’étape suivante) pour ajouter ou supprimer des éléments dans le panier d’achat. Une fois que la base de données a été mis à jour pour refléter les mises à jour le panier d’achat, la **GridView** contrôle est mis à jour sur la page du panier en appelant le `DataBind` méthode pour le **GridView**. En outre, le montant total des commandes sur la page de panier d’achat est mis à jour pour refléter la liste de mises à jour des éléments.

### <a name="updating-and-removing-shopping-cart-items"></a>Mise à jour et suppression d’éléments de panier d’achat

Sur le *ShoppingCart.aspx* page, vous pouvez voir les contrôles ont été ajoutés pour la quantité d’un élément de mise à jour et suppression d’un élément. Maintenant, ajoutez le code qui permettront à ces contrôles de travail.

1. Dans **l’Explorateur de solutions**, ouvrez le *ShoppingCartActions.cs* de fichiers dans le *logique* dossier.
2. Ajoutez le code suivant est mis en surbrillance en jaune pour le *ShoppingCartActions.cs* fichier de classe :   

    [!code-csharp[Main](shopping-cart/samples/sample12.cs?highlight=99-213)]

Le `UpdateShoppingCartDatabase` méthode, appelée à partir de la `UpdateCartItems` méthode sur le *ShoppingCart.aspx.cs* page, contient la logique pour mettre à jour ou supprimer des éléments dans le panier d’achat. Le `UpdateShoppingCartDatabase` méthode effectue une itération dans toutes les lignes dans la liste de panier d’achat. Si un élément de panier d’achat a été marqué pour être supprimé, ou si la quantité est inférieure à 1, le `RemoveItem` méthode est appelée. Dans le cas contraire, l’élément de panier d’achat est activée pour les mises à jour le `UpdateItem` méthode est appelée. Une fois que l’élément de panier d’achat a été supprimé ou mis à jour, les modifications de la base de données sont enregistrées.

Le `ShoppingCartUpdates` structure est utilisée pour contenir tous les éléments du panier d’achat. Le `UpdateShoppingCartDatabase` utilise le `ShoppingCartUpdates` structure afin de déterminer si les éléments doivent être mis à jour ou supprimé.

Dans l’étape suivante du didacticiel, vous allez utiliser le `EmptyCart` méthode pour effacer les achats panier après l’achat des produits. Mais pour l’instant, vous allez utiliser le `GetCount` méthode que vous venez d’ajouter à la *ShoppingCartActions.cs* fichier pour déterminer le nombre d’éléments dans le panier d’achat.

### <a name="adding-a-shopping-cart-counter"></a>Ajout d’un compteur de panier d’achat

Pour autoriser l’utilisateur d’afficher le nombre total d’éléments dans le panier d’achat, vous allez ajouter un compteur à le *Site.Master* page. Ce compteur représente également un lien vers le panier d’achat.

1. Dans **l’Explorateur de solutions**, ouvrez le *Site.Master* page.
2. Modifiez le balisage en ajoutant la liaison de compteur de panier d’achat comme indiqué en jaune dans la section navigation afin qu’elle apparaisse comme suit :  

    [!code-html[Main](shopping-cart/samples/sample13.html?highlight=6)]
3. Ensuite, mettez à jour le code-behind de la *Site.Master.cs* fichier en ajoutant le code mis en surbrillance en jaune comme suit :  

    [!code-csharp[Main](shopping-cart/samples/sample14.cs?highlight=11,77-84)]

Avant de la page est rendue au format HTML, le `Page_PreRender` événement est déclenché. Dans le `Page_PreRender` gestionnaire, le nombre total de panier d’achat est déterminé en appelant le `GetCount` (méthode). La valeur retournée est ajoutée à la `cartCount` étendue incluse dans le balisage de la *Site.Master* page. Le `<span>` balises active les éléments internes doivent être rendus correctement. Lorsque n’importe quelle page du site s’affiche, le total de panier d’achat s’affichera. L’utilisateur peut cliquer également sur le total de panier d’achat pour afficher le panier d’achat.

## <a name="testing-the-completed-shopping-cart"></a>Test du panier d’achat terminé

Vous pouvez exécuter l’application maintenant pour voir comment vous pouvez ajouter, supprimer et les éléments de mise à jour dans le panier d’achat. Le total de panier d’achat reflète le coût total de tous les éléments dans le panier d’achat.

1. Appuyez sur **F5** pour exécuter l’application.  
 Le navigateur s’ouvre et affiche le *Default.aspx* page.
2. Sélectionnez **voitures** à partir du menu de navigation de catégorie.
3. Cliquez sur le **Add To Cart** lien en regard du produit en premier.   
 Le *ShoppingCart.aspx* page s’affiche avec le total de la commande.
4. Sélectionnez **plans** à partir du menu de navigation de catégorie.
5. Cliquez sur le **Add To Cart** lien en regard du produit en premier.
6. Définissez la quantité du premier élément dans le panier d’achat à 3, puis sélectionnez le **supprimer un élément** case à cocher du deuxième élément.<a id="a"></a>
7. Cliquez sur le **mettre à jour** pour mettre à jour de la page du panier et afficher le total de la nouvelle commande. 

    ![Panier d’achat - mise à jour le panier](shopping-cart/_static/image9.png)

## <a name="summary"></a>Récapitulatif

Dans ce didacticiel, vous avez créé un panier d’achat pour l’exemple d’application Wingtip Toys Web Forms. Au cours de ce didacticiel, vous utilisez Entity Framework Code First, les annotations de données, les contrôles de données fortement typées et liaison de modèle.

Le panier d’achat prend en charge l’ajout, la suppression et la mise à jour des éléments de l’utilisateur a sélectionné à l’achat. En plus d’implémenter les fonctionnalités de panier d’achat, vous avez appris comment afficher des éléments de panier d’achat dans une **GridView** contrôler et calculer le total de la commande.

## <a name="addition-information"></a>Plus d’informations

[Vue d’ensemble de l’état de Session ASP.NET](https://msdn.microsoft.com/library/ms178581.aspx)

>[!div class="step-by-step"]
[Précédent](display_data_items_and_details.md)
[Suivant](checkout-and-payment-with-paypal.md)
