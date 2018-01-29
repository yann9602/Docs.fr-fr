---
title: "Configurer la localisation avec des fichiers portable object"
author: sebastienros
description: "Cet article présente les fichiers Portable Object et décrit les étapes permettant de les utiliser dans une application ASP.NET Core avec le framework Orchard Core."
ms.author: scaddie
manager: wpickett
ms.date: 09/26/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/portable-object-localization
ms.openlocfilehash: ad68c8a7df5a8ea0f7ef42137c29cd3b37657052
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="configure-portable-object-localization-with-orchard-core"></a>Configurer la localisation avec des fichiers Portable Object à l'aide d'Orchard Core

Par [Sébastien Ros](https://github.com/sebastienros) et [Scott Addie](https://twitter.com/Scott_Addie)

Cet article décrit les étapes pour l’utilisation des fichiers Portable Object (PO) dans une application ASP.NET Core avec le framework [Orchard Core](https://github.com/OrchardCMS/OrchardCore).

**Remarque :** Orchard Core n’est pas un produit Microsoft. Par conséquent, Microsoft ne fournit aucune prise en charge pour cette fonctionnalité.

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample))

## <a name="what-is-a-po-file"></a>Qu’est un fichier PO ?

Les fichiers PO sont distribués sous forme de fichiers texte contenant les chaînes traduites pour une langue donnée. Il ya certains avantages à utiliser des fichiers PO à la place de fichiers *.resx* comme ceux-ci :
- Les fichiers PO prennent en charge la pluralisation; Les fichiers *.resx* ne prennent pas en charge pluralisation.
- Les fichiers PO ne sont pas compilés comme les fichiers *.resx*. Par conséquent, des étapes de build et des outils spécialisés ne sont pas nécessaires.
- Les fichiers PO fonctionnent correctement avec les outils d’édition en ligne collaboratifs.

### <a name="example"></a>Exemple

Voici un exemple de fichier PO contenant la traduction de deux chaînes en Français, y compris l’un avec sa forme au pluriel :

*fr.po*

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

Cet exemple utilise la syntaxe suivante :

- `#:`: Un commentaire qui indique le contexte de la chaîne à convertir. La même chaîne peut-être être traduite différemment selon l’endroit où elle est utilisée.
- `msgid`: La chaîne non traduite.
- `msgstr`: La chaîne traduite.

Dans le cas de la prise en charge de la pluralisation, plusieurs entrées peuvent être définies.

- `msgid_plural`: La chaîne au pluriel non traduite.
- `msgstr[0]`: La chaîne traduite pour le cas 0.
- `msgstr[N]`: La chaîne traduite pour le cas N.

La spécification des fichiers PO se trouve [ici](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).

## <a name="configuring-po-file-support-in-aspnet-core"></a>Configuration de prise en charge des fichiers PO dans ASP.NET Core

Cet exemple est basé sur une application ASP.NET Core MVC générée à partir d’un modèle de projet Visual Studio 2017.

### <a name="referencing-the-package"></a>Référencement du package

