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
ms.openlocfilehash: 50d2e76c472e67c26238abee4f7b0ed64cd043ab
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="using-angularjs-for-single-page-applications-spas-with-aspnet-core"></a><span data-ttu-id="1d1ed-104">À l’aide d’AngularJS pour des Applications à Page unique (ZPS) avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d1ed-104">Using AngularJS for Single Page Applications (SPAs) with ASP.NET Core</span></span>


<span data-ttu-id="1d1ed-105">Par [Venkata Koppaka](http://blog.falafel.com/author/venkata-koppaka/) et [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="1d1ed-105">By [Venkata Koppaka](http://blog.falafel.com/author/venkata-koppaka/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="1d1ed-106">Dans cet article, vous allez apprendre à créer une application de style SPA ASP.NET à l’aide d’AngularJS.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-106">In this article, you will learn how to build a SPA-style ASP.NET application using AngularJS.</span></span>

[<span data-ttu-id="1d1ed-107">Afficher ou télécharger l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="1d1ed-107">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample)

## <a name="what-is-angularjs"></a><span data-ttu-id="1d1ed-108">Nouveautés AngularJS</span><span class="sxs-lookup"><span data-stu-id="1d1ed-108">What is AngularJS?</span></span>

<span data-ttu-id="1d1ed-109">[AngularJS](http://angularjs.org/) est une infrastructure JavaScript moderne à partir de Google couramment utilisé pour travailler avec des Applications à Page unique (ZPS).</span><span class="sxs-lookup"><span data-stu-id="1d1ed-109">[AngularJS](http://angularjs.org/) is a modern JavaScript framework from Google commonly used to work with Single Page Applications (SPAs).</span></span> <span data-ttu-id="1d1ed-110">AngularJS est open source sous licence du MIT, et la progression du développement d’AngularJS peut être suivie [son référentiel GitHub](https://github.com/angular/angular.js).</span><span class="sxs-lookup"><span data-stu-id="1d1ed-110">AngularJS is open sourced under MIT license, and the development progress of AngularJS can be followed on [its GitHub repository](https://github.com/angular/angular.js).</span></span> <span data-ttu-id="1d1ed-111">La bibliothèque est appelée angulaire car HTML utilise des crochets de forme angulaire.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-111">The library is called Angular because HTML uses angular-shaped brackets.</span></span>

<span data-ttu-id="1d1ed-112">AngularJS n’est pas une bibliothèque de manipulation de DOM comme jQuery, mais il utilise un sous-ensemble de jQuery, appelé jQLite.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-112">AngularJS is not a DOM manipulation library like jQuery, but it uses a subset of jQuery called jQLite.</span></span> <span data-ttu-id="1d1ed-113">AngularJS est principalement basée sur des attributs déclaratifs HTML que vous pouvez ajouter à vos balises HTML.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-113">AngularJS is primarily based on declarative HTML attributes that you can add to your HTML tags.</span></span> <span data-ttu-id="1d1ed-114">Vous pouvez essayer de AngularJS dans votre navigateur à l’aide de la [site Web de l’école de Code](https://www.codeschool.com/courses/shaping-up-with-angular-js) ou [site Web de W3Schools](https://www.w3schools.com/angular/).</span><span class="sxs-lookup"><span data-stu-id="1d1ed-114">You can try AngularJS in your browser using the [Code School website](https://www.codeschool.com/courses/shaping-up-with-angular-js) or  [W3Schools website](https://www.w3schools.com/angular/).</span></span>

<span data-ttu-id="1d1ed-115">Cet article se concentre sur AngularJS avec quelques remarques sur où angulaire est un en-tête.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-115">This article focuses on AngularJS with some notes on where Angular is heading.</span></span>

## <a name="getting-started"></a><span data-ttu-id="1d1ed-116">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="1d1ed-116">Getting started</span></span>

<span data-ttu-id="1d1ed-117">Pour démarrer à l’aide d’AngularJS dans votre application ASP.NET, vous devez l’installer en tant que partie de votre projet, ou référencer à partir d’un réseau de diffusion de contenu (CDN).</span><span class="sxs-lookup"><span data-stu-id="1d1ed-117">To start using AngularJS in your ASP.NET application, you must either install it as part of your project, or reference it from a content delivery network (CDN).</span></span>

### <a name="installation"></a><span data-ttu-id="1d1ed-118">Installation</span><span class="sxs-lookup"><span data-stu-id="1d1ed-118">Installation</span></span>

<span data-ttu-id="1d1ed-119">Il existe plusieurs façons d’ajouter AngularJS à votre application.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-119">There are several ways to add AngularJS to your application.</span></span> <span data-ttu-id="1d1ed-120">Si vous démarrez une application web ASP.NET Core dans Visual Studio, vous pouvez ajouter AngularJS à l’aide de la fonction intégrée [Bower](bower.md) prennent en charge.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-120">If you’re starting a new ASP.NET Core web application in Visual Studio, you can add AngularJS using the built-in [Bower](bower.md) support.</span></span> <span data-ttu-id="1d1ed-121">Ouvrez *bower.json*et ajouter une entrée à la `dependencies` propriété :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-121">Open *bower.json*, and add an entry to the `dependencies` property:</span></span>

<a name=angular-bower-json></a>

<span data-ttu-id="1d1ed-122">[!code-json[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/bower.json?highlight=9)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-122">[!code-json[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/bower.json?highlight=9)]</span></span>

<span data-ttu-id="1d1ed-123">Lors de l’enregistrement du *bower.json* fichier angulaire est installé dans votre projet *wwwroot/lib* dossier.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-123">Upon saving the *bower.json* file, Angular will be installed in your project's *wwwroot/lib* folder.</span></span> <span data-ttu-id="1d1ed-124">En outre, il sera répertorié dans le `Dependencies/Bower` dossier.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-124">Additionally, it will be listed within the `Dependencies/Bower` folder.</span></span> <span data-ttu-id="1d1ed-125">Consultez la capture d’écran ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-125">See the screenshot below.</span></span>

![Explorateur de solutions avec AngularJS projet](angular/_static/angular-solution-explorer.png)

<span data-ttu-id="1d1ed-127">Ensuite, ajoutez un `<script>` référence vers le bas de la `<body>` section de votre page HTML ou *_Layout.cshtml* de fichiers, comme indiqué ici :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-127">Next, add a `<script>` reference to the bottom of the `<body>` section of your HTML page or *_Layout.cshtml* file, as shown here:</span></span>

<span data-ttu-id="1d1ed-128">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=4&range=48-52)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-128">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=4&range=48-52)]</span></span>

<span data-ttu-id="1d1ed-129">Il est recommandé que les applications de production utilisent CDN pour les bibliothèques communes comme AngularJS.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-129">It's recommended that production applications utilize CDNs for common libraries like AngularJS.</span></span> <span data-ttu-id="1d1ed-130">Vous pouvez référencer AngularJS parmi plusieurs CDN, telles que celle-ci :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-130">You can reference AngularJS from one of several CDNs, such as this one:</span></span>

<span data-ttu-id="1d1ed-131">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=10&range=53-67)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-131">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=10&range=53-67)]</span></span>

<span data-ttu-id="1d1ed-132">Une fois que vous avez une référence à la *angular.js* fichier de script, vous êtes prêt à commencer à l’aide d’AngularJS dans vos pages web.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-132">Once you have a reference to the *angular.js* script file, you're ready to begin using AngularJS in your web pages.</span></span>

## <a name="key-components"></a><span data-ttu-id="1d1ed-133">Composants clés</span><span class="sxs-lookup"><span data-stu-id="1d1ed-133">Key components</span></span>

<span data-ttu-id="1d1ed-134">AngularJS inclut un nombre de composants principaux, tels que *directives*, *modèles*, *répéteurs*, *modules*,  *contrôleurs*, *composants*, *routeur de composant* et bien plus encore.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-134">AngularJS includes a number of major components, such as *directives*, *templates*, *repeaters*, *modules*, *controllers*, *components*, *component router* and more.</span></span> <span data-ttu-id="1d1ed-135">Nous allons examiner comment ces composants fonctionnent ensemble pour ajouter un comportement à vos pages web.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-135">Let's examine how these components work together to add behavior to your web pages.</span></span>

### <a name="directives"></a><span data-ttu-id="1d1ed-136">Directives</span><span class="sxs-lookup"><span data-stu-id="1d1ed-136">Directives</span></span>

<span data-ttu-id="1d1ed-137">AngularJS utilise [directives](https://docs.angularjs.org/guide/directive) pour étendre le HTML avec des éléments et attributs personnalisés.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-137">AngularJS uses [directives](https://docs.angularjs.org/guide/directive) to extend HTML with custom attributes and elements.</span></span> <span data-ttu-id="1d1ed-138">Les directives AngularJS sont définis `data-ng-*` ou `ng-*` préfixes (`ng` est l’abréviation d’angulaire).</span><span class="sxs-lookup"><span data-stu-id="1d1ed-138">AngularJS directives are defined via `data-ng-*` or `ng-*` prefixes (`ng` is short for angular).</span></span> <span data-ttu-id="1d1ed-139">Il existe deux types de directives AngularJS :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-139">There are two types of AngularJS directives:</span></span>

   1. <span data-ttu-id="1d1ed-140">**Directives primitifs**: ceux-ci sont prédéfinis par l’équipe angulaire et font partie de l’infrastructure AngularJS.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-140">**Primitive Directives**: These are predefined by the Angular team and are part of the AngularJS framework.</span></span>

   2. <span data-ttu-id="1d1ed-141">**Directives personnalisées**: il s’agit des directives personnalisées que vous pouvez définir.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-141">**Custom Directives**: These are custom directives that you can define.</span></span>

<span data-ttu-id="1d1ed-142">Une des directives primitifs utilisés dans toutes les applications AngularJS est la `ng-app` directive amorce l’application AngularJS.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-142">One of the primitive directives used in all AngularJS applications is the `ng-app` directive, which bootstraps the AngularJS application.</span></span> <span data-ttu-id="1d1ed-143">Cette directive peut être appliquée à la `<body>` balise ou à un élément enfant du corps.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-143">This directive can be applied to the `<body>` tag or to a child element of the body.</span></span> <span data-ttu-id="1d1ed-144">Examinons un exemple en action.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-144">Let's see an example in action.</span></span> <span data-ttu-id="1d1ed-145">En supposant que vous êtes dans un projet ASP.NET, vous pouvez ajouter un fichier HTML pour le `wwwroot` dossier, ou ajoutez une nouvelle action de contrôleur et une vue associée.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-145">Assuming you're in an ASP.NET project, you can either add an HTML file to the `wwwroot` folder, or add a new controller action and an associated view.</span></span> <span data-ttu-id="1d1ed-146">Dans ce cas, j’ai ajouté une nouvelle `Directives` méthode d’action à `HomeController.cs`.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-146">In this case, I've added a new `Directives` action method to `HomeController.cs`.</span></span> <span data-ttu-id="1d1ed-147">La vue associée est illustrée ici :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-147">The associated view is shown here:</span></span>

<span data-ttu-id="1d1ed-148">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Directives.cshtml?highlight=5,7)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-148">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Directives.cshtml?highlight=5,7)]</span></span>

<span data-ttu-id="1d1ed-149">Pour conserver ces exemples indépendants, je n’utilise le fichier de disposition partagé.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-149">To keep these samples independent of one another, I'm not using the shared layout file.</span></span> <span data-ttu-id="1d1ed-150">Vous pouvez voir que nous décorées la balise body avec la `ng-app` directive pour indiquer cette page est une application AngularJS.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-150">You can see that we decorated the body tag with the `ng-app` directive to indicate this page is an AngularJS application.</span></span> <span data-ttu-id="1d1ed-151">Le `{{2+2}}` est une expression de liaison de données angulaire vous découvrirez plus tard.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-151">The `{{2+2}}` is an Angular data binding expression that you will learn more about in a moment.</span></span> <span data-ttu-id="1d1ed-152">Voici le résultat si vous exécutez cette application :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-152">Here is the result if you run this application:</span></span>

![Directive angulaire simple](angular/_static/simple-directive.png)

<span data-ttu-id="1d1ed-154">Autres directives AngularJS primitifs sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-154">Other primitive directives in AngularJS include:</span></span>

<span data-ttu-id="1d1ed-155">`ng-controller`Détermine quel contrôleur JavaScript est lié à la vue.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-155">`ng-controller` Determines which JavaScript controller is bound to which view.</span></span>

<span data-ttu-id="1d1ed-156">`ng-model`Détermine le modèle auquel les valeurs des propriétés d’un élément HTML sont liées.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-156">`ng-model` Determines the model to which the values of an HTML element's properties are bound.</span></span>

<span data-ttu-id="1d1ed-157">`ng-init`Utilisé pour initialiser les données d’application sous la forme d’une expression de l’étendue actuelle.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-157">`ng-init` Used to initialize the application data in the form of an expression for the current scope.</span></span>

<span data-ttu-id="1d1ed-158">`ng-if`Supprime ou recrée l’élément HTML donné dans le DOM en fonction de la truthiness de l’expression fournie.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-158">`ng-if` Removes or recreates the given HTML element in the DOM based on the truthiness of the expression provided.</span></span>

<span data-ttu-id="1d1ed-159">`ng-repeat`Répète un bloc de code HTML donné sur un jeu de données.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-159">`ng-repeat` Repeats a given block of HTML over a set of data.</span></span>

<span data-ttu-id="1d1ed-160">`ng-show`Affiche ou masque l’élément HTML donné en fonction de l’expression fournie.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-160">`ng-show` Shows or hides the given HTML element based on the expression provided.</span></span>

<span data-ttu-id="1d1ed-161">Pour obtenir une liste complète de toutes les directives primitifs pris en charge dans AngularJS, reportez-vous à la [section documentation directive sur le site Web de documentation AngularJS](https://docs.angularjs.org/api/ng/directive).</span><span class="sxs-lookup"><span data-stu-id="1d1ed-161">For a full list of all primitive directives supported in AngularJS, please refer to the [directive documentation section on the AngularJS documentation website](https://docs.angularjs.org/api/ng/directive).</span></span>

### <a name="data-binding"></a><span data-ttu-id="1d1ed-162">Liaison de données</span><span class="sxs-lookup"><span data-stu-id="1d1ed-162">Data binding</span></span>

<span data-ttu-id="1d1ed-163">Fournit des AngularJS [liaison de données](https://docs.angularjs.org/guide/databinding) out-of-the-box à l’aide de prendre en charge la `ng-bind` directive ou une syntaxe d’expression de liaison comme des données `{{expression}}`.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-163">AngularJS provides [data binding](https://docs.angularjs.org/guide/databinding) support out-of-the-box using either the `ng-bind` directive or a data binding expression syntax such as `{{expression}}`.</span></span> <span data-ttu-id="1d1ed-164">AngularJS prend en charge la liaison de données bidirectionnelle où les données à partir d’un modèle sont conservées dans la synchronisation avec un modèle d’affichage à tout moment.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-164">AngularJS supports two-way data binding where data from a model is kept in synchronization with a view template at all times.</span></span> <span data-ttu-id="1d1ed-165">Toute modification apportée à la vue est automatiquement répercutées dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-165">Any changes to the view are automatically reflected in the model.</span></span> <span data-ttu-id="1d1ed-166">De même, toutes les modifications dans le modèle sont répercutées dans la vue.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-166">Likewise, any changes in the model are reflected in the view.</span></span>

<span data-ttu-id="1d1ed-167">Créer un fichier HTML ou une action du contrôleur avec une vue qui l’accompagne nommée `Databinding`.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-167">Create either an HTML file or a controller action with an accompanying view named `Databinding`.</span></span> <span data-ttu-id="1d1ed-168">Dans la vue, sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-168">Include the following in the view:</span></span>

<span data-ttu-id="1d1ed-169">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Databinding.cshtml?highlight=8,9,10)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-169">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Databinding.cshtml?highlight=8,9,10)]</span></span>

<span data-ttu-id="1d1ed-170">Notez que vous pouvez afficher les valeurs du modèle à l’aide de la liaison de directives ou de données (`ng-bind`).</span><span class="sxs-lookup"><span data-stu-id="1d1ed-170">Notice that you can display model values using either directives or data binding (`ng-bind`).</span></span> <span data-ttu-id="1d1ed-171">La page résultante doit ressembler à ceci :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-171">The resulting page should look like this:</span></span>

![Liaison de données simple](angular/_static/simple-databinding.png)

### <a name="templates"></a><span data-ttu-id="1d1ed-173">Modèles</span><span class="sxs-lookup"><span data-stu-id="1d1ed-173">Templates</span></span>

<span data-ttu-id="1d1ed-174">[Modèles](https://docs.angularjs.org/guide/templates) dans AngularJS sont des pages HTML simples décorées avec les directives AngularJS et d’artefacts.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-174">[Templates](https://docs.angularjs.org/guide/templates) in AngularJS are just plain HTML pages decorated with AngularJS directives and artifacts.</span></span> <span data-ttu-id="1d1ed-175">Un modèle dans AngularJS est une combinaison de directives, les expressions, les filtres et les contrôles qui combinent avec du code HTML pour le mode formulaire.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-175">A template in AngularJS is a mixture of directives, expressions, filters, and controls that combine with HTML to form the view.</span></span>

<span data-ttu-id="1d1ed-176">Ajouter une autre vue pour illustrer les modèles et ajouter les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-176">Add another view to demonstrate templates, and add the following to it:</span></span>

<span data-ttu-id="1d1ed-177">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Templates.cshtml?highlight=8,9,10)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-177">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Templates.cshtml?highlight=8,9,10)]</span></span>

<span data-ttu-id="1d1ed-178">Le modèle a des directives AngularJS comme `ng-app`, `ng-init`, `ng-model` et la syntaxe d’expression de liaison de données pour lier le `personName` propriété.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-178">The template has AngularJS directives like `ng-app`, `ng-init`, `ng-model` and data binding expression syntax to bind the `personName` property.</span></span> <span data-ttu-id="1d1ed-179">En cours d’exécution dans le navigateur, l’affichage ressemble à la capture d’écran ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-179">Running in the browser, the view looks like the screenshot below:</span></span>

![Exemple simple de modèles 1](angular/_static/simple-templates-1.png)

<span data-ttu-id="1d1ed-181">Si vous modifiez le nom en le tapant dans le champ d’entrée, vous verrez le texte en regard du champ d’entrée dynamiquement mise à jour, montrant une liaison de données bidirectionnelle angulaire en action.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-181">If you change the name by typing in the input field, you will see the text next to the input field dynamically update, showing Angular two-way data binding in action.</span></span>

![Exemple simple de modèles 2](angular/_static/simple-templates-2.png)

### <a name="expressions"></a><span data-ttu-id="1d1ed-183">Expressions</span><span class="sxs-lookup"><span data-stu-id="1d1ed-183">Expressions</span></span>

<span data-ttu-id="1d1ed-184">[Expressions](https://docs.angularjs.org/guide/expression) dans AngularJS sont extraits de code de type JavaScript qui sont écrits à l’intérieur de la `{{ expression }}` syntaxe.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-184">[Expressions](https://docs.angularjs.org/guide/expression) in AngularJS are JavaScript-like code snippets that are written inside the `{{ expression }}` syntax.</span></span> <span data-ttu-id="1d1ed-185">Les données à partir de ces expressions sont liées au format HTML de la même façon que `ng-bind` directives.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-185">The data from these expressions is bound to HTML the same way as `ng-bind` directives.</span></span> <span data-ttu-id="1d1ed-186">La principale différence entre les expressions d’AngularJS et d’expressions régulières que JavaScript est que AngularJS les expressions sont évaluées par rapport à la `$scope` objet dans AngularJS.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-186">The main difference between AngularJS expressions and regular JavaScript expressions is that AngularJS expressions are evaluated against the `$scope` object in AngularJS.</span></span>

<span data-ttu-id="1d1ed-187">Les expressions d’AngularJS dans l’exemple ci-dessous liaison `personName` et un code JavaScript simple calculée expression :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-187">The AngularJS expressions in the sample below bind `personName` and a simple JavaScript calculated expression:</span></span>

<span data-ttu-id="1d1ed-188">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Expressions.cshtml?highlight=8,9,10)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-188">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Expressions.cshtml?highlight=8,9,10)]</span></span>

<span data-ttu-id="1d1ed-189">L’exemple en cours d’exécution dans le navigateur affiche le `personName` données et les résultats du calcul :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-189">The example running in the browser displays the `personName` data and the results of the calculation:</span></span>

![Expressions simples](angular/_static/simple-expressions.png)

### <a name="repeaters"></a><span data-ttu-id="1d1ed-191">Répéteurs</span><span class="sxs-lookup"><span data-stu-id="1d1ed-191">Repeaters</span></span>

<span data-ttu-id="1d1ed-192">Extensible de AngularJS est effectuée via une directive primitifs appelée `ng-repeat`.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-192">Repeating in AngularJS is done via a primitive directive called `ng-repeat`.</span></span> <span data-ttu-id="1d1ed-193">Le `ng-repeat` directive répète un élément HTML donné dans une vue sur la longueur d’un tableau de données extensible.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-193">The `ng-repeat` directive repeats a given HTML element in a view over the length of a repeating data array.</span></span> <span data-ttu-id="1d1ed-194">Répétez répéteurs dans AngularJS sur un tableau de chaînes ou objets.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-194">Repeaters in AngularJS can repeat over an array of strings or objects.</span></span> <span data-ttu-id="1d1ed-195">Voici un exemple d’utilisation de répétition sur un tableau de chaînes :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-195">Here is a sample usage of repeating over an array of strings:</span></span>

<span data-ttu-id="1d1ed-196">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters.cshtml?highlight=8,10,11)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-196">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters.cshtml?highlight=8,10,11)]</span></span>

<span data-ttu-id="1d1ed-197">Le [repeat (directive)](https://docs.angularjs.org/api/ng/directive/ngRepeat) génère une série d’éléments de liste dans une liste non triée, comme vous pouvez le voir dans les outils de développement indiqués dans cette capture d’écran :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-197">The [repeat directive](https://docs.angularjs.org/api/ng/directive/ngRepeat) outputs a series of list items in an unordered list, as you can see in the developer tools shown in this screenshot:</span></span>

![Exemple de répéteur](angular/_static/repeater.png)

<span data-ttu-id="1d1ed-199">Voici un exemple qui se répète sur un tableau d’objets.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-199">Here is an example that repeats over an array of objects.</span></span> <span data-ttu-id="1d1ed-200">Le `ng-init` directive établit un `names` tableau, où chaque élément est un objet contenant le premier et le nom.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-200">The `ng-init` directive establishes a `names` array, where each element is an object containing first and last names.</span></span> <span data-ttu-id="1d1ed-201">Le `ng-repeat` l’attribution, `name in names`, génère un élément de liste pour chaque élément du tableau.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-201">The `ng-repeat` assignment, `name in names`, outputs a list item for every array element.</span></span>

<span data-ttu-id="1d1ed-202">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters2.cshtml?highlight=8,9,10,11,13,14)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-202">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters2.cshtml?highlight=8,9,10,11,13,14)]</span></span>

<span data-ttu-id="1d1ed-203">La sortie dans ce cas est le même que dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-203">The output in this case is the same as in the previous example.</span></span>

<span data-ttu-id="1d1ed-204">Angulaire fournit certaines directives supplémentaires qui peuvent aider à fournir un comportement en fonction de la boucle dans son exécution.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-204">Angular provides some additional directives that can help provide behavior based on where the loop is in its execution.</span></span>

`$index`

<span data-ttu-id="1d1ed-205">Utilisez `$index` dans le `ng-repeat` se trouve sur la boucle pour déterminer quelle position d’index à votre boucle actuellement.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-205">Use `$index` in the `ng-repeat` loop to determine which index position your loop currently is on.</span></span>

<span data-ttu-id="1d1ed-206">`$even` et `$odd`</span><span class="sxs-lookup"><span data-stu-id="1d1ed-206">`$even` and `$odd`</span></span>

<span data-ttu-id="1d1ed-207">Utilisez `$even` dans le `ng-repeat` boucle pour déterminer si l’index actuel dans la boucle est une ligne même indexée.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-207">Use `$even` in the `ng-repeat` loop to determine whether the current index in your loop is an even indexed row.</span></span> <span data-ttu-id="1d1ed-208">De même, utilisez `$odd` pour déterminer si l’index actuel est une ligne indexée impaire.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-208">Similarly, use `$odd` to determine if the current index is an odd indexed row.</span></span>

<span data-ttu-id="1d1ed-209">`$first` et `$last`</span><span class="sxs-lookup"><span data-stu-id="1d1ed-209">`$first` and `$last`</span></span>

<span data-ttu-id="1d1ed-210">Utilisez `$first` dans le `ng-repeat` boucle pour déterminer si l’index actuel dans la boucle est la première ligne.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-210">Use `$first` in the `ng-repeat` loop to determine whether the current index in your loop is the first row.</span></span> <span data-ttu-id="1d1ed-211">De même, utilisez `$last` pour déterminer si l’index actuel est la dernière ligne.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-211">Similarly, use `$last` to determine if the current index is the last row.</span></span>

<span data-ttu-id="1d1ed-212">Voici un exemple qui montre `$index`, `$even`, `$odd`, `$first`, et `$last` en action :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-212">Below is a sample that shows `$index`, `$even`, `$odd`, `$first`, and `$last` in action:</span></span>

<span data-ttu-id="1d1ed-213">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters3.cshtml?highlight=14,15,16,17,18)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-213">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters3.cshtml?highlight=14,15,16,17,18)]</span></span>

<span data-ttu-id="1d1ed-214">Voici le résultat obtenu :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-214">Here is the resulting output:</span></span>

![Exemple de répéteur 2](angular/_static/repeaters2.png)

### <a name="scope"></a><span data-ttu-id="1d1ed-216">$scope</span><span class="sxs-lookup"><span data-stu-id="1d1ed-216">$scope</span></span>

<span data-ttu-id="1d1ed-217">`$scope`est un objet JavaScript qui agit comme un collage entre la vue (modèle) et le contrôleur (voir ci-après).</span><span class="sxs-lookup"><span data-stu-id="1d1ed-217">`$scope` is a JavaScript object that acts as glue between the view (template) and the controller (explained below).</span></span> <span data-ttu-id="1d1ed-218">Un modèle d’affichage dans AngularJS connaît uniquement les valeurs liées à la `$scope` objet dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-218">A view template in AngularJS only knows about the values attached to the `$scope` object in the controller.</span></span>

> [!NOTE]
> <span data-ttu-id="1d1ed-219">Dans le monde MVVM, le `$scope` objet dans AngularJS est souvent définie comme le ViewModel.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-219">In the MVVM world, the `$scope` object in AngularJS is often defined as the ViewModel.</span></span> <span data-ttu-id="1d1ed-220">L’équipe AngularJS fait référence à la `$scope` objet en tant que le modèle de données.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-220">The AngularJS team refers to the `$scope` object as the Data-Model.</span></span> <span data-ttu-id="1d1ed-221">[En savoir plus sur les étendues dans AngularJS](https://docs.angularjs.org/guide/scope).</span><span class="sxs-lookup"><span data-stu-id="1d1ed-221">[Learn more about Scopes in AngularJS](https://docs.angularjs.org/guide/scope).</span></span>

<span data-ttu-id="1d1ed-222">Voici un exemple simple illustrant comment définir des propriétés sur `$scope` dans un fichier JavaScript distinct, *scope.js*:</span><span class="sxs-lookup"><span data-stu-id="1d1ed-222">Below is a simple example showing how to set properties on `$scope` within a separate JavaScript file, *scope.js*:</span></span>

<span data-ttu-id="1d1ed-223">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/scope.js?highlight=2,3)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-223">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/scope.js?highlight=2,3)]</span></span>

<span data-ttu-id="1d1ed-224">Observez le `$scope` paramètre passé au contrôleur sur la ligne 2.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-224">Observe the `$scope` parameter passed to the controller on line 2.</span></span> <span data-ttu-id="1d1ed-225">Cet objet est ce que connaît la vue.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-225">This object is what the view knows about.</span></span> <span data-ttu-id="1d1ed-226">Sur la ligne 3, nous définissons une propriété appelée « name » à « Mary Jane ».</span><span class="sxs-lookup"><span data-stu-id="1d1ed-226">On line 3, we are setting a property called "name" to "Mary Jane".</span></span>

<span data-ttu-id="1d1ed-227">Que se passe-t-il quand une propriété particulière n’est pas trouvée par la vue ?</span><span class="sxs-lookup"><span data-stu-id="1d1ed-227">What happens when a particular property is not found by the view?</span></span> <span data-ttu-id="1d1ed-228">La vue définie ci-dessous fait référence aux propriétés de « nom » et « age » :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-228">The view defined below refers to "name" and "age" properties:</span></span>

<span data-ttu-id="1d1ed-229">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Scope.cshtml?highlight=9,10,14)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-229">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Scope.cshtml?highlight=9,10,14)]</span></span>

