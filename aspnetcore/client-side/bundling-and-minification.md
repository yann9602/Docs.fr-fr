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
ms.openlocfilehash: 810d89798330aeb1c387ec85eb05b1f4efde167a
ms.sourcegitcommit: 275a5381b6172b4f0b5fcd1d252aff03d3dae166
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2017
---
# <a name="bundling-and-minification-in-aspnet-core"></a><span data-ttu-id="04e0f-103">Groupement et la minimisation dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="04e0f-103">Bundling and minification in ASP.NET Core</span></span>

<span data-ttu-id="04e0f-104">Groupement et la minimisation sont deux techniques que vous pouvez utiliser dans ASP.NET pour améliorer les performances de chargement de page de votre application web.</span><span class="sxs-lookup"><span data-stu-id="04e0f-104">Bundling and minification are two techniques you can use in ASP.NET to improve page load performance for your web application.</span></span> <span data-ttu-id="04e0f-105">Regroupement de combine plusieurs fichiers dans un seul fichier.</span><span class="sxs-lookup"><span data-stu-id="04e0f-105">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="04e0f-106">Minimisation effectue diverses optimisations de code différent aux scripts et CSS, ce qui entraîne des charges plus petites.</span><span class="sxs-lookup"><span data-stu-id="04e0f-106">Minification performs a variety of different code optimizations to scripts and CSS, which results in smaller payloads.</span></span> <span data-ttu-id="04e0f-107">Utilisées conjointement, groupement et la minimisation améliore les performances de temps de chargement en réduisant le nombre de demandes au serveur et de réduire la taille des actifs demandés (par exemple, les fichiers CSS et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="04e0f-107">Used together, bundling and minification improves load time performance by reducing the number of requests to the server and reducing the size of the requested assets (such as CSS and JavaScript files).</span></span>

<span data-ttu-id="04e0f-108">Cet article explique les avantages de l’utilisation de groupement et la minimisation, y compris comment ces fonctionnalités peuvent être utilisées avec les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="04e0f-108">This article explains the benefits of using bundling and minification, including how these features can be used with ASP.NET Core applications.</span></span>

## <a name="overview"></a><span data-ttu-id="04e0f-109">Vue d'ensemble</span><span class="sxs-lookup"><span data-stu-id="04e0f-109">Overview</span></span>

<span data-ttu-id="04e0f-110">Dans les applications ASP.NET Core, il existe plusieurs options pour le groupement et réduction des ressources côté client.</span><span class="sxs-lookup"><span data-stu-id="04e0f-110">In ASP.NET Core apps, there are multiple options for bundling and minifying client-side resources.</span></span> <span data-ttu-id="04e0f-111">Les modèles de base pour MVC fournissent une solution d’out of box à l’aide d’un fichier de configuration et le package NuGet de BuildBundlerMinifier.</span><span class="sxs-lookup"><span data-stu-id="04e0f-111">The core templates for MVC provide an out-of-the-box solution using a configuration file and BuildBundlerMinifier NuGet package.</span></span> <span data-ttu-id="04e0f-112">Outils tiers, tels que [Gulp](using-gulp.md) et [Grunt](using-grunt.md) sont également disponibles pour accomplir les mêmes tâches doivent nécessitent vos processus de flux de travail supplémentaire ou complexes.</span><span class="sxs-lookup"><span data-stu-id="04e0f-112">Third party tools, such as [Gulp](using-gulp.md) and [Grunt](using-grunt.md) are also available to accomplish the same tasks should your processes require additional workflow or complexities.</span></span> <span data-ttu-id="04e0f-113">À l’aide de groupement et la minimisation au moment du design, les fichiers réduites sont créés avant le déploiement de l’application.</span><span class="sxs-lookup"><span data-stu-id="04e0f-113">By using design-time bundling and minification, the minified files are created prior to the application’s deployment.</span></span> <span data-ttu-id="04e0f-114">Groupement et réduction avant le déploiement offre l’avantage d’une charge serveur réduite.</span><span class="sxs-lookup"><span data-stu-id="04e0f-114">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="04e0f-115">Toutefois, il est important de noter ce regroupement au moment du design et minimisation augmente la complexité de la build et fonctionne uniquement avec des fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="04e0f-115">However, it’s important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

<span data-ttu-id="04e0f-116">Groupement et la minimisation principalement améliorent le premier temps de chargement de demande de page.</span><span class="sxs-lookup"><span data-stu-id="04e0f-116">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="04e0f-117">Une fois qu’une page web a été demandée, le navigateur met en cache les ressources (JavaScript, CSS et images) afin de groupement et la minimisation ne fournissent n’importe quel légèrement les performances lors de la demande de la même page ou de pages sur le même site demande les mêmes actifs.</span><span class="sxs-lookup"><span data-stu-id="04e0f-117">Once a web page has been requested, the browser caches the assets (JavaScript, CSS and images) so bundling and minification won’t provide any performance boost when requesting the same page, or pages on the same site requesting the same assets.</span></span> <span data-ttu-id="04e0f-118">Si vous ne définissez pas l’expiration en-tête correctement sur vos éléments multimédias, vous n’utilisez pas groupement et la minimisation heuristique d’actualisation du navigateur marque les éléments multimédias obsolètes après quelques jours et, le navigateur nécessite une demande de validation pour chaque élément multimédia.</span><span class="sxs-lookup"><span data-stu-id="04e0f-118">If you don’t set the expires header correctly on your assets, and you don’t use bundling and minification, the browser's freshness heuristics will mark the assets stale after a few days and the browser will require a validation request for each asset.</span></span> <span data-ttu-id="04e0f-119">Dans ce cas, groupement et la minimisation fournissent une augmentation des performances même après la première demande de page.</span><span class="sxs-lookup"><span data-stu-id="04e0f-119">In this case, bundling and minification provide a performance increase even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="04e0f-120">Regroupement</span><span class="sxs-lookup"><span data-stu-id="04e0f-120">Bundling</span></span>

<span data-ttu-id="04e0f-121">Regroupement d’est une fonctionnalité qui permet de facilement combiner ou regrouper plusieurs fichiers dans un seul fichier.</span><span class="sxs-lookup"><span data-stu-id="04e0f-121">Bundling is a feature that makes it easy to combine or bundle multiple files into a single file.</span></span> <span data-ttu-id="04e0f-122">Étant donné que le regroupement de combine plusieurs fichiers dans un seul fichier, il réduit le nombre de demandes au serveur qui sont nécessaires pour récupérer et afficher une ressource web, par exemple une page web.</span><span class="sxs-lookup"><span data-stu-id="04e0f-122">Because bundling combines multiple files into a single file, it reduces the number of requests to the server that are required to retrieve and display a web asset, such as a web page.</span></span> <span data-ttu-id="04e0f-123">Vous pouvez créer le code CSS, JavaScript et autres groupes.</span><span class="sxs-lookup"><span data-stu-id="04e0f-123">You can create CSS, JavaScript and other bundles.</span></span> <span data-ttu-id="04e0f-124">Moins de fichiers signifie moins de demandes HTTP à partir de votre navigateur sur le serveur ou à partir du service en fournissant votre application.</span><span class="sxs-lookup"><span data-stu-id="04e0f-124">Fewer files means fewer HTTP requests from your browser to the server or from the service providing your application.</span></span> <span data-ttu-id="04e0f-125">Il en résulte dans amélioration des performances de charge première page.</span><span class="sxs-lookup"><span data-stu-id="04e0f-125">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="04e0f-126">Minimisation</span><span class="sxs-lookup"><span data-stu-id="04e0f-126">Minification</span></span>

<span data-ttu-id="04e0f-127">Minimisation effectue diverses optimisations de code différent pour réduire la taille des actifs demandés (par exemple, CSS, des images, des fichiers JavaScript).</span><span class="sxs-lookup"><span data-stu-id="04e0f-127">Minification performs a variety of different code optimizations to reduce the size of requested assets (such as CSS, images, JavaScript files).</span></span> <span data-ttu-id="04e0f-128">Résultats communs de minimisation incluent suppression inutilement de l’espace blanc et de commentaires et raccourcir les noms de variables pour un seul caractère.</span><span class="sxs-lookup"><span data-stu-id="04e0f-128">Common results of minification include removing unnecessary white space and comments, and shortening variable names to one character.</span></span>

<span data-ttu-id="04e0f-129">Observez la fonction JavaScript suivante :</span><span class="sxs-lookup"><span data-stu-id="04e0f-129">Consider the following JavaScript function:</span></span>

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

<span data-ttu-id="04e0f-130">Après réduction, la fonction est réduite à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="04e0f-130">After minification, the function is reduced to the following:</span></span>

```javascript
AddAltToImg=function(t,a){var r=$(t,a);r.attr("alt",r.attr("id").replace(/ID/,""))};
```

<span data-ttu-id="04e0f-131">Outre la suppression des commentaires et espaces inutiles, les paramètres suivants et les noms de variables ont été renommés (abrégé) comme suit :</span><span class="sxs-lookup"><span data-stu-id="04e0f-131">In addition to removing the comments and unnecessary whitespace, the following parameters and variable names were renamed (shortened) as follows:</span></span>

<span data-ttu-id="04e0f-132">D'origine</span><span class="sxs-lookup"><span data-stu-id="04e0f-132">Original</span></span> | <span data-ttu-id="04e0f-133">Affectation d'un nouveau nom</span><span class="sxs-lookup"><span data-stu-id="04e0f-133">Renamed</span></span>
--- | :---:
<span data-ttu-id="04e0f-134">imageTagAndImageID</span><span class="sxs-lookup"><span data-stu-id="04e0f-134">imageTagAndImageID</span></span> | <span data-ttu-id="04e0f-135">t</span><span class="sxs-lookup"><span data-stu-id="04e0f-135">t</span></span>
<span data-ttu-id="04e0f-136">imageContext</span><span class="sxs-lookup"><span data-stu-id="04e0f-136">imageContext</span></span> | <span data-ttu-id="04e0f-137">a</span><span class="sxs-lookup"><span data-stu-id="04e0f-137">a</span></span>
<span data-ttu-id="04e0f-138">imageElement</span><span class="sxs-lookup"><span data-stu-id="04e0f-138">imageElement</span></span> | <span data-ttu-id="04e0f-139">b</span><span class="sxs-lookup"><span data-stu-id="04e0f-139">r</span></span>

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="04e0f-140">Impact de groupement et minimisation</span><span class="sxs-lookup"><span data-stu-id="04e0f-140">Impact of bundling and minification</span></span>

<span data-ttu-id="04e0f-141">Le tableau suivant montre plusieurs différences importantes entre répertoriant tous les composants individuellement et à l’aide de groupement et la minimisation sur une page web simple :</span><span class="sxs-lookup"><span data-stu-id="04e0f-141">The following table shows several important differences between listing all the assets individually and using bundling and minification on a simple web page:</span></span>

<span data-ttu-id="04e0f-142">Action</span><span class="sxs-lookup"><span data-stu-id="04e0f-142">Action</span></span> | <span data-ttu-id="04e0f-143">B/m</span><span class="sxs-lookup"><span data-stu-id="04e0f-143">With B/M</span></span> | <span data-ttu-id="04e0f-144">Sans B/M</span><span class="sxs-lookup"><span data-stu-id="04e0f-144">Without B/M</span></span> | <span data-ttu-id="04e0f-145">Modification</span><span class="sxs-lookup"><span data-stu-id="04e0f-145">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="04e0f-146">Demandes de fichiers</span><span class="sxs-lookup"><span data-stu-id="04e0f-146">File Requests</span></span> |<span data-ttu-id="04e0f-147">7</span><span class="sxs-lookup"><span data-stu-id="04e0f-147">7</span></span> | <span data-ttu-id="04e0f-148">18</span><span class="sxs-lookup"><span data-stu-id="04e0f-148">18</span></span> | <span data-ttu-id="04e0f-149">157%</span><span class="sxs-lookup"><span data-stu-id="04e0f-149">157%</span></span>
<span data-ttu-id="04e0f-150">Ko transféré</span><span class="sxs-lookup"><span data-stu-id="04e0f-150">KB Transferred</span></span> | <span data-ttu-id="04e0f-151">156</span><span class="sxs-lookup"><span data-stu-id="04e0f-151">156</span></span> | <span data-ttu-id="04e0f-152">264.68</span><span class="sxs-lookup"><span data-stu-id="04e0f-152">264.68</span></span> | <span data-ttu-id="04e0f-153">70%</span><span class="sxs-lookup"><span data-stu-id="04e0f-153">70%</span></span>
<span data-ttu-id="04e0f-154">Temps de chargement (MS)</span><span class="sxs-lookup"><span data-stu-id="04e0f-154">Load Time (MS)</span></span> | <span data-ttu-id="04e0f-155">885</span><span class="sxs-lookup"><span data-stu-id="04e0f-155">885</span></span> | <span data-ttu-id="04e0f-156">2360</span><span class="sxs-lookup"><span data-stu-id="04e0f-156">2360</span></span> | <span data-ttu-id="04e0f-157">167%</span><span class="sxs-lookup"><span data-stu-id="04e0f-157">167%</span></span>

<span data-ttu-id="04e0f-158">Les octets envoyés avaient une réduction significative avec regroupement comme les navigateurs sont assez détaillés avec les en-têtes HTTP auxquels ils s’appliquent sur les demandes.</span><span class="sxs-lookup"><span data-stu-id="04e0f-158">The bytes sent had a significant reduction with bundling as browsers are fairly verbose with the HTTP headers that they apply on requests.</span></span> <span data-ttu-id="04e0f-159">Le temps de chargement montre une amélioration notable, toutefois, cet exemple a été exécuté localement.</span><span class="sxs-lookup"><span data-stu-id="04e0f-159">The load time shows a big improvement, however this example was run locally.</span></span> <span data-ttu-id="04e0f-160">Vous obtiendrez des gains supérieurs des performances lorsque les ressources à l’aide de groupement et la minimisation transférées sur un réseau.</span><span class="sxs-lookup"><span data-stu-id="04e0f-160">You will get greater gains in performance when using bundling and minification with assets transferred over a network.</span></span>

## <a name="using-bundling-and-minification-in-a-project"></a><span data-ttu-id="04e0f-161">Dans un projet à l’aide de groupement et minimisation</span><span class="sxs-lookup"><span data-stu-id="04e0f-161">Using bundling and minification in a project</span></span>

<span data-ttu-id="04e0f-162">Le modèle de projet MVC fournit un `bundleconfig.json` fichier de configuration qui définit les options pour chaque regroupement.</span><span class="sxs-lookup"><span data-stu-id="04e0f-162">The MVC project template provides a `bundleconfig.json` configuration file which defines the options for each bundle.</span></span> <span data-ttu-id="04e0f-163">Par défaut, une configuration de lot unique est définie pour le code JavaScript personnalisé (`wwwroot/js/site.js`) et la feuille de style (`wwwroot/css/site.css`) les fichiers.</span><span class="sxs-lookup"><span data-stu-id="04e0f-163">By default, a single bundle configuration is defined for the custom JavaScript (`wwwroot/js/site.js`) and Stylesheet (`wwwroot/css/site.css`) files.</span></span>

<span data-ttu-id="04e0f-164">[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig.json)]</span><span class="sxs-lookup"><span data-stu-id="04e0f-164">[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig.json)]</span></span>

