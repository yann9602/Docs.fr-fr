---
title: "Liaison de modèle"
author: rick-anderson
description: 
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/model-binding
ms.openlocfilehash: 84b9c5dc3a87b739affaeaecaa180d1b01f49b8e
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="model-binding"></a>Liaison de modèle

Par [Rachel Appel](https://github.com/rachelappel)

## <a name="introduction-to-model-binding"></a>Introduction à la liaison de modèle

Liaison de modèle dans ASP.NET MVC de base mappe les données de requêtes HTTP aux paramètres de méthode d’action. Les paramètres peuvent être des types simples tels que des chaînes, des entiers ou des nombres à virgule flottante, ou ils peuvent être des types complexes. Il s’agit d’une fonctionnalité intéressante de MVC, car le mappage des données entrantes et son homologue est un scénario souvent répété, quelle que soit la taille ou la complexité des données. MVC résout ce problème en faisant abstraction liaison afin que les développeurs ne doivent conserver réécriture d’une version légèrement différente de ce même code dans chaque application. L’écriture de votre propre texte pour le code de convertisseur de type est fastidieux et sujet aux erreurs.

## <a name="how-model-binding-works"></a>Fonctionne de la liaison de modèle

Lorsque MVC reçoit une requête HTTP, il achemine vers une méthode d’action spécifique d’un contrôleur. Il détermine la méthode d’action à exécuter en fonction de ce qui est dans les données d’itinéraire, puis elle lie les valeurs de la requête HTTP pour les paramètres de cette méthode d’action. Par exemple, considérez l’URL suivante :

`http://contoso.com/movies/edit/2`

Étant donné que le modèle d’itinéraire ressemble à ceci, `{controller=Home}/{action=Index}/{id?}`, `movies/edit/2` achemine vers le `Movies` contrôleur et son `Edit` méthode d’action. Elle accepte également un paramètre facultatif, appelé `id`. Le code de la méthode d’action doit ressembler à ceci :

```csharp
public IActionResult Edit(int? id)
   ```

Remarque : Les chaînes dans l’itinéraire d’URL ne sont pas respecter la casse.

MVC tente de se lier des données de la demande pour les paramètres d’action par nom. MVC recherchera des valeurs pour chaque paramètre à l’aide du nom du paramètre et les noms de ses propriétés définissables publiques. Dans l’exemple ci-dessus, le paramètre de la seule action est nommé `id`, lequel MVC lie à la valeur portant le même nom dans les valeurs d’itinéraire. En plus des valeurs d’itinéraire MVC lier des données à partir de différentes parties de la demande et il le fait dans un ordre bien défini. Voici une liste des sources de données dans l’ordre de liaison de modèle recherche dans les :

1. `Form values`: Ce sont des valeurs de formulaire qui vont dans la requête HTTP à l’aide de la méthode POST. (y compris les requêtes POST jQuery).

2. `Route values`: Le jeu de valeurs d’itinéraire fourni par [routage](../../fundamentals/routing.md)

3. `Query strings`: La partie de chaîne de requête de l’URI.

<!-- DocFX BUG
The link works but generates an error when building with DocFX
@fundamentals/routing
[Routing](xref:fundamentals/routing)
-->

Remarque : Écran de valeurs, les données d’itinéraire et toutes les chaînes sont stockées en tant que paires nom-valeur de requête.

Étant donné que la liaison de modèle invité à entrer une clé nommée `id` et aucun élément nommé `id` dans les valeurs de formulaire, il déplacées sur les valeurs d’itinéraire recherchez cette clé. Dans notre exemple, il est une correspondance. Liaison se produit, et la valeur est convertie à l’entier 2. La même demande à l’aide de l’édition (id de chaîne) permet de convertir l’à la chaîne « 2 ».

Jusqu'à présent, l’exemple utilise des types simples. Dans MVC types simples sont tout type de primitive .NET ou avec un convertisseur de type chaîne. Si le paramètre de la méthode d’action agissait une classe comme le `Movie` type, qui contient des types simples et complexes tels que les propriétés, modèle liaison continueront de MVC gérer correctement. Il utilise la réflexion et récurrence pour parcourir les propriétés de recherche de correspondances de types complexes. Liaison de modèle recherche le modèle *parameter_name.property_name* pour lier des valeurs aux propriétés. S’il ne trouve pas les valeurs correspondantes de ce formulaire, il va tenter de lier à l’aide de simplement le nom de propriété. Pour ces types, tels que `Collection` , types de liaison de modèle de recherche des correspondances à *nom_paramètre [index]* ou simplement *[index]*. Traite de la liaison de modèle `Dictionary` les types de la même façon, lui demandant de *nom_paramètre [clé]* ou simplement *[clé]*, à condition que les clés sont des types simples. Clés qui sont prises en charge correspondent aux noms de champ HTML et programmes d’assistance de balise générés pour le même type de modèle. Ainsi, les valeurs aller-retour afin que les champs de formulaire restent remplis avec l’entrée d’utilisateur pour leur faciliter la tâche, par exemple, lorsque les données liées à partir d’une création ou la modification n’a pas réussi la validation.

Dans l’ordre de liaison doit se produire la classe doit avoir un constructeur public par défaut et les membres d’être lié doivent être publiques propriétés accessibles en écriture. Lors de la liaison de modèle se produit, que la classe sera uniquement être instanciée en utilisant le constructeur public par défaut, les propriétés peuvent être définies.

Lorsqu’un paramètre est lié, liaison de modèle arrête la recherche de valeurs portant ce nom et elle passe à lier le paramètre suivant. Sinon, le comportement de liaison de modèle par défaut définit les paramètres à leurs valeurs par défaut en fonction de leur type :

* `T[]`: À l’exception des tableaux de type `byte[]`, liaison définit les paramètres de type `T[]` à `Array.Empty<T>()`. Les tableaux de type `byte[]` ont la valeur `null`.

* Types de référence : Liaison crée une instance d’une classe avec le constructeur par défaut sans définir ses propriétés. Toutefois, les jeux de liaison de modèle `string` paramètres `null`.

* Types Nullable : Les types Nullable sont définies sur `null`. Dans l’exemple ci-dessus, les jeux de liaison de modèle `id` à `null` car il est de type `int?`.

* : Les Types de valeur les types valeur Non nullable de type `T` ont la valeur `default(T)`. Par exemple, liaison de modèle définit un paramètre `int id` à 0. Pensez à l’aide de la validation des modèles ou des types nullable au lieu de s’appuyer sur les valeurs par défaut.

Si la liaison échoue, MVC ne lève pas une erreur. Chaque action qui accepte une entrée d’utilisateur doit vérifier le `ModelState.IsValid` propriété.

Remarque : Chaque entrée dans le contrôleur de `ModelState` propriété est un `ModelStateEntry` contenant un `Errors` propriété. Il est rarement nécessaire interroger cette collection vous-même. Utilisez plutôt `ModelState.IsValid`.

En outre, il existe de certains types de données spéciaux que MVC doit prendre en compte lors de la liaison de modèle :

* `IFormFile`, `IEnumerable<IFormFile>`: Un ou plusieurs fichiers téléchargés qui font partie de la requête HTTP.

* `CancellationToken`: Utilisé pour annuler l’activité dans les contrôleurs asynchrones.

Ces types peuvent être liés aux paramètres d’action ou à des propriétés sur un type de classe.

Une fois que la liaison de modèle est terminée, [Validation](validation.md) se produit. Liaison de modèle par défaut fonctionne bien pour la majorité des scénarios de développement. Il est également extensible donc si vous avez des besoins spécifiques, vous pouvez personnaliser le comportement intégré.

## <a name="customize-model-binding-behavior-with-attributes"></a>Personnaliser le comportement de liaison de modèle avec des attributs

MVC contient plusieurs attributs que vous pouvez utiliser pour indiquer son comportement de liaison de modèle par défaut à une source différente. Par exemple, vous pouvez spécifier si la liaison est requise pour une propriété, ou si elle doit jamais se produire à tout à l’aide de la `[BindRequired]` ou `[BindNever]` attributs. Vous pouvez également remplacer la source de données par défaut et spécifier la source de données du classeur de modèles. Vous trouverez ci-dessous la liste des attributs de liaison de modèle :

* `[BindRequired]`: Cet attribut ajoute une erreur d’état de modèle si la liaison ne peut pas se produire.

* `[BindNever]`: Indique le classeur de modèles jamais lier à ce paramètre.

* `[FromHeader]`, `[FromQuery]`, `[FromRoute]`, `[FromForm]`: Les utiliser pour spécifier la source de liaison exacte que vous souhaitez appliquer.

* `[FromServices]`: Cet attribut utilise [injection de dépendance](../../fundamentals/dependency-injection.md) pour lier les paramètres à partir des services.

* `[FromBody]`: Utilisez les formateurs configurés pour lier des données à partir du corps de la demande. Le module de formatage est sélectionné selon le type de contenu de la demande.

* `[ModelBinder]`: Utilisé pour remplacer le classeur de modèles par défaut, la source de liaison et le nom.

Les attributs sont très utiles lorsque vous devez remplacer le comportement par défaut de la liaison de modèle.

## <a name="binding-formatted-data-from-the-request-body"></a>Liaison de mise en forme des données à partir du corps de demande

Dans divers formats, notamment JSON, XML et bien d’autres peuvent provenir de données de la demande. Lorsque vous utilisez l’attribut [FromBody] pour indiquer que vous souhaitez lier un paramètre à des données dans le corps de la demande, MVC utilise un jeu de formateurs configuré pour gérer les données de la demande en fonction de son type de contenu. Par défaut, MVC inclut un `JsonInputFormatter` de classe pour la gestion des données JSON, mais vous peuvent ajouter des formateurs supplémentaires pour la gestion de XML et autres formats personnalisés.

> [!NOTE]
> Il peut y avoir au plus un paramètre par action décorée avec `[FromBody]`. La durée d’exécution ASP.NET MVC de base délègue la responsabilité de lire le flux de demande au formateur. Une fois que le flux de demande est en lecture pour un paramètre, il n’est généralement pas possible de lire le flux de demande de liaison des autres `[FromBody]` paramètres.

> [!NOTE]
> Le `JsonInputFormatter` est le formateur par défaut et est basée sur [Json.NET](https://www.newtonsoft.com/json).

ASP.NET sélectionne les formateurs d’entrée selon la [Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html) en-tête et le type du paramètre, sauf s’il existe un attribut appliqué en spécifiant dans le cas contraire. Si vous souhaitez utiliser des données XML ou un autre format vous devez le configurer dans le *Startup.cs* fichier, mais vous devrez peut-être d’abord avez obtenir une référence à `Microsoft.AspNetCore.Mvc.Formatters.Xml` à l’aide de NuGet. Votre code de démarrage doit ressembler à ceci :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddXmlSerializerFormatters();
   }
```

Le code dans le *Startup.cs* fichier contient un `ConfigureServices` méthode avec un `services` argument, vous pouvez utiliser pour générer des services pour votre application ASP.NET. Dans l’exemple, nous ajoutons un formateur XML en tant que service MVC fournit pour cette application. Le `options` argument passé dans le `AddMvc` méthode permet d’ajouter et gérer des filtres, les formateurs et les autres options de système de MVC lors du démarrage de l’application. Puis appliquez le `Consumes` d’attributs pour les classes de contrôleur ou de méthodes d’action pour travailler avec le format souhaité.

### <a name="custom-model-binding"></a>Liaison de modèle personnalisé

Vous pouvez étendre la liaison de modèle en écrivant vos propres classeurs de modèles personnalisés. En savoir plus sur [liaison de modèle personnalisé](../advanced/custom-model-binding.md).
