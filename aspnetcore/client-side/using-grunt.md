---
title: "À l’aide de Grunt dans ASP.NET Core"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 471112e9-2c33-454b-96fc-32916102ce73
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/using-grunt
ms.openlocfilehash: 8ae50514ce24c7f9e3bb1e347d5d860e1de43c5f
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="using-grunt-in-aspnet-core"></a><span data-ttu-id="e7c1a-103">À l’aide de Grunt dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7c1a-103">Using Grunt in ASP.NET Core</span></span> 

<span data-ttu-id="e7c1a-104">Par [Noel riz](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)</span><span class="sxs-lookup"><span data-stu-id="e7c1a-104">By [Noel Rice](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)</span></span>

<span data-ttu-id="e7c1a-105">Grunt est un exécuteur de tâches JavaScript qui automatise une minimisation du script, la compilation TypeScript, outils « lint » qualité du code, les processeurs avant CSS et pratiquement n’importe quel, répétitive qui doit effectuer pour prendre en charge le développement de clients.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-105">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="e7c1a-106">Grunt est entièrement pris en charge dans Visual Studio, bien que les modèles de projet ASP.NET utilisent Gulp par défaut (voir [à l’aide de Gulp](using-gulp.md)).</span><span class="sxs-lookup"><span data-stu-id="e7c1a-106">Grunt is fully supported in Visual Studio, though the ASP.NET project templates use Gulp by default (see [Using Gulp](using-gulp.md)).</span></span>

<span data-ttu-id="e7c1a-107">Cet exemple utilise un projet ASP.NET Core vide comme point de départ, pour montrer comment automatiser le processus de génération de client à partir de zéro.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-107">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="e7c1a-108">L’exemple terminé nettoie le répertoire de déploiement cible, combine les fichiers JavaScript, vérifie la qualité du code, condense le contenu du fichier JavaScript et déploie sur la racine de votre application web.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-108">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="e7c1a-109">Nous allons utiliser les packages suivants :</span><span class="sxs-lookup"><span data-stu-id="e7c1a-109">We will use the following packages:</span></span>

* <span data-ttu-id="e7c1a-110">**Grunt**: package de runner Grunt la tâche.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-110">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="e7c1a-111">**Grunt-cotisation nettoyer**: un plug-in qui supprime des fichiers ou répertoires.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-111">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="e7c1a-112">**Grunt-cotisation-jshint**: un plug-in qui passe en revue la qualité du code JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-112">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="e7c1a-113">**Grunt-cotisation-concat**: un plug-in qui joint les fichiers dans un seul fichier.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-113">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="e7c1a-114">**Grunt-cotisation uglify**: un plug-in qui minimise le JavaScript pour réduire la taille.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-114">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="e7c1a-115">**Grunt-cotisation-espion**: un plug-in qui surveille l’activité des fichiers.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-115">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="e7c1a-116">Préparation de l’application</span><span class="sxs-lookup"><span data-stu-id="e7c1a-116">Preparing the application</span></span>

<span data-ttu-id="e7c1a-117">Pour commencer, configurez une nouvelle application web vide et ajouter des fichiers d’exemple TypeScript.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-117">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="e7c1a-118">Fichiers typeScript sont automatiquement compilés dans JavaScript à l’aide des paramètres de Visual Studio par défaut et seront notre matières premières à traiter à l’aide de Grunt.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-118">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1.  <span data-ttu-id="e7c1a-119">Dans Visual Studio, créez un nouveau `ASP.NET Web Application`.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-119">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2.  <span data-ttu-id="e7c1a-120">Dans le **nouveau projet ASP.NET** boîte de dialogue, sélectionnez le ASP.NET Core **vide** modèle et cliquez sur le bouton OK.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-120">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3.  <span data-ttu-id="e7c1a-121">Dans l’Explorateur de solutions, passez en revue la structure de projet.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-121">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="e7c1a-122">Le `\src` dossier inclut vide `wwwroot` et `Dependencies` nœuds.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-122">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![solution de site web vide](using-grunt/_static/grunt-solution-explorer.png)

