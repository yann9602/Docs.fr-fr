---
uid: mvc/overview/older-versions-1/controllers-and-routing/understanding-action-filters-vb
title: "Présentation des filtres d’Action (VB) | Documents Microsoft"
author: microsoft
description: "L’objectif de ce didacticiel est d’expliquer les filtres d’action. Un filtre d’action est un attribut que vous pouvez appliquer à une action de contrôleur--ou à un contrôleur ensemble..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/16/2008
ms.topic: article
ms.assetid: e83812f2-c53e-4a43-a7c1-d64c59ecf694
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/controllers-and-routing/understanding-action-filters-vb
msc.type: authoredcontent
ms.openlocfilehash: 483133ec5db27c2fa1ed4b463e37e17efab12e0f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="understanding-action-filters-vb"></a>Présentation des filtres d’Action (VB)
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le PDF](http://download.microsoft.com/download/e/f/3/ef3f2ff6-7424-48f7-bdaa-180ef64c3490/ASPNET_MVC_Tutorial_14_VB.pdf)

> L’objectif de ce didacticiel est d’expliquer les filtres d’action. Un filtre d’action est un attribut que vous pouvez appliquer à une action de contrôleur--ou à un contrôleur entière--qui modifie la façon dont dans lequel l’action est exécutée.


## <a name="understanding-action-filters"></a>Présentation des filtres d’Action

L’objectif de ce didacticiel est d’expliquer les filtres d’action. Un filtre d’action est un attribut que vous pouvez appliquer à une action de contrôleur--ou à un contrôleur entière--qui modifie la façon dont dans lequel l’action est exécutée. L’infrastructure ASP.NET MVC inclut plusieurs filtres d’action :

- OutputCache – ce filtre d’action met en cache la sortie d’une action de contrôleur pour une durée spécifiée.
- HandleError – ce filtre d’action gère les erreurs qui se produisent lors de l’exécution d’une action de contrôleur.
- Autoriser – ce filtre d’action vous permet de restreindre l’accès à un utilisateur particulier ou un rôle.

Vous pouvez également créer vos propres filtres d’action personnalisés. Par exemple, vous souhaiterez créer un filtre d’action personnalisé pour implémenter un système d’authentification personnalisé. Ou bien, vous souhaiterez peut-être créer un filtre d’action qui modifie les données retournées par une action de contrôleur d’affichage.

Dans ce didacticiel, vous allez apprendre à créer un filtre d’action de toutes pièces. Nous créons un filtre d’action de journal qui consigne les différentes étapes du traitement d’une action dans la fenêtre de sortie de Visual Studio.

### <a name="using-an-action-filter"></a>À l’aide d’un filtre d’Action

Un filtre d’action est un attribut. Vous pouvez appliquer la plupart des filtres d’action pour une action de contrôleur individuels ou d’un contrôleur entière.

Par exemple, le contrôleur de données dans la liste 1 expose une action nommée `Index()` qui retourne l’heure actuelle. Cette action est décorée avec le `OutputCache` filtre d’action. Ce filtre provoque la valeur retournée par l’action doit être mis en cache pendant 10 secondes.

**La liste 1 :`Controllers\DataController.vb`**

[!code-vb[Main](understanding-action-filters-vb/samples/sample1.vb)]

Si vous appelez à plusieurs reprises la `Index()` action en entrant l’URL/données/Index dans la barre d’adresses de votre navigateur et en appuyant sur l’actualisation bouton plusieurs fois, vous verrez simultanément pendant 10 secondes. La sortie de la `Index()` action est mis en cache pendant 10 secondes (voir Figure 1).


[![Fois mis en cache](understanding-action-filters-vb/_static/image2.png)](understanding-action-filters-vb/_static/image1.png)

**Figure 01**: heure de mise en cache ([cliquez pour afficher l’image en taille réelle](understanding-action-filters-vb/_static/image3.png))


Dans la liste 1, un filtre d’action unique : le `OutputCache` filtre d’action : est appliqué à la `Index()` (méthode). Si vous avez besoin, vous pouvez appliquer plusieurs filtres d’action pour la même action. Par exemple, vous pouvez souhaiter appliquer à la fois le `OutputCache` et `HandleError` filtres d’action pour la même action.

Dans la liste de 1, le `OutputCache` filtre d’action est appliquée à la `Index()` action. Vous pouvez également appliquer cet attribut à la `DataController` classe elle-même. Dans ce cas, le résultat retourné par une action exposée par le contrôleur est mis en cache pendant 10 secondes.

### <a name="the-different-types-of-filters"></a>Les différents Types de filtres

L’infrastructure ASP.NET MVC prend en charge quatre types de filtres :

1. Filtres d’autorisation : implémente le `IAuthorizationFilter` attribut.
2. Filtres d’action : implémente le `IActionFilter` attribut.
3. Générer des filtres : implémente le `IResultFilter` attribut.
4. Filtres d’exception : implémente le `IExceptionFilter` attribut.

Les filtres sont exécutés dans l’ordre indiqué ci-dessus. Par exemple, les filtres d’autorisation sont toujours exécutés avant les filtres d’action et les filtres d’exception sont toujours exécutées après tous les autres types de filtre.

Filtres d’autorisation sont utilisées pour implémenter l’authentification et autorisation pour les actions de contrôleur. Par exemple, le filtre Authorize est un exemple de filtre d’autorisation.

Filtres d’action contiennent une logique qui est exécutée avant et après l’exécution d’une action de contrôleur. Vous pouvez utiliser un filtre d’action, par exemple, pour modifier les données d’affichage renvoyant à une action du contrôleur.

Filtres de résultat contiennent une logique qui est exécutée avant et après l’exécution du résultat d’une vue. Par exemple, vous pouvez souhaiter modifier un résultat de la vue avant que la vue est restituée dans le navigateur.

Filtres d’exception sont le dernier type de filtre à exécuter. Vous pouvez utiliser un filtre d’exception à gérer les erreurs déclenchées par vos actions de contrôleur ou résultats d’action de contrôleur. Vous pouvez également utiliser les filtres d’exception pour consigner les erreurs.

Chaque type de filtre est exécutée dans un ordre particulier. Si vous souhaitez contrôler l’ordre dans lequel les filtres du même type sont exécutées vous pouvez définir les propriétés de commande d’un filtre.

La classe de base pour tous les filtres d’action est la `System.Web.Mvc.FilterAttribute` classe. Si vous souhaitez implémenter un type particulier de filtre, vous devez créer une classe qui hérite de la classe de filtre de base et implémente une ou plusieurs des IAuthorizationFilter, IActionFilter, IResultFilter ou ExceptionFilter des interfaces.

### <a name="the-base-actionfilterattribute-class"></a>La classe de Base ActionFilterAttribute

Pour le rendre plus facile à implémenter un filtre d’action personnalisé, l’infrastructure ASP.NET MVC inclut une base de `ActionFilterAttribute` classe. Cette classe implémente à la fois le `IActionFilter` et `IResultFilter` interfaces et hérite de la `Filter` classe.

La terminologie ici n’est pas entièrement cohérente. Techniquement, une classe qui hérite de la classe ActionFilterAttribute est un filtre d’action et un filtre de résultat. Toutefois, dans le sens de pertes de connexion, le filtre d’action word est utilisé pour faire référence à n’importe quel type de filtre dans l’infrastructure ASP.NET MVC.

La classe de base ActionFilterAttribute a les méthodes suivantes que vous pouvez substituer :

- OnActionExecuting – cette méthode est appelée avant l’exécution d’une action du contrôleur.
- OnActionExecuted – cette méthode est appelée après l’exécution d’une action du contrôleur.
- OnResultExecuting : cette méthode est appelée avant l’exécution d’un résultat d’action de contrôleur.
- OnResultExecuted – cette méthode est appelée après l’exécution d’un résultat d’action de contrôleur.

Dans la section suivante, nous verrons comment vous pouvez implémenter chacun de ces différentes méthodes.

### <a name="creating-a-log-action-filter"></a>Création d’un filtre d’Action de journal

Afin d’illustrer comment vous pouvez créer un filtre d’action personnalisé, nous allons créer un filtre d’action personnalisé qui enregistre les étapes de traitement d’une action du contrôleur dans la fenêtre Sortie de Visual Studio. Notre `LogActionFilter` est contenue dans la liste 2.

**Liste 2 :`ActionFilters\LogActionFilter.vb`**

[!code-vb[Main](understanding-action-filters-vb/samples/sample2.vb)]

Dans la liste 2, le `OnActionExecuting()`, `OnActionExecuted()`, `OnResultExecuting()`, et `OnResultExecuted()` toutes les méthodes appellent le `Log()` (méthode). Le nom de la méthode et les données d’itinéraire actuel est passé à la `Log()` (méthode). Le `Log()` méthode écrit un message dans la fenêtre Sortie de Visual Studio (voir Figure 2).


[![Écriture dans la fenêtre Sortie de Visual Studio](understanding-action-filters-vb/_static/image5.png)](understanding-action-filters-vb/_static/image4.png)

**Figure 02**: l’écriture dans la fenêtre Sortie de Visual Studio ([cliquez pour afficher l’image en taille réelle](understanding-action-filters-vb/_static/image6.png))


Le contrôleur Home dans le Listing 3 illustre comment vous pouvez appliquer le filtre d’action de journal à une classe de contrôleur entière. Chaque fois que toutes les actions exposées par le contrôleur Home sont appelées – soit le `Index()` méthode ou la `About()` méthode – les étapes de traitement de l’action sont enregistrés dans la fenêtre Sortie de Visual Studio.

**La liste 3 :`Controllers\HomeController.vb`**

[!code-vb[Main](understanding-action-filters-vb/samples/sample3.vb)]

### <a name="summary"></a>Résumé

Dans ce didacticiel, vous ont été introduits pour les filtres d’action ASP.NET MVC. Vous avez appris les quatre différents types de filtres : filtres d’autorisation, les filtres d’action, les filtres de résultat et les filtres d’exception. Vous avez également appris sur la base `ActionFilterAttribute` classe.

Enfin, vous avez appris comment implémenter un filtre d’action simple. Nous avons créé un filtre d’action de journal qui enregistre les étapes de traitement d’une action du contrôleur dans la fenêtre Sortie de Visual Studio.

>[!div class="step-by-step"]
[Précédent](asp-net-mvc-routing-overview-vb.md)
[Suivant](improving-performance-with-output-caching-vb.md)
