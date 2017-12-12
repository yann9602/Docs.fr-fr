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
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="using-grunt-in-aspnet-core"></a>À l’aide de Grunt dans ASP.NET Core 

Par [Noel riz](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)

Grunt est un exécuteur de tâches JavaScript qui automatise une minimisation du script, la compilation TypeScript, outils « lint » qualité du code, les processeurs avant CSS et pratiquement n’importe quel, répétitive qui doit effectuer pour prendre en charge le développement de clients. Grunt est entièrement pris en charge dans Visual Studio, bien que les modèles de projet ASP.NET utilisent Gulp par défaut (voir [à l’aide de Gulp](using-gulp.md)).

Cet exemple utilise un projet ASP.NET Core vide comme point de départ, pour montrer comment automatiser le processus de génération de client à partir de zéro.

L’exemple terminé nettoie le répertoire de déploiement cible, combine les fichiers JavaScript, vérifie la qualité du code, condense le contenu du fichier JavaScript et déploie sur la racine de votre application web. Nous allons utiliser les packages suivants :

* **Grunt**: package de runner Grunt la tâche.

* **Grunt-cotisation nettoyer**: un plug-in qui supprime des fichiers ou répertoires.

* **Grunt-cotisation-jshint**: un plug-in qui passe en revue la qualité du code JavaScript.

* **Grunt-cotisation-concat**: un plug-in qui joint les fichiers dans un seul fichier.

* **Grunt-cotisation uglify**: un plug-in qui minimise le JavaScript pour réduire la taille.

* **Grunt-cotisation-espion**: un plug-in qui surveille l’activité des fichiers.

## <a name="preparing-the-application"></a>Préparation de l’application

Pour commencer, configurez une nouvelle application web vide et ajouter des fichiers d’exemple TypeScript. Fichiers typeScript sont automatiquement compilés dans JavaScript à l’aide des paramètres de Visual Studio par défaut et seront notre matières premières à traiter à l’aide de Grunt.

1.  Dans Visual Studio, créez un nouveau `ASP.NET Web Application`.

2.  Dans le **nouveau projet ASP.NET** boîte de dialogue, sélectionnez le ASP.NET Core **vide** modèle et cliquez sur le bouton OK.

3.  Dans l’Explorateur de solutions, passez en revue la structure de projet. Le `\src` dossier inclut vide `wwwroot` et `Dependencies` nœuds.

    ![solution de site web vide](using-grunt/_static/grunt-solution-explorer.png)

4.  Ajouter un nouveau dossier nommé `TypeScript` à votre répertoire de projet.

5.  Avant d’ajouter des fichiers, assurez-vous que Visual Studio propose l’option ' compiler lors de l’enregistrement ' pour les fichiers TypeScript activées. *Outils > Options > Éditeur de texte > Typescript > projet*

    ![Options de paramétrage compliation automatique des fichiers TypeScript](using-grunt/_static/typescript-options.png)

6.  Avec le bouton droit le `TypeScript` directory et sélectionnez **Ajouter > nouvel élément** dans le menu contextuel. Sélectionnez le **fichier JavaScript** d’élément et nommez le fichier *Tastes.ts* (Notez le \*.ts extension). Copiez la ligne de code TypeScript ci-dessous dans le fichier (lorsque vous enregistrez, un nouveau *Tastes.js* fichier s’affiche avec la source JavaScript).
    
    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7.  Ajoutez un deuxième fichier dans le **TypeScript** active et nommez-le `Food.ts`. Copiez le code ci-dessous dans le fichier.

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

## <a name="configuring-npm"></a>Configuration de NPM

Ensuite, configurez NPM pour télécharger grunt et les tâches de grunt.

1. Dans l’Explorateur de solutions, cliquez sur le projet et sélectionnez **Ajouter > nouvel élément** dans le menu contextuel. Sélectionnez le **fichier de configuration NPM** d’élément, laissez le nom par défaut, *package.json*, puis cliquez sur le **ajouter** bouton.

