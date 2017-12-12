---
title: "Migration à partir de l’API Web ASP.NET"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4f0564b4-ed4e-4e1e-9755-c1144d21a0ef
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/webapi
ms.openlocfilehash: 4acb7ccf7f944df5d08ac7faa342f0c72cf9d1a7
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="migrating-from-aspnet-web-api"></a>Migration à partir de l’API Web ASP.NET

Par [Steve Smith](https://ardalis.com/) et [Scott Addie](https://scottaddie.com)

API Web est des services HTTP qui atteignent un large éventail de clients, y compris les navigateurs et périphériques mobiles. Base d’ASP.NET MVC prend en charge les API Web offre un moyen unique et homogène de la création d’applications web de génération. Dans cet article, nous allons montrer les étapes requises pour migrer une implémentation de l’API Web à partir de l’API Web ASP.NET vers ASP.NET MVC de base.

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/migration/webapi/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="review-aspnet-web-api-project"></a>Projet d’API Web ASP.NET révision

Cet article utilise l’exemple de projet *ProductsApp*, créé dans l’article [Getting Started with ASP.NET Web API](https://docs.microsoft.com/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) comme point de départ. Dans ce projet, un projet d’API Web ASP.NET simple est configuré comme suit.

Dans *Global.asax.cs*, un appel est fait à `WebApiConfig.Register`:

[!code-csharp[Main](../migration/webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

`WebApiConfig`est défini dans *App_Start*, et a simplement une ligne statique `Register` méthode :

[!code-csharp[Main](../migration/webapi/sample/ProductsApp/App_Start/WebApiConfig.cs?highlight=15,16,17,18,19,20)]


Cette classe configure [attribut routage](https://docs.microsoft.com/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), bien qu’il n’est pas réellement utilisé dans le projet. Il configure également la table de routage qui est utilisée par l’API Web ASP.NET. Dans ce cas, API Web ASP.NET s’attend à recevoir les URL au format */api/ {controller} / {id}*, avec *{id}* est facultatif.

Le *ProductsApp* projet comprend qu’un seul contrôleur simple, qui hérite de `ApiController` et expose deux méthodes :

[!code-csharp[Main](../migration/webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=19,24)]

Enfin, le modèle, *produit*, utilisée par le *ProductsApp*, est une classe simple :

[!code-csharp[Main](webapi/sample/ProductsApp/Models/Product.cs)]

Maintenant que nous avons un projet simple à partir duquel commencer, nous pouvons montrent comment migrer ce projet d’API Web pour ASP.NET MVC de base.

## <a name="create-the-destination-project"></a>Créer le projet de Destination

À l’aide de Visual Studio, créez une solution vide et nommez-la *WebAPIMigration*. Ajouter existant *ProductsApp* de projet à ce dernier, puis ajouter un nouveau projet d’Application Web ASP.NET Core à la solution. Nommez le nouveau projet *ProductsCore*.

![Boîte de dialogue Nouveau projet ouvrir avec des modèles Web](webapi/_static/add-web-project.png)

Ensuite, choisissez le modèle de projet d’API Web. Nous allons migrer le *ProductsApp* le contenu pour ce nouveau projet.

![Boîte de dialogue nouvelle Application Web avec le modèle de projet d’API Web sélectionné dans la liste des modèles ASP.NET Core](webapi/_static/aspnet-5-webapi.png)

Supprimer le `Project_Readme.html` le fichier à partir du nouveau projet. Votre solution doit maintenant ressembler à ceci :

![Solution d’application ouverte dans l’Explorateur de solutions affichant les fichiers et dossiers des projets ProductsApp et ProductsCore](webapi/_static/webapimigration-solution.png)

## <a name="migrate-configuration"></a>Migration de Configuration

ASP.NET Core n’utilise plus *Global.asax*, *web.config*, ou *App_Start* dossiers. Au lieu de cela, toutes les tâches de démarrage sont effectuées en *Startup.cs* à la racine du projet (consultez [démarrage de l’Application](../fundamentals/startup.md)). Dans ASP.NET MVC de base, le routage basé sur l’attribut est à présent inclus par défaut lorsque `UseMvc()` est appelé ; et, il s’agit de l’approche recommandée pour la configuration des itinéraires de l’API Web (et comment le projet de démarrage des API Web gère le routage).

[!code-none[Main](../migration/webapi/sample/ProductsCore/Startup.cs?highlight=40)]

En supposant que vous souhaitez utiliser le routage d’attributs dans votre projet à l’avenir, aucune configuration supplémentaire n’est nécessaire. Simplement appliquer les attributs en fonction des besoins de vos contrôleurs et vos actions, comme dans l’exemple `ValuesController` classe qui est inclus dans le projet de démarrage des API Web :

[!code-csharp[Main](../migration/webapi/sample/ProductsCore/Controllers/ValuesController.cs?highlight=9,13,20,27,33,39)]

Notez la présence de *[controller]* sur la ligne 8. Le routage basé sur l’attribut maintenant prend en charge certains jetons, tels que *[controller]* et *[action]*. Ces jetons sont remplacés lors de l’exécution avec le nom du contrôleur ou action, respectivement, à laquelle l’attribut a été appliqué. Ceci permet de réduire le nombre de chaînes magiques dans le projet, et il garantit que les itinéraires restent synchronisés avec leurs contrôleurs correspondants et leurs actions lorsque refactorisations de changement de nom automatique sont appliquées.

Pour migrer le contrôleur d’API de produits, nous devons d’abord copier *ProductsController* vers le nouveau projet. Insérez ensuite simplement l’attribut d’itinéraire sur le contrôleur :

```csharp
[Route("api/[controller]")]
```

Vous devez également ajouter le `[HttpGet]` d’attributs pour les deux méthodes, étant donné que les deux doivent être appelés via HTTP Get. Inclure l’attente d’un paramètre « id » dans l’attribut pour `GetProduct()`:

```csharp
// /api/products
[HttpGet]
...

// /api/products/1
[HttpGet("{id}")]
```

À ce stade, le routage est correcte ; Toutefois, nous ne pouvons pas encore le tester. Des modifications supplémentaires doivent être effectuées avant *ProductsController* peut être compilé.

## <a name="migrate-models-and-controllers"></a>Migrer des modèles et des contrôleurs

La dernière étape du processus de migration pour ce projet d’API Web simple consiste à copier sur les contrôleurs et les modèles qu’ils utilisent. Dans ce cas, il suffit de copier *Controllers/ProductsController.cs* à partir du projet d’origine vers le nouveau. Ensuite, copiez la totalité du dossier des modèles de projet d’origine vers le nouveau. Ajuster les espaces de noms pour le nouveau nom de projet (*ProductsCore*).  À ce stade, vous pouvez générer l’application, et vous trouverez un nombre d’erreurs de compilation. Il doivent généralement être comprises dans les catégories suivantes :

* *ApiController* n’existe pas

* *System.Web.Http* espace de noms n’existe pas

* *IHttpActionResult* n’existe pas

Heureusement, il s’agit très faciles à corriger :

* Modification *ApiController* à *contrôleur* (il se pouvez que vous deviez ajouter *à l’aide de Microsoft.AspNetCore.Mvc*)

* Supprimer tout à l’aide d’instruction faisant référence à *System.Web.Http*

* Modifier toute méthode retournant *IHttpActionResult* pour renvoyer un *IActionResult*

Une fois ces modifications ont été apportées et inutilisés à l’aide d’instructions supprimés, migrées *ProductsController* classe ressemble à ceci :

[!code-csharp[Main](../migration/webapi/sample/ProductsCore/Controllers/ProductsController.cs?highlight=1,2,6,8,9,27)]

Vous devez maintenant être en mesure d’exécuter le projet migré et accédez à */api/produits*; et, vous devez voir la liste complète des 3 produits. Accédez à */api/products/1* et vous devez voir le premier produit.

## <a name="summary"></a>Résumé

Migration d’un projet d’API Web ASP.NET simple vers ASP.NET MVC de base est assez simple, grâce à la prise en charge intégrée pour les API Web dans ASP.NET MVC de base. Le principal que doivent faire migrer tous les projets API Web ASP.NET sont itinéraires, les contrôleurs et les modèles, ainsi que des mises à jour pour les types utilisés par les contrôleurs et les actions.
