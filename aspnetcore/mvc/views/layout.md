---
title: Disposition
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 29f12d1f-9734-48bd-bf1a-cee53a8ab700
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/layout
ms.openlocfilehash: 9a7f140722548b51bbce33d44389ff5646aa6047
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="layout"></a>Disposition

Par [Steve Smith](http://ardalis.com)

Vues partagent fréquemment des éléments visuels et par programmation. Dans cet article, vous allez apprendre à utiliser des dispositions courantes, partager des directives et exécuter le code commun avant le rendu des vues dans votre application ASP.NET.

## <a name="what-is-a-layout"></a>Qu’est une mise en page

La plupart des applications web ont une disposition courante qui offre une expérience cohérente de l’utilisateur lorsqu’ils naviguent de page en page. En général, la mise en page inclut des éléments d’interface utilisateur courantes telles que l’en-tête de l’application, la navigation ou les éléments de menu et un pied de page.

![Exemple de mise en page](layout/_static/page-layout.png)

Structures HTML communes telles que des scripts et des feuilles de style sont fréquemment utilisés par nombre de pages au sein d’une application. Tous ces éléments partagés peuvent être définis dans un *disposition* fichier, qui peut ensuite être référencé par n’importe quelle vue utilisée dans l’application. Dispositions réduisent code en double dans les vues, de les aider à suivre le [ne répétez vous-même (sec) principe](http://deviq.com/don-t-repeat-yourself/).

Par convention, la disposition par défaut pour une application ASP.NET est nommée `_Layout.cshtml`. Le modèle de projet Visual Studio ASP.NET Core MVC inclut ce fichier de mise en page dans le `Views/Shared` dossier :

![dossier d’affichages dans l’Explorateur de solutions](layout/_static/web-project-views.png)

Cette disposition définit un modèle de niveau supérieur pour les vues dans l’application. Les applications ne nécessitent pas une mise en page et applications peuvent définir plusieurs dispositions, avec des vues différentes en spécifiant des dispositions différentes.

Un exemple `_Layout.cshtml`:

[!code-html[Main](../../common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=42,66)]

## <a name="specifying-a-layout"></a>Spécification d’une disposition

Les vues Razor ont un `Layout` propriété. Des vues spécifient une disposition en définissant cette propriété :

[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

La mise en page spécifiée peut utiliser un chemin d’accès complet (exemple : `/Views/Shared/_Layout.cshtml`) ou un nom partiel (exemple : `_Layout`). Lorsqu’un nom partiel est fourni, le moteur d’affichage Razor recherchera le fichier de disposition à l’aide de son processus de découverte standard. Le dossier associé au contrôleur de la recherche est effectuée en premier lieu, suivi par le `Shared` dossier. Ce processus de découverte est identique à celui utilisé pour découvrir les [vues partielles](partial.md).

Par défaut, chaque disposition doit appeler `RenderBody`. Chaque fois que l’appel à `RenderBody` est placé, le contenu de la vue est restitué.

<a name=layout-sections-label></a>

### <a name="sections"></a>Sections

Une disposition peut éventuellement faire référence à un ou plusieurs *sections*, en appelant `RenderSection`. Sections fournissent un moyen d’organiser où certains éléments de la page doivent être placés. Chaque appel à `RenderSection` peut spécifier si cette section est obligatoire ou facultatif. Si une section requise n’est trouvée, une exception est levée. Des vues spécifient le contenu à restituer dans une section à l’aide de la `@section` la syntaxe Razor. Si une vue définit une section, il doit être rendu (ou une erreur se produit).

Un exemple `@section` définition dans une vue :

```html
@section Scripts {
     <script type="text/javascript" src="/scripts/main.js"></script>
   }
   ```

Dans le code ci-dessus, les scripts de validation sont ajoutées à la `scripts` section sur une vue qui inclut un formulaire. Autres vues dans la même application ne nécessitent pas d’autres scripts et donc n’aurez pas besoin de définir une section de scripts.

Les sections définies dans une vue sont disponibles uniquement dans sa page de présentation immédiate. Ils ne peuvent pas être référencés à partir d’autres parties de la vue système, affichage des composants ou aucun.

### <a name="ignoring-sections"></a>Ignorer des sections

Par défaut, le corps et toutes les sections dans une page de contenu doivent tous être rendues par la page de disposition. Le moteur d’affichage Razor applique ceci en effectuant le suivi si le corps et chaque section ont été rendues.

Pour indiquer au moteur de vue d’ignorer le corps ou des sections, appelez le `IgnoreBody` et `IgnoreSection` méthodes.

Le corps et chaque section dans une page Razor doivent être rendus soit ignorés.

<a name=viewimports></a>

## <a name="importing-shared-directives"></a>L’importation des Directives partagés

Vues peuvent utiliser des directives de Razor pour effectuer diverses opérations, telles que l’importation d’espaces de noms ou d’effectuer [injection de dépendance](dependency-injection.md). Directives partagés par plusieurs vues peuvent être spécifiés dans un commun `_ViewImports.cshtml` fichier. Le `_ViewImports` fichier prend en charge les directives suivantes :

* `@addTagHelper`

* `@removeTagHelper`

* `@tagHelperPrefix`

* `@using`

* `@model`

* `@inherits`

* `@inject`

Le fichier ne prend pas en charge les autres fonctionnalités Razor, telles que les fonctions et les définitions de la section.

Un exemple `_ViewImports.cshtml` fichier :

[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

Le `_ViewImports.cshtml` de fichiers pour une application ASP.NET MVC de base est généralement placée dans le `Views` dossier. A `_ViewImports.cshtml` fichier peut être placé dans un dossier dans lequel cas seront uniquement appliquée aux vues dans ce dossier et ses sous-dossiers. `_ViewImports`les fichiers sont traités en commençant au niveau racine, et ensuite pour chaque dossier jusqu'à l’emplacement de la vue proprement dite, donc les paramètres spécifiés au niveau racine peut être remplacée au niveau du dossier.

Par exemple, si un niveau racine `_ViewImports.cshtml` fichier spécifie `@model` et `@addTagHelper`et un autre `_ViewImports.cshtml` fichier dans le dossier associé au contrôleur de la vue spécifie une autre `@model` et ajoute un autre `@addTagHelper`, la vue aura accès à ces deux programmes d’assistance de balise et utilisera ce dernier `@model`.

Si plusieurs `_ViewImports.cshtml` fichiers sont exécutées pour une vue, combinées de comportement des directives inclus dans le `ViewImports.cshtml` fichiers seront comme suit :

* `@addTagHelper`, `@removeTagHelper`: tous s’exécuter dans l’ordre

* `@tagHelperPrefix`: celui le plus proche à la vue remplace les autres noms

* `@model`: celui le plus proche à la vue remplace les autres noms

* `@inherits`: celui le plus proche à la vue remplace les autres noms

* `@using`: toutes les sont incluses ; les doublons sont ignorés.

* `@inject`: pour chaque propriété, celui le plus proche à la vue remplace les autres noms portant le même nom de propriété

<a name=viewstart></a>

## <a name="running-code-before-each-view"></a>Exécution du Code avant chaque vue.

Si vous avez un code que vous devez exécuter avant chaque vue, cela doit être placé dans le `_ViewStart.cshtml` fichier. Par convention, le `_ViewStart.cshtml` fichier se trouve dans le `Views` dossier. Les instructions figurant dans `_ViewStart.cshtml` sont exécutés avant chaque vue complète (pas de dispositions et les vues partielles pas). Comme [ViewImports.cshtml](xref:mvc/views/layout#viewimports), `_ViewStart.cshtml` est hiérarchique. Si un `_ViewStart.cshtml` fichier est défini dans le dossier de la vue associée au contrôleur, il sera exécuté après celui défini dans la racine de la `Views` dossier (le cas échéant).

Un exemple `_ViewStart.cshtml` fichier :

[!code-html[Main](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

Le fichier ci-dessus spécifie que toutes les vues utilisent le `_Layout.cshtml` mise en page.

> [!NOTE]
> Ni `_ViewStart.cshtml` ni `_ViewImports.cshtml` sont généralement placés dans le `/Views/Shared` dossier. Les versions de niveau de l’application de ces fichiers doivent être placées directement dans le `/Views` dossier.
