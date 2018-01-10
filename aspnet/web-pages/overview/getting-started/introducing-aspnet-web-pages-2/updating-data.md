---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/updating-data
title: "Présentation des Pages Web ASP.NET - mise à jour de la base de données | Documents Microsoft"
author: tfitzmac
description: "Ce didacticiel vous montre comment mettre à jour (modifier) une base de données entrée lorsque vous utilisez les Pages Web ASP.NET (Razor). Il suppose que vous avez terminé la série th..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/02/2018
ms.topic: article
ms.assetid: ac86ec9c-6b69-485b-b9e0-8b9127b13e6b
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/updating-data
msc.type: authoredcontent
ms.openlocfilehash: b016231975bf8d359f4c390b0b478edc383117d4
ms.sourcegitcommit: df2157ae9aeea0075772719c29784425c783e82a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
<a name="introducing-aspnet-web-pages---updating-database-data"></a>Présentation des Pages Web ASP.NET - mise à jour de la base de données
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Ce didacticiel vous montre comment mettre à jour (modifier) une base de données entrée lorsque vous utilisez les Pages Web ASP.NET (Razor). Il suppose que vous avez terminé la série via [saisie de données par à l’aide de formulaires à l’aide des Pages Web ASP.NET](entering-data.md).
> 
> Ce que vous allez apprendre :
> 
> - Comment sélectionner un enregistrement particulier dans le `WebGrid` helper.
> - Comment lire un enregistrement unique à partir d’une base de données.
> - Comment précharger un formulaire avec les valeurs de l’enregistrement de la base de données.
> - Comment mettre à jour un enregistrement existant dans une base de données.
> - Explique comment stocker des informations dans la page sans l’afficher.
> - Comment utiliser un champ masqué pour stocker les informations.
>   
> 
> Fonctionnalités technologies abordées :
> 
> - Le `WebGrid` helper.
> - L’instruction SQL `Update` commande.
> - Méthode `Database.Execute`
> - Champs masqués (`<input type="hidden">`).


## <a name="what-youll-build"></a>Ce que vous allez générer

Dans le didacticiel précédent, vous avez appris comment ajouter un enregistrement à une base de données. Ici, vous allez apprendre à afficher un enregistrement pour la modification. Dans le *films* page, vous allez mettre à jour le `WebGrid` helper afin qu’elle affiche un **modifier** lien en regard de chaque séquence :

![WebGrid afficher, y compris un lien « Modifier » pour chaque vidéo](updating-data/_static/image1.png)

Lorsque vous cliquez sur le **modifier** lien, il vous redirige vers une autre page, où les informations de film sont déjà dans un formulaire :

![Modifier la page de film montrant des films à modifier](updating-data/_static/image2.png)

Vous pouvez modifier les valeurs. Lorsque vous soumettez les modifications, le code dans la page mises à jour de la base de données et vous revenez à la liste de films.

Cette partie du processus fonctionne presque exactement comme le *AddMovie.cshtml* page que vous avez créé dans le didacticiel précédent, une grande partie de ce didacticiel est donc familier.

Il existe plusieurs méthodes que vous pouvez implémenter un moyen de modifier une vidéo individuelle. L’approche illustrée a été choisie, car il est facile à implémenter et faciles à comprendre.

## <a name="adding-an-edit-link-to-the-movie-listing"></a>Ajout d’un lien Modifier à la liste de films

Pour commencer, vous allez mettre à jour le *films* page afin que chaque vidéo qui répertorie également contient un **modifier** lien.

Ouvrez le *Movies.cshtml* fichier.

Dans le corps de la page, vous devez modifier le `WebGrid` balisage en ajoutant une colonne. Voici le balisage modifié :

[!code-html[Main](updating-data/samples/sample1.html?highlight=6)]

La nouvelle colonne est celle-ci :

[!code-html[Main](updating-data/samples/sample2.html)]

Le point de cette colonne est d’illustrer un lien (`<a>` élément) dont le texte indique « Modifier ». Nous recherchons est pour créer un lien qui ressemble à ceci lorsque la page s’exécute, avec la `id` valeur différente pour chaque vidéo :

[!code-css[Main](updating-data/samples/sample3.css)]

Ce lien appelle une page nommée *EditMovie*, et elle passe la chaîne de requête `?id=7` à cette page.

