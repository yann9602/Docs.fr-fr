---
uid: web-forms/overview/older-versions-getting-started/tailspin-spyworks/tailspin-spyworks-part-2
title: "Partie 2 : Couche d’accès aux données | Documents Microsoft"
author: JoeStagner
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application Tailspin Spyworks. Partie 2 couvre l’ajout de la couche d’accès aux données."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/21/2010
ms.topic: article
ms.assetid: 5a9d5429-d70b-411c-8474-f42cf7ef8a2b
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/tailspin-spyworks/tailspin-spyworks-part-2
msc.type: authoredcontent
ms.openlocfilehash: 8b07b320640c1bb0074a4d3a04ca7c5b7e7bb6cd
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-2-data-access-layer"></a>Partie 2 : Couche d’accès aux données
====================
par [Joe Stagner](https://github.com/JoeStagner)

> Tailspin Spyworks montre comment extrêmement simple est de créer des applications puissantes et évolutives pour la plateforme .NET. Il illustre comment utiliser les nouvelles fonctionnalités dans ASP.NET 4 pour créer un magasin en ligne, y compris les achats, l’extraction et l’administration.
> 
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application Tailspin Spyworks. Partie 2 couvre l’ajout de la couche d’accès aux données.


## <a id="_Toc260221668"></a>Ajout de la couche d’accès aux données

Notre application de commerce électronique dépend de deux bases de données.

Pour plus d’informations client, nous allons utiliser la base de données standard de l’appartenance d’ASP.NET. Pour notre catalogue de produit et de panier d’achat nous allons implémenter une base de données SQL Express comme suit.

![](tailspin-spyworks-part-2/_static/image1.jpg)

Après avoir créé la base de données (Commerce.mdf) dans application l’application\_dossier de données que nous pouvons passer à la création de la couche d’accès aux données à l’aide de l’entité de .NET Framework.

Nous allons créer un dossier nommé « données\_accès » et cliquez avec le bouton droit sur ce dossier et sélectionnez « Ajouter un nouvel élément ».

Dans le « modèles installés » élément et sélectionnez « ADO.NET Entity Data Model » Entrez EDM\_Commerce.edmx en tant que le nom et cliquez sur le bouton « Ajouter ».

![](tailspin-spyworks-part-2/_static/image2.jpg)

Cliquez sur « Générer à partir de la base de données ».

![](tailspin-spyworks-part-2/_static/image1.png)

![](tailspin-spyworks-part-2/_static/image2.png)

![](tailspin-spyworks-part-2/_static/image3.png)

![](tailspin-spyworks-part-2/_static/image3.jpg)

Enregistrez et générez.

Nous sommes maintenant prêts à ajouter la fonctionnalité de notre premier : un menu de catégorie de produit.

>[!div class="step-by-step"]
[Précédent](tailspin-spyworks-part-1.md)
[Suivant](tailspin-spyworks-part-3.md)
