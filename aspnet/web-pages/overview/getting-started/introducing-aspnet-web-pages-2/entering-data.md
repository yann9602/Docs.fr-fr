---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/entering-data
title: "Présentation des Pages Web ASP.NET - saisie de données de la base de données à l’aide de formulaires | Documents Microsoft"
author: tfitzmac
description: "Ce didacticiel vous montre comment créer un formulaire d’entrée, puis entrez les données que vous obtenez à partir du formulaire dans une table de base de données lorsque vous utilisez ASP.NET Web Pages (...)"
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/28/2015
ms.topic: article
ms.assetid: d37c93fc-25fd-4e94-8671-0d437beef206
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/entering-data
msc.type: authoredcontent
ms.openlocfilehash: b74eecb16b2c4695bb417816b90f701f724cc9d0
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="introducing-aspnet-web-pages---entering-database-data-by-using-forms"></a>Présentation des Pages Web ASP.NET - saisie de données de la base de données à l’aide de formulaires
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Ce didacticiel vous montre comment créer un formulaire d’entrée, puis entrez les données que vous obtenez à partir du formulaire dans une table de base de données lorsque vous utilisez les Pages Web ASP.NET (Razor). Il suppose que vous avez terminé la série via [principes de base de formulaires HTML dans les Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=251581).
> 
> Ce que vous allez apprendre :
> 
> - Plus d’informations sur la façon de traiter les formulaires de saisie.
> - Comment ajouter les données (insert) dans une base de données.
> - Comment s’assurer que les utilisateurs ont entré une valeur requise dans un formulaire (comment valider l’entrée d’utilisateur).
> - Comment afficher les erreurs de validation.
> - Comment accéder à une autre page à partir de la page actuelle.
>   
> 
> Fonctionnalités technologies abordées :
> 
> - Méthode `Database.Execute`
> - L’instruction SQL `Insert Into` instruction
> - Le `Validation` helper.
> - Méthode `Response.Redirect`


## <a name="what-youll-build"></a>Ce que vous allez générer

Dans le didacticiel plus haut qui vous a montré comment créer une base de données, vous avez entré les données de la base de données en modifiant la base de données directement dans WebMatrix, utilisation les **base de données** espace de travail. Dans la plupart des applications, qui n’est pas un moyen pratique de placer des données dans la base de données, cependant. Par conséquent, dans ce didacticiel, vous allez créer une interface web qui vous permet de vous ou un utilisateur d’entrer des données et l’enregistrer dans la base de données.

Vous allez créer une page où vous pouvez saisir de nouveaux films. La page contient un formulaire d’entrée qui a des champs (zones de texte) dans lequel vous pouvez entrer un titre de film, genre et année. La page doit ressembler à cette page :

![Ajouter des films de page dans le navigateur](entering-data/_static/image1.png)

Les zones de texte sera au format HTML `<input>` éléments qui va rechercher tels que ce balisage :

`<input type="text" name="genre" value="" />`

## <a name="creating-the-basic-entry-form"></a>Création du formulaire d’entrée de base

Créer une page nommée *AddMovie.cshtml*.

Remplacez ce qui est dans le fichier avec le balisage suivant. Remplacer tous les éléments ; vous allez ajouter un bloc de code en haut peu de temps.

[!code-cshtml[Main](entering-data/samples/sample1.cshtml)]

Cet exemple montre le HTML standard pour la création d’un formulaire. Il utilise `<input>` éléments pour les zones de texte et le bouton Envoyer. Les légendes pour les zones de texte sont créées à l’aide standard `<label>` éléments. Le `<fieldset>` et `<legend>` éléments mettre une zone nice autour du formulaire.

