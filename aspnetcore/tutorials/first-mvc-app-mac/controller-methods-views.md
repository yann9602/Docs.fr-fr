---
title: "Méthodes et vues de contrôleur dans une application ASP.NET Core MVC"
author: rick-anderson
description: "Utilisation des méthodes, vues et DataAnnotations de contrôleur"
manager: wpickett
ms.author: riande
ms.date: 04/07/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/first-mvc-app-mac/controller-methods-views
ms.openlocfilehash: 01c20e505bd9d1591e1921701f94d102822231c8
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="controller-methods-and-views-in-an-aspnet-core-mvc-app"></a>Méthodes et vues de contrôleur dans une application ASP.NET Core MVC

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Nous avons une bonne ébauche de l’application de films, mais sa présentation n’est pas idéale. Nous ne voulons pas voir l’heure (« 12:00:00 AM » dans l’illustration suivante) et **ReleaseDate** doit être composé de deux mots.

![Vue d’index : Release Date apparaît en un seul mot (sans espace) et chaque date de sortie d’un film a comme heure « 12 AM »](../../tutorials/first-mvc-app/working-with-sql/_static/m55.png)

Ouvrez le fichier *Models/Movie.cs*, puis ajoutez les lignes en surbrillance ci-dessous :

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDate.cs?name=snippet_1&highlight=2,11-12)]

Générez et exécutez l’application.

<!-- include start
![MVC Movie application open browser showing movie data](../../tutorials/first-mvc-app/working-with-sql/_static/m55.png)

 -->

[!INCLUDE[adding-model](../../includes/mvc-intro/controller-methods-views.md)]

>[!div class="step-by-step"]
[Précédent - Utilisation de SQLite](working-with-sql.md)
[Suivant - Ajouter une fonction de recherche](search.md)
