---
title: "Examen des méthodes Details et Delete"
author: rick-anderson
description: "La méthode et l’affichage du contrôleur Details dans une application ASP.NET Core MVC de base."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 43394106c9074f9487e1065a37a88eb017833bae
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="examining-the-details-and-delete-methods"></a>Examen des méthodes Details et Delete

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Ouvrez le contrôleur vidéo et examinez la méthode `Details` :

[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_details)]

Le moteur de génération de modèles automatique MVC qui a créé cette méthode d’action ajoute un commentaire présentant une requête HTTP qui appelle la méthode. Dans le cas présent, il s’agit d’une requête GET avec trois segments d’URL : le contrôleur `Movies`, la méthode `Details` et une valeur `id`. N’oubliez pas que ces segments sont définis dans *Startup.cs*.

[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

EF facilite la recherche de données à l’aide de la méthode `SingleOrDefaultAsync`. Une fonctionnalité de sécurité importante intégrée à la méthode réside dans le fait que le code vérifie que la méthode de recherche a trouvé un film avant de tenter toute opération que ce soit avec lui. Par exemple, un pirate informatique pourrait induire des erreurs dans le site en modifiant l’URL créée par les liens, en remplaçant `http://localhost:xxxx/Movies/Details/1` par quelque chose comme `http://localhost:xxxx/Movies/Details/12345` (ou une autre valeur qui ne représente pas un film réel). Si vous n’avez pas recherché un film non valide, l’application lève une exception.

Examinez les méthodes `Delete` et `DeleteConfirmed`.

[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete)]

Notez que la méthode `HTTP GET Delete` ne supprime pas le film spécifié, mais retourne une vue du film où vous pouvez soumettre (HttpPost) la suppression. L’exécution d’une opération de suppression en réponse à une requête GET (ou encore l’exécution d’une opération de modification, d’une opération de création ou de toute autre opération qui modifie des données) génère une faille de sécurité.

La méthode `[HttpPost]` qui supprime les données est nommée `DeleteConfirmed` pour donner à la méthode HTTP POST une signature ou un nom unique. Les signatures des deux méthodes sont illustrées ci-dessous :

[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]


Le Common Language Runtime (CLR) nécessite des méthodes surchargées pour avoir une signature à paramètre unique (même nom de méthode, mais liste de paramètres différentes). Toutefois, vous avez ici besoin de deux méthodes `Delete` (une pour GET et une pour POST) ayant toutes les deux la même signature de paramètre. (Elles doivent toutes les deux accepter un entier unique comme paramètre.)

Il existe deux approches pour ce problème. L’une consiste à attribuer aux méthodes des noms différents. C’est ce qu’a fait le mécanisme de génération de modèles automatique dans l’exemple précédent. Toutefois, elle présente un petit problème : ASP.NET mappe des segments d’une URL à des méthodes d’action par nom. Si vous renommez une méthode, il est probable que le routage ne pourra pas trouver cette méthode. La solution consiste à faire ce que vous voyez dans l’exemple, c’est-à-dire à ajouter l’attribut `ActionName("Delete")` à la méthode `DeleteConfirmed`. Cet attribut effectue un mappage du système de routage afin qu’une URL qui inclut /Delete/ pour une requête POST trouve la méthode `DeleteConfirmed`.

Pour contourner le problème des méthodes qui ont des noms et des signatures identiques, vous pouvez également modifier artificiellement la signature de la méthode POST pour inclure un paramètre supplémentaire (inutilisé). C’est ce que nous avons fait dans une publication précédente quand nous avons ajouté le paramètre `notUsed`. Vous pouvez faire de même ici pour la méthode `[HttpPost] Delete` :

```csharp
// POST: Movies/Delete/6
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a>Publier sur Azure

Consultez la page [Publier une application web ASP.NET Core sur Azure App Service à l’aide de Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) pour savoir comment publier cette application sur Azure à l’aide de Visual Studio.  Il est également possible de la publier en [ligne de commande](xref:tutorials/publish-to-azure-webapp-using-cli).

Nous vous remercions d’avoir effectué cette introduction à ASP.NET Core MVC. Vos commentaires nous intéressent. La rubrique [Bien démarrer avec MVC et EF Core](xref:data/ef-mvc/intro) est un excellent moyen de poursuivre après ce didacticiel.

>[!div class="step-by-step"]
[Précédent](validation.md)