Ajoutez une référence au package NuGet `OrchardCore.Localization.Core`. Il est disponible sur [MyGet](https://www.myget.org/) via la source de package suivante : https://www.myget.org/F/orchardcore-preview/api/v3/index.json

Le fichier *.csproj* contient maintenant une ligne semblable à celle-ci (le numéro de version peut varier) :

[!code-xml[Main](localization/sample/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a>Inscription du service

Ajouter les services nécessaires à la méthode `ConfigureServices` de *Startup.cs*:

[!code-csharp[Main](localization/sample/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

Ajouter l’intergiciel (middleware) nécessaire à la méthode `Configure` de *Startup.cs*:

[!code-csharp[Main](localization/sample/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

Ajoutez le code suivant à la vue Razor de votre choix. *About.cshtml* est utilisé dans cet exemple.

[!code-cshtml[Main](localization/sample/POLocalization/Views/Home/About.cshtml)]

Une instance `IViewLocalizer` est injectée et utilisée pour convertir le texte « Hello world ! ».

### <a name="creating-a-po-file"></a>Création d’un fichier PO

Créez un fichier nommé *<culture code>.po* dans votre dossier racine. Dans cet exemple, le nom de fichier est *fr.po* , car la langue Française est utilisée :

[!code-text[Main](localization/sample/POLocalization/fr.po)]

Ce fichier contient la chaîne à traduire et la chaîne traduite en Français. Les traductions se réfèrent à leur culture parente, si nécessaire. Dans cet exemple, le *fr.po* fichier est utilisé si la culture demandée est `fr-FR` ou `fr-CA`.

### <a name="testing-the-application"></a>Test de l'application

Exécutez votre application et accédez à l’URL `/Home/About`. Le texte **Hello world !** s’affiche.

Accédez à l’URL `/Home/About?culture=fr-FR`. Le texte **Bonjour le monde !** s’affiche.

## <a name="pluralization"></a>Pluralisation

Les fichiers PO prennent en charge les formes de pluralisation, ce qui est utile lorsque la même chaîne doit être traduites différemment selon une cardinalité. Cette tâche est rendue compliquée par le fait que chaque langage définit des règles personnalisées pour sélectionner la chaîne à utiliser en fonction de la cardinalité.

Le package Orchard localisation fournit une API pour appeler ces différentes formes plurielles automatiquement.

### <a name="creating-pluralization-po-files"></a>Création de fichiers PO de pluralisation

Ajoutez le contenu suivant au fichier *fr.po* mentionné précédemment :

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

Consultez [qu’est un fichier PO ?](#what-is-a-po-file) pour une explication de ce que représente chaque entrée dans cet exemple.

### <a name="adding-a-language-using-different-pluralization-forms"></a>Ajout d’une langue à l’aide de formulaires de pluralisation différents

Les chaînes en anglais et français ont été utilisés dans l’exemple précédent. L'anglais et le français ont uniquement deux formes de pluralisation et partagent les mêmes règles de formes, c'est-à-dire qu’une cardinalité de 1 est mappée à la première forme au pluriel. N’importe quelle autre cardinalité est mappée à la deuxième forme au pluriel.

Toutes les langues ne partagent pas les mêmes règles. Cela est le cas pour la langue tchèque qui a trois formes plurielles.

Créer le `cs.po` de fichiers comme suit, puis notez comment la pluralisation a besoin de trois traductions différentes :

[!code-text[Main](localization/sample/POLocalization/cs.po)]

Pour accepter les localisations tchèques, ajoutez `"cs"` à la liste des cultures prises en charge dans la méthode `ConfigureServices` :

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

Modifier le fichier *Views/Home/About.cshtml* pour restituer des chaînes localisées, au pluriel pour plusieurs cardinalités :

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

**Remarque :** dans un scénario réel, une variable est utilisée pour représenter le nombre. Ici, nous répétons le même code avec trois valeurs différentes pour exposer un cas très spécifique.

En basculant les cultures, vous consultez les résultats suivants :

Pour `/Home/About`:

```html
There is one item.
There are 2 items.
There are 5 items.
```

Pour `/Home/About?culture=fr`:

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

Pour `/Home/About?culture=cs`:

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

Notez que pour la culture tchèque, les trois traductions sont différentes. Les cultures anglaise et française partagent la même construction pour les deux dernières chaînes traduites.

## <a name="advanced-tasks"></a>Tâches avancées

### <a name="contextualizing-strings"></a>CONTEXTUALISATION des chaînes

Les applications contiennent souvent les chaînes à traduire dans différents endroits. La même chaîne peut-être avoir une traduction différents dans certains emplacements au sein d’une application (des vues Razor ou des fichiers de classe). Un fichier PO prend en charge la notion d’un contexte de fichier, qui peut être utilisé pour classer la chaîne qui est représentée. À l’aide d’un contexte de fichier, une chaîne peut être traduite différemment, selon le contexte du fichier (ou l’absence d’un contexte de fichier).

Les services de localisation PO utilisent le nom de la classe complète ou la vue qui est utilisée lors de la conversion d’une chaîne. Cela est accompli en définissant la valeur sur l'entrée `msgctxt`.

Considérez un ajout mineur à l'exemple *fr.po* précédent. Une vue Razor située dans *Views/Home/About.cshtml* peut être définie comme le contexte du fichier en définissant la valeur de l'entrée `msgctxt`:

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

Avec l'entrée `msgctxt` définie ainsi, la traduction de texte se produit lorsque vous naviguez vers `/Home/About?culture=fr-FR`. La conversion ne se produira pas lorsque vous naviguerez vers `/Home/Contact?culture=fr-FR`.

Lorsqu’aucune entrée spécifique ne correspond à un contexte de fichier donné, le mécanisme de secours d'Orchard Core recherche un fichier PO approprié sans contexte. En supposant qu’il n’existe aucun contexte de fichier spécifique définie pour *Views/Home/Contact.cshtml*, la navigation vers `/Home/Contact?culture=fr-FR` charge un fichier PO tel que celui-ci :

[!code-text[Main](localization/sample/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>Modification de l’emplacement des fichiers PO

L’emplacement par défaut des fichiers PO peut être modifiés dans `ConfigureServices`:

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

Dans cet exemple, les fichiers PO sont chargés à partir du dossier *localisation*.

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>Implémenter une logique personnalisée pour rechercher des fichiers de localisation

Lorsqu’une logique plus complexe est nécessaire pour localiser les fichiers PO, l'interface `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` peut être implémentée et enregistrée en tant que service. Cela est utile lorsque les fichiers PO peuvent être stockés dans des emplacements différents ou lorsque les fichiers doivent se trouver dans une hiérarchie de dossiers.

### <a name="using-a-different-default-pluralized-language"></a>À l’aide d’une autre langue pluralisée par défaut

Le package inclut une méthode d’extension `Plural` qui est spécifique à deux formes au pluriel. Pour les langues nécessitant plus de formes plurielles, créez une méthode d’extension. Avec une méthode d’extension, vous n’aurez pas à fournir tous les fichiers de localisation pour la langue par défaut &mdash; les chaînes d’origine sont déjà disponibles directement dans le code.

Vous pouvez utiliser la surcharge plus générique `Plural(int count, string[] pluralForms, params object[] arguments)` qui accepte un tableau de chaînes de traductions.
