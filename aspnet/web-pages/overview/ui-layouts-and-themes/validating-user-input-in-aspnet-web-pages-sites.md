---
uid: web-pages/overview/ui-layouts-and-themes/validating-user-input-in-aspnet-web-pages-sites
title: "Validation des entrées d’utilisateur dans ASP.NET Web Pages (Razor) Sites | Documents Microsoft"
author: tfitzmac
description: "Cet article explique comment valider des informations provenant d’utilisateurs &mdash; , assurez-vous que les utilisateurs entrent valide les informations au format HTML forms dans un sous..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2014
ms.topic: article
ms.assetid: 4eb060cc-cf14-41ae-bab1-14a2c15332d0
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/validating-user-input-in-aspnet-web-pages-sites
msc.type: authoredcontent
ms.openlocfilehash: 3bde2a4ea69577ebcbe3e9e89a7ee07e6ece8dd1
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="validating-user-input-in-aspnet-web-pages-razor-sites"></a>Validation des entrées d’utilisateur dans les Sites ASP.NET Web Pages (Razor)
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article explique comment valider des informations provenant d’utilisateurs &mdash; , assurez-vous que les utilisateurs entrent valide les informations au format HTML forms dans un site ASP.NET Web Pages (Razor).
> 
> Ce que vous allez apprendre :
> 
> - Comment vérifier qu’un entrée de l’utilisateur correspond aux critères de validation que vous définissez.
> - Comment déterminer si tous les tests de validation ont réussi.
> - Comment afficher les erreurs de validation (et comment les mettre en forme).
> - Comment valider les données qui ne provient pas directement à partir des utilisateurs.
> 
> Il s’agit de programmation les concepts présentés dans l’article ASP.NET :
> 
> - Le `Validation` helper.
> - Le `Html.ValidationSummary` et `Html.ValidationMessage` méthodes.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 3
>   
> 
> Ce didacticiel fonctionne également avec ASP.NET Web Pages 2.


Cet article contient les sections suivantes :

