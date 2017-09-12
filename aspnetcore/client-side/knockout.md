---
title: Framework Knockout.js MVVM dans ASP.NET Core
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: b20e3b23-1c51-47bf-adac-91b5048567e0
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/knockout
ms.openlocfilehash: d1c5cbd430587b757bb550f8f04355e67f04eb54
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="knockoutjs-mvvm-framework-in-aspnet-core"></a>Framework Knockout.js MVVM dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/)

Knockout est une bibliothèque JavaScript courants qui simplifie la création d’interfaces utilisateur de base de données complexes. Il peut être utilisé seul ou avec d’autres bibliothèques, telles que jQuery. Son principal objectif consiste à lier des éléments d’interface utilisateur à un modèle de données sous-jacent défini comme un objet JavaScript, telles que lorsque des modifications sont apportées à l’interface utilisateur, le modèle est mis à jour et vice versa. Knockout facilite l’utilisation d’un modèle Model-View-ViewModel (MVVM) d’un comportement d’une application web côté client. Les deux concepts principaux une doit savoir lorsque vous travaillez avec l’implémentation de MVVM de Knockout sont Observables et les liaisons.

## <a name="getting-started"></a>Bien démarrer

Knockout est déployé en tant qu’un seul fichier JavaScript, par conséquent, l’installation et son utilisation est très simple à l’aide de [bower](bower.md). En supposant que vous avez déjà [bower](bower.md) et [gulp](using-gulp.md) configuré, ouvrez *bower.json* dans ASP.NET de la base de données de projet et ajoutez la dépendance de knockout, comme indiqué ici :

```json
{
  "name": "KnockoutDemo",
  "private": true,
  "dependencies": {
    "knockout" : "^3.3.0"
  },
  "exportsOverride": {
  }
}
```

Tout cela en place, vous pouvez ensuite exécuter bower en ouvrant le Task Runner Explorer (sous vue ‣ autres fenêtres ‣ Task Runner Explorer) manuellement, puis sous tâches, cliquez sur bower et sélectionnez Exécuter. Le résultat doit ressembler à ceci :

![bower knockout en cours d’exécution dans l’Explorateur d’exécuteur de tâche](knockout/_static/bower-knockout.png)

Maintenant, si vous regardez dans votre projet `wwwroot` dossier, vous devez voir knockout installé sous le dossier lib.

![Knockout installé dans le dossier lib](knockout/_static/wwwroot-knockout.png)

Il est recommandé que dans votre environnement de production, vous référencez knockout via un réseau de distribution de contenu, ou un CDN, car cela augmente la probabilité que vos utilisateurs disposent déjà d’une copie mise en cache du fichier et seront donc pas à télécharger du tout. Knockout est disponible sur plusieurs CDN, y compris le CDN Microsoft Ajax, ici :