<span data-ttu-id="04e0f-165">Options de regroupement :</span><span class="sxs-lookup"><span data-stu-id="04e0f-165">Bundle options include:</span></span>

* <span data-ttu-id="04e0f-166">outputFileName - nom du fichier d’offre groupée de sortie.</span><span class="sxs-lookup"><span data-stu-id="04e0f-166">outputFileName - name of the bundle file to output.</span></span> <span data-ttu-id="04e0f-167">Peut contenir un chemin d’accès relatif à partir de la `bundleconfig.json` fichier.</span><span class="sxs-lookup"><span data-stu-id="04e0f-167">Can contain a relative path from the `bundleconfig.json` file.</span></span> <span data-ttu-id="04e0f-168">**Obligatoire**</span><span class="sxs-lookup"><span data-stu-id="04e0f-168">**required**</span></span>
* <span data-ttu-id="04e0f-169">inputFiles - tableau de fichiers à regrouper.</span><span class="sxs-lookup"><span data-stu-id="04e0f-169">inputFiles - array of files to bundle together.</span></span> <span data-ttu-id="04e0f-170">Voici les chemins d’accès relatifs au fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="04e0f-170">These are relative paths to the configuration file.</span></span> <span data-ttu-id="04e0f-171">**facultatif**, * une valeur vide entraîne un fichier de sortie vide.</span><span class="sxs-lookup"><span data-stu-id="04e0f-171">**optional**, *an empty value results in an empty output file.</span></span> <span data-ttu-id="04e0f-172">[la globalisation](http://www.tldp.org/LDP/abs/html/globbingref.html) modèles sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="04e0f-172">[globbing](http://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="04e0f-173">minimiser - options de réduction de la sortie de type.</span><span class="sxs-lookup"><span data-stu-id="04e0f-173">minify - minification options for the output type.</span></span> <span data-ttu-id="04e0f-174">**facultatif**, *par défaut :`minify: { enabled: true }`*</span><span class="sxs-lookup"><span data-stu-id="04e0f-174">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="04e0f-175">Options de configuration sont disponibles par type de fichier de sortie.</span><span class="sxs-lookup"><span data-stu-id="04e0f-175">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="04e0f-176">Minimisation CSS</span><span class="sxs-lookup"><span data-stu-id="04e0f-176">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="04e0f-177">Minimisation de JavaScript</span><span class="sxs-lookup"><span data-stu-id="04e0f-177">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/jsminifier)
    * [<span data-ttu-id="04e0f-178">Minimisation de HTML</span><span class="sxs-lookup"><span data-stu-id="04e0f-178">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/htmlminifier)
* <span data-ttu-id="04e0f-179">includeInProject - ajouter des fichiers générés au fichier projet.</span><span class="sxs-lookup"><span data-stu-id="04e0f-179">includeInProject - add generated files to project file.</span></span> <span data-ttu-id="04e0f-180">**facultatif**, *par défaut : false*</span><span class="sxs-lookup"><span data-stu-id="04e0f-180">**optional**, *default - false*</span></span>
* <span data-ttu-id="04e0f-181">sourceMaps - générer des mappages de sources pour le fichier regroupé.</span><span class="sxs-lookup"><span data-stu-id="04e0f-181">sourceMaps - generate source maps for the bundled file.</span></span> <span data-ttu-id="04e0f-182">**facultatif**, *par défaut : false*</span><span class="sxs-lookup"><span data-stu-id="04e0f-182">**optional**, *default - false*</span></span>

### <a name="visual-studio-2015--2017"></a><span data-ttu-id="04e0f-183">Visual Studio 2015 / 2017</span><span class="sxs-lookup"><span data-stu-id="04e0f-183">Visual Studio 2015 / 2017</span></span>

<span data-ttu-id="04e0f-184">Ouvrez `bundleconfig.json` dans Visual Studio, si votre environnement ne possède pas d’extension installée ; une invite s’affiche indiquant qu’il existe un qui peut aider à ce type de fichier.</span><span class="sxs-lookup"><span data-stu-id="04e0f-184">Open `bundleconfig.json` in Visual Studio, if your environment does not have the extension installed; a prompt is presented suggesting that there is one that could assist with this file type.</span></span>

![Extension de BuildBundlerMinifier Suggestion](../client-side/bundling-and-minification/_static/bundler-extension-suggestion.png)

<span data-ttu-id="04e0f-186">Afficher les Extensions de sélectionner et d’installer le **textile & minimisation** extension (redémarrer nécessite Visual Studio).</span><span class="sxs-lookup"><span data-stu-id="04e0f-186">Select View Extensions, and install the **Bundler & Minifier** extension (Requires Visual Studio restart).</span></span>

![Extension de BuildBundlerMinifier Suggestion](../client-side/bundling-and-minification/_static/view-extension.png)

<span data-ttu-id="04e0f-188">Une fois le redémarrage terminé, vous devez configurer la build pour exécuter les processus de réduction et de regroupement les composants côté client.</span><span class="sxs-lookup"><span data-stu-id="04e0f-188">When the restart is complete, you need to configure the build to run the processes of minifying and bundling the client-side assets.</span></span> <span data-ttu-id="04e0f-189">Avec le bouton droit le `bundleconfig.json` fichier et sélectionnez *offre groupée d’activer sur la build...* .</span><span class="sxs-lookup"><span data-stu-id="04e0f-189">Right-click the `bundleconfig.json` file and select *Enable bundle on build...*.</span></span>

<span data-ttu-id="04e0f-190">Générez le projet et le `bundleconfig.json` est inclus dans le processus de génération pour générer les fichiers de sortie en fonction de la configuration.</span><span class="sxs-lookup"><span data-stu-id="04e0f-190">Build the project, and the `bundleconfig.json` is included in the build process to produce the output files based on the configuration.</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierExample, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierExample -> C:\BuildBundlerMinifierExample\bin\Debug\netcoreapp1.1\BuildBundlerMinifierExample.dll
========== Build: 1 succeeded or up-to-date, 0 failed, 0 skipped ==========
```

### <a name="visual-studio-code-or-command-line"></a><span data-ttu-id="04e0f-191">Code Visual Studio ou la ligne de commande</span><span class="sxs-lookup"><span data-stu-id="04e0f-191">Visual Studio Code or Command Line</span></span>

<span data-ttu-id="04e0f-192">Visual Studio et l’extension pilotent le processus de regroupement et minimisation à l’aide des mouvements de l’interface graphique utilisateur ; Toutefois, les mêmes fonctionnalités sont disponibles avec le `dotnet` package CLI et BuildBundlerMinifier NuGet.</span><span class="sxs-lookup"><span data-stu-id="04e0f-192">Visual Studio and the extension drive the bundling and minification process using GUI gestures; however, the same capabilities are available with the `dotnet` CLI and BuildBundlerMinifier NuGet package.</span></span>

<span data-ttu-id="04e0f-193">Ajoutez le package NuGet à votre projet :</span><span class="sxs-lookup"><span data-stu-id="04e0f-193">Add the NuGet package to your project:</span></span>

```console
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="04e0f-194">Restaurer les dépendances :</span><span class="sxs-lookup"><span data-stu-id="04e0f-194">Restore the dependencies:</span></span>

```console
dotnet restore
```

<span data-ttu-id="04e0f-195">Générez l’application :</span><span class="sxs-lookup"><span data-stu-id="04e0f-195">Build the app:</span></span>

```console
dotnet build
```

<span data-ttu-id="04e0f-196">La sortie de la commande de génération indique les résultats de la minimisation et/ou de regroupement en fonction de ce qui est configuré.</span><span class="sxs-lookup"><span data-stu-id="04e0f-196">The output from the build command shows the results of the minification and/or bundling according to what is configured.</span></span>

```console
Microsoft (R) Build Engine version 15.1.545.13942
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Begin processing bundleconfig.json
     Minified wwwroot/css/site.min.css
  Bundler: Done processing bundleconfig.json
  BuildBundlerMinifierExample -> /BuildBundlerMinifierExample/bin/Debug/netcoreapp1.0/BuildBundlerMinifierExample.dll
```

## <a name="adding-files"></a><span data-ttu-id="04e0f-197">Ajout de fichiers</span><span class="sxs-lookup"><span data-stu-id="04e0f-197">Adding files</span></span>

<span data-ttu-id="04e0f-198">Dans cet exemple, un fichier CSS supplémentaire est ajouté appelée `custom.css` et configurés pour le groupement et la minimisation avec `site.css`, se traduisant par un seul `site.min.css`.</span><span class="sxs-lookup"><span data-stu-id="04e0f-198">In this example, an additional CSS file is added called `custom.css` and configured for bundling and minification with `site.css`, resulting in a single `site.min.css`.</span></span>

<span data-ttu-id="04e0f-199">Custom.CSS</span><span class="sxs-lookup"><span data-stu-id="04e0f-199">custom.css</span></span>

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

<span data-ttu-id="04e0f-200">Ajoutez le chemin d’accès relatif à `bundleconfig.json`.</span><span class="sxs-lookup"><span data-stu-id="04e0f-200">Add the relative path to `bundleconfig.json`.</span></span>

<span data-ttu-id="04e0f-201">[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig2.json)]</span><span class="sxs-lookup"><span data-stu-id="04e0f-201">[!code-json[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/bundleconfig2.json)]</span></span>