<span data-ttu-id="1d1ed-230">Notez à la ligne 9 que nous demandons angulaire pour afficher la propriété « name » à l’aide de la syntaxe d’expression.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-230">Notice on line 9 that we are asking Angular to show the "name" property using expression syntax.</span></span> <span data-ttu-id="1d1ed-231">Ligne 10 alors fait référence à « age », une propriété qui n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-231">Line 10 then refers to "age", a property that does not exist.</span></span> <span data-ttu-id="1d1ed-232">L’exemple en cours affiche le nom de la valeur « Mary Jane » et rien d’âge.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-232">The running example shows the name set to "Mary Jane" and nothing for age.</span></span> <span data-ttu-id="1d1ed-233">Propriétés manquantes sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-233">Missing properties are ignored.</span></span>

![Exemple d’étendue](angular/_static/scope.png)

### <a name="modules"></a><span data-ttu-id="1d1ed-235">Modules</span><span class="sxs-lookup"><span data-stu-id="1d1ed-235">Modules</span></span>

<span data-ttu-id="1d1ed-236">A [module](https://docs.angularjs.org/guide/module) dans AngularJS est une collection de contrôleurs, les services, les directives, etc.. Le `angular.module()` appel de fonction est utilisé pour créer, enregistrer et de récupérer les modules dans AngularJS.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-236">A [module](https://docs.angularjs.org/guide/module) in AngularJS is a collection of controllers, services, directives, etc. The `angular.module()` function call is used to create, register, and retrieve modules in AngularJS.</span></span> <span data-ttu-id="1d1ed-237">Tous les modules, y compris ceux fournis par l’équipe de AngularJS et les bibliothèques de tierce partie, doivent être inscrites à l’aide de la `angular.module()` (fonction).</span><span class="sxs-lookup"><span data-stu-id="1d1ed-237">All modules, including those shipped by the AngularJS team and third party libraries, should be registered using the `angular.module()` function.</span></span>

<span data-ttu-id="1d1ed-238">Voici un extrait de code qui montre comment créer un nouveau module dans AngularJS.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-238">Below is a snippet of code that shows how to create a new module in AngularJS.</span></span> <span data-ttu-id="1d1ed-239">Le premier paramètre est le nom du module.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-239">The first parameter is the name of the module.</span></span> <span data-ttu-id="1d1ed-240">Le deuxième paramètre définit des dépendances sur d’autres modules.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-240">The second parameter defines dependencies on other modules.</span></span> <span data-ttu-id="1d1ed-241">Plus loin dans cet article, nous montrant comment transmettre ces dépendances pour un `angular.module()` appel de méthode.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-241">Later in this article, we will be showing how to pass these dependencies to an `angular.module()` method call.</span></span>

```javascript
var personApp = angular.module('personApp', []);
```

<span data-ttu-id="1d1ed-242">Utilisez la `ng-app` directive pour représenter un module AngularJS sur la page.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-242">Use the `ng-app` directive to represent an AngularJS module on the page.</span></span> <span data-ttu-id="1d1ed-243">Pour utiliser un module, d’affecter le nom du module, `personApp` dans cet exemple, pour le `ng-app` directive dans notre modèle.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-243">To use a module, assign the name of the module, `personApp` in this example, to the `ng-app` directive in our template.</span></span>

```html
<body ng-app="personApp">
```

### <a name="controllers"></a><span data-ttu-id="1d1ed-244">Contrôleurs</span><span class="sxs-lookup"><span data-stu-id="1d1ed-244">Controllers</span></span>

<span data-ttu-id="1d1ed-245">[Contrôleurs](https://docs.angularjs.org/guide/controller) dans AngularJS sont le premier point d’entrée pour votre code.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-245">[Controllers](https://docs.angularjs.org/guide/controller) in AngularJS are the first point of entry for your code.</span></span> <span data-ttu-id="1d1ed-246">Le `<module name>.controller()` appel de fonction est utilisé pour créer et inscrire les contrôleurs dans AngularJS.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-246">The `<module name>.controller()` function call is used to create and register controllers in AngularJS.</span></span> <span data-ttu-id="1d1ed-247">Le `ng-controller` directive est utilisée pour représenter un contrôleur AngularJS sur la page HTML.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-247">The `ng-controller` directive is used to represent an AngularJS controller on the HTML page.</span></span> <span data-ttu-id="1d1ed-248">Le rôle du contrôleur dans angulaire consiste à définir l’état et le comportement du modèle de données (`$scope`).</span><span class="sxs-lookup"><span data-stu-id="1d1ed-248">The role of the controller in Angular is to set state and behavior of the data model (`$scope`).</span></span> <span data-ttu-id="1d1ed-249">Contrôleurs de ne doivent pas être utilisés pour manipuler le DOM directement.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-249">Controllers should not be used to manipulate the DOM directly.</span></span>

<span data-ttu-id="1d1ed-250">Voici un extrait de code qui inscrit un nouveau contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-250">Below is a snippet of code that registers a new controller.</span></span> <span data-ttu-id="1d1ed-251">Le `personApp` variable dans l’extrait de code fait référence à un module angulaire, qui est défini sur la ligne 2.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-251">The `personApp` variable in the snippet references an Angular module, which is defined on line 2.</span></span>

<span data-ttu-id="1d1ed-252">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/controllers.js?highlight=2,5)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-252">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/controllers.js?highlight=2,5)]</span></span>

<span data-ttu-id="1d1ed-253">La vue à l’aide de la `ng-controller` directive attribue le nom du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-253">The view using the `ng-controller` directive assigns the controller name:</span></span>

<span data-ttu-id="1d1ed-254">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Controllers.cshtml?highlight=8,14)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-254">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Controllers.cshtml?highlight=8,14)]</span></span>

