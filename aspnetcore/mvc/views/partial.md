---
title: Vues partielles
author: ardalis
description: "À l’aide de vues partielles dans ASP.NET MVC de base"
keywords: "Partielle d’ASP.NET Core, les vues partielles,"
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.assetid: 4be1b12c-b74e-44ff-826b-99ce86e8d464
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/partial
ms.openlocfilehash: 60f5255ca31accbffffec18053b29810977a5ff1
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2017
---
# <a name="partial-views"></a>Vues partielles

Par [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), et [Rick Anderson](https://twitter.com/RickAndMSFT)

Base d’ASP.NET MVC prend en charge les vues partielles sont utiles lorsque vous avez réutilisables pour des pages web que vous souhaitez partager entre différentes vues.

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample))

## <a name="what-are-partial-views"></a>Quelles sont les vues partielles ?

Une vue partielle est une vue qui est rendue dans une autre vue. La sortie HTML générée par l’exécution de la vue partielle est restituée dans la vue appelante (ou parent). Comme les vues, les vues partielles utilisent le *.cshtml* extension de fichier.

## <a name="when-should-i-use-partial-views"></a>Quand dois-je utiliser les vues partielles ?

Les vues partielles sont un moyen efficace de la rupture des vues de grande taille en éléments plus petits. Ils peuvent réduire la duplication d’afficher le contenu et permettent d’afficher les éléments être réutilisées. Les éléments de disposition commune doivent être spécifiés dans [_Layout.cshtml](layout.md). Contenu réutilisable non peut être encapsulé dans les vues partielles.

Si vous avez une page complexe composée de plusieurs parties logiques, il peut être utile travailler avec chaque élément en tant que sa propre vue partielle. Chaque élément de la page peut être affiché en les isolant du reste de la page, et l’affichage de la page elle-même devient beaucoup plus simple, car il contient uniquement la structure de page et les appels pour afficher les vues partielles globale.

Conseil : Suivez le [ne répétez vous-même principe](http://deviq.com/don-t-repeat-yourself/) dans les vues.

## <a name="declaring-partial-views"></a>Déclarer des vues partielles

Les vues partielles sont créés comme toute autre vue : vous créez un *.cshtml* fichier inclus dans le *vues* dossier. Il n’existe aucune différence de sémantique entre une vue partielle et un affichage normal : ils sont rendus simplement différemment. Vous pouvez avoir une vue qui est retournée directement à partir d’un contrôleur de `ViewResult`, et la vue peut être utilisée comme une vue partielle. La principale différence entre le mode de rendu une vue et une vue partielle est que les vues partielles n’exécutent pas *_ViewStart.cshtml* (alors que les vues faire - en savoir plus sur *_ViewStart.cshtml* dans [mise en page ](layout.md)).

## <a name="referencing-a-partial-view"></a>Faisant référence à une vue partielle

À partir d’une page de vue, il existe plusieurs façons dans laquelle vous pouvez restituer une vue partielle. Est la plus simple d’utiliser `Html.Partial`, qui retourne un `IHtmlString` et peuvent être référencées en faisant précéder l’appel avec `@`:

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=9)]

Le `PartialAsync` méthode n’est disponible pour partielle vues contenant du code asynchrone (bien que le code dans les vues est généralement déconseillée) :

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=8)]

Vous pouvez restituer une vue partielle avec `RenderPartial`. Cette méthode ne renvoie aucun résultat ; il diffuse en continu la sortie rendue directement à la réponse. Car il ne renvoie aucun résultat, il doit être appelé dans un bloc de code Razor (vous pouvez également appeler `RenderPartialAsync` si nécessaire) :

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Home/About.cshtml?range=10-12)]

Car il diffuse le résultat directement, `RenderPartial` et `RenderPartialAsync` peut donner de meilleurs résultats dans certains scénarios. Toutefois, dans la plupart des cas, il est recommandé de vous utiliser `Partial` et `PartialAsync`.

