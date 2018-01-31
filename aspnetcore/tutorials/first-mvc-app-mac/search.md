---
title: "Ajout d’une fonctionnalité de recherche à une application ASP.NET Core MVC"
author: rick-anderson
description: "Montre comment ajouter une fonction de recherche à une application ASP.NET MVC simple"
manager: wpickett
ms.author: riande
ms.date: 04/07/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/first-mvc-app-mac/search
ms.openlocfilehash: d0e42242fd8c05b538e5e2e2686b77c95e9b90f4
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
[!INCLUDE[adding-model](../../includes/mvc-intro/search1.md)]

Remarque : SQLite respecte la casse, vous devez donc rechercher « Ghost » et non pas « ghost ».

[!INCLUDE[adding-model](../../includes/mvc-intro/search2.md)]

Changez la balise `<form>` dans la vue Razor *Views\movie\Index.cshtml* pour spécifier `method="get"` :

```html
<form asp-controller="Movies" asp-action="Index" method="get">
```

[!INCLUDE[adding-model](../../includes/mvc-intro/search3.md)]

>[!div class="step-by-step"]
[Précédent - Méthodes et vues du contrôleur](controller-methods-views.md)
[Suivant - Ajouter un champ](new-field.md)
