---
title: "Programmes d’assistance de balise dans ASP.NET Core"
author: rick-anderson
description: "Découvrez ce que sont les programmes d’assistance de balise et comment les utiliser dans ASP.NET Core."
keywords: "ASP.NET Core, les programmes d’assistance de balise"
ms.author: riande
manager: wpickett
ms.date: 7/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/tag-helpers/intro
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 10471210075dc8a5366b7d5170d6594c2e66ce94
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="introduction-to-tag-helpers-in-aspnet-core"></a>Introduction aux applications d’assistance de balise dans ASP.NET Core 

De [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-are-tag-helpers"></a>Quels sont les programmes d’assistance de balise ?

Programmes d’assistance de balise permettent au code côté serveur participer à la création et le rendu des éléments HTML dans les fichiers Razor. Par exemple, la fonction intégrée `ImageTagHelper` peut ajouter un numéro de version pour le nom de l’image. Chaque modification de l’image, le serveur génère une nouvelle version unique pour l’image, pour les clients sont assurées de bénéficier de l’image actuelle (au lieu d’une image mise en cache obsolète). Il existe de nombreuses applications auxiliaires balise intégrés pour les tâches courantes - telles que la création de formulaires, des liens, des ressources de chargement et plus - et encore plus disponibles dans les référentiels GitHub publics et comme NuGet packages. Programmes d’assistance de balise sont créés en c#, et ils conviennent à des éléments HTML en fonction de la balise parente, nom d’attribut ou nom de l’élément. Par exemple, la fonction intégrée `LabelTagHelper` peuvent cibler HTML `<label>` élément lorsque la `LabelTagHelper` les attributs sont appliqués. Si vous êtes familiarisé avec [programmes d’assistance HTML](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), programmes d’assistance de balise de réduire les transitions explicites entre HTML et c# dans les vues Razor. Dans de nombreux cas, les programmes d’assistance HTML fournissent une approche alternative à une application d’assistance de balise spécifique, mais il est important de reconnaître que les programmes d’assistance de balise ne remplacent pas les programmes d’assistance HTML et n’est pas un programme d’assistance de balise pour chaque programme d’assistance HTML. [Programmes d’assistance par rapport aux programmes d’assistance HTML de la balise](#tag-helpers-compared-to-html-helpers) explique les différences de plus en détail.

## <a name="what-tag-helpers-provide"></a>Ce qui fournissent des programmes d’assistance de balise

**Une expérience de développement HTML convivial** pour la plupart des cas, balisage Razor à l’aide de programmes d’assistance de balise ressemble à HTML standard. Concepteurs frontales familiarisés avec HTML, CSS et JavaScript peuvent modifier Razor sans en apprendre la syntaxe Razor c#.

**Un environnement IntelliSense riche pour la création d’un balisage HTML et Razor** il s’agit en revanche aigu pour les programmes d’assistance HTML, l’approche précédente côté serveur à la création du balisage dans les vues Razor. [Programmes d’assistance par rapport aux programmes d’assistance HTML de la balise](#tag-helpers-compared-to-html-helpers) explique les différences de plus en détail. [Prise en charge IntelliSense pour les programmes d’assistance de balise](#intellisense-support-for-tag-helpers) explique l’environnement d’IntelliSense. Même les développeurs expérimentés avec syntaxe Razor c# sont plus productifs, à l’aide de programmes d’assistance de balise à écrire la balise de C# Razor.

**Un moyen de vous rendre plus productif et plus en mesure de présenter plus robuste et fiable et du code à l’aide des informations disponibles uniquement sur le serveur** , par exemple, historiquement la règle de mise à jour des images a été pour modifier le nom de l’image lorsque vous modifiez l’image. Les images doivent être mises en cache agressive pour des raisons de performances, et sauf si vous modifiez le nom d’une image, vous risquez de clients obtiennent une copie obsolète. Historiquement, après qu’une image a été modifiée, le nom devait être modifiée et chaque référence à l’image dans l’application web est nécessaire pour mettre à jour. Non seulement c’est très main-d'œuvre qu'intensif, il est également sujette aux erreurs (vous a oublié une référence, accidentellement Entrez la chaîne incorrecte, etc.) La fonction intégrée `ImageTagHelper` peut faire cela pour vous automatiquement. Le `ImageTagHelper` peut ajouter une version numéro pour le nom de l’image, pour chaque modification de l’image, le serveur génère automatiquement une nouvelle version unique pour l’image. Les clients sont garanties pour obtenir de l’image actuelle. Cette robustesse et main-d'œuvre économies essentiellement libre à l’aide de la `ImageTagHelper`.

La plupart des programmes d’assistance de balise intégrée cible des éléments HTML existants et fournit des attributs de côté serveur pour l’élément. Par exemple, le `<input>` élément utilisé dans la plupart des affichages dans le *Views/Account* dossier contient le `asp-for` attribut, qui extrait le nom de la propriété de modèle spécifié dans le code HTML restitué. Le balisage de Razor suivant :

```html
<label asp-for="Email"></label>
```

Génère le code HTML suivant :

```html
<label for="Email">Email</label>
```

Le `asp-for` attribut rendue disponible par le `For` propriété dans le `LabelTagHelper`. Consultez [de création de programmes d’assistance balise](authoring.md) pour plus d’informations.

## <a name="managing-tag-helper-scope"></a>La gestion d’étendue de l’application d’assistance de balise

Étendue des programmes d’assistance de balise est contrôlée par une combinaison de `@addTagHelper`, `@removeTagHelper`et le « ! » annulations caractère.

<a name=add-helper-label></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a>`@addTagHelper`disposition des programmes d’assistance de balise

Si vous créez une application web ASP.NET Core nommée *AuthoringTagHelpers* (avec aucune authentification), ce qui suit *Views/_ViewImports.cshtml* fichier sera ajouté à votre projet :

[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

Le `@addTagHelper` directive rend les programmes d’assistance de balise disponibles à la vue. Dans ce cas, le fichier de vue est *Views/_ViewImports.cshtml*, qui par défaut est hérité par tous les fichiers de vue dans le *vues* dossier et les sous-répertoires ; disposition des programmes d’assistance de balise. Le code ci-dessus utilise la syntaxe des caractères génériques («\*») pour spécifier que tous les programmes d’assistance de balise dans l’assembly spécifié (*Microsoft.AspNetCore.Mvc.TagHelpers*) sera disponible pour tous les fichiers de vue du *vues* répertoire ou sous-répertoire. Le premier paramètre après `@addTagHelper` spécifie les programmes d’assistance de balise à charger (nous utilisons «\*» pour tous les programmes d’assistance de balise), et le deuxième paramètre « Microsoft.AspNetCore.Mvc.TagHelpers » Spécifie l’assembly qui contient les programmes d’assistance de balise. *Microsoft.AspNetCore.Mvc.TagHelpers* est l’assembly pour les programmes d’assistance de la balise de base intégrés ASP.NET.

Pour exposer tous les programmes d’assistance de balise dans ce projet (ce qui crée un assembly nommé *AuthoringTagHelpers*), vous utilisez ce qui suit :

[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

Si votre projet contient un `EmailTagHelper` avec l’espace de noms par défaut (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), vous pouvez fournir le nom qualifié complet (FQN) de l’application d’assistance de balise :

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [3]}} -->

```html
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

Pour ajouter un programme d’assistance de balise à une vue à l’aide d’un FQN, vous ajoutez d’abord le FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) et le nom d’assembly (*AuthoringTagHelpers*). La plupart des développeurs préfèrent utiliser le «\*« syntaxe des caractères génériques. La syntaxe de caractère générique vous permet d’insérer le caractère générique «\*» en guise de suffixe dans une FQN. Par exemple, une des directives suivantes s’affiche le `EmailTagHelper`:

```csharp
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

Comme mentionné précédemment, l’ajout le `@addTagHelper` directive pour le *Views/_ViewImports.cshtml* fichier à disposition du programme d’assistance de balise pour afficher tous les fichiers dans le *vues* répertoires et sous-répertoires. Vous pouvez utiliser la `@addTagHelper` directive dans les fichiers de vue spécifique si vous souhaitez participer à l’exposition de l’application d’assistance de balise pour uniquement ces vues.

<a name=remove-razor-directives-label></a>

### <a name="removetaghelper-removes-tag-helpers"></a>`@removeTagHelper`Supprime les programmes d’assistance de balise

Le `@removeTagHelper` a les deux mêmes paramètres que `@addTagHelper`, et il supprime une application d’assistance de balise a été précédemment ajoutée. Par exemple, `@removeTagHelper` appliqué à un supprime une vue spécifique du programme d’assistance de balise spécifié à partir de la vue. À l’aide de `@removeTagHelper` dans un *Views/Folder/_ViewImports.cshtml* fichier supprime l’application d’assistance de balise spécifié à partir de toutes les vues dans *dossier*.

### <a name="controlling-tag-helper-scope-with-the-viewimportscshtml-file"></a>Contrôle de la portée d’assistance de balise avec le *_ViewImports.cshtml* fichier

Vous pouvez ajouter un *_ViewImports.cshtml* à n’importe quel dossier de la vue et la vue moteur applique les directives de deux ce fichier et le *Views/_ViewImports.cshtml* fichier. Si vous avez ajouté un vide *Views/Home/_ViewImports.cshtml* du fichier pour le *accueil* vues, il ne serait aucune modification car la *_ViewImports.cshtml* fichier est additif. N’importe quel `@addTagHelper` directives vous ajoutez à la *Views/Home/_ViewImports.cshtml* fichier (qui ne sont pas dans la valeur par défaut *Views/_ViewImports.cshtml* fichier) expose ces programmes d’assistance de balise aux vues uniquement dans les *Accueil* dossier.

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a>Refus des éléments individuels

Vous pouvez désactiver un programme d’assistance de balise au niveau de l’élément avec le caractère d’annulations d’assistance de balise (« ! »). Par exemple, `Email` validation est désactivée dans le `<span>` avec le caractère d’annulations d’assistance de balise :

```csharp
<!span asp-validation-for="Email" class="text-danger"></!span>
```

Vous devez appliquer le caractère d’annulations d’assistance de balise à l’ouverture et la balise de fermeture. (L’éditeur Visual Studio ajoute automatiquement le caractère d’exclusion à la balise de fermeture lorsque vous ajoutez une balise d’ouverture). Après avoir ajouté le caractère de l’annulation d’abonnement, l’élément et les attributs de l’application d’assistance de balise ne s’affichent plus dans une police unique.

<a name=prefix-razor-directives-label></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a>À l’aide de `@tagHelperPrefix` de rendre l’utilisation du programme d’assistance de balise explicite

Le `@tagHelperPrefix` directive vous permet de spécifier une chaîne de préfixe de balise pour activer la prise en charge de l’application d’assistance de balise et de rendre l’utilisation du programme d’assistance de balise explicite. Par exemple, vous pouvez ajouter le balisage suivant à la *Views/_ViewImports.cshtml* fichier :

```html
@tagHelperPrefix th:
```
Dans l’image du code ci-dessous, le préfixe d’assistance de balise est défini sur `th:`, ainsi que les éléments à l’aide du préfixe `th:` prend en charge des programmes d’assistance de balise (compatible d’assistance de balise les éléments ont une police unique). Le `<label>` et `<input>` éléments ont le préfixe d’assistance de balise et sont compatibles d’assistance de balise, lors de la `<span>` n’est pas le cas de l’élément.

![image](intro/_static/thp.png)

Les mêmes règles de hiérarchie qui s’appliquent à `@addTagHelper` s’appliquent également aux `@tagHelperPrefix`.

## <a name="intellisense-support-for-tag-helpers"></a>Prise en charge IntelliSense pour les programmes d’assistance de balise

Lorsque vous créez une application web ASP.NET dans Visual Studio, il ajoute le package NuGet « Microsoft.AspNetCore.Razor.Tools ». C’est le package qui ajoute les outils d’assistance de balise.

Envisagez d’écrire un code HTML `<label>` élément. Dès que vous entrez `<l` dans l’éditeur Visual Studio, IntelliSense affiche les éléments correspondants :

![image](intro/_static/label.png)

Non seulement vous obtenez HTML (aide), mais l’icône (le « @ » symbole avec « <> » dans cette section).

![image](intro/_static/tagSym.png)

identifie l’élément ciblé par les programmes d’assistance de balise. Les éléments HTML purs (tels que le `fieldset`) affichent l’icône « <> ».

Un code HTML pur `<label>` affiche la balise HTML (avec le thème de couleur Visual Studio par défaut) dans une police marron, les attributs en rouge, et les valeurs de l’attribut en bleu.

![image](intro/_static/LableHtmlTag.png)

Une fois que vous entrez `<label`, IntelliSense répertorie les attributs HTML/CSS disponibles et les attributs ciblées de l’application d’assistance de balise :

![image](intro/_static/labelattr.png)

Saisie semi-automatique des instructions IntelliSense vous permet d’entrer la touche tab pour compléter l’instruction avec la valeur sélectionnée :

![image](intro/_static/stmtcomplete.png)

Dès qu’un attribut d’assistance de balise est entré, les polices de balise et d’attribut changent. À l’aide de thème de couleur « Light » ou par défaut Visual Studio « Bleu », la police est en gras violet. Si vous utilisez le thème « Foncé » de la police est bleu-vert en gras. Les images dans ce document ont été effectuées à l’aide de thème par défaut.

![image](intro/_static/labelaspfor2.png)

Vous pouvez entrer le Visual Studio *CompleteWord* contextuel (Ctrl + espace est la [par défaut](https://docs.microsoft.com/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) à l’intérieur des guillemets doubles (« »), et vous êtes maintenant en c#, tout comme vous serait dans une classe c#. IntelliSense affiche toutes les méthodes et propriétés sur le modèle de page. Les méthodes et propriétés sont disponibles, car le type de propriété est `ModelExpression`. Dans l’image ci-dessous, je vais modifier le `Register` vue, donc la `RegisterViewModel` n’est disponible.

![image](intro/_static/intellemail.png)

IntelliSense répertorie les propriétés et méthodes disponibles pour le modèle dans la page. L’environnement riche IntelliSense vous permet de sélectionner la classe CSS :

![image](intro/_static/iclass.png)

![image](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a>Programmes d’assistance de balise par rapport aux programmes d’assistance HTML

Programmes d’assistance de balise attachement à des éléments HTML dans les vues Razor, tandis que [programmes d’assistance HTML](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) sont appelés comme Méthodes entrecoupées avec du code HTML dans les vues Razor. Prenez en compte le balisage suivant Razor, qui crée un nom HTML avec la classe CSS « caption » :

```html
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

Au (`@`) symbole indique Razor correspond au début du code. Les deux paramètres (« FirstName » et « prénom : ») sont des chaînes, par conséquent, [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense) ne peut aider. Le dernier argument :

```html
new {@class="caption"}
```

Un objet anonyme est utilisé pour représenter des attributs. Étant donné que **classe** est un mot clé réservé en c#, vous utilisez le `@` symbole pour forcer c# pour interpréter «@class= » comme un symbole (nom de propriété). Pour un concepteur frontal (quelqu'un familiarisé avec HTML, CSS et JavaScript et d’autres technologies de client, mais pas familiarisés avec c# et Razor), la plupart de la ligne est étranger. La ligne entière doit être créée sans l’aide d’IntelliSense.

À l’aide de la `LabelTagHelper`, le même balisage peut être écrits en tant que :

![image](intro/_static/label2.png)

Avec la version d’assistance de balise, dès que vous entrez `<l` dans l’éditeur Visual Studio, IntelliSense affiche les éléments correspondants :

![image](intro/_static/label.png)

IntelliSense vous permet d’écrire la ligne entière. Le `LabelTagHelper` également par défaut, le contenu de la `asp-for` valeur (« FirstName ») de l’attribut « Prénom » ; Il convertit des propriétés de casse mixte en une phrase composée du nom de propriété avec un espace où se produit chaque nouvelle lettre majuscule. Dans le balisage suivant :

![image](intro/_static/label2.png)

génère :

```html
<label class="caption" for="FirstName">First Name</label>
```

La casse mixte qui utilise la phrase de contenu n’est pas utilisée si vous ajoutez du contenu à la `<label>`. Exemple :

![image](intro/_static/1stName.png)

génère :

```html
<label class="caption" for="FirstName">Name First</label>
```

L’image du code suivant montre la partie de l’écran de la *Views/Account/Register.cshtml* vue Razor généré à partir de la hérité 4.5.x modèle ASP.NET MVC inclus avec Visual Studio 2015.

![image](intro/_static/regCS.png)

L’éditeur Visual Studio affiche un code c# avec un fond gris. Par exemple, le `AntiForgeryToken` programme d’assistance HTML :

```html
@Html.AntiForgeryToken()
```

s’affiche avec un fond gris. La plupart du balisage dans la vue de Registre est c#. Comparer que l’approche équivalente à l’aide de programmes d’assistance de balise :

![image](intro/_static/regTH.png)

Le balisage est beaucoup plus claire et plus facile à lire, modifier et mettre à jour que l’approche de programmes d’assistance HTML. Le code c# est réduit à la valeur minimale qui doit savoir sur le serveur. L’éditeur Visual Studio affiche un balisage ciblé par une application d’assistance de balise dans une police unique.

Prendre en compte la *messagerie* groupe :

[!code-csharp[Main](intro/sample/Register.cshtml?range=12-18)]

Chacun des attributs « asp- » a la valeur « Email », mais « Email » n’est pas une chaîne. Dans ce contexte, « Email » est la propriété d’expression de modèle c# pour le `RegisterViewModel`.

L’éditeur Visual Studio vous permet d’écrire **tous les** du balisage dans l’approche d’assistance de balise de l’écran du Registre, alors que Visual Studio ne fournit aucune aide pour la plupart du code dans l’approche de programmes d’assistance HTML. [Prise en charge IntelliSense pour les programmes d’assistance de balise](#intellisense-support-for-tag-helpers) fournit de détails sur l’utilisation des programmes d’assistance de balise dans l’éditeur Visual Studio.

## <a name="tag-helpers-compared-to-web-server-controls"></a>Programmes d’assistance de balise par rapport aux contrôles serveur Web

* Programmes d’assistance de balise ne possèdent pas l’élément qu'auxquels elles sont associées ; ils participent simplement le rendu de l’élément et le contenu. ASP.NET [des contrôles serveur Web](https://msdn.microsoft.com/library/7698y1f0.aspx) sont déclarés et appelé sur une page.

* [Contrôles serveur Web](https://msdn.microsoft.com/library/zsyt68f1.aspx) ont un cycle de vie non trivial qui facilitent le développement et le débogage difficile.

* Contrôles serveur Web permettent d’ajouter des fonctionnalités aux éléments de modèle DOM (Document Object) de client à l’aide d’un contrôle client. Programmes d’assistance de balise n’ont aucun modèle DOM.

* Contrôles serveur Web incluent la détection automatique du navigateur. Programmes d’assistance de balise ont aucune connaissance du navigateur.

* Plusieurs programmes d’assistance de balise peut agir sur le même élément (consultez [est en conflit en évitant d’assistance de balise](https://docs.microsoft.com/aspnet/core/mvc/views/tag-helpers/authoring#avoiding-tag-helper-conflicts) ) alors que vous ne peut pas généralement composer des contrôles serveur Web.

* Programmes d’assistance de balise peuvent modifier la balise et le contenu des éléments HTML auxquels ils sont étendus, mais ne pas modifier directement rien d’autre sur une page. Contrôles serveur Web ont une portée moins spécifique et peuvent effectuer des actions qui affectent les autres parties de votre page. activation des effets secondaires involontaires.

* Contrôles serveur Web utilisent des convertisseurs de type pour convertir des chaînes en objets. Avec les programmes d’assistance de balise, vous travaillez en mode natif en c#, vous n’avez pas besoin effectuer une conversion de type.

* Utilisation de contrôles serveur Web [System.ComponentModel](https://docs.microsoft.com/dotnet/api/system.componentmodel) pour implémenter le comportement d’exécution et au moment du design des composants et des contrôles. `System.ComponentModel`inclut les classes de base et les interfaces pour l’implémentation des attributs et des convertisseurs de type, sources de liaison aux données et les licences des composants. Comparez cela à des programmes d’assistance de balise, généralement dérivés de `TagHelper`et le `TagHelper` classe de base expose deux méthodes uniquement `Process` et `ProcessAsync`.

## <a name="customizing-the-tag-helper-element-font"></a>Personnalisation de la police d’élément d’assistance de balise

Vous pouvez personnaliser la police et la colorisation de **outils** > **Options** > **environnement** > **polices Couleurs et**:

![image](intro/_static/fontoptions2.png)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Création de Tag Helpers](authoring.md)
* [Utilisation des formulaires](../working-with-forms.md)
* [TagHelperSamples sur GitHub](https://github.com/dpaquette/TagHelperSamples) contient des exemples d’assistance de balise pour l’utilisation des [Bootstrap](http://getbootstrap.com/).

<!--
* [Working with Forms ](xref:mvc/views/working-with-forms)
-->
