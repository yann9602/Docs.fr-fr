---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/displaying-data
title: "Présentation des Pages Web ASP.NET - affichage des données | Documents Microsoft"
author: tfitzmac
description: "Ce didacticiel vous montre comment créer une base de données dans WebMatrix et comment afficher les données de la base de données dans une page lorsque vous utilisez les Pages Web ASP.NET (Razor). Il suppose y..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/28/2015
ms.topic: article
ms.assetid: b3a006a0-3ea2-4d45-b833-e20e3a3c0a1a
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/displaying-data
msc.type: authoredcontent
ms.openlocfilehash: fdb9af0ba87c7802c63451ac7aa422e0020b5719
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="introducing-aspnet-web-pages---displaying-data"></a>Présentation des Pages Web ASP.NET - affichage des données
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Ce didacticiel vous montre comment créer une base de données dans WebMatrix et comment afficher les données de la base de données dans une page lorsque vous utilisez les Pages Web ASP.NET (Razor). Il suppose que vous avez terminé la série via [Introduction à la programmation de Pages Web ASP.NET](../introducing-razor-syntax-c.md).
> 
> Ce que vous allez apprendre :
> 
> - Comment utiliser les outils de WebMatrix pour créer une base de données et des tables de base de données.
> - Comment utiliser les outils de WebMatrix pour ajouter des données à une base de données.
> - Comment afficher des données à partir de la base de données sur une page.
> - Comment exécuter des commandes SQL dans ASP.NET Web Pages.
> - Comment personnaliser le `WebGrid` application d’assistance pour modifier l’affichage de données et ajouter la pagination et le tri.
>   
> 
> Fonctionnalités technologies abordées :
> 
> - Outils de base de données de WebMatrix.
> - `WebGrid`application d’assistance.


## <a name="what-youll-build"></a>Ce que vous allez générer

Dans le didacticiel précédent, vous ont été introduites pour les Pages Web ASP.NET (*.cshtml* fichiers), les principes fondamentaux de la syntaxe Razor et programmes d’assistance. Dans ce didacticiel, vous allez commencer à créer l’application web réelle que vous allez utiliser pour le reste de la série. L’application est une application de film simple qui vous permet d’afficher, ajouter, modifier et supprimer des informations sur les films.

Lorsque vous avez terminé ce didacticiel, vous serez en mesure d’afficher une liste de films qui ressemble à celle-ci :

![Affichage de WebGrid qui inclut les paramètres définis pour les noms de classe CSS](displaying-data/_static/image1.png)

Mais pour commencer, vous devez créer une base de données.

## <a name="a-very-brief-introduction-to-databases"></a>Une très brève Introduction aux bases de données

Ce didacticiel fournit uniquement l’introduction et aux bases de données. Si vous disposez de bases de données, vous pouvez ignorer cette section courte.

Une base de données contient une ou plusieurs tables qui contiennent des informations &mdash; par exemple, les tables pour les clients, les commandes et les fournisseurs, ou pour les étudiants, les enseignants, les classes et les notes. Point de vue structurel, une table de base de données est une feuille de calcul. Imaginez un carnet d’adresses par défaut. Pour chaque entrée dans le carnet d’adresses (autrement dit, pour chaque personne) vous avez plusieurs éléments d’informations, telles que le prénom, nom, adresse, adresse de messagerie et numéro de téléphone.

![Exemple de table de base de données comme une grille simple](displaying-data/_static/image2.png)

(Lignes sont parfois appelés *enregistrements*, et les colonnes sont parfois appelées *champs*.)

Pour la plupart des tables de base de données, la table doit avoir une colonne qui contient une valeur unique, comme un numéro de client, numéro de compte et ainsi de suite. Cette valeur est appelée la table *clé primaire*, et il permet d’identifier chaque ligne dans la table. Dans l’exemple, la colonne ID est la clé primaire pour le carnet d’adresses indiqué dans l’exemple précédent.

Une grande partie du travail que vous effectuez dans les applications web se compose de la lecture des informations de la base de données et de les afficher sur une page. Vous allez également souvent collecter des informations auprès des utilisateurs et l’ajouter à une base de données, ou vous devez modifier les enregistrements qui se trouvent déjà dans la base de données. (Nous aborderons toutes ces opérations au cours de l’ensemble de ce didacticiel.)

