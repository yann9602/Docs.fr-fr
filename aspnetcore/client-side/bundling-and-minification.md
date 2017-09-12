---
title: Groupement et la minimisation dans ASP.NET Core
author: spboyer
description: 
keywords: "ASP.NET Core, regroupement et de réduction, CSS, JavaScript, minimiser, BuildBundlerMinifier"
ms.author: riande
manager: wpickett
ms.date: 02/28/2017
ms.topic: article
ms.assetid: d54230f9-8e5f-4861-a29c-1d3a14e0b0d9
ms.technology: aspnet
ms.prod: aspnet-core
uid: client-side/bundling-and-minification
ms.openlocfilehash: d8512bdd49b61019f22a49900bdd65086d821a6b
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="bundling-and-minification-in-aspnet-core"></a>Groupement et la minimisation dans ASP.NET Core

Groupement et la minimisation sont deux techniques que vous pouvez utiliser dans ASP.NET pour améliorer les performances de chargement de page de votre application web. Regroupement de combine plusieurs fichiers dans un seul fichier. Minimisation effectue diverses optimisations de code différent aux scripts et CSS, ce qui entraîne des charges plus petites. Utilisées conjointement, groupement et la minimisation améliore les performances de temps de chargement en réduisant le nombre de demandes au serveur et de réduire la taille des actifs demandés (par exemple, les fichiers CSS et JavaScript).

Cet article explique les avantages de l’utilisation de groupement et la minimisation, y compris comment ces fonctionnalités peuvent être utilisées avec les applications ASP.NET Core.

## <a name="overview"></a>Vue d'ensemble

Dans les applications ASP.NET Core, il existe plusieurs options pour le groupement et réduction des ressources côté client. Les modèles de base pour MVC fournissent une solution d’out of box à l’aide d’un fichier de configuration et le package NuGet de BuildBundlerMinifier. Outils tiers, tels que [Gulp](using-gulp.md) et [Grunt](using-grunt.md) sont également disponibles pour accomplir les mêmes tâches doivent nécessitent vos processus de flux de travail supplémentaire ou complexes. À l’aide de groupement et la minimisation au moment du design, les fichiers réduites sont créés avant le déploiement de l’application. Groupement et réduction avant le déploiement offre l’avantage d’une charge serveur réduite. Toutefois, il est important de noter ce regroupement au moment du design et minimisation augmente la complexité de la build et fonctionne uniquement avec des fichiers statiques.

Groupement et la minimisation principalement améliorent le premier temps de chargement de demande de page. Une fois qu’une page web a été demandée, le navigateur met en cache les ressources (JavaScript, CSS et images) afin de groupement et la minimisation ne fournissent n’importe quel légèrement les performances lors de la demande de la même page ou de pages sur le même site demande les mêmes actifs. Si vous ne définissez pas l’expiration en-tête correctement sur vos éléments multimédias, vous n’utilisez pas groupement et la minimisation heuristique d’actualisation du navigateur marque les éléments multimédias obsolètes après quelques jours et, le navigateur nécessite une demande de validation pour chaque élément multimédia. Dans ce cas, groupement et la minimisation fournissent une augmentation des performances même après la première demande de page.

### <a name="bundling"></a>Regroupement

Regroupement d’est une fonctionnalité qui permet de facilement combiner ou regrouper plusieurs fichiers dans un seul fichier. Étant donné que le regroupement de combine plusieurs fichiers dans un seul fichier, il réduit le nombre de demandes au serveur qui sont nécessaires pour récupérer et afficher une ressource web, par exemple une page web. Vous pouvez créer le code CSS, JavaScript et autres groupes. Moins de fichiers signifie moins de demandes HTTP à partir de votre navigateur sur le serveur ou à partir du service en fournissant votre application. Il en résulte dans amélioration des performances de charge première page.

### <a name="minification"></a>Minimisation

Minimisation effectue diverses optimisations de code différent pour réduire la taille des actifs demandés (par exemple, CSS, des images, des fichiers JavaScript). Résultats communs de minimisation incluent suppression inutilement de l’espace blanc et de commentaires et raccourcir les noms de variables pour un seul caractère.

Observez la fonction JavaScript suivante :

```javascript
AddAltToImg = function (imageTagAndImageID, imageContext) {
  ///<signature>
  ///<summary> Adds an alt tab to the image
  // </summary>
  //<param name="imgElement" type="String">The image selector.</param>
  //<param name="ContextForImage" type="String">The image context.</param>
  ///</signature>
  var imageElement = $(imageTagAndImageID, imageContext);
  imageElement.attr('alt', imageElement.attr('id').replace(/ID/, ''));
}
```