> [!NOTE]
> Si vos vues avez besoin d’exécuter du code, le modèle recommandé consiste à utiliser un [composant de vue](view-components.md) au lieu d’une vue partielle.

### <a name="partial-view-discovery"></a>Détection de la vue partielle

Lorsque vous référencez une vue partielle, vous pouvez faire référence à son emplacement de plusieurs façons :

```text
// Uses a view in current folder with this name
// If none is found, searches the Shared folder
@Html.Partial("ViewName")

// A view with this name must be in the same folder
@Html.Partial("ViewName.cshtml")

// Locate the view based on the application root
// Paths that start with "/" or "~/" refer to the application root
@Html.Partial("~/Views/Folder/ViewName.cshtml")
@Html.Partial("/Views/Folder/ViewName.cshtml")

// Locate the view using relative paths
@Html.Partial("../Account/LoginPartial.cshtml")
```

Vous pouvez avoir différentes vues partielles portant le même nom dans les dossiers d’affichage différent. Lorsque vous référencez les vues par nom (sans extension de fichier), des vues dans chaque dossier utilisera la vue partielle dans le même dossier avec eux. Vous pouvez également spécifier une vue partielle de la valeur par défaut à utiliser, placé dans le *Shared* dossier. La vue partielle partagée est utilisée par toutes les vues qui n’ont pas leur propre version de la vue partielle. Vous pouvez avoir une vue partielle par défaut (dans *Shared*), qui est remplacé par une vue partielle portant le même nom dans le même dossier que la vue parent.

Les vues partielles peuvent être *chaînés*. Autrement dit, une vue partielle peut appeler une autre vue partielle (tant que vous ne créez pas une boucle). Dans chaque vue ou une vue partielle, les chemins d’accès relatifs sont toujours par rapport à cette vue de la vue, et pas à la racine ou un parent.

> [!NOTE]
> Si vous déclarez un [Razor](razor.md) `section` dans une vue partielle, il ne sera pas visible pour ses parents ; il se limite à la vue partielle.

## <a name="accessing-data-from-partial-views"></a>L’accès aux données à partir de vues partielles

Lorsqu’une vue partielle est instanciée, elle obtient une copie de la vue parent `ViewData` dictionnaire. Mises à jour apportées aux données dans la vue partielle ne sont pas conservés à la vue parent. `ViewData`modifié dans un partiel vue est perdue lorsque la vue partielle est retournée.

Vous pouvez passer une instance de `ViewDataDictionary` à la vue partielle :

```csharp
@Html.Partial("PartialName", customViewData)
   ```

Vous pouvez également passer un modèle dans une vue partielle. Cela peut être le modèle d’affichage de la page, ou une partie de celui-ci ou un objet personnalisé. Vous pouvez passer à un modèle à `Partial`,`PartialAsync`, `RenderPartial`, ou `RenderPartialAsync`:

```csharp
@Html.Partial("PartialName", viewModel)
   ```

Vous pouvez passer une instance de `ViewDataDictionary` et un modèle d’affichage pour une vue partielle :

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Articles/Read.cshtml?range=15-16)]

Le balisage suivant illustre la *Views/Articles/Read.cshtml* vue qui contient deux vues partielles. La vue partielle deuxième passe dans un modèle et `ViewData` à la vue partielle. Vous pouvez passer de nouveaux `ViewData` dictionnaire tout en conservant existants `ViewData` si vous utilisez la surcharge de constructeur de la `ViewDataDictionary` en surbrillance ci-dessous :

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Articles/Read.cshtml)]

*Vues/Shared/AuthorPartial*:

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Shared/AuthorPartial.cshtml)]

Le *ArticleSection* partielle :

[!code-html[Main](partial/sample/src/PartialViewsSample/Views/Articles/ArticleSection.cshtml)]

Lors de l’exécution, en les gérez est rendus dans la vue parent, qui elle-même est affiché dans le partagé *_Layout.cshtml*

![sortie de la vue partielle](partial/_static/output.png)
