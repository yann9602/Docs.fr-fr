---
title: "Ajout d’une fonction de recherche"
author: rick-anderson
description: "Montre comment ajouter une fonction de recherche à une application ASP.NET MVC simple"
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: get-started-article
ms.assetid: d69e5529-8ef6-4628-855d-200206d962b9
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: f811cd0063404872b0e993a99e8a92cc354064ce
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
[!INCLUDE[adding-model](../../includes/mvc-intro/search1.md)]

<span data-ttu-id="9531c-104">Vous pouvez renommer rapidement le paramètre`searchString` en `id` à l’aide de la commande **Renommer**.</span><span class="sxs-lookup"><span data-stu-id="9531c-104">You can quickly rename the `searchString` parameter to `id` with the **rename** command.</span></span> <span data-ttu-id="9531c-105">Cliquez avec le bouton droit sur `searchString` **> Renommer**.</span><span class="sxs-lookup"><span data-stu-id="9531c-105">Right click on `searchString` **> Rename**.</span></span>

![Menu contextuel](search/_static/rename.png)

<span data-ttu-id="9531c-107">Les cibles de renommage sont mises en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="9531c-107">The rename targets are highlighted.</span></span>

![Éditeur de code présentant la variable mise en surbrillance à l’aide de la méthode Index ActionResult](search/_static/rename2.png)

<span data-ttu-id="9531c-109">Remplacez le paramètre par `id` et toutes les occurrences de `searchString` par `id`.</span><span class="sxs-lookup"><span data-stu-id="9531c-109">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

![Éditeur de code montrant que la variable a été remplacée par id](search/_static/rename3.png)

[!INCLUDE[adding-model](../../includes/mvc-intro/search2.md)]

<span data-ttu-id="9531c-111">Notez comment IntelliSense nous permet de mettre à jour la balise.</span><span class="sxs-lookup"><span data-stu-id="9531c-111">Notice how intelliSense helps us update the markup.</span></span>

![Menu contextuel IntelliSense avec « method » sélectionné dans la liste des attributs de l’élément form](search/_static/int_m.png)

![Menu contextuel IntelliSense avec « get » sélectionné dans la liste des valeurs d’attribut de « method »](search/_static/int_get.png)

<span data-ttu-id="9531c-114">Notez la police distinctive dans la balise `<form>`.</span><span class="sxs-lookup"><span data-stu-id="9531c-114">Notice the distinctive font in the `<form>` tag.</span></span> <span data-ttu-id="9531c-115">Cette police distinctive indique que la balise est prise en charge par les [Tag Helpers](../../mvc/views/tag-helpers/intro.md).</span><span class="sxs-lookup"><span data-stu-id="9531c-115">That distinctive font indicates the tag is supported by [Tag Helpers](../../mvc/views/tag-helpers/intro.md).</span></span>

![balise form avec du texte en violet](search/_static/th_font.png)

[!INCLUDE[adding-model](../../includes/mvc-intro/search3.md)]

>[!div class="step-by-step"]
<span data-ttu-id="9531c-117">[Précédent](controller-methods-views.md)
[Suivant](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="9531c-117">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>  