Notez que dans cette page, le `<form>` élément utilise `post` comme valeur pour le `method` attribut. Dans le didacticiel précédent, vous avez créé un formulaire utilisé le `get` (méthode). Qui a été correct, car bien que les valeurs sur le serveur de l’envoi du formulaire, la demande ne pas apporter des modifications. All c’était le cas a été extraire les données de différentes façons. Toutefois, dans cette page vous *sera* apporter des modifications, vous allez ajouter de nouveaux enregistrements de base de données. Par conséquent, cette forme doit utiliser le `post` (méthode). (Pour plus d’informations sur la différence entre `GET` et `POST` opérations, consultez le[sécurité du verbe HTTP GET et POST](https://go.microsoft.com/fwlink/?LinkId=251581#GET,_POST,_and_HTTP_Verb_Safety) encadré dans le didacticiel précédent.)

Notez que chaque zone de texte a un `name` élément (`title`, `genre`, `year`). Comme vous l’avez vu dans le didacticiel précédent, ces noms sont importants, car vous devez disposer de ces noms afin d’obtenir l’entrée d’utilisateur plus tard. Vous pouvez utiliser des noms. Il est utile d’utiliser des noms explicites qui vous aident à mémoriser les données avec lequel vous travaillez.

Le `value` attribut de chaque `<input>` élément contient un peu de code Razor (par exemple, `Request.Form["title"]`). Vous avez appris une version de cette astuce dans le didacticiel précédent pour conserver la valeur entrée dans la zone de texte (le cas échéant) une fois que le formulaire a été envoyé.

## <a name="getting-the-form-values"></a>Obtenir les valeurs de formulaire

Ensuite, vous ajoutez le code qui gère le formulaire. Dans la structure, vous devez procédez comme suit :

1. Vérifiez si la page est en cours de publication (a été envoyée). Vous souhaitez que votre code s’exécutent uniquement quand les utilisateurs ont cliqué sur le bouton, pas lors de la première exécution de la page.
2. Obtenir les valeurs entrées par l’utilisateur dans les zones de texte. Dans ce cas, étant donné que le formulaire utilise la `POST` verbe, vous obtenez les valeurs de formulaire à partir de la `Request.Form` collection.
3. Insérez les valeurs en tant qu’un nouvel enregistrement dans le *films* table de base de données.

En haut du fichier, ajoutez le code suivant :

[!code-cshtml[Main](entering-data/samples/sample2.cshtml)]

Les premières lignes créent des variables (`title`, `genre`, et `year`) pour contenir les valeurs dans les zones de texte. La ligne `if(IsPost)` permet de s’assurer que les variables sont définies *uniquement* lorsque les utilisateurs cliquent sur le **ajouter un film** bouton, autrement dit, lorsque le formulaire a été validé.

Comme vous l’avez vu dans un didacticiel antérieur, vous obtenez la valeur d’une zone de texte à l’aide d’une expression telle que `Request.Form["name"]`, où *nom* est le nom de la `<input>` élément.

Les noms des variables (`title`, `genre`, et `year`) sont arbitraires. Comme les noms que vous attribuez à `<input>` éléments, vous pouvez les appeler comme vous le souhaitez. (Les noms des variables n’êtes pas obligé de correspondent aux attributs de nom `<input>` éléments sur le formulaire.) Mais comme avec la `<input>` éléments, il est judicieux d’utiliser des noms de variables qui reflètent les données qu’ils contiennent. Lorsque vous écrivez du code, des noms cohérents rendent plus facile à retenir les données avec lequel vous travaillez.

## <a name="adding-data-to-the-database"></a>Ajout de données à la base de données

Dans le code du bloc vous vient d’être ajoutée, seulement *à l’intérieur de* l’accolade fermante ( `}` ) de la `if` de bloc (et pas seulement à l’intérieur du bloc de code), ajoutez le code suivant :

[!code-csharp[Main](entering-data/samples/sample3.cs)]

Cet exemple est semblable au code que vous avez utilisé dans un didacticiel précédent pour extraire et afficher les données. La ligne qui commence par `db =` ouvre la base de données, comme avant et la ligne suivante définit une instruction SQL, à nouveau en tant que vous l’avez vu avant. Toutefois, cette fois il définit une SQL `Insert Into` instruction. L’exemple suivant montre la syntaxe générale de la `Insert Into` instruction :

`INSERT INTO table (column1, column2, column3, ...) VALUES (value1, value2, value3, ...)`

En d’autres termes, vous spécifiez répertorie ensuite les colonnes à insérer dans la table pour insérer et répertorie ensuite les valeurs à insérer. (Comme mentionné précédemment, SQL n’est pas la casse, mais certaines personnes capitaliser les mots clés pour le rendre plus facile à lire la commande.)

Les colonnes que vous insérez dans figurent déjà dans la commande : `(Title, Genre, Year)`. La partie intéressante est la façon dont vous obtenez les valeurs dans les zones de texte dans le `VALUES` dans le cadre de la commande. Au lieu des valeurs réelles, vous voyez `@0`, `@1`, et `@2`, qui sont bien entendu des espaces réservés. Lorsque vous exécutez la commande (sur le `db.Execute` ligne), vous transmettez les valeurs que vous avez obtenu les zones de texte.

**Important !** N’oubliez pas que la seule façon, vous devez inclure jamais de données d’entrée en ligne par un utilisateur dans une instruction SQL est d’utiliser des espaces réservés, comme vous le voyez ici (`VALUES(@0, @1, @2)`). Si vous concaténez des entrées d’utilisateur dans une instruction SQL, vous ouvrez vous-même à une attaque par injection SQL, comme expliqué dans [principes fondamentaux de formulaire dans ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=251581) (le didacticiel précédent).

Toujours dans le `if` bloquer, ajoutez la ligne suivante après la `db.Execute` ligne :

[!code-css[Main](entering-data/samples/sample4.css)]

Une fois la nouvelle séquence a été insérée dans la base de données, cette ligne vous (redirections) accède à la *films* page afin de voir le film que vous venez d’entrer. Le `~` opérateur signifie « la racine du site Web. » (Le `~` opérateur fonctionne uniquement dans les pages ASP.NET, pas au format HTML en général.)

Le bloc de code complet ressemble à l’exemple suivant :

[!code-cshtml[Main](entering-data/samples/sample5.cshtml)]

## <a name="testing-the-insert-command-so-far"></a>Test de la commande d’insertion (jusqu'à présent)

Vous n’avez pas encore terminé, mais il êtes judicieux de test.

Dans l’arborescence de fichiers dans WebMatrix, cliquez sur le *AddMovie.cshtml* page, puis cliquez sur **lancer dans un navigateur**.

![Ajouter des films de page dans le navigateur](entering-data/_static/image2.png)

(Si vous vous retrouvez avec une autre page dans le navigateur, assurez-vous que l’URL est `http://localhost:nnnnn/AddMovie`), où  *nnnnn*  est le numéro de port que vous utilisez.)

Si vous avez reçu une page d’erreur ? Dans ce cas, le lire attentivement et assurez-vous que le code ressemble exactement ce qui a été répertorié précédemment.

Entrez un film sous la forme &mdash; , par exemple, utilisez « Citoyen Kane », « Documentaires » et « 1941 ». (Ou tout autre.) Puis cliquez sur **ajouter un film**.

Si tout se passe bien, vous êtes redirigé vers la *films* page. Assurez-vous que votre nouveau film est répertorié.

![Page de films affichant qui vient d’être ajouté de film](entering-data/_static/image3.png)

## <a name="validating-user-input"></a>Validation des entrées utilisateur

Revenez à la *AddMovie* page ou exécuter de nouveau. Entrez une autre séquence, mais cette fois, entrez uniquement le titre &mdash; par exemple, entrer « Domicile ' dans train ». Puis cliquez sur **ajouter un film**.

Vous êtes redirigé vers la *films* page à nouveau. Vous trouverez la nouvelle séquence, mais il est incomplet.

![Films montrant nouvelle vidéo que des valeurs est manquantes](entering-data/_static/image4.png)

Lorsque vous avez créé le *films* table, vous avez explicitement dit qu’aucun des champs peut être null. Ici, vous avez un formulaire de saisie de nouveaux films, et vous êtes laisser les champs vides. Il s’agit d’une erreur.

Dans ce cas, la base de données n’a pas été déclenche réellement (ou *lever*) une erreur. Vous n’avez pas fourni un genre ou une année, par conséquent, le code dans le *AddMovie* page traité ces valeurs comme soi-disant *chaînes vides*. Lorsque l’instruction SQL `Insert Into` commande s’est exécutée, les champs de genre et l’année n’a pas des données utiles dans les, mais ils ne sont pas null.

Évidemment, vous ne souhaitez pas permettre aux utilisateurs d’entrer des informations de film de moitié vide dans la base de données. La solution consiste à valider les entrées de l’utilisateur. Au départ, la validation sera Assurez-vous simplement que l’utilisateur a entré une valeur pour tous les champs (autrement dit, qu’aucun d'entre eux contient une chaîne vide).

> [!TIP] 
> 
> **Chaînes null et vides**
> 
> Dans la programmation, il existe une distinction entre les différentes notions « aucune valeur ». En règle générale, une valeur est *null* si qu’il n’a jamais été définie ou initialisé en aucune façon. En revanche, une variable qui attend des données de caractères (chaînes) peut être définie sur une *une chaîne vide*. Dans ce cas, la valeur n’est pas null ; Il est simplement été défini explicitement sur une chaîne de caractères dont la longueur est égale à zéro. Les deux instructions suivantes montrent la différence :
> 
> [!code-csharp[Main](entering-data/samples/sample6.cs)]
> 
> C’est un peu plus compliqué que celle, mais le point important est que `null` représente le tri d’un état indéterminé.
> 
> Maintenant, puis il est important de comprendre exactement quand une valeur est null, et quand elle est simplement une chaîne vide. Dans le code de la *AddMovie* page, vous obtenez les valeurs des zones de texte à l’aide de `Request.Form["title"]` et ainsi de suite. Lorsque la page tout d’abord s’exécute (avant de cliquer sur le bouton), la valeur de `Request.Form["title"]` est null. Mais quand vous envoyez le formulaire, `Request.Form["title"]` Obtient la valeur de la `title` zone de texte. Il n’est pas évident, mais une zone de texte vide n’est pas null ; elle doit simplement une chaîne vide dans celui-ci. Par conséquent, lorsque le code s’exécute en réponse au bouton Cliquez, `Request.Form["title"]` contient une chaîne vide.
> 
> Pourquoi cette distinction est importante ? Lorsque vous avez créé le *films* table, vous avez explicitement dit qu’aucun des champs peut être null. Ici, vous avez un formulaire de saisie de nouveaux films, mais vous êtes laisser les champs vides. Vous vous attendez raisonnablement la base de données se plaignent lorsque vous avez essayé d’enregistrer de nouveaux films qui n’ont des valeurs pour le genre ou année. Mais qui est le point de &mdash; même si vous ne renseignez pas les zones de texte, les valeurs ne sont pas null ; elles sont des chaînes vides. Par conséquent, vous êtes en mesure d’enregistrer de nouveaux films à la base de données avec ces colonnes vides &mdash; mais pas null ! des valeurs &mdash;. Par conséquent, vous devez vous assurer que les utilisateurs n’envoient pas une chaîne vide, ce que vous pouvez faire en validant l’entrée d’utilisateur.


### <a name="the-validation-helper"></a>L’application auxiliaire de Validation

Les Pages Web ASP.NET inclut un programme d’assistance &mdash; le `Validation` assistance &mdash; que vous pouvez utiliser pour vous assurer que les utilisateurs entrent des données qui répond à vos besoins. Le `Validation` d’assistance est un des programmes d’assistance qui est intégré à ASP.NET Web Pages, sans que vous ayez à installer en tant que package à l’aide de NuGet, le mode d’installation de l’application d’assistance Gravatar dans un didacticiel antérieur.

Pour valider l’entrée d’utilisateur, vous devez procédez comme suit :

- Utilisez le code pour spécifier que vous souhaitez requièrent des valeurs dans les zones de texte sur la page.
- Placez un test dans le code afin que les informations de séquence sont ajoutées à la base de données uniquement si tous les éléments valide correctement.
- Ajoutez le code dans le balisage pour afficher les messages d’erreur.

Dans le bloc de code dans le *AddMovie* page, à droite vers le haut en haut avant les déclarations de variable, ajoutez le code suivant :

[!code-csharp[Main](entering-data/samples/sample7.cs)]

Vous appelez `Validation.RequireField` une fois pour chaque champ (`<input>` élément) où vous souhaitez exiger une entrée. Vous pouvez également ajouter un message d’erreur personnalisé pour chaque appel, comme indiqué ici. (Nous varier les messages uniquement pour montrer que vous pouvez placer comme vous le souhaitez il.)

S’il existe un problème, vous souhaitez empêcher les nouvelles informations de films à partir de laquelle elle est insérée à la base de données. Dans le `if(IsPost)` de bloc, utilisez `&&` (et logique) pour ajouter une autre condition qui teste `Validation.IsValid()`. Lorsque vous avez terminé, l’ensemble `if(IsPost)` bloc ressemble à ce code :

[!code-csharp[Main](entering-data/samples/sample8.cs)]

S’il existe une erreur de validation avec l’un des champs que vous avez enregistré à l’aide de la `Validation` assistance, le `Validation.IsValid` méthode retourne la valeur false. Et dans ce cas, le code dans ce bloc s’exécute, donc aucune entrée de séquence non valide n’est insérée dans la base de données. Et bien sûr vous n’êtes pas redirigé vers la *films* page.

Le bloc de code complet, y compris le code de validation, ressemble à l’exemple suivant :

[!code-cshtml[Main](entering-data/samples/sample9.cshtml?highlight=10)]

## <a name="displaying-validation-errors"></a>Affichage des erreurs de Validation

La dernière étape consiste à afficher des messages d’erreur. Vous pouvez afficher des messages individuels pour chaque erreur de validation, ou vous pouvez afficher un résumé, ou les deux. Pour ce didacticiel, vous allez effectuer les deux afin que vous pouvez voir comment il fonctionne.

En regard de chaque `<input>` élément que vous êtes en train de valider, appelez le `Html.ValidationMessage` méthode et lui transmettre le nom de la `<input>` élément que vous êtes en train de valider. Vous placez le `Html.ValidationMessage` droite méthode où vous souhaitez le message d’erreur s’affiche. Lorsque la page s’exécute, le `Html.ValidationMessage` méthode restitue un `<span>` élément passe où l’erreur de validation. (Si aucune erreur, le `<span>` élément est affiché, mais aucun texte n’est qu’il contient.)

Modifiez le balisage dans la page afin qu’il inclue un `Html.ValidationMessage` méthode pour chacun des trois `<input>` éléments sur la page, tels que cet exemple :

[!code-cshtml[Main](entering-data/samples/sample10.cshtml?highlight=3,8,13)]

Pour voir comment le résumé fonctionne, également ajouter le balisage suivant et de code juste après le `<h1>Add a Movie</h1>` élément sur la page :

[!code-cshtml[Main](entering-data/samples/sample11.cshtml)]

Par défaut, le `Html.ValidationSummary` méthode affiche tous les messages de validation dans une liste (un `<ul>` élément qui se trouve dans un `<div>` élément). Comme avec la `Html.ValidationMessage` (méthode), le balisage pour le résumé des validations est toujours rendu ; s’il n’y a aucune erreur, aucun élément de liste n’est rendus.

Le résumé peut être une autre façon d’afficher les messages de validation à la place d’à l’aide de la `Html.ValidationMessage` méthode pour afficher chaque erreur spécifiques aux champs. Ou vous pouvez utiliser un résumé et les détails. Ou vous pouvez utiliser la `Html.ValidationSummary` méthode pour afficher une erreur générique, puis individuels `Html.ValidationMessage` les appels pour afficher les détails.

La page ressemble à l’exemple suivant :

[!code-cshtml[Main](entering-data/samples/sample12.cshtml)]

C'est tout ! Vous pouvez maintenant tester la page en ajoutant un film tout en conservant une ou plusieurs des champs. Lorsque vous procédez ainsi, vous voyez l’erreur suivante à afficher :

![Ajouter une page de film affichant des messages d’erreur de validation](entering-data/_static/image5.png)

## <a name="styling-the-validation-error-messages"></a>Les Messages d’erreur de Validation de styles

Vous pouvez voir que les messages d’erreur, mais ils ne se distinguent réellement très bien. Il existe un moyen facile d’appliquer un style de bien que les messages d’erreur.

Pour définir le style des messages d’erreur individuels sont affichés par `Html.ValidationMessage`, créez une classe de style CSS nommée `field-validation-error`. Pour définir l’apparence pour le résumé des validations, créez une classe de style CSS nommée `validation-summary-errors`.

Pour voir comment cette technique fonctionne, ajoutez un `<style>` élément à l’intérieur du `<head>` section de la page. Définissez ensuite les classes de style nommées `field-validation-error` et `validation-summary-errors` qui contiennent les règles suivantes :

[!code-cshtml[Main](entering-data/samples/sample13.cshtml?highlight=4-17)]

Normalement vous placeriez probablement les informations de style en distinct *.css* fichier, mais par souci de simplicité vous pouvez les placer dans la page pour l’instant. (Plus loin dans ce jeu de didacticiel, vous allez déplacer les règles CSS séparément *.css* fichier.)

S’il existe une erreur de validation, le `Html.ValidationMessage` méthode restitue un `<span>` élément inclut `class="field-validation-error"`. En ajoutant une définition de style pour cette classe, vous pouvez configurer l’aspect du message. S’il existe des erreurs, la `ValidationSummary` méthode restitue même dynamiquement l’attribut `class="validation-summary-errors"`.

Réexécutez la page et laissez délibérément deux champs. Les erreurs sont désormais plus visibles. (En fait, elles vous allé trop loin, mais qui est uniquement pour montrer que vous pouvez faire.)

![Ajouter une page de film affichant les erreurs de validation qui ont été mise en forme](entering-data/_static/image6.png)

## <a name="adding-a-link-to-the-movies-page"></a>Ajout d’un lien vers la Page de films

Une étape finale consiste à rendre pratique accéder à la *AddMovie* page à partir de la liste de films d’origine.

Ouvrez le *films* page à nouveau. Après la fermeture `</div>` balise qui suit le `WebGrid` assistance, ajoutez le balisage suivant :

[!code-cshtml[Main](entering-data/samples/sample14.cshtml)]

Comme vous l’avez vu précédemment, ASP.NET interprète la `~` opérateur comme la racine du site Web. Vous n’êtes pas obligé d’utiliser le `~` opérateur ; vous pouvez utiliser le balisage `<a href="./AddMovie">Add a movie</a>` ou un autre moyen de définir le chemin d’accès qui comprend du code HTML. Mais le `~` opérateur est une bonne approche générale lorsque vous créez des liens pour les pages Razor, car elle rend le site plus souple, si vous déplacez la page en cours dans un sous-dossier, le lien accédez toujours à la *AddMovie* page. (N’oubliez pas que le `~` opérateur fonctionne uniquement dans *.cshtml* pages. ASP.NET comprend, mais il n’est pas HTML standard.)

Lorsque vous avez terminé, exécutez le *films* page. Il doit ressembler à cette page :

![Page de films avec un lien vers la page de « Ajouter des films »](entering-data/_static/image7.png)

Cliquez sur le **ajouter un film** lien pour vous assurer qu’il est placé dans le *AddMovie* page.

## <a name="coming-up-next"></a>Prochaine

Dans le didacticiel suivant, vous allez apprendre comment permettre aux utilisateurs de modifier les données qui se trouve déjà dans la base de données.

## <a name="complete-listing-for-addmovie-page"></a>Liste complète de la Page de AddMovie

[!code-cshtml[Main](entering-data/samples/sample15.cshtml)]

## <a name="additional-resources"></a>Ressources supplémentaires

- [Introduction à la programmation Web ASP.NET à l’aide de la syntaxe Razor](https://go.microsoft.com/fwlink/?LinkID=202890)
- [Instruction SQL INSERT INTO](http://www.w3schools.com/sql/sql_insert.asp) sur le site W3Schools
- [Validation des entrées d’utilisateur dans ASP.NET Web Pages Sites](https://go.microsoft.com/fwlink/?LinkId=253002). Plus d’informations sur l’utilisation de la `Validation` helper.

>[!div class="step-by-step"]
[Précédent](form-basics.md)
[Suivant](updating-data.md)