Après réduction, la fonction est réduite à ce qui suit :

```javascript
AddAltToImg=function(t,a){var r=$(t,a);r.attr("alt",r.attr("id").replace(/ID/,""))};
```

Outre la suppression des commentaires et espaces inutiles, les paramètres suivants et les noms de variables ont été renommés (abrégé) comme suit :

D'origine | Affectation d'un nouveau nom
--- | :---:
imageTagAndImageID | t
imageContext | a
imageElement | b

## <a name="impact-of-bundling-and-minification"></a>Impact de groupement et minimisation

Le tableau suivant montre plusieurs différences importantes entre répertoriant tous les composants individuellement et à l’aide de groupement et la minimisation sur une page web simple :

Action | B/m | Sans B/M | Modification
--- | :---: | :---: | :---:
Demandes de fichiers |7 | 18 | 157%
Ko transféré | 156 | 264.68 | 70%
Temps de chargement (MS) | 885 | 2360 | 167%

Les octets envoyés avaient une réduction significative avec regroupement comme les navigateurs sont assez détaillés avec les en-têtes HTTP auxquels ils s’appliquent sur les demandes. Le temps de chargement montre une amélioration notable, toutefois, cet exemple a été exécuté localement. Vous obtiendrez des gains supérieurs des performances lorsque les ressources à l’aide de groupement et la minimisation transférées sur un réseau.

## <a name="using-bundling-and-minification-in-a-project"></a>Dans un projet à l’aide de groupement et minimisation

Le modèle de projet MVC fournit un `bundleconfig.json` fichier de configuration qui définit les options pour chaque regroupement. Par défaut, une configuration de lot unique est définie pour le code JavaScript personnalisé (`wwwroot/js/site.js`) et la feuille de style (`wwwroot/css/site.css`) les fichiers.

[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig.json)]

Options de regroupement :