Travail de la base de données peut être très complexe et nécessite des connaissances spécialisées. Cependant, pour le jeu de ce didacticiel, vous devez comprendre uniquement concepts de base, qui seront tous expliquées que vous avancez.

## <a name="creating-a-database"></a>Création d’une base de données

WebMatrix inclut des outils qui facilitent la création d’une base de données et créer des tables dans la base de données. (La structure d’une base de données est appelée à la base de données *schéma*.) Pour le jeu de ce didacticiel, vous allez créer une base de données qui contient une seule table &mdash; films.

Ouvrez WebMatrix si vous n’avez pas déjà fait et ouvrez le site WebPagesMovies que vous avez créé dans le didacticiel précédent.

Dans le volet gauche, cliquez sur le **base de données** espace de travail.

![Onglet espace de travail de base de données de WebMatrix](displaying-data/_static/image3.png)

Les modifications de ruban pour afficher les tâches liées à la base de données. Dans le ruban, cliquez sur **nouvelle base de données**.

![Bouton de « Nouvelle base de données » dans le ruban de WebMatrix](displaying-data/_static/image4.png)

WebMatrix crée une base de données SQL Server CE (un *.sdf* fichier) qui porte le même nom que votre site &mdash; *WebPagesMovies.sdf*. (Ne sera pas cela ici, mais vous pouvez renommer le fichier à votre convenance, pourvu qu’il ait une *.sdf* extension.)

## <a name="creating-a-table"></a>Création d’une Table

Dans le ruban, cliquez sur **nouvelle Table**. WebMatrix s’ouvre le Concepteur de tables dans un nouvel onglet. (Si l’option de la nouvelle Table n’est pas disponible, assurez-vous que la nouvelle base de données est sélectionné dans l’arborescence à gauche.)

![« Nouvelle Table » situé dans le ruban de WebMatrix](displaying-data/_static/image5.png)

Dans la zone de texte en haut (où le filigrane indique « Entrez le nom de table »), entrez « Films ».

![Entrez un nom de table dans le Concepteur de base de données de WebMatrix](displaying-data/_static/image6.png)

Le volet situé sous le nom de la table est où vous définissez des colonnes individuelles. Pour le *films* table dans ce didacticiel, vous allez créer uniquement certaines colonnes : *ID*, *titre*, *Genre*, et *année*.

Dans le **nom** , entrez « ID ». Entrez une valeur ici active tous les contrôles de la nouvelle colonne.

Onglet à la **Type de données** liste et choisissez **int**. Cette valeur spécifie que la colonne ID contiendra les données de type integer (nombre).

> [!NOTE]
> Nous ne l’appeler toutes les plus ici (beaucoup), mais vous pouvez utiliser des mouvements de clavier Windows standards pour naviguer dans cette grille. Par exemple, vous pouvez accédez par tabulation entre les champs, vous pouvez simplement commencez à taper afin de sélectionner un élément dans une liste et ainsi de suite.


Onglet au-delà de la **valeur par défaut** boîte (autrement dit, laissez ce champ vide). Onglet à la **est la clé primaire** case à cocher et sélectionnez-le. Cette option indique à la base de données qui le *ID* colonne contiendra les données qui identifie les lignes individuelles. (Autrement dit, chaque ligne aura une valeur unique dans la colonne ID que vous pouvez utiliser pour rechercher cette ligne.)

Choisissez le **est d’identité** option. Cette option indique à la base de données qu’il doit générer automatiquement le numéro séquentiel suivant pour chaque nouvelle ligne. (Le **est d’identité** option fonctionne uniquement si vous avez également choisi le **est la clé primaire** option.)

Cliquez sur la ligne de grille suivante, ou appuyez sur Tab à deux reprises pour terminer la ligne actuelle. Un mouvement enregistre la ligne actuelle et commence celle qui suit. Notez que la **valeur par défaut** colonne indique désormais **Null**. (Null est la valeur par défaut pour la valeur par défaut, pour ainsi dire.)

Lorsque vous avez terminé la définition de la nouvelle **ID** colonne, le concepteur ressemblera cette illustration :