4.  <span data-ttu-id="e7c1a-124">Ajouter un nouveau dossier nommé `TypeScript` à votre répertoire de projet.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-124">Add a new folder named `TypeScript` to your project directory.</span></span>

5.  <span data-ttu-id="e7c1a-125">Avant d’ajouter des fichiers, assurez-vous que Visual Studio propose l’option ' compiler lors de l’enregistrement ' pour les fichiers TypeScript activées.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-125">Before adding any files, let’s make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="e7c1a-126">*Outils > Options > Éditeur de texte > Typescript > projet*</span><span class="sxs-lookup"><span data-stu-id="e7c1a-126">*Tools > Options > Text Editor > Typescript > Project*</span></span>

    ![Options de paramétrage compliation automatique des fichiers TypeScript](using-grunt/_static/typescript-options.png)

6.  <span data-ttu-id="e7c1a-128">Avec le bouton droit le `TypeScript` directory et sélectionnez **Ajouter > nouvel élément** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-128">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="e7c1a-129">Sélectionnez le **fichier JavaScript** d’élément et nommez le fichier *Tastes.ts* (Notez le \*.ts extension).</span><span class="sxs-lookup"><span data-stu-id="e7c1a-129">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="e7c1a-130">Copiez la ligne de code TypeScript ci-dessous dans le fichier (lorsque vous enregistrez, un nouveau *Tastes.js* fichier s’affiche avec la source JavaScript).</span><span class="sxs-lookup"><span data-stu-id="e7c1a-130">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>
    
    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7.  <span data-ttu-id="e7c1a-131">Ajoutez un deuxième fichier dans le **TypeScript** active et nommez-le `Food.ts`.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-131">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="e7c1a-132">Copiez le code ci-dessous dans le fichier.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-132">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }
    
      private _name: string;
      get Name() {
        return this._name;
      }
    
      private _calories: number;
      get Calories() {
        return this._calories;
      }
    
      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="e7c1a-133">Configuration de NPM</span><span class="sxs-lookup"><span data-stu-id="e7c1a-133">Configuring NPM</span></span>