2. Dans le *package.json* de fichiers, dans le `devDependencies` accolades de l’objet, entrez « des grunt ». Sélectionnez `grunt` dans Intellisense liste et appuyez sur ENTRÉE. Visual Studio le nom du package grunt de placer entre guillemets et ajouter un signe deux-points. À droite du signe deux-points, sélectionnez la dernière version stable du package à partir du haut de la liste Intellisense (appuyez sur `Ctrl-Space` si Intellisense n’apparaît pas).

    ![grun Intellisense](using-grunt/_static/devdependencies-grunt.png)
    
    > [!NOTE]
    > NPM utilise [contrôle de version sémantique](http://semver.org/) pour organiser les dépendances. Contrôle de version sémantique, également appelé SemVer, identifie les packages avec le modèle de numérotation <major>.<minor>. <patch>. IntelliSense simplifie le contrôle de version sémantique en affichant uniquement quelques choix courants. Le premier élément dans la liste Intellisense (0.4.5 dans l’exemple ci-dessus) est considéré comme la dernière version stable du package. Le symbole du signe insertion (^) correspond à la version majeure la plus récente et le tilde (~) correspond à la version mineure la plus récente. Consultez le [référence d’analyseur NPM semver version](https://www.npmjs.com/package/semver) comme guide pour l’expressivité complète qui fournit des SemVer.

3. Ajouter plus de dépendances pour charger des grunt-cotisation -\* packages pour *propre*, *jshint*, *concat*, *uglify*et *espion* comme indiqué dans l’exemple ci-dessous. Les versions n’avez pas besoin de faire correspondre l’exemple.

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

4. Enregistrer le *package.json* fichier.

Les packages pour chaque élément devDependencies télécharge, ainsi que tous les fichiers requis par chaque package. Vous pouvez trouver les fichiers de package dans le `node_modules` répertoire en activant la **afficher tous les fichiers** bouton dans l’Explorateur de solutions.

![Grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> Si vous le souhaitez, vous pouvez restaurer manuellement les dépendances dans l’Explorateur de solutions en cliquant sur `Dependencies\NPM` et en sélectionnant le **restaurer les Packages** option de menu.

![restaurer les packages](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Configuration de Grunt

Grunt est configuré à l’aide d’un manifeste nommé *Gruntfile.js* qui définit, charge et enregistre les tâches que vous peuvent exécuter manuellement ou configurés pour s’exécuter automatiquement en fonction des événements dans Visual Studio.

1.  Cliquez sur le projet et sélectionnez **Ajouter > nouvel élément**. Sélectionnez le **fichier de Configuration de Grunt** option, laissez le nom par défaut, *Gruntfile.js*, puis cliquez sur le **ajouter** bouton.

    Le code initial inclut une définition de module et le `grunt.initConfig()` (méthode). Le `initConfig()` est utilisé pour définir les options pour chaque package, et le reste du module est chargés et inscrire des tâches.
    
    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
      });
    };
    ```

2. À l’intérieur de la `initConfig()` (méthode), ajouter des options pour le `clean` comme indiqué dans l’exemple de tâches *Gruntfile.js* ci-dessous. La tâche clean accepte un tableau de chaînes de répertoire. Cette tâche supprime les fichiers de wwwroot/lib et supprime le répertoire entier/temp.

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

3. Sous la méthode initConfig(), ajoutez un appel à `grunt.loadNpmTasks()`. Cela facilitera la tâche exécutable à partir de Visual Studio.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

4. Enregistrer *Gruntfile.js*. Le fichier doit ressembler à la capture d’écran ci-dessous.

    ![gruntfile initiale](using-grunt/_static/gruntfile-js-initial.png)

5. Avec le bouton droit *Gruntfile.js* et sélectionnez **Task Runner Explorer** dans le menu contextuel. La fenêtre Explorateur d’exécuteur de tâche s’ouvre.

    ![menu de l’Explorateur d’exécuteur de tâche](using-grunt/_static/task-runner-explorer-menu.png)

6. Vérifiez que `clean` s’affiche sous **tâches** dans l’Explorateur d’exécuteur de tâche.

    ![liste des tâches tâches runner explorer](using-grunt/_static/task-runner-explorer-tasks.png)

7. Avec le bouton droit de la tâche clean et sélectionnez **exécuter** dans le menu contextuel. Une fenêtre de commande affiche la progression de la tâche.

    ![tâche de nettoyage de tâche runner explorer exécuter](using-grunt/_static/task-runner-explorer-run-clean.png)
    
    > [!NOTE]
    > Il n’existe aucun fichier ou répertoire pour nettoyer encore. Si vous le souhaitez, vous pouvez les créer manuellement dans l’Explorateur de solutions et puis exécutez la tâche clean qu’un test.
    
8. Dans la méthode initConfig(), ajoutez une entrée pour `concat` en utilisant le code ci-dessous.

    Le `src` tableau de propriétés répertorie les fichiers à combiner, dans l’ordre qu’ils doivent être combinées. Le `dest` propriété affecte le chemin d’accès au fichier combiné qui est généré.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```
    
    > [!NOTE]
    > Le `all` propriété dans le code ci-dessus est le nom d’une cible. Les cibles sont utilisés dans certaines tâches Grunt pour autoriser plusieurs environnements de build. Vous pouvez afficher les cibles intégrées à l’aide d’Intellisense ou affecter vos propres.
    
9. Ajouter le `jshint` de tâches en utilisant le code ci-dessous.

    L’utilitaire de la qualité du code jshint est exécuté sur tous les fichiers JavaScript trouvés dans le répertoire temporaire.
    
    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > L’option «-W069 » est une erreur générée par jshint lorsque JavaScript utilise crochet de syntaxe pour attribuer une propriété au lieu de la notation par points, c'est-à-dire `Tastes["Sweet"]` au lieu de `Tastes.Sweet`. L’option désactive l’avertissement pour autoriser le reste du processus pour continuer.

10.  Ajouter le `uglify` de tâches en utilisant le code ci-dessous.

    La tâche minimise le *combined.js* fichier trouvé dans le répertoire temporaire et crée le fichier de résultats en suivant la convention d’affectation de noms standard wwwroot/lib  *\<nom de fichier\>. min.js*.
    
    ```javascript
    uglify: {
      all: {
        src: ['temp/combined.js'],
        dest: 'wwwroot/lib/combined.min.js'
      }
    },
    ```

