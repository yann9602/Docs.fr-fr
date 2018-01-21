---
title: "La gestion des requêtes avec des contrôleurs dans ASP.NET MVC de base"
author: ardalis
description: 
ms.author: riande
manager: wpickett
ms.date: 07/03/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/actions
ms.openlocfilehash: cef493fc2010d1c82e5c1dfec85864539252b817
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="handling-requests-with-controllers-in-aspnet-core-mvc"></a>La gestion des requêtes avec des contrôleurs dans ASP.NET MVC de base

Par [Steve Smith](https://ardalis.com/) et [Scott Addie](https://github.com/scottaddie)

Contrôleurs, les actions et les résultats d’action sont un élément fondamental de comment créer des applications à l’aide d’ASP.NET MVC de base par les développeurs.

## <a name="what-is-a-controller"></a>Qu’est un contrôleur ?

Un contrôleur est utilisé pour définir et regrouper un ensemble d’actions. Une action (ou *méthode d’action*) est une méthode sur un contrôleur qui gère les demandes. Contrôleurs de regrouper logiquement des actions similaires. Cette agrégation d’actions permet des jeux de règles, telles que le routage, la mise en cache et l’autorisation, à appliquer collectivement communs. Les demandes sont mappées à des actions via [routage](xref:mvc/controllers/routing).

Par convention, les classes de contrôleur :
* Résident dans la racine du projet *contrôleurs* dossier
* Hériter de`Microsoft.AspNetCore.Mvc.Controller`

Un contrôleur est une classe Instanciable dans laquelle au moins une des conditions suivantes est true :
* Le nom de classe est suivi du suffixe « Controller »
* La classe hérite d’une classe dont le nom est suivi du suffixe « Controller »
* La classe est décorée avec le `[Controller]` attribut

Une classe de contrôleur ne doit pas être associé à `[NonController]` attribut.

Contrôleurs doivent suivre le [principe de dépendances explicites](http://deviq.com/explicit-dependencies-principle/). Il existe deux approches pour implémenter ce principe. Si plusieurs actions de contrôleur nécessitent le même service, envisagez d’utiliser [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) pour demander ces dépendances. Si le service est requis par une seule méthode d’action unique, envisagez d’utiliser [Action Injection](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) pour demander la dépendance.

Dans le **M**odèle -**V**UE -**C**ontroller modèle, un contrôleur est chargé pour le traitement initial de la demande et l’instanciation du modèle. En règle générale, les décisions commerciales doivent être effectuées dans le modèle.

Le contrôleur prend le résultat de l’option de traitement du modèle (le cas échéant) et retourne la vue appropriée et ses données de la vue associée ou le résultat de l’appel d’API. En savoir plus sur [vue d’ensemble d’ASP.NET Core MVC](xref:mvc/overview) et [mise en route avec ASP.NET MVC de base et de Visual Studio](xref:tutorials/first-mvc-app/start-mvc).

Le contrôleur est un *au niveau de l’interface utilisateur* abstraction. Ses responsabilités sont données de la demande est valides et choisir quelle vue (ou le résultat d’API) doit être retournée. Dans les applications bien factorisées, il n’inclut pas directement logique de données access ou d’entreprise. Au lieu de cela, le contrôleur délègue aux services de gestion de ces responsabilités.

## <a name="defining-actions"></a>Définition des Actions

Les méthodes publiques sur un contrôleur, sauf ceux décorée avec le `[NonAction]` d’attributs, sont des actions. Paramètres pour les actions sont liées aux données de la requête et sont validés à l’aide de [liaison de modèle](xref:mvc/models/model-binding). Validation du modèle se produit pour tout ce qui est liée au modèle. Le `ModelState.IsValid` valeur de la propriété indique si la liaison de modèle et la validation a réussi.

Méthodes d’action doivent contenir de logique pour mapper une demande pour une activité commerciale. Problèmes d’entreprise doivent généralement être représentés en tant que services le contrôleur accède via [injection de dépendance](xref:mvc/controllers/dependency-injection). Actions mappent le résultat de l’action entreprise sur un état de l’application.

Actions peuvent retourner une valeur, mais souvent retourner une instance de `IActionResult` (ou `Task<IActionResult>` pour les méthodes async) qui génère une réponse. La méthode d’action est chargée de choisir *le type de réponse*. Le résultat d’action *est la réponse*.

### <a name="controller-helper-methods"></a>Méthodes d’assistance de contrôleur

Contrôleurs héritent généralement [contrôleur](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller), bien que cela n’est pas nécessaire. Dérivation de `Controller` fournit l’accès aux trois catégories de méthodes d’assistance :

#### <a name="1-methods-resulting-in-an-empty-response-body"></a>1. Méthodes résultant dans un corps de réponse vide

Ne `Content-Type` en-tête de réponse HTTP est inclus, étant donné que le corps de réponse n’a pas de contenu à décrire.

Il existe deux types de résultats de cette catégorie : redirection et le Code d’état HTTP.

* **Code d’état HTTP**

    Ce type retourne un code d’état HTTP. Deux méthodes d’assistance de ce type sont `BadRequest`, `NotFound`, et `Ok`. Par exemple, `return BadRequest();` génère un code de 400 état lors de l’exécution. Lorsque les méthodes telles que `BadRequest`, `NotFound`, et `Ok` sont surchargés, elles ne sont plus qualifiées de répondeurs du Code d’état HTTP, étant donné que la négociation de contenu est en cours.

* **Redirect**

    Ce type retourne une redirection pour une action ou une destination (à l’aide de `Redirect`, `LocalRedirect`, `RedirectToAction`, ou `RedirectToRoute`). Par exemple, `return RedirectToAction("Complete", new {id = 123});` redirige vers `Complete`, en passant un objet anonyme.

    Le type de résultat de redirection est différent du type de Code d’état HTTP principalement de l’ajout d’un `Location` en-tête de réponse HTTP.

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a>2. Méthodes résultant dans un corps de réponse de non vide avec un type de contenu prédéfini

La plupart des méthodes d’assistance de cette catégorie incluent un `ContentType` propriété, ce qui vous permet de définir la `Content-Type` en-tête de réponse pour décrire le corps de réponse.

Il existe deux types de résultats de cette catégorie : [vue](xref:mvc/views/overview) et [au format de réponse](xref:mvc/models/formatting).

* **Affichage**

    Ce type retourne une vue qui utilise un modèle pour le rendu HTML. Par exemple, `return View(customer);` transmet un modèle à l’affichage pour la liaison de données.

* **Réponse mise en forme**

    Ce type retourne JSON ou un format d’échange de données similaires pour représenter un objet d’une manière spécifique. Par exemple, `return Json(customer);` sérialise l’objet fourni au format JSON.
    
    Les autres méthodes courantes de ce type sont `File`, `PhysicalFile`, et `VirtualFile`. Par exemple, `return PhysicalFile(customerFilePath, "text/xml");` retourne un fichier XML décrit par un `Content-Type` valeur d’en-tête de réponse de « texte/xml ».

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a>3. Mise en forme de méthodes résultant dans un corps de réponse de non vide dans un type de contenu négocié avec le client

Cette catégorie est mieux appelé **négociation de contenu**. [Négociation de contenu](xref:mvc/models/formatting#content-negotiation) s’applique chaque fois qu’une action retourne un [ObjectResult](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.objectresult) type ou une valeur autre qu’une [IActionResult](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.iactionresult) mise en œuvre. Une action qui retourne un non -`IActionResult` implémentation (par exemple, `object`) retourne également une réponse mise en forme.

Voici quelques méthodes d’assistance de ce type : `BadRequest`, `CreatedAtRoute`, et `Ok`. Exemples de ces méthodes `return BadRequest(modelState);`, `return CreatedAtRoute("routename", values, newobject);`, et `return Ok(value);`, respectivement. Notez que `BadRequest` et `Ok` effectuer la négociation de contenu uniquement lorsque la valeur passée ; sans passer une valeur, ils servent à la place en tant que types de résultats de Code d’état HTTP. Le `CreatedAtRoute` (méthode), quant à eux, exécute toujours négociation de contenu depuis ses surcharges tous requièrent qu’une valeur est passée.

### <a name="cross-cutting-concerns"></a>Problèmes transversaux

En règle générale, les applications partagent les parties de leur flux de travail. Une application qui nécessite l’authentification pour accéder au panier d’achat ou une application qui met en cache les données de certaines pages sont des exemples. Pour exécuter la logique avant ou après une méthode d’action, utilisez un *filtre*. À l’aide de [filtres](xref:mvc/controllers/filters) sur les problèmes transversaux peut réduire la duplication, ce qui leur permet de suivre les [ne répétez vous-même (sec) principe](http://deviq.com/don-t-repeat-yourself/).

Plus un filtrage des attributs, tels que `[Authorize]`, peuvent être appliquées au niveau du contrôleur ou d’action selon le niveau de granularité souhaité.

Gestion des erreurs et la réponse mise en cache sont souvent des problèmes transversaux :
   * [Gestion des erreurs](xref:mvc/controllers/filters#exception-filters)
   * [Mise en cache des réponses](xref:performance/caching/response)

Nombreux problèmes transversaux peuvent être gérées à l’aide de filtres ou personnalisé [intergiciel (middleware)](xref:fundamentals/middleware).
