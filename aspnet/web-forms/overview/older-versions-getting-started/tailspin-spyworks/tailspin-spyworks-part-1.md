---
uid: web-forms/overview/older-versions-getting-started/tailspin-spyworks/tailspin-spyworks-part-1
title: "Partie 1 : Fichier -> Nouveau projet | Documents Microsoft"
author: JoeStagner
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application Tailspin Spyworks. Partie 1 couvre la vue d’ensemble et le fichier/nouveau projet."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/21/2010
ms.topic: article
ms.assetid: 15d4652b-d5aa-4172-b186-2c7f96ba316d
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/tailspin-spyworks/tailspin-spyworks-part-1
msc.type: authoredcontent
ms.openlocfilehash: bd840f9f3f5d723e6bc1bb35955a7770634e9483
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-1-file--new-project"></a>Partie 1 : Fichier -> Nouveau projet
====================
par [Joe Stagner](https://github.com/JoeStagner)

> Tailspin Spyworks montre comment extrêmement simple est de créer des applications puissantes et évolutives pour la plateforme .NET. Il illustre comment utiliser les nouvelles fonctionnalités dans ASP.NET 4 pour créer un magasin en ligne, y compris les achats, l’extraction et l’administration.
> 
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application Tailspin Spyworks. Partie 1 couvre la vue d’ensemble et le fichier/nouveau projet.


## <a id="_Toc260221666"></a>Vue d’ensemble

Ce didacticiel est une introduction à Web Forms d’ASP.NET. Nous allons commencer lentement, afin de l’expérience de développement de niveau web débutant est OK.

L’application que nous allons créer est un magasin en ligne simple.

![](tailspin-spyworks-part-1/_static/image1.jpg)


Les visiteurs peuvent parcourir les produits par catégorie :

![](tailspin-spyworks-part-1/_static/image2.jpg)

Ils peuvent afficher un produit unique et l’ajouter à leur panier d’achat :

![](tailspin-spyworks-part-1/_static/image3.jpg)

Ils peuvent consulter leur panier d’achat, suppression de tous les éléments qu’ils ne voulez plus que :

![](tailspin-spyworks-part-1/_static/image4.jpg)

Procéder à la validation les invite à

![](tailspin-spyworks-part-1/_static/image5.jpg)

![](tailspin-spyworks-part-1/_static/image6.jpg)

Après le classement, ils voient un écran de confirmation simple :

![](tailspin-spyworks-part-1/_static/image7.jpg)


Nous allons commencer par créer un nouveau projet ASP.NET WebForms dans Visual Studio 2010, et nous allons ajouter progressivement les fonctionnalités pour créer une application fonctionnelle complète. Avant cela, nous aborderons accès de base de données, les affichages de listes et de la grille, pages de mise à jour des données, validation des données, à l’aide de pages maîtres pour la mise en page cohérente, AJAX, validation, l’appartenance des utilisateurs et bien plus encore.

Peut suivre la procédure étape par étape, ou vous pouvez télécharger l’application terminée à partir de [http://tailspinspyworks.codeplex.com/](http://tailspinspyworks.codeplex.com/)

Vous pouvez utiliser Visual Studio 2010 ou le libre Visual Web Developer 2010 à partir de [https://www.microsoft.com/express/Web/](https://www.microsoft.com/express/Web/). Pour générer l’application, vous pouvez utiliser SQL Server soit libre SQL Server Express pour héberger la base de données.

## <a id="_Toc260221667"></a>Fichier / nouveau projet

Nous allons commencer en sélectionnant le nouveau projet à partir du menu fichier dans Visual Studio. De la boîte de dialogue Nouveau projet s’affiche.

![](tailspin-spyworks-part-1/_static/image8.jpg)

Nous allons sélectionner Visual C# / modèles Web groupe situé à gauche, puis choisissez le modèle « Application de Web ASP.NET » dans la colonne centrale. Nommez votre projet TailspinSpyworks et appuyez sur le bouton OK.

![](tailspin-spyworks-part-1/_static/image9.jpg)

Cela va créer notre projet. Examinons les dossiers qui sont inclus dans notre application dans l’Explorateur de solutions sur le côté droit.

![](tailspin-spyworks-part-1/_static/image10.jpg)

La Solution vide n’est pas vide, il ajoute une structure de dossiers de base :

![](tailspin-spyworks-part-1/_static/image1.png)

Notez les conventions implémentées par le modèle de projet par défaut ASP.NET 4.

- Le dossier « Compte » implémente une interface utilisateur de base pour ASP. Sous-système de l’appartenance du réseau.
- Le dossier « Scripts » sert de référentiel pour les fichiers JavaScript côté client et les fichiers .js de jQuery principaux sont disponibles par défaut.
- Le dossier « Styles » est utilisé pour organiser nos éléments visuels de site web (feuilles de Style CSS)

Lorsque vous appuyez sur F5 pour exécuter votre application et de restituer la page default.aspx nous consultez les rubriques suivantes.

![](tailspin-spyworks-part-1/_static/image11.jpg)

Notre première amélioration d’application seront pour remplacer le fichier Style.css dans le modèle Web Forms par défaut avec les classes CSS et les fichiers d’image associée qui restituera l’asthetics visual que nous souhaitons pour notre application Tailspin Spyworks.

Après cela, notre page default.aspx restitue comme suit.

![](tailspin-spyworks-part-1/_static/image12.jpg)

Notez les liens de l’image en haut à droite de la page et les éléments de menu qui ont été ajoutés à la page maître. Uniquement les liens de la « connexion » et « Compte » pointent vers les pages qui existent (générés par le modèle par défaut) et le reste des pages que nous créons notre application, nous allons implémenter.

Nous allons également de déplacer la Page maître dans le répertoire de Styles. S’il s’agit uniquement d’une préférence il peut simplifier les choses un peu si vous décidez que notre application « personnalisable » dans le futur.

Une fois cela que nous aurons besoin pour modifier la page maître références dans tous les fichiers .aspx générés par la valeur par défaut des pages Web Forms d’ASP.NET.

>[!div class="step-by-step"]
[Next](tailspin-spyworks-part-2.md)
