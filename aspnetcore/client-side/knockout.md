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
ms.openlocfilehash: 87b4fdc86f6bb870ae0a8cc85688a549fd0740ac
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="knockoutjs-mvvm-framework-in-aspnet-core"></a><span data-ttu-id="69aca-103">Framework Knockout.js MVVM dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="69aca-103">Knockout.js MVVM Framework in ASP.NET Core</span></span>

<span data-ttu-id="69aca-104">Par [Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="69aca-104">By [Steve Smith](http://ardalis.com)</span></span>

<span data-ttu-id="69aca-105">Knockout est une bibliothèque JavaScript courants qui simplifie la création d’interfaces utilisateur de base de données complexes.</span><span class="sxs-lookup"><span data-stu-id="69aca-105">Knockout is a popular JavaScript library that simplifies the creation of complex data-based user interfaces.</span></span> <span data-ttu-id="69aca-106">Il peut être utilisé seul ou avec d’autres bibliothèques, telles que jQuery.</span><span class="sxs-lookup"><span data-stu-id="69aca-106">It can be used alone or with other libraries, such as jQuery.</span></span> <span data-ttu-id="69aca-107">Son principal objectif consiste à lier des éléments d’interface utilisateur à un modèle de données sous-jacent défini comme un objet JavaScript, telles que lorsque des modifications sont apportées à l’interface utilisateur, le modèle est mis à jour et vice versa.</span><span class="sxs-lookup"><span data-stu-id="69aca-107">Its primary purpose is to bind UI elements to an underlying data model defined as a JavaScript object, such that when changes are made to the UI, the model is updated, and vice versa.</span></span> <span data-ttu-id="69aca-108">Knockout facilite l’utilisation d’un modèle Model-View-ViewModel (MVVM) d’un comportement d’une application web côté client.</span><span class="sxs-lookup"><span data-stu-id="69aca-108">Knockout facilitates the use of a Model-View-ViewModel (MVVM) pattern in a web application's client-side behavior.</span></span> <span data-ttu-id="69aca-109">Les deux concepts principaux une doit savoir lorsque vous travaillez avec l’implémentation de MVVM de Knockout sont Observables et les liaisons.</span><span class="sxs-lookup"><span data-stu-id="69aca-109">The two main concepts one must learn when working with Knockout's MVVM implementation are Observables and Bindings.</span></span>

## <a name="getting-started"></a><span data-ttu-id="69aca-110">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="69aca-110">Getting started</span></span>

<span data-ttu-id="69aca-111">Knockout est déployé en tant qu’un seul fichier JavaScript, par conséquent, l’installation et son utilisation est très simple à l’aide de [bower](bower.md).</span><span class="sxs-lookup"><span data-stu-id="69aca-111">Knockout is deployed as a single JavaScript file, so installing and using it is very straightforward using [bower](bower.md).</span></span> <span data-ttu-id="69aca-112">En supposant que vous avez déjà [bower](bower.md) et [gulp](using-gulp.md) configuré, ouvrez *bower.json* dans ASP.NET de la base de données de projet et ajoutez la dépendance de knockout, comme indiqué ici :</span><span class="sxs-lookup"><span data-stu-id="69aca-112">Assuming you already have [bower](bower.md) and [gulp](using-gulp.md) configured, open *bower.json* in your ASP.NET Core project and add the knockout dependency as shown here:</span></span>

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

<span data-ttu-id="69aca-113">Tout cela en place, vous pouvez ensuite exécuter bower en ouvrant le Task Runner Explorer (sous vue ‣ autres fenêtres ‣ Task Runner Explorer) manuellement, puis sous tâches, cliquez sur bower et sélectionnez Exécuter.</span><span class="sxs-lookup"><span data-stu-id="69aca-113">With this in place, you can then manually run bower by opening the Task Runner Explorer (under View ‣ Other Windows ‣ Task Runner Explorer) and then under Tasks, right-click on bower and select Run.</span></span> <span data-ttu-id="69aca-114">Le résultat doit ressembler à ceci :</span><span class="sxs-lookup"><span data-stu-id="69aca-114">The result should appear similar to this:</span></span>

![bower knockout en cours d’exécution dans l’Explorateur d’exécuteur de tâche](knockout/_static/bower-knockout.png)

<span data-ttu-id="69aca-116">Maintenant, si vous regardez dans votre projet `wwwroot` dossier, vous devez voir knockout installé sous le dossier lib.</span><span class="sxs-lookup"><span data-stu-id="69aca-116">Now if you look in your project's `wwwroot` folder, you should see knockout installed under the lib folder.</span></span>

![Knockout installé dans le dossier lib](knockout/_static/wwwroot-knockout.png)

<span data-ttu-id="69aca-118">Il est recommandé que dans votre environnement de production, vous référencez knockout via un réseau de distribution de contenu, ou un CDN, car cela augmente la probabilité que vos utilisateurs disposent déjà d’une copie mise en cache du fichier et seront donc pas à télécharger du tout.</span><span class="sxs-lookup"><span data-stu-id="69aca-118">It's recommended that in your production environment you reference knockout via a Content Delivery Network, or CDN, as this increases the likelihood that your users will already have a cached copy of the file and thus will not need to download it at all.</span></span> <span data-ttu-id="69aca-119">Knockout est disponible sur plusieurs CDN, y compris le CDN Microsoft Ajax, ici :</span><span class="sxs-lookup"><span data-stu-id="69aca-119">Knockout is available on several CDNs, including the Microsoft Ajax CDN, here:</span></span>

[<span data-ttu-id="69aca-120">http://AJAX.aspnetcdn.com/AJAX/Knockout/Knockout-3.3.0.js</span><span class="sxs-lookup"><span data-stu-id="69aca-120">http://ajax.aspnetcdn.com/ajax/knockout/knockout-3.3.0.js</span></span>](http://ajax.aspnetcdn.com/ajax/knockout/knockout-3.3.0.js)

<span data-ttu-id="69aca-121">Pour inclure Knockout sur une page qui l’utilise, ajoutez simplement un `<script>` élément faisant référence au fichier à partir, là où vous allez héberger il (avec votre application, ou via un réseau CDN) :</span><span class="sxs-lookup"><span data-stu-id="69aca-121">To include Knockout on a page that will use it, simply add a `<script>` element referencing the file from wherever you will be hosting it (with your application, or via a CDN):</span></span>

```html
<script type="text/javascript" src="knockout-3.3.0.js"></script>
```

## <a name="observables-viewmodels-and-simple-binding"></a><span data-ttu-id="69aca-122">Observables ViewModel et la liaison simple</span><span class="sxs-lookup"><span data-stu-id="69aca-122">Observables, ViewModels, and simple binding</span></span>

<span data-ttu-id="69aca-123">Vous peut-être déjà familiarisé avec l’aide de JavaScript pour manipuler les éléments sur une page web, soit via un accès direct au DOM ou à l’aide d’une bibliothèque, par exemple jQuery.</span><span class="sxs-lookup"><span data-stu-id="69aca-123">You may already be familiar with using JavaScript to manipulate elements on a web page, either via direct access to the DOM or using a library like jQuery.</span></span> <span data-ttu-id="69aca-124">En règle générale, ce type de comportement est obtenu en écrivant du code pour définir directement des valeurs de l’élément en réponse à certaines actions de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="69aca-124">Typically this kind of behavior is achieved by writing code to directly set element values in response to certain user actions.</span></span> <span data-ttu-id="69aca-125">Avec Knockout, une approche déclarative est effectuée à la place, par le biais duquel les éléments sur la page sont liés aux propriétés sur un objet.</span><span class="sxs-lookup"><span data-stu-id="69aca-125">With Knockout, a declarative approach is taken instead, through which elements on the page are bound to properties on an object.</span></span> <span data-ttu-id="69aca-126">Au lieu d’écrire du code pour manipuler des éléments DOM, les actions utilisateur simplement interagissent avec l’objet ViewModel et Knockout prend en charge de garantir que les éléments de page sont synchronisées.</span><span class="sxs-lookup"><span data-stu-id="69aca-126">Instead of writing code to manipulate DOM elements, user actions simply interact with the ViewModel object, and Knockout takes care of ensuring the page elements are synchronized.</span></span>

<span data-ttu-id="69aca-127">À titre d’exemple, considérez la liste de la page ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="69aca-127">As a simple example, consider the page list below.</span></span> <span data-ttu-id="69aca-128">Elle inclut un `<span>` élément avec un `data-bind` attribut indiquant que le contenu de texte doit être lié à AuthorName ;.</span><span class="sxs-lookup"><span data-stu-id="69aca-128">It includes a `<span>` element with a `data-bind` attribute indicating that the text content should be bound to authorName.</span></span> <span data-ttu-id="69aca-129">Ensuite, dans un bloc JavaScript un viewModel variable est défini avec une seule propriété, `authorName`, définissez une valeur.</span><span class="sxs-lookup"><span data-stu-id="69aca-129">Next, in a JavaScript block a variable viewModel is defined with a single property, `authorName`, set to some value.</span></span> <span data-ttu-id="69aca-130">Enfin, un appel à `ko.applyBindings` est effectuée, en passant cette variable viewModel.</span><span class="sxs-lookup"><span data-stu-id="69aca-130">Finally, a call to `ko.applyBindings` is made, passing in this viewModel variable.</span></span>

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

<span data-ttu-id="69aca-131">Lorsqu’ils sont affichés dans le navigateur, le contenu de la <span> élément est remplacé par la valeur dans la variable viewModel :</span><span class="sxs-lookup"><span data-stu-id="69aca-131">When viewed in the browser, the content of the <span> element is replaced with the value in the viewModel variable:</span></span>

![liaison simple Knockout](knockout/_static/simple-binding-screenshot.png)

<span data-ttu-id="69aca-133">Nous disposons de travail de liaison unidirectionnel simple.</span><span class="sxs-lookup"><span data-stu-id="69aca-133">We now have simple one-way binding working.</span></span> <span data-ttu-id="69aca-134">Notez que nulle part dans le code a nous écrivons JavaScript pour affecter une valeur pour le contenu de l’étendue.</span><span class="sxs-lookup"><span data-stu-id="69aca-134">Notice that nowhere in the code did we write JavaScript to assign a value to the span's contents.</span></span> <span data-ttu-id="69aca-135">Si vous souhaitez manipuler le ViewModel, nous pouvons prendre une étape supplémentaire et ajouter une zone de texte d’entrée HTML et lier à sa valeur, comme pour :</span><span class="sxs-lookup"><span data-stu-id="69aca-135">If we want to manipulate the ViewModel, we can take this a step further and add an HTML input textbox, and bind to its value, like so:</span></span>

```html
<p>
    Author Name: <input type="text" data-bind="value: authorName" />
</p>
```

<span data-ttu-id="69aca-136">Recharger la page, nous constatons que cette valeur est effectivement liée à la zone d’entrée :</span><span class="sxs-lookup"><span data-stu-id="69aca-136">Reloading the page, we see that this value is indeed bound to the input box:</span></span>

![liaison d’entrée de Knockout](knockout/_static/input-binding-screenshot.png)

<span data-ttu-id="69aca-138">Toutefois, si nous modifions la valeur dans la zone de texte, la valeur correspondante dans le `<span>` élément ne change pas.</span><span class="sxs-lookup"><span data-stu-id="69aca-138">However, if we change the value in the textbox, the corresponding value in the `<span>` element doesn't change.</span></span> <span data-ttu-id="69aca-139">Pourquoi ?</span><span class="sxs-lookup"><span data-stu-id="69aca-139">Why not?</span></span>

<span data-ttu-id="69aca-140">Le problème est que rien ne reçoit une notification le `<span>` qu’il devait être mis à jour.</span><span class="sxs-lookup"><span data-stu-id="69aca-140">The issue is that nothing notified the `<span>` that it needed to be updated.</span></span> <span data-ttu-id="69aca-141">Simplement mettre à jour le ViewModel n’est toujours pas suffisant, à moins que les propriétés de ce dernier sont encapsulées dans un type spécial.</span><span class="sxs-lookup"><span data-stu-id="69aca-141">Simply updating the ViewModel isn't by itself sufficient, unless the ViewModel's properties are wrapped in a special type.</span></span> <span data-ttu-id="69aca-142">Nous devons utiliser **observables** dans ViewModel pour toutes les propriétés qui nécessitent des modifications mises à jour automatiquement lorsqu’elles se produisent.</span><span class="sxs-lookup"><span data-stu-id="69aca-142">We need to use **observables** in the ViewModel for any properties that need to have changes automatically updated as they occur.</span></span> <span data-ttu-id="69aca-143">En modifiant le ViewModel à utiliser `ko.observable("value")` au lieu de simplement « valeur », le ViewModel met à jour des éléments HTML qui sont liés à sa valeur chaque fois qu’une modification se produit.</span><span class="sxs-lookup"><span data-stu-id="69aca-143">By changing the ViewModel to use `ko.observable("value")` instead of just "value", the ViewModel will update any HTML elements that are bound to its value whenever a change occurs.</span></span> <span data-ttu-id="69aca-144">Notez que les zones d’entrée ne mettez à jour leur valeur jusqu'à ce qu’ils perdent le focus, vous ne verrez les modifications apportées aux éléments liés en cours de frappe.</span><span class="sxs-lookup"><span data-stu-id="69aca-144">Note that input boxes don't update their value until they lose focus, so you won't see changes to bound elements as you type.</span></span>

> [!NOTE]
> <span data-ttu-id="69aca-145">Ajout de la prise en charge pour la mise à jour dynamique après chaque keypress est simplement une question d’ajout `valueUpdate: "afterkeydown"` à la `data-bind` contenu de l’attribut.</span><span class="sxs-lookup"><span data-stu-id="69aca-145">Adding support for live updating after each keypress is simply a matter of adding `valueUpdate: "afterkeydown"` to the `data-bind` attribute's contents.</span></span> <span data-ttu-id="69aca-146">Vous pouvez également obtenir ce comportement à l’aide de `data-bind="textInput: authorName"` pour obtenir les mises à jour instantanées de valeurs.</span><span class="sxs-lookup"><span data-stu-id="69aca-146">You can also get this behavior by using `data-bind="textInput: authorName"` to get instant updates of values.</span></span> 

<span data-ttu-id="69aca-147">Notre viewModel, après la mise à jour pour utiliser ko.observable :</span><span class="sxs-lookup"><span data-stu-id="69aca-147">Our viewModel, after updating it to use ko.observable:</span></span>

```javascript
var viewModel = {
  authorName: ko.observable('Steve Smith')
};
ko.applyBindings(viewModel);
```

<span data-ttu-id="69aca-148">Knockout prend en charge un nombre de types de liaisons.</span><span class="sxs-lookup"><span data-stu-id="69aca-148">Knockout supports a number of different kinds of bindings.</span></span> <span data-ttu-id="69aca-149">Jusqu'à présent, nous avons vu comment lier à `text` et `value`.</span><span class="sxs-lookup"><span data-stu-id="69aca-149">So far we've seen how to bind to `text` and to `value`.</span></span> <span data-ttu-id="69aca-150">Vous pouvez également lier à un attribut donné.</span><span class="sxs-lookup"><span data-stu-id="69aca-150">You can also bind to any given attribute.</span></span> <span data-ttu-id="69aca-151">Par exemple, pour créer un lien hypertexte avec une balise d’ancrage, le `src` attribut peut être lié à ce dernier.</span><span class="sxs-lookup"><span data-stu-id="69aca-151">For instance, to create a hyperlink with an anchor tag, the `src` attribute can be bound to the viewModel.</span></span> <span data-ttu-id="69aca-152">Knockout prend également en charge la liaison à des fonctions.</span><span class="sxs-lookup"><span data-stu-id="69aca-152">Knockout also supports binding to functions.</span></span> <span data-ttu-id="69aca-153">Pour illustrer cela, nous allons viewModel pour inclure le handle de twitter de l’auteur de mettre à jour et afficher le handle twitter comme un lien vers la page d’auteur twitter.</span><span class="sxs-lookup"><span data-stu-id="69aca-153">To demonstrate this, let's update the viewModel to include the author's twitter handle, and display the twitter handle as a link to the author's twitter page.</span></span> <span data-ttu-id="69aca-154">Pour cela, nous allons en trois étapes.</span><span class="sxs-lookup"><span data-stu-id="69aca-154">We'll do this in three stages.</span></span>

<span data-ttu-id="69aca-155">Tout d’abord, ajoutez le code HTML pour afficher le lien hypertexte, que nous verrons entre parenthèses après le nom de l’auteur :</span><span class="sxs-lookup"><span data-stu-id="69aca-155">First, add the HTML to display the hyperlink, which we'll show in parentheses after the author's name:</span></span>

```html
<h1>Some Article</h1>
<p>
    By <span data-bind="text: authorName"></span>
    (<a data-bind="attr: { href: twitterUrl}, text: twitterAlias" ></a>)
</p>
```

<span data-ttu-id="69aca-156">Ensuite, mettez à jour les viewModel pour inclure les propriétés twitterUrl et twitterAlias :</span><span class="sxs-lookup"><span data-stu-id="69aca-156">Next, update the viewModel to include the twitterUrl and twitterAlias properties:</span></span>

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

<span data-ttu-id="69aca-157">Notez qu’à ce stade nous n’avons pas encore mis à jour le twitterUrl pour accéder à l’URL correcte pour cet alias twitter : il est simplement en pointant sur twitter.com.</span><span class="sxs-lookup"><span data-stu-id="69aca-157">Notice that at this point we haven't yet updated the twitterUrl to go to the correct URL for this twitter alias – it's just pointing at twitter.com.</span></span> <span data-ttu-id="69aca-158">Notez également que nous utilisons une nouvelle fonction de masquage, `computed`, pour twitterUrl.</span><span class="sxs-lookup"><span data-stu-id="69aca-158">Also notice that we're using a new Knockout function, `computed`, for twitterUrl.</span></span> <span data-ttu-id="69aca-159">Il s’agit d’une fonction observable qui vous informe si elle modifie les éléments d’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="69aca-159">This is an observable function that will notify any UI elements if it changes.</span></span> <span data-ttu-id="69aca-160">Toutefois, pour qu’il ait accès à d’autres propriétés dans viewModel, nous devons modifier comment nous créons le viewModel, afin que chaque propriété est sa propre instruction.</span><span class="sxs-lookup"><span data-stu-id="69aca-160">However, for it to have access to other properties in the viewModel, we need to change how we are creating the viewModel, so that each property is its own statement.</span></span>

<span data-ttu-id="69aca-161">Vous trouverez ci-dessous la déclaration viewModel révisée.</span><span class="sxs-lookup"><span data-stu-id="69aca-161">The revised viewModel declaration is shown below.</span></span> <span data-ttu-id="69aca-162">Maintenant, il est déclaré en tant que fonction.</span><span class="sxs-lookup"><span data-stu-id="69aca-162">It is now declared as a function.</span></span> <span data-ttu-id="69aca-163">Notez que chaque propriété est sa propre instruction maintenant, se terminant par un point-virgule.</span><span class="sxs-lookup"><span data-stu-id="69aca-163">Notice that each property is its own statement now, ending with a semicolon.</span></span> <span data-ttu-id="69aca-164">Notez également que pour accéder à la valeur de propriété twitterAlias, nous devons exécuter, de sorte que sa référence contient ().</span><span class="sxs-lookup"><span data-stu-id="69aca-164">Also notice that to access the twitterAlias property value, we need to execute it, so its reference includes ().</span></span>

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

<span data-ttu-id="69aca-165">Le résultat fonctionne comme prévu dans le navigateur :</span><span class="sxs-lookup"><span data-stu-id="69aca-165">The result works as expected in the browser:</span></span>

![lien hypertexte de Knockout](knockout/_static/hyperlink-screenshot.png)

<span data-ttu-id="69aca-167">Knockout prend également en charge la liaison à certains événements d’élément de l’interface utilisateur, tels que l’événement click.</span><span class="sxs-lookup"><span data-stu-id="69aca-167">Knockout also supports binding to certain UI element events, such as the click event.</span></span> <span data-ttu-id="69aca-168">Cela vous permet de facilement et de manière déclarative lier des éléments de l’interface utilisateur à des fonctions dans viewModel de l’application.</span><span class="sxs-lookup"><span data-stu-id="69aca-168">This allows you to easily and declaratively bind UI elements to functions within the application's viewModel.</span></span> <span data-ttu-id="69aca-169">À titre d’exemple, nous pouvons ajouter un bouton qui, lorsque vous cliquez dessus, modifie twitterAlias l’auteur en majuscules.</span><span class="sxs-lookup"><span data-stu-id="69aca-169">As a simple example, we can add a button that, when clicked, modifies the author's twitterAlias to be all caps.</span></span>

<span data-ttu-id="69aca-170">Tout d’abord, nous ajoutons le bouton, la liaison à du bouton sur événement faisant référence au nom de fonction Nous allons ajouter au viewModel :</span><span class="sxs-lookup"><span data-stu-id="69aca-170">First, we add the button, binding to the button's click event, and referencing the function name we're going to add to the viewModel:</span></span>

```html
<p>
    <button data-bind="click: capitalizeTwitterAlias">Capitalize</button>
</p>
```

<span data-ttu-id="69aca-171">Ensuite, ajoutez la fonction dans le modèle de vues et rattachez pour modifier état du viewModel.</span><span class="sxs-lookup"><span data-stu-id="69aca-171">Then, add the function to the viewModel, and wire it up to modify the viewModel's state.</span></span> <span data-ttu-id="69aca-172">Notez que pour définir une nouvelle valeur à la propriété twitterAlias, nous l’appeler comme une méthode et passer de la nouvelle valeur.</span><span class="sxs-lookup"><span data-stu-id="69aca-172">Notice that to set a new value to the twitterAlias property, we call it as a method and pass in the new value.</span></span>

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

<span data-ttu-id="69aca-173">Le code en cours d’exécution et en cliquant sur le bouton modifie le lien affiché comme prévu :</span><span class="sxs-lookup"><span data-stu-id="69aca-173">Running the code and clicking the button modifies the displayed link as expected:</span></span>

![mettre en majuscules du lien hypertexte](knockout/_static/hyperlink-caps-screenshot.png)

## <a name="control-flow"></a><span data-ttu-id="69aca-175">Flux de contrôle</span><span class="sxs-lookup"><span data-stu-id="69aca-175">Control flow</span></span>

<span data-ttu-id="69aca-176">Knockout comprend des liaisons qui peuvent effectuer des opérations conditionnelles et de bouclage.</span><span class="sxs-lookup"><span data-stu-id="69aca-176">Knockout includes bindings that can perform conditional and looping operations.</span></span> <span data-ttu-id="69aca-177">Les opérations en boucle sont particulièrement utiles pour la liaison des listes de données à des listes, menus et grilles ou tables de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="69aca-177">Looping operations are especially useful for binding lists of data to UI lists, menus, and grids or tables.</span></span> <span data-ttu-id="69aca-178">La liaison foreach effectue une itération sur un tableau.</span><span class="sxs-lookup"><span data-stu-id="69aca-178">The foreach binding will iterate over an array.</span></span> <span data-ttu-id="69aca-179">Lorsqu’il est utilisé avec un tableau observable, il met automatiquement à jour les éléments d’interface utilisateur lorsque les éléments sont ajoutés ou supprimés à partir du tableau, sans recréer chaque élément dans l’arborescence de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="69aca-179">When used with an observable array, it will automatically update the UI elements when items are added or removed from the array, without re-creating every element in the UI tree.</span></span> <span data-ttu-id="69aca-180">L’exemple suivant utilise un nouveau viewModel qui inclut un tableau observable des résultats de jeu.</span><span class="sxs-lookup"><span data-stu-id="69aca-180">The following example uses a new viewModel which includes an observable array of game results.</span></span> <span data-ttu-id="69aca-181">Il est lié à une table simple avec deux colonnes en utilisant un `foreach` de liaison de la `<tbody>` élément.</span><span class="sxs-lookup"><span data-stu-id="69aca-181">It is bound to a simple table with two columns using a `foreach` binding on the `<tbody>` element.</span></span> <span data-ttu-id="69aca-182">Chaque `<tr>` élément dans `<tbody>` sera lié à un élément de la collection gameResults.</span><span class="sxs-lookup"><span data-stu-id="69aca-182">Each `<tr>` element within `<tbody>` will be bound to an element of the gameResults collection.</span></span>

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

<span data-ttu-id="69aca-183">Notez que cette fois que nous utilisons ViewModel avec une majuscule « V », car nous pensons que pour la construction à l’aide de « nou » (dans l’appel applyBindings).</span><span class="sxs-lookup"><span data-stu-id="69aca-183">Notice that this time we're using ViewModel with a capital “V" because we expect to construct it using “new" (in the applyBindings call).</span></span> <span data-ttu-id="69aca-184">Lors de l’exécution, la résultats de la page dans la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="69aca-184">When executed, the page results in the following output:</span></span>

![modèle de vue de l’enregistrement de Knockout](knockout/_static/record-screenshot.png)

<span data-ttu-id="69aca-186">Pour illustrer l’utilisation de la collection observable, vous allez ajouter un peu plus de fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="69aca-186">To demonstrate that the observable collection is working, let's add a bit more functionality.</span></span> <span data-ttu-id="69aca-187">Nous pouvons incluent la possibilité d’enregistrer les résultats d’un autre jeu au ViewModel, puis ajoutez un bouton et une interface utilisateur pour travailler avec cette nouvelle fonction.</span><span class="sxs-lookup"><span data-stu-id="69aca-187">We can include the ability to record the results of another game to the ViewModel, and then add a button and some UI to work with this new function.</span></span>  <span data-ttu-id="69aca-188">Tout d’abord, nous allons créer la méthode addResult :</span><span class="sxs-lookup"><span data-stu-id="69aca-188">First, let's create the addResult method:</span></span>

```javascript
// add this to ViewModel()
self.addResult = function() {
  self.gameResults.push(new GameResult("", self.resultChoices[0]));
}
```

<span data-ttu-id="69aca-189">Lier cette méthode à un bouton à l’aide de la `click` liaison :</span><span class="sxs-lookup"><span data-stu-id="69aca-189">Bind this method to a button using the `click` binding:</span></span>

```html
<button data-bind="click: addResult">Add New Result</button>
```

<span data-ttu-id="69aca-190">Ouvrez la page dans le navigateur et cliquez sur le bouton plusieurs fois, ce qui entraîne une nouvelle ligne de table à chaque clic :</span><span class="sxs-lookup"><span data-stu-id="69aca-190">Open the page in the browser and click the button a couple of times, resulting in a new table row with each click:</span></span>

![Ajoutez des résultats](knockout/_static/record-addresult-screenshot.png)

<span data-ttu-id="69aca-192">Il existe plusieurs façons pour prendre en charge l’ajout de nouveaux enregistrements dans l’interface utilisateur, en général inline ou dans un autre formulaire.</span><span class="sxs-lookup"><span data-stu-id="69aca-192">There are a few ways to support adding new records in the UI, typically either inline or in a separate form.</span></span> <span data-ttu-id="69aca-193">Nous pouvons facilement modifier la table pour utiliser des zones de texte et la compréhension des listes afin que tout est modifiable.</span><span class="sxs-lookup"><span data-stu-id="69aca-193">We can easily modify the table to use textboxes and dropdownlists so that the whole thing is editable.</span></span> <span data-ttu-id="69aca-194">Il suffit de modifier le `<tr>` élément, comme indiqué :</span><span class="sxs-lookup"><span data-stu-id="69aca-194">Just change the `<tr>` element as shown:</span></span>

```html
<tbody data-bind="foreach: gameResults">
    <tr>
      <td><input data-bind="value:opponent" /></td>
      <td><select data-bind="options: $root.resultChoices, value:result, optionsText: $data"></select></td>
    </tr>
</tbody>
```

<span data-ttu-id="69aca-195">Notez que `$root` fait référence à la racine ViewModel, où les valeurs possibles sont exposées.</span><span class="sxs-lookup"><span data-stu-id="69aca-195">Note that `$root` refers to the root ViewModel, which is where the possible choices are exposed.</span></span> <span data-ttu-id="69aca-196">`$data`fait référence à quel que soit le modèle actuel se trouve dans un contexte donné, dans ce cas, qu'il fait référence à un élément individuel du tableau resultChoices, chacun d’eux est une chaîne simple.</span><span class="sxs-lookup"><span data-stu-id="69aca-196">`$data` refers to whatever the current model is within a given context - in this case it refers to an individual element of the resultChoices array, each of which is a simple string.</span></span>

<span data-ttu-id="69aca-197">Avec cette modification, toute la grille devient modifiable :</span><span class="sxs-lookup"><span data-stu-id="69aca-197">With this change, the entire grid becomes editable:</span></span>

![Écran format grille modifiable](knockout/_static/editable-grid-screenshot.png)

<span data-ttu-id="69aca-199">Si nous n’utilisions Knockout, nous pouvons obtenir à l’aide de jQuery, mais très probablement qu’il ne serait pas presque aussi efficaces.</span><span class="sxs-lookup"><span data-stu-id="69aca-199">If we weren't using Knockout, we could achieve all of this using jQuery, but most likely it would not be nearly as efficient.</span></span> <span data-ttu-id="69aca-200">Knockout suit les données liées dans ViewModel les éléments correspondent à des éléments d’interface utilisateur et met à jour uniquement les éléments qui doivent être ajoutés, supprimés ou mis à jour.</span><span class="sxs-lookup"><span data-stu-id="69aca-200">Knockout tracks which bound data items in the ViewModel correspond to which UI elements, and only updates those elements that need to be added, removed, or updated.</span></span> <span data-ttu-id="69aca-201">Il faudrait un effort considérable pour effectuer cette opération à l’aide de jQuery ou manipulation directe de DOM, et même si nous voulions puis afficher les résultats de regroupement (par exemple, un enregistrement de la perte de win) en fonction des données de la table, nous devez une fois de plus de boucle par son biais et analyser le Éléments HTML.</span><span class="sxs-lookup"><span data-stu-id="69aca-201">It would take significant effort to achieve this ourselves using jQuery or direct DOM manipulation, and even then if we then wanted to display aggregate results (such as a win-loss record) based on the table's data, we would need to once more loop through it and parse the HTML elements.</span></span>  <span data-ttu-id="69aca-202">Avec Knockout, l’affichage de l’enregistrement de la perte de win est trivial.</span><span class="sxs-lookup"><span data-stu-id="69aca-202">With Knockout, displaying the win-loss record is trivial.</span></span> <span data-ttu-id="69aca-203">Nous pouvons effectuer les calculs dans le modèle de vues et puis affichez-le avec une liaison de texte simple et un `<span>`.</span><span class="sxs-lookup"><span data-stu-id="69aca-203">We can perform the calculations within the ViewModel itself, and then display it with a simple text binding and a `<span>`.</span></span>

<span data-ttu-id="69aca-204">Pour générer la chaîne d’enregistrement win-perte, nous pouvons utiliser un observable calculée.</span><span class="sxs-lookup"><span data-stu-id="69aca-204">To build the win-loss record string, we can use a computed observable.</span></span> <span data-ttu-id="69aca-205">Notez que les références aux propriétés observables dans ViewModel doit être des appels de fonction, sinon ils ne récupèrent pas la valeur de l’observable (c'est-à-dire `gameResults()` pas `gameResults` dans le code indiqué) :</span><span class="sxs-lookup"><span data-stu-id="69aca-205">Note that references to observable properties within the ViewModel must be function calls, otherwise they will not retrieve the value of the observable (i.e. `gameResults()` not `gameResults` in the code shown):</span></span>

```javascript
self.displayRecord = ko.computed(function () {
  var wins = self.gameResults().filter(function (value) { return value.result() == "Win"; }).length;
  var losses = self.gameResults().filter(function (value) { return value.result() == "Loss"; }).length;
  var ties = self.gameResults().filter(function (value) { return value.result() == "Tie"; }).length;
  return wins + " - " + losses + " - " + ties;
}, this);
```

<span data-ttu-id="69aca-206">Lier cette fonction à une étendue dans le `<h1>` élément en haut de la page :</span><span class="sxs-lookup"><span data-stu-id="69aca-206">Bind this function to a span within the `<h1>` element at the top of the page:</span></span>

```html
<h1>Record <span data-bind="text: displayRecord"></span></h1>
```

<span data-ttu-id="69aca-207">Le résultat :</span><span class="sxs-lookup"><span data-stu-id="69aca-207">The result:</span></span>

![Perte de Win](knockout/_static/record-winloss-screenshot.png)

<span data-ttu-id="69aca-209">Ajout de lignes ou de la modification de l’élément sélectionné dans la colonne de résultats d’une ligne met à jour l’enregistrement apparaît en haut de la fenêtre.</span><span class="sxs-lookup"><span data-stu-id="69aca-209">Adding rows or modifying the selected element in any row's Result column will update the record shown at the top of the window.</span></span>

<span data-ttu-id="69aca-210">En plus de la liaison de valeurs, vous pouvez également utiliser presque toute expression JavaScript juridique au sein d’une liaison.</span><span class="sxs-lookup"><span data-stu-id="69aca-210">In addition to binding to values, you can also use almost any legal JavaScript expression within a binding.</span></span> <span data-ttu-id="69aca-211">Par exemple, si un élément d’interface utilisateur doit uniquement apparaître sous certaines conditions, par exemple quand une valeur dépasse un certain seuil, vous pouvez spécifier cette logique dans l’expression de liaison :</span><span class="sxs-lookup"><span data-stu-id="69aca-211">For example, if a UI element should only appear under certain conditions, such as when a value exceeds a certain threshold, you can specify this logically within the binding expression:</span></span>

```html
<div data-bind="visible: customerValue > 100"></div>
```

<span data-ttu-id="69aca-212">Cela `<div>` est visible uniquement lorsque le customerValue est supérieures à 100.</span><span class="sxs-lookup"><span data-stu-id="69aca-212">This `<div>` will only be visible when the customerValue is over 100.</span></span>

## <a name="templates"></a><span data-ttu-id="69aca-213">Modèles</span><span class="sxs-lookup"><span data-stu-id="69aca-213">Templates</span></span>

<span data-ttu-id="69aca-214">Knockout a prise en charge des modèles, afin que vous puissiez facilement séparer votre interface utilisateur de votre problème, ou charger de façon incrémentielle des éléments d’interface utilisateur dans une grande application à la demande.</span><span class="sxs-lookup"><span data-stu-id="69aca-214">Knockout has support for templates, so that you can easily separate your UI from your behavior, or incrementally load UI elements into a large application on demand.</span></span> <span data-ttu-id="69aca-215">Nous pouvons mettre à jour notre exemple précédent pour effectuer chaque ligne son propre modèle simplement extrayant de déconnecter le code HTML dans un modèle et en spécifiant le modèle par son nom dans l’appel de la liaison de données sur `<tbody>`.</span><span class="sxs-lookup"><span data-stu-id="69aca-215">We can update our previous example to make each row its own template by simply pulling the HTML out into a template and specifying the template by name in the data-bind call on `<tbody>`.</span></span>

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

<span data-ttu-id="69aca-216">Knockout prend également en charge les autres moteurs de création de modèles, tels que la bibliothèque jQuery.tmpl et le moteur de création de modèles de Underscore.js.</span><span class="sxs-lookup"><span data-stu-id="69aca-216">Knockout also supports other templating engines, such as the jQuery.tmpl library and Underscore.js's templating engine.</span></span>

## <a name="components"></a><span data-ttu-id="69aca-217">Composants</span><span class="sxs-lookup"><span data-stu-id="69aca-217">Components</span></span>

<span data-ttu-id="69aca-218">Composants permettent d’organiser et de réutiliser le code de l’interface utilisateur, généralement en même temps que les données ViewModel dont dépend le code de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="69aca-218">Components allow you to organize and reuse UI code, usually along with the ViewModel data on which the UI code depends.</span></span> <span data-ttu-id="69aca-219">Pour créer un composant, vous devez simplement spécifier son modèle et ses viewModel et lui donner un nom.</span><span class="sxs-lookup"><span data-stu-id="69aca-219">To create a component, you simply need to specify its template and its viewModel, and give it a name.</span></span> <span data-ttu-id="69aca-220">Pour ce faire, il suffit d'appeler `ko.components.register()`.</span><span class="sxs-lookup"><span data-stu-id="69aca-220">This is done by calling `ko.components.register()`.</span></span> <span data-ttu-id="69aca-221">Outre la définition des modèles et viewmodel inline, ils peuvent être chargés à partir de fichiers externes à l’aide d’une bibliothèque, par exemple *require.js*, ce code très claire et plus efficace.</span><span class="sxs-lookup"><span data-stu-id="69aca-221">In addition to defining the templates and viewmodel inline, they can be loaded from external files using a library like *require.js*, resulting in very clean and efficient code.</span></span>

## <a name="communicating-with-apis"></a><span data-ttu-id="69aca-222">Communication avec les API</span><span class="sxs-lookup"><span data-stu-id="69aca-222">Communicating with APIs</span></span>

<span data-ttu-id="69aca-223">Knockout peut fonctionner avec toutes les données au format JSON.</span><span class="sxs-lookup"><span data-stu-id="69aca-223">Knockout can work with any data in JSON format.</span></span> <span data-ttu-id="69aca-224">Est une méthode courante pour récupérer et enregistrer des données à l’aide de Knockout jQuery, qui prend en charge la `$.getJSON()` fonction pour récupérer des données et le `$.post()` méthode pour envoyer des données à partir du navigateur à un point de terminaison d’API.</span><span class="sxs-lookup"><span data-stu-id="69aca-224">A common way to retrieve and save data using Knockout is with jQuery, which supports the `$.getJSON()` function to retrieve data, and the `$.post()` method to send data from the browser to an API endpoint.</span></span> <span data-ttu-id="69aca-225">Bien entendu, si vous préférez un autre moyen pour envoyer et recevoir des données JSON, Knockout fonctionnera également avec lui.</span><span class="sxs-lookup"><span data-stu-id="69aca-225">Of course, if you prefer a different way to send and receive JSON data, Knockout will work with it as well.</span></span>

## <a name="summary"></a><span data-ttu-id="69aca-226">Résumé</span><span class="sxs-lookup"><span data-stu-id="69aca-226">Summary</span></span>

<span data-ttu-id="69aca-227">Knockout fournit un moyen simple et élégant pour lier des éléments d’interface utilisateur à l’état actuel de l’application cliente, définie dans un modèle de vues.</span><span class="sxs-lookup"><span data-stu-id="69aca-227">Knockout provides a simple, elegant way to bind UI elements to the current state of the client application, defined in a ViewModel.</span></span> <span data-ttu-id="69aca-228">La syntaxe de liaison de Knockout utilise l’attribut de lier les données, appliqué aux éléments HTML qui doivent être traitées.</span><span class="sxs-lookup"><span data-stu-id="69aca-228">Knockout's binding syntax uses the data-bind attribute, applied to HTML elements that are to be processed.</span></span> <span data-ttu-id="69aca-229">Knockout est capable de restituer efficacement et de mettre à jour de grands jeux de données en effectuant le suivi des éléments d’interface utilisateur et traitement uniquement les modifications apportées aux affectées des éléments.</span><span class="sxs-lookup"><span data-stu-id="69aca-229">Knockout is able to efficiently render and update large data sets by tracking UI elements and only processing changes to affected elements.</span></span> <span data-ttu-id="69aca-230">Les applications volumineuses peuvent diviser les logique de l’interface utilisateur à l’aide de modèles et des composants, ce qui peuvent être chargées à la demande à partir de fichiers externes.</span><span class="sxs-lookup"><span data-stu-id="69aca-230">Large applications can break up UI logic using templates and components, which can be loaded on demand from external files.</span></span> <span data-ttu-id="69aca-231">Actuellement la version 3, Knockout est une bibliothèque JavaScript stable qui peut améliorer les applications web qui requièrent l’interactivité de client riche.</span><span class="sxs-lookup"><span data-stu-id="69aca-231">Currently version 3, Knockout is a stable JavaScript library that can improve web applications that require rich client interactivity.</span></span>
