---
uid: web-forms/overview/older-versions-getting-started/tailspin-spyworks/tailspin-spyworks-part-3
title: "Partie 3 : Mise en page et Menu catégorie | Documents Microsoft"
author: JoeStagner
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application Tailspin Spyworks. Partie 3 traite de l’ajout de mise en page et un menu de la catégorie."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/21/2010
ms.topic: article
ms.assetid: 94ea1a70-a9bc-4241-8f36-08366d64bab9
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/tailspin-spyworks/tailspin-spyworks-part-3
msc.type: authoredcontent
ms.openlocfilehash: 57c0342efb67b94a0d8c8b06dc13a727e7184db8
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-3-layout-and-category-menu"></a>Partie 3 : Mise en page et Menu catégorie
====================
par [Joe Stagner](https://github.com/JoeStagner)

> Tailspin Spyworks montre comment extrêmement simple est de créer des applications puissantes et évolutives pour la plateforme .NET. Il illustre comment utiliser les nouvelles fonctionnalités dans ASP.NET 4 pour créer un magasin en ligne, y compris les achats, l’extraction et l’administration.
> 
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application Tailspin Spyworks. Partie 3 traite de l’ajout de mise en page et un menu de la catégorie.


## <a id="_Toc260221669"></a>Ajout d’une mise en page et un Menu de la catégorie

Dans notre page maître du site, nous allons ajouter un élément div de la colonne de gauche qui contiendra notre menu de catégorie de produit.

[!code-aspx[Main](tailspin-spyworks-part-3/samples/sample1.aspx)]

Notez que l’alignement souhaité et autres mises en forme seront fournies par la classe CSS que nous avons ajouté à notre fichier Style.css.

[!code-css[Main](tailspin-spyworks-part-3/samples/sample2.css)]

Le menu de catégorie de produit sera être créé de façon dynamique lors de l’exécution en interrogeant la base de données de Commerce pour les catégories de produits et créer les éléments de menu et existants correspondant est liée.

Pour ce faire, nous allons utiliser deux des ASP. Contrôles de données puissants de NET. Le contrôle de la « Source de données d’entité » et le contrôle de la « Liste ».

![](tailspin-spyworks-part-3/_static/image1.jpg)

Nous allons en « Mode de conception » et les programmes d’assistance permet de configurer les contrôles.

![](tailspin-spyworks-part-3/_static/image2.jpg)

Nous allons définir la propriété ID du contrôle EntityDataSource pour EDS\_catégorie\_Menu et cliquez sur « Configurer la Source de données ».

![](tailspin-spyworks-part-3/_static/image3.jpg)

Sélectionnez la connexion CommerceEntities qui a été créé pour nous lorsque nous avons créé le modèle de Source de données entité pour notre base de données de Commerce, puis cliquez sur « Suivant ».

![](tailspin-spyworks-part-3/_static/image4.jpg)

Sélectionnez le nom de jeu d’entités de « Catégories » et laisser le reste des options comme valeur par défaut. Cliquez sur « Terminer ».

Maintenant nous allons définir la propriété ID de l’instance du contrôle ListView qui nous les avons placées sur notre page relative à ListView\_ProductsMenu et activer ses d’assistance.

![](tailspin-spyworks-part-3/_static/image5.jpg)

Bien que nous pouvons utiliser les options de contrôle à mettre en forme l’affichage d’élément de données et mise en forme, la création de notre menu ne nécessite que balisage simple pour sera entrer le code dans la vue de source.

[!code-aspx[Main](tailspin-spyworks-part-3/samples/sample3.aspx)]

Notez l’instruction « Eval » : &lt;% # Eval("CategoryName") %&gt;

La syntaxe ASP.NET &lt;% # %&gt; est une convention de raccourci qui indique à l’exécution pour exécuter tout ce qui est contenu dans et les résultats « dans la ligne ».

L’instruction Eval("CategoryName") fait en sorte que, pour l’entrée actuelle dans la collection liée d’éléments de données, d’extraire la valeur des noms d’élément de modèle d’entité « CatagoryName ». Il s’agit d’une syntaxe concise pour une fonctionnalité très puissante.

Permet d’exécuter l’application maintenant.

![](tailspin-spyworks-part-3/_static/image6.jpg)

Notez que notre menu de catégorie de produit s’affiche désormais lorsque nous pointez sur un des éléments de menu de catégorie nous pouvons voir les points de lien d’élément menu à une page, il n’est pas encore implémenter nommé ProductsList.aspx et que nous avons créé un argument de chaîne de requête dynamique qui contient le  id de catégorie.

>[!div class="step-by-step"]
[Précédent](tailspin-spyworks-part-2.md)
[Suivant](tailspin-spyworks-part-4.md)