<span data-ttu-id="e7c1a-134">Ensuite, configurez NPM pour télécharger grunt et les tâches de grunt.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-134">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="e7c1a-135">Dans l’Explorateur de solutions, cliquez sur le projet et sélectionnez **Ajouter > nouvel élément** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-135">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="e7c1a-136">Sélectionnez le **fichier de configuration NPM** d’élément, laissez le nom par défaut, *package.json*, puis cliquez sur le **ajouter** bouton.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-136">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="e7c1a-137">Dans le *package.json* de fichiers, dans le `devDependencies` accolades de l’objet, entrez « des grunt ».</span><span class="sxs-lookup"><span data-stu-id="e7c1a-137">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="e7c1a-138">Sélectionnez `grunt` dans Intellisense liste et appuyez sur ENTRÉE.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-138">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="e7c1a-139">Visual Studio le nom du package grunt de placer entre guillemets et ajouter un signe deux-points.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-139">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="e7c1a-140">À droite du signe deux-points, sélectionnez la dernière version stable du package à partir du haut de la liste Intellisense (appuyez sur `Ctrl-Space` si Intellisense n’apparaît pas).</span><span class="sxs-lookup"><span data-stu-id="e7c1a-140">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense does not appear).</span></span>

    ![grun Intellisense](using-grunt/_static/devdependencies-grunt.png)
    
    > [!NOTE]
    > <span data-ttu-id="e7c1a-142">NPM utilise [contrôle de version sémantique](http://semver.org/) pour organiser les dépendances.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-142">NPM uses [semantic versioning](http://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="e7c1a-143">Contrôle de version sémantique, également appelé SemVer, identifie les packages avec le modèle de numérotation <major>.<minor>. <patch>. IntelliSense simplifie le contrôle de version sémantique en affichant uniquement quelques choix courants.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-143">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme <major>.<minor>.<patch>. Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="e7c1a-144">Le premier élément dans la liste Intellisense (0.4.5 dans l’exemple ci-dessus) est considéré comme la dernière version stable du package.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="e7c1a-145">Le symbole du signe insertion (^) correspond à la version majeure la plus récente et le tilde (~) correspond à la version mineure la plus récente.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="e7c1a-146">Consultez le [référence d’analyseur NPM semver version](https://www.npmjs.com/package/semver) comme guide pour l’expressivité complète qui fournit des SemVer.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="e7c1a-147">Ajouter plus de dépendances pour charger des grunt-cotisation -\* packages pour *propre*, *jshint*, *concat*, *uglify*et *espion* comme indiqué dans l’exemple ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="e7c1a-148">Les versions n’avez pas besoin de faire correspondre l’exemple.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-148">The versions do not need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="e7c1a-149">Enregistrer le *package.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-149">Save the *package.json* file.</span></span>

<span data-ttu-id="e7c1a-150">Les packages pour chaque élément devDependencies télécharge, ainsi que tous les fichiers requis par chaque package.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-150">The packages for each devDependencies item will download, along with any files that each package requires.</span></span> <span data-ttu-id="e7c1a-151">Vous pouvez trouver les fichiers de package dans le `node_modules` répertoire en activant la **afficher tous les fichiers** bouton dans l’Explorateur de solutions.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-151">You can find the package files in the `node_modules` directory by enabling the **Show All Files** button in the Solution Explorer.</span></span>

![Grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="e7c1a-153">Si vous le souhaitez, vous pouvez restaurer manuellement les dépendances dans l’Explorateur de solutions en cliquant sur `Dependencies\NPM` et en sélectionnant le **restaurer les Packages** option de menu.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-153">If you need to, you can manually restore dependencies in Solution Explorer by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![restaurer les packages](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="e7c1a-155">Configuration de Grunt</span><span class="sxs-lookup"><span data-stu-id="e7c1a-155">Configuring Grunt</span></span>

<span data-ttu-id="e7c1a-156">Grunt est configuré à l’aide d’un manifeste nommé *Gruntfile.js* qui définit, charge et enregistre les tâches que vous peuvent exécuter manuellement ou configurés pour s’exécuter automatiquement en fonction des événements dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1.  <span data-ttu-id="e7c1a-157">Cliquez sur le projet et sélectionnez **Ajouter > nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-157">Right-click the project and select **Add > New Item**.</span></span> <span data-ttu-id="e7c1a-158">Sélectionnez le **fichier de Configuration de Grunt** option, laissez le nom par défaut, *Gruntfile.js*, puis cliquez sur le **ajouter** bouton.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-158">Select the **Grunt Configuration file** option, leave the default name, *Gruntfile.js*, and click the **Add** button.</span></span>

    <span data-ttu-id="e7c1a-159">Le code initial inclut une définition de module et le `grunt.initConfig()` (méthode).</span><span class="sxs-lookup"><span data-stu-id="e7c1a-159">The initial code includes a module definition and the `grunt.initConfig()` method.</span></span> <span data-ttu-id="e7c1a-160">Le `initConfig()` est utilisé pour définir les options pour chaque package, et le reste du module est chargés et inscrire des tâches.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-160">The `initConfig()` is used to set options for each package, and the remainder of the module will load and register tasks.</span></span>
    
    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
      });
    };
    ```

2. <span data-ttu-id="e7c1a-161">À l’intérieur de la `initConfig()` (méthode), ajouter des options pour le `clean` comme indiqué dans l’exemple de tâches *Gruntfile.js* ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-161">Inside the `initConfig()` method, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="e7c1a-162">La tâche clean accepte un tableau de chaînes de répertoire.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-162">The clean task accepts an array of directory strings.</span></span> <span data-ttu-id="e7c1a-163">Cette tâche supprime les fichiers de wwwroot/lib et supprime le répertoire entier/temp.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-163">This task removes files from wwwroot/lib and removes the entire /temp directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

3. <span data-ttu-id="e7c1a-164">Sous la méthode initConfig(), ajoutez un appel à `grunt.loadNpmTasks()`.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-164">Below the initConfig() method, add a call to `grunt.loadNpmTasks()`.</span></span> <span data-ttu-id="e7c1a-165">Cela facilitera la tâche exécutable à partir de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

4. <span data-ttu-id="e7c1a-166">Enregistrer *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="e7c1a-167">Le fichier doit ressembler à la capture d’écran ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-167">The file should look something like the screenshot below.</span></span>

    ![gruntfile initiale](using-grunt/_static/gruntfile-js-initial.png)

5. <span data-ttu-id="e7c1a-169">Avec le bouton droit *Gruntfile.js* et sélectionnez **Task Runner Explorer** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="e7c1a-170">La fenêtre Explorateur d’exécuteur de tâche s’ouvre.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-170">The Task Runner Explorer window will open.</span></span>

    ![menu de l’Explorateur d’exécuteur de tâche](using-grunt/_static/task-runner-explorer-menu.png)

6. <span data-ttu-id="e7c1a-172">Vérifiez que `clean` s’affiche sous **tâches** dans l’Explorateur d’exécuteur de tâche.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-172">Verify that `clean` shows under **Tasks** in the Task Runner Explorer.</span></span>

    ![liste des tâches tâches runner explorer](using-grunt/_static/task-runner-explorer-tasks.png)

7. <span data-ttu-id="e7c1a-174">Avec le bouton droit de la tâche clean et sélectionnez **exécuter** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="e7c1a-175">Une fenêtre de commande affiche la progression de la tâche.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-175">A command window displays progress of the task.</span></span>

    ![tâche de nettoyage de tâche runner explorer exécuter](using-grunt/_static/task-runner-explorer-run-clean.png)
    
    > [!NOTE]
    > <span data-ttu-id="e7c1a-177">Il n’existe aucun fichier ou répertoire pour nettoyer encore.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="e7c1a-178">Si vous le souhaitez, vous pouvez les créer manuellement dans l’Explorateur de solutions et puis exécutez la tâche clean qu’un test.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>
    
8. <span data-ttu-id="e7c1a-179">Dans la méthode initConfig(), ajoutez une entrée pour `concat` en utilisant le code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-179">In the initConfig() method, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="e7c1a-180">Le `src` tableau de propriétés répertorie les fichiers à combiner, dans l’ordre qu’ils doivent être combinées.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="e7c1a-181">Le `dest` propriété affecte le chemin d’accès au fichier combiné qui est généré.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-181">The `dest` property assigns the path to the combined file that is produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```
    
    > [!NOTE]
    > <span data-ttu-id="e7c1a-182">Le `all` propriété dans le code ci-dessus est le nom d’une cible.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="e7c1a-183">Les cibles sont utilisés dans certaines tâches Grunt pour autoriser plusieurs environnements de build.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="e7c1a-184">Vous pouvez afficher les cibles intégrées à l’aide d’Intellisense ou affecter vos propres.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-184">You can view the built-in targets using Intellisense or assign your own.</span></span>
    
