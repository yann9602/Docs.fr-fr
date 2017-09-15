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

Vous pouvez renommer rapidement le paramètre`searchString` en `id` à l’aide de la commande **Renommer**. Cliquez avec le bouton droit sur `searchString` **> Renommer**.

![Menu contextuel](search/_static/rename.png)

Les cibles de renommage sont mises en surbrillance.

![Éditeur de code présentant la variable mise en surbrillance à l’aide de la méthode Index ActionResult](search/_static/rename2.png)

Remplacez le paramètre par `id` et toutes les occurrences de `searchString` par `id`.

![Éditeur de code montrant que la variable a été remplacée par id](search/_static/rename3.png)

[!INCLUDE[adding-model](../../includes/mvc-intro/search2.md)]

Notez comment IntelliSense nous permet de mettre à jour la balise.

![Menu contextuel IntelliSense avec « method » sélectionné dans la liste des attributs de l’élément form](search/_static/int_m.png)

![Menu contextuel IntelliSense avec « get » sélectionné dans la liste des valeurs d’attribut de « method »](search/_static/int_get.png)

Notez la police distinctive dans la balise `<form>`. Cette police distinctive indique que la balise est prise en charge par les [Tag Helpers](../../mvc/views/tag-helpers/intro.md).

![balise form avec du texte en violet](search/_static/th_font.png)

[!INCLUDE[adding-model](../../includes/mvc-intro/search3.md)]

>[!div class="step-by-step"]
[Précédent](controller-methods-views.md)
[Suivant](new-field.md)  
