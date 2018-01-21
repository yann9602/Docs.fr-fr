---
title: "Validation de modèle dans ASP.NET MVC de base"
author: rachelappel
description: "En savoir plus sur la validation de modèle dans ASP.NET MVC de base."
ms.author: riande
manager: wpickett
ms.date: 12/18/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/validation
ms.openlocfilehash: 91db17e103723ac411a2ad4f3f9549860f250cce
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="introduction-to-model-validation-in-aspnet-core-mvc"></a>Introduction à la validation de modèle dans ASP.NET MVC de base

Par [Rachel Appel](https://github.com/rachelappel)

## <a name="introduction-to-model-validation"></a>Introduction à la validation du modèle

Avant d’une application stocke des données dans une base de données, l’application doit valider les données. Données doivent être vérifiées par des menaces de sécurité, vérifiés que sa mise en forme correctement par le type et la taille, et il doit être conforme à vos règles de. La validation est nécessaire même si elle peut être fastidieux à implémenter et redondants. Dans MVC, la validation se produit sur le client et le serveur.

Heureusement, .NET a abstraits validation dans les attributs de validation. Ces attributs contiennent un code de validation, ce qui réduit la quantité de code à écrire.

[Afficher ou télécharger l’exemple à partir de GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample).

## <a name="validation-attributes"></a>Attributs de validation

Attributs de validation sont un moyen pour configurer la validation de modèle afin qu’il est similaire sur le plan conceptuel à la validation des champs dans les tables de base de données. Cela inclut les contraintes telles que l’affectation de types de données ou les champs obligatoires. Autres types de validation incluent l’application de modèles de données pour appliquer des règles d’entreprise, tel qu’une carte de crédit, numéro de téléphone, ou l’adresse de messagerie. Attributs de validation rendent l’application de ces exigences beaucoup plus simples et plus faciles à utiliser.

Voici un annoté `Movie` modèle à partir d’une application qui stocke des informations sur les films et les émissions de télévision. La plupart des propriétés est nécessaire, et plusieurs propriétés de chaîne ont des exigences de longueur. En outre, il n’est une restriction de plage numérique en place pour le `Price` propriété à partir de 0 à $999,99, ainsi que d’un attribut de validation personnalisé.

[!code-csharp[Main](validation/sample/Movie.cs?range=6-29)]

En lisant simplement le modèle, vous affichez les règles concernant les données de cette application, rendant ainsi plus facile à maintenir le code. Voici plusieurs attributs de validation intégrées courants :

* `[CreditCard]`: Valide la propriété a un format de carte de crédit.

* `[Compare]`: Valide les deux propriétés dans une correspondance de modèle.

* `[EmailAddress]`: Valide la propriété a un format de courrier électronique.

* `[Phone]`: Valide la propriété a un format de téléphone.

* `[Range]`: Valide la valeur de propriété se situe dans la plage donnée.

* `[RegularExpression]`: Vérifie que les données correspond à l’expression régulière spécifiée.

* `[Required]`: Rend une propriété requise.

* `[StringLength]`: Valide une propriété de chaîne qu’au maximum la longueur maximale spécifiée.

* `[Url]`: Valide la propriété a un format d’URL.

MVC prend en charge tout attribut qui dérive de `ValidationAttribute` à des fins de validation. Vous trouverez d’attributs de validation utile dans les [System.ComponentModel.DataAnnotations](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations) espace de noms.

Il peut y avoir des instances où vous avez besoin de davantage de fonctionnalités pour fournissent des attributs prédéfinis. Dans ces cas, vous pouvez créer des attributs de validation personnalisé en dérivant de `ValidationAttribute` ou la modification de votre modèle à mettre en œuvre `IValidatableObject`.

## <a name="notes-on-the-use-of-the-required-attribute"></a>Remarques sur l’utilisation de l’attribut requis

Les [types valeur](/dotnet/csharp/language-reference/keywords/value-types) non Nullable (tels que `decimal`, `int`, `float` et `DateTime`) sont obligatoires par nature et n’ont pas besoin de l’attribut `Required`. L’application n’effectue aucune vérification de la validation côté serveur pour les types non nullable sont marquées `Required`.

Liaison de modèle MVC, ce qui n’est pas concerné par la validation et les attributs de validation, rejette l’envoi d’un champ de formulaire contenant une valeur manquante ou un espace blanc pour un type non nullable. En l’absence d’un `BindRequired` attribut ignore les données manquantes pour les types non nullable, où le champ de formulaire est absent sur la propriété cible, liaison de modèle à partir des données de formulaire entrantes.

Le [BindRequired attribut](/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.bindrequiredattribute) (consultez également [personnaliser le comportement de liaison de modèle avec des attributs](xref:mvc/models/model-binding#customize-model-binding-behavior-with-attributes)) est utile pour vérifier les données de formulaire soient terminées. Quand il est appliqué à une propriété, le système de liaison de modèle requiert une valeur pour cette propriété. Quand il est appliqué à un type, le système de liaison de modèle requiert des valeurs pour toutes les propriétés de ce type.

Lorsque vous utilisez un [Nullable\<T > type](/dotnet/csharp/programming-guide/nullable-types/) (par exemple, `decimal?` ou `System.Nullable<decimal>`) et marquez-la `Required`, une vérification de la validation côté serveur est effectuée que si la propriété de type nullable (pour standard l’exemple, un `string`).

La validation côté client requiert une valeur pour un champ de formulaire qui correspond à une propriété de modèle que vous avez marqué `Required` et pour une propriété de type n’acceptant que vous n’avez pas marquées `Required`. `Required`peut être utilisé pour contrôler le message d’erreur de validation côté client.

## <a name="model-state"></a>État du modèle

État du modèle représente des erreurs de validation dans les valeurs de formulaire HTML envoyés.

MVC continue validation des champs jusqu'à ce qu’atteigne le nombre maximal d’erreurs (200 par défaut). Vous pouvez configurer ce nombre en insérant le code suivant dans le `ConfigureServices` méthode dans le *Startup.cs* fichier :

[!code-csharp[Main](validation/sample/Startup.cs?range=27)]

## <a name="handling-model-state-errors"></a>État de modèle de gestion des erreurs

Validation du modèle se produit avant chaque action du contrôleur qui est appelée, et il incombe de la méthode d’action à inspecter `ModelState.IsValid` et réagir de façon appropriée. Dans de nombreux cas, la réaction appropriée doit renvoyer une réponse d’erreur, dans l’idéal, détaillant la raison de l’échec de validation du modèle.

Certaines applications choisit de suivre une convention standard pour traiter les erreurs de validation de modèle, auquel cas un filtre peut être un emplacement approprié pour implémenter une telle stratégie. Vous devez tester le comportement de vos actions avec les États de modèles valides et non valides.

## <a name="manual-validation"></a>Validation manuelle

Une fois la validation et la liaison de modèle, vous souhaiterez répéter des parties de celui-ci. Par exemple, un utilisateur peut-être entré du texte dans un champ attendu d’un entier, ou vous devrez peut-être calculer une valeur pour les propriétés d’un modèle.

Vous devrez peut-être exécuter manuellement la validation. Pour ce faire, appelez le `TryValidateModel` méthode, comme illustré ici :

[!code-csharp[Main](validation/sample/MoviesController.cs?range=52)]

## <a name="custom-validation"></a>Validation personnalisée

Attributs de validation fonctionnent pour la plupart des besoins de la validation. Toutefois, des règles de validation sont spécifiques à votre entreprise. Vos règles peut-être pas les techniques de validation de données courantes telles que vous être assuré d’un champ est obligatoire ou qu’il est conforme à une plage de valeurs. Pour ces scénarios, les attributs de validation personnalisés sont une excellente solution. Il est facile de créer vos propres attributs de validation personnalisée dans MVC. Uniquement hériter de la `ValidationAttribute`et remplacez le `IsValid` (méthode). Le `IsValid` méthode accepte deux paramètres, le premier est un objet nommé *valeur* et le second est un `ValidationContext` objet nommé *validationContext*. *Valeur* fait référence à la valeur réelle du champ de la validation de votre validateur personnalisé.

Dans l’exemple suivant, une règle d’entreprise stipule que les utilisateurs ne peuvent pas définir le genre *classique* pour une vidéo publiée après le 1960. Le `[ClassicMovie]` attribut vérifie d’abord le genre et s’il s’agit d’un standard, il vérifie ensuite la date de publication pour qu’il s’agit 1960 plus tard. Si elle est libérée après 1960, la validation échoue. L’attribut accepte un paramètre entier qui représente l’année que vous pouvez utiliser pour valider les données. Vous pouvez capturer la valeur du paramètre dans le constructeur de l’attribut, comme illustré ici :

[!code-csharp[Main](validation/sample/ClassicMovieAttribute.cs?range=9-29)]

Le `movie` variable ci-dessus représente un `Movie` objet qui contient les données à partir de l’envoi du formulaire à valider. Dans ce cas, le code de validation vérifie la date et le genre dans le `IsValid` méthode de la `ClassicMovieAttribute` classe conformément aux règles. Si la validation réussit `IsValid` retourne un `ValidationResult.Success` code, et lorsque la validation échoue, un `ValidationResult` avec un message d’erreur. Quand un utilisateur modifie le `Genre` champ et envoie le formulaire, le `IsValid` méthode de le `ClassicMovieAttribute` doit vérifier si la séquence est un standard. Comme n’importe quel attribut intégré, vous devez appliquer le `ClassicMovieAttribute` à une propriété telle que `ReleaseDate` pour garantir la validation se produit, comme indiqué dans l’exemple de code précédent. Étant donné que l’exemple fonctionne uniquement avec `Movie` types, une meilleure option consiste à utiliser `IValidatableObject` comme indiqué dans le paragraphe suivant.

Ou bien, ce code même pu être placé dans le modèle en implémentant la `Validate` méthode sur le `IValidatableObject` interface. Tandis que les attributs de validation personnalisés fonctionnent bien pour la validation des propriétés individuelles, implémentation `IValidatableObject` peut être utilisé pour implémenter la validation au niveau de la classe comme illustré ci-après.

[!code-csharp[Main](validation/sample/MovieIValidatable.cs?range=32-40)]

## <a name="client-side-validation"></a>Validation côté client

Validation côté client est très pratique pour les utilisateurs. Il enregistre à temps serait sinon attend un aller-retour sur le serveur. En termes de l’entreprise, même de quelques fractions de secondes multipliés des centaines de fois par jour ajoute jusqu'à beaucoup de temps, des frais et frustration. Validation simple et immédiate permet aux utilisateurs de travailler plus efficacement et de produire une meilleure qualité d’entrée et sortie.

Vous devez disposer d’une vue avec les références de script JavaScript appropriées en place pour la validation côté client fonctionnent comme vous le voyez ici.

[!code-cshtml[Main](validation/sample/Views/Shared/_Layout.cshtml?range=37)]

[!code-cshtml[Main](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml)]

Le [jQuery Validation non Obstrusive](https://github.com/aspnet/jquery-validation-unobtrusive) script est une bibliothèque de frontale personnalisée de Microsoft qui s’appuie sur le courant [jQuery validation](https://jqueryvalidation.org/) plug-in. Sans jQuery Validation discrète, vous devrez la même logique de validation à deux emplacements de code : une fois dans les attributs de validation côté serveur sur les propriétés du modèle, puis à nouveau dans les scripts côté client (les exemples de jQuery validation [ `validate()` ](https://jqueryvalidation.org/validate/) méthode montre comment complexe cela peut devenir). Au lieu de cela, de MVC [programmes d’assistance de balise](xref:mvc/views/tag-helpers/intro) et [programmes d’assistance HTML](xref:mvc/views/overview) sont en mesure d’utiliser les attributs de validation et de métadonnées à partir des propriétés de modèle pour effectuer le rendu HTML 5 de type [attributs de données](http://w3c.github.io/html/dom.html#embedding-custom-non-visible-data-with-the-data-attributes) dans les éléments de formulaire nécessitant une validation. MVC génère le `data-` attributs pour les attributs intégrés et personnalisés. jQuery non obstructive Validation analyse ensuite ces `data-` les attributs et transmet la logique de jQuery validation, en réalité « copie » la logique de validation côté serveur au client. Vous pouvez afficher les erreurs de validation sur le client en utilisant les programmes d’assistance de balise appropriés comme indiqué ici :

[!code-cshtml[Main](validation/sample/Views/Movies/Create.cshtml?highlight=4,5&range=19-25)]

Les programmes d’assistance de balise ci-dessus de rendu HTML ci-dessous. Notez que la `data-` attributs dans le code HTML de sortie correspondent aux attributs de validation pour le `ReleaseDate` propriété. Le `data-val-required` attribut ci-dessous contient un message d’erreur à afficher si l’utilisateur ne remplit pas dans le champ date de mise en production. jQuery Validation discrète passe cette valeur à la validation jQuery [ `required()` ](https://jqueryvalidation.org/required-method/) (méthode), qui affiche alors ce message en l’accompagnant  **\<span >** élément.

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="" />
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

Empêche la validation côté client soumission jusqu'à ce que le formulaire est valid. Le bouton d’envoi s’exécute JavaScript qui envoie le formulaire ou affiche des messages d’erreur.

MVC détermine les valeurs d’attribut de type en fonction du type de données .NET d’une propriété, et éventuellement remplacé à l’aide `[DataType]` attributs. La base de `[DataType]` attribut aucune validation n’est réel côté serveur. Navigateurs choisir leurs propres messages d’erreur et affichent ces erreurs, mais ils le souhaitent, toutefois package jQuery Validation discrète peut remplacer les messages et les afficher de manière cohérente avec d’autres. Cela produit plus évidemment lorsque les utilisateurs appliquent `[DataType]` sous-classes comme `[EmailAddress]`.

### <a name="add-validation-to-dynamic-forms"></a>Ajouter une Validation aux formulaires dynamiques

Car jQuery Validation discrète transmet la logique de validation et les paramètres jQuery validation lors du premier charge de la page, les formulaires générés de manière dynamique pas automatiquement présentera de validation. Au lieu de cela, vous devez indiquer jQuery Validation discrète pour analyser la forme dynamique immédiatement après sa création. Par exemple, le code ci-dessous montre comment vous pouvez configurer la validation côté client sur un formulaire ajoutée via AJAX.

```js
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Could not add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

Le `$.validator.unobtrusive.parse()` méthode accepte un sélecteur de jQuery pour un argument. Cette méthode indique à jQuery Validation discrète pour analyser les `data-` attributs des formes au sein de ce sélecteur. Les valeurs de ces attributs sont ensuite passées pour le plug-in de la validation jQuery afin que le formulaire présente les règles de validation côté client souhaité.

### <a name="add-validation-to-dynamic-controls"></a>Ajouter une Validation à des contrôles dynamiques

Vous pouvez également mettre à jour les règles de validation sur un formulaire lorsque les contrôles individuels, tels que `<input/>`s et `<select/>`s, sont générées de façon dynamique. Vous ne pouvez pas passer des sélecteurs pour ces éléments pour le `parse()` directement la méthode, car le formulaire qui l’entoure a déjà été analysé et ne met pas à jour. Au lieu de cela, vous supprimez d’abord les données de validation existantes, puis d’analyse de l’intégralité du formulaire, comme indiqué ci-dessous :

```js
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Could not add form. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        form.removeData("validator")    // Added by the raw jQuery Validate
            .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="iclientmodelvalidator"></a>IClientModelValidator

Vous pouvez créer une logique côté client pour votre attribut personnalisé, et [validation discrète](http://jqueryvalidation.org/documentation/) exécutera sur le client pour vous automatiquement dans le cadre de la validation. La première étape consiste à contrôler les attributs de données sont ajoutés en implémentant le `IClientModelValidator` interface comme indiqué ici :

[!code-csharp[Main](validation/sample/ClassicMovieAttribute.cs?range=30-42)]

Les attributs qui implémentent cette interface peuvent ajouter des attributs HTML pour les champs générés. Examen de la sortie pour le `ReleaseDate` élément révèle HTML qui est similaire à l’exemple précédent, mais il existe désormais un `data-val-classicmovie` attribut qui a été défini dans le `AddValidation` méthode de `IClientModelValidator`.

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="" />
```

Validation non obstrusive utilise les données dans le `data-` les attributs à afficher des messages d’erreur. Toutefois, jQuery ne connaît pas les règles ou des messages jusqu'à ce que vous les ajoutez à de jQuery `validator` objet. Cela est illustré dans l’exemple ci-dessous ajoute une méthode nommée `classicmovie` contenant le code de validation client personnalisés pour le jQuery `validator` objet.

[!code-javascript[Main](validation/sample/Views/Movies/Create.cshtml?range=71-93)]

JQuery possède maintenant les informations pour exécuter la validation personnalisée de JavaScript, ainsi que le message d’erreur à afficher si le code de validation qui retourne la valeur false.

## <a name="remote-validation"></a>Validation à distance

Validation à distance est une fonctionnalité intéressante à utiliser lorsque vous avez besoin valider les données sur le client par rapport aux données sur le serveur. Par exemple, votre application peut avoir besoin vérifier si un nom d’utilisateur ou de messagerie est déjà en cours d’utilisation, et il doit interroger une grande quantité de données à le faire. Téléchargement de grands jeux de données pour la validation d’un ou plusieurs champs consomme trop de ressources. Il peut également exposer des informations sensibles. Une alternative consiste à effectuer une demande de parcours circulaire pour valider un champ.

Vous pouvez implémenter la validation à distance dans un processus en deux étapes. Tout d’abord, vous devez annoter votre modèle avec la `[Remote]` attribut. Le `[Remote]` attribut accepte plusieurs surcharges qui vous permet de diriger le côté client JavaScript pour le code approprié à appeler. L’exemple ci-dessous pointe vers le `VerifyEmail` méthode d’action de la `Users` contrôleur.

[!code-csharp[Main](validation/sample/User.cs?range=7-8)]

La deuxième étape pour placer le code de validation dans la méthode d’action correspondante tel que défini dans le `[Remote]` attribut. En fonction de la validation jQuery [ `remote()` ](https://jqueryvalidation.org/remote-method/) documentation sur la méthode :

> La réponse serverside doit être une chaîne JSON qui doit être `"true"` pour les éléments valides et peut être `"false"`, `undefined`, ou `null` pour les éléments non valides, à l’aide de message d’erreur par défaut. Si la réponse serverside est une chaîne, par exemple. `"That name is already taken, try peter123 instead"`, cette chaîne s’affichera sous la forme d’un message d’erreur personnalisé à la place de la valeur par défaut.

La définition de la `VerifyEmail()` méthode suit ces règles, comme indiqué ci-dessous. Il renvoie une erreur de validation de message si l’adresse de messagerie est effectuée, ou `true` si l’adresse de messagerie est libre et encapsule le résultat dans un `JsonResult` objet. Du côté client permet ensuite la valeur retournée pour continuer ou sur l’erreur s’affiche si nécessaire.

[!code-csharp[Main](validation/sample/UsersController.cs?range=19-28)]

Maintenant lorsque les utilisateurs entrent un message électronique, JavaScript dans la vue effectue un appel à distance pour voir si l’e-mail a été utilisé et, dans ce cas, affiche le message d’erreur. Dans le cas contraire, l’utilisateur peut soumettre le formulaire comme d’habitude.

Le `AdditionalFields` propriété de la `[Remote]` attribut est utile pour valider les combinaisons de champs sur des données sur le serveur. Par exemple, si le `User` modèle ci-dessus a deux propriétés supplémentaires : `FirstName` et `LastName`, vous pouvez souhaiter vérifier qu’aucun utilisateur n’existant a déjà cette paire de noms. Vous définissez les nouvelles propriétés comme indiqué dans le code suivant :

[!code-csharp[Main](validation/sample/User.cs?range=10-13)]

`AdditionalFields`Impossible ont été définis explicitement sur les chaînes `"FirstName"` et `"LastName"`, mais en utilisant le [ `nameof` ](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/nameof) opérateur comme cela simplifie la refactorisation ultérieurement. La méthode d’action pour effectuer la validation doit accepter deux arguments, un pour la valeur de `FirstName` et l’autre pour la valeur de `LastName`.


[!code-csharp[Main](validation/sample/UsersController.cs?range=30-39)]

Maintenant lorsque les utilisateurs entrer un nom et prénom, JavaScript :

* Effectue un appel à distance pour voir si cette paire de noms a été prise.
* Si la paire a été effectuée, un message d’erreur s’affiche. 
* Si ne pas le cas, l’utilisateur peut envoyer le formulaire.

Si vous devez valider deux ou plusieurs champs supplémentaires avec le `[Remote]` attribut, vous lui fournir sous forme de liste délimitée par des virgules. Par exemple, pour ajouter un `MiddleName` propriété du modèle, affectez le `[Remote]` d’attributs comme indiqué dans le code suivant :

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`, comme tous les arguments d’attribut, doit être une expression constante. Par conséquent, vous ne devez pas utiliser un [interpolées chaîne](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/interpolated-strings) ou appelez [ `string.Join()` ](https://msdn.microsoft.com/en-us/library/system.string.join(v=vs.110).aspx) pour initialiser `AdditionalFields`. Pour chaque champ supplémentaire que vous ajoutez à la `[Remote]` attribut, vous devez ajouter un autre argument à la méthode d’action de contrôleur correspondant.
