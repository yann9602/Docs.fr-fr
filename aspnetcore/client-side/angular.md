---
title: "À l’aide d’AngularJS pour des Applications à Page unique (ZPS)"
author: rick-anderson
description: "Découvrez comment créer une application de style SPA ASP.NET à l’aide d’AngularJS"
keywords: ASP.NET Core, AngularJS, SPA
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4b30576b-2718-4c39-9253-a59966747893
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/angular
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ccdf1625cdaf2400780500ac5ab86f41537964a9
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="using-angularjs-for-single-page-applications-spas-with-aspnet-core"></a>À l’aide d’AngularJS pour des Applications à Page unique (ZPS) avec ASP.NET Core


Par [Venkata Koppaka](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/) et [Scott Addie](https://scottaddie.com)

Dans cet article, vous allez apprendre à créer une application de style SPA ASP.NET à l’aide d’AngularJS.

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="what-is-angularjs"></a>Nouveautés AngularJS

[AngularJS](https://angularjs.org/) est une infrastructure JavaScript moderne à partir de Google couramment utilisé pour travailler avec des Applications à Page unique (ZPS). AngularJS est open source sous licence du MIT, et la progression du développement d’AngularJS peut être suivie [son référentiel GitHub](https://github.com/angular/angular.js). La bibliothèque est appelée angulaire car HTML utilise des crochets de forme angulaire.

AngularJS n’est pas une bibliothèque de manipulation de DOM comme jQuery, mais il utilise un sous-ensemble de jQuery, appelé jQLite. AngularJS est principalement basée sur des attributs déclaratifs HTML que vous pouvez ajouter à vos balises HTML. Vous pouvez essayer de AngularJS dans votre navigateur à l’aide de la [site Web de l’école de Code](https://www.codeschool.com/courses/shaping-up-with-angularjs) ou [site Web de W3Schools](https://www.w3schools.com/angular/).

Cet article se concentre sur AngularJS avec quelques remarques sur où angulaire est un en-tête.

## <a name="getting-started"></a>Bien démarrer

Pour démarrer à l’aide d’AngularJS dans votre application ASP.NET, vous devez l’installer en tant que partie de votre projet, ou référencer à partir d’un réseau de diffusion de contenu (CDN).

### <a name="installation"></a>Installation

Il existe plusieurs façons d’ajouter AngularJS à votre application. Si vous démarrez une application web ASP.NET Core dans Visual Studio, vous pouvez ajouter AngularJS à l’aide de la fonction intégrée [Bower](bower.md) prennent en charge. Ouvrez *bower.json*et ajouter une entrée à la `dependencies` propriété :

<a name="angular-bower-json"></a>

[!code-json[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/bower.json?highlight=9)]

Lors de l’enregistrement du *bower.json* fichier angulaire est installé dans votre projet *wwwroot/lib* dossier. En outre, il sera répertorié dans le `Dependencies/Bower` dossier. Consultez la capture d’écran ci-dessous.

![Explorateur de solutions avec AngularJS projet](angular/_static/angular-solution-explorer.png)

Ensuite, ajoutez un `<script>` référence vers le bas de la `<body>` section de votre page HTML ou *_Layout.cshtml* de fichiers, comme indiqué ici :

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=4&range=48-52)]

Il est recommandé que les applications de production utilisent CDN pour les bibliothèques communes comme AngularJS. Vous pouvez référencer AngularJS parmi plusieurs CDN, telles que celle-ci :

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=10&range=53-67)]

Une fois que vous avez une référence à la *angular.js* fichier de script, vous êtes prêt à commencer à l’aide d’AngularJS dans vos pages web.

## <a name="key-components"></a>Composants clés

AngularJS inclut un nombre de composants principaux, tels que *directives*, *modèles*, *répéteurs*, *modules*,  *contrôleurs*, *composants*, *routeur de composant* et bien plus encore. Nous allons examiner comment ces composants fonctionnent ensemble pour ajouter un comportement à vos pages web.

### <a name="directives"></a>Directives

