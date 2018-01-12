---
title: Groupement et la minimisation dans ASP.NET Core
author: scottaddie
description: "Découvrez comment optimiser les ressources statiques dans une application de web ASP.NET Core en appliquant le groupement et la minimisation des techniques."
manager: wpickett
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2018
ms.devlang: csharp
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: article
uid: client-side/bundling-and-minification
ms.openlocfilehash: ac8e7fee7600dabb8f4970b5bf87ad7a57ebf17f
ms.sourcegitcommit: 12e5194936b7e820efc5505a2d5d4f84e88eb5ef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="bundling-and-minification"></a>Groupement et minimisation

Par [Scott Addie](https://twitter.com/Scott_Addie)

Cet article explique les avantages de l’application de groupement et la minimisation, y compris comment ces fonctionnalités peuvent être utilisées avec les applications web ASP.NET Core.

## <a name="what-is-bundling-and-minification"></a>Groupement et la minimisation Nouveautés

Regroupement et la minimisation sont deux optimisations de performances distincts que vous pouvez appliquer dans une application web. Utilisées conjointement, groupement et la minimisation améliorent les performances en réduisant le nombre de demandes de serveur et de réduire la taille des actifs statiques demandés.

Groupement et la minimisation principalement améliorent le premier temps de chargement de demande de page. Une fois qu’une page web a été demandée, le navigateur met en cache les ressources statiques (images, CSS et JavaScript). Par conséquent, groupement et la minimisation ne pas améliorer les performances lors de la demande de la même page, ou sur le même site demande les mêmes actifs. Si vous ne définissez pas l’en-tête correctement sur vos éléments multimédias d’expiration, et si vous n’utilisez pas groupement et la minimisation, heuristique d’actualisation du navigateur marque les ressources obsolètes après quelques jours. En outre, le navigateur requiert une demande de validation pour chaque élément multimédia. Dans ce cas, groupement et la minimisation fournissent une amélioration des performances même après la première demande de page.

### <a name="bundling"></a>Regroupement

Regroupement de combine plusieurs fichiers dans un seul fichier. Regroupement réduit le nombre de demandes de serveur qui sont nécessaires pour afficher une ressource web, par exemple une page web. Vous pouvez créer n’importe quel nombre de lots individuels spécifiquement pour CSS, JavaScript, etc. Moins de fichiers signifie moins de demandes HTTP à partir du navigateur sur le serveur ou dans le service de fourniture de votre application. Il en résulte dans amélioration des performances de charge première page.

### <a name="minification"></a>Minimisation

Minimisation supprime les caractères inutiles à partir de code sans modifier les fonctionnalités. Il en résulte une réduction de taille importante de ressources demandées (par exemple, CSS, des images et des fichiers JavaScript). Les effets secondaires communs de minimisation incluent raccourcir les noms de variables pour un caractère et de suppression de commentaires et espaces inutiles.

Observez la fonction JavaScript suivante :

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

Minimisation réduit la fonction à ce qui suit :

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Outre la suppression des commentaires et espaces inutiles, les noms de paramètre et variable suivants ont été renommés comme suit :

D'origine | Affectation d'un nouveau nom
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Impact de groupement et minimisation

Le tableau suivant présente les différences entre individuellement le chargement des ressources et à l’aide de groupement et la minimisation :

Action | B/m | Sans B/M | Modification
--- | :---: | :---: | :---:
Demandes de fichiers  | 7   | 18     | 157%
Ko transféré | 156 | 264.68 | 70%
Temps de chargement (ms) | 885 | 2360   | 167%

Les navigateurs sont assez détaillés en ce qui concerne les en-têtes de requête HTTP. Nombre total d’octets envoyés métrique vu une réduction significative lors du regroupement. Le temps de chargement montre une amélioration significative, toutefois, cet exemple s’exécutait localement. Les gains de performances supérieurs sont réalisés lorsque les ressources à l’aide de groupement et la minimisation transférées sur un réseau.

## <a name="choose-a-bundling-and-minification-strategy"></a>Choisir la stratégie de regroupement et minimisation

Les modèles de projet MVC et les Pages Razor fournissent une solution out of box pour le groupement et la minimisation consistant en un fichier de configuration JSON. Outils tiers, tels que les [Gulp](xref:client-side/using-gulp) et [Grunt](xref:client-side/using-grunt) exécuteurs de tâches, d’accomplir les mêmes tâches avec un peu plus complexe. Un outil tiers est parfait lorsque votre flux de travail de développement requiert le traitement au-delà de groupement et la minimisation&mdash;telles que l’optimisation pelucheux et image. À l’aide de groupement et la minimisation au moment du design, les fichiers réduites sont créés avant le déploiement de l’application. Groupement et réduction avant le déploiement offre l’avantage d’une charge serveur réduite. Toutefois, il est important de noter ce regroupement au moment du design et minimisation augmente la complexité de la build et fonctionne uniquement avec des fichiers statiques.

## <a name="configure-bundling-and-minification"></a>Configurer le regroupement et minimisation

Les modèles de projet MVC et les Pages Razor fournissent une *bundleconfig.json* fichier de configuration qui définit les options pour chaque regroupement. Par défaut, une configuration de lot unique est définie pour le code JavaScript personnalisé (*wwwroot/js/site.js*) et la feuille de style (*wwwroot/css/site.css*) fichiers :

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

Options de configuration sont les suivantes :

* `outputFileName`: Le nom du fichier d’offre groupée de sortie. Peut contenir un chemin d’accès relatif à partir de la *bundleconfig.json* fichier. **Obligatoire**
* `inputFiles`: Un tableau de fichiers à regrouper. Voici les chemins d’accès relatifs au fichier de configuration. **facultatif**, * une valeur vide entraîne un fichier de sortie vide. [la globalisation](http://www.tldp.org/LDP/abs/html/globbingref.html) modèles sont pris en charge.
* `minify`: Options de réduction pour le type de sortie. **facultatif**, *par défaut :`minify: { enabled: true }`*
  * Options de configuration sont disponibles par type de fichier de sortie.
    * [Minimisation CSS](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [Minimisation de JavaScript](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [Minimisation de HTML](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Indicateur indiquant s’il faut ajouter les fichiers générés au fichier projet. **facultatif**, *par défaut : false*
* `sourceMap`: Indicateur précisant s’il faut générer une carte de code source pour le fichier regroupé. **facultatif**, *par défaut : false*
* `sourceMapRootPath`: Le chemin d’accès racine pour stocker le fichier de mappage de source.

## <a name="build-time-execution-of-bundling-and-minification"></a>Exécution du moment de la génération du groupement et minimisation

Le [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) package NuGet permet l’exécution de groupement et minimisation au moment de la génération. Le package injecte [cibles de MSBuild](/visualstudio/msbuild/msbuild-targets) qui exécuté à la génération et l’heure de nettoyage. Le *bundleconfig.json* fichier est analysé par le processus de génération pour générer les fichiers de sortie en fonction de la configuration définie.

> [!NOTE]
> BuildBundlerMinifier appartient à un projet communautaire sur GitHub pour lesquels Microsoft ne fournit aucune prise en charge. Problèmes doivent être classés [ici](https://github.com/madskristensen/BundlerMinifier/issues).

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Ajouter le *BuildBundlerMinifier* package pour votre projet.

Générez le projet. Le message suivant s’affiche dans la fenêtre Sortie :

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>  Minified wwwroot/css/site.min.css
1>  Minified wwwroot/js/site.min.js
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

Nettoyer le projet. Le message suivant s’affiche dans la fenêtre Sortie :

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[CLI .NET Core](#tab/netcore-cli) 

Ajouter le *BuildBundlerMinifier* package pour votre projet :

```console
dotnet add package BuildBundlerMinifier
```

Si vous utilisez ASP.NET Core 1.x, restaurer le package nouvellement ajouté :

```console
dotnet restore
```

Générez le projet :

```console
dotnet build
```

Les options suivantes apparaissent :

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

Nettoyer le projet :

```console
dotnet clean
```

La sortie suivante s’affiche :

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a>Exécution ad hoc de groupement et minimisation

Il est possible d’exécuter les tâches de groupement et la minimisation sur une base ad hoc, sans générer le projet. Ajouter le [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) package NuGet à votre projet :

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> BundlerMinifier.Core appartient à un projet communautaire sur GitHub pour lesquels Microsoft ne fournit aucune prise en charge. Problèmes doivent être classés [ici](https://github.com/madskristensen/BundlerMinifier/issues).

Ce package étend .NET Core CLI pour inclure la *offre groupée-dotnet* outil. La commande suivante peut être exécutée dans la fenêtre de Console de gestionnaire de Package (PMC) ou dans une invite de commandes :

```console
dotnet bundle
```

> [!IMPORTANT]
> Gestionnaire de Package NuGet ajoute les dépendances vers le fichier *.csproj comme `<PackageReference />` nœuds. Le `dotnet bundle` commande est inscrit avec l’interface de ligne de base .NET uniquement lorsqu’un `<DotNetCliToolReference />` nœud est utilisé. Modifiez le fichier *.csproj en conséquence.

## <a name="add-files-to-workflow"></a>Ajouter des fichiers à un flux de travail

Prenons un exemple dans lequel un autre *custom.css* fichier est ajouté qui ressemble à ce qui suit :

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Pour minimiser *custom.css* et regrouper avec *site.css* dans un *site.min.css* , ajoutez le chemin d’accès relatif à *bundleconfig.json*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Vous pouvez également le modèle de la globalisation suivants peut être utilisé :
>
> ```json
> "inputFiles": ["wwwroot/**/*(*.css|!(*.min.css)"]
> ```
>
> Ce modèle de la globalisation correspond à tous les fichiers CSS et exclut le modèle de fichier réduite.

Générez l'application. Ouvrez *site.min.css* et notez le contenu de *custom.css* est ajouté à la fin du fichier.

## <a name="environment-based-bundling-and-minification"></a>Groupement et minimisation basée sur l’environnement

Comme meilleure pratique, les fichiers regroupés et réduites de votre application doivent être utilisés dans un environnement de production. Pendant le développement, les fichiers d’origine apporter pour faciliter le débogage de l’application.

Spécifier les fichiers à inclure dans vos pages à l’aide de la [assistance de balise d’environnement](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) dans les vues. L’application d’assistance de balise environnement restitue uniquement son contenu lors de l’exécution dans spécifiques [environnements](xref:fundamentals/environments).

Les éléments suivants `environment` effectue le rendu lors de l’exécution les fichiers CSS non traités le `Development` environnement :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

---

Les éléments suivants `environment` effectue le rendu lors de l’exécution dans un environnement autre que les fichiers CSS groupées et réduites `Development`. Par exemple, en cours d’exécution `Production` ou `Staging` déclenche le rendu de ces feuilles de style :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

---

## <a name="consume-bundleconfigjson-from-gulp"></a>Consommer bundleconfig.json de Gulp

Il existe des cas dans lequel les flux de travail groupement et la minimisation d’une application nécessite un traitement supplémentaire. Optimisation des images, fonctions antispam de cache et le traitement d’asset CDN sont des exemples. Pour satisfaire ces exigences, vous pouvez convertir le flux de travail groupement et la minimisation pour utiliser Gulp.

### <a name="use-the-bundler--minifier-extension"></a>Utiliser l’extension textile et minimisation

Visual Studio [textile & minimisation](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension gère la conversion Gulp.

> [!NOTE]
> L’extension du programme d’installation regroupé & minimisation appartient à un projet communautaire sur GitHub pour lesquels Microsoft ne fournit aucune prise en charge. Problèmes doivent être classés [ici](https://github.com/madskristensen/BundlerMinifier/issues).

Avec le bouton droit le *bundleconfig.json* fichier dans l’Explorateur de solutions et sélectionnez **textile & minimisation** > **convertir à Gulp...** :

![Convertir à Gulp un élément de menu contextuel](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

Le *gulpfile.js* et *package.json* fichiers sont ajoutés au projet. La prise en charge [npm](https://www.npmjs.com/) packages répertoriés dans le *package.json* du fichier `devDependencies` section sont installés.

Dans la fenêtre PMC pour installer l’interface CLI Gulp comme dépendance globale, exécutez la commande suivante :

```console
npm i -g gulp-cli
```

Le *gulpfile.js* lecture des fichiers du *bundleconfig.json* fichier pour les entrées, sorties et les paramètres.

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a>Convertir manuellement

Si l’extension du programme d’installation regroupé & minimisation et/ou de Visual Studio ne sont pas disponible, convertir manuellement.

Ajouter un *package.json* fichier, par le code suivant `devDependencies`, à la racine du projet :

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Installer les dépendances en exécutant la commande suivante au même niveau que *package.json*:

```console
npm i
```

Installez l’interface CLI Gulp comme une dépendance globale :

```console
npm i -g gulp-cli
```

Copie le *gulpfile.js* le fichier sous la racine du projet :

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Exécuter des tâches de Gulp

Pour déclencher la tâche de réduction Gulp avant le projet dans Visual Studio, ajoutez le code suivant [cible MSBuild](/visualstudio/msbuild/msbuild-targets) au fichier *.csproj :

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

Dans cet exemple, toutes les tâches définies dans le `MyPreCompileTarget` cible s’exécutent avant prédéfinis `Build` cible. Sortie semblable au suivant s’affiche dans la fenêtre de sortie de Visual Studio :

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

Également, Explorateur d’exécuteur de tâche de Visual Studio peuvent être utilisée pour lier des tâches de Gulp à des événements spécifiques de Visual Studio. Consultez [en cours d’exécution des tâches par défaut](xref:client-side/using-gulp#running-default-tasks) pour obtenir des instructions sur cela.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Utilisation de Gulp](xref:client-side/using-gulp)
* [Utilisation de Grunt](xref:client-side/using-grunt)
* [Utilisation de plusieurs environnements](xref:fundamentals/environments)
* [Tag Helpers](xref:mvc/views/tag-helpers/intro)
