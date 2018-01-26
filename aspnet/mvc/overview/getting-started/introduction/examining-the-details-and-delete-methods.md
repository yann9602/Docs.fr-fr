---
uid: mvc/overview/getting-started/introduction/examining-the-details-and-delete-methods
title: "Examiner les détails et les méthodes de suppression | Documents Microsoft"
author: Rick-Anderson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/26/2015
ms.topic: article
ms.assetid: f1d2a916-626c-4a54-8df4-77e6b9fff355
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/introduction/examining-the-details-and-delete-methods
msc.type: authoredcontent
ms.openlocfilehash: a4e2b075497e08334183519bf8942e4af6f7a727
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="examining-the-details-and-delete-methods"></a>Examiner les détails et les méthodes de suppression
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

[!INCLUDE[Tutorial Note](sample/code-location.md)]

Dans cette partie du didacticiel, vous allez examiner générées automatiquement `Details` et `Delete` méthodes.

## <a name="examining-the-details-and-delete-methods"></a>Examiner les détails et les méthodes de suppression

Ouvrez le `Movie` contrôleur et examiner les `Details` (méthode).

![](examining-the-details-and-delete-methods/_static/image1.png)

[!code-csharp[Main](examining-the-details-and-delete-methods/samples/sample1.cs)]

Le moteur de génération de modèles automatique de MVC qui a créé cette méthode d’action ajoute un commentaire indiquant une requête HTTP qui appelle la méthode. Dans ce cas, il est un `GET` demande avec trois segments d’URL, le `Movies` contrôleur, le `Details` (méthode) et un `ID` valeur.

Code tout d’abord vous permettent de rechercher des données à l’aide du `Find` (méthode). Une fonctionnalité de sécurité importante intégrée à la méthode est que le code vérifie que le `Find` méthode a trouvé un film avant que le code tente de faire quelque chose avec lui. Par exemple, un pirate pourrait induire des erreurs dans le site en modifiant l’URL créée par les liens à partir de `http://localhost:xxxx/Movies/Details/1` à quelque chose comme `http://localhost:xxxx/Movies/Details/12345` (ou une autre valeur qui ne représente pas un film réel). Si vous n’avez pas coché pour un film null, un film null entraînerait une erreur de base de données.

Examinez les méthodes `Delete` et `DeleteConfirmed`.

[!code-csharp[Main](examining-the-details-and-delete-methods/samples/sample2.cs?highlight=17)]

Notez que la `HTTP Get``Delete` méthode ne supprime pas le film spécifié, il retourne une vue de la séquence dans laquelle vous pouvez envoyer (`HttpPost`) la suppression... L’exécution d’une opération de suppression en réponse à une requête GET (ou encore l’exécution d’une opération de modification, d’une opération de création ou de toute autre opération qui modifie des données) génère une faille de sécurité. Pour plus d’informations, consultez le billet de blog de Stephen Walther [ASP.NET MVC Conseil #46 : n’utilisez pas supprimer les liens parce qu’elles créent des failles de sécurité](http://stephenwalther.com/blog/archive/2009/01/21/asp.net-mvc-tip-46-ndash-donrsquot-use-delete-links-because.aspx).

La méthode `HttpPost` qui supprime les données est nommée `DeleteConfirmed` pour donner à la méthode HTTP POST une signature ou un nom unique. Les signatures des deux méthodes sont illustrées ci-dessous :

[!code-csharp[Main](examining-the-details-and-delete-methods/samples/sample3.cs)]

Le Common Language Runtime (CLR) nécessite des méthodes surchargées pour avoir une signature à paramètre unique (même nom de méthode, mais liste de paramètres différentes). Toutefois, vous devez ici deux méthodes de suppression : un pour GET--et un pour valider que les deux ont la même signature de paramètre. (Elles doivent toutes les deux accepter un entier unique comme paramètre.)

Pour trier sur ce point, vous pouvez effectuer deux choses. Une consiste à attribuer aux méthodes des noms différents. C’est ce qu’a fait le mécanisme de génération de modèles automatique dans l’exemple précédent. Toutefois, elle présente un petit problème : ASP.NET mappe des segments d’une URL à des méthodes d’action par nom. Si vous renommez une méthode, il est probable que le routage ne pourra pas trouver cette méthode. La solution consiste à faire ce que vous voyez dans l’exemple, c’est-à-dire à ajouter l’attribut `ActionName("Delete")` à la méthode `DeleteConfirmed`. Cela exécute efficacement mappage pour le système de routage afin qu’une URL qui inclut */Delete/* un billet demande trouveront le `DeleteConfirmed` (méthode).

Une autre méthode pour éviter tout problème avec les méthodes qui ont des signatures et des noms identiques est artificiellement modifier la signature de la méthode POST pour inclure un paramètre inutilisé. Par exemple, certains développeurs ajoutent un type de paramètre `FormCollection` qui est passé à la méthode POST et puis simplement n’utilisez pas le paramètre :

[!code-csharp[Main](examining-the-details-and-delete-methods/samples/sample4.cs)]

## <a name="summary"></a>Récapitulatif

Vous avez maintenant une application ASP.NET MVC complète qui stocke les données dans une base de données de base de données locale. Vous pouvez créer, lire, mettre à jour, supprimer et rechercher des films.

![](examining-the-details-and-delete-methods/_static/image2.png)

## <a name="next-steps"></a>Étapes suivantes

Après avoir généré et testé une application web, l’étape suivante consiste à rendre disponible à d’autres personnes à utiliser sur Internet. Pour ce faire, vous devez déployer vers un fournisseur d’hébergement web. Microsoft propose d’hébergement web gratuit pour jusqu'à 10 sites web dans un [libérer le compte d’évaluation Azure](https://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A443DD604). Je vous suggère ensuite suivre mon didacticiel [déployer une application Secure ASP.NET MVC avec l’appartenance, OAuth et base de données SQL Azure](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data). Un excellent didacticiel est de niveau intermédiaire de Tom Dykstra [création d’un modèle de données Entity Framework pour une Application ASP.NET MVC](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md). [StackOverflow](http://stackoverflow.com/help) et [forums d’ASP.NET MVC](https://forums.asp.net/1146.aspx) sont une bonne place pour poser des questions. Suivez [me](https://twitter.com/RickAndMSFT) sur twitter, vous pouvez obtenir les mises à jour sur mon didacticiels plus récente.

Commentaires sont Bienvenue.

— [Rick Anderson](https://blogs.msdn.com/rickAndy) twitter :[@RickAndMSFT](https://twitter.com/RickAndMSFT)  
— [Scott Hanselman](http://www.hanselman.com/blog/) twitter :[@shanselman](https://twitter.com/shanselman)

>[!div class="step-by-step"]
[Précédent](adding-validation.md)
