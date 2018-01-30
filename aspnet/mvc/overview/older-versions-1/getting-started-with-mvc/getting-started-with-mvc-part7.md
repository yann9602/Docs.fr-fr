---
uid: mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part7
title: "Ajout d’une Validation pour le modèle | Documents Microsoft"
author: shanselman
description: "Il s’agit d’un didacticiel débutant qui présente les notions de base d’ASP.NET MVC. Créez une application web simple qui lit et écrit à partir d’une base de données."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/14/2010
ms.topic: article
ms.assetid: aa7b3e8e-e23d-49f1-b160-f99a7f2982bd
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part7
msc.type: authoredcontent
ms.openlocfilehash: 5616c3c3bc77be0a770540d04cc2ae48ba9eedff
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
<a name="adding-validation-to-the-model"></a>Ajout d’une Validation pour le modèle
====================
par [Scott Hanselman](https://github.com/shanselman)

> Il s’agit d’un didacticiel débutant qui présente les notions de base d’ASP.NET MVC. Vous allez créer une application web simple qui lit et écrit à partir d’une base de données. Visitez le [centre d’apprentissage ASP.NET MVC](../../../index.md) pour rechercher d’autres ASP.NET MVC didacticiels et exemples.


Dans cette section, nous allons implémenter la prise en charge nécessaire pour activer la validation d’entrée de l’application. Nous allons vous assurer que notre contenu de la base de données est toujours correct et fournir des messages d’erreur utiles aux utilisateurs finaux lorsqu’ils essayez et entrer des données de film qui ne sont pas valides. Nous allons commencer en ajoutant un peu logique de validation à la classe de film.

Cliquez avec le bouton droit sur le dossier de modèle et sélectionnez Ajouter une classe. Nom de votre classe de film.

Lorsque nous avons créé le modèle d’entité de film précédemment, l’IDE créé une classe de film. En fait, la partie de la classe de film peut être dans un fichier et partie dans un autre. Il s’agit d’une classe partielle. Nous allons étendre la classe de films à partir d’un autre fichier.

Nous allons créer une classe partielle de film qui pointe vers une classe « associé » avec certains attributs qui vous donne des indicateurs de validation pour le système. Nous allons marquer le titre et le prix que nécessaire et également imposer que le prix soit dans une certaine plage. Cliquez avec le bouton droit sur le dossier Modèles et sélectionnez Ajouter une classe. Nom de votre classe film et cliquez sur le bouton OK. Voici ce que notre partielle film classe ressemble.

[!code-csharp[Main](getting-started-with-mvc-part7/samples/sample1.cs)]

Exécuter à nouveau votre application et essayez de saisir un film avec un prix supérieures à 100. Vous obtiendrez une erreur après avoir soumis le formulaire. L’erreur est interceptée côté serveur et se produit une fois que le formulaire est publié. Notez comment les programmes d’assistance HTML intégrés de ASP.NET MVC ont été assez intelligents pour afficher le message d’erreur et de conserver les valeurs pour nous dans les éléments de la zone de texte :

[![CreateMovieWithValidation](getting-started-with-mvc-part7/_static/image2.png)](getting-started-with-mvc-part7/_static/image1.png)

Cela fonctionne très bien, mais il peut être intéressant si nous pourrions l’utilisateur sur la côté client, immédiatement, avant que le serveur obtient impliqué.

Nous allons activer une validation côté client avec JavaScript.

## <a name="adding-client-side-validation"></a>Ajout d’une Validation côté Client

Étant donné que certains attributs de validation est déjà dans notre classe vidéo, nous devez simplement ajouter quelques fichiers JavaScript à notre Create.aspx afficher le modèle et ajouter une ligne de code pour activer la validation côté client puisse avoir lieu.

À partir de VWD accéder notre dossier vues/film et ouvrez Create.aspx.

Ouvrez le dossier de Scripts dans l’Explorateur de solutions et faites glisser les scripts suivants trois à dans le &lt;head&gt; balise.

- MicrosoftAjax.js
- MicrosoftMvcValidation.js

Vous souhaitez que ces fichiers de script à apparaître dans cet ordre.

[!code-html[Main](getting-started-with-mvc-part7/samples/sample2.html)]

En outre, ajoutez la ligne unique ci-dessus le Html.BeginForm :

[!code-aspx[Main](getting-started-with-mvc-part7/samples/sample3.aspx)]

Voici le code indiqué dans l’IDE.

[![Films - Microsoft Visual Web Developer 2010 Express (10)](getting-started-with-mvc-part7/_static/image4.png)](getting-started-with-mvc-part7/_static/image3.png)

Exécuter votre application et consultez à nouveau /Movies/Create et cliquez sur créer sans entrer de données. Les messages d’erreur apparaissent immédiatement sans la page flash que nous associons à l’envoi de données complètement sur le serveur. Il s’agit, car ASP.NET MVC est maintenant valider l’entrée sur les deux le client (à l’aide de JavaScript) et sur le serveur.

[![Create - Windows Internet Explorer](getting-started-with-mvc-part7/_static/image6.png)](getting-started-with-mvc-part7/_static/image5.png)

Cette recherche bon ! Vous allez maintenant ajouter une colonne supplémentaire à la base de données.

>[!div class="step-by-step"]
[Précédent](getting-started-with-mvc-part6.md)
[Suivant](getting-started-with-mvc-part8.md)
