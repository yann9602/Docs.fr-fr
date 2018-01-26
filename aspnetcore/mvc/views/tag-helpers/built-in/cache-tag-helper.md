---
title: "Mettre en cache d’assistance de balise dans le cœur d’ASP.NET MVC"
author: pkellner
description: "Montre comment travailler avec l’application d’assistance de balise de Cache"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: 10aa1b493dbd0672cac789f6e48ddf2f14ba35dc
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a>Mettre en cache d’assistance de balise dans le cœur d’ASP.NET MVC

Par [Peter Kellner](http://peterkellner.net) 

L’application d’assistance de balise de Cache permet d’améliorer considérablement les performances de votre application ASP.NET Core par son contenu pour le fournisseur de cache ASP.NET Core interne de la mise en cache.

Le moteur d’affichage Razor définit la valeur par défaut `expires-after` vingt minutes.

Le balisage de Razor suivant met en cache la date/heure :

```cshtml
<cache>@DateTime.Now</cache>
```

La première demande vers la page qui contient `CacheTagHelper` affiche la date/heure actuelle. Demandes supplémentaires affichera la valeur mise en cache jusqu'à ce que le cache expire (par défaut, 20 minutes) ou est supprimé par sollicitation de la mémoire.

Vous pouvez définir la durée du cache avec les attributs suivants :

## <a name="cache-tag-helper-attributes"></a>Mettre en cache les attributs d’assistance de balise

- - -

### <a name="enabled"></a>enabled    


| Type d’attribut    | Valeurs valides      |
|----------------   |----------------   |
| boolean           | « true » (par défaut)  |
|                   | "false"   |


Détermine si le contenu du programme d’assistance de balise de Cache est mis en cache. La valeur par défaut est `true`.  Si la valeur `false` ce programme d’assistance de balise de Cache n’a aucun effet mise en cache sur la sortie rendue.

Exemple :

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="expires-on"></a>expires-on 

| Type d’attribut    | Exemple de valeur     |
|----------------   |----------------   |
| DateTimeOffset    | «@new DateTime(2025,1,29,17,02,0) »    |


Définit une date d’expiration absolue. L’exemple suivant met en cache le contenu de l’application d’assistance de balise de Cache jusqu'à 5 h 02 le 29 janvier 2025.

Exemple :

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="expires-after"></a>expire après

| Type d’attribut    | Exemple de valeur     |
|----------------   |----------------   |
| TimeSpan    | "@TimeSpan.FromSeconds(120)"    |


Définit la durée à partir de la première demande pour mettre en cache le contenu. 

Exemple :

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="expires-sliding"></a>expiration de retrait

| Type d’attribut    | Exemple de valeur     |
|----------------   |----------------   |
| TimeSpan    | "@TimeSpan.FromSeconds(60)"     |


Définit l’heure à laquelle une entrée de cache doit être supprimée si elle n’a pas été accédé.

Exemple :

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="vary-by-header"></a>varient en fonction de l’en-tête

| Type d’attribut    | Exemples de valeurs                |
|----------------   |----------------               |
| Chaîne            | « User-Agent »                  |
|                   | « User-Agent, content-encoding » |

Accepte une valeur d’en-tête unique ou une liste séparée par des virgules de valeurs d’en-tête qui déclenchent une actualisation du cache quand ils changent. L’exemple suivant analyse la valeur d’en-tête `User-Agent`. L’exemple mettra en cache le contenu de chaque autre `User-Agent` présenté au serveur web.

Exemple :

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="vary-by-query"></a>varient par requête

| Type d’attribut    | Exemples de valeurs                |
|----------------   |----------------               |
| Chaîne            | « Vérifiez »                |
|                   | « Assurez-vous, modèle » |

Accepte une valeur d’en-tête unique ou une liste séparée par des virgules de valeurs d’en-tête qui déclenchent une actualisation du cache lorsque la valeur d’en-tête. L’exemple suivant présente les valeurs de `Make` et `Model`.

Exemple :

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="vary-by-route"></a>varient par itinéraire

| Type d’attribut    | Exemples de valeurs                |
|----------------   |----------------               |
| Chaîne            | « Vérifiez »                |
|                   | « Assurez-vous, modèle » |

Accepte une valeur d’en-tête unique ou une liste séparée par des virgules de valeurs d’en-tête qui déclenchent une actualisation du cache lorsque le paramètre de données d’itinéraire spécifiées à modifier. Exemple :

*Startup.cs* 

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```
  
*Index.cshtml*

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="vary-by-cookie"></a>varient par cookie

| Type d’attribut    | Exemples de valeurs                |
|----------------   |----------------               |
| Chaîne            | ".AspNetCore.Identity.Application"                |
|                   | ".AspNetCore.Identity.Application,HairColor" |

Accepte une valeur d’en-tête unique ou une liste séparée par des virgules de valeurs d’en-tête qui déclenchent une actualisation du cache lorsque les valeurs d’en-tête changement de (s). L’exemple suivant présente le cookie associé d’identité ASP.NET. Lorsqu’un utilisateur est authentifié le cookie de la demande à définir qui déclenche une actualisation du cache.

Exemple :

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="vary-by-user"></a>varient par utilisateur

| Type d’attribut    | Exemples de valeurs                |
|----------------   |----------------               |
| Booléen             | "true"                  |
|                     | « false » (valeur par défaut) |

Spécifie si le cache doit réinitialiser lorsque l’utilisateur connecté (ou Principal du contexte) change. L’utilisateur actuel est également connue sous le Principal demande du contexte et peuvent être affiché dans une vue Razor en référençant `@User.Identity.Name`.

L’exemple suivant ressemble au niveau de l’utilisateur actuellement connecté.  

Exemple :

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

À l’aide de cet attribut gère le contenu dans le cache via un cycle de connexion et déconnexion.  Lorsque vous utilisez `vary-by-user="true"`, une action de connexion et déconnexion invalide le cache pour l’utilisateur authentifié.  Le cache est invalidé, car une nouvelle valeur de cookie unique est générée sur la connexion. Cache est conservé à l’état anonyme lorsque aucun cookie n’est présent ou a expiré. Cela signifie que si aucun utilisateur n’est connecté, le cache est conservé.

- - -

### <a name="vary-by"></a>variation de

| Type d’attribut    | Exemples de valeurs                |
|----------------   |----------------               |
| Chaîne             | "@Model"                 |


Permet la personnalisation de données obtient mis en cache. Lorsque l’objet référencé par les modifications apportées à la valeur de la chaîne de l’attribut, le contenu de l’application d’assistance de balise de Cache est mis à jour. Fréquence à laquelle une concaténation de chaînes de valeurs de modèle sont affectées à cet attribut.  En effet, cela signifie qu'une mise à jour à une des valeurs concaténées invalide le cache.

L’exemple suivant suppose la somme de la vue de rendu de la valeur entière des deux paramètres d’itinéraire, la méthode du contrôleur `myParam1` et `myParam2`et qui retourne en tant que la propriété de modèle unique. Lorsque cette somme est modifié, le contenu de l’application d’assistance de balise de Cache est rendu et mis en cache à nouveau.  

Exemple :

Action :

```csharp
public IActionResult Index(string myParam1,string myParam2,string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

*Index.cshtml*

```cshtml
<cache vary-by="@Model"">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="priority"></a>priority

| Type d’attribut    | Exemples de valeurs                |
|----------------   |----------------               |
| CacheItemPriority  | « High »                   |
|                    | « Low » |
|                    | « NeverRemove » |
|                    | "Normal" |

Fournit des instructions de suppression de cache au fournisseur de cache intégré. Supprimez le serveur web `Low` mettre en cache les entrées tout d’abord une mémoire insuffisante.

Exemple :

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Le `priority` attribut ne garantit pas un niveau spécifique de la rétention du cache. `CacheItemPriority`est uniquement une suggestion. Cet attribut `NeverRemove` ne garantit pas que le cache est conservé. Consultez [des ressources supplémentaires](#additional-resources) pour plus d’informations.

L’application d’assistance de balise de Cache est dépendante de la [du service de cache mémoire](xref:performance/caching/memory). L’application d’assistance de balise de Cache ajoute le service s’il n’a pas été ajouté.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