La syntaxe de la nouvelle colonne peut se présenter un peu complexe, mais c’est uniquement parce qu’elle réunit plusieurs éléments. Chaque élément individuel est simple. Si vous vous concentrer sur uniquement le `<a>` élément, vous voyez ce balisage :

[!code-html[Main](updating-data/samples/sample4.html)]

Certains en arrière-plan sur le fonctionne de la grille : la grille affiche les lignes, une pour chaque enregistrement de la base de données, et il affiche les colonnes pour chaque champ dans l’enregistrement de la base de données. Tandis que chaque ligne de la grille est en cours de construction, la `item` objet contient l’enregistrement de base de données (élément) pour cette ligne. Ceci vous offre une méthode dans le code pour obtenir les données pour cette ligne. C’est ce que vous voyez ici : l’expression `item.ID` reçoit la valeur d’ID de l’élément actuel de la base de données. Vous pouvez obtenir les valeurs de base de données (titre, genre ou année) de la même façon à l’aide de `item.Title`, `item.Genre`, ou `item.Year`.

L’expression `"~/EditMovie?id=@item.ID` combine la partie codée en dur de l’URL cible (`~/EditMovie?id=`) avec l’ID dérivé dynamiquement. (Vous avez vu le `~` opérateur dans le didacticiel précédent ; il s’agit d’un opérateur d’ASP.NET qui représente la racine du site Web en cours.)

Le résultat est que cette partie de la balise dans la colonne produit simplement un nom tel que le balisage suivant au moment de l’exécution :

[!code-xml[Main](updating-data/samples/sample5.xml)]

Naturellement, la valeur réelle de `id` sera différente pour chaque ligne.

## <a name="creating-a-custom-display-for-a-grid-column"></a>Création d’un affichage personnalisé pour une colonne de la grille

Revenons à la colonne de grille. Les trois colonnes vous aviez la grille affiche uniquement les valeurs (titre, genre et année). Vous avez spécifié à cet affichage en passant le nom de la colonne de base de données &mdash; , par exemple, `grid.Column("Title")`.

Cette nouvelle **modifier** colonne de lien est différent. Au lieu de spécifier un nom de colonne, vous transmettez un `format` paramètre. Ce paramètre vous permet de définir le balisage qui le `WebGrid` helper apparaîtront avec la `item` valeur pour afficher des données de la colonne en caractères gras ou vert ou dans le format que vous souhaitez. Par exemple, si vous souhaitiez le titre à afficher en gras, vous pouvez créer une colonne à l’exemple suivant :

[!code-html[Main](updating-data/samples/sample6.html)]

(Les différentes `@` caractères que vous voyez dans le `format` propriété marquer la transition entre le balisage et une valeur de code.)

Une fois que vous connaissez le `format` propriété, il est plus facile à comprendre comment le nouveau **modifier** colonne de lien est mis en place :

[!code-html[Main](updating-data/samples/sample7.html)]

La colonne se compose *uniquement* le balisage restitue le lien, ainsi que certaines informations (ID) qui est extraite à partir de l’enregistrement de la base de données pour la ligne.