> [!NOTE]
> <span data-ttu-id="04e0f-202">Vous pouvez également le modèle de la globalisation utilisable - `"inputFiles": ["wwwroot/**/*(*.css|!(*.min.css)"]` qui obtient CSS tous les fichiers et exclut le modèle de fichier réduite.</span><span class="sxs-lookup"><span data-stu-id="04e0f-202">Alternatively, the globbing pattern could be used - `"inputFiles": ["wwwroot/**/*(*.css|!(*.min.css)"]` which gets all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="04e0f-203">Générez l’application et si vous ouvrez `site.min.css`, vous remarquerez maintenant que le contenu de `custom.css` a été ajouté à la fin du fichier.</span><span class="sxs-lookup"><span data-stu-id="04e0f-203">Build the application and if you open `site.min.css`, you'll now notice that contents of `custom.css` has been appended to the end of the file.</span></span>

## <a name="controlling-bundling-and-minification"></a><span data-ttu-id="04e0f-204">Contrôle de regroupement et minimisation</span><span class="sxs-lookup"><span data-stu-id="04e0f-204">Controlling bundling and minification</span></span>

<span data-ttu-id="04e0f-205">En général, vous souhaitez utiliser les fichiers regroupés et réduites de votre application uniquement dans un environnement de production.</span><span class="sxs-lookup"><span data-stu-id="04e0f-205">In general, you want to use the bundled and minified files of your app only in a production environment.</span></span> <span data-ttu-id="04e0f-206">Pendant le développement, vous souhaitez utiliser vos fichiers d’origine de votre application est plus facile à déboguer.</span><span class="sxs-lookup"><span data-stu-id="04e0f-206">During development, you want to use your original files so your app is easier to debug.</span></span>

