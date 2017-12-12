---
uid: web-api/overview/web-api-routing-and-actions/create-a-rest-api-with-attribute-routing
title: "Créer une API REST avec l’attribut routage dans ASP.NET Web API 2 | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/26/2013
ms.topic: article
ms.assetid: 23fc77da-2725-4434-99a0-ff872d96336b
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/web-api-routing-and-actions/create-a-rest-api-with-attribute-routing
msc.type: authoredcontent
ms.openlocfilehash: 9ecc233e595716a167ad800a0a21a6162b051648
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="create-a-rest-api-with-attribute-routing-in-aspnet-web-api-2"></a>Créer une API REST avec l’attribut de routage dans l’API Web ASP.NET 2
====================
par [Mike Wasson](https://github.com/MikeWasson)

API Web 2 prend en charge un nouveau type de routage, appelé *attribut routage*. Pour obtenir une vue d’ensemble du routage d’attributs, consultez [routage d’attributs dans l’API Web 2](attribute-routing-in-web-api-2.md). Dans ce didacticiel, vous utiliserez routage d’attributs pour créer une API REST pour une collection de livres. L’API prend en charge les actions suivantes :

| Action | Exemple d’URI |
| --- | --- |
| Obtenir une liste de tous les livres. | documentation/api / |
| Obtenir un livre par ID. | /API/Books/1 |
| Obtenir les détails d’un livre. | /API/Books/1/Details |
| Obtenir une liste de livres par genre. | /API/Books/fantasy |
| Obtenir une liste de livres par date de publication. | /API/Books/date/2013-02-16 /api/books/date/2013/02/16 (autre forme) |
| Obtenir une liste de livres publiés par un auteur particulier. | /API/authors/1/Books |

Toutes les méthodes sont en lecture seule (demandes HTTP GET).

La couche données, nous allons utiliser Entity Framework. Enregistrements de livre possède les champs suivants :

- Id
- Titre
- Genre
- Date de publication
- Prix
- Description
- AuthorID (clé étrangère à une table Authors)

Toutefois, pour la plupart des requêtes, l’API retourne un sous-ensemble de ces données (titre, auteur et genre). Pour obtenir des demandes de l’enregistrement complet, le client `/api/books/{id}/details`.

## <a name="prerequisites"></a>Conditions préalables

[Visual Studio 2017](https://www.visualstudio.com/vs/) Community, Professional ou Enterprise edition.

## <a name="create-the-visual-studio-project"></a>Créer le projet Visual Studio

Commencer par exécuter Visual Studio. À partir de la **fichier** menu, sélectionnez **nouveau** , puis sélectionnez **projet**.

Dans le **modèles** volet, sélectionnez **modèles installés** et développez le **Visual C#** nœud. Sous **Visual C#**, sélectionnez **Web**. Dans la liste des modèles de projet, sélectionnez **ASP.NET MVC 4 Web Application**. Nommez le projet &quot;BooksAPI&quot;.

![](create-a-rest-api-with-attribute-routing/_static/image1.png)

Dans le **nouveau projet ASP.NET** boîte de dialogue, sélectionnez le **vide** modèle. Sous « Ajouter des dossiers et pour les références de base », sélectionnez le **API Web** case à cocher. Cliquez sur **créer le projet**.

![](create-a-rest-api-with-attribute-routing/_static/image2.png)

Cette opération crée un squelette de projet qui est configuré pour la fonctionnalité de l’API Web.

### <a name="domain-models"></a>Modèles de domaine

Ensuite, ajoutez des classes pour les modèles de domaine. Dans l’Explorateur de solutions, cliquez sur le dossier de modèles. Sélectionnez **ajouter**, puis sélectionnez **classe**. Nommez la classe `Author`.

![](create-a-rest-api-with-attribute-routing/_static/image3.png)

Remplacez le code dans Author.cs avec les éléments suivants :

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample1.cs)]

Ajoutez ensuite une autre classe nommée `Book`.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample2.cs)]

### <a name="add-a-web-api-controller"></a>Ajouter un contrôleur d’API Web

Dans cette étape, nous allons ajouter un contrôleur d’API Web qui utilise Entity Framework en tant que la couche données.

Appuyez sur CTRL+MAJ+B pour générer le projet. Entity Framework utilise la réflexion pour découvrir les propriétés des modèles, de sorte qu’elle nécessite un assembly compilé créer le schéma de base de données.

Dans l’Explorateur de solutions, cliquez sur le dossier contrôleurs. Sélectionnez **ajouter**, puis sélectionnez **contrôleur**.

![](create-a-rest-api-with-attribute-routing/_static/image4.png)

Dans le **ajouter une vue de structure** boîte de dialogue, sélectionnez « API Web 2 contrôleur avec actions de lecture/écriture, à l’aide d’Entity Framework. »

[![](create-a-rest-api-with-attribute-routing/_static/image6.png)](create-a-rest-api-with-attribute-routing/_static/image5.png)

