---
uid: mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part5
title: "L’accès aux données de votre modèle à partir d’un contrôleur | Documents Microsoft"
author: shanselman
description: "Il s’agit d’un didacticiel débutant qui présente les notions de base d’ASP.NET MVC. Vous allez créer une application web simple qui lit et écrit à partir d’une base de données."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/14/2010
ms.topic: article
ms.assetid: 004703cd-e0e9-4ba7-9974-1b0475c71222
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part5
msc.type: authoredcontent
ms.openlocfilehash: 1a733accabcd10409f5611c31001397e97533fb6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="accessing-your-models-data-from-a-controller"></a>L’accès aux données de votre modèle à partir d’un contrôleur
====================
par [Scott Hanselman](https://github.com/shanselman)

> Il s’agit d’un didacticiel débutant qui présente les notions de base d’ASP.NET MVC. Vous allez créer une application web simple qui lit et écrit à partir d’une base de données. Visitez le [centre d’apprentissage ASP.NET MVC](../../../index.md) pour rechercher d’autres ASP.NET MVC didacticiels et exemples.


Dans cette section, nous allons créer une nouvelle classe MoviesController et écrire du code qui extrait les données de notre vidéo et l’affiche au navigateur à l’aide d’un modèle d’affichage.

Cliquez avec le bouton droit sur le dossier contrôleurs et effectuer une nouvelle MoviesController.

[![Ajouter un contrôleur](getting-started-with-mvc-part5/_static/image2.png)](getting-started-with-mvc-part5/_static/image1.png)

Cela crée un nouveau fichier « MoviesController.cs » sous le dossier \Controllers dans notre projet. Nous allons mettre à jour de la MovieController pour récupérer la liste de films à partir de notre base de données qui vient d’être rempli.

[!code-csharp[Main](getting-started-with-mvc-part5/samples/sample1.cs)]

Nous exécutons une requête LINQ, afin que nous récupérer uniquement les films publiés après l’été 1984. Nous avons besoin d’un modèle d’affichage pour rendre cette liste de films précédent : avec le bouton droit dans la méthode et sélectionnez Ajouter une vue de le créer.

Dans la boîte de dialogue Ajouter une vue nous allons indiquer que nous passons une liste&lt;Movies.Models.Movie&gt; à notre modèle de vue. Contrairement aux fois précédentes nous utilisé la boîte de dialogue Ajouter une vue et que vous avez choisi de créer un modèle « Vide », cette fois, que nous allons indiquer que nous souhaitons automatiquement « structurez » un modèle d’affichage pour nous avec du contenu par défaut de Visual Studio. Nous allons le faire en sélectionnant l’élément « List » dans le menu « contenu de liste déroulante de l’affichage.

N’oubliez pas, lorsque vous avez créé une nouvelle classe, vous devez compiler votre application pour qu’elle s’affiche dans la boîte de dialogue Vue ajouter.

![Ajouter une vue](getting-started-with-mvc-part5/_static/image3.png)

Cliquez sur Ajouter, et le système génère automatiquement le code pour nous qui affiche la liste de films pour une vue. Il est judicieux de modifier le &lt;h2&gt; titre à quelque chose comme « Ma liste de films » comme nous l’avons fait précédemment avec la vue Hello World.

[![Films - Microsoft Visual Web Developer 2010 Express](getting-started-with-mvc-part5/_static/image5.png)](getting-started-with-mvc-part5/_static/image4.png)

Exécutez votre application, vous accédez à /Movies dans la barre d’adresses. Maintenant, nous avons récupéré des données à partir de la base de données à l’aide d’une requête de base à l’intérieur du contrôleur et renvoyé les données à une vue qui connaît les films. Cette vue, puis passe via la liste de films et crée une table de données pour nous.

[![Liste de films - Windows Internet Explorer](getting-started-with-mvc-part5/_static/image7.png)](getting-started-with-mvc-part5/_static/image6.png)

Nous ne sera pas implémenter les détails, modifier et supprimer des fonctionnalités avec cette application - qui nous évite les liens de valeur par défaut créé par le modèle de vue de structure pour nous. Ouvrez le fichier /Movies/Index.aspx et les supprimer.

Voici le code source pour que notre modèle mis à jour de la vue doit ressembler à une fois que nous avons apporté ces modifications :

[!code-aspx[Main](getting-started-with-mvc-part5/samples/sample2.aspx)]

Elle consiste à créer des liens ne sont pas nécessaires, nous allons supprimer les pour cet exemple. Nous allons conserver notre créer un nouveau lien, car il s’agit suivant ! Voici ce que notre application avec cette colonne supprimée.

[![Liste de films - Windows Internet Explorer](getting-started-with-mvc-part5/_static/image9.png)](getting-started-with-mvc-part5/_static/image8.png)

Nous disposons désormais d’une liste simple de nos données de film. Toutefois, si vous cliquez sur le lien « Nouvel », nous obtenons une erreur car il n’est pas connecté ! Nous allons implémenter une méthode d’Action de création et permettre à un utilisateur à entrer de nouveaux films dans notre base de données.

>[!div class="step-by-step"]
[Précédent](getting-started-with-mvc-part4.md)
[Suivant](getting-started-with-mvc-part6.md)