> [!TIP] 
> 
> **Les paramètres nommés et les paramètres positionnels d’une méthode**
> 
> Autant de fois lorsque vous avez appelé une méthode et des paramètres passés à ce dernier, vous avez répertorié simplement les valeurs de paramètres séparés par des virgules. Voici quelques exemples :
> 
> `db.Execute(insertCommand, title, genre, year)`
> 
> `Validation.RequireField("title", "You must enter a title")`
> 
> Nous n’avez pas signaler le problème lorsque vous l’avez vu tout d’abord ce code, mais dans chaque cas, vous êtes passage de paramètres aux méthodes dans un ordre spécifique &mdash; à savoir, l’ordre dans lequel les paramètres sont définis dans cette méthode. Pour `db.Execute` et `Validation.RequireFields`, si une combinaison de l’ordre des valeurs que vous passez, vous pourriez obtenir un message d’erreur lorsque la page s’exécute, ou au moins des résultats inattendus. Clairement, vous devez connaître l’ordre de passer les paramètres dans. (Dans WebMatrix, IntelliSense peut vous aider à apprendre figure le nom, le type et l’ordre des paramètres.)
> 
> Comme alternative à la transmission de valeurs dans l’ordre, vous pouvez utiliser *des paramètres nommés*. (Passage de paramètres dans l’ordre est appelé à l’aide de *paramètres positionnels*.) Des paramètres nommés, vous incluez explicitement le nom du paramètre lors du passage de sa valeur. Vous avez utilisé les paramètres nommés déjà un nombre de fois dans ces didacticiels. Exemple :
> 
> [!code-csharp[Main](updating-data/samples/sample8.cs)]
> 
> et
> 
> [!code-css[Main](updating-data/samples/sample9.css)]
> 
> Les paramètres nommés sont pratiques pour deux situations, en particulier lorsqu’une méthode accepte le nombre de paramètres. Un est lorsque vous souhaitez passer uniquement un ou deux paramètres, mais les valeurs que vous souhaitez passer ne sont pas entre les positions premier dans la liste de paramètres. Une autre situation est lorsque vous souhaitez rendre votre code plus lisible en passant les paramètres dans l’ordre le plus logique pour vous.
> 
> Évidemment, pour utiliser des paramètres nommés, vous devez connaître les noms des paramètres. WebMatrix IntelliSense peut *afficher* vous les noms, mais il ne peut pas actuellement remplir pour vous.


## <a name="creating-the-edit-page"></a>Création de la Page de modification

Vous pouvez désormais créer le *EditMovie* page. Lorsque les utilisateurs cliquent sur le **modifier** lien, ils obtiendrez sur cette page.

Créer une page nommée *EditMovie.cshtml* et remplacez ce qui est dans le fichier avec le balisage suivant :

[!code-cshtml[Main](updating-data/samples/sample10.cshtml)]

Ce balisage et le code est similaire à ce dont vous disposez dans le *AddMovie* page. Il existe une légère différence dans le texte du bouton Envoyer. Comme avec la *AddMovie* page, il existe un `Html.ValidationSummary` appel qui affiche les erreurs de validation s’il existe des. Cette fois, nous mettons en laissant les appels à `Validation.Message`, car les erreurs s’affichent dans le résumé des validations. Comme indiqué dans le didacticiel précédent, vous pouvez utiliser le résumé des validations et les messages d’erreur individuels dans différentes combinaisons.