Dans le **ajouter un contrôleur** boîte de dialogue, pour **nom de contrôleur**, entrez &quot;BooksController&quot;. Sélectionnez le &quot;utiliser les actions de contrôleur asynchrones&quot; case à cocher. Pour **classe de modèle**, sélectionnez &quot;livre&quot;. (Si vous ne voyez pas la `Book` classe répertorié dans la liste déroulante, assurez-vous que vous avez créé le projet.) Cliquez ensuite sur le bouton « + ».

![](create-a-rest-api-with-attribute-routing/_static/image7.png)

Cliquez sur **ajouter** dans les **nouveau contexte de données** boîte de dialogue.

![](create-a-rest-api-with-attribute-routing/_static/image8.png)

Cliquez sur **ajouter** dans les **ajouter un contrôleur** boîte de dialogue. La génération de modèles automatique ajoute une classe nommée `BooksController` qui définit le contrôleur d’API. Il ajoute également une classe nommée `BooksAPIContext` dans le dossier Modèles, qui définit le contexte de données pour Entity Framework.

![](create-a-rest-api-with-attribute-routing/_static/image9.png)

### <a name="seed-the-database"></a>Valeur initiale de la base de données

Dans le menu Outils, sélectionnez **Gestionnaire de Package de bibliothèque**, puis sélectionnez **Package Manager Console**.

Dans la fenêtre de Console du Gestionnaire de Package, entrez la commande suivante :

[!code-powershell[Main](create-a-rest-api-with-attribute-routing/samples/sample3.ps1)]

Cette commande crée un dossier Migrations et ajoute un nouveau fichier de code nommé Configuration.cs. Ouvrez ce fichier et ajoutez le code suivant à la `Configuration.Seed` (méthode).

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample4.cs)]

Dans la fenêtre de Console du Gestionnaire de Package, tapez les commandes suivantes.

[!code-powershell[Main](create-a-rest-api-with-attribute-routing/samples/sample5.ps1)]

Ces commandes créer une base de données locale et appeler la méthode de la valeur de départ pour remplir la base de données.

![](create-a-rest-api-with-attribute-routing/_static/image10.png)

## <a name="add-dto-classes"></a>Ajouter des Classes DTO

Si vous exécutez l’application maintenant et envoyez une requête GET à /api/books/1, la réponse est similaire à ce qui suit. (J’ai ajouté la mise en retrait pour une meilleure lisibilité).

[!code-json[Main](create-a-rest-api-with-attribute-routing/samples/sample6.json)]

Au lieu de cela, je veux cette requête pour retourner un sous-ensemble des champs. En outre, je le souhaite pour retourner le nom de l’auteur, plutôt que l’ID de l’auteur. Pour ce faire, nous allons modifier les méthodes de contrôleur pour retourner un *objet de transfert de données* (DTO) au lieu du modèle EF. Un DTO est un objet qui est conçu uniquement pour transmettre les données.

Dans l’Explorateur de solutions, cliquez sur le projet et sélectionnez **ajouter** | **nouveau dossier**. Nommez le dossier &quot;DTO&quot;. Ajoutez une classe nommée `BookDto` dans le dossier de données, avec la définition suivante :

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample7.cs)]

Ajouter une autre classe nommée `BookDetailDto`.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample8.cs)]

Ensuite, mettez à jour le `BooksController` classe pour retourner `BookDto` instances. Nous allons utiliser la [Queryable.Select](https://msdn.microsoft.com/en-us/library/system.linq.queryable.select.aspx) méthode au projet `Book` instances à `BookDto` instances. Voici le code de mise à jour de la classe de contrôleur.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample9.cs)]

> [!NOTE]
> J’ai supprimé le `PutBook`, `PostBook`, et `DeleteBook` méthodes, car ils ne sont pas nécessaires pour ce didacticiel.


Maintenant si vous exécutez l’application et demandez /api/books/1, le corps de réponse doit ressembler à ceci :

[!code-json[Main](create-a-rest-api-with-attribute-routing/samples/sample10.json)]

## <a name="add-route-attributes"></a>Ajouter des attributs d’itinéraire

Ensuite, nous allons convertir le contrôleur pour utiliser le routage de l’attribut. Tout d’abord, ajoutez un **RoutePrefix** attribut au contrôleur. Cet attribut définit les segments d’URI initiales pour toutes les méthodes sur ce contrôleur.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample11.cs?highlight=1)]

Ajoutez ensuite **[itinéraire]** d’attributs pour les actions de contrôleur, comme suit :

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample12.cs?highlight=1,7)]

Le modèle d’itinéraire pour chaque méthode de contrôleur est le préfixe ainsi que la chaîne spécifiée dans le **itinéraire** attribut. Pour le `GetBook` méthode, le modèle d’itinéraire inclut la chaîne paramétrable &quot;{id : int}&quot;, qui met en correspondance si le segment de l’URI contient une valeur entière.