<span data-ttu-id="1d1ed-255">La page affiche « Mary » et « Jane » qui correspondent à la `firstName` et `lastName` propriétés attaché à la `$scope` objet :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-255">The page shows "Mary" and "Jane" that correspond to the `firstName` and `lastName` properties attached to the `$scope` object:</span></span>

![Exemple de contrôleur](angular/_static/controllers.png)

### <a name="components"></a><span data-ttu-id="1d1ed-257">Composants</span><span class="sxs-lookup"><span data-stu-id="1d1ed-257">Components</span></span>

<span data-ttu-id="1d1ed-258">[Composants](https://docs.angularjs.org/guide/component) dans angulaire 1.5.x autoriser pour l’encapsulation et la capacité de créer des éléments HTML individuels.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-258">[Components](https://docs.angularjs.org/guide/component) in Angular 1.5.x allow for the encapsulation and capability of creating individual HTML elements.</span></span> <span data-ttu-id="1d1ed-259">Vous pouvez d’obtenir la même fonctionnalité à l’aide de la méthode .directive() dans 1.4.x angulaire.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-259">In Angular 1.4.x you could achieve the same feature using the .directive() method.</span></span>

<span data-ttu-id="1d1ed-260">À l’aide de la méthode .component(), développement est simplifié gagner de la fonctionnalité de la directive et le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-260">By using the .component() method, development is simplified gaining the functionality of the directive and the controller.</span></span> <span data-ttu-id="1d1ed-261">Autres avantages ; isolement de la portée, les meilleures pratiques sont inhérentes et migration 2 angulaire devient une tâche plus facile.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-261">Other benefits include; scope isolation, best practices are inherent, and migration to Angular 2 becomes an easier task.</span></span> <span data-ttu-id="1d1ed-262">Le `<module name>.component()` appel de fonction est utilisé pour créer et inscrire des composants dans AngularJS.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-262">The `<module name>.component()` function call is used to create and register components in AngularJS.</span></span>

<span data-ttu-id="1d1ed-263">Voici un extrait de code qui inscrit un nouveau composant.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-263">Below is a snippet of code that registers a new component.</span></span> <span data-ttu-id="1d1ed-264">Le `personApp` variable dans l’extrait de code fait référence à un module angulaire, qui est défini sur la ligne 2.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-264">The `personApp` variable in the snippet references an Angular module, which is defined on line 2.</span></span>

<span data-ttu-id="1d1ed-265">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/components.js?highlight=2,5,13)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-265">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/components.js?highlight=2,5,13)]</span></span>