AngularJS utilise [directives](https://docs.angularjs.org/guide/directive) pour étendre le HTML avec des éléments et attributs personnalisés. Les directives AngularJS sont définis `data-ng-*` ou `ng-*` préfixes (`ng` est l’abréviation d’angulaire). Il existe deux types de directives AngularJS :

   1. **Directives primitifs**: ceux-ci sont prédéfinis par l’équipe angulaire et font partie de l’infrastructure AngularJS.

   2. **Directives personnalisées**: il s’agit des directives personnalisées que vous pouvez définir.

Une des directives primitifs utilisés dans toutes les applications AngularJS est la `ng-app` directive amorce l’application AngularJS. Cette directive peut être appliquée à la `<body>` balise ou à un élément enfant du corps. Examinons un exemple en action. En supposant que vous êtes dans un projet ASP.NET, vous pouvez ajouter un fichier HTML pour le `wwwroot` dossier, ou ajoutez une nouvelle action de contrôleur et une vue associée. Dans ce cas, j’ai ajouté une nouvelle `Directives` méthode d’action à `HomeController.cs`. La vue associée est illustrée ici :

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Directives.cshtml?highlight=5,7)]

Pour conserver ces exemples indépendants, je n’utilise le fichier de disposition partagé. Vous pouvez voir que nous décorées la balise body avec la `ng-app` directive pour indiquer cette page est une application AngularJS. Le `{{2+2}}` est une expression de liaison de données angulaire vous découvrirez plus tard. Voici le résultat si vous exécutez cette application :

![Directive angulaire simple](angular/_static/simple-directive.png)

Autres directives AngularJS primitifs sont les suivantes :

`ng-controller`Détermine quel contrôleur JavaScript est lié à la vue.

`ng-model`Détermine le modèle auquel les valeurs des propriétés d’un élément HTML sont liées.

`ng-init`Utilisé pour initialiser les données d’application sous la forme d’une expression de l’étendue actuelle.

`ng-if`Supprime ou recrée l’élément HTML donné dans le DOM en fonction de la truthiness de l’expression fournie.

`ng-repeat`Répète un bloc de code HTML donné sur un jeu de données.

`ng-show`Affiche ou masque l’élément HTML donné en fonction de l’expression fournie.

Pour obtenir une liste complète de toutes les directives primitifs pris en charge dans AngularJS, reportez-vous à la [section documentation directive sur le site Web de documentation AngularJS](https://docs.angularjs.org/api/ng/directive).

### <a name="data-binding"></a>Liaison de données

Fournit des AngularJS [liaison de données](https://docs.angularjs.org/guide/databinding) out-of-the-box à l’aide de prendre en charge la `ng-bind` directive ou une syntaxe d’expression de liaison comme des données `{{expression}}`. AngularJS prend en charge la liaison de données bidirectionnelle où les données à partir d’un modèle sont conservées dans la synchronisation avec un modèle d’affichage à tout moment. Toute modification apportée à la vue est automatiquement répercutées dans le modèle. De même, toutes les modifications dans le modèle sont répercutées dans la vue.

Créer un fichier HTML ou une action du contrôleur avec une vue qui l’accompagne nommée `Databinding`. Dans la vue, sont les suivantes :

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Databinding.cshtml?highlight=8,9,10)]

Notez que vous pouvez afficher les valeurs du modèle à l’aide de la liaison de directives ou de données (`ng-bind`). La page résultante doit ressembler à ceci :

![Liaison de données simple](angular/_static/simple-databinding.png)

### <a name="templates"></a>Modèles

[Modèles](https://docs.angularjs.org/guide/templates) dans AngularJS sont des pages HTML simples décorées avec les directives AngularJS et d’artefacts. Un modèle dans AngularJS est une combinaison de directives, les expressions, les filtres et les contrôles qui combinent avec du code HTML pour le mode formulaire.

Ajouter une autre vue pour illustrer les modèles et ajouter les éléments suivants :

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Templates.cshtml?highlight=8,9,10)]

Le modèle a des directives AngularJS comme `ng-app`, `ng-init`, `ng-model` et la syntaxe d’expression de liaison de données pour lier le `personName` propriété. En cours d’exécution dans le navigateur, l’affichage ressemble à la capture d’écran ci-dessous :

![Exemple simple de modèles 1](angular/_static/simple-templates-1.png)

Si vous modifiez le nom en le tapant dans le champ d’entrée, vous verrez le texte en regard du champ d’entrée dynamiquement mise à jour, montrant une liaison de données bidirectionnelle angulaire en action.