| Méthode | Modèle d’itinéraire | Exemple d’URI |
| --- | --- | --- |
| `GetBooks` | « api/books » | `http://localhost/api/books` |
| `GetBook` | « api/la documentation / {id : int} » | `http://localhost/api/books/5` |

## <a name="get-book-details"></a>Obtenir les détails de l’annuaire

Pour obtenir des détails de l’annuaire, le client envoie une demande GET pour `/api/books/{id}/details`, où *{id}* est l’ID de l’annuaire.

Ajoutez la méthode suivante à la classe `BooksController`.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample13.cs)]

Si vous demandez `/api/books/1/details`, la réponse ressemble à ceci :

[!code-json[Main](create-a-rest-api-with-attribute-routing/samples/sample14.json)]

## <a name="get-books-by-genre"></a>Obtenir la documentation par Genre

Pour obtenir une liste de livres d’un genre spécifique, le client envoie une demande GET pour `/api/books/genre`, où *genre* est le nom de la genre. (Par exemple, `/get/books/fantasy`.)

Ajoutez la méthode suivante à `BooksController`.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample15.cs)]

Ici, nous allons définir un itinéraire qui contient un paramètre {genre} dans le modèle d’URI. Notez que l’API Web est en mesure de distinguer ces deux URI et de les acheminer vers les différentes méthodes :

`/api/books/1`

`/api/books/fantasy`

C’est parce que le `GetBook` méthode inclut une contrainte que le segment « id » doit être une valeur entière :

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample16.cs?highlight=1)]

Si vous demandez /api/books/fantasy, la réponse ressemble à ceci :

`[ { "Title": "Midnight Rain", "Author": "Ralls, Kim", "Genre": "Fantasy" }, { "Title": "Maeve Ascendant", "Author": "Corets, Eva", "Genre": "Fantasy" }, { "Title": "The Sundered Grail", "Author": "Corets, Eva", "Genre": "Fantasy" } ]`

## <a name="get-books-by-author"></a>Obtenir la documentation par l’auteur

Pour obtenir une liste d’une documentation pour un auteur particulier, le client envoie une demande GET pour `/api/authors/id/books`, où *id* est l’ID de l’auteur.

Ajoutez la méthode suivante à `BooksController`.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample17.cs)]

Cet exemple est intéressant, car &quot;la documentation&quot; est traité une ressource enfant de &quot;auteurs&quot;. Ce modèle est assez courant dans les API RESTful.

Le tilde (~) dans le modèle d’itinéraire remplace le préfixe d’itinéraire dans la **RoutePrefix** attribut.

## <a name="get-books-by-publication-date"></a>Obtenir la documentation par Date de Publication

Pour obtenir une liste de livres par date de publication, le client envoie une demande GET pour `/api/books/date/yyyy-mm-dd`, où *aaaa-mm-jj* correspond à la date.

Voici une méthode pour ce faire :

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample18.cs)]

Le `{pubdate:datetime}` paramètre doit correspondre à un **DateTime** valeur. Cela fonctionne, mais elle est en fait plus permissive que nous souhaitons. Par exemple, ces URI aussi à l’itinéraire :

`/api/books/date/Thu, 01 May 2008`

`/api/books/date/2000-12-16T00:00:00`

Il n’existe aucun problème à l’autorisation de ces URI. Toutefois, vous pouvez restreindre l’itinéraire dans un format spécifique en ajoutant une contrainte d’expression régulière pour le modèle d’itinéraire :

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample19.cs?highlight=1)]

Désormais uniquement les dates sous la forme &quot;aaaa-mm-jj&quot; correspondra. Notez que nous n’utilisons pas l’expression régulière pour valider que nous avons obtenu une date réelle. Qui est géré lors de l’API Web essaie de convertir le segment d’URI dans un **DateTime** instance. Une date non valide, tel que « 2012-47-99 » ne pourra pas être converti, et le client obtiendra une erreur 404.

Vous pouvez prennent également en charge un séparateur de barre oblique (`/api/books/date/yyyy/mm/dd`) en ajoutant un autre **[itinéraire]** attribut avec un autre regex.

[!code-html[Main](create-a-rest-api-with-attribute-routing/samples/sample20.html)]

Il existe un détail subtil mais important ici. Le second modèle d’itinéraire comporte un caractère générique (\*) au début du paramètre {pubdate} :

[!code-json[Main](create-a-rest-api-with-attribute-routing/samples/sample21.json)]

Cela indique au moteur de routage que pubdate {} doit correspondent au reste de l’URI. Par défaut, un paramètre de modèle correspond à un seul segment d’URI. Dans ce cas, nous souhaitons {pubdate} s’étendent sur plusieurs segments d’URI :

`/api/books/date/2013/06/17`

## <a name="controller-code"></a>Code du contrôleur

Voici le code complet pour la classe BooksController.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample22.cs)]

## <a name="summary"></a>Résumé

Routage d’attributs vous donne davantage de contrôle et une plus grande souplesse lors de la conception de l’URI de votre API.