<span data-ttu-id="1d1ed-266">La vue dans laquelle nous affichons l’élément HTML personnalisé.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-266">The view where we are displaying the custom HTML element.</span></span>

<span data-ttu-id="1d1ed-267">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Components.cshtml?highlight=8)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-267">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Components.cshtml?highlight=8)]</span></span>

<span data-ttu-id="1d1ed-268">Le modèle associé par composant :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-268">The associated template used by component:</span></span>

<span data-ttu-id="1d1ed-269">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personcomponent.html?highlight=2,3)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-269">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personcomponent.html?highlight=2,3)]</span></span>

<span data-ttu-id="1d1ed-270">La page affiche « Aftab » et « Ansari » qui correspondent à la `firstName` et `lastName` propriétés attaché à la `vm` objet :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-270">The page shows "Aftab" and "Ansari" that correspond to the `firstName` and `lastName` properties attached to the `vm` object:</span></span>

![Exemple de composants](angular/_static/components.png)

### <a name="services"></a><span data-ttu-id="1d1ed-272">Services</span><span class="sxs-lookup"><span data-stu-id="1d1ed-272">Services</span></span>

<span data-ttu-id="1d1ed-273">[Services](https://docs.angularjs.org/guide/services) dans AngularJS sont couramment utilisées pour le code partagé qui est abstrait dans un fichier qui peut être utilisé dans l’ensemble de la durée de vie d’une application angulaire.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-273">[Services](https://docs.angularjs.org/guide/services) in AngularJS are commonly used for shared code that is abstracted away into a file which can be used throughout the lifetime of an Angular application.</span></span> <span data-ttu-id="1d1ed-274">Les services sont instanciés tardivement, ce qui signifie qu’il pas sera une instance d’un service, sauf si un composant dont dépend le service obtient utilisé.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-274">Services are lazily instantiated, meaning that there will not be an instance of a service unless a component that depends on the service gets used.</span></span> <span data-ttu-id="1d1ed-275">Fabriques sont un exemple de service utilisé dans les applications d’AngularJS.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-275">Factories are an example of a service used in AngularJS applications.</span></span> <span data-ttu-id="1d1ed-276">Fabriques sont créés à l’aide de la `myApp.factory()` , l’appel de fonction où `myApp` est le module.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-276">Factories are created using the `myApp.factory()` function call, where `myApp` is the module.</span></span>

<span data-ttu-id="1d1ed-277">Voici un exemple qui montre comment utiliser des fabriques dans AngularJS :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-277">Below is an example that shows how to use factories in AngularJS:</span></span>

<span data-ttu-id="1d1ed-278">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/simpleFactory.js?highlight=1)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-278">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/simpleFactory.js?highlight=1)]</span></span>

