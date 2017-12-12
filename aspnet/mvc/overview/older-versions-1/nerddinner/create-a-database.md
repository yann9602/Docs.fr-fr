---
uid: mvc/overview/older-versions-1/nerddinner/create-a-database
title: "Créer une base de données | Documents Microsoft"
author: microsoft
description: "Étape 2 illustre les étapes pour créer la base de données contenant tous le dîner et RSVP des données pour notre application NerdDinner."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/27/2010
ms.topic: article
ms.assetid: 983f3ffa-08b8-4868-b8c9-aa34593fc683
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/nerddinner/create-a-database
msc.type: authoredcontent
ms.openlocfilehash: 7635722fc357356edd06fb4cff301a8c4dfebbef
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="create-a-database"></a>Créer une base de données
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le PDF](http://aspnetmvcbook.s3.amazonaws.com/aspnetmvc-nerdinner_v1.pdf)

> Il s’agit d’étape 2 a gratuit [« « l’application NerdDinner](introducing-the-nerddinner-tutorial.md) qui parcours à comment générer un petit, mais se termine, application web à l’aide d’ASP.NET MVC 1.
> 
> Étape 2 illustre les étapes pour créer la base de données contenant tous le dîner et RSVP des données pour notre application NerdDinner.
> 
> Si vous utilisez ASP.NET MVC 3, nous vous recommandons de suivre les [mise en route a démarré avec MVC 3](../../older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) ou [magasin de musique MVC](../../older-versions/mvc-music-store/mvc-music-store-part-1.md) didacticiels.


## <a name="nerddinner-step-2-creating-the-database"></a>NerdDinner étape 2 : Création de la base de données

Nous utiliserons une base de données pour stocker toutes les données dîner et RSVP pour notre application NerdDinner.

Les étapes ci-dessous montrent la création de la base de données à l’aide de l’édition gratuite de SQL Server Express (que vous pouvez facilement installer à l’aide de V2 de la [Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx)). Tout le code que nous allons écrire fonctionne avec SQL Server Express et SQL Server complète.

### <a name="creating-a-new-sql-server-express-database"></a>Création d’une nouvelle base de données SQL Server Express

Nous allons commencer en cliquant sur notre projet web, puis sélectionnez le **Add -&gt;un nouvel élément** commande de menu :

![](create-a-database/_static/image1.png)

Cela affiche la boîte de dialogue « Ajouter un nouvel élément » de Visual Studio. Nous allons filtrer par la catégorie « Données » et sélectionnez le modèle d’élément « Base de données SQL Server » :

![](create-a-database/_static/image2.png)

Nous allons nommer la base de données SQL Server Express, que nous voulons créer « NerdDinner.mdf » et appuyez sur OK. Visual Studio vous demande si vous souhaitez ajouter ce fichier à notre \App\_répertoire de données (qui est un répertoire déjà le programme d’installation avec accès en lecture et écriture des ACL de sécurité) :

![](create-a-database/_static/image3.png)

Cliquez sur « Oui » et notre nouvelle base de données est créé et ajouté à l’Explorateur de solutions :

![](create-a-database/_static/image4.png)

### <a name="creating-tables-within-our-database"></a>Création de Tables au sein de notre base de données

Nous disposons désormais d’une base de données vide. Nous allons lui ajouter des tables.

Pour ce faire, nous allez accéder à la fenêtre d’onglet de « Explorateur de serveurs » dans Visual Studio, qui permet de gérer les serveurs et bases de données. Bases de données SQL Server Express, stockées dans le \App\_dossier de données de notre application s’affichent automatiquement dans l’Explorateur de serveurs. Nous pouvons éventuellement utiliser l’icône « Se connecter à la base de données » en haut de la fenêtre « Explorateur de serveurs » pour ajouter d’autres bases de données SQL Server (locales et distants) à la liste ainsi :

![](create-a-database/_static/image5.png)

Nous allons ajouter deux tables à notre base de données de NerdDinner : un pour stocker notre préparés et l’autre pour effectuer le suivi de RSVP acceptations leur. Nous pouvons créer des tables en cliquant sur le dossier « Tables » dans notre base de données et en choisissant la commande de menu « Ajouter une nouvelle Table » :

![](create-a-database/_static/image6.png)

Un concepteur de tables qui nous permet de configurer le schéma de la table s’ouvre. Pour notre table « Préparés », nous allons ajouter 10 colonnes de données :

![](create-a-database/_static/image7.png)

Nous voulons la colonne « DinnerID » soit une clé primaire unique pour la table. Nous pouvons le configurer en cliquant sur la colonne « DinnerID » et en choisissant l’élément de menu « Définir la clé primaire » :

![](create-a-database/_static/image8.png)

En plus de rendre DinnerID une clé primaire, nous souhaitons également nous configurez-le en tant que « identité » colonne dont la valeur augmente automatiquement lorsque de nouvelles lignes de données sont ajoutées à la table (ce qui signifie la première ligne Dinner insérée aura un DinnerID 1, le deuxième inséré ligne aura un DinnerID de 2, etc.).

Nous pouvons ce faire, sélectionnez la colonne « DinnerID », puis utilisez l’éditeur « Propriétés de colonne » pour définir la propriété « (identité est) » sur la colonne « Yes ». Nous allons utiliser les valeurs par défaut de l’identité standard (commencent à 1 et incrémente de 1 sur chaque nouvelle ligne Dinner) :

![](create-a-database/_static/image9.png)

Nous allons ensuite enregistrer notre table en tapant Ctrl + S ou à l’aide de la **fichier -&gt;enregistrer** commande de menu. Il vous est demandé pour le nom de la table. Nous allons nommez-le « Préparés » :

![](create-a-database/_static/image10.png)

Notre nouvelle table préparés s’affiche alors dans notre base de données dans l’Explorateur de serveurs.

Nous allons puis répétez les étapes ci-dessus et créer une table « RSVP ». Cette table avec avoir 3 colonnes. Nous le programme d’installation de la colonne RsvpID comme clé primaire et également rendre une colonne d’identité :

![](create-a-database/_static/image11.png)

Nous allons enregistrer et attribuez-lui le nom « RSVP ».

### <a name="setting-up-a-foreign-key-relationship-between-tables"></a>Configuration d’une relation de clé étrangère entre les Tables

Nous disposons de deux tables au sein de notre base de données. Notre dernière étape de création du schéma sera pour configurer une relation « un-à-plusieurs » entre ces deux tables, afin que nous pouvons associer chaque ligne Dinner à zéro ou plusieurs lignes RSVP qui s’y appliquent. Nous le ferons en configurant la colonne « DinnerID » de la table RSVP pour avoir une relation de clé étrangère pour la colonne « DinnerID » dans la table « Préparés ».

Pour ce faire, nous allons ouvrir la table RSVP dans le Concepteur de tables en double-cliquant dessus dans l’Explorateur de serveurs. Sélectionnez la colonne « DinnerID » qu’il contient, puis avec le bouton droit, puis choisissez le « Relationshps... » commande de menu contextuel :

![](create-a-database/_static/image12.png)

Cela affiche une boîte de dialogue que nous pouvons utiliser pour les relations entre les tables du programme d’installation :

![](create-a-database/_static/image13.png)

Cliquez sur le bouton « Ajouter » pour ajouter une relation à la boîte de dialogue. Une fois qu’une relation a été ajoutée, nous allons développer le nœud d’arborescence « Spécification de Tables et de la colonne » dans la grille des propriétés à droite de la boîte de dialogue et puis cliquez sur le bouton «... » à droite de celui-ci :

![](create-a-database/_static/image14.png)

Une autre boîte de dialogue qui permet de spécifier les tables et les colonnes sont impliqués dans la relation, ainsi que nous permettent de nommer la relation en cliquant sur le bouton «... » s’affiche.

Nous modifie la Table de clé primaire pour être « Préparés » et sélectionnez la colonne « DinnerID » dans la table préparés comme clé primaire. Notre table RSVP correspond à la table de clé étrangère et le protocole RSVP. Colonne de DinnerID sera associé en tant que la clé étrangère :

![](create-a-database/_static/image15.png)

Chaque ligne dans la table RSVP sont désormais associé à une ligne dans la table de dîner. SQL Server maintenir l’intégrité référentielle pour nous et nous empêcher d’ajouter une nouvelle ligne RSVP si elle ne pointe pas vers une ligne Dinner valide. Il nous empêchera également de supprimer une ligne Dinner s’il existe toujours RSVP lignes faisant référence à celle-ci.

### <a name="adding-data-to-our-tables"></a>Ajout de données à nos Tables

Nous allons terminer en ajoutant des exemples de données à notre table préparés. Nous pouvons ajouter des données à une table en cliquant dessus dans l’Explorateur de serveurs et en choisissant la commande « Afficher les données de Table » :

![](create-a-database/_static/image16.png)

Nous allons ajouter quelques lignes de données Dinner que nous pouvons utiliser ultérieurement comme nous allons commencer l’implémentation de l’application :

![](create-a-database/_static/image17.png)

### <a name="next-step"></a>Étape suivante

Nous avons terminé la création de notre base de données. Nous allons maintenant créer les classes de modèle que nous pouvons utiliser pour interroger et mettre à jour.

>[!div class="step-by-step"]
[Précédent](create-a-new-aspnet-mvc-project.md)
[Suivant](build-a-model-with-business-rule-validations.md)