* outputFileName - nom du fichier d’offre groupée de sortie. Peut contenir un chemin d’accès relatif à partir de la `bundleconfig.json` fichier. **Obligatoire**
* inputFiles - tableau de fichiers à regrouper. Voici les chemins d’accès relatifs au fichier de configuration. **facultatif**, * une valeur vide entraîne un fichier de sortie vide. [la globalisation](http://www.tldp.org/LDP/abs/html/globbingref.html) modèles sont pris en charge.
* minimiser - options de réduction de la sortie de type. **facultatif**, *par défaut :`minify: { enabled: true }`*
  * Options de configuration sont disponibles par type de fichier de sortie.
    * [Minimisation CSS](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [Minimisation de JavaScript](https://github.com/madskristensen/BundlerMinifier/wiki)
    * [Minimisation de HTML](https://github.com/madskristensen/BundlerMinifier/wiki)
* includeInProject - ajouter des fichiers générés au fichier projet. **facultatif**, *par défaut : false*
* sourceMaps - générer des mappages de sources pour le fichier regroupé. **facultatif**, *par défaut : false*

### <a name="visual-studio-2015--2017"></a>Visual Studio 2015 / 2017

Ouvrez `bundleconfig.json` dans Visual Studio, si votre environnement ne possède pas d’extension installée ; une invite s’affiche indiquant qu’il existe un qui peut aider à ce type de fichier.

![Extension de BuildBundlerMinifier Suggestion](../client-side/bundling-and-minification/_static/bundler-extension-suggestion.png)

Afficher les Extensions de sélectionner et d’installer le **textile & minimisation** extension (redémarrer nécessite Visual Studio).

![Extension de BuildBundlerMinifier Suggestion](../client-side/bundling-and-minification/_static/view-extension.png)

Une fois le redémarrage terminé, vous devez configurer la build pour exécuter les processus de réduction et de regroupement les composants côté client. Avec le bouton droit le `bundleconfig.json` fichier et sélectionnez *offre groupée d’activer sur la build... *.

Générez le projet et le `bundleconfig.json` est inclus dans le processus de génération pour générer les fichiers de sortie en fonction de la configuration.

```console
1>------ Build started: Project: BuildBundlerMinifierExample, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierExample -> C:\BuildBundlerMinifierExample\bin\Debug\netcoreapp1.1\BuildBundlerMinifierExample.dll
========== Build: 1 succeeded or up-to-date, 0 failed, 0 skipped ==========
```

### <a name="visual-studio-code-or-command-line"></a>Code Visual Studio ou la ligne de commande

Visual Studio et l’extension pilotent le processus de regroupement et minimisation à l’aide des mouvements de l’interface graphique utilisateur ; Toutefois, les mêmes fonctionnalités sont disponibles avec le `dotnet` package CLI et BuildBundlerMinifier NuGet.

Ajoutez le package NuGet à votre projet :

```console
dotnet add package BuildBundlerMinifier
```

Restaurer les dépendances :

```console
dotnet restore
```

Générez l’application :

```console
dotnet build
```

La sortie de la commande de génération indique les résultats de la minimisation et/ou de regroupement en fonction de ce qui est configuré.

```console
Microsoft (R) Build Engine version 15.1.545.13942
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Begin processing bundleconfig.json
     Minified wwwroot/css/site.min.css
  Bundler: Done processing bundleconfig.json
  BuildBundlerMinifierExample -> /BuildBundlerMinifierExample/bin/Debug/netcoreapp1.0/BuildBundlerMinifierExample.dll
```

## <a name="adding-files"></a>Ajout de fichiers

Dans cet exemple, un fichier CSS supplémentaire est ajouté appelée `custom.css` et configurés pour le groupement et la minimisation avec `site.css`, se traduisant par un seul `site.min.css`.

Custom.CSS

```css
.about, [role=main], [role=complementary]
{
    margin-top: 60px;
}

footer
{
    margin-top: 10px;
}
```

Ajoutez le chemin d’accès relatif à `bundleconfig.json`.

[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig2.json)]

> [!NOTE]
> Vous pouvez également le modèle de la globalisation utilisable - `"inputFiles": ["wwwroot/**/*(*.css|!(*.min.css)"]` qui obtient CSS tous les fichiers et exclut le modèle de fichier réduite.

Générez l’application et si vous ouvrez `site.min.css`, vous remarquerez maintenant que le contenu de `custom.css` a été ajouté à la fin du fichier.

## <a name="controlling-bundling-and-minification"></a>Contrôle de regroupement et minimisation

En général, vous souhaitez utiliser les fichiers regroupés et réduites de votre application uniquement dans un environnement de production. Pendant le développement, vous souhaitez utiliser vos fichiers d’origine de votre application est plus facile à déboguer.

Vous pouvez spécifier les scripts et les fichiers CSS à inclure dans vos pages à l’aide de l’assistance de balise d’environnement dans vos pages de disposition (consultez [programmes d’assistance de balise](../mvc/views/tag-helpers/index.md)). L’application d’assistance de balise environnement effectue le rendu uniquement son contenu lors de l’exécution dans des environnements spécifiques. Consultez [fonctionne avec plusieurs environnements](../fundamentals/environments.md) pour plus d’informations sur la spécification de l’environnement actuel.

La balise d’environnement restituera les fichiers CSS non traités lors de l’exécution le `Development` environnement :

[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=3&range=9-12)]

Cette balise d’environnement restituera les fichiers CSS groupées et réduites uniquement lors de l’exécution `Production` ou `Staging`:

[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=5&range=13-18)]

## <a name="consuming-bundleconfigjson-from-gulp"></a>Consommation bundleconfig.json de Gulp

Si votre flux de travail groupement et la minimisation du application nécessite des processus supplémentaires telles que le traitement d’image cache fonctions antispam, le traitement d’assest CDN, etc., vous pouvez convertir le processus de regroupement et Minify à Gulp.

> [!NOTE]
> Option conversion est disponible uniquement dans Visual Studio 2015 et 2017.

Avec le bouton droit le `bundleconfig.json` et sélectionnez **convertir Gulp... **. Cette opération génère le `gulpfile.js` et installer les packages nécessaires npm.

![Convertir en Gulp](../client-side/bundling-and-minification/_static/convert-togulp.png)

Le `gulpfile.js` produits lit le `bundleconfig.json` fichier pour la configuration, par conséquent, il peut continuer à utiliser pour les entrées/sorties et les paramètres.

[!code-javascript[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/gulpfile.js)]

Pour activer les choses lors de la génération du projet dans Visual Studio 2017, ajoutez le code suivant au fichier *.csproj :

```xml
<Target Name="MyPreCompileTarget" BeforeTargets="Build">
    <Exec Command="gulp min" />
</Target>
```

Pour activer les choses lors de la génération du projet dans Visual Studio 2015, ajoutez le code suivant à la `project.json` fichier :

```json
"scripts": {
    "precompile": "gulp min"
}
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Utilisation de Gulp](using-gulp.md)
* [Utilisation de Grunt](using-grunt.md)
* [Utilisation de plusieurs environnements](../fundamentals/environments.md)
* [Tag Helpers](../mvc/views/tag-helpers/index.md)