<span data-ttu-id="1d1ed-279">Pour appeler cette fabrique à partir du contrôleur, passez `personFactory` en tant que paramètre à la `controller` (fonction) :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-279">To call this factory from the controller, pass `personFactory` as a parameter to the `controller` function:</span></span>

```javascript
personApp.controller('personController', function($scope,personFactory) {
  $scope.name = personFactory.getName();
});
```

### <a name="using-services-to-talk-to-a-rest-endpoint"></a><span data-ttu-id="1d1ed-280">À l’aide des services pour communiquer avec un point de terminaison REST</span><span class="sxs-lookup"><span data-stu-id="1d1ed-280">Using services to talk to a REST endpoint</span></span>

<span data-ttu-id="1d1ed-281">Ci-dessous est un exemple de bout en bout à l’aide de services dans AngularJS pour interagir avec un point de terminaison de l’API Web ASP.NET principale.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-281">Below is an end-to-end example using services in AngularJS to interact with an ASP.NET Core Web API endpoint.</span></span> <span data-ttu-id="1d1ed-282">L’exemple obtienne les données de l’API Web et affiche les données dans un modèle d’affichage.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-282">The example gets data from the Web API and displays the data in a view template.</span></span> <span data-ttu-id="1d1ed-283">Nous allons commencer par la vue :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-283">Let's start with the view first:</span></span>

