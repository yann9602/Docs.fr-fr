---
title: "Inférieur, Sass et la police impressionnant dans ASP.NET Core"
author: ardalis
description: "Découvrez comment utiliser inférieur, Sass et police impressionnant dans les applications ASP.NET Core."
ms.author: tdykstra
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/less-sass-fa
ms.openlocfilehash: c3a53d6118a72c00d61d9139b05325fd1cbd53da
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="introduction-to-styling-applications-with-less-sass-and-font-awesome-in-aspnet-core"></a>Introduction aux applications de style avec moins, Sass et police impressionnant dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/)

Les utilisateurs d’applications web ont des exigences plus élevées lorsqu’il s’agit du style et expérience globale. Les applications web modernes fréquemment tirer parti des outils riches et infrastructures pour définir et gérer leur apparence et la convivialité de façon cohérente. Comme les infrastructures [Bootstrap](http://getbootstrap.com/) peuvent accéder permettent de définir un ensemble commun des styles et options de disposition pour les sites web. Toutefois, la plupart des sites non trivial également bénéficier de pouvoir définir et mettre à jour les styles et les fichiers de feuille de style en cascade de manière efficace, mais aussi ayant un accès facile aux icônes non-image qui permettent d’interface du site plus intuitive. C’est là langages et outils qui prennent en charge [moins](http://lesscss.org/) et [Sass](http://sass-lang.com/), et les bibliothèques telles que [police impressionnant](http://fontawesome.io/), sont disponibles dans.

## <a name="css-preprocessor-languages"></a>Langues de préprocesseur CSS

Les langues qui sont compilés dans d’autres langues, afin d’améliorer l’expérience de l’utilisation de la langue sous-jacent, sont appelés Préprocesseurs. Il existe deux Préprocesseurs populaires pour CSS : inférieur et Sass.  Ces Préprocesseurs ajouter des fonctionnalités à CSS, telles que la prise en charge pour les variables et les règles imbriquées, qui améliorent la facilité de maintenance de feuilles de style volumineuses et complexes. CSS en tant que langage est très simple, ne dispose pas de prise en charge de la même pour un élément simple en tant que variables, et cela a tendance à rendre les fichiers CSS répétitives et trop importants. Ajout de fonctionnalités de langage de programmation réel via Préprocesseurs peut aider à réduire la duplication et fournir une meilleure organisation des règles de style. Visual Studio fournit une prise en charge intégrée pour les deux moins et Sass, ainsi que les extensions qui peuvent améliorer l’expérience de développement lorsque vous travaillez avec ces langages.

Prenons un exemple de comment Préprocesseurs peuvent améliorer la lisibilité et facilité de gestion des informations de style, CSS :

```css
.header {
    color: black;
    font-weight: bold;
    font-size: 18px;
    font-family: Helvetica, Arial, sans-serif;
}

.small-header {
    color: black;
    font-weight: bold;
    font-size: 14px;
    font-family: Helvetica, Arial, sans-serif;
}
```

Utilisation moindre, cela peut être réécrit pour éliminer tous les doublons, à l’aide un *mixin* (ainsi nommé, car il permet de « mix » propriétés d’une classe ou d’ensemble de règles dans un autre) :

```less
.header {
    color: black;
    font-weight: bold;
    font-size: 18px;
    font-family: Helvetica, Arial, sans-serif;
}

.small-header {
    .header;
    font-size: 14px;
}
```

## <a name="less"></a>Moins

Le moins CSS préprocesseur s’exécute à l’aide de Node.js. Pour installer inférieure, utilisez le Gestionnaire de Package de nœud (npm) à partir d’une invite de commandes (-g signifie « globaux ») :

```console
npm install -g less
```

Si vous utilisez Visual Studio, vous pouvez commencer à utiliser moins de ressources en ajoutant un ou plusieurs fichiers inférieure à votre projet, puis en configurant Gulp (ou Grunt) pour les traiter au moment de la compilation. Ajouter un *Styles* dossier à votre projet, puis ajoutez un nouveau moins un fichier nommé *main.less* dans ce dossier.

![Ajouter le fichier](less-sass-fa/_static/add-less-file.png)

Une fois ajoutée, la structure des dossiers doit ressembler à ceci :

![Structure de dossiers](less-sass-fa/_static/folder-structure.png)

Maintenant, vous pouvez ajouter des styles de base pour le fichier, qui sera compilé en CSS et déployé dans le dossier wwwroot par Gulp.

Modifier *main.less* pour inclure le contenu suivant, qui crée une palette de couleurs simple à partir d’une couleur de base unique.

```less
@base: #663333;
@background: spin(@base, 180);
@lighter: lighten(spin(@base, 5), 10%);
@lighter2: lighten(spin(@base, 10), 20%);
@darker: darken(spin(@base, -5), 10%);
@darker2: darken(spin(@base, -10), 20%);

body {
    background-color:@background;
}
.baseColor  {color:@base}
.bgLight    {color:@lighter}
.bgLight2   {color:@lighter2}
.bgDark     {color:@darker}
.bgDark2    {color:@darker2}
```

`@base`et l’autre @-prefixed éléments sont des variables. Chacun d’eux représente une couleur. À l’exception de `@base`, ils sont définis à l’aide des fonctions de couleur : éclaircir assombrissement et de rotation. Éclaircir et assombrissement faire à peu près ce que vous attendez ; rotation ajuste la teinte d’une couleur d’un nombre de degrés (autour de la roulette de la couleur). Moins le processeur est assez intelligent pour ignorer les variables qui ne sont pas utilisées, donc pour illustrer le fonctionnement de ces variables, vous devez les utiliser quelque part. Les classes `.baseColor`, etc. va vous montrer les valeurs calculées de chacune des variables dans le fichier CSS qui est généré.

### <a name="getting-started"></a>Bien démarrer

Créer un **fichier de Configuration npm** (*package.json*) dans votre dossier de projet et le modifier pour faire référence à `gulp` et `gulp-less`:

```json
{
  "version": "1.0.0",
  "name": "asp.net",
  "private": true,
  "devDependencies": {
    "gulp": "3.9.1",
    "gulp-less": "3.3.0"
  }
}
```

Installer les dépendances au niveau d’une invite de commandes dans votre dossier de projet, ou dans Visual Studio **l’Explorateur de solutions** (**dépendances > npm > Restaurer les packages**).

```console
npm install
```

![Restauration des packages VS](less-sass-fa/_static/restore-packages.png)

Dans le dossier du projet, créez un **Gulp un fichier de Configuration** (*gulpfile.js*) pour définir le processus automatisé.  Ajoutez une variable en haut du fichier pour représenter inférieur et une tâche à exécuter inférieur :

```javascript
var gulp = require("gulp"),
  fs = require("fs"),
  less = require("gulp-less");

gulp.task("less", function () {
  return gulp.src('Styles/main.less')
    .pipe(less())
    .pipe(gulp.dest('wwwroot/css'));
});
```

Ouvrez le **Explorateur d’exécuteur de tâches** (**vue > autres fenêtres > Explorateur d’exécuteur de tâches**). Parmi les tâches, vous devez voir une nouvelle tâche nommée `less`. Vous devrez peut-être actualiser la fenêtre.

Exécutez le `less` tâche et que vous consultez une sortie similaire à celui indiqué ici :

![moins exécuteur de tâches](less-sass-fa/_static/less-task-runner.png)

Le *wwwroot/css* dossier contient désormais un nouveau fichier, *main.css*:

![css principal créé](less-sass-fa/_static/main-css-created.png)

Ouvrez *main.css* et vous voyez quelque chose comme suit :

```css
body {
    background-color: #336666;
}
.baseColor {
    color: #663333;
}
.bgLight {
    color: #884a44;
}
.bgLight2 {
    color: #aa6355;
}
.bgDark {
    color: #442225;
}
.bgDark2 {
    color: #221114;
}
```

Ajouter une page HTML simple à la *wwwroot* dossier et référence *main.css* pour afficher la palette de couleurs en action.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <link href="css/main.css" rel="stylesheet" />
    <title></title>
</head>
<body>
    <div>
        <div class="baseColor">BaseColor</div>
        <div class="bgLight">Light</div>
        <div class="bgLight2">Light2</div>
        <div class="bgDark">Dark</div>
        <div class="bgDark2">Dark2</div>
    </div>
</body>
</html>
```

Vous pouvez voir que le degré de 180 lancer sur `@base` utilisé pour produire `@background` a entraîné la roue opposées couleur de `@base`:

![exemple de test inférieur](less-sass-fa/_static/less-test-screenshot.png)

Inférieur prend également en charge pour les règles imbriquées, ainsi que les requêtes de média imbriquée. Par exemple, la définition des hiérarchies imbriquées telles que les menus peuvent entraîner des règles CSS verbose comme ceux-ci :

```css
nav {
    height: 40px;
    width: 100%;
}
nav li {
    height: 38px;
    width: 100px;
}
nav li a:link {
    color: #000;
    text-decoration: none;
}
nav li a:visited {
    text-decoration: none;
    color: #CC3333;
}
nav li a:hover {
    text-decoration: underline;
    font-weight: bold;
}
nav li a:active {
    text-decoration: underline;
}
```

Dans l’idéal, toutes les règles de style associées sont placés entre eux dans le fichier CSS, mais dans la pratique il n’y a rien appliquer cette règle à l’exception de la convention et éventuellement des commentaires de bloc.

Définition de ces règles mêmes utilisation moindre ressemble à ceci :

```less
nav {
    height: 40px;
    width: 100%;
    li {
        height: 38px;
        width: 100px;
        a {
            color: #000;
            &:link { text-decoration:none}
            &:visited { color: #CC3333; text-decoration:none}
            &:hover { text-decoration:underline; font-weight:bold}
            &:active {text-decoration:underline}
        }
    }
}
```

Notez que dans ce cas, tous les éléments subordonnés de `nav` contenus dans son étendue. Il n’est plus toute répétition des éléments parents (`nav`, `li`, `a`), et le nombre total de la ligne a supprimé ainsi (même si certains des qui est un résultat de l’utilisation de valeurs sur les mêmes lignes dans le deuxième exemple). Il peut être très utile, Oui, pour afficher toutes les règles pour un élément d’interface utilisateur donné au sein d’une portée limitée explicitement, dans ce cas définie du reste du fichier par des accolades.

Le `&` syntaxe est une fonctionnalité moins sélecteur et qui représente le parent de sélection en cours. Par conséquent, dans l’un {...} bloc, `&` représente un `a` balise et par conséquent `&:link` équivaut à `a:link`.

Requêtes de média, extrêmement utiles pour la création d’une conception réactive, peuvent également contribuer fortement à répétition et de la complexité de CSS. Inférieur permet les requêtes de média être imbriqués dans les classes, afin que la définition de classe entière ne doit être répété dans différents niveau supérieur `@media` éléments. Par exemple, voici CSS pour un menu réactif :

```css
.navigation {
    margin-top: 30%;
    width: 100%;
}
@media screen and (min-width: 40em) {
    .navigation {
        margin: 0;
    }
}
@media screen and (min-width: 62em) {
    .navigation {
        width: 960px;
        margin: 0;
    }
}
```

Cela peut être mieux défini dans un délai inférieur en tant que :

```less
.navigation {
    margin-top: 30%;
    width: 100%;
    @media screen and (min-width: 40em) {
        margin: 0;
    }
    @media screen and (min-width: 62em) {
        width: 960px;
        margin: 0;
    }
}
```

Une autre fonctionnalité de moins que nous avons déjà vu est sa prise en charge des opérations mathématiques, ce qui permet des attributs de style doit être construite à partir de variables prédéfinies. Cela facilite la mise à jour de styles connexes beaucoup plus faciles, étant donné que la variable de base peut être modifiée et toutes les valeurs dépendantes changent automatiquement.

Les fichiers CSS, en particulier pour les sites de grande taille (et en particulier si les requêtes de média sont utilisés), ont tendance à devenir très volumineux au fil du temps, ce qui leur utilisation lourde. Moins de fichiers peuvent être définies séparément, puis extraites à l’aide de `@import` directives. Inférieur peut également être utilisé pour importer CSS des fichiers individuels, de même, si vous le souhaitez.

*Mixins* peut accepter des paramètres, et inférieur prend en charge une logique conditionnelle dans l’écran de protecteurs mixin, qui fournissent un moyen déclaratif de définir certaines mixins prise d’effet. Une utilisation courante pour les protecteurs de mixin est pour ajuster les couleurs en fonction de la lumière ou sombre la couleur source. Étant donné un mixin qui accepte un paramètre de couleur, un garde mixin peut servir à modifier le mixin en fonction de cette couleur :

```less
.box (@color) when (lightness(@color) >= 50%) {
    background-color: #000;
}
.box (@color) when (lightness(@color) < 50%) {
    background-color: #FFF;
}
.box (@color) {
    color: @color;
}

.feature {
    .box (@base);
}
```

Étant donné notre actuel `@base` valeur `#663333`, moins ce script génère le code CSS suivant :

```css
.feature {
    background-color: #FFF;
    color: #663333;
}
```

Inférieur fournit un nombre de fonctionnalités supplémentaires, mais cela doit vous donner une idée de la puissance de ce langage de prétraitement.

## <a name="sass"></a>Sass

Sass est similaire à une valeur inférieure, en fournissant la prise en charge pour la plupart de ces fonctionnalités, mais avec une syntaxe légèrement différente. Il est construit à l’aide de Ruby, plutôt que JavaScript, ainsi que les exigences de configuration différents. Le langage Sass d’origine n’a pas utilisé des accolades ou des points-virgules, mais au lieu de cela défini étendue à l’aide d’un espace blanc et mise en retrait. Dans la version 3 de Sass, introduite une nouvelle syntaxe, **SCSS** (« CSS Sassy »). SCSS est similaire à CSS dans la mesure où il ignore les espaces blancs et les niveaux de mise en retrait et utilise à la place des points-virgules et des accolades.

Pour installer Sass, en général, vous installez d’abord Ruby (préinstallé sur Mac) et puis exécutez :

```console
gem install sass
```

Toutefois, si vous utilisez Visual Studio, vous pouvez commencer à utiliser Sass à peu près la même façon comme vous le feriez avec moins. Ouvrez *package.json* et ajouter le package « gulp-sass » à `devDependencies`:

```json
"devDependencies": {
  "gulp": "3.9.1",
  "gulp-less": "3.3.0",
  "gulp-sass": "3.1.0"
}
```

Ensuite, modifiez *gulpfile.js* pour ajouter une variable sass et une tâche pour compiler vos fichiers Sass et de placer les résultats dans le dossier wwwroot :

```javascript
var gulp = require("gulp"),
  fs = require("fs"),
  less = require("gulp-less"),
  sass = require("gulp-sass");

// other content removed

gulp.task("sass", function () {
  return gulp.src('Styles/main2.scss')
    .pipe(sass())
    .pipe(gulp.dest('wwwroot/css'));
});
```

Vous pouvez maintenant ajouter le fichier Sass *main2.scss* à la *Styles* dossier à la racine du projet :

![ajouter les fichiers scss](less-sass-fa/_static/add-scss-file.png)

Ouvrez *main2.scss* et ajoutez le code suivant :

```sass
$base: #CC0000;
body {
    background-color: $base;
}
```

Enregistrez tous vos fichiers. Désormais, lorsque vous actualisez **Task Runner Explorer**, vous voyez un `sass` tâche. Exécutez-le, puis en regardant dans la */wwwroot/css* dossier. Il existe désormais un *main2.css* fichier, avec ce contenu :

```css
body {
    background-color: #CC0000;
}
```

Sass prend en charge l’imbrication à peu près le même a été qu’inférieur est le cas, en fournissant des avantages similaires. Peut être fractionnées en fonction de fichiers et inclus à l’aide de la `@import` directive :

```sass
@import 'anotherfile';
```

Sass prend en charge mixins également, à l’aide de la `@mixin` mot clé pour les définir et `@include` pour les inclure, comme dans cet exemple à partir de [sass-lang.com](http://sass-lang.com):

```sass
@mixin border-radius($radius) {
    -webkit-border-radius: $radius;
    -moz-border-radius: $radius;
    -ms-border-radius: $radius;
    border-radius: $radius;
}

.box { @include border-radius(10px); }
```

En plus de mixins, Sass prend également en charge le concept d’héritage, ce qui permet une classe étendre un autre. Il est conceptuellement semblable à un mixin, mais entraîne moins de code CSS. Il est réalisé en utilisant la `@extend` (mot clé). Pour tester mixins, ajoutez le code suivant à votre *main2.scss* fichier :

```sass
@mixin alert {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
}

.success {
    @include alert;
    border-color: green;
}

.error {
    @include alert;
    color: red;
    border-color: red;
    font-weight:bold;
}
```

Examinez la sortie dans *main2.css* après l’exécution de la `sass` de tâches dans **Task Runner Explorer**:

```css
.success {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
    border-color: green;
 }

.error {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
    color: red;
    border-color: red;
    font-weight: bold;
}
```

Notez que toutes les propriétés courantes de l’alerte mixin sont répétés dans chaque classe. Le mixin fait un bon de travail de la déduplication au moment du développement, mais il est toujours créant un fichier CSS avec un grand nombre de duplication, entraînant plus grands que les fichiers CSS nécessaires - un problème de performances.

Maintenant remplacer l’alerte mixin avec un `.alert` classe et remplacez `@include` à `@extend` (sans oublier d’étendre `.alert`, et non `alert`) :

```sass
.alert {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
}

.success {
    @extend .alert;
    border-color: green;
}

.error {
    @extend .alert;
    color: red;
    border-color: red;
    font-weight:bold;
}
```

Exécuter une fois de plus de Sass et examinez le code CSS qui en résulte :

```css
.alert, .success, .error {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
}

.success {
    border-color: green;
}

.error {
    color: red;
    border-color: red;
    font-weight: bold;
}
```

Désormais, les propriétés sont définies uniquement autant que nécessaire, et mieux CSS est généré.

Sass inclut également des fonctions et les opérations de la logique conditionnelle, similaires à une valeur inférieure. En fait, les fonctionnalités de ces deux langages sont très similaires.

## <a name="less-or-sass"></a>Inférieur ou Sass ?

Il n’existe encore aucune consensus que s’il s’agit en général préférable d’utiliser inférieur et Sass (ou même s’il convient de préférer les Sass d’origine ou la nouvelle syntaxe SCSS dans Sass). La décision la plus importante consiste probablement à **utiliser un de ces outils**, par opposition à simplement à codage manuel vos fichiers CSS. Une fois que vous avez apportées qui inférieurs, de décision et Sass sont de bons choix.

## <a name="font-awesome"></a>Police impressionnant

Outre les Préprocesseurs CSS, une excellente ressource pour les applications de style web moderne est impressionnant de police. Police Awesome est un kit de ressources qui fournit plus de 500 icônes vecteur évolutive qui peuvent être utilisés librement dans vos applications web. Il a été conçu pour fonctionner avec les données d’amorçage, mais il n’a aucune dépendance sur cette infrastructure ou sur toutes les bibliothèques JavaScript.

Prise en main impressionnant de police, la plus simple consiste à ajouter une référence à celui-ci, à l’aide de son emplacement réseau (CDN) de diffusion de contenu public :

```html
<link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/font-awesome/4.3.0/css/font-awesome.min.css">
```

Vous pouvez également l’ajouter à votre projet Visual Studio en l’ajoutant aux section « dépendances » dans *bower.json*:

```json
{
  "name": "ASP.NET",
  "private": true,
  "dependencies": {
    "bootstrap": "3.0.0",
    "jquery": "1.10.2",
    "jquery-validation": "1.11.1",
    "jquery-validation-unobtrusive": "3.2.2",
    "hammer.js": "2.0.4",
    "bootstrap-touch-carousel": "0.8.0",
    "Font-Awesome": "4.3.0"
  }
}
```

Une fois que vous avez une référence à la police impressionnant sur une page, vous pouvez ajouter des icônes à votre application en appliquant des classes police impressionnant, généralement précédés de « fa- », à vos éléments HTML inline (tel que `<span>` ou `<i>`).  Par exemple, vous pouvez ajouter des icônes aux listes simples et des menus à l’aide de code similaire à celui-ci :

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title></title>
    <link href="lib/font-awesome/css/font-awesome.css" rel="stylesheet" />
</head>
<body>
    <ul class="fa-ul">
        <li><i class="fa fa-li fa-home"></i> Home</li>
        <li><i class="fa fa-li fa-cog"></i> Settings</li>
    </ul>
</body>
</html>
```

Cela génère les éléments suivants dans le navigateur, remarquez l’icône en regard de chaque élément :

![icônes de la liste](less-sass-fa/_static/list-icons-screenshot.png)

Vous pouvez afficher la liste complète des icônes disponibles ici :

http://fontawesome.io/icons/

## <a name="summary"></a>Récapitulatif

Les applications web modernes exigent plus en plus réactives, fluide conceptions qui sont propres, intuitive et facile à utiliser à partir de différents périphériques. Gérer la complexité des feuilles de style CSS requis pour atteindre ces objectifs est mieux effectuée à l’aide d’un type de préprocesseur inférieur ou Sass. En outre, les kits comme police impressionnant fournissent rapidement des icônes bien connus pour les menus de navigation textuelle et expérience des boutons, l’amélioration de l’utilisateur globale de votre application.