Notez à nouveau que les `method` attribut de la `<form>` a la valeur `post`. Comme avec la *AddMovie.cshtml* page, cette page apporte des modifications à la base de données. Par conséquent, ce formulaire doit effectuer une `POST` opération. (Pour plus d’informations sur la différence entre `GET` et `POST` opérations, consultez le [sécurité du verbe HTTP GET et POST](form-basics.md#GET,_POST,_and_HTTP_Verb_Safety) encadré dans le didacticiel sur les formulaires HTML.)

Comme vous l’avez vu dans un didacticiel antérieur, le `value` les attributs des zones de texte sont définies avec le code Razor pour précharger. Cette fois, vous utilisez des variables comme, `title` et `genre` pour cette tâche à la place de `Request.Form["title"]`:

`<input type="text" name="title" value="@title" />`

Comme avant, ce balisage sera précharger les valeurs de zone de texte avec les valeurs de séquence. Vous verrez dans un moment pourquoi il est pratique d’utiliser des variables instant au lieu d’utiliser le `Request` objet.

Il existe également un `<input type="hidden">` élément sur cette page. Cet élément enregistre l’ID de film sans le rendre visible dans la page. L’ID est initialement passée à la page par à l’aide d’une valeur de chaîne de requête (`?id=7` ou similaires dans l’URL). En plaçant la valeur d’ID dans un champ masqué, vous assurer qu’il est disponible lorsque le formulaire est envoyé, même si vous n’avez plus accès à la page a été appelée avec l’URL d’origine.

Contrairement à la *AddMovie* page, le code pour le *EditMovie* page possède deux fonctions distinctes. La première fonction est que lorsque la page s’affiche pour la première fois (et *uniquement* puis), le code obtient l’ID de film à partir de la chaîne de requête. Le code, puis utilise l’ID pour lire le film correspondant en dehors de la base de données et afficher (précharge) il dans les zones de texte.

La deuxième fonction est que lorsque l’utilisateur clique sur le **soumettre les modifications** bouton, le code doit lire les valeurs des zones de texte et de les valider. Le code doit également mettre à jour l’élément de la base de données avec les nouvelles valeurs. Cette technique est similaire à l’ajout d’un enregistrement, comme vous l’avez vu dans *AddMovie*.

## <a name="adding-code-to-read-a-single-movie"></a>Ajout de Code pour lire un film unique

Pour effectuer la première fonction, ajoutez ce code vers le haut de la page :

[!code-cshtml[Main](updating-data/samples/sample11.cshtml)]

La majeure partie de ce code est à l’intérieur d’un bloc qui commence `if(!IsPost)`. Le `!` opérateur signifie « not », l’expression signifie *si cette demande n’est pas une soumission post*, qui est un moyen indirect de dire *si cette demande est la première fois que cette page a été exécutée*. Comme indiqué précédemment, ce code doit s’exécuter *uniquement* lors de la première exécution de la page. Si vous n’avez pas placer le code dans `if(!IsPost)`, il permet de s’exécuter chaque fois que la page est appelée, si la première fois ou en réponse à un bouton, cliquez sur.

Notez que le code inclut un `else` bloquer cette fois-ci. Comme nous l’avons dit lorsque nous avons présenté `if` blocs, parfois, vous souhaitez exécuter le code de substitution si la condition que vous testez n’est pas vrai. Qui est le cas ici. Si la condition passe (autrement dit, si l’ID passée à la page est OK), une ligne en lecture à partir de la base de données. Toutefois, si la condition ne remplit pas la `else` bloc s’exécute et le code définit un message d’erreur.

## <a name="validating-a-value-passed-to-the-page"></a>Validation d’une valeur passée à la Page

Le code utilise `Request.QueryString["id"]` pour obtenir l’ID qui est passé à la page. Le code permet de s’assurer qu’une valeur a été réellement passée pour le code. Si aucune valeur n’a été passée, le code définit une erreur de validation.

Ce code montre une manière différente pour valider les informations. Dans le didacticiel précédent, vous avez travaillé avec le `Validation` helper. Vous avez inscrit des champs à valider et ASP.NET a la validation automatiquement et affiche les erreurs à l’aide de `Html.ValidationMessage` et `Html.ValidationSummary`. Dans ce cas, toutefois, vous n'êtes pas vraiment valider l’entrée d’utilisateur. Au lieu de cela, vous êtes validation d’une valeur qui a été passée à la page à partir d’un autre emplacement. Le `Validation` helper ne fera pas pour vous.

Par conséquent, vous vérifiez la valeur vous-même, en le testant avec `if(!Request.QueryString["ID"].IsEmpty()`). S’il existe un problème, vous pouvez afficher l’erreur à l’aide de `Html.ValidationSummary`, comme vous le faisiez avec les `Validation` helper. Pour ce faire, vous appelez `Validation.AddFormError` et lui passer un message à afficher. `Validation.AddFormError`est une méthode intégrée qui vous permet de définir des messages personnalisés à vous êtes déjà familiarisé avec le système de contrôle. (Plus loin dans ce didacticiel nous parlerons comment rendre ce processus de validation un peu plus fiable.)

Après avoir vérifié qu’il existe un ID pour la vidéo, le code lit la base de données, recherchez un élément unique de la base de données. (Vous avez probablement remarqué le modèle général pour les opérations de base de données : ouvrir la base de données, définir une instruction SQL et exécutez l’instruction.) Cette fois-ci, l’instruction SQL `Select` instruction inclut `WHERE ID = @0`. Étant donné que l’ID est unique, un seul enregistrement peut être retourné.

La requête est exécutée à l’aide de `db.QuerySingle` (pas `db.Query`, comme vous avez utilisé pour obtenir la liste de films), et le code place le résultat dans le `row` variable. Le nom `row` est arbitraire ; vous pouvez nommer les variables comme vous le souhaitez. Les variables initialisées en haut sont ensuite remplis avec les détails des films afin que ces valeurs peuvent être affichées dans les zones de texte.

## <a name="testing-the-edit-page-so-far"></a>Test de la Page de modification (jusqu'à présent)

Si vous souhaitez tester votre page, exécutez le *films* page maintenant, cliquez sur une **modifier** lien en regard de n’importe quel film. Vous verrez la *EditMovie* page avec les détails remplie pour le film que vous avez sélectionné :

![Modifier la page de film montrant des films à modifier](updating-data/_static/image3.png)

Notez que l’URL de la page inclut un nom tel que `?id=10` (ou tout autre numéro). Jusqu'à présent, vous avez testé qui **modifier** des liaisons dans le *film* page travail, votre page lit l’ID de la chaîne de requête, fonctionne et que la base de données de requête pour obtenir un enregistrement de séquence unique.

Vous pouvez modifier les informations de film, mais rien ne se produit lorsque vous cliquez sur **soumettre les modifications**.

## <a name="adding-code-to-update-the-movie-with-the-users-changes"></a>Ajout de Code pour mettre à jour le film avec les modifications apportées par l’utilisateur

Dans le *EditMovie.cshtml* , pour implémenter la fonction second (l’enregistrement des modifications), ajoutez le code suivant à l’intérieur de l’accolade fermante de la `@` bloc. (Si vous ne savez pas exactement où placer le code, vous pouvez consulter le [l’intégralité du code pour la page Modifier le film](#Complete_Page_Listing_for_EditMovie) qui s’affiche à la fin de ce didacticiel.)

[!code-csharp[Main](updating-data/samples/sample12.cs)]

Là encore, cette balise et le code est similaire au code dans *AddMovie*. Le code est dans un `if(IsPost)` bloquer, étant donné que ce code s’exécute uniquement lorsque l’utilisateur clique sur le **soumettre les modifications** bouton &mdash; , autrement dit, si (et seulement si) la forme a été validée. Dans ce cas, vous n’utilisez pas un test comme `if(IsPost && Validation.IsValid())`: autrement dit, vous combinez pas les deux tests à l’aide d’and. Dans cette page, vous d’abord déterminer s’il existe un envoi de formulaire (`if(IsPost)`) et ensuite inscrire les champs pour la validation. Ensuite, vous pouvez tester les résultats de validation (`if(Validation.IsValid()`). Le flux est légèrement différent de celle de la *AddMovie.cshtml* page, mais l’effet est le même.

Vous obtenez les valeurs des zones de texte à l’aide de `Request.Form["title"]` et un code similaire pour les autres `<input>` éléments. Notez que cette fois-ci, le code obtient l’ID de film hors du champ masqué (`<input type="hidden">`). Lorsque la page a exécuté la première fois, le code obtenu l’ID de la chaîne de requête. Vous obtenez la valeur du champ masqué pour vous assurer que vous obtenez l’ID de la séquence affichée à l’origine, au cas où la chaîne de requête a été modifiée depuis.

La différence très importante entre les *AddMovie* code et ce code est d’utiliser l’instruction SQL dans ce code `Update` instruction au lieu du `Insert Into` instruction. L’exemple suivant montre la syntaxe de l’instruction SQL `Update` instruction :

`UPDATE table SET col1="value", col2="value", col3="value" ... WHERE ID = value`

Vous pouvez spécifier des colonnes dans n’importe quel ordre, et ne sont pas nécessairement à mettre à jour chaque colonne pendant une `Update` opération. (Vous ne peut pas mettre à jour le code lui-même, qui est en vigueur l’enregistrer en tant qu’un nouvel enregistrement, et qui n’est pas autorisée pour une `Update` opération.)

> [!NOTE] 
> 
> **Important** le `Where` clause avec l’ID est très important, car c’est la façon dont la base de données sait quelle base de données enregistrement que vous souhaitez mettre à jour. Si vous l’avez laissé le `Where` clause, la base de données est mise à jour *chaque* enregistrement dans la base de données. Dans la plupart des cas, qui correspond à un incident.


Dans le code, les valeurs à mettre à jour sont passées à l’instruction SQL à l’aide des espaces réservés. Pour répéter ce que nous avons dit avant : pour des raisons de sécurité, *uniquement* utiliser des espaces réservés pour passer des valeurs à une instruction SQL.

Une fois que le code utilise `db.Execute` pour exécuter le `Update` instruction, il est redirigé vers la page de liste, où vous pouvez voir les modifications.

> [!TIP] 
> 
> **Instructions SQL différente, différentes méthodes**
> 
> Vous avez peut-être remarqué que vous utilisez des méthodes légèrement différentes pour exécuter des instructions SQL différentes. Pour exécuter un `Select` qui seraient retourne plusieurs enregistrements, vous utilisez la `Query` (méthode). Pour exécuter un `Select` requête qui retourne un seul élément de la base de données, vous utilisez la `QuerySingle` (méthode). Pour exécuter des commandes qui apporter des modifications, mais qui ne renvoient pas les éléments de base de données, vous utilisez la `Execute` (méthode).
> 
> Vous devez disposer de différentes méthodes, car chacun d’eux retourne des résultats différents, comme vous avez déjà vu à la différence entre `Query` et `QuerySingle`. (Le `Execute` méthode retourne une valeur également &mdash; , à savoir, le nombre de lignes de base de données qui ont été affectés par la commande &mdash; , mais vous avez été ignoré qui jusqu'à présent.)
> 
> Bien entendu, le `Query` méthode peut retourner qu’une seule ligne de base de données. Toutefois, ASP.NET traite toujours les résultats de la `Query` méthode sous forme de collection. Même si la méthode retourne une seule ligne, vous devez extraire cette ligne unique de la collection. Par conséquent, dans les situations où vous *savoir* vous allez obtenir qu’une seule ligne, il est un peu plus pratique d’utiliser `QuerySingle`.
> 
> Il existe plusieurs autres méthodes qui effectuent des types spécifiques d’opérations de base de données. Vous trouverez une liste des méthodes de base de données dans le [référence rapide de l’API ASP.NET Web Pages](../../api-reference/asp-net-web-pages-api-reference.md#Data).


## <a name="making-validation-for-the-id-more-robust"></a>Effectue une Validation pour le code plus robuste

La première fois que la page s’exécute, vous obtenez l’ID de film à partir de la chaîne de requête afin que vous puissiez accéder à obtenir cette vidéo à partir de la base de données. En réalité survenue d’une valeur pour accéder à rechercher, lequel vous l’avez fait à l’aide de ce code vérifié :

[!code-csharp[Main](updating-data/samples/sample13.cs)]

Ce code vous permet de vous assurer que si un utilisateur obtient une le *EditMovies* page sans avoir à sélectionner un film dans le *films* page, la page affiche un message d’erreur convivial. (Dans le cas contraire, les utilisateurs seraient voir une erreur qui aurait probablement simplement les confondre.)

Toutefois, cette validation n’est pas très robuste. La page peut également être appelée avec ces erreurs :

- L’ID n’est pas un nombre. Par exemple, la page peut être appelée avec une URL telle que `http://localhost:nnnnn/EditMovie?id=abc`.
- L’ID est un nombre, mais elle fait référence à un film qui n’existe pas (par exemple, `http://localhost:nnnnn/EditMovie?id=100934`).

Si vous n’êtes pour découvrir les erreurs qui résultent de ces URL, exécutez le *films* page. Sélectionnez un film à modifier, puis modifiez l’URL de la *EditMovie* page vers une URL qui contient une liste alphabétique, ID ou l’ID d’un film inexistant.

Par conséquent, que devez-vous faire ? La première solution consiste à vous assurer que non seulement est un ID transmis à la page, mais que l’ID est un entier. Modifier le code de la `!IsPost` test ressemble à cet exemple :

[!code-csharp[Main](updating-data/samples/sample14.cs)]

Vous avez ajouté une seconde condition à la `IsEmpty` test, reliée par `&&` (et logique) :

[!code-csharp[Main](updating-data/samples/sample15.cs)]

Vous pouvez vous rappeler à partir de la [Introduction à la programmation de Pages Web ASP.NET](../introducing-razor-syntax-c.md) didacticiel méthodes telles que `AsBool` un `AsInt` convertir une chaîne de caractères en un autre type de données. Le `IsInt` (méthode) (et d’autres, telles que `IsBool` et `IsDateTime`) sont similaires. Toutefois, de test uniquement si vous *pouvez* convertir la chaîne, sans exécuter réellement la conversion. Ici vous dites essentiellement *si la valeur de chaîne de requête peut être convertie en entier en cours...* .

L’autre problème potentiel cherche un film qui n’existe pas. Le code pour obtenir un film ressemble à ce code :

[!code-csharp[Main](updating-data/samples/sample16.cs)]

Si vous passez un `movieId` valeur le `QuerySingle` méthode qui ne correspond pas à un film réel, rien n’est retourné et les instructions qui suivent (par exemple, `title=row.Title`) entraîner des erreurs.

Là encore il est facile à corriger. Si le `db.QuerySingle` méthode ne retourne aucun résultat, le `row` variable sera null. Pour vous pouvez de vérifier si le `row` variable a la valeur null avant d’essayer d’obtenir les valeurs à partir de celui-ci. Le code suivant ajoute un `if` bloc autour les instructions qui obtiennent les valeurs de la `row` objet :

[!code-csharp[Main](updating-data/samples/sample17.cs)]

Avec ces deux tests de validation supplémentaires, la page devient plus à toute épreuve. Le code complet pour le `!IsPost` branche ressemble à l’exemple suivant :

[!code-csharp[Main](updating-data/samples/sample18.cs)]

Nous allons Notez une fois de plus que cette tâche n’est recommandé pour un `else` bloc. Si vous ne transmettez pas les tests, le `else` blocs définir des messages d’erreur.

## <a name="adding-a-link-to-return-to-the-movies-page"></a>Ajout d’un lien pour revenir à la Page de films

Un détail final et utile consiste à ajouter un lien vers le *films* page. Dans le flux d’événements d’ordinaire, les utilisateurs commencent à la *films* page, puis cliquez sur une **modifier** lien. Ce qui amène la *EditMovie* page, où ils peuvent modifier la vidéo et cliquez sur le bouton. Une fois que le code traite la modification, il redirige vers le *films* page.

Toutefois :

- L’utilisateur peut décider ne pas apporter de modifications.
- Éventuelles de l’utilisateur à cette page sans cliquer d’abord sur un **modifier** lien dans le *films* page.

Dans les deux cas, que vous souhaitez faciliter la retourner à la liste principale. Il est facile à corriger &mdash; ajoutez le balisage suivant juste après la fermeture `</form>` balise dans le balisage :

[!code-html[Main](updating-data/samples/sample19.html)]

Ce balisage utilise la même syntaxe pour une `<a>` élément que vous avez déjà vu ailleurs. L’URL inclut `~` signifie « la racine du site Web. »

## <a name="testing-the-movie-update-process"></a>Tester le processus de mise à jour de film

Vous pouvez maintenant tester. Exécutez le *films* page, puis cliquez sur **modifier** en regard d’un film. Lorsque le *EditMovie* page s’affiche, apporter des modifications à la vidéo et cliquez sur **soumettre les modifications**. Lorsque la liste de film apparaît, assurez-vous que vos modifications sont affichées.

Pour vous assurer que la validation fonctionne, cliquez sur **modifier** pour une autre animation. Lorsque vous accédez à la *EditMovie* page, désactivez la **Genre** champ (ou **année** champ, ou les deux) et essayez d’envoyer vos modifications. Vous verrez une erreur, comme prévu :

![Modifier la page de film affichant les erreurs de validation](updating-data/_static/image4.png)

Cliquez sur le **revenir à la liste de films** lien pour abandonner vos modifications et revenir à la *films* page.

## <a name="coming-up-next"></a>Prochaine

Dans l’étape suivante du didacticiel, vous verrez comment supprimer un enregistrement vidéo.

## <a name="complete-listing-for-movie-page-updated-with-edit-links"></a>Liste complète pour la Page de film (mis à jour avec des liens d’édition)

[!code-cshtml[Main](updating-data/samples/sample20.cshtml)]

<a id="Complete_Page_Listing_for_EditMovie"></a>
## <a name="complete-page-listing-for-edit-movie-page"></a>Terminer l’annonce de Page pour la Page de modification du film

[!code-cshtml[Main](updating-data/samples/sample21.cshtml)]

## <a name="additional-resources"></a>Ressources supplémentaires

- [Introduction à la programmation Web ASP.NET à l’aide de la syntaxe Razor](../../getting-started/introducing-razor-syntax-c.md)
- [Instruction de mise à jour de SQL](http://www.w3schools.com/sql/sql_update.asp) sur le site W3Schools

>[!div class="step-by-step"]
[Précédent](entering-data.md)
[Suivant](deleting-data.md)
