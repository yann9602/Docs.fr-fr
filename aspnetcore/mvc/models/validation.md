---
title: "Validation de modèle dans ASP.NET MVC de base"
author: rick-anderson
description: "Présente la validation de modèle dans ASP.NET MVC de base."
keywords: Validation ASP.NET Core, MVC,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 3a8676dd-7ed8-4a05-bca2-44e288ab99ee
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/validation
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 514c7770b7f508893a059c1adcf56204160aceda
ms.sourcegitcommit: 275a5381b6172b4f0b5fcd1d252aff03d3dae166
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2017
---
# <a name="introduction-to-model-validation-in-aspnet-core-mvc"></a>Introduction à la validation de modèle dans ASP.NET MVC de base

Par [Rachel Appel](http://github.com/rachelappel)

## <a name="introduction-to-model-validation"></a>Introduction à la validation du modèle

Avant d’une application stocke des données dans une base de données, l’application doit valider les données. Données doivent être vérifiées par des menaces de sécurité, vérifiés que sa mise en forme correctement par le type et la taille, et il doit être conforme à vos règles de. La validation est nécessaire même si elle peut être fastidieux à implémenter et redondants. Dans MVC, la validation se produit sur le client et le serveur.

Heureusement, .NET a abstraits validation dans les attributs de validation. Ces attributs contiennent un code de validation, ce qui réduit la quantité de code à écrire.

[Afficher ou télécharger l’exemple à partir de GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample).

## <a name="validation-attributes"></a>Attributs de validation

Attributs de validation sont un moyen pour configurer la validation de modèle afin qu’il est similaire sur le plan conceptuel à la validation des champs dans les tables de base de données. Cela inclut les contraintes telles que l’affectation de types de données ou les champs obligatoires. Autres types de validation incluent l’application de modèles de données pour appliquer des règles d’entreprise, tel qu’une carte de crédit, numéro de téléphone, ou l’adresse de messagerie. Attributs de validation rendent l’application de ces exigences beaucoup plus simples et plus faciles à utiliser.

Voici un annoté `Movie` modèle à partir d’une application qui stocke des informations sur les films et les émissions de télévision. La plupart des propriétés est nécessaire, et plusieurs propriétés de chaîne ont des exigences de longueur. En outre, il n’est une restriction de plage numérique en place pour le `Price` propriété à partir de 0 à $999,99, ainsi que d’un attribut de validation personnalisé.

[!code-csharp[Main](validation/sample/Movie.cs?range=6-31)]

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

MVC prend en charge tout attribut qui dérive de `ValidationAttribute` à des fins de validation. Vous trouverez d’attributs de validation utile dans les [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations(v=vs.110).aspx) espace de noms.

Il peut y avoir des instances où vous avez besoin de davantage de fonctionnalités pour fournissent des attributs prédéfinis. Dans ces cas, vous pouvez créer des attributs de validation personnalisé en dérivant de `ValidationAttribute` ou la modification de votre modèle à mettre en œuvre `IValidatableObject`.

## <a name="model-state"></a>État du modèle

État du modèle représente des erreurs de validation dans les valeurs de formulaire HTML envoyés.

MVC continue validation des champs jusqu'à ce qu’atteigne le nombre maximal d’erreurs (200 par défaut). Vous pouvez configurer ce nombre en insérant le code suivant dans le `ConfigureServices` méthode dans le *Startup.cs* fichier :

[!code-csharp[Main](validation/sample/Startup.cs?range=27)]

## <a name="handling-model-state-errors"></a>État de modèle de gestion des erreurs

Validation du modèle se produit avant chaque action du contrôleur qui est appelée, et il incombe de la méthode d’action à inspecter `ModelState.IsValid` et réagir de façon appropriée. Dans de nombreux cas, la réaction appropriée doit retourner un type de réponse d’erreur, dans l’idéal, détaillant la raison de l’échec de validation du modèle.

Certaines applications choisit de suivre une convention standard pour traiter les erreurs de validation de modèle, auquel cas un filtre peut être un emplacement approprié pour implémenter une telle stratégie. Vous devez tester le comportement de vos actions avec les États de modèles valides et non valides.

## <a name="manual-validation"></a>Validation manuelle

Une fois la validation et la liaison de modèle, vous souhaiterez répéter des parties de celui-ci. Par exemple, un utilisateur peut-être entré du texte dans un champ attendu d’un entier, ou vous devrez peut-être calculer une valeur pour les propriétés d’un modèle.

Vous devrez peut-être exécuter manuellement la validation. Pour ce faire, appelez le `TryValidateModel` méthode, comme illustré ici :

[!code-csharp[Main](validation/sample/MoviesController.cs?range=52)]

## <a name="custom-validation"></a>Validation personnalisée

Attributs de validation fonctionnent pour la plupart des besoins de la validation. Toutefois, des règles de validation sont spécifiques à votre entreprise, qu’elles ne sont pas simplement à la validation de données génériques tels que la vérification d’un champ est requise ou qu’il est conforme à une plage de valeurs. Pour ces scénarios, les attributs de validation personnalisés sont une excellente solution. Il est facile de créer vos propres attributs de validation personnalisée dans MVC. Uniquement hériter de la `ValidationAttribute`et remplacez le `IsValid` (méthode). Le `IsValid` méthode accepte deux paramètres, le premier est un objet nommé *valeur* et le second est un `ValidationContext` objet nommé *validationContext*. *Valeur* fait référence à la valeur réelle du champ de la validation de votre validateur personnalisé.

Dans l’exemple suivant, une règle d’entreprise stipule que les utilisateurs ne peuvent pas définir le genre *classique* pour une vidéo publiée après le 1960. Le `[ClassicMovie]` attribut vérifie d’abord le genre et s’il s’agit d’un standard, il vérifie ensuite la date de publication pour qu’il s’agit 1960 plus tard. Si elle est libérée après 1960, la validation échoue. L’attribut accepte un paramètre entier qui représente l’année que vous pouvez utiliser pour valider les données. Vous pouvez capturer la valeur du paramètre dans le constructeur de l’attribut, comme illustré ici :

[!code-csharp[Main](validation/sample/ClassicMovieAttribute.cs?range=9-28)]

Le `movie` variable ci-dessus représente un `Movie` objet qui contient les données à partir de l’envoi du formulaire à valider. Dans ce cas, le code de validation vérifie la date et le genre dans le `IsValid` méthode de la `ClassicMovieAttribute` classe conformément aux règles. Si la validation réussit `IsValid` retourne un `ValidationResult.Success` code, et lorsque la validation échoue, un `ValidationResult` avec un message d’erreur. Quand un utilisateur modifie le `Genre` champ et envoie le formulaire, le `IsValid` méthode de le `ClassicMovieAttribute` doit vérifier si la séquence est un standard. Comme n’importe quel attribut intégré, vous devez appliquer le `ClassicMovieAttribute` à une propriété telle que `ReleaseDate` pour garantir la validation se produit, comme indiqué dans l’exemple de code précédent. Étant donné que l’exemple fonctionne uniquement avec `Movie` types, une meilleure option consiste à utiliser `IValidatableObject` comme indiqué dans le paragraphe suivant.

Ou bien, ce code même pu être placé dans le modèle en implémentant la `Validate` méthode sur le `IValidatableObject` interface. Tandis que les attributs de validation personnalisés fonctionnent bien pour la validation des propriétés individuelles, implémentation `IValidatableObject` peut être utilisé pour implémenter la validation au niveau de la classe comme illustré ci-après.

[!code-csharp[Main](validation/sample/MovieIValidatable.cs?range=33-41)]

## <a name="client-side-validation"></a>Validation côté client

Validation côté client est très pratique pour les utilisateurs. Il enregistre à temps serait sinon attend un aller-retour sur le serveur. En termes de l’entreprise, même de quelques fractions de secondes multipliés des centaines de fois par jour ajoute jusqu'à beaucoup de temps, des frais et frustration. Validation simple et immédiate permet aux utilisateurs de travailler plus efficacement et de produire une meilleure qualité d’entrée et sortie.

Vous devez disposer d’une vue avec les références de script JavaScript appropriées en place pour la validation côté client fonctionnent comme vous le voyez ici.

[!code-html[Main](validation/sample/Views/Shared/_Layout.cshtml?range=37)]

[!code-html[Main](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml)]

MVC utilise les attributs de validation en plus des métadonnées de type à partir des propriétés de modèle à valider les données et afficher les messages d’erreur à l’aide de JavaScript. Lorsque vous utilisez MVC pour restituer les éléments de formulaire à partir d’un modèle à l’aide de [programmes d’assistance de balise](xref:mvc/views/tag-helpers/intro) ou [programmes d’assistance HTML](xref:mvc/views/overview) ajoutera HTML 5 [attributs de données](http://w3c.github.io/html/dom.html#embedding-custom-non-visible-data-with-the-data-attributes) dans les éléments de formulaire nécessitant une validation, en tant que indiqué ci-dessous. MVC génère le `data-` attributs pour les attributs intégrés et personnalisés. Vous pouvez afficher les erreurs de validation sur le client en utilisant les programmes d’assistance de balise appropriés comme indiqué ici :

[!code-html[Main](validation/sample/Views/Movies/Create.cshtml?highlight=4,5&range=19-25)]

Les programmes d’assistance de balise ci-dessus de rendu HTML ci-dessous. Notez que la `data-` attributs dans le code HTML de sortie correspondent aux attributs de validation pour le `ReleaseDate` propriété. Le `data-val-required` attribut ci-dessous contient un message d’erreur à afficher si ne remplit pas l’utilisateur dans le champ date de mise en production, et ce message s’affiche dans l’accompagnant `<span>` élément.

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

Vous pouvez implémenter la validation à distance dans un processus en deux étapes. Tout d’abord, vous devez annoter votre modèle avec la `[Remote]` attribut. Le `[Remote]` attribut accepte plusieurs surcharges qui vous permet de diriger le côté client JavaScript pour le code approprié à appeler. L’exemple pointe vers le `VerifyEmail` méthode d’action de la `Users` contrôleur.

[!code-csharp[Main](validation/sample/User.cs?range=5-9)]

La deuxième étape pour placer le code de validation dans la méthode d’action correspondante tel que défini dans le `[Remote]` attribut. Elle retourne un `JsonResult` que du côté client peut utiliser pour continuer ou interrompre et affiche une erreur si nécessaire.

[!code-none[Main](validation/sample/UsersController.cs?range=19-28)]

Maintenant lorsque les utilisateurs entrent un message électronique, JavaScript dans la vue effectue un appel à distance pour voir si cet e-mail a été pris et si tel est le cas, puis affiche le message d’erreur. Dans le cas contraire, l’utilisateur peut soumettre le formulaire comme d’habitude.
