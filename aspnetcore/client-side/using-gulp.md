---
title: Utilisation de Gulp dans ASP.NET Core
author: rick-anderson
description: "Découvrez comment utiliser Gulp dans ASP.NET Core."
manager: wpickett
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 02/28/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: client-side/using-gulp
ms.openlocfilehash: f091370bc85a37eeaac1291a2fdc6ea85164f148
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="introduction-to-using-gulp-in-aspnet-core"></a>Introduction à l’utilisation de Gulp dans ASP.NET Core 

Par [Erik Reitan](https://github.com/Erikre), [Scott Addie](https://scottaddie.com), [Michel Roth](https://github.com/danroth27), et [Shayne Boyer](https://twitter.com/spboyer)

Dans une application web moderne classique, le processus de génération peut :

* Regroupement et minimiser les fichiers JavaScript et CSS.
* Exécuter les outils pour appeler les groupement et la minimisation des tâches avant chaque build.
* Compilez inférieur ou SASS fichiers CSS.
* Compiler des fichiers JavaScript CoffeeScript ou TypeScript.

A *exécuteur de tâches* est un outil qui automatise les tâches de développement de routine et bien plus encore. Visual Studio fournit la prise en charge intégrée pour deux exécuteurs de tâches de base de JavaScript populaires : [Gulp](https://gulpjs.com/) et [Grunt](using-grunt.md).

## <a name="gulp"></a>gulp

Gulp est un toolkit basées JavaScript de build en continu pour le code côté client. Il est couramment utilisé pour diffuser des fichiers côté client via une série de processus de déclenchement d’un événement spécifique dans un environnement de génération. Par exemple, Gulp permettre être utilisé pour automatiser [groupement et la minimisation](bundling-and-minification.md) ou le nettoyage d’un environnement de développement avant une nouvelle build.

Un ensemble de tâches de Gulp est défini dans *gulpfile.js*. Le code JavaScript suivant inclut les modules Gulp et spécifie les chemins d’accès des fichiers référencés dans les tâches à venir :

```javascript
/// <binding Clean='clean' />
"use strict";

var gulp = require("gulp"),
  rimraf = require("rimraf"),
  concat = require("gulp-concat"),
  cssmin = require("gulp-cssmin"),
  uglify = require("gulp-uglify");

var paths = {
  webroot: "./wwwroot/"
};

paths.js = paths.webroot + "js/**/*.js";
paths.minJs = paths.webroot + "js/**/*.min.js";
paths.css = paths.webroot + "css/**/*.css";
paths.minCss = paths.webroot + "css/**/*.min.css";
paths.concatJsDest = paths.webroot + "js/site.min.js";
paths.concatCssDest = paths.webroot + "css/site.min.css";
```

Le code ci-dessus spécifie quels modules de nœud sont requises. Le `require` fonction importe chaque module afin que les tâches dépendantes peuvent utiliser leurs fonctionnalités. Chaque module importé est assigné à une variable. Les modules peuvent être situés par nom ou chemin d’accès. Dans cet exemple, les modules nommé `gulp`, `rimraf`, `gulp-concat`, `gulp-cssmin`, et `gulp-uglify` sont récupérés par nom. En outre, une série de chemins d’accès sont créés afin que les emplacements des fichiers CSS et JavaScript peuvent être réutilisées et référencées dans les tâches. Le tableau suivant fournit des descriptions des modules inclus dans *gulpfile.js*.

| Nom du module | Description |
| ----------- | ----------- |
| gulp        | Gulp de diffusion en continu de système de génération. Pour plus d’informations, consultez [gulp](https://www.npmjs.com/package/gulp). |
| rimraf      | Un module de suppression de nœud. Pour plus d’informations, consultez [rimraf](https://www.npmjs.com/package/rimraf). |
| gulp-concat | Un module qui concatène des fichiers en fonction de caractère de saut de ligne du système d’exploitation. Pour plus d’informations, consultez [gulp-concat](https://www.npmjs.com/package/gulp-concat). |
| gulp-cssmin | Un module qui minimise les fichiers CSS. Pour plus d’informations, consultez [gulp-cssmin](https://www.npmjs.com/package/gulp-cssmin). |
| gulp-uglify | Un module qui minimise *.js* fichiers. Pour plus d’informations, consultez [gulp-uglify](https://www.npmjs.com/package/gulp-uglify). |

Une fois que les modules requis sont importés, les tâches peuvent être spécifiés. Ici, il existe six tâches inscrit, représenté par le code suivant :

```javascript
gulp.task("clean:js", function (cb) {
  rimraf(paths.concatJsDest, cb);
});

gulp.task("clean:css", function (cb) {
  rimraf(paths.concatCssDest, cb);
});

gulp.task("clean", ["clean:js", "clean:css"]);

gulp.task("min:js", function () {
  return gulp.src([paths.js, "!" + paths.minJs], { base: "." })
    .pipe(concat(paths.concatJsDest))
    .pipe(uglify())
    .pipe(gulp.dest("."));
});

gulp.task("min:css", function () {
  return gulp.src([paths.css, "!" + paths.minCss])
    .pipe(concat(paths.concatCssDest))
    .pipe(cssmin())
    .pipe(gulp.dest("."));
});

gulp.task("min", ["min:js", "min:css"]);
```

Le tableau suivant fournit une explication des tâches spécifié dans le code ci-dessus :

|Nom de la tâche|Description|
|--- |--- |
|nettoyer : js|Une tâche qui utilise le module de suppression de nœud rimraf pour supprimer la version réduite du fichier site.js.|
|nettoyer : css|Une tâche qui utilise le module de suppression de nœud rimraf pour supprimer la version réduite du fichier site.css.|
|Nettoyer|Une tâche qui appelle le `clean:js` tâche, suivi par le `clean:css` tâche.|
|min:js|Une tâche qui minimise et concatène tous les fichiers .js dans le dossier js. Le. les fichiers min.js sont exclus.|
|min:CSS|Une tâche qui minimise et concatène tous les fichiers .css dans le dossier css. Le. les fichiers min.css sont exclus.|
|min|Une tâche qui appelle le `min:js` tâche, suivi par le `min:css` tâche.|

## <a name="running-default-tasks"></a>Exécution des tâches par défaut

Si vous n’avez pas déjà créé une application Web, créez un nouveau projet d’Application Web ASP.NET dans Visual Studio.

1.  Ajouter un nouveau fichier JavaScript à votre projet et nommez-le *gulpfile.js*, puis copiez le code suivant.

    ```javascript
    /// <binding Clean='clean' />
    "use strict";
    
    var gulp = require("gulp"),
      rimraf = require("rimraf"),
      concat = require("gulp-concat"),
      cssmin = require("gulp-cssmin"),
      uglify = require("gulp-uglify");
    
    var paths = {
      webroot: "./wwwroot/"
    };
    
    paths.js = paths.webroot + "js/**/*.js";
    paths.minJs = paths.webroot + "js/**/*.min.js";
    paths.css = paths.webroot + "css/**/*.css";
    paths.minCss = paths.webroot + "css/**/*.min.css";
    paths.concatJsDest = paths.webroot + "js/site.min.js";
    paths.concatCssDest = paths.webroot + "css/site.min.css";
    
    gulp.task("clean:js", function (cb) {
      rimraf(paths.concatJsDest, cb);
    });
    
    gulp.task("clean:css", function (cb) {
      rimraf(paths.concatCssDest, cb);
    });
    
    gulp.task("clean", ["clean:js", "clean:css"]);
    
    gulp.task("min:js", function () {
      return gulp.src([paths.js, "!" + paths.minJs], { base: "." })
        .pipe(concat(paths.concatJsDest))
        .pipe(uglify())
        .pipe(gulp.dest("."));
    });
    
    gulp.task("min:css", function () {
      return gulp.src([paths.css, "!" + paths.minCss])
        .pipe(concat(paths.concatCssDest))
        .pipe(cssmin())
        .pipe(gulp.dest("."));
    });
    
    gulp.task("min", ["min:js", "min:css"]);
    ```

2.  Ouvrez le *package.json* fichier (ajoutez si ce n’est pas il) et ajoutez le code suivant.

    ```json
    {
      "devDependencies": {
        "gulp": "3.9.1",
        "gulp-concat": "2.6.1",
        "gulp-cssmin": "0.1.7",
        "gulp-uglify": "2.0.1",
        "rimraf": "2.6.1"
      }
    }
    ```

3.  Dans **l’Explorateur de solutions**, avec le bouton droit *gulpfile.js*, puis sélectionnez **Task Runner Explorer**.
    
    ![Ouvrez l’Explorateur d’exécuteur de tâche à partir de l’Explorateur de solutions](using-gulp/_static/02-SolutionExplorer-TaskRunnerExplorer.png)
    
    **Explorateur d’exécuteur de tâches** affiche la liste des tâches de Gulp. (Vous devrez peut-être cliquez sur le **Actualiser** bouton qui apparaît à gauche du nom du projet.)
    
    ![Explorateur d’exécuteur de tâche](using-gulp/_static/03-TaskRunnerExplorer.png)

4.  Sous **tâches** dans **Task Runner Explorer**, avec le bouton droit **propre**, puis sélectionnez **exécuter** dans le menu contextuel.

    ![Tâche de nettoyage d’Explorateur d’exécuteur de tâche](using-gulp/_static/04-TaskRunner-clean.png)

    **Explorateur d’exécuteur de tâches** créera un nouvel onglet nommé **propre** et exécuter la tâche clean telle qu’elle est définie dans *gulpfile.js*.

5.  Avec le bouton droit le **propre** de tâches, puis sélectionnez **liaisons** > **avant de générer**.

    ![Liaison BeforeBuild Task Runner Explorer](using-gulp/_static/05-TaskRunner-BeforeBuild.png)

    Le **avant de générer** liaison configure la tâche clean à s’exécuter automatiquement avant chaque génération du projet.

Les liaisons défini avec **Task Runner Explorer** sont stockés sous la forme d’un commentaire en haut de votre *gulpfile.js* et sont effectifs seulement dans Visual Studio. Une alternative qui ne nécessite pas Visual Studio consiste à configurer l’exécution automatique des tâches de gulp dans votre *.csproj* fichier. Par exemple, placez cela votre *.csproj* fichier :

```xml
<Target Name="MyPreCompileTarget" BeforeTargets="Build">
  <Exec Command="gulp clean" />
</Target>
```

Maintenant la tâche de nettoyage est exécutée lorsque vous exécutez le projet dans Visual Studio ou à partir d’une invite de commandes à l’aide de la `dotnet run` commande (exécuter `npm install` premier).

## <a name="defining-and-running-a-new-task"></a>Définition et une nouvelle tâche en cours d’exécution

Pour définir une nouvelle tâche de choses, modifier *gulpfile.js*.

1.  Ajoutez le code JavaScript suivant à la fin de *gulpfile.js*:

    ```javascript
    gulp.task("first", function () {
      console.log('first task! <-----');
    });
    ```

    Cette tâche est nommée `first`, et il affiche simplement une chaîne.

2.  Enregistrer *gulpfile.js*.

3.  Dans **l’Explorateur de solutions**, avec le bouton droit *gulpfile.js*, puis sélectionnez *Task Runner Explorer*.

4.  Dans **Task Runner Explorer**, avec le bouton droit **première**, puis sélectionnez **exécuter**.

    ![Exécutez la première tâche Task Runner Explorer](using-gulp/_static/06-TaskRunner-First.png)

    Vous verrez que le texte de sortie s’affiche. Si vous êtes intéressé par exemples basés sur un scénario courant, consultez les recettes de Gulp.

## <a name="defining-and-running-tasks-in-a-series"></a>Définition et l’exécution de tâches dans une série

Lorsque vous exécutez plusieurs tâches, les tâches exécutées simultanément par défaut. Toutefois, si vous avez besoin exécuter des tâches dans un ordre spécifique, vous devez spécifier que chaque tâche est terminé, ainsi que les tâches qui dépendent de l’achèvement d’une autre tâche.

1.  Pour définir une série de tâches à exécuter dans l’ordre, remplacez le `first` les tâches que vous avez ajouté ci-dessus *gulpfile.js* avec les éléments suivants :

    ```javascript
    gulp.task("series:first", function () {
      console.log('first task! <-----');
    });
 
    gulp.task("series:second", ["series:first"], function () {
      console.log('second task! <-----');
    });
 
    gulp.task("series", ["series:first", "series:second"], function () {});
    ```
 
    Vous disposez maintenant de trois tâches : `series:first`, `series:second`, et `series`. Le `series:second` tâche inclut un deuxième paramètre qui spécifie un tableau de tâches à exécuter et sont terminés avant du `series:second` tâche exécutera. Comme indiqué dans le code ci-dessus, seule la `series:first` tâche doit être réalisée avant le `series:second` tâche exécutera.

2.  Enregistrer *gulpfile.js*.

3.  Dans **l’Explorateur de solutions**, avec le bouton droit *gulpfile.js* et sélectionnez **Task Runner Explorer** si elle n’est pas déjà ouverte.

4.  Dans **Task Runner Explorer**, avec le bouton droit **série** et sélectionnez **exécuter**.

    ![Explorateur d’exécuteur de tâches exécuter la tâche de série](using-gulp/_static/07-TaskRunner-Series.png)

## <a name="intellisense"></a>IntelliSense

IntelliSense fournit l’exécution de code, des descriptions de paramètre et d’autres fonctionnalités pour améliorer la productivité et réduire les erreurs. Les tâches de gulp sont écrites en JavaScript ; Par conséquent, IntelliSense peut fournir une assistance lors du développement. Lorsque vous travaillez avec JavaScript, IntelliSense répertorie les objets, les fonctions, les propriétés et paramètres qui sont disponibles en fonction de votre contexte actuel. Sélectionnez une option de programmation dans la liste contextuelle fournie par IntelliSense pour compléter le code.

![gulp IntelliSense](using-gulp/_static/08-IntelliSense.png)

Pour plus d’informations sur IntelliSense, consultez [JavaScript IntelliSense](https://docs.microsoft.com/visualstudio/ide/javascript-intellisense).

## <a name="development-staging-and-production-environments"></a>Environnements de développement, intermédiaire et de production

Lorsque Gulp est utilisé pour optimiser les fichiers côté client pour intermédiaire et de production, les fichiers traités sont enregistrés dans un emplacement intermédiaire et de production local. Le *_Layout.cshtml* fichier utilise le **environnement** balise d’assistance pour fournir deux versions différentes de fichiers CSS. Une version des fichiers CSS est pour le développement et l’autre version est optimisée pour la préparation et de production. Dans Visual Studio 2017, lorsque vous modifiez le **ASPNETCORE_ENVIRONMENT** variable d’environnement `Production`, Visual Studio génère l’application Web et un lien vers les fichiers CSS sous forme réduites. La balise suivante montre la **environnement** programmes d’assistance qui contient des balises de liens à la balise la `Development` CSS le réduite et fichiers `Staging, Production` fichiers CSS.

```html
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
    <script src="~/js/site.js" asp-append-version="true"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.2.0.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery"
            crossorigin="anonymous"
            integrity="sha384-K+ctZQ+LL8q6tP7I94W+qzQsfRV2a+AfHIi9k8z8l9ggpc8X+Ytst4yBo/hH+8Fk">
    </script>
    <script src="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/bootstrap.min.js"
            asp-fallback-src="~/lib/bootstrap/dist/js/bootstrap.min.js"
            asp-fallback-test="window.jQuery && window.jQuery.fn && window.jQuery.fn.modal"
            crossorigin="anonymous"
            integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa">
    </script>
    <script src="~/js/site.min.js" asp-append-version="true"></script>
</environment>
```

## <a name="switching-between-environments"></a>Basculer entre les environnements

Pour basculer entre la compilation pour des environnements différents, vous devez modifier le **ASPNETCORE_ENVIRONMENT** valeur de la variable d’environnement.

1.  Dans **Task Runner Explorer**, vérifiez que le **min** tâche a été défini pour exécuter **avant de générer**.

2.  Dans **l’Explorateur de solutions**, cliquez sur le nom du projet et sélectionnez **propriétés**.

    La feuille de propriétés de l’application Web s’affiche.

3.  Cliquez sur l’onglet **Déboguer**.

4.  Définir la valeur de la **: environnement d’hébergement** variable d’environnement `Production`.

5.  Appuyez sur **F5** pour exécuter l’application dans un navigateur.

6.  Dans la fenêtre du navigateur, avec le bouton droit de la page, puis sélectionnez **afficher la Source** pour afficher le code HTML de la page.

    Notez que les liens de la feuille de style pointent vers les fichiers CSS réduites.

7.  Fermez le navigateur pour arrêter l’application Web.

8.  Dans Visual Studio, revenir à la feuille de propriétés de l’application Web et modifier la **: environnement d’hébergement** variable d’environnement pour revenir `Development`.

9.  Appuyez sur **F5** réexécuter l’application dans un navigateur.

10. Dans la fenêtre du navigateur, avec le bouton droit de la page, puis sélectionnez **afficher la Source** pour afficher le code HTML de la page.

    Notez que les liens de la feuille de style pointent vers les versions unminified des fichiers CSS.

Pour plus d’informations sur les environnements dans ASP.NET Core, consultez [fonctionne avec plusieurs environnements](../fundamentals/environments.md).

## <a name="task-and-module-details"></a>Détails des tâches et de module

Une tâche Gulp est inscrit avec un nom de fonction. Vous pouvez spécifier des dépendances si d’autres tâches doivent être exécutées avant que la tâche actuelle. Les fonctions supplémentaires permettent à exécuter et surveiller les tâches de choses, ainsi que de définir la source (*src*) et de destination (*dest*) des fichiers en cours de modification. Les fonctions API de Gulp principales sont les suivantes :

|Gulp (fonction)|Syntaxe|Description|
|---   |--- |--- |
|task  |`gulp.task(name[, deps], fn) { }`|Le `task` fonction crée une tâche. Le `name` paramètre définit le nom de la tâche. Le `deps` paramètre contient un tableau de tâches à effectuer avant d’exécuter cette tâche. Le `fn` paramètre représente une fonction de rappel qui effectue les opérations de la tâche.|
|Espion |`gulp.watch(glob [, opts], tasks) { }`|Le `watch` fonction surveille les fichiers et exécute des tâches lorsqu’une modification de fichier se produit. Le `glob` paramètre est un `string` ou `array` qui détermine les fichiers à surveiller. Le `opts` paramètre fournit la surveillance des options de fichiers supplémentaires.|
|src   |`gulp.src(globs[, options]) { }`|Le `src` fonction fournit des fichiers qui correspondent aux valeurs glob. Le `glob` paramètre est un `string` ou `array` qui détermine les fichiers à lire. Le `options` paramètre fournit des options de fichiers supplémentaires.|
|destination  |`gulp.dest(path[, options]) { }`|Le `dest` fonction définit un emplacement dans lequel les fichiers peuvent être écrites. Le `path` paramètre est une chaîne ou une fonction qui détermine le dossier de destination. Le `options` paramètre est un objet qui spécifie les options de dossier de sortie.|

Pour plus d’informations de référence API de Gulp supplémentaires, consultez [Gulp des API de documents](https://github.com/gulpjs/gulp/blob/master/docs/API.md).

## <a name="gulp-recipes"></a>Gulp recettes

La Communauté Gulp fournit Gulp [recettes](https://github.com/gulpjs/gulp/blob/master/docs/recipes/README.md). Ces recettes sont constituées des tâches de Gulp pour résoudre des scénarios courants.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Documentation de gulp](https://github.com/gulpjs/gulp/blob/master/docs/README.md)
* [Groupement et la minimisation dans ASP.NET Core](bundling-and-minification.md)
* [À l’aide de Grunt dans ASP.NET Core](using-grunt.md)