<span data-ttu-id="04e0f-207">Vous pouvez spécifier les scripts et les fichiers CSS à inclure dans vos pages à l’aide de l’assistance de balise d’environnement dans vos pages de disposition (consultez [programmes d’assistance de balise](../mvc/views/tag-helpers/index.md)).</span><span class="sxs-lookup"><span data-stu-id="04e0f-207">You can specify which scripts and CSS files to include in your pages using the environment tag helper in your layout pages (see [Tag Helpers](../mvc/views/tag-helpers/index.md)).</span></span> <span data-ttu-id="04e0f-208">L’application d’assistance de balise environnement effectue le rendu uniquement son contenu lors de l’exécution dans des environnements spécifiques.</span><span class="sxs-lookup"><span data-stu-id="04e0f-208">The environment tag helper will only render its contents when running in specific environments.</span></span> <span data-ttu-id="04e0f-209">Consultez [fonctionne avec plusieurs environnements](../fundamentals/environments.md) pour plus d’informations sur la spécification de l’environnement actuel.</span><span class="sxs-lookup"><span data-stu-id="04e0f-209">See [Working with Multiple Environments](../fundamentals/environments.md) for details on specifying the current environment.</span></span>

<span data-ttu-id="04e0f-210">La balise d’environnement restituera les fichiers CSS non traités lors de l’exécution le `Development` environnement :</span><span class="sxs-lookup"><span data-stu-id="04e0f-210">The following environment tag will render the unprocessed CSS files when running in the `Development` environment:</span></span>

