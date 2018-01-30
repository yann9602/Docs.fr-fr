---
uid: mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part6
title: "Ajout d’une méthode de création et créer la vue | Documents Microsoft"
author: shanselman
description: "Il s’agit d’un didacticiel débutant qui présente les notions de base d’ASP.NET MVC. Créez une application web simple qui lit et écrit à partir d’une base de données."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/14/2010
ms.topic: article
ms.assetid: a3a90963-0286-4fa0-9b3d-c230cc18b0a3
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part6
msc.type: authoredcontent
ms.openlocfilehash: 36b3d6ef0432292f21ecd8f29ea2d88ee8867436
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
<a name="adding-a-create-method-and-create-view"></a>Ajout d’une méthode de création et créer la vue
====================
par [Scott Hanselman](https://github.com/shanselman)

> Il s’agit d’un didacticiel débutant qui présente les notions de base d’ASP.NET MVC. Vous allez créer une application web simple qui lit et écrit à partir d’une base de données. Visitez le [centre d’apprentissage ASP.NET MVC](../../../index.md) pour rechercher d’autres ASP.NET MVC didacticiels et exemples.


Dans cette section, nous allons implémenter la prise en charge nécessaire pour permettre aux utilisateurs de créer de nouveaux films dans notre base de données. Pour cela, nous allons implémenter l’action de l’URL de films/Create.

Implémentation de l’URL de films/Créer est un processus en deux étapes. Lorsqu’un utilisateur visite tout d’abord l’URL de films/Create que vous souhaitez afficher un formulaire HTML qui peut remplir pour entrer un nouveau film. Ensuite, lorsque l’utilisateur envoie le formulaire et les publications, les données vers le serveur, vous souhaitez récupérer le contenu publié et l’enregistrer dans notre base de données.

Nous allons implémenter ces deux étapes dans les deux méthodes Create() au sein de notre classe MoviesController. Une méthode affiche le &lt;formulaire&gt; que l’utilisateur doit remplir pour créer une nouvelle séquence. La deuxième méthode gère le traitement des données validées lorsque l’utilisateur envoie le &lt;formulaire&gt; sur le serveur et enregistrer un nouveau film dans notre base de données.

Voici le code que nous allons ajouter à la classe MoviesController pour implémenter cela :

[!code-csharp[Main](getting-started-with-mvc-part6/samples/sample1.cs)]

Le code ci-dessus contient tout le code que nous aurons besoin dans notre contrôleur.

Nous allons maintenant implémenter le modèle Create View que nous allons utiliser pour afficher un formulaire à l’utilisateur. Nous allons cliquez avec le bouton droit dans la première méthode de création et sélectionnez « Ajouter une vue « pour créer le modèle d’affichage pour notre formulaire de film.

Nous allons sélectionner nous va passer le modèle d’affichage un film « » en tant que classe de données d’affichage, et indiquer que vous voulez un modèle « Créer » « structurez ».

[![Ajouter une vue](getting-started-with-mvc-part6/_static/image2.png)](getting-started-with-mvc-part6/_static/image1.png)

Après avoir cliqué sur le bouton Ajouter, modèle de vue \Movies\Create.aspx sera créé pour vous. Étant donné que nous avons sélectionné « Créer » dans la liste déroulante « Afficher le contenu », la boîte de dialogue Ajouter une vue automatiquement « structuré » du contenu par défaut pour nous. La génération de modèles automatique créé HTML &lt;formulaire&gt;, un emplacement pour l’erreur de validation des messages pour accéder et étant donné que la structure connaît des films, il créé étiquette et les champs pour chaque propriété de notre classe.

[!code-aspx[Main](getting-started-with-mvc-part6/samples/sample2.aspx)]

Étant donné que notre base de données donne automatiquement un ID d’un film, nous allons supprimer les champs de ce modèle de référence. ID de notre créer une vue. Supprimer les 7 lignes après &lt;légende&gt;champs&lt;/legend&gt; comme elles affichent le champ ID que nous ne voulons pas.

Nous allons maintenant créer une nouvelle séquence et l’ajouter à la base de données. Nous allons le faire en exécutant à nouveau l’application et visitez la « / films « URL et cliquez sur la « créer » ce lien pour ajouter un nouveau film.

[![Create - Windows Internet Explorer](getting-started-with-mvc-part6/_static/image4.png)](getting-started-with-mvc-part6/_static/image3.png)

Lorsque vous cliquez sur le bouton Créer, nous publierons (par HTTP POST) les données sur ce formulaire à la méthode /Movies/Create que nous venons de créer. Lorsque le système a pris le paramètre « numTimes » et « nom » de l’URL et automatiquement les mappés à des paramètres sur une méthode plus haut, comme le système sera automatiquement prendre les champs de formulaire à partir d’une publication et les mapper à un objet. Dans ce cas, les valeurs des champs au format HTML, comme « ReleaseDate » et « Title » sont automatiquement activés dans les propriétés correctes d’une nouvelle instance d’un film.

Examinons la deuxième méthode de création de notre MoviesController à nouveau. Notez la façon dont il prend un objet de « Film » en tant qu’argument :

[!code-csharp[Main](getting-started-with-mvc-part6/samples/sample3.cs)]

Cet objet de séquence a été passé ensuite à la version de [HttpPost] de la méthode d’action Create, et nous avons enregistré dans la base de données et puis l’utilisateur est redirigé vers la méthode d’action Index() qui affiche le résultat enregistré dans la liste de films :

[![Liste de films - Windows Internet Explorer](getting-started-with-mvc-part6/_static/image6.png)](getting-started-with-mvc-part6/_static/image5.png)

Nous ne la vérification si notre films sont corrects, cependant, et la base de données ne sont pas à enregistrer une vidéo avec aucun titre. Il peut être intéressant si nous pourrions l’utilisateur a généré une erreur avant de la base de données. Nous nous chargeons ce qui suit en ajoutant la prise en charge de la validation à notre application.

>[!div class="step-by-step"]
[Précédent](getting-started-with-mvc-part5.md)
[Suivant](getting-started-with-mvc-part7.md)