![Exemple simple de modèles 2](angular/_static/simple-templates-2.png)

### <a name="expressions"></a>Expressions

[Expressions](https://docs.angularjs.org/guide/expression) dans AngularJS sont extraits de code de type JavaScript qui sont écrits à l’intérieur de la `{{ expression }}` syntaxe. Les données à partir de ces expressions sont liées au format HTML de la même façon que `ng-bind` directives. La principale différence entre les expressions d’AngularJS et d’expressions régulières que JavaScript est que AngularJS les expressions sont évaluées par rapport à la `$scope` objet dans AngularJS.

Les expressions d’AngularJS dans l’exemple ci-dessous liaison `personName` et un code JavaScript simple calculée expression :

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Expressions.cshtml?highlight=8,9,10)]

L’exemple en cours d’exécution dans le navigateur affiche le `personName` données et les résultats du calcul :

![Expressions simples](angular/_static/simple-expressions.png)

### <a name="repeaters"></a>Répéteurs

Extensible de AngularJS est effectuée via une directive primitifs appelée `ng-repeat`. Le `ng-repeat` directive répète un élément HTML donné dans une vue sur la longueur d’un tableau de données extensible. Répétez répéteurs dans AngularJS sur un tableau de chaînes ou objets. Voici un exemple d’utilisation de répétition sur un tableau de chaînes :

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters.cshtml?highlight=8,10,11)]

Le [repeat (directive)](https://docs.angularjs.org/api/ng/directive/ngRepeat) génère une série d’éléments de liste dans une liste non triée, comme vous pouvez le voir dans les outils de développement indiqués dans cette capture d’écran :

![Exemple de répéteur](angular/_static/repeater.png)

Voici un exemple qui se répète sur un tableau d’objets. Le `ng-init` directive établit un `names` tableau, où chaque élément est un objet contenant le premier et le nom. Le `ng-repeat` l’attribution, `name in names`, génère un élément de liste pour chaque élément du tableau.

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters2.cshtml?highlight=8,9,10,11,13,14)]

La sortie dans ce cas est le même que dans l’exemple précédent.

Angulaire fournit certaines directives supplémentaires qui peuvent aider à fournir un comportement en fonction de la boucle dans son exécution.

`$index`

Utilisez `$index` dans le `ng-repeat` se trouve sur la boucle pour déterminer quelle position d’index à votre boucle actuellement.

`$even` et `$odd`

Utilisez `$even` dans le `ng-repeat` boucle pour déterminer si l’index actuel dans la boucle est une ligne même indexée. De même, utilisez `$odd` pour déterminer si l’index actuel est une ligne indexée impaire.

`$first` et `$last`

Utilisez `$first` dans le `ng-repeat` boucle pour déterminer si l’index actuel dans la boucle est la première ligne. De même, utilisez `$last` pour déterminer si l’index actuel est la dernière ligne.

Voici un exemple qui montre `$index`, `$even`, `$odd`, `$first`, et `$last` en action :

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters3.cshtml?highlight=14,15,16,17,18)]

Voici le résultat obtenu :

![Exemple de répéteur 2](angular/_static/repeaters2.png)

### <a name="scope"></a>$scope

`$scope`est un objet JavaScript qui agit comme un collage entre la vue (modèle) et le contrôleur (voir ci-après). Un modèle d’affichage dans AngularJS connaît uniquement les valeurs liées à la `$scope` objet dans le contrôleur.

> [!NOTE]
> Dans le monde MVVM, le `$scope` objet dans AngularJS est souvent définie comme le ViewModel. L’équipe AngularJS fait référence à la `$scope` objet en tant que le modèle de données. [En savoir plus sur les étendues dans AngularJS](https://docs.angularjs.org/guide/scope).

Voici un exemple simple illustrant comment définir des propriétés sur `$scope` dans un fichier JavaScript distinct, *scope.js*:

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/scope.js?highlight=2,3)]

Observez le `$scope` paramètre passé au contrôleur sur la ligne 2. Cet objet est ce que connaît la vue. Sur la ligne 3, nous définissons une propriété appelée « name » à « Mary Jane ».

Que se passe-t-il quand une propriété particulière n’est pas trouvée par la vue ? La vue définie ci-dessous fait référence aux propriétés de « nom » et « age » :

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Scope.cshtml?highlight=9,10,14)]

