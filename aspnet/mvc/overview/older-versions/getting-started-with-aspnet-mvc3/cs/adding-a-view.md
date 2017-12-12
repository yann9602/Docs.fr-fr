---
uid: mvc/overview/older-versions/getting-started-with-aspnet-mvc3/cs/adding-a-view
title: "Ajout d’une vue (c#) | Documents Microsoft"
author: Rick-Anderson
description: "Ce didacticiel, vous allez apprendre les principes fondamentaux de la création d’une application Web ASP.NET MVC à l’aide de Microsoft Visual Web Developer 2010 Express Service Pack 1, qui est en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/12/2011
ms.topic: article
ms.assetid: abc7c78d-cb09-4a4c-a887-61bc401d40e3
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-aspnet-mvc3/cs/adding-a-view
msc.type: authoredcontent
ms.openlocfilehash: 46d5494e668dfe156aeb6647ded83e6ce5366714
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="adding-a-view-c"></a>Ajout d’une vue (c#)
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> > [!NOTE]
> > Une version mise à jour de ce didacticiel est disponible [ici](../../../getting-started/introduction/getting-started.md) qui utilise ASP.NET MVC 5 et Visual Studio 2013. Il est plus sécurisé, beaucoup plus simple à suivre et illustre plusieurs fonctionnalités.
> 
> 
> Ce didacticiel, vous allez apprendre les principes fondamentaux de la création d’une application Web ASP.NET MVC à l’aide de Microsoft Visual Web Developer 2010 Express Service Pack 1, qui est une version gratuite de Microsoft Visual Studio. Avant de commencer, assurez-vous que vous avez installé les composants requis répertoriés ci-dessous. Vous pouvez installer tous les en cliquant sur le lien suivant : [Web Platform Installer](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack). Vous pouvez également installer individuellement les conditions préalables à l’aide des liens suivants :
> 
> - [Conditions préalables requises de Visual Studio Web Developer Express SP1](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack)
> - [Mettre à jour des outils ASP.NET MVC 3](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=MVC3)
> - [SQL Server Compact 4.0](https://www.microsoft.com/web/gallery/install.aspx?appid=SQLCE;SQLCEVSTools_4_0)(runtime + outils prennent en charge)
> 
> Si vous utilisez Visual Studio 2010 au lieu de Visual Web Developer 2010, installez les composants requis en cliquant sur le lien suivant : [composants requis de Visual Studio 2010](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=VS2010SP1Pack).
> 
> Un projet de Visual Web Developer avec code source c# est disponible pour accompagner cette rubrique. [Télécharger la version c#](https://code.msdn.microsoft.com/Introduction-to-MVC-3-10d1b098). Si vous préférez Visual Basic, basculez vers le [version Visual Basic](../vb/intro-to-aspnet-mvc-3.md) de ce didacticiel.


Dans cette section que vous allez modifier la `HelloWorldController` classe à utiliser l’affichage des fichiers de modèle à proprement encapsulent le processus de génération des réponses HTML à un client.

Vous allez créer un fichier de modèle de vue à l’aide de la nouvelle [moteur d’affichage Razor](https://weblogs.asp.net/scottgu/archive/2010/07/02/introducing-razor.aspx) introduite avec ASP.NET MVC 3. Modèles de vue Razor ont un *.cshtml* extension de fichier et fournissent un moyen élégant de créer du code HTML de la sortie à l’aide de c#. Razor réduit le nombre de caractères et séquences de touches requis lors de l’écriture d’un modèle d’affichage et permet un fluide rapide, flux de travail de codage.

Démarrer à l’aide d’un modèle d’affichage avec le `Index` méthode dans la `HelloWorldController` classe. Actuellement, la méthode `Index` retourne une chaîne avec un message qui est codé en dur dans la classe du contrôleur. Modifier la `Index` méthode pour retourner un `View` de l’objet, comme indiqué dans l’exemple suivant :

[!code-csharp[Main](adding-a-view/samples/sample1.cs)]

Ce code utilise un modèle d’affichage pour générer une réponse HTML au navigateur. Dans le projet, ajoutez un modèle d’affichage que vous pouvez utiliser avec le `Index` (méthode). Pour ce faire, cliquez à l’intérieur de la `Index` (méthode) et cliquez sur **ajouter une vue**.

![](adding-a-view/_static/image1.png)

Le **ajouter une vue** boîte de dialogue s’affiche. Laissez les valeurs par défaut de la façon dont ils sont et cliquez sur le **ajouter** bouton :

![](adding-a-view/_static/image2.png)

Le *MvcMovie\Views\HelloWorld* dossier et le *MvcMovie\Views\HelloWorld\Index.cshtml* fichier sont créés. Vous pouvez les consulter dans **l’Explorateur de solutions**:

![](adding-a-view/_static/image3.png)

Le suivant le *Index.cshtml* fichier qui a été créé :

[![HelloWorldIndex](adding-a-view/_static/image5.png)](adding-a-view/_static/image4.png)

Ajouter du code HTML sous la `<h2>` balise. Modifié *MvcMovie\Views\HelloWorld\Index.cshtml* fichier est présenté ci-dessous.

[!code-cshtml[Main](adding-a-view/samples/sample2.cshtml)]

Exécutez l’application et accédez à la `HelloWorld` contrôleur (`http://localhost:xxxx/HelloWorld`). Le `Index` n’avez pas procédé dans votre contrôleur de quantité de travail ; il a été exécuté l’instruction simplement `return View()`, lequel spécifié que la méthode doit utiliser un fichier de modèle de vue pour restituer une réponse au navigateur. Étant donné que vous n’avez pas spécifier explicitement le nom du fichier de modèle de vue à utiliser, ASP.NET MVC par défaut à l’aide de la *Index.cshtml* afficher le fichier dans le *\Views\HelloWorld* dossier. L’image ci-dessous montre la chaîne codée en dur dans la vue.

![](adding-a-view/_static/image6.png)

Il semble bon. Toutefois, notez que la barre de titre indique « Index » et le gros titre sur la page indique « Mon Application MVC ». Nous allons modifier ceux.

## <a name="changing-views-and-layout-pages"></a>Modification des vues et des Pages de disposition

Tout d’abord, vous souhaitez modifier le titre « My Application MVC » en haut de la page. Ce texte est courant de chaque page. Il est réellement implémentée dans un seul endroit dans le projet, même si elle apparaît sur chaque page de l’application. Accédez à la */vues/Shared* dossier **l’Explorateur de solutions** et ouvrez le  *\_Layout.cshtml* fichier. Ce fichier s’appelle un *page de disposition* et il s’agit de l’interface « partagé » qui utilisent de toutes les autres pages.

[![_LayoutCshtml](adding-a-view/_static/image8.png)](adding-a-view/_static/image7.png)

Modèles de disposition permettent de spécifier la disposition du conteneur HTML de votre site dans un seul emplacement, puis l’appliquer sur plusieurs pages de votre site. Remarque la `@RenderBody()` ligne au bas du fichier. `RenderBody`est un espace réservé dans lequel toutes les pages spécifiques à la vue que vous créez apparaissent, « encapsulées » dans la page de disposition. Modifier l’en-tête du titre dans le modèle de disposition à partir de « My MVC Application » à « Application de film MVC ».

[!code-cshtml[Main](adding-a-view/samples/sample3.cshtml)]

Exécutez l’application et notez qu’il indique à présent « Film d’application MVC ». Cliquez sur le **sur** lien et que vous consultez Comment cette page indique « Film d’application MVC », trop. Nous n’avons pu apporter la modification une fois dans le modèle de disposition et ont toutes les pages sur le site de refléter le nouveau titre.

![](adding-a-view/_static/image9.png)

Le texte complet  *\_Layout.cshtml* fichier est présenté ci-dessous :

[!code-cshtml[Main](adding-a-view/samples/sample4.cshtml)]

Maintenant, nous allons modifier le titre de la page d’Index (vue).

Ouvrez *MvcMovie\Views\HelloWorld\Index.cshtml*. Il existe deux emplacements pour apporter une modification : tout d’abord, le texte qui apparaît dans le titre du navigateur, puis dans l’en-tête secondaire (le `<h2>` élément). Vous allez les modifier légèrement pour voir quel morceau du code modifie quelle partie de l’application.

[!code-cshtml[Main](adding-a-view/samples/sample5.cshtml)]

Pour indiquer le titre HTML à afficher, le code ci-dessus définit un `Title` propriété de la `ViewBag` objet (qui se trouve dans le *Index.cshtml* modèle d’affichage). Si vous revenez dans le code source du modèle de mise en page, vous remarquerez que le modèle utilise cette valeur dans la `<title>` élément dans le cadre de la `<head>` section du code HTML. À l’aide de cette approche, vous pouvez facilement passer les autres paramètres entre votre modèle d’affichage et de votre fichier de disposition.

Exécutez l’application et accédez à `http://localhost:xx/HelloWorld`. Notez que le titre du navigateur, l’en-tête principal et les en-têtes secondaires ont changé. (Si vous ne voyez pas les changements dans le navigateur, vous voyez peut-être le contenu mis en cache. Appuyez sur Ctrl+F5 dans votre navigateur pour forcer le chargement de la réponse du serveur.)

Notez également comment le contenu dans le *Index.cshtml* afficher le modèle a été fusionné avec la  *\_Layout.cshtml* modèle d’affichage et une seule réponse HTML a été envoyée au navigateur. Les modèles de disposition permettent d’apporter facilement des modifications qui s’appliquent à toutes les pages de votre application.

![](adding-a-view/_static/image10.png)

Nos quelques « données » (dans le cas présent, le message « Hello from our View Template! » ) sont toutefois codées en dur. L’application MVC a une vue (« V») et vous avez un contrôleur (« C »), mais pas encore de modèle (« M »). Peu de temps, nous allons la créer une base de données et extraire les données de modèle.

## <a name="passing-data-from-the-controller-to-the-view"></a>Passage de données du contrôleur vers la vue

Avant de nous accédez à une base de données et parler de modèles, cependant, tout d’abord parlons passer des informations à partir du contrôleur à une vue. Classes de contrôleur sont appelées en réponse à une demande d’URL entrante. Une classe de contrôleur est où vous écrivez le code qui gère les paramètres entrants, récupère les données à partir d’une base de données et de décider quel type de réponse à envoyer au navigateur. Afficher les modèles peuvent ensuite servir à partir d’un contrôleur pour générer et mettre en forme une réponse HTML au navigateur.

Contrôleurs sont chargés de fournir les données ou les objets sont requis pour un modèle d’affichage restituer une réponse au navigateur. Un modèle d’affichage ne doit jamais exécuter la logique métier ou interagir directement avec une base de données. Au lieu de cela, il doit fonctionner uniquement avec les données qui sont fournies à ce dernier par le contrôleur. En conservant ce « séparation des préoccupations » permet de conserver votre code propre et plus facile à gérer.

Actuellement, le `Welcome` méthode d’action dans le `HelloWorldController` classe prend un `name` et un `numTimes` paramètre, puis les sorties, les valeurs directement dans le navigateur. Plutôt que le contrôleur de rendre cette réponse sous forme de chaîne, nous allons modifier le contrôleur pour utiliser un modèle d’affichage à la place. Le modèle de vue génère une réponse dynamique, ce qui signifie que vous devez passer les bits de données appropriés du contrôleur à la vue pour générer la réponse. Vous pouvez faire cela ayant le contrôleur de placer les données dynamiques qui le modèle de vue doit être dans un `ViewBag` objet du modèle d’affichage peut alors accéder.

Retour à la *HelloWorldController.cs* de fichiers et de modifier le `Welcome` méthode pour ajouter un `Message` et `NumTimes` valeur le `ViewBag` objet. `ViewBag`est un objet dynamique, ce qui signifie que vous pouvez placer tout ce que vous voulez le `ViewBag` objet ne possède aucune propriété définie jusqu'à ce que vous placez un élément qu’il contient. Le fichier *HelloWorldController.cs* complet ressemble à ceci :

[!code-csharp[Main](adding-a-view/samples/sample6.cs)]

Maintenant le `ViewBag` objet contient des données qui seront passées automatiquement à la vue.

Vous devez ensuite un modèle d’affichage de bienvenue ! Dans le **déboguer** menu, sélectionnez **MvcMovie de Build** pour vous assurer que le projet est compilé.

[![BuildHelloWorld](adding-a-view/_static/image12.png)](adding-a-view/_static/image11.png)

Puis avec le bouton droit à l’intérieur de la `Welcome` (méthode) et cliquez sur **ajouter une vue**. Voici le **ajouter une vue** boîte de dialogue ressemble à :

![](adding-a-view/_static/image13.png)

Cliquez sur **ajouter**, puis ajoutez le code suivant sous le `<h2>` élément dans la nouvelle *Welcome.cshtml* fichier. Vous allez créer une boucle qui indique « Hello » à chaque fois que l’utilisateur indique qu’il doit. Le texte complet *Welcome.cshtml* fichier est présenté ci-dessous.

[!code-cshtml[Main](adding-a-view/samples/sample7.cshtml)]

Exécutez l’application et accédez à l’URL suivante :

`http://localhost:xx/HelloWorld/Welcome?name=Scott&numtimes=4`

Maintenant les données sont extraites de l’URL et passées automatiquement au contrôleur. Le contrôleur regroupe les données dans un `ViewBag` objet et passe à la vue. La vue affiche ensuite les données au format HTML à l’utilisateur.

![](adding-a-view/_static/image14.png)

Il s’agissait d’une sorte de « M » pour le modèle, mais pas d’une base de données en soi. Créons une base de données de films en utilisant ce que nous avons appris.

>[!div class="step-by-step"]
[Précédent](adding-a-controller.md)
[Suivant](adding-a-model.md)
