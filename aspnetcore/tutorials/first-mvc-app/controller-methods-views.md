---
title: "Méthodes et vues de contrôleur"
author: rick-anderson
description: "Utilisation des méthodes, vues et DataAnnotations de contrôleur"
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: get-started-article
ms.assetid: c7313211-b271-4adf-bab8-8e72603cc0ce
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: 8960853438d3227de3ef7c50936626149d8d5997
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="controller-methods-and-views"></a><span data-ttu-id="a1788-104">Méthodes et vues de contrôleur</span><span class="sxs-lookup"><span data-stu-id="a1788-104">Controller methods and views</span></span>

<span data-ttu-id="a1788-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a1788-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a1788-106">Nous avons un bon début pour l’application gestion de films, mais sa présentation n’est pas idéale.</span><span class="sxs-lookup"><span data-stu-id="a1788-106">We have a good start to the movie app, but the presentation is not ideal.</span></span> <span data-ttu-id="a1788-107">Nous ne voulons pas voir l’heure (« 12:00:00 AM » dans l’image ci-dessous) et **ReleaseDate** doit être composé de deux mots.</span><span class="sxs-lookup"><span data-stu-id="a1788-107">We don't want to see the time (12:00:00 AM in the image below) and **ReleaseDate** should be two words.</span></span>

![Vue d’index : Release Date apparaît en un seul mot (sans espace) et chaque date de sortie d’un film a comme heure « 12 AM »](working-with-sql/_static/m55.png)

<span data-ttu-id="a1788-109">Ouvrez le fichier *Models/Movie.cs*, puis ajoutez les lignes en surbrillance ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="a1788-109">Open the *Models/Movie.cs* file and add the highlighted lines shown below:</span></span>

<span data-ttu-id="a1788-110">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MovieDateWithExtraUsings.cs?name=snippet_1&highlight=13-14)]</span><span class="sxs-lookup"><span data-stu-id="a1788-110">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MovieDateWithExtraUsings.cs?name=snippet_1&highlight=13-14)]</span></span>

<span data-ttu-id="a1788-111">Cliquez avec le bouton droit sur une ligne ondulée rouge **> Actions rapides et refactorisations**.</span><span class="sxs-lookup"><span data-stu-id="a1788-111">Right click on a red squiggly line **> Quick Actions and Refactorings**.</span></span>

  ![Le menu contextuel affiche **> Actions rapides et refactorisations**.](controller-methods-views/_static/qa.png)


<span data-ttu-id="a1788-113">Appuyez sur `using System.ComponentModel.DataAnnotations;`</span><span class="sxs-lookup"><span data-stu-id="a1788-113">Tap `using System.ComponentModel.DataAnnotations;`</span></span>

  ![using System.ComponentModel.DataAnnotations en haut de la liste](controller-methods-views/_static/da.png)

  <span data-ttu-id="a1788-115">Visual studio ajoute `using System.ComponentModel.DataAnnotations;`.</span><span class="sxs-lookup"><span data-stu-id="a1788-115">Visual studio adds `using System.ComponentModel.DataAnnotations;`.</span></span>

<span data-ttu-id="a1788-116">Supprimons les instructions `using` qui ne sont pas nécessaires.</span><span class="sxs-lookup"><span data-stu-id="a1788-116">Let's remove the `using` statements that are not needed.</span></span> <span data-ttu-id="a1788-117">Elles s’affichent par défaut dans une police gris clair.</span><span class="sxs-lookup"><span data-stu-id="a1788-117">They show up by default in a light grey font.</span></span> <span data-ttu-id="a1788-118">Cliquez avec le bouton droit sur n’importe où dans le fichier *Movie.cs* **> Supprimer et trier les Usings**.</span><span class="sxs-lookup"><span data-stu-id="a1788-118">Right click anywhere in the *Movie.cs* file **> Remove and Sort Usings**.</span></span>

![Supprimer et trier les Usings](controller-methods-views/_static/rm.png)

<span data-ttu-id="a1788-120">Le code mis à jour :</span><span class="sxs-lookup"><span data-stu-id="a1788-120">The updated code:</span></span>

<span data-ttu-id="a1788-121">[!code-csharp[Main](./start-mvc/sample/MvcMovie/Models/MovieDate.cs?name=snippet_1)]</span><span class="sxs-lookup"><span data-stu-id="a1788-121">[!code-csharp[Main](./start-mvc/sample/MvcMovie/Models/MovieDate.cs?name=snippet_1)]</span></span>

<!-- include start -->

[!INCLUDE[adding-model](../../includes/mvc-intro/controller-methods-views.md)]

>[!div class="step-by-step"]
<span data-ttu-id="a1788-122">[Précédent](working-with-sql.md)
[Suivant](search.md)</span><span class="sxs-lookup"><span data-stu-id="a1788-122">[Previous](working-with-sql.md)
[Next](search.md)</span></span>  