<span data-ttu-id="1d1ed-284">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Index.cshtml?highlight=5,8,10,17,18,19)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-284">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Index.cshtml?highlight=5,8,10,17,18,19)]</span></span>

<span data-ttu-id="1d1ed-285">Dans cette vue, nous avons un module angulaire appelé `PersonsApp` et un contrôleur appelé `personController`.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-285">In this view, we have an Angular module called `PersonsApp` and a controller called `personController`.</span></span> <span data-ttu-id="1d1ed-286">Nous utilisons `ng-repeat` d’itérer sur la liste des personnes.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-286">We are using `ng-repeat` to iterate over the list of persons.</span></span> <span data-ttu-id="1d1ed-287">Nous référençons trois fichiers JavaScript personnalisés sur les lignes 17-19.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-287">We are referencing three custom JavaScript files on lines 17-19.</span></span>

<span data-ttu-id="1d1ed-288">Le *personApp.js* fichier est utilisé pour inscrire le `PersonsApp` module ; et la syntaxe est semblable aux exemples précédents.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-288">The *personApp.js* file is used to register the `PersonsApp` module; and, the syntax is similar to previous examples.</span></span> <span data-ttu-id="1d1ed-289">Nous utilisons le `angular.module` fonction permettant de créer une nouvelle instance du module qui nous travaillerons avec.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-289">We are using the `angular.module` function to create a new instance of the module that we will be working with.</span></span>

<span data-ttu-id="1d1ed-290">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personApp.js?highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-290">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personApp.js?highlight=3)]</span></span>

<span data-ttu-id="1d1ed-291">Examinons un *personFactory.js*, ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-291">Let's take a look at *personFactory.js*, below.</span></span> <span data-ttu-id="1d1ed-292">Nous appelons du module `factory` méthode pour créer une fabrique.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-292">We are calling the module’s `factory` method to create a factory.</span></span> <span data-ttu-id="1d1ed-293">La ligne 12 montre l’angulaire intégré `$http` la récupération des informations sur les personnes à partir d’un service web de service.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-293">Line 12 shows the built-in Angular `$http` service retrieving people information from a web service.</span></span>

<span data-ttu-id="1d1ed-294">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personFactory.js?highlight=6,7,12)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-294">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personFactory.js?highlight=6,7,12)]</span></span>

<span data-ttu-id="1d1ed-295">Dans *personController.js*, nous appelons du module `controller` pour créer le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-295">In *personController.js*, we are calling the module’s `controller` method to create the controller.</span></span> <span data-ttu-id="1d1ed-296">Le `$scope` l’objet `people` les données retournées à partir de la personFactory (ligne 13) est assignée à la propriété.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-296">The `$scope` object's `people` property is assigned the data returned from the personFactory (line 13).</span></span>