9. <span data-ttu-id="e7c1a-185">Ajouter le `jshint` de tâches en utilisant le code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="e7c1a-186">L’utilitaire de la qualité du code jshint est exécuté sur tous les fichiers JavaScript trouvés dans le répertoire temporaire.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-186">The jshint code-quality utility is run against every JavaScript file found in the temp directory.</span></span>
    
    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="e7c1a-187">L’option «-W069 » est une erreur générée par jshint lorsque JavaScript utilise crochet de syntaxe pour attribuer une propriété au lieu de la notation par points, c'est-à-dire `Tastes["Sweet"]` au lieu de `Tastes.Sweet`.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="e7c1a-188">L’option désactive l’avertissement pour autoriser le reste du processus pour continuer.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

10.  <span data-ttu-id="e7c1a-189">Ajouter le `uglify` de tâches en utilisant le code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="e7c1a-190">La tâche minimise le *combined.js* fichier trouvé dans le répertoire temporaire et crée le fichier de résultats en suivant la convention d’affectation de noms standard wwwroot/lib * \<nom de fichier\>. min.js*.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>
    
    ```javascript
    uglify: {
      all: {
        src: ['temp/combined.js'],
        dest: 'wwwroot/lib/combined.min.js'
      }
    },
    ```

11. <span data-ttu-id="e7c1a-191">Sous l’appel grunt.loadNpmTasks() qui charge grunt-cotisation nettoyer, incluent le même appel pour jshint, concat et uglify en utilisant le code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-191">Under the call grunt.loadNpmTasks() that loads grunt-contrib-clean, include the same call for jshint, concat and uglify using the code below.</span></span>
    
    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