<span data-ttu-id="04e0f-211">[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=3&range=9-12)]</span><span class="sxs-lookup"><span data-stu-id="04e0f-211">[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=3&range=9-12)]</span></span>

<span data-ttu-id="04e0f-212">Cette balise d’environnement restituera les fichiers CSS groupées et réduites uniquement lors de l’exécution `Production` ou `Staging`:</span><span class="sxs-lookup"><span data-stu-id="04e0f-212">This environment tag will render the bundled and minified CSS files only when running in `Production` or `Staging`:</span></span>

<span data-ttu-id="04e0f-213">[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=5&range=13-18)]</span><span class="sxs-lookup"><span data-stu-id="04e0f-213">[!code-html[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/Views/Shared/_Layout.cshtml?highlight=5&range=13-18)]</span></span>

## <a name="consuming-bundleconfigjson-from-gulp"></a><span data-ttu-id="04e0f-214">Consommation bundleconfig.json de Gulp</span><span class="sxs-lookup"><span data-stu-id="04e0f-214">Consuming bundleconfig.json from Gulp</span></span>

<span data-ttu-id="04e0f-215">Si votre flux de travail groupement et la minimisation du application nécessite des processus supplémentaires telles que le traitement d’image cache fonctions antispam, le traitement d’assest CDN, etc., vous pouvez convertir le processus de regroupement et Minify à Gulp.</span><span class="sxs-lookup"><span data-stu-id="04e0f-215">If your app bundling and minification workflow requires additional processes such as image processing, cache busting, CDN assest processing, etc., then you can convert the Bundle and Minify process to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="04e0f-216">Option conversion est disponible uniquement dans Visual Studio 2015 et 2017.</span><span class="sxs-lookup"><span data-stu-id="04e0f-216">Conversion option only available in Visual Studio 2015 and 2017.</span></span>