![Concepteur de base de données de WebMatrix après la définition de la colonne d’ID pour la table de films](displaying-data/_static/image7.png)

Pour créer la colonne suivante, cliquez dans la zone dans le **nom** colonne. Entrez « Title » pour la colonne, puis sélectionnez **nvarchar** pour le **Type de données** valeur. La partie « var » de **nvarchar** indique à la base de données que les données de cette colonne sera une chaîne dont la taille peut varier d’un enregistrement à l’autre. (Le préfixe « n » représente « national », ce qui signifie que le champ peut contenir toute lettre ou le système d’écriture des données caractère : autrement dit, le champ contient des données Unicode.)

Lorsque vous choisissez **nvarchar**, une autre zone s’affiche dans laquelle vous pouvez entrer la longueur maximale du champ. Entrez 50, sur l’hypothèse qu’aucun titre du film que vous utiliserez dans ce didacticiel n’y a plus de 50 caractères.

Ignorer **valeur par défaut** et désactivez la **autoriser les valeurs null** option. Vous ne souhaitez pas autoriser des films doit être entrée dans la base de données qui n’ont pas un titre de la base de données.

Lorsque vous avez terminé et passez à la ligne suivante, le concepteur se présente comme dans cette illustration :

![Concepteur de base de données de WebMatrix après la définition de la colonne de titre pour la table de films](displaying-data/_static/image8.png)

Répétez ces étapes pour créer une colonne nommée « Genre », à l’exception de la longueur, affectez-lui la valeur 30 uniquement.

Créer une autre colonne nommée « Year ». Pour le type de données, choisissez **nchar** (pas **nvarchar**) et la longueur de la valeur 4. Pour l’année, vous allez utiliser un numéro à 4 chiffres tels que « 1995 » ou « 2010 », donc vous ne nécessitent pas une colonne de taille variable.

Voici à quoi ressemble la conception :

![WebMatrix Concepteur de base de données une fois que tous les champs sont définis pour la table de films](displaying-data/_static/image9.png)

Appuyez sur Ctrl + S ou cliquez sur le **enregistrer** bouton dans la barre d’outils Accès rapide. Fermez le Concepteur de base de données en fermant l’onglet.

## <a name="adding-some-example-data"></a>Ajout des données d’exemple

Plus loin dans cette série de didacticiels, vous allez créer une page où vous pouvez entrer des nouveaux films dans un formulaire. Toutefois, pour l’instant, vous pouvez ajouter des données d’exemple que vous pouvez ensuite afficher sur une page.

Dans le **base de données** espace de travail dans WebMatrix, notez qu’il existe une arborescence qui vous montre les *.sdf* fichier que vous avez créé précédemment. Ouvrez le nœud pour votre nouveau *.sdf* de fichier, puis ouvrez le **Tables** nœud.

![Espace de travail de base de données de WebMatrix avec arborescence ouverte à la table de films](displaying-data/_static/image10.png)

Cliquez sur le **films** nœud, puis choisissez **données**. WebMatrix ouvre une grille dans laquelle vous pouvez entrer des données pour le *films* table :

![Grille d’entrée de base de données dans WebMatrix (vide)](displaying-data/_static/image11.png)

Cliquez sur le **titre** colonne, puis entrez « Lorsque Harry remplies Sarah ». Déplacer vers le **Genre** colonne (vous pouvez utiliser la touche Tab) et entrez « Est si romantique comédie ». Déplacer vers le **année** colonne, puis entrez « 1989 » :

![Grille d’entrée de base de données dans WebMatrix avec un seul enregistrement](displaying-data/_static/image12.png)

Appuyez sur entrée et WebMatrix enregistre la nouvelle séquence. Notez que la **ID** colonne a été renseignée.

![Grille d’entrée de base de données dans WebMatrix avec un seul enregistrement et l’ID généré automatiquement](displaying-data/_static/image13.png)

Entrez une autre séquence (par exemple, « passées avec le vent », « Documentaires », « 1939 »). La colonne ID est de nouveau remplie :

![Grille d’entrée de base de données dans WebMatrix avec deux enregistrements et les ID généré automatiquement](displaying-data/_static/image14.png)

Entrez un film tiers (par exemple, « Ghostbusters », « Comédie »). L’expérience, laissez le **année** colonne vide et appuyez sur ENTRÉE. Étant donné que vous désélectionnez le **autoriser les valeurs null** option, la base de données affiche une erreur :