11. Sous l’appel grunt.loadNpmTasks() qui charge grunt-cotisation nettoyer, incluent le même appel pour jshint, concat et uglify en utilisant le code ci-dessous.
    
    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

12. Enregistrer *Gruntfile.js*. Le fichier doit ressembler à l’exemple ci-dessous.

    ![exemple de fichier grunt terminée](using-grunt/_static/gruntfile-js-complete.png)

13. Notez que la liste des tâches de l’Explorateur d’exécuteur tâche inclut `clean`, `concat`, `jshint` et `uglify` tâches. Exécutez chaque tâche dans l’ordre et observez les résultats dans l’Explorateur de solutions. Chaque tâche doit s’exécuter sans erreurs.
    
    ![Explorateur d’exécuteur de tâche exécuter chaque tâche.](using-grunt/_static/task-runner-explorer-run-each-task.png)
    
    La tâche concat crée un *combined.js* de fichier et le place dans le répertoire temporaire. La tâche jshint simplement s’exécute et ne génère pas sortie. La tâche uglify crée un nouveau *combined.min.js* de fichier et le place dans wwwroot/lib. Une fois terminée, la solution doit ressembler à la capture d’écran ci-dessous :
    
    ![une fois toutes les tâches de l’Explorateur de solutions](using-grunt/_static/solution-explorer-after-all-tasks.png)
    
    > [!NOTE]
    > Pour plus d’informations sur les options pour chaque package, visitez [https://www.npmjs.com/](https://www.npmjs.com/) et recherche le nom du package dans la zone de recherche dans la page principale. Par exemple, vous pouvez rechercher le package nettoyer grunt-cotisation pour obtenir un lien de la documentation qui décrit tous les paramètres.

### <a name="all-together-now"></a>Récapitulons

Utilisez le Grunt `registerTask()` méthode à exécuter une série de tâches dans une séquence particulière. Par exemple, pour exécuter l’exemple, les étapes décrites plus haut dans la commande clean -> concat -> jshint -> uglify, ajoutez le code ci-dessous au module. Le code doit être ajouté au même niveau que les appels loadNpmTasks(), à l’extérieur initConfig.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

La nouvelle tâche s’affiche dans l’Explorateur d’exécuteur de tâche sous tâches de l’Alias. Vous pouvez avec le bouton droit et l’exécuter comme vous le feriez autres tâches. Le `all` tâche exécutera `clean`, `concat`, `jshint` et `uglify`, dans l’ordre.

![tâches de grunt d’alias](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>La surveillance des modifications

A `watch` tâche surveille les fichiers et répertoires. L’observation déclenche automatiquement des tâches s’il détecte des modifications. Ajoutez le code ci-dessous pour initConfig pour surveiller les modifications apportées aux \*fichiers .js dans le répertoire TypeScript. Si un fichier JavaScript est modifié, `watch` s’exécutera la `all` tâche.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Ajoutez un appel à `loadNpmTasks()` pour afficher la `watch` tâche dans l’Explorateur d’exécuteur de tâche.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Avec le bouton droit de la tâche de suivi dans l’Explorateur d’exécuteur de tâche et sélectionnez Exécuter dans le menu contextuel. La fenêtre de commande qui affiche l’exécution des tâches Espion affiche un paramètre « en attente... » « Operation Not Found! ». Ouvrez un des fichiers TypeScript, ajoutez un espace, puis enregistrez le fichier. Cela sera déclencher la tâche de surveillance et les autres tâches à exécuter dans l’ordre. La capture d’écran ci-dessous montre un exemple d’exécution.

![résultats des tâches en cours d’exécution](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Liaison à des événements Visual Studio

Sauf si vous souhaitez démarrer manuellement vos tâches chaque fois que vous travaillez dans Visual Studio, vous pouvez lier des tâches **avant de générer**, **après génération**, **Clean**, et  **Projet ouvert** événements.

Nous allons lier `watch` afin qu’elle s’exécute chaque fois que Visual Studio s’ouvre. Dans l’Explorateur d’exécuteur de tâche, avec le bouton droit de la tâche de surveillance, puis sélectionnez **liaisons > ouverture du projet** dans le menu contextuel.

![lier une tâche à l’ouverture du projet](using-grunt/_static/bindings-project-open.png)

Décharger et recharger le projet. Lorsque le projet a été chargé à nouveau, la tâche espion commence à s’exécuter automatiquement.

## <a name="summary"></a>Résumé

Grunt est un exécuteur de tâches puissant qui peut être utilisé pour automatiser la plupart des tâches de génération de client. Grunt tire parti de NPM pour fournir ses packages, fonctionnalités et outils d’intégration avec Visual Studio. Explorateur d’exécuteur de tâche de Visual Studio détecte les modifications apportées aux fichiers de configuration et fournit une interface pratique pour exécuter des tâches, afficher les tâches en cours d’exécution et lier des tâches à des événements de Visual Studio.

## <a name="additional-resources"></a>Ressources supplémentaires

   * [Utilisation de Gulp](using-gulp.md)
