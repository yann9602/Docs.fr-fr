---
title: "Création de sites et attrayantes et réactives avec les données d’amorçage"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: bd27832c-2877-4b7b-9337-e009361d845f
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/bootstrap
ms.openlocfilehash: fc00bcce24d586865bf6a1d3f2f7e782a2f1c703
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="building-beautiful-responsive-sites-with-bootstrap"></a>Création de sites et attrayantes et réactives avec les données d’amorçage

<a name=bootstrap-index></a>

Par [Steve Smith](https://ardalis.com/)

Programme d’amorçage est actuellement l’infrastructure web les plus populaires pour le développement d’applications web réactives. Il offre un nombre de fonctionnalités et avantages qui peuvent améliorer l’expérience de vos utilisateurs avec votre site web, si vous êtes un utilisateur débutant à la conception frontale et de développement ou d’un expert. Programme d’amorçage est déployé en tant qu’ensemble de fichiers CSS et JavaScript et est conçu pour évoluer vos application ou un site efficacement des téléphones jusqu’aux tablettes aux ordinateurs de bureau.

## <a name="getting-started"></a>Bien démarrer

Il existe plusieurs façons de prise en main d’amorçage. Si vous démarrez une nouvelle application web dans Visual Studio, vous pouvez choisir le modèle de démarrage par défaut pour ASP.NET Core, dans lequel cas Bootstrap proviendront préalablement installé :

![Démarrer dans la vue de solution de modèle de démarrage](bootstrap/_static/bootstrap-in-starter-template.png)

Ajout d’amorçage à un ASP.NET Core projet consiste simplement à ajouter *bower.json* en tant que dépendance :

[!code-json[Main](../common/samples/WebApplication1/bower.json?highlight=5)]

Il s’agit de la méthode recommandée pour ajouter des données d’amorçage à un projet ASP.NET Core.

Vous pouvez également installer d’amorçage à l’aide de plusieurs gestionnaires de packages, tels que Bower, npm ou NuGet. Dans chaque cas, le processus est essentiellement le même :

### <a name="bower"></a>Bower

```console
bower install bootstrap
```

### <a name="npm"></a>npm

```console
npm install bootstrap
```

### <a name="nuget"></a>NuGet

```console
Install-Package bootstrap
```

> [!NOTE]
> La méthode recommandée pour installer des dépendances côté client comme programme d’amorçage dans ASP.NET Core via Bower (à l’aide de *bower.json*, comme indiqué ci-dessus). L’utilisation de npm/NuGet sont affichés pour illustrer comment facilement Bootstrap peut être ajouté à d’autres types d’applications web, y compris les versions antérieures d’ASP.NET.

Si vous êtes faisant référence à vos propres versions locales d’amorçage, vous devez les référencer dans toutes les pages qui l’utilisent. En production, vous devez référencer les données d’amorçage à l’aide d’un CDN. Dans le modèle de site ASP.NET par défaut, le *_Layout.cshtml* fichier donc, comme ceci :

[!code-html[Main](../common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=9,13,51,59)]

> [!NOTE]
> Si vous souhaitez utiliser des plug-ins de jQuery du programme d’amorçage, vous devez également référencer jQuery.

## <a name="basic-templates-and-features"></a>Fonctionnalités et modèles de base

Le modèle de démarrage plus simple ressemble beaucoup le *_Layout.cshtml* fichier illustré ci-dessus et simplement comprend un menu de base pour la navigation et un emplacement pour restituer le reste de la page.

### <a name="basic-navigation"></a>Navigation de base

Le modèle par défaut utilise un ensemble de `<div>` éléments afin d’afficher une barre de navigation supérieure et le corps de la page. Si vous utilisez HTML5, vous pouvez remplacer la première `<div>` la balise avec un `<nav>` balise pour obtenir le même effet, mais avec une sémantique plus précise.  Dans ce premier `<div>` vous pouvez voir d’autres. Tout d’abord, un `<div>` avec une classe de « conteneur », puis, dans, les deux autres `<div>` éléments : « barre de navigation en-tête » et « réduction de la barre de navigation ».  L’élément div d’en-tête de la barre de navigation inclut un bouton qui s’affiche lorsque l’écran est inférieure à une certaine largeur minimale, affichant 3 lignes horizontales (un dite « icône représentant un hamburger »). L’icône est restitué à l’aide de pure HTML et CSS ; Aucune image n’est requise. Voici le code qui affiche l’icône, avec chacun de la <span> une des barres blanches de rendu des balises :

```html
<button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
    <span class="icon-bar"></span>
    <span class="icon-bar"></span>
    <span class="icon-bar"></span>
</button>
```

Il inclut également le nom de l’application qui apparaît dans le coin supérieur gauche.  Le menu de navigation principal est restitué par le `<ul>` élément dans la deuxième div et inclut des liens pour chez eux, sur et de Contact. Des liens supplémentaires pour inscrire et de connexion sont ajoutés à la ligne _LoginPartial ligne 29. Sous le volet de navigation, le corps principal de chaque page est rendu dans un autre `<div>`, marqués avec les classes « conteneur » et « contenu du corps ». Dans le fichier de _Layout simple par défaut indiqué ici, le contenu de la page est rendu par l’affichage spécifique associé à la page, puis un simple `<footer>` est ajouté à la fin de la `<div>` élément.  Vous pouvez voir comment la fonction intégrée sur la page s’affiche à l’aide ce modèle :

![Sur la page](bootstrap/_static/about-page-wide.png)

La barre de navigation réduite, avec un bouton dans le coin supérieur droit, « hamburger » s’affiche quand la fenêtre devient inférieur à une certaine largeur :

![sur la page avec hamburger menu](bootstrap/_static/about-page-hamburger.png)

En cliquant sur l’icône pour afficher les éléments de menu dans un tiroir vertical qui glisse vers le bas à partir du haut de la page :

![sur la page avec hamburger menu développé](bootstrap/_static/about-page-hamburger-open.png)

### <a name="typography-and-links"></a>Des liens et la typographie

Programme d’amorçage définit la base typographie, couleurs et dans son fichier CSS de mise en forme de lien de site. Ce fichier CSS inclut les styles par défaut pour les tables, des boutons, des éléments de formulaire, des images et plus ([plus](http://getbootstrap.com/css/)). Une fonctionnalité particulièrement utile est le système de disposition Grille couvert ensuite.

### <a name="grids"></a>Grilles

Une des fonctionnalités plus populaires d’amorçage est son système de mise en page de grille. Les applications web modernes Évitez d’utiliser le `<table>` balise pour la mise en page, au lieu de cela en limitant l’utilisation de cet élément aux données tabulaires réelles. Au lieu de cela, colonnes et lignes peuvent être présentés à l’aide d’une série de `<div>` éléments et les classes CSS appropriées. Il existe plusieurs avantages de cette approche, y compris la possibilité d’ajuster la disposition des grilles pour afficher verticalement sur les écrans étroits, comme sur les téléphones.

[Système de disposition Grille du programme d’amorçage](http://getbootstrap.com/css/#grid) est basée sur des douze colonnes. Ce nombre a été choisi, car il peut être divisée uniformément 1, 2, 3 ou 4 colonnes et les largeurs de colonne peuvent varier à dans 1/12 de la largeur verticale de l’écran. Pour commencer à utiliser le système de disposition Grille, vous devez commencer par un conteneur `<div>` , puis ajoutez une ligne `<div>`, comme illustré ici :

```html
<div class="container">
    <div class="row">
        ...
    </div>
</div>
```

Ensuite, ajoutez supplémentaires `<div>` éléments pour chaque colonne et spécifiez le nombre de colonnes qui `<div>` doit occuper (hors 12) dans le cadre d’une classe CSS en commençant par « col - md- ». Par exemple, si vous souhaitez simplement comporter deux colonnes de taille égale, vous utiliseriez une classe de « col-md-6 » pour chacun d’eux. Dans ce cas, « md » est l’abréviation de « moyenne » et fait référence à des tailles d’affichage de format standard ordinateur de bureau. Il existe quatre options différentes, que vous pouvez choisir parmi, et chacune sera utilisé pour les largeurs supérieurs sauf substitution (de sorte que si vous souhaitez que la mise en page pour être fixe, quelle que soit la largeur de l’écran, il vous suffit de spécifier les classes xs).

Préfixe de classe CSS | Niveau de l’appareil | Largeur
:---: | :---: | :---:
col-xs - | Téléphones | < 768px
col-sm - | Tablettes | > = 768px
col-md - | Ordinateurs de bureau | > = 992px
col-lg - | Affiche de bureau plus grande | > = 1200px

Lors de la spécification des deux colonnes avec « col-md-6 « la mise en page qui en résulte seront deux colonnes avec des résolutions de bureau, mais ces deux colonnes seront empilent verticalement lors du rendu sur les périphériques de petite taille (ou une fenêtre de navigateur plus étroite sur un ordinateur de bureau), permettant aux utilisateurs d’afficher facilement contenu sans avoir besoin de faire défiler horizontalement.

Programme d’amorçage toujours par défaut à une disposition de colonne unique, vous devez uniquement spécifier des colonnes lorsque vous souhaitez que plusieurs colonnes. Le seul moment où vous ne souhaitez pas spécifier explicitement qu’un `<div>` occupent toutes les 12 colonnes serait pour substituer le comportement d’un plus grand niveau de périphérique. Lorsque vous spécifiez plusieurs classes de niveau de périphérique, vous devrez peut-être réinitialiser le rendu des colonnes à certains points. Ajout d’un élément div clearfix qui est uniquement visible dans une certaine fenêtre d’affichage permettre effectuer cette opération, comme indiqué ici :

![grille de la fenêtre d’affichage étroites et étendues](bootstrap/_static/narrow-and-wide-viewport-grid.png)

Dans l’exemple ci-dessus, un et deux partage une ligne dans la disposition « md », tandis que les deux et trois partagent une ligne dans la disposition « xs ». Sans le clearfix `<div>`, deux et trois ne sont pas affichés correctement dans la vue « xs » (Notez que seul, 4 et 5 est affichées) :

![grille sans utiliser de clearfix](bootstrap/_static/grid-without-clearfix.png)

Dans cet exemple, une seule ligne `<div>` a été utilisé, et fixe d’amorçage a principalement l’attitude en ce qui concerne la mise en page et des colonnes d’empilement. En règle générale, vous devez spécifier une ligne `<div>` pour chaque ligne horizontale nécessite la mise en page, et bien entendu, vous pouvez imbriquer des grilles d’amorçage dans un autre. Lorsque vous procédez ainsi, chaque grille imbriquée occupera 100 % de la largeur de l’élément sur lequel il est placé, qui peut ensuite être subdivisée à l’aide des classes de colonne.

### <a name="jumbotron"></a>JumboTron

Si vous avez utilisé les modèles ASP.NET MVC par défaut dans Visual Studio 2012 ou 2013, vous avez probablement remarqué la Jumbotron en action. Il fait référence à une section pleine chasse volumineuse d’une page qui peut être utilisée pour afficher une image d’arrière-plan de grande taille, un appel à l’action, une rotation ou éléments similaires. Pour ajouter un jumbotron à une page, ajoutez simplement un `<div>` et lui donner une classe de « jumbotron », puis placer un conteneur `<div>` à l’intérieur et ajoutez votre contenu.  Nous pouvons ajuster facilement la norme sur la page à utiliser un jumbotron pour les titres principaux, qu'il affiche :

![exemple de JumboTron](bootstrap/_static/jumbotron.png)

### <a name="buttons"></a>Boutons

Les classes de bouton par défaut et leurs couleurs sont affichés dans la figure ci-dessous.

![boutons à thème](bootstrap/_static/theme-buttons.png)

### <a name="badges"></a>Badges

Badges font référence aux légendes de petite taille, généralement numériques en regard d’un élément de navigation. Ils peuvent indiquer un nombre de messages ou des notifications en attente ou la présence de mises à jour. En spécifiant ces badges est aussi simple que l’ajout d’un <span> contenant le texte, avec une classe de « badge » :

![badges à thème](bootstrap/_static/theme-badges.png)

### <a name="alerts"></a>Alertes

Vous devrez peut-être afficher un type de notification, une alerte ou message d’erreur pour les utilisateurs de votre application. C'est-à-dire, où les classes d’alerte standards sont utiles.  Il existe quatre niveaux de gravité différents jeux de couleurs associé :

![alertes à thème](bootstrap/_static/theme-alerts.png)

### <a name="navbars-and-menus"></a>Menus et barres de navigation

Notre disposition inclut déjà une barre de navigation standard, mais le thème d’amorçage prend en charge les options de style supplémentaires. Nous pouvons également facilement choisir d’afficher la barre de navigation verticalement plutôt que horizontalement si qui a par défaut, ainsi que l’ajout sous éléments de navigation dans les menus volants. Menus de navigation simple comme onglet bandes, reposent sur des <ul> éléments. Vous pouvez créer très simplement en leur permettant uniquement avec les classes CSS « nav » et « nav onglets » :

![poste à thème](bootstrap/_static/theme-tabstrips.png)

Barres de navigation sont générés de la même façon, mais sont un peu plus complexes.  Ils commencent par un `<nav>` ou `<div>` avec une classe de « barre de navigation », dans lequel un élément div conteneur conserve le reste des éléments. Notre page inclut déjà une barre de navigation dans son en-tête, celui illustré ci-dessous se développe simplement à ce sujet, ajout de la prise en charge pour un menu déroulant :

![barres de navigation à thème](bootstrap/_static/theme-navbars.png)

### <a name="additional-elements"></a>Éléments supplémentaires

Le thème par défaut peut également servir à présenter des tableaux HTML dans un style de mise en forme correcte, y compris la prise en charge pour les vues distribuées. Il existe des étiquettes avec des styles sont semblables à celles des boutons. Vous pouvez créer des menus déroulants personnalisés qui prennent en charge les options de style supplémentaires au-delà de la norme HTML `<select>` élément, ainsi que les barres de navigation semblable à celui de notre site de démarrage par défaut est déjà utilisé. Si vous avez besoin d’une barre de progression, il existe plusieurs styles à sélectionner, ainsi que la liste des groupes et des panneaux d’incluent un titre et le contenu.  Explorer les options supplémentaires dans le thème d’amorçage standard ici :

[http://getbootstrap.com/Examples/Theme/](http://getbootstrap.com/examples/theme/)

## <a name="more-themes"></a>Plus de thèmes

Vous pouvez étendre le thème d’amorçage standard en remplaçant tout ou partie de ses CSS, régler les couleurs et les styles selon les besoins de votre propre application. Si vous souhaitez démarrer à partir d’un thème prêts à l’emploi, il existe plusieurs galeries de thème disponibles en ligne spécialisés dans les thèmes d’amorçage, telles que WrapBootstrap.com (qui possède un éventail de thèmes commerciales) et Bootswatch.com (qui offre des thèmes libres).  Certains des modèles disponibles payants fournissent un large éventail de fonctionnalités par-dessus le thème d’amorçage base, tels que prise en charge des menus d’administration et des tableaux de bord avec des jauges et graphiques enrichis. Est un exemple d’un modèle payant populaires Inspinia, actuellement vente pour $18, qui inclut un modèle MVC5 ASP.NET outre AngularJS et les versions HTML statiques. Vous trouverez ci-dessous une capture d’écran de l’exemple.

![Exemple thème inspinia](bootstrap/_static/theme-inspinia.png)

Si vous souhaitez modifier le thème d’amorçage, placez le *bootstrap.css* fichier pour le thème que vous souhaitez dans le **wwwroot/css** dossier et modifiez les références dans *_Layout.cshtml* afin qu’il pointe.  Modifier les liens pour tous les environnements :

```html
<environment names="Development">
    <link rel="stylesheet" href="~/css/bootstrap.css" />
```

```html
<environment names="Staging,Production">
    <link rel="stylesheet" href="~/css/bootstrap.min.css" />
```

Si vous souhaitez créer votre propre tableau de bord, vous pouvez démarrer à partir de l’exemple libre disponible ici : [http://getbootstrap.com/examples/dashboard/](http://getbootstrap.com/examples/dashboard/).

## <a name="components"></a>Composants

En plus de ces éléments déjà mentionnés, amorçage inclut la prise en charge pour un large éventail de [composants d’interface utilisateur intégrées](http://getbootstrap.com/components/).

### <a name="glyphicons"></a>Glyphicons

Amorçage inclut les jeux d’icônes à partir de Glyphicons ([http://glyphicons.com](http://glyphicons.com)), avec des icônes plus de 200 disponibles gratuitement pour une utilisation dans votre application web compatible d’amorçage. Voici juste un petit échantillon :

![Glyphicons](bootstrap/_static/theme-glyphicons.png)

### <a name="input-groups"></a>Groupes d’entrée

Groupes d’entrée autoriser le regroupement de texte ou des boutons à un élément d’entrée, en fournissant une expérience plus intuitive à l’utilisateur :

![groupes d’entrée](bootstrap/_static/input-groups.png)

### <a name="breadcrumbs"></a>Vues miniatures

Vues miniatures sont un composant de l’interface utilisateur commun utilisé pour montrer leur historique récent ou la profondeur dans la hiérarchie de navigation d’un site à l’utilisateur. Ajoutez-les facilement en appliquant la classe « fil d’Ariane » aux `<ol>` élément de liste. Inclure la prise en charge intégrée pour la pagination à l’aide de la classe « pagination » sur un `<ul>` élément au sein d’un `<nav>`. Ajouter des diaporamas incorporé réactive et vidéo à l’aide de `<iframe>`, `<embed>`, `<video>`, ou `<object>` éléments dont le programme d’amorçage sera style automatiquement. Spécifiez un format particulier à l’aide des classes spécifiques, comme « incorporer-réactive-16by9 ».

## <a name="javascript-support"></a>Prise en charge de JavaScript

Bibliothèque de JavaScript du programme d’amorçage inclut la prise en charge API pour les composants inclus, ce qui vous permet de contrôler leur comportement par programme au sein de votre application. En outre, *bootstrap.js* inclut plus d’une dizaine plug-ins de jQuery personnalisées, en fournissant des fonctionnalités supplémentaires, comme des transitions, boîtes de dialogue modales, faites défiler la détection (où l’utilisateur a défilé dans le document en fonction des styles de mise à jour), comportement de réduction, tapis roulants et menus apposition dans la fenêtre de sorte qu’ils ne pas faire défiler l’écran. Il n’existe pas de suffisamment d’espace pour couvrir tous les modules complémentaires JavaScript intégrés Bootstrap – pour en savoir plus, consultez [http://getbootstrap.com/javascript/](http://getbootstrap.com/javascript/).

## <a name="summary"></a>Résumé

Programme d’amorçage fournit une infrastructure web qui peut être utilisée pour rapidement et efficacement mise en page et le style d’un large éventail d’applications et sites Web. Typographie de base et des styles fournissent une apparence et convivialité agréable qui peuvent être manipulée aisément prise en charge du thème personnalisé qui peut être écrit à la main ou acheté dans le commerce. Il prend en charge un ordinateur hôte des composants web qui aurait nécessité coûteuse des contrôles tiers pour mener à bien, lors de la prise en charge des normes web modernes et ouvertes dans le passé.
