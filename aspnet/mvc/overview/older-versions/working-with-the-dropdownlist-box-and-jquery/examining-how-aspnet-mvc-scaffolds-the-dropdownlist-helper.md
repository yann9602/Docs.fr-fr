---
uid: mvc/overview/older-versions/working-with-the-dropdownlist-box-and-jquery/examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper
title: "Examiner la façon dont ASP.NET MVC micro-capsules du programme d’assistance DropDownList | Documents Microsoft"
author: Rick-Anderson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/12/2012
ms.topic: article
ms.assetid: 8921d7f2-21f0-427a-8b27-2df7251174b0
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/working-with-the-dropdownlist-box-and-jquery/examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper
msc.type: authoredcontent
ms.openlocfilehash: b5210f9a29f82fbadd0e6dd2d81bd85e7f23ae7e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="examining--how--aspnet-mvc-scaffolds-the-dropdownlist-helper"></a>Examiner la façon dont ASP.NET MVC micro-capsules du programme d’assistance DropDownList
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

Dans **l’Explorateur de solutions**, avec le bouton droit le *contrôleurs* dossier, puis sélectionnez **ajouter un contrôleur**. Nommez le contrôleur **StoreManagerController**. Définir les options de la **ajouter un contrôleur** boîte de dialogue comme illustré dans l’image ci-dessous.

![](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/_static/image1.png)

Modifier la *StoreManager\Index.cshtml* afficher et supprimer `AlbumArtUrl`. Suppression `AlbumArtUrl` améliorer la lisibilité de la présentation. Le code complet est indiqué ci-dessous.

[!code-cshtml[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample1.cshtml)]

Ouvrez le *Controllers\StoreManagerController.cs* de fichiers et recherchez le `Index` (méthode). Ajouter le `OrderBy` afin que les albums sont triés par prix. Le code complet est indiqué ci-dessous.

[!code-csharp[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample2.cs)]

Tri par prix rend plus faciles à tester les modifications apportées à la base de données. Lorsque vous testez la modification et créez des méthodes, vous pouvez utiliser un prix pour les données enregistrées sont affichées en premier.

Ouvrez le *StoreManager\Edit.cshtml* fichier. Ajoutez la ligne suivante juste après l’étiquette de légende.

[!code-cshtml[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample3.cshtml)]

Le code suivant affiche le contexte de cette modification :

[!code-cshtml[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample4.cshtml)]

Le `AlbumId` est nécessaire pour apporter des modifications à un enregistrement de l’album.

Appuyez sur CTRL+F5 pour exécuter l'application. Sélectionnez cette option pour le **Admin** lier, puis sélectionnez le **créer un nouveau** lien pour créer un nouvel album. Vérifier que les informations de l’album a été enregistrées. Modifier un album et vérifier les modifications apportées sont conservées.

### <a name="the-album-schema"></a>Le schéma de l’Album

Le `StoreManager` contrôleur créé par le mécanisme de génération de modèles automatique de MVC autorise l’accès CRUD (création, lecture, mise à jour, suppression) pour les albums dans la base de données du magasin de musique. Le schéma pour des informations sur l’album est indiqué ci-dessous :

![](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/_static/image2.png)

Le `Albums` table ne stocke pas le genre de l’album et la description, il stocke une clé étrangère à la `Genres` table. Le `Genres` table contient le nom du genre et la description. De même, la `Albums` table ne contient pas le nom de l’album artistes, mais une clé étrangère à la `Artists` table. Le `Artists` table contient des CD. Si vous examinez les données dans le `Albums` table, vous pouvez voir chaque ligne contient une clé étrangère à la `Genres` table et une clé étrangère à la `Artists` table. L’image ci-dessous montrent certaines données de la table à partir de la `Albums` table.

![](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/_static/image3.png)

### <a name="the-html-select-tag"></a>La balise HTML Select

