---
uid: mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part8
title: "Ajout d’une colonne au modèle | Documents Microsoft"
author: shanselman
description: "Il s’agit d’un didacticiel débutant qui présente les notions de base d’ASP.NET MVC. Créez une application web simple qui lit et écrit à partir d’une base de données."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/14/2010
ms.topic: article
ms.assetid: 7ae696b9-348f-4993-8ebb-a838acbe0c28
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part8
msc.type: authoredcontent
ms.openlocfilehash: 17ee105f596319423ac83cf718683ed293f952f3
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
<a name="adding-a-column-to-the-model"></a>Ajout d’une colonne au modèle
====================
par [Scott Hanselman](https://github.com/shanselman)

> Il s’agit d’un didacticiel débutant qui présente les notions de base d’ASP.NET MVC. Vous allez créer une application web simple qui lit et écrit à partir d’une base de données. Visitez le [centre d’apprentissage ASP.NET MVC](../../../index.md) pour rechercher d’autres ASP.NET MVC didacticiels et exemples.


Dans cette section, nous allons guident comment nous pouvons apporter des modifications au schéma de notre base de données et gérer les modifications de l’application.

Vous allez ajouter une colonne « Évaluation » à la table de film. Revenez à l’IDE, puis cliquez sur l’Explorateur de base de données. Cliquez avec le bouton droit sur la table de film et sélectionnez Ouvrir la définition de Table.

Ajouter une colonne « Évaluation » comme indiqué ci-dessous. Étant donné que nous n’avons pas toutes les évaluations maintenant, la colonne peut autoriser les valeurs NULL. Cliquez sur Enregistrer.

[![Modification de Table de films](getting-started-with-mvc-part8/_static/image2.png)](getting-started-with-mvc-part8/_static/image1.png)

Ensuite, revenez à l’Explorateur de solutions et ouvrez le fichier Movies.edmx (qui se trouve dans le dossier \Models). Cliquez avec le bouton droit sur l’aire de conception (la zone blanche) et sélectionnez le modèle de mise à jour à partir de la base de données.

[![Films - Microsoft Visual Web Developer 2010 Express (11)](getting-started-with-mvc-part8/_static/image4.png)](getting-started-with-mvc-part8/_static/image3.png)

Cette action lance l’Assistant de mise à jour « ». Cliquez sur l’onglet de l’actualisation dans celui-ci, puis cliquez sur Terminer. Notre classe de modèle de film sera ensuite être mis à jour avec la nouvelle colonne.

![Assistant Mise à jour (2)](getting-started-with-mvc-part8/_static/image5.png)

Après avoir cliqué sur Terminer, vous pouvez voir que la nouvelle colonne de classement a été ajoutée à l’entité de film dans notre modèle.

[![Entité de film](getting-started-with-mvc-part8/_static/image7.png)](getting-started-with-mvc-part8/_static/image6.png)

Nous avons ajouté une colonne dans le modèle de base de données, mais ne conscient pas les vues à son sujet.

## <a name="update-views-with-model-changes"></a>Vues de la mise à jour avec les modifications apportées au modèle

Il existe plusieurs façons, nous pouvons mettre à jour nos modèles de vue afin de refléter la nouvelle colonne de classement. Étant donné que nous avons créé ces vues en les générant via la boîte de dialogue Ajouter une vue, nous avons supprimez-les et recréez-les à nouveau. Toutefois, en général personnes seront déjà modifications apportées à leurs modèles d’affichage à partir de la génération de modèle généré automatiquement initiale et souhaiteront ajouter ou supprimer des champs manuellement, comme nous l’avons fait pour créer le champ ID.

Ouvrez le modèle \Views\Movies\Index.aspx et ajoutez un &lt;th&gt;évaluation&lt;/th&gt; au début de la table de film. J’ai ajouté mine après le Genre. Puis, dans la même position de colonne, mais plus bas, ajouter une ligne pour notre nouvelle évaluation de la sortie.

[!code-aspx[Main](getting-started-with-mvc-part8/samples/sample1.aspx)]

Notre modèle Index.aspx final doit ressembler à ceci :

[!code-aspx[Main](getting-started-with-mvc-part8/samples/sample2.aspx)]

Nous allons ensuite ouvrir le modèle \Views\Movies\Create.aspx et ajouter une étiquette et une zone de texte pour notre nouvelle propriété de contrôle d’accès :

[!code-aspx[Main](getting-started-with-mvc-part8/samples/sample3.aspx)]

Notre modèle Create.aspx final ressembler à ceci et nous allons modifier le titre et la base de données secondaire de notre navigateur &lt;h2&gt; titre à quelque chose comme « Créer un film » pendant que nous sommes ici !

[!code-aspx[Main](getting-started-with-mvc-part8/samples/sample4.aspx)]

Exécuter votre application et que vous disposez désormais un nouveau champ dans la base de données qui a été ajouté à la page de création. Ajouter un nouveau film - cette fois avec une évaluation égale à - et cliquez sur Créer.

[![Créer un film - Windows Internet Explorer](getting-started-with-mvc-part8/_static/image9.png)](getting-started-with-mvc-part8/_static/image8.png)

Une fois que vous cliquez sur Créer, vous êtes envoyé à la page d’Index où vous nouveau film est répertorié avec la nouvelle colonne de classement dans la base de données

[![Liste de films - Windows Internet Explorer (12)](getting-started-with-mvc-part8/_static/image11.png)](getting-started-with-mvc-part8/_static/image10.png)

Ce didacticiel de base obtenu vous lancer dans la création de contrôleurs, leur association avec des vues et en passant autour des données codées en dur. Ensuite, nous avons créé et conçu une base de données et placer des données dans. Nous les données récupérées à partir de la base de données et elle affiche dans une table HTML. Ensuite, nous avons ajouté un formulaire de création qui permettent à l’utilisateur d’ajouter des données à la base de données eux-mêmes à partir de l’Application Web. Nous avons ajouté la validation, puis apportées à la validation d’utiliser JavaScript côté client. Enfin, nous avons modifié la base de données pour inclure une colonne de données, puis nos deux pages pour créer et afficher ces nouvelles données mises à jour.

Je maintenant vous encourage à passer à notre didacticiel de niveau intermédiaire «[magasin de musique MVC](../../older-versions/mvc-music-store/mvc-music-store-part-1.md)», ainsi que des vidéos et des ressources au nombre [https://asp.net/mvc](https://asp.net/mvc) pour en savoir plus sur ASP.NET MVC !

Bonne lecture !

- Scott Hanselman - [http://hanselman.com](http://hanselman.com) et [ @shanselman ](http://twitter.com/shanselman) sur Twitter.

>[!div class="step-by-step"]
[Précédent](getting-started-with-mvc-part7.md)