<span data-ttu-id="04e0f-217">Avec le bouton droit le `bundleconfig.json` et sélectionnez **convertir Gulp...** . Cette opération génère le `gulpfile.js` et installer les packages nécessaires npm.</span><span class="sxs-lookup"><span data-stu-id="04e0f-217">Right-click the `bundleconfig.json` and select **Convert to Gulp...**. This will generate the `gulpfile.js` and install the necessary npm packages.</span></span>

![Convertir en Gulp](../client-side/bundling-and-minification/_static/convert-togulp.png)

<span data-ttu-id="04e0f-219">Le `gulpfile.js` produits lit le `bundleconfig.json` fichier pour la configuration, par conséquent, il peut continuer à utiliser pour les entrées/sorties et les paramètres.</span><span class="sxs-lookup"><span data-stu-id="04e0f-219">The `gulpfile.js` produced reads the `bundleconfig.json` file for the configuration, therefore it can continue to be used for the inputs/outputs and settings.</span></span>

<span data-ttu-id="04e0f-220">[!code-javascript[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/gulpfile.js)]</span><span class="sxs-lookup"><span data-stu-id="04e0f-220">[!code-javascript[Main](../client-side/bundling-and-minification/samples/BuildBundlerMinifierExample/gulpfile.js)]</span></span>

