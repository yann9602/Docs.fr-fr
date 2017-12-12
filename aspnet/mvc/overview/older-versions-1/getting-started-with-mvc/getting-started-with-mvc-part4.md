---
uid: mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part4
title: "Création d’une base de données | Documents Microsoft"
author: shanselman
description: "Il s’agit d’un didacticiel débutant qui présente les notions de base d’ASP.NET MVC. Vous allez créer une application web simple qui lit et écrit à partir d’une base de données."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/14/2010
ms.topic: article
ms.assetid: 742df67f-484d-4ef3-af6b-8c791e556b43
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part4
msc.type: authoredcontent
ms.openlocfilehash: 6a9617b8056f883d6be2547588b13063a58abd0e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-a-database"></a>Création d’une base de données
====================
par [Scott Hanselman](https://github.com/shanselman)

> Il s’agit d’un didacticiel débutant qui présente les notions de base d’ASP.NET MVC. Vous allez créer une application web simple qui lit et écrit à partir d’une base de données. Visitez le [centre d’apprentissage ASP.NET MVC](../../../index.md) pour rechercher d’autres ASP.NET MVC didacticiels et exemples.


Dans cette section, nous allons créer une nouvelle base de SQL Express que nous allons utiliser pour stocker et récupérer des données de notre vidéo. À partir de l’IDE de Visual Web Developer, sélectionnez Afficher | Explorateur de serveurs. Cliquez avec le bouton droit sur connexions de données et cliquez sur Ajouter une connexion...

![AddConnection](getting-started-with-mvc-part4/_static/image1.png)

Dans la boîte de dialogue Choisir la Source de données, sélectionnez Microsoft SQL Server et sélectionnez Continuer.

![](getting-started-with-mvc-part4/_static/image2.png)

Dans la boîte de dialogue Ajouter une connexion, entrez «. \SQLEXPRESS » pour le nom de votre serveur, puis entrez « Films » comme nom pour votre nouvelle base de données.

[![Ajouter la boîte de dialogue Connexion](getting-started-with-mvc-part4/_static/image4.png)](getting-started-with-mvc-part4/_static/image3.png)

Cliquez sur OK et vous demandera si vous souhaitez créer cette base de données. Sélectionnez Oui.

[![Créer des films ?](getting-started-with-mvc-part4/_static/image6.png)](getting-started-with-mvc-part4/_static/image5.png)

Vous avez maintenant une base de données vide dans l’Explorateur de serveurs.

![Ajouter une nouvelle Table](getting-started-with-mvc-part4/_static/image7.png)

Cliquez avec le bouton droit sur les Tables et cliquez sur Ajouter une Table. Le Concepteur de tables s’affiche. Ajouter des colonnes pour l’Id, Title, ReleaseDate, Genre et prix. Cliquez avec le bouton droit sur la colonne ID et cliquez sur définie la clé primaire. Voici mon secteurs conception ressemble à.

[![Éditeur de Table de base de données](getting-started-with-mvc-part4/_static/image9.png)](getting-started-with-mvc-part4/_static/image8.png)

En outre, sélectionnez la colonne d’Id et sous propriétés de colonne ci-dessous, remplacez « Spécification d’identité » sur « Oui ».

[![IsIdentity - propriétés des colonnes](getting-started-with-mvc-part4/_static/image11.png)](getting-started-with-mvc-part4/_static/image10.png)

Lorsque c’est terminé, cliquez sur l’icône Enregistrer dans la barre d’outils ou sélectionnez fichier | Enregistrer dans le menu et nommez votre table «**film**» (singulier). Nous avons une base de données et une table !

[![Choisissez le nom](getting-started-with-mvc-part4/_static/image13.png)](getting-started-with-mvc-part4/_static/image12.png)

Revenez à l’Explorateur de serveurs et cliquez avec le bouton droit sur la table de film, puis sélectionnez « Afficher les données Table ». Entrez les films quelques afin que notre base de données dispose de certaines données.

[![Modification de la Table de base de données](getting-started-with-mvc-part4/_static/image15.png)](getting-started-with-mvc-part4/_static/image14.png)

## <a name="creating-a-model"></a>Création d’un modèle

Maintenant, revenez à l’Explorateur de solutions sur le côté droit de l’IDE et avec le bouton droit sur le dossier de modèles et sélectionnez Ajouter | Nouvel élément.

[![addnewmodelitem](getting-started-with-mvc-part4/_static/image17.png)](getting-started-with-mvc-part4/_static/image16.png)

Nous allons créer un modèle d’entité à partir de notre nouvelle base de données. Cette opération ajoute un ensemble de classes à notre projet qui facilite la tâche pour interroger et manipuler les données dans notre base de données. Sélectionnez le nœud de données sur le côté gauche de la boîte de dialogue, puis le modèle d’élément ADO.NET Entity Data Model. Nommez-le Movies.edmx.

[![AddNewDataModel](getting-started-with-mvc-part4/_static/image19.png)](getting-started-with-mvc-part4/_static/image18.png)

Cliquez sur le bouton « Ajouter ». Cela lance le « Assistant Entity Data Model ».

Dans la nouvelle boîte de dialogue qui s’affiche, sélectionnez Générer à partir de la base de données. Étant donné que nous avons simplement une base de données, nous devrons uniquement indiquer à Entity Framework sur notre nouvelle base de données et sa table. Cliquez sur Suivant pour enregistrer notre connexion de base de données dans la configuration de notre application web. Examinez à présent les Tables et les films case à cocher et cliquez sur Terminer.

[![Assistant Entity Data Model](getting-started-with-mvc-part4/_static/image21.png)](getting-started-with-mvc-part4/_static/image20.png)

Nous pouvons maintenant voir notre nouvelle table de film dans Entity Framework Designer et y accéder à partir du code.

[![Films - Microsoft Visual Web Developer 2010 Express](getting-started-with-mvc-part4/_static/image23.png)](getting-started-with-mvc-part4/_static/image22.png)

Sur l’aire de conception, vous pouvez voir une classe « Film ». Cette classe mappe à la table « Film » dans notre base de données, et chaque propriété est mappé à une colonne avec la table. Chaque instance d’une classe « Film » correspond à une ligne dans la table « Film ».

Si vous n’aimez pas d’affectation de noms par défaut et de mappage des conventions utilisées par Entity Framework, vous pouvez utiliser le Concepteur d’Entity Framework pour modifier ou les personnaliser. Pour cette application, nous allons utiliser les valeurs par défaut et enregistrez simplement le fichier en tant que-est.

Maintenant, nous allons travailler avec des données réelles.

>[!div class="step-by-step"]
[Précédent](getting-started-with-mvc-part3.md)
[Suivant](getting-started-with-mvc-part5.md)