- [Vue d’ensemble de la Validation des entrées d’utilisateur](#Overview_of_User_Input_Validation)
- [Validation des entrées utilisateur](#Validating_User_Input)
- [Ajout d’une Validation côté Client](#Adding_Client-Side_Validation)
- [Mise en forme des erreurs de Validation](#Formatting_Validation_Errors)
- [Validation des données qui ne provient pas directement à partir d’utilisateurs](#Validating_Data_That_Doesnt_Come_Directly_from_Users)

<a id="Overview_of_User_Input_Validation"></a>
## <a name="overview-of-user-input-validation"></a>Vue d’ensemble de la Validation des entrées d’utilisateur

Si vous demandez aux utilisateurs d’entrer des informations dans une page, par exemple, dans un formulaire, il est important de s’assurer que les valeurs qu’elles saisir sont valides. Par exemple, vous ne souhaitez pas traiter un formulaire auquel il manque des informations critiques.

Lorsque les utilisateurs entrent des valeurs dans un formulaire HTML, les valeurs qu’elles saisir sont des chaînes. Dans de nombreux cas, les valeurs que vous avez besoin sont certains autres types de données, tels que des entiers ou des dates. Par conséquent, vous devez également vous assurer que les valeurs que les utilisateurs entrent peuvent être convertis correctement aux types de données approprié.

Vous pouvez également avoir certaines restrictions sur les valeurs. Même si les utilisateurs entrent correctement un entier, par exemple, vous devrez peut-être vous assurer que la valeur est comprise dans une plage donnée.

![Erreurs de validation qui utilisent des classes de style CSS](validating-user-input-in-aspnet-web-pages-sites/_static/image1.png)

> [!NOTE] 
> 
> **Important** valider l’entrée d’utilisateur est également importante pour la sécurité. Lorsque vous limitez les valeurs que les utilisateurs peuvent entrer dans les formulaires, vous réduisez le risque que quelqu'un peut entrer une valeur qui peut compromettre la sécurité de votre site.


<a id="Validating_User_Input"></a>
## <a name="validating-user-input"></a>Validation des entrées utilisateur

Dans ASP.NET Web Pages 2, vous pouvez utiliser le `Validator` application d’assistance pour tester l’entrée d’utilisateur. L’approche de base consiste à effectuer les opérations suivantes :

1. Déterminer quels d’entrée des éléments (champs) que vous souhaitez valider.

    En règle générale, vous validez des valeurs dans `<input>` éléments dans un formulaire. Toutefois, il est conseillé de valider toutes les entrées, de même d’entrée qui provient d’un élément de contraint comme un `<select>` liste. Cela permet de s’assurer que les utilisateurs ne pas ignorer les contrôles sur une page et envoyer un formulaire.
2. Dans le code de la page, ajouter des contrôles de validation individuels pour chaque élément d’entrée à l’aide des méthodes de la `Validation` helper.

    Pour vérifier les champs obligatoires, utilisez `Validation.RequireField(field, [error message])` (pour un champ individuel) ou `Validation.RequireFields(field1, field2, ...))` (pour une liste de champs). Pour les autres types de validation, utilisez `Validation.Add(field, ValidationType)`. Pour `ValidationType`, vous pouvez utiliser ces options :

    `Validator.DateTime ([error message])`  
`Validator.Decimal([error message])`  
`Validator.EqualsTo(otherField [, error message])`  
`Validator.Float([error message])`  
`Validator.Integer([error message])`  
`Validator.Range(min, max [, error message])`  
`Validator.RegEx(pattern [, error message])`  
`Validator.Required([error message])`  
`Validator.StringLength(length)`  
`Validator.Url([error message])`
3. Lorsque la page est envoyée, vérifiez si la validation a réussi en vérifiant `Validation.IsValid`:

    [!code-csharp[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample1.cs)]

    S’il existe des erreurs de validation, vous ignorez le traitement d’une page normale. Par exemple, si l’objectif de la page est mise à jour d’une base de données, vous ne le faire jusqu'à ce que toutes les erreurs de validation ont été résolus.
4. S’il existe des erreurs de validation, afficher des messages d’erreur dans le balisage de la page à l’aide de `Html.ValidationSummary` ou `Html.ValidationMessage`, ou les deux.

L’exemple suivant montre une page qui illustre ces étapes.

[!code-cshtml[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample2.cshtml)]

Pour vérifier le fonctionne de la validation, exécutez cette page et faites délibérément des erreurs. Par exemple, voici ce que la page ressemble si vous oubliez d’entrer un nom de cours, si vous entrez un, et si vous entrez une date non valide :

![Erreurs de validation dans la page rendue](validating-user-input-in-aspnet-web-pages-sites/_static/image2.png)

<a id="Adding_Client-Side_Validation"></a>
## <a name="adding-client-side-validation"></a>Ajout d’une Validation côté Client

Par défaut, l’entrée d’utilisateur est validée après envoi de la page, autrement dit, la validation est effectuée dans le code serveur. L’inconvénient de cette approche est que les utilisateurs ne savent qu’ils avez apporté une erreur tant qu’après leur envoyer la page. Si un formulaire est longue ou complexe, peut être gênant à l’utilisateur de rapports d’erreurs uniquement après que la page est envoyée.

Vous pouvez ajouter la prise en charge pour effectuer une validation dans le script client. Dans ce cas, la validation est effectuée dans le navigateur. Par exemple, supposons que vous spécifiez qu’une valeur doit être un entier. Si un utilisateur entre une valeur non entière, l’erreur est signalée dès que l’utilisateur quitte le champ d’entrée. Les utilisateurs obtiennent rétroaction immédiate, qui est très utile pour eux. Validation basée sur le client peut également réduire le nombre de fois que l’utilisateur doit envoyer le formulaire pour corriger plusieurs erreurs.

> [!NOTE]
> Même si vous utilisez la validation côté client, est également toujours de validation dans le code serveur. Exécution de la validation dans le code serveur est une mesure de sécurité, les utilisateurs contournent la validation basée sur le client.


1. Inscrire les bibliothèques JavaScript suivants dans la page :  

    [!code-html[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample3.html)]

 Deux des bibliothèques sont chargés à partir d’un réseau de diffusion de contenu (CDN), afin que vous ne sont pas nécessairement à les installer sur votre ordinateur ou serveur. Toutefois, vous devez disposer d’une copie locale de *jquery.validate.unobtrusive.js*. Si vous n’utilisez pas déjà avec un modèle de WebMatrix (comme **Starter Site** ) qui inclut la bibliothèque, créez un site de Pages Web qui est basé sur **Starter Site**. Copiez le *.js* fichier vers votre site actuel.
2. Dans le balisage, pour chaque élément que vous êtes la validation, ajoutez un appel à `Validation.For(field)`. Cette méthode émet des attributs qui sont utilisés par la validation côté client. (Au lieu de l’émission de code JavaScript réel, la méthode émet des attributs tels que `data-val-...`. Ces attributs prennent en charge la validation client non obstructive qui utilise jQuery pour effectuer le travail.)

La page suivante montre comment ajouter des fonctionnalités de validation client pour l’exemple illustré précédemment.

[!code-cshtml[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample4.cshtml?highlight=35-39,51,61,71)]

Pas de tous les contrôles de validation exécutées sur le client. En particulier, la validation de type de données (entier, date, etc.) ne s’exécutent sur le client. Les vérifications suivantes fonctionnent sur le client et le serveur :

- `Required`
- `Range(minValue, maxValue)`
- `StringLength(maxLength[, minLength])`
- `Regex(pattern)`
- `EqualsTo(otherField)`

Dans cet exemple, le test d’une date valide ne fonctionne pas dans le code client. Toutefois, le test sera exécuté dans le code serveur.

<a id="Formatting_Validation_Errors"></a>
## <a name="formatting-validation-errors"></a>Mise en forme des erreurs de Validation

Vous pouvez contrôler l’affichent des erreurs de validation en définissant les classes CSS qui ont des noms réservés suivants :

- `field-validation-error`. Définit la sortie de la `Html.ValidationMessage` méthode lorsqu’il affiche une erreur.
- `field-validation-valid`. Définit la sortie de la `Html.ValidationMessage` méthode lorsqu’il n’existe aucune erreur.
- `input-validation-error`. Définit comment `<input>` les éléments sont rendus lorsqu’il existe une erreur. (Par exemple, vous pouvez utiliser cette classe pour définir la couleur d’arrière-plan d’un &lt;d’entrée&gt; élément sur une couleur différente si sa valeur n’est pas valide.) Cette classe CSS est utilisée uniquement lors de la validation de client (dans ASP.NET Web Pages 2).
- `input-validation-valid`. Définit l’apparence des `<input>` éléments quand il n’existe aucune erreur.
- `validation-summary-errors`. Définit la sortie de la `Html.ValidationSummary` méthode qu’elle affiche une liste d’erreurs.
- `validation-summary-valid`. Définit la sortie de la `Html.ValidationSummary` méthode lorsqu’il n’existe aucune erreur.

Les éléments suivants `<style>` illustre les règles de conditions d’erreur.

[!code-css[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample5.css)]

Si vous incluez ce bloc de style dans l’exemple des pages plus haut dans l’article, l’affichage des erreurs doit ressembler à l’illustration suivante :

![Erreurs de validation qui utilisent des classes de style CSS](validating-user-input-in-aspnet-web-pages-sites/_static/image3.png)

> [!NOTE]
> Si vous n’utilisez pas la validation client dans ASP.NET Web Pages 2, les classes CSS pour les `<input>` éléments (`input-validation-error` et `input-validation-valid` n’ont aucun effet.


### <a name="static-and-dynamic-error-display"></a>Affichage des erreurs statiques et dynamiques

Les règles CSS sont fournis par paires, tels que `validation-summary-errors` et `validation-summary-valid`. Ces paires vous permettent de définir des règles pour les deux conditions : une condition d’erreur et une condition (sans erreur) « normale ». Il est important de comprendre que le balisage de l’affichage des erreurs est toujours rendu, même si aucun message d’erreur. Par exemple, si une page comporte un `Html.ValidationSummary` méthode dans le balisage, la page source contient le balisage suivant même lorsque la page est demandée pour la première fois :

`<div class="validation-summary-valid" data-valmsg-summary="true"><ul></ul></div>`

En d’autres termes, le `Html.ValidationSummary` méthode restitue toujours un `<div>` élément et une liste, même si la liste d’erreurs est vide. De même, la `Html.ValidationMessage` méthode restitue toujours un `<span>` élément comme espace réservé pour une erreur de champ individuel, même s’il n’existe aucune erreur.

Dans certaines situations, affiche un message d’erreur peuvent entraîner la page à ajuster dynamiquement et peut entraîner des éléments sur la page pour vous déplacer. Les règles CSS qui se terminent par `-valid` vous permettent de définir une disposition qui peut aider à éviter ce problème. Par exemple, vous pouvez définir `field-validation-error` et `field-validation-valid` pour les deux ont la même taille fixe. De cette façon, la zone d’affichage pour le champ est statique et ne change pas le flux de page si un message d’erreur s’affiche.

<a id="Validating_Data_That_Doesnt_Come_Directly_from_Users"></a>
## <a name="validating-data-that-doesnt-come-directly-from-users"></a>Validation des données qui ne provient pas directement à partir d’utilisateurs

Parfois, vous devez valider les informations qui ne provient pas directement à partir d’un formulaire HTML. Un exemple typique d’une page dans laquelle une valeur est passée dans une chaîne de requête, comme dans l’exemple suivant :

`http://server/myapp/EditClassInformation?classid=1022`

Dans ce cas, vous souhaitez vous assurer que la valeur est passée à la page (ici, 1022 pour la valeur de `classid`) est valide. Vous ne pouvez pas utiliser directement le `Validation` application d’assistance pour effectuer cette validation. Toutefois, vous pouvez utiliser d’autres fonctionnalités du système de contrôle, comme la possibilité d’afficher les messages d’erreur de validation.

> [!NOTE] 
> 
> **Important** toujours valider les valeurs que vous obtenez à partir de *tout* source, y compris les valeurs de champ de formulaire, les valeurs de chaîne de requête et valeurs de cookie. Il est facile pour les utilisateurs à modifier ces valeurs (par exemple dans un but malveillant). Par conséquent, vous devez vérifier ces valeurs afin de protéger votre application.


L’exemple suivant montre comment vous pouvez valider une valeur qui est passée dans une chaîne de requête. Le code vérifie que la valeur n’est pas vide et qu’il s’agit d’un entier.

[!code-csharp[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample6.cs)]

Notez que le test est effectué lorsque la demande n’est pas un envoi de formulaire (`if(!IsPost)`). Ce test peut passer à la première fois que la page est demandée, mais pas lorsque la demande est un envoi de formulaire.

Pour afficher cette erreur, vous pouvez ajouter l’erreur à la liste des erreurs de validation en appelant `Validation.AddFormError("message")`. Si la page contient un appel à la `Html.ValidationSummary` (méthode), l’erreur s’affiche, comme une erreur de validation de l’entrée d’utilisateur.

<a id="AdditionalResources"></a>
## <a name="additional-resources"></a>Ressources supplémentaires

[Utilisation des formulaires HTML dans les Sites ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkID=202892)