Notez à la ligne 9 que nous demandons angulaire pour afficher la propriété « name » à l’aide de la syntaxe d’expression. Ligne 10 alors fait référence à « age », une propriété qui n’existe pas. L’exemple en cours affiche le nom de la valeur « Mary Jane » et rien d’âge. Propriétés manquantes sont ignorées.

![Exemple d’étendue](angular/_static/scope.png)

### <a name="modules"></a>Modules

A [module](https://docs.angularjs.org/guide/module) dans AngularJS est une collection de contrôleurs, les services, les directives, etc. Le `angular.module()` appel de fonction est utilisé pour créer, enregistrer et de récupérer les modules dans AngularJS. Tous les modules, y compris ceux fournis par l’équipe de AngularJS et les bibliothèques de tierce partie, doivent être inscrites à l’aide de la `angular.module()` (fonction).

Voici un extrait de code qui montre comment créer un nouveau module dans AngularJS. Le premier paramètre est le nom du module. Le deuxième paramètre définit des dépendances sur d’autres modules. Plus loin dans cet article, nous montrant comment transmettre ces dépendances pour un `angular.module()` appel de méthode.

```javascript
var personApp = angular.module('personApp', []);
```

Utilisez la `ng-app` directive pour représenter un module AngularJS sur la page. Pour utiliser un module, d’affecter le nom du module, `personApp` dans cet exemple, pour le `ng-app` directive dans notre modèle.

```html
<body ng-app="personApp">
```

### <a name="controllers"></a>Contrôleurs

[Contrôleurs](https://docs.angularjs.org/guide/controller) dans AngularJS sont le premier point d’entrée pour votre code. Le `<module name>.controller()` appel de fonction est utilisé pour créer et inscrire les contrôleurs dans AngularJS. Le `ng-controller` directive est utilisée pour représenter un contrôleur AngularJS sur la page HTML. Le rôle du contrôleur dans angulaire consiste à définir l’état et le comportement du modèle de données (`$scope`). Contrôleurs de ne doivent pas être utilisés pour manipuler le DOM directement.

Voici un extrait de code qui inscrit un nouveau contrôleur. Le `personApp` variable dans l’extrait de code fait référence à un module angulaire, qui est défini sur la ligne 2.

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/controllers.js?highlight=2,5)]

La vue à l’aide de la `ng-controller` directive attribue le nom du contrôleur :

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Controllers.cshtml?highlight=8,14)]

La page affiche « Mary » et « Jane » qui correspondent à la `firstName` et `lastName` propriétés attaché à la `$scope` objet :

![Exemple de contrôleur](angular/_static/controllers.png)

### <a name="components"></a>Composants

[Composants](https://docs.angularjs.org/guide/component) dans angulaire 1.5.x autoriser pour l’encapsulation et la capacité de créer des éléments HTML individuels. Vous pouvez d’obtenir la même fonctionnalité à l’aide de la méthode .directive() dans 1.4.x angulaire.

À l’aide de la méthode .component(), développement est simplifié gagner de la fonctionnalité de la directive et le contrôleur. Autres avantages ; isolement de la portée, les meilleures pratiques sont inhérentes et migration 2 angulaire devient une tâche plus facile. Le `<module name>.component()` appel de fonction est utilisé pour créer et inscrire des composants dans AngularJS.

Voici un extrait de code qui inscrit un nouveau composant. Le `personApp` variable dans l’extrait de code fait référence à un module angulaire, qui est défini sur la ligne 2.

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/components.js?highlight=2,5,13)]

La vue dans laquelle nous affichons l’élément HTML personnalisé.

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Components.cshtml?highlight=8)]

Le modèle associé par composant :

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personcomponent.html?highlight=2,3)]

La page affiche « Aftab » et « Ansari » qui correspondent à la `firstName` et `lastName` propriétés attaché à la `vm` objet :

![Exemple de composants](angular/_static/components.png)

### <a name="services"></a>Services