![Erreur de « Données non valide » affiche si une valeur de la colonne requise est vide.](displaying-data/_static/image15.png)

Cliquez sur **OK** pour revenir en arrière et corriger l’entrée (l’année pour « Ghostbusters » est 1984) et appuyez sur ENTRÉE.

Renseignez plusieurs films jusqu'à ce que vous ayez 8 ou donc. (Saisie 8 rend plus facile de travailler avec la pagination plus tard. Mais si c’est trop grand nombre, entrer quelques exemples pour le moment.) Peu importe les données réelles.

![Grille d’entrée de base de données dans WebMatrix avec deux enregistrements.](displaying-data/_static/image16.png)

Si vous avez entré toutes les vidéos sans erreurs, les valeurs d’ID sont séquentielles. Si vous avez essayé d’enregistrer un enregistrement vidéo incomplet, les numéros d’identification n’est peut-être pas séquentiels. Dans ce cas, c’est OK. Les nombres n’ont aucune signification inhérente, et la seule chose qui est importante est qu’ils sont uniques dans le *films* table.

Fermez l’onglet qui contient le Concepteur de base de données.

Vous pouvez maintenant activer à l’affichage de ces données sur une page web.

## <a name="displaying-data-in-a-page-by-using-the-webgrid-helper"></a>Affichage des données dans une Page à l’aide du programme d’assistance WebGrid

Pour afficher des données dans une page, vous allez utiliser le `WebGrid` helper. Ce programme d’assistance produisant un affichage dans une grille ou une table (lignes et colonnes). Comme vous le verrez, vous serez en mesure de redéfinir la grille avec mise en forme et d’autres fonctionnalités.

Pour exécuter la grille, vous devrez écrire quelques lignes de code. Ces quelques lignes servira à un type de modèle pour la quasi-totalité de l’accès aux données que vous effectuez dans ce didacticiel.

> [!NOTE]
> En réalité de nombreuses options pour afficher les données sur une page ; le `WebGrid` d’assistance est qu’une seule. A été choisi pour ce didacticiel, car il est le moyen le plus simple pour afficher des données et qu’il est assez flexible. Dans le didacticiel suivantes, vous allez apprendre à utiliser une méthode plus « manual » pour utiliser des données dans la page, ce qui vous donne un contrôle plus direct sur la façon d’afficher les données.


Dans le volet gauche de WebMatrix, cliquez sur le **fichiers** espace de travail.

La nouvelle base de données que vous avez créé est dans le *application\_données* dossier. Si le dossier n’existe pas déjà, WebMatrix créé pour votre nouvelle base de données. (Le dossier peut avoir existé si vous aviez précédemment installé des applications auxiliaires.)

Dans l’arborescence, sélectionnez la racine du site Web. Vous devez sélectionner la racine du site Web ; Sinon, le nouveau fichier peut être ajouté à l’application\_dossier de données.

Dans le ruban, cliquez sur **nouveau**. Dans le **choisir un Type de fichier** , choisissez **CSHTML**.

Dans le **nom** zone, nommez la nouvelle page « Movies.cshtml » :

![Boîte de dialogue « Choisir un Type de fichier » affichant la page « Films »](displaying-data/_static/image17.png)

Cliquez sur le **OK** bouton. WebMatrix ouvre un nouveau fichier avec des éléments squelettes qu’elle contient. Tout d’abord, vous allez écrire du code pour obtenir les données de la base de données. Puis vous allez ajouter le balisage à la page pour afficher les données.

### <a name="writing-the-data-query-code"></a>L’écriture du Code de requête de données

En haut de la page, entre le `@{` et `}` caractères, entrez le code suivant. (Assurez-vous que vous entrez ce code entre les accolades ouvrantes et fermantes.)

[!code-csharp[Main](displaying-data/samples/sample1.cs)]

