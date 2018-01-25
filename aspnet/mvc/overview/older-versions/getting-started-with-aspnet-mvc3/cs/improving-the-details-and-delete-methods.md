---
uid: mvc/overview/older-versions/getting-started-with-aspnet-mvc3/cs/improving-the-details-and-delete-methods
title: "Améliorer les détails et les méthodes de suppression (c#) | Documents Microsoft"
author: Rick-Anderson
description: "Ce didacticiel, vous allez apprendre les principes fondamentaux de la création d’une application Web ASP.NET MVC à l’aide de Microsoft Visual Web Developer 2010 Express Service Pack 1, qui est en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/12/2011
ms.topic: article
ms.assetid: 3f42edd9-c5b8-4712-9055-970f7d38e350
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-aspnet-mvc3/cs/improving-the-details-and-delete-methods
msc.type: authoredcontent
ms.openlocfilehash: e46616d45ad0e4a0ab861e6fb53f33bc567cbdea
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="improving-the-details-and-delete-methods-c"></a>Améliorer les détails et les méthodes de suppression (c#)
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> > [!NOTE]
> > Une version mise à jour de ce didacticiel est disponible [ici](../../../getting-started/introduction/getting-started.md) qui utilise ASP.NET MVC 5 et Visual Studio 2013. Il est plus sécurisé, beaucoup plus simple à suivre et illustre plusieurs fonctionnalités.
> 
> 
> Ce didacticiel, vous allez apprendre les principes fondamentaux de la création d’une application Web ASP.NET MVC à l’aide de Microsoft Visual Web Developer 2010 Express Service Pack 1, qui est une version gratuite de Microsoft Visual Studio. Avant de commencer, assurez-vous que vous avez installé les composants requis répertoriés ci-dessous. Vous pouvez installer tous les en cliquant sur le lien suivant : [Web Platform Installer](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack). Vous pouvez également installer individuellement les conditions préalables à l’aide des liens suivants :
> 
> - [Conditions préalables requises de Visual Studio Web Developer Express SP1](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack)
> - [Mettre à jour des outils ASP.NET MVC 3](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=MVC3)
> - [SQL Server Compact 4.0](https://www.microsoft.com/web/gallery/install.aspx?appid=SQLCE;SQLCEVSTools_4_0)(runtime + outils prennent en charge)
> 
> Si vous utilisez Visual Studio 2010 au lieu de Visual Web Developer 2010, installez les composants requis en cliquant sur le lien suivant : [composants requis de Visual Studio 2010](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=VS2010SP1Pack).
> 
> Un projet de Visual Web Developer avec code source c# est disponible pour accompagner cette rubrique. [Télécharger la version c#](https://code.msdn.microsoft.com/Introduction-to-MVC-3-10d1b098). Si vous préférez Visual Basic, basculez vers le [version Visual Basic](../vb/intro-to-aspnet-mvc-3.md) de ce didacticiel.


Dans cette partie du didacticiel, vous apporterez des améliorations apportées à généré automatiquement `Details` et `Delete` méthodes. Ces modifications ne sont pas obligatoires, mais avec quelques petits extraits de code, vous pouvez facilement améliorer l’application.

## <a name="improving-the-details-and-delete-methods"></a>Améliorer les détails et les méthodes de suppression

Lorsque vous structuré le `Movie` contrôleur, cela fonctionne très bien, mais qui peuvent être effectuées plus robuste avec seulement quelques petites modifications de code généré par ASP.NET MVC.

Ouvrir le `Movie` contrôleur et modifier le `Details` méthode en retournant `HttpNotFound` quand un film n’est pas trouvé. Vous devez également modifier le `Details` pour définir une valeur par défaut pour le code qui lui est passé. (Vous avez apporté des modifications similaires à la `Edit` méthode dans [partie 6](examining-the-edit-methods-and-edit-view.md) de ce didacticiel.) Toutefois, vous devez modifier le type de retour de la `Details` méthode à partir de `ViewResult` à `ActionResult`, car le `HttpNotFound` méthode ne retourne pas une `ViewResult` objet. L’exemple suivant illustre la modification `Details` (méthode).

[!code-csharp[Main](improving-the-details-and-delete-methods/samples/sample1.cs)]

Code tout d’abord vous permettent de rechercher des données à l’aide du `Find` (méthode). Une fonctionnalité de sécurité importante qui nous intégrés à la méthode est que le code vérifie que le `Find` méthode a trouvé un film avant que le code tente de faire quelque chose avec lui. Par exemple, un pirate pourrait induire des erreurs dans le site en modifiant l’URL créée par les liens à partir de `http://localhost:xxxx/Movies/Details/1` à quelque chose comme `http://localhost:xxxx/Movies/Details/12345` (ou une autre valeur qui ne représente pas un film réel). Si vous n’avez pas la vérification d’un film null, cela peut entraîner une erreur de base de données.

De même, la `Delete` et `DeleteConfirmed` méthodes pour spécifier une valeur par défaut pour le paramètre ID et retourner `HttpNotFound` lorsqu’un film n’est pas trouvé. La mise à jour `Delete` méthodes dans le `Movie` contrôleur sont indiquées ci-dessous.

[!code-csharp[Main](improving-the-details-and-delete-methods/samples/sample2.cs)]

Notez que le `Delete` méthode ne supprime pas les données. L’exécution d’une opération de suppression en réponse à une requête GET (ou encore l’exécution d’une opération de modification, d’une opération de création ou de toute autre opération qui modifie des données) génère une faille de sécurité. Pour plus d’informations, consultez le billet de blog de Stephen Walther [ASP.NET MVC Conseil #46 : n’utilisez pas supprimer les liens parce qu’elles créent des failles de sécurité](http://stephenwalther.com/blog/archive/2009/01/21/asp.net-mvc-tip-46-ndash-donrsquot-use-delete-links-because.aspx).

La méthode `HttpPost` qui supprime les données est nommée `DeleteConfirmed` pour donner à la méthode HTTP POST une signature ou un nom unique. Les signatures des deux méthodes sont illustrées ci-dessous :

[!code-csharp[Main](improving-the-details-and-delete-methods/samples/sample3.cs)]

Le common language runtime (CLR) requiert des méthodes surchargées pour avoir une signature unique (même nom, autre liste de paramètres). Toutefois, vous devez ici deux méthodes de suppression : un pour GET--et un pour valider que requièrent la même signature. (Elles doivent toutes les deux accepter un entier unique comme paramètre.)

Pour trier sur ce point, vous pouvez effectuer deux choses. Une consiste à attribuer aux méthodes des noms différents. C’est ce que nous l’avons fait il précédent exemple. Toutefois, elle présente un petit problème : ASP.NET mappe des segments d’une URL à des méthodes d’action par nom. Si vous renommez une méthode, il est probable que le routage ne pourra pas trouver cette méthode. La solution consiste à faire ce que vous voyez dans l’exemple, c’est-à-dire à ajouter l’attribut `ActionName("Delete")` à la méthode `DeleteConfirmed`. Cela exécute efficacement mappage pour le système de routage afin qu’une URL qui inclut */Delete/*un billet demande trouveront le `DeleteConfirmed` (méthode).

Une autre façon d’éviter un problème avec les méthodes qui ont des signatures et des noms identiques est artificiellement modifier la signature de la méthode POST pour inclure un paramètre inutilisé. Par exemple, certains développeurs ajoutent un type de paramètre `FormCollection` qui est passé à la méthode POST et puis simplement n’utilisez pas le paramètre :

[!code-csharp[Main](improving-the-details-and-delete-methods/samples/sample4.cs)]

## <a name="wrapping-up"></a>Pour résumer

Vous avez maintenant une application ASP.NET MVC complète qui stocke les données dans une base de données SQL Server Compact. Vous pouvez créer, lire, mettre à jour, supprimer et rechercher des films.

![](improving-the-details-and-delete-methods/_static/image1.png)

Ce didacticiel de base obtenu vous lancer dans la création de contrôleurs, de les associer à des vues et en passant autour des données codées en dur. Ensuite, vous avez créé et conçu un modèle de données. Entity Framework Code First créé une base de données à partir du modèle de données à la volée, et le système de génération de modèles automatique ASP.NET MVC est généré automatiquement les méthodes d’action et des vues pour les opérations CRUD de base. Ensuite, vous avez ajouté un formulaire de recherche qui permettent aux utilisateurs de rechercher la base de données. Votre modifié la base de données pour inclure une colonne de données, puis mis à jour deux pages pour créer et afficher ces nouvelles données. Vous avez ajouté la validation en marquant le modèle de données avec des attributs à partir de la `DataAnnotations` espace de noms. La validation résultante s’exécute sur le client et sur le serveur.

Si vous souhaitez déployer votre application, il est utile pour le premier test de l’application sur votre serveur IIS 7 local. Vous pouvez utiliser cette [Web Platform Installer](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=ASPNET;) lien pour activer le paramètre IIS pour les applications ASP.NET. Consultez les liens de déploiement suivants :

- [Organigramme de déploiement ASP.NET](https://msdn.microsoft.com/library/dd394698.aspx)
- [L’activation d’IIS 7.x](https://blogs.msdn.com/b/rickandy/archive/2011/03/14/enabling-iis-7-x-on-windows-7-vista-sp1-windows-2008-windows-2008-r2.aspx)
- [Déploiement de projets d’Application Web](https://msdn.microsoft.com/library/dd394698.aspx)

Je maintenant vous encourage à passer à notre niveau intermédiaire [création d’un modèle de données Entity Framework pour une Application ASP.NET MVC](../../../getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md) et [magasin de musique MVC](../../mvc-music-store/mvc-music-store-part-1.md) didacticiels, pour Explorer le [ASP.NET articles sur MSDN](https://msdn.microsoft.com/library/gg416514(VS.98).aspx)et à consulter les vidéos et des ressources sur plusieurs [https://asp.net/mvc](https://asp.net/mvc) pour en savoir plus sur ASP.NET MVC ! Le [forums d’ASP.NET MVC](https://forums.asp.net/1146.aspx) sont idéal pour poser des questions.

Bonne lecture !

— Scott Hanselman ([http://hanselman.com](http://hanselman.com) et [ @shanselman ](http://twitter.com/shanselman) sur Twitter) et Rick Anderson [blogs.msdn.com/rickAndy](https://blogs.msdn.com/rickAndy)

>[!div class="step-by-step"]
[Précédent](adding-validation-to-the-model.md)
