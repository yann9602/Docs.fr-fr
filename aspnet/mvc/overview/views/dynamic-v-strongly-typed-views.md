---
uid: mvc/overview/views/dynamic-v-strongly-typed-views
title: "Dynamique v. Que les vues fortement typées | Documents Microsoft"
author: Rick-Anderson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/27/2011
ms.topic: article
ms.assetid: 0cbd88da-0da6-4605-b222-2835c6478304
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/views/dynamic-v-strongly-typed-views
msc.type: authoredcontent
ms.openlocfilehash: 8a96d43e04a0a50d5176c10c26aa49918a0e56ef
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="dynamic-v-strongly-typed-views"></a>Dynamique v. Vues fortement typées
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

Il existe trois façons de passer des informations à partir d’un contrôleur à une vue dans ASP.NET MVC 3 :

1. En tant qu’objet modèle fortement typé.
2. En tant que type dynamique (à l’aide de @model dynamique)
3. À l’aide de l’élément ViewBag

J’ai écrit une application MVC 3 haut Blog simple pour comparer des vues dynamiques et fortement typées. Le contrôleur démarre avec une simple liste de blogs :

[!code-csharp[Main](dynamic-v-strongly-typed-views/samples/sample1.cs)]

Cliquez avec le bouton droit dans la méthode IndexNotStonglyTyped() et ajouter une vue Razor.

[![8475.NotStronglyTypedView [1]](dynamic-v-strongly-typed-views/_static/image2.png)](dynamic-v-strongly-typed-views/_static/image1.png)

Assurez-vous que le **créer une vue fortement typée** case n’est pas activée. La vue résultante ne contient pas une grande partie :

[!code-cshtml[Main](dynamic-v-strongly-typed-views/samples/sample2.cshtml)]

[!code-cshtml[Main](dynamic-v-strongly-typed-views/samples/sample3.cshtml)]

Étant donné que nous utilisons un dynamique et non une vue fortement typée, intellisense ne nous aider à. Le code complet est indiqué ci-dessous :

[!code-cshtml[Main](dynamic-v-strongly-typed-views/samples/sample4.cshtml)]

[![6646.NotStronglyTypedView_5F00_IE [1]](dynamic-v-strongly-typed-views/_static/image4.png)](dynamic-v-strongly-typed-views/_static/image3.png)

Maintenant, nous allons ajouter une vue fortement typée. Ajoutez le code suivant pour le contrôleur :

[!code-csharp[Main](dynamic-v-strongly-typed-views/samples/sample5.cs)]


Notez qu’il est exactement le View(topBlogs) retour même ; appeler en tant que la vue non fortement typée. Cliquez avec le bouton droit à l’intérieur de *StonglyTypedIndex()* et sélectionnez **ajouter une vue**. Cette fois-ci, sélectionnez le **Blog** classe de modèle et sélectionnez **liste** que le modèle de vue de structure.

[![5658.StrongView [1]](dynamic-v-strongly-typed-views/_static/image6.png)](dynamic-v-strongly-typed-views/_static/image5.png)

Dans le modèle d’affichage, nous obtenons prise en charge intellisense.

[![7002.intellesince [1]](dynamic-v-strongly-typed-views/_static/image8.png)](dynamic-v-strongly-typed-views/_static/image7.png)

Le projet c# peut être téléchargé [ici](https://blogs.msdn.com/cfs-file.ashx/__key/CommunityServer-Blogs-Components-WeblogFiles/00-00-01-11-73-SSMS/1817.Mvc3ViewDemo.zip).