[Services](https://docs.angularjs.org/guide/services) dans AngularJS sont couramment utilisées pour le code partagé qui est abstrait dans un fichier qui peut être utilisé dans l’ensemble de la durée de vie d’une application angulaire. Les services sont instanciés tardivement, ce qui signifie qu’il pas sera une instance d’un service, sauf si un composant dont dépend le service obtient utilisé. Fabriques sont un exemple de service utilisé dans les applications d’AngularJS. Fabriques sont créés à l’aide de la `myApp.factory()` , l’appel de fonction où `myApp` est le module.

Voici un exemple qui montre comment utiliser des fabriques dans AngularJS :

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/simpleFactory.js?highlight=1)]

Pour appeler cette fabrique à partir du contrôleur, passez `personFactory` en tant que paramètre à la `controller` (fonction) :

```javascript
personApp.controller('personController', function($scope,personFactory) {
  $scope.name = personFactory.getName();
});
```

### <a name="using-services-to-talk-to-a-rest-endpoint"></a>À l’aide des services pour communiquer avec un point de terminaison REST

Ci-dessous est un exemple de bout en bout à l’aide de services dans AngularJS pour interagir avec un point de terminaison de l’API Web ASP.NET principale. L’exemple obtienne les données de l’API Web et affiche les données dans un modèle d’affichage. Nous allons commencer par la vue :

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Index.cshtml?highlight=5,8,10,17,18,19)]

Dans cette vue, nous avons un module angulaire appelé `PersonsApp` et un contrôleur appelé `personController`. Nous utilisons `ng-repeat` d’itérer sur la liste des personnes. Nous référençons trois fichiers JavaScript personnalisés sur les lignes 17-19.

Le *personApp.js* fichier est utilisé pour inscrire le `PersonsApp` module ; et la syntaxe est semblable aux exemples précédents. Nous utilisons le `angular.module` fonction permettant de créer une nouvelle instance du module qui nous travaillerons avec.

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personApp.js?highlight=3)]

Examinons un *personFactory.js*, ci-dessous. Nous appelons du module `factory` méthode pour créer une fabrique. La ligne 12 montre l’angulaire intégré `$http` la récupération des informations sur les personnes à partir d’un service web de service.

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personFactory.js?highlight=6,7,12)]

Dans *personController.js*, nous appelons du module `controller` pour créer le contrôleur. Le `$scope` l’objet `people` les données retournées à partir de la personFactory (ligne 13) est assignée à la propriété.

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personController.js?highlight=6,7,13)]

Jetons un œil rapide à l’API Web et le modèle derrière lui. Le `Person` modèle est un POCO (objet CLR simple) avec `Id`, `FirstName`, et `LastName` propriétés :

[!code-csharp[Main](angular/sample/AngularJSSample/src/AngularJSSample/Models/Person.cs)]

Le `Person` contrôleur retourne une liste au format JSON de `Person` objets :

[!code-csharp[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Controllers/Api/PersonController.cs?highlight=9,10,19)]

Nous allons voir l’application en action :

![Contrôleur affichant autres résultats](angular/_static/rest-bound.png)

Vous pouvez [permet d’afficher la structure de l’application sur GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample).

> [!NOTE]
> Pour plus d’informations sur l’organisation des applications d’AngularJS, consultez [Guide de Style de John Papa angulaire](https://github.com/johnpapa/angular-styleguide)

&nbsp;

> [!NOTE]
> Pour créer le module AngularJS, contrôleur, la fabrique, directive et afficher les fichiers facilement, veillez à consulter du Sayed Hashimi [pack de modèle SideWaffle pour Visual Studio](http://sidewaffle.com/). Sayed Hashimi est un type de programme senior de l’équipe Visual Studio Web Microsoft et SideWaffle modèles sont considérées comme la norme or. Au moment de la rédaction, SideWaffle est disponible pour Visual Studio 2012, 2013 et 2015.

### <a name="routing-and-multiple-views"></a>Routage et vues multiples

AngularJS dispose d’un fournisseur de routage intégrée pour gérer la navigation de SPA (Application à Page unique) en fonction. Pour utiliser le routage AngularJS, vous devez ajouter le `angular-route` bibliothèque à l’aide de Bower. Vous pouvez voir dans le [bower.json](#angular-bower-json) fichier référencé au début de cet article que nous sommes déjà y faire référence dans notre projet.

Après avoir installé le package, ajoutez la référence de script (*angular-route.js*) à votre affichage.

Maintenant examinons l’application de la personne nous ont été génération et ajouter la navigation à celui-ci. Tout d’abord, nous effectuer une copie de l’application en créant un `PeopleController` action appelée `Spa` et `Spa.cshtml` vue en copiant la vue Index.cshtml dans le `People` dossier. Ajouter une référence de script à `angular-route` (voir la ligne 11). Également ajouter un `div` marquée avec la `ng-view` directive (voir la ligne 6) comme espace réservé de placer des vues dans. Nous allons utiliser plusieurs supplémentaires *.js* les fichiers qui sont référencés dans les lignes 13 à 16.

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Spa.cshtml?highlight=6,11,12,13,14,15,16)]

Examinons un *personModule.js* fichier pour voir comment nous examinons l’instanciation du module avec le routage. Nous passons `ngRoute` en tant que bibliothèque dans le module. Ce module gère le routage dans notre application.

[!code-javascript[Main](angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personModule.js)]

Le *personRoutes.js* fichier ci-dessous, définit les itinéraires basés sur le fournisseur de l’itinéraire. Définissent la navigation en disant efficacement lorsqu’une URL avec lignes 4-7 `/persons` est demandé, utilisez un modèle appelé `partials/personlist` en passant par `personListController`. Les lignes 8 à 11 indiquent une page de détails avec un paramètre d’itinéraire de `personId`. Si l’URL ne correspond pas à un des modèles, angulaire par défaut est le `/persons` vue.

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personRoutes.js?highlight=4,5,6,7,8,9,10,11,13)]