Le code HTML `<select>` élément (créée par le code HTML [DropDownList](https://msdn.microsoft.com/en-us/library/dd492948.aspx) helper) est utilisé pour afficher une liste complète des valeurs (par exemple, la liste des genres). Pour modifier les formulaires, lorsque la valeur actuelle est connue, la liste de sélection peut afficher la valeur actuelle. Nous avons vu ce précédemment lorsque nous devons définir la valeur sélectionnée sur **comédie**. La liste de sélection est idéale pour l’affichage des catégories ou des données de clé étrangère. Le `<select>` , élément pour la clé étrangère Genre affiche la liste des noms de genre possibles, mais lorsque vous enregistrez le formulaire la propriété Genre est mis à jour avec la valeur de clé étrangère Genre, pas le nom du genre affichée. Dans l’image ci-dessous, le genre sélectionné est **Disco** et l’artiste est **Donna Summer**.

![](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/_static/image4.png)

### <a name="examining-the-aspnet-mvc-scaffolded-code"></a>Examen de l’ASP.NET MVC structuré de Code

Ouvrez le *Controllers\StoreManagerController.cs* de fichiers et recherchez le `HTTP GET Create` (méthode).

[!code-csharp[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample5.cs)]

Le `Create` méthode ajoute deux [SelectList](https://msdn.microsoft.com/en-us/library/system.web.mvc.selectlist.aspx) des objets sur le `ViewBag`, une pour contenir les informations de genre et une pour contenir les informations artiste. Le [SelectList](https://msdn.microsoft.com/en-us/library/dd505286.aspx) surcharge de constructeur ci-dessus prend trois arguments :

[!code-csharp[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample6.cs)]

1. *éléments*: un [IEnumerable](https://msdn.microsoft.com/en-us/library/system.collections.ienumerable.aspx) contenant les éléments dans la liste. Dans l’exemple ci-dessus, la liste des genres retourné par `db.Genres`.
2. *dataValueField*: le nom de la propriété dans le **IEnumerable** liste qui contient la valeur de clé. Dans l’exemple ci-dessus, `GenreId` et `ArtistId`.
3. *dataTextField*: le nom de la propriété dans le **IEnumerable** liste qui contient les informations à afficher. Dans les artistes et de table de genre, le `name` champ est utilisé.

Ouvrez le *Views\StoreManager\Create.cshtml* de fichiers et d’examiner le `Html.DropDownList` balisage d’assistance pour le champ de genre.

[!code-cshtml[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample7.cshtml)]

La première ligne indique que la vue create prend un `Album` modèle. Dans le `Create` méthode illustrée ci-dessus, aucun modèle n’a été passé, afin de la vue obtient un **null** `Album` modèle. À ce stade, nous allons créer un nouvel album afin que nous ne les `Album` données pour lui.

Le [Html.DropDownList](https://msdn.microsoft.com/en-us/library/dd492948.aspx) surcharge ci-dessus prend le nom du champ à lier au modèle. Il utilise également ce nom pour rechercher un **ViewBag** objet contenant un [SelectList](https://msdn.microsoft.com/en-us/library/dd505286.aspx) objet. À l’aide de cette surcharge, vous êtes invité à nom le **ViewBag SelectList** objet `GenreId`. Le deuxième paramètre (`String.Empty`) est le texte à afficher lorsque aucun élément n’est sélectionné. C’est exactement ce que nous voulons lors de la création d’un nouvel album. Si vous supprimée le deuxième paramètre et le code suivant :

[!code-cshtml[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample8.cshtml)]

La liste de sélection est par défaut pour le premier élément, ou Rock dans notre exemple.

![](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/_static/image5.png)

Examiner la `HTTP POST Create` (méthode).

[!code-csharp[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample9.cs)]

Cette surcharge de la `Create` méthode prend un `album` objet, créé par le système de liaison de modèle ASP.NET MVC à partir des valeurs de formulaire publiées. Quand vous envoyez un nouvel album, si l’état du modèle est valide et il n’existe aucune erreur de base de données, le nouvel album est ajouté à la base de données. L’image suivante illustre la création d’un nouvel album.

![](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/_static/image6.png)

Vous pouvez utiliser la [outil fiddler](http://www.fiddler2.com/fiddler2/) pour examiner les valeurs de formulaire publiées cette liaison de modèle ASP.NET MVC utilise pour créer l’objet de l’album.

![](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/_static/image7.png).

### <a name="refactoring-the-viewbag-selectlist-creation"></a>La création de l’élément ViewBag SelectList de refactorisation

Les deux le `Edit` méthodes et les `HTTP POST Create` méthode avoir un code identique pour configurer le **SelectList** dans les **ViewBag**. Dans cet esprit de [sec](http://en.wikipedia.org/wiki/Don't_repeat_yourself), nous devez refactoriser ce code. Nous allons effectuer l’utilisation de ce code de refactoriser plus tard.

Créez une méthode pour ajouter un genre et un artiste **SelectList** à la **ViewBag**.

[!code-csharp[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample10.cs)]

Remplacez les deux lignes de paramètre le `ViewBag` dans chacun de la `Create` et `Edit` méthodes avec un appel à la `SetGenreArtistViewBag` (méthode). Le code complet est indiqué ci-dessous.

[!code-csharp[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample11.cs)]

Créer un nouvel album et modifier un album pour vérifier que les modifications fonctionnent.

### <a name="explicitly-passing-the-selectlist-to-the-dropdownlist"></a>En fournissant explicitement le SelectList à DropDownList

Les créer et modifier des vues créées à l’aide de la génération de modèles automatique ASP.NET MVC suivantes **DropDownList** surcharge :

[!code-csharp[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample12.cs)]

Le `DropDownList` balisage de l’affichage de créer est indiqué ci-dessous.

[!code-cshtml[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample13.cshtml)]

Car le `ViewBag` propriété pour le `SelectList` est nommé `GenreId`, le **DropDownList** helper utilisera le `GenreId` **SelectList** dans le **ViewBag** . Dans l’exemple suivant **DropDownList** de surcharge, le `SelectList` est transmise explicitement.

[!code-csharp[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample14.cs)]

Ouvrez le *Views\StoreManager\Edit.cshtml* de fichiers et de modifier le **DropDownList** appel à passer explicitement dans le **SelectList**, à l’aide de la surcharge ci-dessus. Le fait pour la catégorie de Genre. Le code complet est indiqué ci-dessous :

[!code-cshtml[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample15.cshtml)]

Exécutez l’application et cliquez sur le **Admin** lier, puis accédez à un album Jazz et sélectionnez le **modifier** lien.

![](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/_static/image8.png)

Au lieu de Jazz en tant que le genre actuellement sélectionné, Rock s’affiche. Lorsque l’argument de chaîne (la propriété à lier) et le **SelectList** objet portent le même nom, la valeur sélectionnée n’est pas utilisée. Lorsqu’il n’existe aucune valeur sélectionnée est fourni, les navigateurs par défaut au premier élément dans le **SelectList**(c'est-à-dire **Rock** dans l’exemple ci-dessus). Il s’agit d’une limitation connue de le **DropDownList** helper.

Ouvrez le *Controllers\StoreManagerController.cs* de fichiers et de modifier le **SelectList** noms à l’objet `Genres` et `Artists`. Le code complet est indiqué ci-dessous :

[!code-csharp[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample16.cs)]

Les noms Genres et artistes sont mieux noms pour les catégories, car ils contiennent plus que l’ID de chaque catégorie. La refactorisation, que nous l’avons fait précédemment payé. Au lieu de modifier le **ViewBag** dans les quatre méthodes, nos modifications étaient isolées dans le `SetGenreArtistViewBag` (méthode).

Modifier la **DropDownList** appeler dans le créer et modifier des vues pour utiliser la nouvelle **SelectList** noms. Le balisage pour le mode édition est indiqué ci-dessous :

[!code-cshtml[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample17.cshtml)]

La vue Create nécessite une chaîne vide pour le premier élément de la SelectList empêcher de s’afficher.

[!code-cshtml[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample18.cshtml)]

Créer un nouvel album et modifier un album pour vérifier que les modifications fonctionnent. Tester le code de la modifier en sélectionnant un album avec un genre que Rock.

### <a name="using-a-view-model-with-the-dropdownlist-helper"></a>À l’aide d’un modèle d’affichage avec l’application d’assistance DropDownList

Créer une nouvelle classe dans le dossier ViewModel nommé `AlbumSelectListViewModel`. Remplacez le code dans la `AlbumSelectListViewModel` classe par le code suivant :

[!code-csharp[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample19.cs)]

Le `AlbumSelectListViewModel` constructeur accepte un album, une liste d’artistes et genres et crée un objet qui contient l’album et un `SelectList` pour genres et artistes.

Générez le projet afin que la `AlbumSelectListViewModel` est disponible quand vous en créerez une vue à l’étape suivante.

Ajouter un `EditVM` méthode à la `StoreManagerController`. Le code complet est indiqué ci-dessous.

[!code-csharp[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample20.cs)]

Bouton droit sur `AlbumSelectListViewModel`, sélectionnez **résoudre**, puis **à l’aide de MvcMusicStore.ViewModels ;**.

![](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/_static/image9.png)

Vous pouvez également ajouter les éléments suivants à l’aide d’instruction :

[!code-csharp[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample21.cs)]

Bouton droit sur `EditVM` et sélectionnez **ajouter une vue**. Utilisez les options ci-dessous.

![](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/_static/image10.png)

Sélectionnez **ajouter**, puis remplacez le contenu de la *Views\StoreManager\EditVM.cshtml* fichier avec les éléments suivants :

[!code-cshtml[Main](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/samples/sample22.cshtml)]

Le `EditVM` balisage est très similaire à celle d’origine `Edit` balisage avec les exceptions suivantes.

- Modèle de propriétés dans le `Edit` vue sont sous la forme `model.property`(par exemple, `model.Title` ). Modèle de propriétés dans le `EditVm` vue sont sous la forme `model.Album.property`(par exemple, `model.Album.Title`). C’est parce que le `EditVM` view est passée à un conteneur pour un `Album`, et non un `Album` comme dans le `Edit` vue.
- Le **DropDownList** deuxième paramètre est fourni à partir du modèle de vue, pas le **ViewBag**.
- Le **BeginForm** helper dans le `EditVM` vue explicitement publie vers le `Edit` méthode d’action. En publiant à nouveau à la `Edit` action, nous n’avons pas à écrire un `HTTP POST EditVM` action et vous pouvez réutiliser le `HTTP POST` `Edit` action.

Exécutez l’application et modifier un album. Modifier l’URL à utiliser `EditVM`. Modifier un champ et appuyez sur la **enregistrer** bouton pour vérifier le code fonctionne.

![](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper/_static/image11.png)

### <a name="which-approach-should-you-use"></a>Quelle approche utiliser ?

Tous les trois approches indiqués sont acceptables. De nombreux développeurs préfèrent explictily passe le `SelectList` à la `DropDownList` à l’aide de la `ViewBag`. Cette approche présente l’avantage de ce qui vous donne la possibilité d’utiliser un nom plus approprié pour la collection. L’inconvénient est que vous ne pouvez pas nommer le `ViewBag SelectList` le même nom que la propriété du modèle d’objet.

Certains développeurs préfèrent l’approche ViewModel. Prendre le plus détaillé balisage et le code HTML généré de ViewModel approchent un inconvénient.

Dans cette section, nous avons appris trois manières d’utiliser le **DropDownList** avec les données de catégorie. Dans la section suivante, nous allons montrer comment ajouter une nouvelle catégorie.

>[!div class="step-by-step"]
[Précédent](using-the-dropdownlist-helper-with-aspnet-mvc.md)
[Suivant](adding-a-new-category-to-the-dropdownlist-using-jquery-ui.md)
