---
uid: web-forms/overview/older-versions-getting-started/tailspin-spyworks/tailspin-spyworks-part-4
title: "Partie 4 : Liste des produits | Documents Microsoft"
author: JoeStagner
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application Tailspin Spyworks. Partie 4 couvre les produits de la liste avec le contrat de GridView..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/21/2010
ms.topic: article
ms.assetid: 4fab47d5-a6ec-4fdc-91f0-651a093a24b9
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/tailspin-spyworks/tailspin-spyworks-part-4
msc.type: authoredcontent
ms.openlocfilehash: 420cdbcc002bcbfff619d399a7a374009999d754
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-4-listing-products"></a>Partie 4 : Liste de produits
====================
par [Joe Stagner](https://github.com/JoeStagner)

> Tailspin Spyworks montre comment extrêmement simple est de créer des applications puissantes et évolutives pour la plateforme .NET. Il illustre comment utiliser les nouvelles fonctionnalités dans ASP.NET 4 pour créer un magasin en ligne, y compris les achats, l’extraction et l’administration.
> 
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application Tailspin Spyworks. Partie 4 couvre la liste des produits avec le contrôle GridView.


## <a id="_Toc260221670"></a>Liste des produits avec le contrôle GridView

Nous allons commencer l’implémentation de notre page ProductsList.aspx en « Cliquant avec le bouton droit sur » sur notre solution, puis en sélectionnant « Ajouter » et « Nouvel élément ».

![](tailspin-spyworks-part-4/_static/image1.jpg)

Choisissez « Formulaire à l’aide de Page maître Web » et entrez un nom de page de ProductsList.aspx ».

Cliquez sur « Ajouter ».

![](tailspin-spyworks-part-4/_static/image2.jpg)

Ensuite, choisissez le dossier « Styles » où nous avons placé la page Site.Master et sélectionnez-le dans la fenêtre « Contenu du dossier ».

![](tailspin-spyworks-part-4/_static/image3.jpg)

Cliquez sur « Ok » pour créer la page.

Notre base de données est remplie avec les données de produit, comme indiqué ci-dessous.

![](tailspin-spyworks-part-4/_static/image4.jpg)

Après la création de notre page Nous utiliserons à nouveau une Source de données d’entité pour accéder aux données de ce produit, mais dans ce cas, nous devons sélectionner les entités Product et nous avons besoin limiter les éléments qui sont retournés à ceux de la catégorie sélectionnée.

Pour cela nous indiquerons EntityDataSource pour générer automatiquement la clause WHERE et spécifions le WhereParameter.

Rappelez-vous que lorsque nous avons créé les éléments de Menu dans notre « Menu de catégorie de produit » nous construites dynamiquement le lien en ajoutant le CatagoryID à la chaîne de requête pour chaque lien. Nous indique à la Source de données d’entité pour dériver le paramètre d’emplacement à partir de ce paramètre de chaîne de requête.

[!code-aspx[Main](tailspin-spyworks-part-4/samples/sample1.aspx)]

Ensuite, nous allons configurer le contrôle ListView pour afficher une liste de produits. Pour créer une expérience d’achat optimale, que nous allons compact plusieurs fonctionnalités concises dans chaque produit affiché dans notre ListVew.

- Le nom de produit doit être un lien vers l’affichage des détails du produit.
- Le prix du produit s’affiche.
- Une image du produit s’affiche et nous allons sélectionner dynamiquement l’image à partir d’un répertoire d’images de catalogue dans notre application.
- Il inclut un lien pour ajouter immédiatement le produit spécifique dans le panier d’achat.

Voici le balisage de notre instance du contrôle ListView.

[!code-aspx[Main](tailspin-spyworks-part-4/samples/sample2.aspx)]

Nous créons dynamiquement plusieurs liens pour chaque produit affiché.

En outre, avant de tester le propre nouvelle page, nous devons créer la structure de répertoire pour le produit des images du catalogue comme suit.

![](tailspin-spyworks-part-4/_static/image1.png)

Une fois que les images de nos produits sont accessibles, nous pouvons tester notre page de liste de produits.

![](tailspin-spyworks-part-4/_static/image5.jpg)

À partir de la page d’accueil du site, cliquez sur un des liens de liste de catégorie.

![](tailspin-spyworks-part-4/_static/image6.jpg)

Nous devons à présent mettre en œuvre de la page ProductDetials.apsx et la fonctionnalité AddToCart.

Utilisez le fichier -&gt;nouveau pour créer un nom de page ProductDetails.aspx à l’aide de la Page maître de site comme nous l’avons fait précédemment.

Nous allons utiliser à nouveau d’un contrôle EntityDataSource pour accéder à l’enregistrement de produit spécifique dans la base de données, et nous allons utiliser un contrôle ASP.NET FormView pour afficher les données de produit comme suit.

[!code-aspx[Main](tailspin-spyworks-part-4/samples/sample3.aspx)]

Ne vous inquiétez pas si la mise en forme semble un peu amusantes à vous. Le balisage ci-dessus laisse place dans la disposition de l’affichage pour quelques fonctionnalités que nous allons implémenter plus tard.

Le panier d’achat représente une logique plus complexe dans notre application. Pour commencer, utilisez le fichier -&gt;nouveau pour créer une page nommée MyShoppingCart.aspx.

Notez que nous choisissons pas le nom ShoppingCart.aspx.

Notre base de données contient une table nommée « ShoppingCart ». Lorsque nous avons généré un Entity Data Model, une classe a été créée pour chaque table dans la base de données. Par conséquent, l’Entity Data Model généré une classe d’entité nommée « ShoppingCart ». Nous pouvons modifier le modèle afin que nous pourrions utiliser ce nom pour notre implémentation de panier d’achat ou de l’étendre à vos besoins, mais nous inclut à la place pour simplement sélectionnez un nom qui permet d’éviter le conflit.

Il est également important de noter que nous allons créer un panier d’achat simple et l’incorporation de la logique de panier d’achat avec l’affichage du panier d’achat. Nous pouvons également choisir d’implémenter notre panier d’achat dans une couche métier complètement distincte.

>[!div class="step-by-step"]
[Précédent](tailspin-spyworks-part-3.md)
[Suivant](tailspin-spyworks-part-5.md)
