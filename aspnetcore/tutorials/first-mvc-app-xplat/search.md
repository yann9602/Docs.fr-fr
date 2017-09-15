---
title: "Ajout d’une fonctionnalité de recherche"
author: rick-anderson
description: "Montre comment ajouter une fonctionnalité de recherche à une application ASP.NET MVC simple"
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 04/07/2017
ms.topic: get-started-article
ms.assetid: d69e5529-ffff-4628-855d-200206d96269
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app-xplat/search
ms.openlocfilehash: 57419c697aea80a054e906c75002f5a39c512fc6
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
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