<span data-ttu-id="04e0f-221">Pour activer les choses lors de la génération du projet dans Visual Studio 2017, ajoutez le code suivant au fichier *.csproj :</span><span class="sxs-lookup"><span data-stu-id="04e0f-221">To enable Gulp when the project builds in Visual Studio 2017, add the following to the *.csproj file:</span></span>

```xml
<Target Name="MyPreCompileTarget" BeforeTargets="Build">
    <Exec Command="gulp min" />
</Target>
```

<span data-ttu-id="04e0f-222">Pour activer les choses lors de la génération du projet dans Visual Studio 2015, ajoutez le code suivant à la `project.json` fichier :</span><span class="sxs-lookup"><span data-stu-id="04e0f-222">To enable Gulp when the project builds in Visual Studio 2015, add the following to the `project.json` file:</span></span>

```json
"scripts": {
    "precompile": "gulp min"
}
```

## <a name="additional-resources"></a><span data-ttu-id="04e0f-223">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="04e0f-223">Additional resources</span></span>

* [<span data-ttu-id="04e0f-224">Utilisation de Gulp</span><span class="sxs-lookup"><span data-stu-id="04e0f-224">Using Gulp</span></span>](using-gulp.md)
* [<span data-ttu-id="04e0f-225">À l’aide de Grunt</span><span class="sxs-lookup"><span data-stu-id="04e0f-225">Using Grunt</span></span>](using-grunt.md)
* [<span data-ttu-id="04e0f-226">Utilisation de plusieurs environnements</span><span class="sxs-lookup"><span data-stu-id="04e0f-226">Working with Multiple Environments</span></span>](../fundamentals/environments.md)
* [<span data-ttu-id="04e0f-227">Programmes d’assistance de balise</span><span class="sxs-lookup"><span data-stu-id="04e0f-227">Tag Helpers</span></span>](../mvc/views/tag-helpers/index.md)
