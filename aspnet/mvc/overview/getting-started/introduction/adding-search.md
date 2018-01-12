---
uid: mvc/overview/getting-started/introduction/adding-search
title: Recherche | Documents Microsoft
author: Rick-Anderson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/22/2015
ms.topic: article
ms.assetid: df001954-18bf-4550-b03d-43911a0ea186
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-search
msc.type: authoredcontent
ms.openlocfilehash: 10457d154f5fda875f7d1054d48daeeba3a50b7c
ms.sourcegitcommit: 2b263e87217658caa42eedc4f9d2d21ef0ab5d59
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
<a name="search"></a>Rechercher
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

[!INCLUDE[Tutorial Note](sample/code-location.md)]

## <a name="adding-a-search-method-and-search-view"></a>Ajout d’une méthode de recherche et l’affichage de la recherche

Dans cette section, vous allez ajouter des capacités de recherche à la `Index` méthode d’action qui vous permet de rechercher des films par nom ou par genre.

## <a name="updating-the-index-form"></a>Mise à jour de la forme d’Index

Commencez par mettre à jour le `Index` à l’objet de méthode d’action `MoviesController` classe. Voici le code :

[!code-csharp[Main](adding-search/samples/sample1.cs?highlight=1,6-9)]

La première ligne de la `Index` méthode crée les éléments suivants [LINQ](https://msdn.microsoft.com/en-us/library/bb397926.aspx) requête pour sélectionner les films :

[!code-csharp[Main](adding-search/samples/sample2.cs)]

La requête est définie à ce stade, mais n’a pas encore été exécutée sur la base de données.

Si le `searchString` paramètre contient une chaîne, la requête de films est modifiée pour filtrer sur la valeur de la chaîne de recherche, en utilisant le code suivant :

[!code-csharp[Main](adding-search/samples/sample3.cs)]

Le code `s => s.Title` ci-dessus est une [expression lambda](https://msdn.microsoft.com/en-us/library/bb397687.aspx). Les lambdas sont utilisés dans fondée sur une méthode [LINQ](https://msdn.microsoft.com/en-us/library/bb397926.aspx) interroge en tant qu’arguments à des méthodes d’opérateur de requête standard telles que la [où](https://msdn.microsoft.com/en-us/library/system.linq.enumerable.where.aspx) méthode utilisée dans le code ci-dessus. Les requêtes LINQ ne sont pas exécutées lorsqu’ils sont définis, ou lorsqu’il est modifié en appelant une méthode comme `Where` ou `OrderBy`. Au lieu de cela, l’exécution de la requête est différée, ce qui signifie que l’évaluation d’une expression est retardée jusqu'à ce que sa valeur réalisé est réellement itéré ou [ `ToList` ](https://msdn.microsoft.com/en-us/library/bb342261.aspx) méthode est appelée. Dans le `Search` exemple, la requête est exécutée dans le *Index.cshtml* vue. Pour plus d’informations sur l’exécution différée des requêtes, consultez [Exécution de requête](https://msdn.microsoft.com/en-us/library/bb738633.aspx).

> [!NOTE]
> Le [Contains](https://msdn.microsoft.com/en-us/library/bb155125.aspx) méthode est exécutée sur la base de données, pas le code c# ci-dessus. Sur la base de données [contient](https://msdn.microsoft.com/en-us/library/bb155125.aspx) est mappé à [SQL LIKE](https://msdn.microsoft.com/en-us/library/ms179859.aspx), qui respecte la casse.

Maintenant vous pouvez mettre à jour le `Index` vue qui affiche le formulaire à l’utilisateur.

Exécutez l’application et accédez à *films/Index*. Ajoutez une chaîne de requête comme `?searchString=ghost` à l’URL. Les films filtrés sont affichés.

![SearchQryStr](adding-search/_static/image1.png)

Si vous modifiez la signature de la `Index` méthode comme ayant un paramètre nommé `id`, le `id` paramètre correspondra à la `{id}` achemine les espace réservé pour la valeur par défaut définie dans le *application\_Start RouteConfig.cs* fichier.

[!code-json[Main](adding-search/samples/sample4.json)]

La version d’origine `Index` méthode ressemble à ceci :

[!code-csharp[Main](adding-search/samples/sample5.cs)]

Modifié `Index` méthode se présente comme suit :

[!code-csharp[Main](adding-search/samples/sample6.cs?highlight=1,3)]

Vous pouvez maintenant passer le titre de la recherche en tant que données de routage (un segment de l’URL) et non pas en tant que valeur de chaîne de requête.

![](adding-search/_static/image2.png)

Cependant, vous ne pouvez pas attendre des utilisateurs qu’ils modifient l’URL à chaque fois qu’ils veulent rechercher un film. Donc, maintenant, vous vous allez ajouter une interface utilisateur pour aider à les filtrer de films. Si vous avez modifié la signature de la `Index` méthode à tester comment passer le paramètre ID de la limite de l’itinéraire, modifiez-le pour que votre `Index` méthode prend un paramètre de chaîne nommé `searchString`:

[!code-csharp[Main](adding-search/samples/sample7.cs)]

Ouvrez le *Views\Movies\Index.cshtml* de fichiers et juste après `@Html.ActionLink("Create New", "Create")`, ajoutez le balisage de formulaire en surbrillance ci-dessous :

[!code-cshtml[Main](adding-search/samples/sample8.cshtml?highlight=12-15)]

Le `Html.BeginForm` assistance crée une ouverture `<form>` balise. Le `Html.BeginForm` application auxiliaire, le formulaire à valider dans lui-même lorsque l’utilisateur envoie le formulaire en cliquant sur le **filtre** bouton.

Visual Studio 2013 a une amélioration lors de l’affichage et la modification des fichiers de vue. Lorsque vous exécutez l’application avec un fichier de vue ouverte, Visual Studio 2013 appelle la méthode d’action de contrôleur correct pour afficher la vue.

![](adding-search/_static/image3.png)

La vue Index ouvert dans Visual Studio (comme indiqué dans l’image ci-dessus), appuyez sur CTRL F5 ou F5 pour exécuter l’application et essayez ensuite de rechercher un film.

![](adding-search/_static/image4.png)

Il est sans `HttpPost` surcharge de la `Index` (méthode). Vous n’avez pas besoin, car la méthode n’est pas modifier l’état de l’application, filtrage des données.

Vous pourriez ajouter la méthode `HttpPost Index` suivante. Dans ce cas, le demandeur d’action correspondrait à la `HttpPost Index` (méthode) et le `HttpPost Index` méthode exécuterait comme indiqué dans l’image ci-dessous.

[!code-csharp[Main](adding-search/samples/sample9.cs)]

![SearchPostGhost](adding-search/_static/image5.png)

Cependant, même si vous ajoutez cette version `HttpPost` de la méthode `Index`, il existe une limitation dans la façon dont tout ceci a été implémenté. Imaginez que vous voulez insérer un signet pour une recherche spécifique, ou que vous voulez envoyer un lien à vos amis sur lequel ils peuvent cliquer pour afficher la même liste filtrée de films. Notez que l’URL de la demande HTTP POST est le même que l’URL de la demande GET (localhost:xxxxx/films/Index) : il n’existe aucune information de recherche dans l’URL proprement dite. Droite maintenant, les informations de chaîne de recherche sont envoyées au serveur en tant que valeur d’un champ de formulaire. Cela signifie que vous ne pouvez pas capturer ces informations de recherche pour insérer un signet ou l’envoyer à vos amis dans une URL.

La solution consiste à utiliser une surcharge de `BeginForm` qui spécifie que la requête POST doit ajouter les informations de recherche à l’URL et qu’il doit être routé vers le `HttpGet` version de la `Index` (méthode). Remplacer la sans paramètre `BeginForm` méthode avec le balisage suivant :

[!code-cshtml[Main](adding-search/samples/sample10.cshtml)]

![BeginFormPost_SM](adding-search/_static/image6.png)

Maintenant lorsque vous soumettez une recherche, l’URL contient une chaîne de requête de recherche. La recherche accède également à la méthode d’action `HttpGet Index`, même si vous avez une méthode `HttpPost Index`.

![IndexWithGetURL](adding-search/_static/image7.png)

## <a name="adding-search-by-genre"></a>Ajout de recherche par Genre

Si vous avez ajouté le `HttpPost` version de la `Index` (méthode), supprimez maintenant.

Ensuite, vous allez ajouter une fonctionnalité pour permettre aux utilisateurs de rechercher des films par genre. Remplacez la méthode `Index` par le code suivant :

[!code-csharp[Main](adding-search/samples/sample11.cs)]

Cette version de la `Index` méthode prend un paramètre supplémentaire, à savoir `movieGenre`. Les premières lignes de code créent un `List` objet pour contenir les genres de film à partir de la base de données.

Le code suivant est une requête LINQ qui récupère tous les genres dans la base de données.

[!code-csharp[Main](adding-search/samples/sample12.cs)]

Le code utilise le `AddRange` méthode du générique `List` collection pour ajouter tous les genres distincts à la liste. (Sans le `Distinct` modificateur, genres en double sont ajoutés, par exemple, comédie est ajoutée à deux reprises dans notre exemple). Le code puis stocke la liste des genres dans le `ViewBag.MovieGenre` objet. Le stockage des données de catégorie (de ce un genre de film) comme un [SelectList](https://msdn.microsoft.com/en-us/library/system.web.mvc.selectlist(v=vs.108).aspx) de l’objet dans un `ViewBag`, accéder aux données de catégorie dans une zone de liste déroulante est une approche courante pour les applications MVC.

Le code suivant montre comment vérifier la `movieGenre` paramètre. S’il n’est pas vide, le code plus contraint la requête de films pour limiter les films sélectionnés pour le genre spécifié.

[!code-csharp[Main](adding-search/samples/sample13.cs)]

Comme indiqué précédemment, la requête n’est pas exécutée sur la base de données jusqu'à ce que la liste de films est itérée (ce qui se produit dans la vue, après la `Index` retourne de la méthode d’action).

## <a name="adding-markup-to-the-index-view-to-support-search-by-genre"></a>Ajout de balisage à la vue de l’Index pour prendre en charge la recherche par Genre

Ajouter un `Html.DropDownList` application d’assistance pour le *Views\Movies\Index.cshtml* de fichiers, juste avant la `TextBox` helper. Le balisage complet est indiqué ci-dessous :

[!code-cshtml[Main](adding-search/samples/sample14.cshtml?highlight=11)]

Dans le code suivant :

[!code-cshtml[Main](adding-search/samples/sample15.cshtml)]

Le paramètre « MovieGenre » fournit la clé pour le `DropDownList` application d’assistance pour rechercher un `IEnumerable<SelectListItem>` dans le `ViewBag`. Le `ViewBag` a été rempli dans la méthode d’action :

[!code-csharp[Main](adding-search/samples/sample16.cs?highlight=10)]

Le paramètre « All » fournit une étiquette d’option. Si vous examinez ce choix dans votre navigateur, vous verrez que son attribut de « value » est vide. Étant donné que notre contrôleur filtre uniquement `if` la chaîne n’est pas `null` ou est vide, l’envoi d’une valeur vide pour `movieGenre` montre tous les genres.

Vous pouvez également définir une option soit activée par défaut. Si vous souhaitiez « Comédie » en tant que l’option par défaut, vous devez modifier le code dans le contrôleur de comme suit :

[!code-cshtml[Main](adding-search/samples/sample17.cshtml)]

Exécutez l’application et accédez à *films/Index*. Essayez une recherche par genre, par le nom du film et par les deux critères.

![](adding-search/_static/image8.png)

Dans cette section, vous avez créé une méthode d’action de recherche et la vue qui permettent aux utilisateurs d’effectuer une recherche par titre du film et le genre. Dans la section suivante, vous allez examiner comment ajouter une propriété à la `Movie` modèle et comment ajouter un initialiseur qui crée automatiquement une base de données de test.

>[!div class="step-by-step"]
[Précédent](examining-the-edit-methods-and-edit-view.md)
[Suivant](adding-a-new-field.md)
