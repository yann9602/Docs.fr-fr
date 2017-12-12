---
uid: mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-4
title: "Partie 4 : Accès aux données et les modèles | Documents Microsoft"
author: jongalloway
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 4 couvre l’accès aux données et modèles."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/21/2011
ms.topic: article
ms.assetid: ab55ca81-ab9b-44a0-8700-dc6da2599335
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-4
msc.type: authoredcontent
ms.openlocfilehash: 727336344493b439130b2cf0ec6e7b5925dd5637
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-4-models-and-data-access"></a>Partie 4 : Accès aux données et les modèles
====================
par [Jon Galloway](https://github.com/jongalloway)

> Le magasin de musique MVC est une application du didacticiel qui présente et explique étape par étape comment utiliser ASP.NET MVC et Visual Studio pour le développement web.  
>   
> Le magasin de musique MVC est une implémentation de magasin exemple léger qui vend des albums de musique en ligne et implémente l’administration de site de base, authentification de l’utilisateur et les fonctionnalités de panier d’achat.
> 
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 4 couvre l’accès aux données et modèles.


Jusqu'à présent, nous avons simplement été passant « données factices » de nos contrôleurs à nos modèles de vue. Maintenant, nous sommes prêts à raccorder à une base de données réel. Dans ce didacticiel nous allons couvrant l’utilisation de SQL Server Compact Edition (souvent appelés SQL CE) en tant que notre moteur de base de données. SQL CE est une base de données gratuite et intégrée, fichier basé ne nécessitant pas toute installation ou la configuration, ce qui le rend très pratique pour le développement local.

## <a name="database-access-with-entity-framework-code-first"></a>Accès de base de données avec Entity Framework Code-First

Nous allons utiliser la prise en charge Entity Framework (EF) qui est inclus dans les projets ASP.NET MVC 3 pour interroger et mettre à jour la base de données. EF est un objet flexible relationnel de mappage de données de (ORM) API qui permet aux développeurs de requêtes et mise à jour des données stockées dans une base de données d’une manière orientée objet.

Entity Framework version 4 prend en charge un paradigme de développement appelé code en premier. Code en premier permet de créer un objet de modèle en écrivant des classes simples (également appelé POCO à partir des objets CLR « plain-old ») et peut même créer la base de données à la volée à partir de vos classes.

### <a name="changes-to-our-model-classes"></a>Modifications apportées aux Classes du modèle

Dans ce didacticiel, nous va exploiter la fonctionnalité de création de base de données dans Entity Framework. Avant cela, cependant, créons quelques modifications mineures à nos classes de modèle à ajouter dans certaines choses que nous utiliserons plus tard.

#### <a name="adding-the-artist-model-classes"></a>Ajout des Classes de modèle artiste

Notre Albums à associer à artistes, donc nous allons ajouter une classe de modèle simple pour décrire un artiste. Ajoutez une nouvelle classe dans le dossier de modèles nommé Artist.cs en utilisant le code indiqué ci-dessous.

[!code-csharp[Main](mvc-music-store-part-4/samples/sample1.cs)]

#### <a name="updating-our-model-classes"></a>Classes de notre modèle de mise à jour

Mettre à jour de la classe Album comme indiqué ci-dessous.

[!code-csharp[Main](mvc-music-store-part-4/samples/sample2.cs)]

Ensuite, vérifiez les mises à jour suivantes à la classe de Genre.

[!code-csharp[Main](mvc-music-store-part-4/samples/sample3.cs)]

### <a name="adding-the-appdata-folder"></a>Ajout de l’application\_dossier de données

Nous allons ajouter une application\_répertoire de données à notre projet pour stocker ses fichiers de base de données SQL Server Express. Application\_données sont un répertoire spécial dans ASP.NET qui possède déjà les autorisations d’accès de sécurité correct pour l’accès de la base de données. Dans le menu projet, sélectionnez Ajouter le dossier ASP.NET, puis application\_données.

![](mvc-music-store-part-4/_static/image1.png)

### <a name="creating-a-connection-string-in-the-webconfig-file"></a>Création d’une chaîne de connexion dans le fichier web.config

Nous allons ajouter quelques lignes au fichier de configuration du site Web afin que Entity Framework sait comment se connecter à notre base de données. Double-cliquez sur le fichier Web.config situé à la racine du projet.

![](mvc-music-store-part-4/_static/image2.png)

Faites défiler vers le bas de ce fichier et ajouter un &lt;connectionStrings&gt; section directement au-dessus de la dernière ligne, comme indiqué ci-dessous.

[!code-xml[Main](mvc-music-store-part-4/samples/sample4.xml)]

### <a name="adding-a-context-class"></a>Ajout d’une classe de contexte

Cliquez sur le dossier Modèles et ajouter une nouvelle classe nommée MusicStoreEntities.cs.

![](mvc-music-store-part-4/_static/image3.png)

Cette classe sera représentent le contexte de base de données Entity Framework, sera gérer nos créer, lire, mettre à jour et suppressions pour nous. Le code de cette classe est indiqué ci-dessous.

[!code-csharp[Main](mvc-music-store-part-4/samples/sample5.cs)]

C’est tout - il n’existe aucune autre configuration, des interfaces spéciales, etc. En étendant la classe de base DbContext, notre classe MusicStoreEntities est en mesure de gérer nos opérations de base de données pour nous. Maintenant que nous avons rattachée, vous allez ajouter quelques propriétés supplémentaires pour nos classes de modèle pour tirer parti de certaines des informations supplémentaires dans notre base de données.

### <a name="adding-our-store-catalog-data"></a>Ajout de nos données de catalogue de magasin

Nous allons parti d’une fonctionnalité dans Entity Framework, qui ajoute des données de « seed » à une base de données nouvellement créé. Cela sera préremplir notre catalogue de magasin avec une liste des Genres, les artistes et les Albums. Le téléchargement du MvcMusicStore-Assets.zip - notre site conception les fichiers inclus utilisés précédemment dans ce didacticiel - possède un fichier de classe avec ces données de valeur initiale, situés dans un dossier nommé Code.

Dans le Code de dossier de modèles, recherchez le fichier SampleData.cs et la placer dans le dossier de modèles dans notre projet, comme indiqué ci-dessous.

![](mvc-music-store-part-4/_static/image4.png)

Maintenant, nous devons ajouter une ligne de code pour informer Entity Framework de cette classe SampleData. Double-cliquez sur le fichier Global.asax à la racine du projet et ajoutez la ligne suivante vers le haut l’Application\_Start (méthode).

[!code-csharp[Main](mvc-music-store-part-4/samples/sample6.cs)]

À ce stade, nous avons terminé le travail nécessaire pour configurer notre projet Entity Framework.

## <a name="querying-the-database"></a>Interrogation de la base de données

Maintenant nous allons mettre à jour notre StoreController afin qu’au lieu d’utiliser « factice des données » il appelle dans notre base de données à interroger toutes ses informations à la place. Nous allons commencer en déclarant un champ sur la **StoreController** pour stocker une instance de la classe MusicStoreEntities, nommée storeDB :

[!code-csharp[Main](mvc-music-store-part-4/samples/sample7.cs)]

### <a name="updating-the-store-index-to-query-the-database"></a>Mise à jour de l’Index de magasin pour interroger la base de données

La classe MusicStoreEntities est gérée par Entity Framework et expose une propriété de collection pour chaque table dans notre base de données. Nous allons mettre à jour les action sur l’Index de notre StoreController pour récupérer tous les Genres dans notre base de données. Précédemment nous a fait cela en codage en dur des données de type chaîne. Maintenant nous pouvons utiliser à la place simplement le contexte de l’Entity Framework Generes collection :

[!code-csharp[Main](mvc-music-store-part-4/samples/sample8.cs)]

Aucune modification ne doivent se produire à notre modèle de vue, car nous renvoyons toujours le même StoreIndexViewModel nous retournés avant - nous simplement renvoyons données actives à partir de notre base de données maintenant.

Exécuter à nouveau le projet et accédez à l’URL « / stocker », nous verrons désormais une liste de tous les Genres dans notre base de données :

![](mvc-music-store-part-4/_static/image1.jpg)

### <a name="updating-store-browse-and-details-to-use-live-data"></a>Mise à jour de magasin de parcourir et détails à utiliser les données actives

Avec le magasin/Parcourir ? genre =*[certains genre]* méthode d’action, nous recherchons un Genre par nom. Nous n'espérons qu’un seul résultat, étant donné que nous ne doit jamais deux entrées pour le nom du même Genre, et par conséquent, nous pouvons utiliser le. Extension Single() dans LINQ pour interroger l’objet de Genre approprié comme suit (ne tapez pas cela encore) :

[!code-csharp[Main](mvc-music-store-part-4/samples/sample9.cs)]

La seule méthode prend une expression Lambda en tant que paramètre, qui spécifie que nous voulons un seul objet Genre telles que son nom correspond à la valeur que nous avons défini. Dans le cas ci-dessus, nous chargeons un seul objet Genre avec une valeur de nom correspondant Disco.

Nous allons tirer parti d’une fonction d’Entity Framework qui permet d’indiquer d’autres entités associées, nous souhaitons également chargées lorsque l’objet de Genre est récupéré. Cette fonctionnalité est appelée à la mise en forme du résultat de requête et nous permet de réduire le nombre de fois où que nous avons besoin d’accéder à la base de données pour récupérer toutes les informations que nous avons besoin. Nous souhaitons Prérécupérer les Albums pour Genre nous récupérer, donc nous allons mettre à jour notre requête à inclure à partir de Genres.Include("Albums") pour indiquer que nous souhaitons également les albums connexes. Cela est plus efficace, car il récupère les données de notre Genre et l’Album dans une requête de base de données unique.

Avec des explications, voici à quoi ressemble notre action de contrôleur Parcourir mis à jour :

[!code-csharp[Main](mvc-music-store-part-4/samples/sample10.cs)]

Nous pouvons maintenant mettre à jour le magasin de parcourir la vue pour afficher les albums qui sont disponibles dans chaque Genre. Ouvrez le modèle d’affichage (dans /Views/Store/Browse.cshtml) et ajouter une liste à puces des Albums, comme indiqué ci-dessous.

[!code-cshtml[Main](mvc-music-store-part-4/samples/sample11.cshtml)]

Notre application en cours d’exécution et en accédant à/Store/Parcourir ? genre = affiche Jazz que nos résultats sont maintenant chargés à partir de la base de données, affichage de tous les albums dans notre Genre sélectionné.

![](mvc-music-store-part-4/_static/image2.jpg)

Nous allons effectuer la même remplacer à notre /Store/détails / [id] URL, puis remplacez nos données factices avec une requête de base de données qui charge un Album dont l’ID correspond à la valeur du paramètre.

[!code-csharp[Main](mvc-music-store-part-4/samples/sample12.cs)]

Notre application en cours d’exécution et en accédant à /Store/Details/1 montre que nos résultats sont maintenant chargés à partir de la base de données.

![](mvc-music-store-part-4/_static/image5.png)

Maintenant que notre page de détails de la banque est configuré pour afficher un album par l’ID de l’Album, mettons à jour la **Parcourir** qui permet de créer un lien vers l’affichage des détails. Nous allons utiliser Html.ActionLink, exactement comme nous l’avons fait pour créer un lien à partir de l’Index de la banque pour parcourir de magasin à la fin de la section précédente. La source complète pour le mode de navigation apparaît ci-dessous.

[!code-cshtml[Main](mvc-music-store-part-4/samples/sample13.cshtml)]

Nous pouvons maintenant accéder à partir de notre page magasin à une page de Genre, qui répertorie les albums disponibles, et en cliquant sur un album nous pouvons afficher les détails de cet album.

![](mvc-music-store-part-4/_static/image6.png)

>[!div class="step-by-step"]
[Précédent](mvc-music-store-part-3.md)
[Suivant](mvc-music-store-part-5.md)