Le `personlist.html` fichier est une vue partielle contenant uniquement le code HTML que nécessaire pour afficher la liste de personnes.

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personlist.html?highlight=3)]

Le contrôleur est défini à l’aide du module `controller` fonctionner dans *personListController.js*.

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personListController.js?highlight=1)]

Si nous exécuter cette application et accédez à la `people/spa#/persons` URL, nous allons voir :

![Mode liste de personnes](angular/_static/spa-persons.png)

Si nous, accédez à une page de détails, par exemple `people/spa#/persons/2`, nous allons voir la vue partielle en détail :

![Vue Détails](angular/_static/spa-persons-2.png)

Vous pouvez afficher la source complète et ne pas illustrés dans cet article sur tous les fichiers [GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample).

### <a name="event-handlers"></a>Gestionnaires d'événements

Il existe un certain nombre de directives dans AngularJS qui ajoutent des fonctionnalités de gestion des événements pour les éléments d’entrée dans votre DOM. HTML Vous trouverez ci-dessous la liste des événements qui sont intégrées à AngularJS.

   * `ng-click`

   * `ng-dbl-click`

   * `ng-mousedown`

   * `ng-mouseup`

   * `ng-mouseenter`

   * `ng-mouseleave`

   * `ng-mousemove`

   * `ng-keydown`

   * `ng-keyup`

   * `ng-keypress`

   * `ng-change`

> [!NOTE]
> Vous pouvez ajouter vos propres gestionnaires d’événements à l’aide de la [directives personnalisées de fonctionnalité dans AngularJS](https://docs.angularjs.org/guide/directive).

Voyons comment la `ng-click` événements sont associés. Créer un fichier JavaScript nommé *eventHandlerController.js*et lui ajouter les éléments suivants :

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/eventHandlerController.js?highlight=5,6,7)]

Remarquez le nouveau `sayName` fonctionner dans `eventHandlerController` à la ligne 5 ci-dessus. Effectue un ensemble de la méthode pour maintenant affiche une alerte de JavaScript à l’utilisateur avec un message d’accueil.

La vue ci-dessous lie une fonction de contrôleur à un événement AngularJS. La ligne 9 comporte un bouton sur lequel le `ng-click` directive angulaire a été appliquée. Il appelle notre `sayName` fonction, qui est attachée à la `$scope` objet passé à cette vue.

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Events.cshtml?highlight=9)]

L’exemple en cours d’exécution montre que le contrôleur de `sayName` fonction est appelée automatiquement lorsque le bouton est activé.

![Événements Click](angular/_static/events.png)

Pour plus d’informations sur les directives de gestionnaire d’événements AngularJS, veillez à tête pour la [site Web de documentation](https://docs.angularjs.org/api/ng/directive/ngClick) d’AngularJS.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Docs angulaires](https://docs.angularjs.org)

* [Info 2 angulaire](https://angular.io/)