La première ligne s’ouvre à la base de données que vous avez créé précédemment, qui est toujours la première étape avant de procéder à un élément avec la base de données. Vous indiquez le `Database.Open` nom de la méthode de la base de données à ouvrir. Notez que vous n’incluez pas *.sdf* dans le nom. Le `Open` méthode part du principe qu’il recherche une *.sdf* fichier (autrement dit, *WebPagesMovies.sdf*) et que le *.sdf* fichier se trouve dans le *application\_ Données* dossier. (Précédemment, nous avons vu que le *application\_données* dossier est réservé ; ce scénario est un des emplacements où ASP.NET émet des hypothèses sur ce nom.)

Lorsque la base de données est ouverte, une référence à celui-ci est placée dans la variable nommée `db`. (Ce qui peut être n’importe quel nom.) Le `db` variable est que vous obtiendrez comment interagir avec la base de données.

La deuxième ligne extrait réellement les données de la base de données à l’aide de la `Query` (méthode). Notez le fonctionne de ce code : le `db` variable a une référence à la base de données ouvert et que vous appelez le `Query` méthode à l’aide de la `db` variable (`db.Query`).

La requête elle-même est SQL `Select` instruction. (Pour un peu d’informations sur SQL, voir l’explication ultérieurement). Dans l’instruction, `Movies` identifie la table à la requête. Le `*` caractère spécifie que la requête doit retourner toutes les colonnes de la table. (Vous pouvez également répertorier colonnes individuellement, séparés par des virgules.)

Les résultats de la requête, le cas échéant, sont retournés et mis à disposition dans le `selectedData` variable. Là encore, la variable peut être n’importe quel nom.

Enfin, la troisième ligne indique à ASP.NET que vous souhaitez utiliser une instance de la `WebGrid` helper. Vous créez (*instancier*) l’objet d’assistance à l’aide de la `new` (mot clé) et lui passer les résultats de la requête via le `selectedData` variable. La nouvelle `WebGrid` objet, ainsi que les résultats de la requête de base de données, sont mis à disposition dans le `grid` variable. Vous aurez besoin de ce résultat dans un instant pour afficher les données dans la page.

À ce stade, la base de données a été ouverte, vous avez obtenu les données souhaitées, et que vous avez préparé la `WebGrid` programme d’assistance avec ces données. Est ensuite pour créer le balisage dans la page.