[http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-3.3.0.js](http://ajax.aspnetcdn.com/ajax/knockout/knockout-3.3.0.js)

Pour inclure Knockout sur une page qui l’utilise, ajoutez simplement un `<script>` élément faisant référence au fichier à partir, là où vous allez héberger il (avec votre application, ou via un réseau CDN) :

```html
<script type="text/javascript" src="knockout-3.3.0.js"></script>
```

## <a name="observables-viewmodels-and-simple-binding"></a>Observables ViewModel et la liaison simple

Vous peut-être déjà familiarisé avec l’aide de JavaScript pour manipuler les éléments sur une page web, soit via un accès direct au DOM ou à l’aide d’une bibliothèque, par exemple jQuery. En règle générale, ce type de comportement est obtenu en écrivant du code pour définir directement des valeurs de l’élément en réponse à certaines actions de l’utilisateur. Avec Knockout, une approche déclarative est effectuée à la place, par le biais duquel les éléments sur la page sont liés aux propriétés sur un objet. Au lieu d’écrire du code pour manipuler des éléments DOM, les actions utilisateur simplement interagissent avec l’objet ViewModel et Knockout prend en charge de garantir que les éléments de page sont synchronisées.

À titre d’exemple, considérez la liste de la page ci-dessous. Elle inclut un `<span>` élément avec un `data-bind` attribut indiquant que le contenu de texte doit être lié à AuthorName ;. Ensuite, dans un bloc JavaScript un viewModel variable est défini avec une seule propriété, `authorName`, définissez une valeur. Enfin, un appel à `ko.applyBindings` est effectuée, en passant cette variable viewModel.

```html
<html>
<head>
    <script type="text/javascript" src="lib/knockout/knockout.js"></script>
</head>
<body>
    <h1>Some Article</h1>
    <p>
        By <span data-bind="text: authorName"></span>
    </p>
    <script type="text/javascript">
      var viewModel = {
        authorName: 'Steve Smith'
      };
      ko.applyBindings(viewModel);
    </script>
</body>
</html>
```

Lorsqu’ils sont affichés dans le navigateur, le contenu de la <span> élément est remplacé par la valeur dans la variable viewModel :

![liaison simple Knockout](knockout/_static/simple-binding-screenshot.png)

Nous disposons de travail de liaison unidirectionnel simple. Notez que nulle part dans le code a nous écrivons JavaScript pour affecter une valeur pour le contenu de l’étendue. Si vous souhaitez manipuler le ViewModel, nous pouvons prendre une étape supplémentaire et ajouter une zone de texte d’entrée HTML et lier à sa valeur, comme pour :

```html
<p>
    Author Name: <input type="text" data-bind="value: authorName" />
</p>
```

Recharger la page, nous constatons que cette valeur est effectivement liée à la zone d’entrée :

![liaison d’entrée de Knockout](knockout/_static/input-binding-screenshot.png)

Toutefois, si nous modifions la valeur dans la zone de texte, la valeur correspondante dans le `<span>` élément ne change pas. Pourquoi ?

Le problème est que rien ne reçoit une notification le `<span>` qu’il devait être mis à jour. Simplement mettre à jour le ViewModel n’est toujours pas suffisant, à moins que les propriétés de ce dernier sont encapsulées dans un type spécial. Nous devons utiliser **observables** dans ViewModel pour toutes les propriétés qui nécessitent des modifications mises à jour automatiquement lorsqu’elles se produisent. En modifiant le ViewModel à utiliser `ko.observable("value")` au lieu de simplement « valeur », le ViewModel met à jour des éléments HTML qui sont liés à sa valeur chaque fois qu’une modification se produit. Notez que les zones d’entrée ne mettez à jour leur valeur jusqu'à ce qu’ils perdent le focus, vous ne verrez les modifications apportées aux éléments liés en cours de frappe.

> [!NOTE]
> Ajout de la prise en charge pour la mise à jour dynamique après chaque keypress est simplement une question d’ajout `valueUpdate: "afterkeydown"` à la `data-bind` contenu de l’attribut. Vous pouvez également obtenir ce comportement à l’aide de `data-bind="textInput: authorName"` pour obtenir les mises à jour instantanées de valeurs. 

Notre viewModel, après la mise à jour pour utiliser ko.observable :

```javascript
var viewModel = {
  authorName: ko.observable('Steve Smith')
};
ko.applyBindings(viewModel);
```

Knockout prend en charge un nombre de types de liaisons. Jusqu'à présent, nous avons vu comment lier à `text` et `value`. Vous pouvez également lier à un attribut donné. Par exemple, pour créer un lien hypertexte avec une balise d’ancrage, le `src` attribut peut être lié à ce dernier. Knockout prend également en charge la liaison à des fonctions. Pour illustrer cela, nous allons viewModel pour inclure le handle de twitter de l’auteur de mettre à jour et afficher le handle twitter comme un lien vers la page d’auteur twitter. Pour cela, nous allons en trois étapes.

Tout d’abord, ajoutez le code HTML pour afficher le lien hypertexte, que nous verrons entre parenthèses après le nom de l’auteur :

```html
<h1>Some Article</h1>
<p>
    By <span data-bind="text: authorName"></span>
    (<a data-bind="attr: { href: twitterUrl}, text: twitterAlias" ></a>)
</p>
```

Ensuite, mettez à jour les viewModel pour inclure les propriétés twitterUrl et twitterAlias :

```javascript
var viewModel = {
  authorName: ko.observable('Steve Smith'),
  twitterAlias: ko.observable('@ardalis'),
  twitterUrl: ko.computed(function() {
    return "https://twitter.com/";
  }, this)
};
ko.applyBindings(viewModel);
```

Notez qu’à ce stade nous n’avons pas encore mis à jour le twitterUrl pour accéder à l’URL correcte pour cet alias twitter : il est simplement en pointant sur twitter.com. Notez également que nous utilisons une nouvelle fonction de masquage, `computed`, pour twitterUrl. Il s’agit d’une fonction observable qui vous informe si elle modifie les éléments d’interface utilisateur. Toutefois, pour qu’il ait accès à d’autres propriétés dans viewModel, nous devons modifier comment nous créons le viewModel, afin que chaque propriété est sa propre instruction.

Vous trouverez ci-dessous la déclaration viewModel révisée. Maintenant, il est déclaré en tant que fonction. Notez que chaque propriété est sa propre instruction maintenant, se terminant par un point-virgule. Notez également que pour accéder à la valeur de propriété twitterAlias, nous devons exécuter, de sorte que sa référence contient ().

```javascript
function viewModel() {
  this.authorName = ko.observable('Steve Smith');
  this.twitterAlias = ko.observable('@ardalis');

  this.twitterUrl = ko.computed(function() {
    return "https://twitter.com/" + this.twitterAlias().replace('@','');
  }, this)
};
ko.applyBindings(viewModel);
```

Le résultat fonctionne comme prévu dans le navigateur :

![lien hypertexte de Knockout](knockout/_static/hyperlink-screenshot.png)

Knockout prend également en charge la liaison à certains événements d’élément de l’interface utilisateur, tels que l’événement click. Cela vous permet de facilement et de manière déclarative lier des éléments de l’interface utilisateur à des fonctions dans viewModel de l’application. À titre d’exemple, nous pouvons ajouter un bouton qui, lorsque vous cliquez dessus, modifie twitterAlias l’auteur en majuscules.

Tout d’abord, nous ajoutons le bouton, la liaison à du bouton sur événement faisant référence au nom de fonction Nous allons ajouter au viewModel :

```html
<p>
    <button data-bind="click: capitalizeTwitterAlias">Capitalize</button>
</p>
```

Ensuite, ajoutez la fonction dans le modèle de vues et rattachez pour modifier état du viewModel. Notez que pour définir une nouvelle valeur à la propriété twitterAlias, nous l’appeler comme une méthode et passer de la nouvelle valeur.

```javascript
function viewModel() {
  this.authorName = ko.observable('Steve Smith');
  this.twitterAlias = ko.observable('@ardalis');

  this.twitterUrl = ko.computed(function() {
    return "https://twitter.com/" + this.twitterAlias().replace('@','');
  }, this);

  this.capitalizeTwitterAlias = function() {
    var currentValue = this.twitterAlias();
    this.twitterAlias(currentValue.toUpperCase());
  }
};
ko.applyBindings(viewModel);
```

Le code en cours d’exécution et en cliquant sur le bouton modifie le lien affiché comme prévu :

![mettre en majuscules du lien hypertexte](knockout/_static/hyperlink-caps-screenshot.png)

## <a name="control-flow"></a>Flux de contrôle

Knockout comprend des liaisons qui peuvent effectuer des opérations conditionnelles et de bouclage. Les opérations en boucle sont particulièrement utiles pour la liaison des listes de données à des listes, menus et grilles ou tables de l’interface utilisateur. La liaison foreach effectue une itération sur un tableau. Lorsqu’il est utilisé avec un tableau observable, il met automatiquement à jour les éléments d’interface utilisateur lorsque les éléments sont ajoutés ou supprimés à partir du tableau, sans recréer chaque élément dans l’arborescence de l’interface utilisateur. L’exemple suivant utilise un nouveau viewModel qui inclut un tableau observable des résultats de jeu. Il est lié à une table simple avec deux colonnes en utilisant un `foreach` de liaison de la `<tbody>` élément. Chaque `<tr>` élément dans `<tbody>` sera lié à un élément de la collection gameResults.

```html
<h1>Record</h1>
<table>
    <thead>
        <tr>
            <th>Opponent</th>
            <th>Result</th>
        </tr>
    </thead>
    <tbody data-bind="foreach: gameResults">
        <tr>
            <td data-bind="text:opponent"></td>
            <td data-bind="text:result"></td>
        </tr>
    </tbody>
</table>
<script type="text/javascript">
  function GameResult(opponent, result) {
    var self = this;
    self.opponent = opponent;
    self.result = ko.observable(result);
  }

  function ViewModel() {
    var self = this;

    self.resultChoices = ["Win", "Loss", "Tie"];

    self.gameResults = ko.observableArray([
      new GameResult("Brendan", self.resultChoices[0]),
      new GameResult("Brendan", self.resultChoices[0]),
      new GameResult("Michelle", self.resultChoices[1])
    ]);
  };
  ko.applyBindings(new ViewModel);
</script>
```

Notez que cette fois que nous utilisons ViewModel avec une majuscule « V », car nous pensons que pour la construction à l’aide de « nou » (dans l’appel applyBindings). Lors de l’exécution, la résultats de la page dans la sortie suivante :

![modèle de vue de l’enregistrement de Knockout](knockout/_static/record-screenshot.png)

Pour illustrer l’utilisation de la collection observable, vous allez ajouter un peu plus de fonctionnalités. Nous pouvons incluent la possibilité d’enregistrer les résultats d’un autre jeu au ViewModel, puis ajoutez un bouton et une interface utilisateur pour travailler avec cette nouvelle fonction.  Tout d’abord, nous allons créer la méthode addResult :

```javascript
// add this to ViewModel()
self.addResult = function() {
  self.gameResults.push(new GameResult("", self.resultChoices[0]));
}
```

Lier cette méthode à un bouton à l’aide de la `click` liaison :

```html
<button data-bind="click: addResult">Add New Result</button>
```

Ouvrez la page dans le navigateur et cliquez sur le bouton plusieurs fois, ce qui entraîne une nouvelle ligne de table à chaque clic :

![Ajoutez des résultats](knockout/_static/record-addresult-screenshot.png)

Il existe plusieurs façons pour prendre en charge l’ajout de nouveaux enregistrements dans l’interface utilisateur, en général inline ou dans un autre formulaire. Nous pouvons facilement modifier la table pour utiliser des zones de texte et la compréhension des listes afin que tout est modifiable. Il suffit de modifier le `<tr>` élément, comme indiqué :

```html
<tbody data-bind="foreach: gameResults">
    <tr>
      <td><input data-bind="value:opponent" /></td>
      <td><select data-bind="options: $root.resultChoices, value:result, optionsText: $data"></select></td>
    </tr>
</tbody>
```

Notez que `$root` fait référence à la racine ViewModel, où les valeurs possibles sont exposées. `$data`fait référence à quel que soit le modèle actuel se trouve dans un contexte donné, dans ce cas, qu'il fait référence à un élément individuel du tableau resultChoices, chacun d’eux est une chaîne simple.

Avec cette modification, toute la grille devient modifiable :

![Écran format grille modifiable](knockout/_static/editable-grid-screenshot.png)

Si nous n’utilisions Knockout, nous pouvons obtenir à l’aide de jQuery, mais très probablement qu’il ne serait pas presque aussi efficaces. Knockout suit les données liées dans ViewModel les éléments correspondent à des éléments d’interface utilisateur et met à jour uniquement les éléments qui doivent être ajoutés, supprimés ou mis à jour. Il faudrait un effort considérable pour effectuer cette opération à l’aide de jQuery ou manipulation directe de DOM, et même si nous voulions puis afficher les résultats de regroupement (par exemple, un enregistrement de la perte de win) en fonction des données de la table, nous devez une fois de plus de boucle par son biais et analyser le Éléments HTML.  Avec Knockout, l’affichage de l’enregistrement de la perte de win est trivial. Nous pouvons effectuer les calculs dans le modèle de vues et puis affichez-le avec une liaison de texte simple et un `<span>`.

Pour générer la chaîne d’enregistrement win-perte, nous pouvons utiliser un observable calculée. Notez que les références aux propriétés observables dans ViewModel doit être des appels de fonction, sinon ils ne récupèrent pas la valeur de l’observable (c'est-à-dire `gameResults()` pas `gameResults` dans le code indiqué) :

```javascript
self.displayRecord = ko.computed(function () {
  var wins = self.gameResults().filter(function (value) { return value.result() == "Win"; }).length;
  var losses = self.gameResults().filter(function (value) { return value.result() == "Loss"; }).length;
  var ties = self.gameResults().filter(function (value) { return value.result() == "Tie"; }).length;
  return wins + " - " + losses + " - " + ties;
}, this);
```

Lier cette fonction à une étendue dans le `<h1>` élément en haut de la page :

```html
<h1>Record <span data-bind="text: displayRecord"></span></h1>
```

Le résultat :

![Perte de Win](knockout/_static/record-winloss-screenshot.png)

Ajout de lignes ou de la modification de l’élément sélectionné dans la colonne de résultats d’une ligne met à jour l’enregistrement apparaît en haut de la fenêtre.

En plus de la liaison de valeurs, vous pouvez également utiliser presque toute expression JavaScript juridique au sein d’une liaison. Par exemple, si un élément d’interface utilisateur doit uniquement apparaître sous certaines conditions, par exemple quand une valeur dépasse un certain seuil, vous pouvez spécifier cette logique dans l’expression de liaison :

```html
<div data-bind="visible: customerValue > 100"></div>
```

Cela `<div>` est visible uniquement lorsque le customerValue est supérieures à 100.

## <a name="templates"></a>Modèles

Knockout a prise en charge des modèles, afin que vous puissiez facilement séparer votre interface utilisateur de votre problème, ou charger de façon incrémentielle des éléments d’interface utilisateur dans une grande application à la demande. Nous pouvons mettre à jour notre exemple précédent pour effectuer chaque ligne son propre modèle simplement extrayant de déconnecter le code HTML dans un modèle et en spécifiant le modèle par son nom dans l’appel de la liaison de données sur `<tbody>`.

```html
<tbody data-bind="template: { name: 'rowTemplate', foreach: gameResults }">
</tbody>
<script type="text/html" id="rowTemplate">
  <tr>
      <td><input data-bind="value:opponent" /></td>
      <td><select data-bind="options: $root.resultChoices, value:result, optionsText: $data"></select></td>
  </tr>
</script>
```

Knockout prend également en charge les autres moteurs de création de modèles, tels que la bibliothèque jQuery.tmpl et le moteur de création de modèles de Underscore.js.

## <a name="components"></a>Composants

Composants permettent d’organiser et de réutiliser le code de l’interface utilisateur, généralement en même temps que les données ViewModel dont dépend le code de l’interface utilisateur. Pour créer un composant, vous devez simplement spécifier son modèle et ses viewModel et lui donner un nom. Pour ce faire, il suffit d'appeler `ko.components.register()`. Outre la définition des modèles et viewmodel inline, ils peuvent être chargés à partir de fichiers externes à l’aide d’une bibliothèque, par exemple *require.js*, ce code très claire et plus efficace.

## <a name="communicating-with-apis"></a>Communication avec les API

Knockout peut fonctionner avec toutes les données au format JSON. Est une méthode courante pour récupérer et enregistrer des données à l’aide de Knockout jQuery, qui prend en charge la `$.getJSON()` fonction pour récupérer des données et le `$.post()` méthode pour envoyer des données à partir du navigateur à un point de terminaison d’API. Bien entendu, si vous préférez un autre moyen pour envoyer et recevoir des données JSON, Knockout fonctionnera également avec lui.

## <a name="summary"></a>Résumé

Knockout fournit un moyen simple et élégant pour lier des éléments d’interface utilisateur à l’état actuel de l’application cliente, définie dans un modèle de vues. La syntaxe de liaison de Knockout utilise l’attribut de lier les données, appliqué aux éléments HTML qui doivent être traitées. Knockout est capable de restituer efficacement et de mettre à jour de grands jeux de données en effectuant le suivi des éléments d’interface utilisateur et traitement uniquement les modifications apportées aux affectées des éléments. Les applications volumineuses peuvent diviser les logique de l’interface utilisateur à l’aide de modèles et des composants, ce qui peuvent être chargées à la demande à partir de fichiers externes. Actuellement la version 3, Knockout est une bibliothèque JavaScript stable qui peut améliorer les applications web qui requièrent l’interactivité de client riche.