<span data-ttu-id="1d1ed-297">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personController.js?highlight=6,7,13)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-297">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personController.js?highlight=6,7,13)]</span></span>

<span data-ttu-id="1d1ed-298">Jetons un œil rapide à l’API Web et le modèle derrière lui.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-298">Let's take a quick look at the Web API and the model behind it.</span></span> <span data-ttu-id="1d1ed-299">Le `Person` modèle est un POCO (objet CLR simple) avec `Id`, `FirstName`, et `LastName` propriétés :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-299">The `Person` model is a POCO (Plain Old CLR Object) with `Id`, `FirstName`, and `LastName` properties:</span></span>

<span data-ttu-id="1d1ed-300">[!code-csharp[Main](angular/sample/AngularJSSample/src/AngularJSSample/Models/Person.cs)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-300">[!code-csharp[Main](angular/sample/AngularJSSample/src/AngularJSSample/Models/Person.cs)]</span></span>

<span data-ttu-id="1d1ed-301">Le `Person` contrôleur retourne une liste au format JSON de `Person` objets :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-301">The `Person` controller returns a JSON-formatted list of `Person` objects:</span></span>

<span data-ttu-id="1d1ed-302">[!code-csharp[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Controllers/Api/PersonController.cs?highlight=9,10,19)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-302">[!code-csharp[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Controllers/Api/PersonController.cs?highlight=9,10,19)]</span></span>

<span data-ttu-id="1d1ed-303">Nous allons voir l’application en action :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-303">Let's see the application in action:</span></span>

![Contrôleur affichant autres résultats](angular/_static/rest-bound.png)

<span data-ttu-id="1d1ed-305">Vous pouvez [permet d’afficher la structure de l’application sur GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample).</span><span class="sxs-lookup"><span data-stu-id="1d1ed-305">You can [view the application's structure on GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample).</span></span>

> [!NOTE]
> <span data-ttu-id="1d1ed-306">Pour plus d’informations sur l’organisation des applications d’AngularJS, consultez [Guide de Style de John Papa angulaire](https://github.com/johnpapa/angular-styleguide)</span><span class="sxs-lookup"><span data-stu-id="1d1ed-306">For more on structuring AngularJS applications, see [John Papa's Angular Style Guide](https://github.com/johnpapa/angular-styleguide)</span></span>

&nbsp;

> [!NOTE]
> <span data-ttu-id="1d1ed-307">Pour créer le module AngularJS, contrôleur, la fabrique, directive et afficher les fichiers facilement, veillez à consulter du Sayed Hashimi [pack de modèle SideWaffle pour Visual Studio](http://sidewaffle.com/).</span><span class="sxs-lookup"><span data-stu-id="1d1ed-307">To create AngularJS module, controller, factory, directive and view files easily, be sure to check out Sayed Hashimi's [SideWaffle template pack for Visual Studio](http://sidewaffle.com/).</span></span> <span data-ttu-id="1d1ed-308">Sayed Hashimi est un type de programme senior de l’équipe Visual Studio Web Microsoft et SideWaffle modèles sont considérées comme la norme or.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-308">Sayed Hashimi is a Senior Program Manager on the Visual Studio Web Team at Microsoft and SideWaffle templates are considered the gold standard.</span></span> <span data-ttu-id="1d1ed-309">Au moment de la rédaction, SideWaffle est disponible pour Visual Studio 2012, 2013 et 2015.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-309">At the time of this writing, SideWaffle is available for Visual Studio 2012, 2013, and 2015.</span></span>

### <a name="routing-and-multiple-views"></a><span data-ttu-id="1d1ed-310">Routage et vues multiples</span><span class="sxs-lookup"><span data-stu-id="1d1ed-310">Routing and multiple views</span></span>

<span data-ttu-id="1d1ed-311">AngularJS dispose d’un fournisseur de routage intégrée pour gérer la navigation de SPA (Application à Page unique) en fonction.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-311">AngularJS has a built-in route provider to handle SPA (Single Page Application) based navigation.</span></span> <span data-ttu-id="1d1ed-312">Pour utiliser le routage AngularJS, vous devez ajouter le `angular-route` bibliothèque à l’aide de Bower.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-312">To work with routing in AngularJS, you must add the `angular-route` library using Bower.</span></span> <span data-ttu-id="1d1ed-313">Vous pouvez voir dans le [bower.json](#angular-bower-json) fichier référencé au début de cet article que nous sommes déjà y faire référence dans notre projet.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-313">You can see in the [bower.json](#angular-bower-json) file referenced at the start of this article that we are already referencing it in our project.</span></span>

<span data-ttu-id="1d1ed-314">Après avoir installé le package, ajoutez la référence de script (*angular-route.js*) à votre affichage.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-314">After you install the package, add the script reference (*angular-route.js*) to your view.</span></span>

<span data-ttu-id="1d1ed-315">Maintenant examinons l’application de la personne nous ont été génération et ajouter la navigation à celui-ci.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-315">Now let's take the Person App we have been building and add navigation to it.</span></span> <span data-ttu-id="1d1ed-316">Tout d’abord, nous effectuer une copie de l’application en créant un `PeopleController` action appelée `Spa` et `Spa.cshtml` vue en copiant la vue Index.cshtml dans le `People` dossier.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-316">First, we will make a copy of the app by creating a new `PeopleController` action called `Spa` and a corresponding `Spa.cshtml` view by copying the Index.cshtml view in the `People` folder.</span></span> <span data-ttu-id="1d1ed-317">Ajouter une référence de script à `angular-route` (voir la ligne 11).</span><span class="sxs-lookup"><span data-stu-id="1d1ed-317">Add a script reference to `angular-route` (see line 11).</span></span> <span data-ttu-id="1d1ed-318">Également ajouter un `div` marquée avec la `ng-view` directive (voir la ligne 6) comme espace réservé de placer des vues dans.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-318">Also add a `div` marked with the `ng-view` directive (see line 6) as a placeholder to place views in.</span></span> <span data-ttu-id="1d1ed-319">Nous allons utiliser plusieurs supplémentaires *.js* les fichiers qui sont référencés dans les lignes 13 à 16.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-319">We are going to be using several additional *.js* files which are referenced on lines 13-16.</span></span>

<span data-ttu-id="1d1ed-320">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Spa.cshtml?highlight=6,11,12,13,14,15,16)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-320">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Spa.cshtml?highlight=6,11,12,13,14,15,16)]</span></span>

<span data-ttu-id="1d1ed-321">Examinons un *personModule.js* fichier pour voir comment nous examinons l’instanciation du module avec le routage.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-321">Let's take a look at *personModule.js* file to see how we are instantiating the module with routing.</span></span> <span data-ttu-id="1d1ed-322">Nous passons `ngRoute` en tant que bibliothèque dans le module.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-322">We are passing `ngRoute` as a library into the module.</span></span> <span data-ttu-id="1d1ed-323">Ce module gère le routage dans notre application.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-323">This module handles routing in our application.</span></span>

<span data-ttu-id="1d1ed-324">[!code-javascript[Main](angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personModule.js)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-324">[!code-javascript[Main](angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personModule.js)]</span></span>

<span data-ttu-id="1d1ed-325">Le *personRoutes.js* fichier ci-dessous, définit les itinéraires basés sur le fournisseur de l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-325">The *personRoutes.js* file, below, defines routes based on the route provider.</span></span> <span data-ttu-id="1d1ed-326">Définissent la navigation en disant efficacement lorsqu’une URL avec lignes 4-7 `/persons` est demandé, utilisez un modèle appelé `partials/personlist` en passant par `personListController`.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-326">Lines 4-7 define navigation by effectively saying, when a URL with `/persons` is requested, use a template called `partials/personlist` by working through `personListController`.</span></span> <span data-ttu-id="1d1ed-327">Les lignes 8 à 11 indiquent une page de détails avec un paramètre d’itinéraire de `personId`.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-327">Lines 8-11 indicate a detail page with a route parameter of `personId`.</span></span> <span data-ttu-id="1d1ed-328">Si l’URL ne correspond pas à un des modèles, angulaire par défaut est le `/persons` vue.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-328">If the URL doesn't match one of the patterns, Angular defaults to the `/persons` view.</span></span>

<span data-ttu-id="1d1ed-329">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personRoutes.js?highlight=4,5,6,7,8,9,10,11,13)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-329">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personRoutes.js?highlight=4,5,6,7,8,9,10,11,13)]</span></span>

<span data-ttu-id="1d1ed-330">Le `personlist.html` fichier est une vue partielle contenant uniquement le code HTML que nécessaire pour afficher la liste de personnes.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-330">The `personlist.html` file is a partial view containing only the HTML needed to display person list.</span></span>

<span data-ttu-id="1d1ed-331">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personlist.html?highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-331">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personlist.html?highlight=3)]</span></span>

<span data-ttu-id="1d1ed-332">Le contrôleur est défini à l’aide du module `controller` fonctionner dans *personListController.js*.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-332">The controller is defined by using the module's `controller` function in *personListController.js*.</span></span>

<span data-ttu-id="1d1ed-333">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personListController.js?highlight=1)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-333">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personListController.js?highlight=1)]</span></span>

<span data-ttu-id="1d1ed-334">Si nous exécuter cette application et accédez à la `people/spa#/persons` URL, nous allons voir :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-334">If we run this application and navigate to the `people/spa#/persons` URL, we will see:</span></span>

![Mode liste de personnes](angular/_static/spa-persons.png)

<span data-ttu-id="1d1ed-336">Si nous, accédez à une page de détails, par exemple `people/spa#/persons/2`, nous allons voir la vue partielle en détail :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-336">If we navigate to a detail page, for example `people/spa#/persons/2`, we will see the detail partial view:</span></span>

![Vue Détails](angular/_static/spa-persons-2.png)

<span data-ttu-id="1d1ed-338">Vous pouvez afficher la source complète et ne pas illustrés dans cet article sur tous les fichiers [GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample).</span><span class="sxs-lookup"><span data-stu-id="1d1ed-338">You can view the full source and any files not shown in this article on [GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample).</span></span>

### <a name="event-handlers"></a><span data-ttu-id="1d1ed-339">Gestionnaires d'événements</span><span class="sxs-lookup"><span data-stu-id="1d1ed-339">Event Handlers</span></span>

<span data-ttu-id="1d1ed-340">Il existe un certain nombre de directives dans AngularJS qui ajoutent des fonctionnalités de gestion des événements pour les éléments d’entrée dans votre DOM. HTML</span><span class="sxs-lookup"><span data-stu-id="1d1ed-340">There are a number of directives in AngularJS that add event-handling capabilities to the input elements in your HTML DOM.</span></span> <span data-ttu-id="1d1ed-341">Vous trouverez ci-dessous la liste des événements qui sont intégrées à AngularJS.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-341">Below is a list of the events that are built into AngularJS.</span></span>

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
> <span data-ttu-id="1d1ed-342">Vous pouvez ajouter vos propres gestionnaires d’événements à l’aide de la [directives personnalisées de fonctionnalité dans AngularJS](https://docs.angularjs.org/guide/directive).</span><span class="sxs-lookup"><span data-stu-id="1d1ed-342">You can add your own event handlers using the [custom directives feature in AngularJS](https://docs.angularjs.org/guide/directive).</span></span>

<span data-ttu-id="1d1ed-343">Voyons comment la `ng-click` événements sont associés.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-343">Let's look at how the `ng-click` event is wired up.</span></span> <span data-ttu-id="1d1ed-344">Créer un fichier JavaScript nommé *eventHandlerController.js*et lui ajouter les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="1d1ed-344">Create a new JavaScript file named *eventHandlerController.js*, and add the following to it:</span></span>

<span data-ttu-id="1d1ed-345">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/eventHandlerController.js?highlight=5,6,7)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-345">[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/eventHandlerController.js?highlight=5,6,7)]</span></span>

<span data-ttu-id="1d1ed-346">Remarquez le nouveau `sayName` fonctionner dans `eventHandlerController` à la ligne 5 ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-346">Notice the new `sayName` function in `eventHandlerController` on line 5 above.</span></span> <span data-ttu-id="1d1ed-347">Effectue un ensemble de la méthode pour maintenant affiche une alerte de JavaScript à l’utilisateur avec un message d’accueil.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-347">All the method is doing for now is showing a JavaScript alert to the user with a welcome message.</span></span>

<span data-ttu-id="1d1ed-348">La vue ci-dessous lie une fonction de contrôleur à un événement AngularJS.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-348">The view below binds a controller function to an AngularJS event.</span></span> <span data-ttu-id="1d1ed-349">La ligne 9 comporte un bouton sur lequel le `ng-click` directive angulaire a été appliquée.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-349">Line 9 has a button on which the `ng-click` Angular directive has been applied.</span></span> <span data-ttu-id="1d1ed-350">Il appelle notre `sayName` fonction, qui est attachée à la `$scope` objet passé à cette vue.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-350">It calls our `sayName` function, which is attached to the `$scope` object passed to this view.</span></span>

<span data-ttu-id="1d1ed-351">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Events.cshtml?highlight=9)]</span><span class="sxs-lookup"><span data-stu-id="1d1ed-351">[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Events.cshtml?highlight=9)]</span></span>

<span data-ttu-id="1d1ed-352">L’exemple en cours d’exécution montre que le contrôleur de `sayName` fonction est appelée automatiquement lorsque le bouton est activé.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-352">The running example demonstrates that the controller's `sayName` function is called automatically when the button is clicked.</span></span>

![Événements Click](angular/_static/events.png)

<span data-ttu-id="1d1ed-354">Pour plus d’informations sur les directives de gestionnaire d’événements AngularJS, veillez à tête pour la [site Web de documentation](https://docs.angularjs.org/api/ng/directive/ngClick) d’AngularJS.</span><span class="sxs-lookup"><span data-stu-id="1d1ed-354">For more detail on AngularJS built-in event handler directives, be sure to head to the [documentation website](https://docs.angularjs.org/api/ng/directive/ngClick) of AngularJS.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1d1ed-355">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1d1ed-355">Additional resources</span></span>

* [<span data-ttu-id="1d1ed-356">Docs angulaires</span><span class="sxs-lookup"><span data-stu-id="1d1ed-356">Angular Docs</span></span>](https://docs.angularjs.org)

* [<span data-ttu-id="1d1ed-357">Info 2 angulaire</span><span class="sxs-lookup"><span data-stu-id="1d1ed-357">Angular 2 Info</span></span>](http://angular.io)
