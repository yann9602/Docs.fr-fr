---
title: "Configurer la localisation d’un objet portable"
author: sebastienros
description: "Cet article présente les fichiers objets Portable et décrit les étapes permettant de les utiliser dans une application ASP.NET Core avec l’infrastructure de base de Orchard."
keywords: ASP.NET Core, localisation, culture, langue, objet portable
ms.author: scaddie
manager: wpickett
ms.date: 09/26/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/portable-object-localization
ms.openlocfilehash: e563cec0878a86d6c6031cfae4cde61a753486fa
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2017
---
# <a name="configure-portable-object-localization-with-orchard-core"></a>Configurer la localisation d’un objet portable avec Orchard de base

Par [Sébastien Ros](https://github.com/sebastienros) et [Scott Addie](https://twitter.com/Scott_Addie)

Cet article décrit les étapes pour l’utilisation des fichiers d’objet Portable (PO) dans une application ASP.NET Core avec la [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.

**Remarque :** Orchard Core n’est pas un produit Microsoft. Par conséquent, Microsoft ne fournit aucune prise en charge pour cette fonctionnalité.

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization)

## <a name="what-is-a-po-file"></a>Qu’est un fichier de bon de commande ?

Les fichiers de bon de commande sont distribués sous forme de fichiers texte contenant les chaînes traduites pour une langue donnée. Certains avantages de l’utilisation de fichiers de bon de commande à la place *.resx* fichiers incluent :
- Fichiers de bon de commande prennent en charge la pluralisation ; *.resx* fichiers ne prennent pas en charge pluralisation.
- Fichiers de bon de commande ne sont pas compilés comme *.resx* fichiers. Par conséquent, des étapes de build et outils que ceux spécialisées ne sont pas nécessaires.
- Fichiers de bon de commande fonctionnent correctement avec les outils d’édition en ligne de collaboration.

### <a name="example"></a>Exemple

Voici un exemple de fichier de bon de commande contenant la traduction de deux chaînes en Français, y compris l’un avec sa forme au pluriel :

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

Cet exemple utilise la syntaxe suivante :

- `#:`: Un commentaire qui indique le contexte de la chaîne à convertir. La même chaîne peut-être être traduite différemment selon l’endroit où il est utilisé.
- `msgid`: Chaîne non traduite.
- `msgstr`: La chaîne traduite.

Dans le cas de prise en charge de la pluralisation, plusieurs entrées peuvent être définies.

- `msgid_plural`: La chaîne au pluriel non traduite.
- `msgstr[0]`: La chaîne traduite pour le cas de 0.
- `msgstr[N]`: La chaîne traduite pour le n de cas.

La spécification de fichier de bon de commande se trouve [ici](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).

## <a name="configuring-po-file-support-in-aspnet-core"></a>Configuration de prise en charge du fichier de bon de commande dans ASP.NET Core

Cet exemple est basé sur une application ASP.NET MVC de base générée à partir d’un modèle de projet Visual Studio 2017.

### <a name="referencing-the-package"></a>Référencement du package

Ajoutez une référence à la `OrchardCore.Localization.Core` package NuGet. Il est disponible sur [MyGet](https://www.myget.org/) à la source du package suivant : https://www.myget.org/F/orchardcore-preview/api/v3/index.json

Le *.csproj* fichier contient maintenant une ligne semblable à celle-ci (numéro de version peut varier) :

[!code-xml[Main](localization/sample/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a>Inscription du service

Ajouter les services requis pour le `ConfigureServices` méthode *Startup.cs*:

[!code-csharp[Main](localization/sample/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

Ajouter l’intergiciel (middleware) requis pour le `Configure` méthode *Startup.cs*:

[!code-csharp[Main](localization/sample/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

Ajoutez le code suivant à votre affichage Razor de choix. *About.cshtml* est utilisé dans cet exemple.

[!code-cshtml[Main](localization/sample/POLocalization/Views/Home/About.cshtml)]

Un `IViewLocalizer` instance est injecté et utilisé pour convertir le texte « Hello world ! ».

### <a name="creating-a-po-file"></a>Création d’un fichier de bon de commande

Créez un fichier nommé  *<culture code>.po* dans votre dossier racine. Dans cet exemple, le nom de fichier est *fr.po* , car la langue Français est utilisée :

[!code-text[Main](localization/sample/POLocalization/fr.po)]

Ce fichier contient la chaîne à traduire et la chaîne traduite en Français. Traductions rétablir leur culture parent, si nécessaire. Dans cet exemple, le *fr.po* fichier est utilisé si la culture demandée est `fr-FR` ou `fr-CA`.

### <a name="testing-the-application"></a>Test de l'application

Exécutez votre application et accédez à l’URL `/Home/About`. Le texte **Hello world !** s’affiche.

Accédez à l’URL `/Home/About?culture=fr-FR`. Le texte **Bonjour le monde !** s’affiche.

## <a name="pluralization"></a>Pluralisation

Fichiers de bon de commande prend en charge les formulaires de pluralisation, ce qui est utile lorsque la même chaîne doit être traduites différemment selon une cardinalité. Cette tâche est effectuée compliquée par le fait que chaque langage définit des règles personnalisées pour sélectionner la chaîne à utiliser en fonction de la cardinalité.

Le package Orchard localisation fournit une API pour appeler ces différentes formes au pluriel automatiquement.

### <a name="creating-pluralization-po-files"></a>Création de fichiers de bon de commande de pluralisation

Ajoutez le contenu suivant à mentionnées précédemment *fr.po* fichier :

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

Consultez [qu’est un fichier de bon de commande ?](#what-is-a-po-file) pour une explication de ce que représente chaque entrée dans cet exemple.

### <a name="adding-a-language-using-different-pluralization-forms"></a>Ajout d’une langue à l’aide de formulaires de pluralisation différents

Les chaînes en anglais et Français ont été utilisés dans l’exemple précédent. Anglais et le Français ont uniquement deux formes de pluralisation et partagent les mêmes règles de formulaire, c'est-à-dire qu’une cardinalité de 1 est mappée à la première forme au pluriel. N’importe quel autre cardinalité est mappée à la deuxième forme au pluriel.

Pas toutes les langues partagent les mêmes règles. Cela est illustré par la langue tchèque, ce qui a trois formes au pluriel.

Créer le `cs.po` de fichiers comme suit, puis notez comment la pluralisation a besoin de trois des traductions différentes :

[!code-text[Main](localization/sample/POLocalization/cs.po)]

Pour accepter les localisations tchèques, ajoutez `"cs"` à la liste des cultures prises en charge dans les `ConfigureServices` méthode :

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

Modifier la *Views/Home/About.cshtml* fichier pour restituer des chaînes localisées, au pluriel pour plusieurs cardinalités :

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

**Remarque :** dans un scénario réel, une variable est utilisée pour représenter le nombre. Ici, nous répéter le même code avec trois valeurs différentes pour exposer un cas très spécifique.

En basculant les cultures, vous consultez les rubriques suivantes :

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

Notez que pour la culture en tchèque, les trois traductions sont différentes. Les cultures anglais et Français partagent la même construction pour les deux dernières chaînes traduites.

## <a name="advanced-tasks"></a>Tâches avancées

### <a name="contextualizing-strings"></a>CONTEXTUALISATION des chaînes

Les applications contiennent souvent les chaînes doivent être converties à plusieurs endroits. La même chaîne peut-être avoir une traduction différents dans certains emplacements au sein d’une application (vues Razor ou des fichiers de classe). Un fichier de bon de commande prend en charge la notion d’un contexte de fichier, qui peut être utilisé pour classer la chaîne qui est représentée. À l’aide d’un contexte de fichier, une chaîne peut être traduite différemment, selon le contexte du fichier (ou l’absence d’un contexte de fichier).

Les services de localisation de bon de commande utilisent le nom de la classe complète ou la vue qui est utilisée lors de la conversion d’une chaîne. Cela est accompli en définissant la valeur sur la `msgctxt` entrée.

Considérez un ajout mineur précédemment *fr.po* exemple. Une vue Razor situé *Views/Home/About.cshtml* peut être défini comme le contexte du fichier en définissant la réservé `msgctxt` valeur de l’écriture :

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

Avec la `msgctxt` définie en tant que tel, la traduction de texte se produit lorsque vous naviguerez vers `/Home/About?culture=fr-FR`. La conversion ne se produit pas lorsque vous naviguerez vers `/Home/Contact?culture=fr-FR`.

Lorsqu’aucune entrée spécifique n’est mis en correspondance avec un contexte de fichier donné, mécanisme de secours de noyaux Orchard recherche un fichier de bon de commande approprié sans contexte. En supposant qu’il n’existe aucun contexte de fichier spécifique définie pour *Views/Home/Contact.cshtml*, la navigation vers `/Home/Contact?culture=fr-FR` charge un fichier de bon de commande tels que :

[!code-text[Main](localization/sample/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>Modification de l’emplacement des fichiers de bon de commande

L’emplacement par défaut des fichiers de bon de commande peut être modifié dans `ConfigureServices`:

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

Dans cet exemple, les fichiers de bon de commande sont chargés à partir de la *localisation* dossier.

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>Implémenter une logique personnalisée pour rechercher des fichiers de localisation

Lorsqu’une logique plus complexe est nécessaire pour localiser les fichiers de bon de commande, le `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface peut être implémentée et enregistré en tant que service. Cela est utile lorsque les fichiers de bon de commande peuvent être stockés dans des emplacements différents ou lorsque les fichiers doivent se trouver dans une hiérarchie de dossiers.

### <a name="using-a-different-default-pluralized-language"></a>À l’aide d’une autre langue par défaut pluralisé

Le package inclut un `Plural` méthode d’extension qui est spécifique à deux formes au pluriel. Pour les langues nécessitant plus pluriel, créez une méthode d’extension. Avec une méthode d’extension, vous n’aurez pas à fournir n’importe quel fichier de localisation pour la langue par défaut &mdash; les chaînes d’origine sont déjà disponibles directement dans le code.

Vous pouvez utiliser le plus générique `Plural(int count, string[] pluralForms, params object[] arguments)` surcharge qui accepte un tableau de chaînes de traductions.