> [!TIP] 
> 
> **Structured Query Language (SQL)**
> 
> SQL est un langage qui est utilisé dans la plupart des bases de données relationnelles pour la gestion des données dans une base de données. Il comprend des commandes qui vous permettent de récupérer des données et mettre à jour, et qui vous permettent de créer, modifier et gérer les données dans les tables de base de données. SQL est différent de celui d’un langage de programmation (tels que c#). Avec SQL, vous indiquez à la base de données de ce que vous souhaitez, et travail de la base de données consiste à déterminer comment obtenir les données ou d’effectuer la tâche. Voici des exemples de certaines commandes SQL et à quoi ils :
> 
> `Select * From Movies`
> 
> `SELECT ID, Name, Price FROM Product WHERE Price > 10.00 ORDER BY Name`
> 
> La première `Select` instruction Obtient toutes les colonnes (spécifié par `*`) à partir de la *films* table.
> 
> La seconde `Select` instruction extrait les colonnes ID, nom et prix à partir d’enregistrements dans la *produit* table dont valeur de la colonne prix est supérieur à 10. La commande retourne les résultats par ordre alphabétique selon les valeurs de la colonne de nom. Si aucun enregistrement correspondent aux critères de prix, la commande retourne un jeu vide.
> 
> `INSERT INTO Product (Name, Description, Price) VALUES ('Croissant', 'A flaky delight', 1.99)`
> 
> Cette commande insère un nouvel enregistrement dans le *produit* table, la définition de la colonne de nom pour le prix, la colonne de Description pour « Plaisir d’instable A » et « Croissant » à 1,99.
> 
> Notez que lorsque vous spécifiez une valeur non numérique, la valeur est placée entre guillemets simples (pas guillemets doubles, comme dans c#). Vous utilisez ces guillemets autour des valeurs de texte ou de date, mais pas autour des nombres.
> 
> `DELETE FROM Product WHERE ExpirationDate < '01/01/2008'`
> 
> Cette commande supprime les enregistrements dans la *produit* table dont colonne de date d’expiration est antérieure au 1er janvier 2008. (La commande suppose que le *produit* table a une telle colonne, bien sûr.) La date est entrée ici dans le format MM/jj/aaaa, mais il doit être entré au format qui est utilisé pour vos paramètres régionaux.
> 
> Le `Insert` et `Delete` commandes ne retournent des jeux de résultats. Au lieu de cela, elles retournent un nombre qui indique le nombre d’enregistrements qui ont été affecté par la commande.
> 
> Pour certaines de ces opérations (comme l’insertion et de suppression d’enregistrements), le processus qui demande l’opération doit disposer des autorisations appropriées dans la base de données. C’est pourquoi, pour les bases de données vous devez souvent fournir un nom d’utilisateur et un mot de passe lorsque vous vous connectez à la base de données.
> 
> Il existe des dizaines de commandes SQL, mais elles suivent un modèle, comme les commandes que vous voyez ici. Vous pouvez utiliser des commandes SQL pour créer des tables de base de données, le nombre d’enregistrements dans une table, calculer les prix et effectuer de nombreuses opérations plus.


### <a name="adding-markup-to-display-the-data"></a>Ajout de balisage pour afficher les données

À l’intérieur de la `<head>` élément, le contenu de l’ensemble de la `<title>` élément « Cinéma » :

[!code-html[Main](displaying-data/samples/sample2.html?highlight=3)]

À l’intérieur du `<body>` élément de la page, ajoutez le code suivant :

[!code-html[Main](displaying-data/samples/sample3.html)]

C'est tout ! Le `grid` variable est la valeur que vous avez créé le `WebGrid` objet dans le code précédemment.

Dans l’arborescence de WebMatrix, avec le bouton droit de la page, puis sélectionnez **lancer dans un navigateur**. Vous verrez quelque chose qu’à cette page :

![Sortie de d’assistance WebGrid par défaut de la table de films](displaying-data/_static/image18.png)

Cliquez sur un lien de titre de colonne pour trier par colonne. Pour trier en cliquant sur un en-tête est une fonctionnalité qui est intégrée à la **WebGrid** helper.

Le `GetHtml` (méthode), comme son nom l’indique, génère un balisage qui affiche les données. Par défaut, le `GetHtml` méthode génère un élément HTML `<table>` élément. (Si vous le souhaitez, vous pouvez vérifier le rendu en examinant la source de la page dans le navigateur.)

## <a name="modifying-the-look-of-the-grid"></a>Modification de l’apparence de la grille

À l’aide de la `WebGrid` application d’assistance que vous venez de faire est facile, mais l’affichage résultant est simple. Le `WebGrid` helper a toutes sortes d’options qui vous permettent de contrôler comment les données sont affichées. Il existe trop nombreux pour Explorer dans ce didacticiel, mais cette section vous donnera une idée de certaines de ces options. Quelques options supplémentaires seront abordées dans les didacticiels plus loin dans cette série.

### <a name="specifying-individual-columns-to-display"></a>Spécification des colonnes à afficher

Pour commencer, vous pouvez spécifier que vous souhaitez afficher uniquement certaines colonnes. Par défaut, comme vous l’avez vu, la grille affiche les quatre colonnes de la *films* table.

Dans le *Movies.cshtml* du fichier, remplacez le `@grid.GetHtml()` balisage que vous venez d’ajouter par le code suivant :

[!code-css[Main](displaying-data/samples/sample4.css)]

Pour indiquer les colonnes à afficher à l’application d’assistance, vous incluez un `columns` paramètre pour le `GetHtml` (méthode) et passez une collection de colonnes. Dans la collection, vous spécifiez chaque colonne à inclure. Vous spécifiez une colonne individuelle à afficher en incluant un `grid.Column` de l’objet et passer le nom de la colonne de données que vous souhaitez. (Ces colonnes doivent être incluses dans les résultats de la requête SQL : le `WebGrid` helper ne peut pas afficher les colonnes qui n’ont pas été renvoyés par la requête.)

Lancer le *Movies.cshtml* page dans le navigateur et cette fois, vous obtenez un affichage semblable à celui ci-dessous (Notez qu’aucune colonne d’ID n’est affiché) :

![Affichage de WebGrid affichant uniquement les colonnes sélectionnées](displaying-data/_static/image19.png)

### <a name="changing-the-look-of-the-grid"></a>Changer l’aspect de la grille

Il existe quelques autres options pour l’affichage des colonnes, certains d'entre eux seront présentées dans des didacticiels ultérieurs dans ce jeu. Pour l’instant, cette section présente les façons dans lequel vous pouvez appliquer un style de la grille dans son ensemble.

À l’intérieur de la `<head>` section de la page, juste avant la fermeture `</head>` de balise, ajoutez le code suivant `<style>` élément :

[!code-css[Main](displaying-data/samples/sample5.css)]

Ce balisage CSS définit les classes nommées `grid`, `head`, et ainsi de suite. Vous pouvez également placer ces définitions de style dans une fonction *.css* de fichier et le lier à la page. (En fait, vous allez faire plus loin dans ce didacticiel jeu.) Mais pour vous simplifier pour ce didacticiel, ils sont à l’intérieur de la même page qui affiche les données.

Vous pouvez désormais le `WebGrid` application d’assistance pour utiliser ces classes de style. Le programme d’assistance a un nombre de propriétés (par exemple, `tableStyle`) dans ce but, vous leur attribuez un nom de classe de style CSS, et ce nom de classe est rendu en tant que partie du balisage restitué par l’application d’assistance.

Modifier la `grid.GetHtml` balisage afin qu’il ressemble maintenant à ce code :

[!code-css[Main](displaying-data/samples/sample6.css)]

Quelles sont les nouveautés ici est que vous avez ajouté `tableStyle`, `headerStyle`, et `alternatingRowStyle` paramètres à la `GetHtml` (méthode). Ces paramètres ont été définis pour les noms des styles CSS que vous avez ajouté à un moment où il y a.

Exécuter la page, et cette fois, vous voyez une grille qui ressemble beaucoup moins brut qu’avant :

![Affichage de WebGrid qui inclut les paramètres définis pour les noms de classe CSS](displaying-data/_static/image20.png)

Pour voir quels sont les `GetHtml` méthode générée, vous pouvez consulter la source de la page dans le navigateur. Nous n’entrerons pas dans les détails ici, mais le point important est que, en spécifiant des paramètres tels que `tableStyle`, vous a provoqué la grille générer des balises HTML comme suit :

`<table class="grid">`

Le `<table>` tag a eu un `class` attribut ajouté à celle-ci qui fait référence à l’une des règles CSS que vous avez ajouté précédemment. Ce code vous montre le modèle de base &mdash; des paramètres différents pour la `GetHtml` méthode let vous référence CSS classes que la méthode génère ensuite avec le balisage. Que pouvez-vous faire avec les classes CSS vous revient.

## <a name="adding-paging"></a>Ajout de pagination

En tant que la dernière tâche pour ce didacticiel, vous allez ajouter la pagination à la grille. Pour l’instant ne pose aucun problème pour afficher tous vos films à la fois. Mais si vous avez ajouté des centaines de films, l’affichage de la page est long.

Dans le code de la page, modifiez la ligne qui crée le `WebGrid` objet au code suivant :

[!code-csharp[Main](displaying-data/samples/sample7.cs)]

La seule différence d’avant est que vous avez ajouté un `rowsPerPage` paramètre a la valeur 3.

Exécutez la page. La grille affiche les 3 lignes à une heure, ainsi que des liens de navigation qui vous permettent de parcourir sur les films dans votre base de données :

![Affichage de WebGrid avec pagination](displaying-data/_static/image21.png)

## <a name="coming-up-next"></a>Prochaine

Dans l’étape suivante du didacticiel, vous allez apprendre à utiliser le code Razor et c# pour obtenir l’entrée d’utilisateur dans un formulaire. Vous allez ajouter une zone de recherche à la page de films afin que vous pouvez rechercher les films par titre ou par genre.

## <a name="complete-listing-for-movies-page"></a>Liste complète de la Page de films

[!code-cshtml[Main](displaying-data/samples/sample8.cshtml)]

## <a name="additional-resources"></a>Ressources supplémentaires

- [Introduction à la programmation Web ASP.NET à l’aide de la syntaxe Razor](https://go.microsoft.com/fwlink/?LinkID=202890)

>[!div class="step-by-step"]
[Précédent](intro-to-web-pages-programming.md)
[Suivant](form-basics.md)
