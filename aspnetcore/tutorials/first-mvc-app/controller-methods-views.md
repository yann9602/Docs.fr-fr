---
title: "Méthodes et vues de contrôleur"
author: rick-anderson
description: "Utilisation des méthodes, vues et DataAnnotations de contrôleur"
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: cfe1838371226334d368dca13bba37c5b1f6fc39
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="controller-methods-and-views"></a>Méthodes et vues de contrôleur

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Nous avons une bonne ébauche de l’application de films, mais sa présentation n’est pas idéale. Nous ne voulons pas voir l’heure (« 12:00:00 AM » dans l’image ci-dessous) et **ReleaseDate** doit être composé de deux mots.

![Vue d’index : Release Date apparaît en un seul mot (sans espace) et chaque date de sortie d’un film a comme heure « 12 AM »](working-with-sql/_static/m55.png)

Ouvrez le fichier *Models/Movie.cs*, puis ajoutez les lignes en surbrillance ci-dessous :

[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MovieDateWithExtraUsings.cs?name=snippet_1&highlight=13-14)]

Cliquez avec le bouton droit sur une ligne ondulée rouge **> Actions rapides et refactorisations**.

  ![Le menu contextuel affiche **> Actions rapides et refactorisations**.](controller-methods-views/_static/qa.png)


Appuyez sur `using System.ComponentModel.DataAnnotations;`

  ![using System.ComponentModel.DataAnnotations en haut de la liste](controller-methods-views/_static/da.png)

  Visual studio ajoute `using System.ComponentModel.DataAnnotations;`.

Supprimons les instructions `using` qui ne sont pas nécessaires. Elles s’affichent par défaut dans une police gris clair. Cliquez avec le bouton droit sur n’importe où dans le fichier *Movie.cs* **> Supprimer et trier les Usings**.

![Supprimer et trier les Usings](controller-methods-views/_static/rm.png)

Le code mis à jour :

[!code-csharp[Main](./start-mvc/sample/MvcMovie/Models/MovieDate.cs?name=snippet_1)]

<!-- include start -->

[!INCLUDE[adding-model](../../includes/mvc-intro/controller-methods-views.md)]

>[!div class="step-by-step"]
[Précédent](working-with-sql.md)
[Suivant](search.md)  