12. <span data-ttu-id="e7c1a-192">Enregistrer *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="e7c1a-193">Le fichier doit ressembler à l’exemple ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-193">The file should look something like the example below.</span></span>

    ![exemple de fichier grunt terminée](using-grunt/_static/gruntfile-js-complete.png)

13. <span data-ttu-id="e7c1a-195">Notez que la liste des tâches de l’Explorateur d’exécuteur tâche inclut `clean`, `concat`, `jshint` et `uglify` tâches.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-195">Notice that the Task Runner Explorer Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="e7c1a-196">Exécutez chaque tâche dans l’ordre et observez les résultats dans l’Explorateur de solutions.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-196">Run each task in order and observe the results in Solution Explorer.</span></span> <span data-ttu-id="e7c1a-197">Chaque tâche doit s’exécuter sans erreurs.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-197">Each task should run without errors.</span></span>
    
    ![Explorateur d’exécuteur de tâche exécuter chaque tâche.](using-grunt/_static/task-runner-explorer-run-each-task.png)
    
    <span data-ttu-id="e7c1a-199">La tâche concat crée un *combined.js* de fichier et le place dans le répertoire temporaire.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="e7c1a-200">La tâche jshint simplement s’exécute et ne génère pas sortie.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-200">The jshint task simply runs and doesn’t produce output.</span></span> <span data-ttu-id="e7c1a-201">La tâche uglify crée un nouveau *combined.min.js* de fichier et le place dans wwwroot/lib.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-201">The uglify task creates a new *combined.min.js* file and places it into wwwroot/lib.</span></span> <span data-ttu-id="e7c1a-202">Une fois terminée, la solution doit ressembler à la capture d’écran ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="e7c1a-202">On completion, the solution should look something like the screenshot below:</span></span>
    
    ![une fois toutes les tâches de l’Explorateur de solutions](using-grunt/_static/solution-explorer-after-all-tasks.png)
    
    > [!NOTE]
    > <span data-ttu-id="e7c1a-204">Pour plus d’informations sur les options pour chaque package, visitez [https://www.npmjs.com/](https://www.npmjs.com/) et recherche le nom du package dans la zone de recherche dans la page principale.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="e7c1a-205">Par exemple, vous pouvez rechercher le package nettoyer grunt-cotisation pour obtenir un lien de la documentation qui décrit tous les paramètres.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="e7c1a-206">Récapitulons</span><span class="sxs-lookup"><span data-stu-id="e7c1a-206">All together now</span></span>

<span data-ttu-id="e7c1a-207">Utilisez le Grunt `registerTask()` méthode à exécuter une série de tâches dans une séquence particulière.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="e7c1a-208">Par exemple, pour exécuter l’exemple, les étapes décrites plus haut dans la commande clean -> concat -> jshint -> uglify, ajoutez le code ci-dessous au module.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="e7c1a-209">Le code doit être ajouté au même niveau que les appels loadNpmTasks(), à l’extérieur initConfig.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="e7c1a-210">La nouvelle tâche s’affiche dans l’Explorateur d’exécuteur de tâche sous tâches de l’Alias.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="e7c1a-211">Vous pouvez avec le bouton droit et l’exécuter comme vous le feriez autres tâches.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="e7c1a-212">Le `all` tâche exécutera `clean`, `concat`, `jshint` et `uglify`, dans l’ordre.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![tâches de grunt d’alias](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="e7c1a-214">La surveillance des modifications</span><span class="sxs-lookup"><span data-stu-id="e7c1a-214">Watching for changes</span></span>

<span data-ttu-id="e7c1a-215">A `watch` tâche surveille les fichiers et répertoires.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="e7c1a-216">L’observation déclenche automatiquement des tâches s’il détecte des modifications.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="e7c1a-217">Ajoutez le code ci-dessous pour initConfig pour surveiller les modifications apportées aux \*fichiers .js dans le répertoire TypeScript.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="e7c1a-218">Si un fichier JavaScript est modifié, `watch` s’exécutera la `all` tâche.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="e7c1a-219">Ajoutez un appel à `loadNpmTasks()` pour afficher la `watch` tâche dans l’Explorateur d’exécuteur de tâche.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="e7c1a-220">Avec le bouton droit de la tâche de suivi dans l’Explorateur d’exécuteur de tâche et sélectionnez Exécuter dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="e7c1a-221">La fenêtre de commande qui affiche l’exécution des tâches Espion affiche un paramètre « en attente... »</span><span class="sxs-lookup"><span data-stu-id="e7c1a-221">The command window that shows the watch task running will display a "Waiting…"</span></span> <span data-ttu-id="e7c1a-222">« Operation Not Found! ».</span><span class="sxs-lookup"><span data-stu-id="e7c1a-222">message.</span></span> <span data-ttu-id="e7c1a-223">Ouvrez un des fichiers TypeScript, ajoutez un espace, puis enregistrez le fichier.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-223">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="e7c1a-224">Cela sera déclencher la tâche de surveillance et les autres tâches à exécuter dans l’ordre.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-224">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="e7c1a-225">La capture d’écran ci-dessous montre un exemple d’exécution.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-225">The screenshot below shows a sample run.</span></span>

![résultats des tâches en cours d’exécution](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="e7c1a-227">Liaison à des événements Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e7c1a-227">Binding to Visual Studio events</span></span>

<span data-ttu-id="e7c1a-228">Sauf si vous souhaitez démarrer manuellement vos tâches chaque fois que vous travaillez dans Visual Studio, vous pouvez lier des tâches **avant de générer**, **après génération**, **Clean**, et ** Projet ouvert** événements.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-228">Unless you want to manually start your tasks every time you work in Visual Studio, you can bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="e7c1a-229">Nous allons lier `watch` afin qu’elle s’exécute chaque fois que Visual Studio s’ouvre.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-229">Let’s bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="e7c1a-230">Dans l’Explorateur d’exécuteur de tâche, avec le bouton droit de la tâche de surveillance, puis sélectionnez **liaisons > ouverture du projet** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-230">In Task Runner Explorer, right-click the watch task and select **Bindings > Project Open** from the context menu.</span></span>

![lier une tâche à l’ouverture du projet](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="e7c1a-232">Décharger et recharger le projet.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-232">Unload and reload the project.</span></span> <span data-ttu-id="e7c1a-233">Lorsque le projet a été chargé à nouveau, la tâche espion commence à s’exécuter automatiquement.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-233">When the project loads again, the watch task will start running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="e7c1a-234">Résumé</span><span class="sxs-lookup"><span data-stu-id="e7c1a-234">Summary</span></span>

<span data-ttu-id="e7c1a-235">Grunt est un exécuteur de tâches puissant qui peut être utilisé pour automatiser la plupart des tâches de génération de client.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-235">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="e7c1a-236">Grunt tire parti de NPM pour fournir ses packages, fonctionnalités et outils d’intégration avec Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-236">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="e7c1a-237">Explorateur d’exécuteur de tâche de Visual Studio détecte les modifications apportées aux fichiers de configuration et fournit une interface pratique pour exécuter des tâches, afficher les tâches en cours d’exécution et lier des tâches à des événements de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e7c1a-237">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7c1a-238">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e7c1a-238">Additional resources</span></span>

   * [<span data-ttu-id="e7c1a-239">Utilisation de Gulp</span><span class="sxs-lookup"><span data-stu-id="e7c1a-239">Using Gulp</span></span>](using-gulp.md)
