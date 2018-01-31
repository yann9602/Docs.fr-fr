---
title: "Méthodes et vues de contrôleur"
author: rick-anderson
description: "Utilisation des méthodes, vues et DataAnnotations de contrôleur"
manager: wpickett
ms.author: riande
ms.date: 03/07/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: 200f02f9815966653b3b46918737c60d11f11d5a
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
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
