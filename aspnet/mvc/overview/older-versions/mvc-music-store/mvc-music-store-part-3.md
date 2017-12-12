---
uid: mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-3
title: "Partie 3 : Vues et ViewModel | Documents Microsoft"
author: jongalloway
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 3 couvre les vues et les ViewModel."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/21/2011
ms.topic: article
ms.assetid: 94297aa0-1f2d-4d72-bbcb-63f64653e0c0
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-3
msc.type: authoredcontent
ms.openlocfilehash: 5b38f88283c5d2d93f0bab283dbd9451855d95e3
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-3-views-and-viewmodels"></a>Partie 3 : Vues et ViewModel
====================
par [Jon Galloway](https://github.com/jongalloway)

> Le magasin de musique MVC est une application du didacticiel qui présente et explique étape par étape comment utiliser ASP.NET MVC et Visual Studio pour le développement web.  
>   
> Le magasin de musique MVC est une implémentation de magasin exemple léger qui vend des albums de musique en ligne et implémente l’administration de site de base, authentification de l’utilisateur et les fonctionnalités de panier d’achat.  
>   
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 3 couvre les vues et les ViewModel.


Jusqu'à présent nous avons simplement été retourner des chaînes à partir d’actions de contrôleur. Qui est un moyen agréable pour avoir une idée du fonctionnement des contrôleurs, mais il n’est pas comment vous pouvez créer une application web réelle. Nous allons un meilleur moyen de générer du code HTML à visiter notre site de navigateurs : une où nous pouvons utiliser des fichiers de modèle pour personnaliser plus facilement le contenu HTML renvoyer. C’est exactement ce que font les affichages.

## <a name="adding-a-view-template"></a>Ajout d’un modèle d’affichage

Pour utiliser un modèle d’affichage, nous allons modifier la méthode HomeController Index pour renvoyer un ActionResult et qu’il retourne View(), comme ci-dessous :

[!code-csharp[Main](mvc-music-store-part-3/samples/sample1.cs)]

La modification ci-dessus indique au lieu de retourné une chaîne, nous souhaitons à la place une « vue » permet de générer un résultat précédent.

Nous allons maintenant ajouter un modèle de vue approprié à notre projet. Pour ce faire, nous allons positionnez le curseur de texte au sein de la méthode d’action Index, puis avec le bouton droit et sélectionnez « Ajouter une vue ». Cela affiche la boîte de dialogue Ajouter une vue :

![](mvc-music-store-part-3/_static/image1.jpg) ![](mvc-music-store-part-3/_static/image1.png)

La boîte de dialogue « Ajouter une vue » nous permet de rapidement et facilement générer des fichiers de modèle de vue. Par défaut, la « vue Ajouter » boîte de dialogue remplit au préalable le nom du modèle de vue à créer afin qu’elle corresponde à la méthode d’action qui l’utilise. Étant donné que nous avons utilisé le menu « Ajouter une vue » dans la méthode d’action Index() de notre HomeController, la boîte de dialogue « Ajouter une vue » ci-dessus a « Index » en tant que le nom de la vue préremplie par défaut. Nous n’avez pas besoin de modifier les options de cette boîte de dialogue, cliquez sur le bouton Ajouter.

Lorsque vous cliquez sur le bouton Ajouter, Visual Web Developer créera un nouveau Index.cshtml modèle d’affichage pour nous dans le répertoire \Views\Home, si la création du dossier n’existe pas déjà.

![](mvc-music-store-part-3/_static/image2.png)

L’emplacement de nom et le dossier du fichier « Index.cshtml » est important et suit les conventions d’affectation de noms par défaut ASP.NET MVC. Le nom de répertoire, \Views\Home, correspond au contrôleur - ce qui est nommé HomeController. Le nom du modèle de vue, Index, correspond à la méthode d’action de contrôleur qui affichera la vue.

ASP.NET MVC vous permet de ne pas avoir à spécifier explicitement le nom ou l’emplacement d’un modèle de vue quand vous utilisez cette convention d’affectation de noms pour retourner une vue. Il sera par défaut le rendu du modèle d’affichage \Views\Home\Index.cshtml lorsque nous écrire le code ci-dessous au sein de notre HomeController :

[!code-csharp[Main](mvc-music-store-part-3/samples/sample2.cs)]

Visual Web Developer créé et ouvert le modèle d’affichage « Index.cshtml » une fois que nous cliquons sur le bouton « Ajouter » dans la boîte de dialogue « Ajouter une vue ». Le contenu de Index.cshtml est indiqué ci-dessous.

[!code-cshtml[Main](mvc-music-store-part-3/samples/sample3.cshtml)]

Cette vue est à l’aide de la syntaxe Razor, qui est plus concise que le moteur d’affichage Web Forms utilisé dans ASP.NET Web Forms et les versions précédentes d’ASP.NET MVC. Le moteur d’affichage Web Forms est toujours disponible dans ASP.NET MVC 3, mais de nombreux développeurs si le moteur d’affichage Razor est très bien adaptée développement d’ASP.NET MVC.

Les trois premières lignes définissent le titre de page à l’aide de ViewBag.Title. Nous allons examiner comment cela fonctionne plus en détail plus rapidement, mais tout d’abord nous allons mettre à jour le texte d’en-tête de texte et afficher la page. Mise à jour la &lt;h2&gt; balise à dire « ce est la Page d’accueil » comme indiqué ci-dessous.

[!code-cshtml[Main](mvc-music-store-part-3/samples/sample4.cshtml)]

L’application indique que notre nouveau texte visible sur la page d’accueil en cours d’exécution.

![](mvc-music-store-part-3/_static/image3.png)

## <a name="using-a-layout-for-common-site-elements"></a>À l’aide d’une disposition pour les éléments communs de site

La plupart des sites Web disposent de contenu qui est partagé entre plusieurs pages : navigation, les pieds de page, les images de logo, les références de feuille de style, etc. Le moteur d’affichage Razor facilite cette procédure gérer à l’aide d’une page appelée \_Layout.cshtml qui a été créé automatiquement pour nous dans le dossier Shared/vues.

![](mvc-music-store-part-3/_static/image4.png)

Double-cliquez sur ce dossier pour afficher le contenu, qui sont indiquées ci-dessous.

[!code-cshtml[Main](mvc-music-store-part-3/samples/sample5.cshtml)]

Le contenu à partir de notre des vues sera affiché par le @RenderBody() commande et tout contenu courants que nous souhaitons figurer en dehors de qui peuvent être ajouté à la \_Layout.cshtml balisage. Nous devrons notre magasin de musique MVC d’un en-tête commun avec des liens à notre zone de page d’accueil et magasin sur toutes les pages du site, nous allons ajouter que pour le modèle directement au-dessus, de façon @RenderBody() instruction.

[!code-cshtml[Main](mvc-music-store-part-3/samples/sample6.cshtml)]

## <a name="updating-the-stylesheet"></a>Mise à jour de la feuille de style

Le modèle de projet vide inclut un fichier CSS très simplifié qui inclut uniquement les styles utilisés pour afficher les messages de validation. Notre concepteur a fourni du CSS et des images supplémentaires pour définir l’apparence de notre site, donc nous allons ajouter ceux désormais.

Le fichier CSS et les Images mises à jour sont inclus dans le répertoire de contenu de Assets.zip MvcMusicStore qui est disponible à l’adresse [magasin de musique MVC](https://github.com/evilDave/MVC-Music-Store). Nous allons sélectionnez-les dans l’Explorateur Windows et les déposer dans le dossier de contenu de notre Solution dans Visual Web Developer, comme indiqué ci-dessous :

![](mvc-music-store-part-3/_static/image5.png)

Vous êtes invité à confirmer si vous souhaitez remplacer le fichier Site.css existant. Cliquez sur Oui.

![](mvc-music-store-part-3/_static/image6.png)

Le dossier de contenu de votre application s’affiche maintenant comme suit :

![](mvc-music-store-part-3/_static/image7.png)

Maintenant nous allons exécuter l’application et visualiser l’aspect de nos modifications sur la page d’accueil.

![](mvc-music-store-part-3/_static/image8.png)

- Examinons ce qui a changé : méthode d’action le HomeController Index trouvé et affiche le modèle \Views\Home\Index.cshtmlView, même si notre code appelé « retour View() », étant donné que notre modèle d’affichage de suivre la convention d’affectation de noms standard.
- La Page d’accueil affiche un message d’accueil simple qui est défini dans le modèle d’affichage \Views\Home\Index.cshtml.
- À l’aide de la Page d’accueil de notre \_Layout.cshtml modèle, et par conséquent, le message d’accueil est contenu dans la disposition de HTML du site standard.

## <a name="using-a-model-to-pass-information-to-our-view"></a>À l’aide d’un modèle pour passer des informations à notre vue

Un modèle d’affichage qui affiche simplement codées en dur HTML ne va pas faire un site web très intéressant. Pour créer un site web dynamique, nous allons plutôt que passer des informations à partir de nos actions de contrôleur à nos modèles de vue.

Dans le modèle Model-View-Controller, le terme À que modèle fait référence des objets qui représentent les données de l’application. Souvent, les objets de modèle correspondent aux tables dans votre base de données, mais ils n’ont pas à.

Méthodes d’action de contrôleur qui retournent un ActionResult peuvent passer un objet de modèle à la vue. Cela permet à un contrôleur pour le package correctement toutes les informations nécessaires pour générer une réponse, puis à transmettre ces informations à un modèle d’affichage à utiliser pour générer la réponse HTML appropriée. C’est plus facile à comprendre par voir en action, par conséquent, nous pouvons commencer.

Tout d’abord, nous allons créer certaines classes de modèle pour représenter les Genres et les Albums dans notre magasin. Commençons par créer une classe de Genre. Cliquez sur le dossier « Modèles » dans votre projet, choisissez l’option « Ajouter une classe », nommez le fichier « Genre.cs ».

![](mvc-music-store-part-3/_static/image2.jpg)

![](mvc-music-store-part-3/_static/image9.png)

Puis ajouter une propriété de nom de la chaîne de publique à la classe qui a été créée :

[!code-csharp[Main](mvc-music-store-part-3/samples/sample7.cs)]

*Remarque : dans le cas, vous vous demandez, le {get ; définir ;} notation émane propriétés implémentées automatiquement la fonctionnalité de # de. Cela nous donne les avantages d’une propriété sans nécessiter de déclarer un champ de stockage.*

Ensuite, suivez les mêmes étapes pour créer une classe Album (nommée Album.cs) qui a un titre et une propriété de Genre :

[!code-csharp[Main](mvc-music-store-part-3/samples/sample8.cs)]

Maintenant, nous pouvons modifier le StoreController pour utiliser des vues qui affichent des informations dynamiques à partir de notre modèle. Si le paramètre - à des fins de démonstration maintenant - nous avons nommé notre Albums basés sur l’ID de demande, nous pouvons afficher ces informations comme dans la vue ci-dessous.

![](mvc-music-store-part-3/_static/image10.png)

Nous allons commencer en modifiant l’action détails de magasin pour qu’il affiche les informations pour un seul album. Ajoutez une instruction « using » en haut de la **StoreControllers** classe pour inclure l’espace de noms MvcMusicStore.Models, nous n’avez pas besoin de taper MvcMusicStore.Models.Album chaque fois que vous souhaitez utiliser la classe album. La section les instructions « using » de cette classe doit maintenant apparaître comme suit.

[!code-csharp[Main](mvc-music-store-part-3/samples/sample9.cs)]

Ensuite, nous mettrons à jour l’action de contrôleur détails afin qu’elle retourne un ActionResult plutôt qu’une chaîne, comme nous l’avons fait avec la méthode d’Index du HomeController.

[!code-csharp[Main](mvc-music-store-part-3/samples/sample10.cs)]

Maintenant, nous pouvons modifier la logique permettant de retourner un objet de l’Album à la vue. Plus loin dans ce didacticiel nous récupérons les données à partir d’une base de données, mais pour l’instant, nous allons utiliser « factice des données » pour commencer.

[!code-csharp[Main](mvc-music-store-part-3/samples/sample11.cs)]

*Remarque : Si vous n’êtes pas familiarisé avec c#, vous pouvez supposer que qu’à l’aide de var signifie que notre variable album est à liaison tardive. Qui n’est pas correct, le compilateur c# à l’aide de-l’inférence de type en fonction de ce que nous avons affectation à la variable afin de déterminer que cet album est de type Album et la compilation de la variable locale album comme type d’Album, donc nous obtenons la vérification de la compilation et l’éditeur de code Visual Studio prise en charge.*

Nous allons maintenant créer un modèle d’affichage qui utilise notre Album pour générer une réponse HTML. Avant cela, nous devons générer le projet afin que la boîte de dialogue Ajouter une vue a connaissance de notre classe Album nouvellement créé. Vous pouvez générer le projet en sélectionnant le Debug⇨Build MvcMusicStore élément de menu (de crédit supplémentaire, vous pouvez utiliser le raccourci Ctrl-Maj-B de numéros pour générer le projet).

![](mvc-music-store-part-3/_static/image11.png)

Maintenant que nous avons configuré ses classes de prise en charge, nous sommes prêts à notre modèle de vue de génération. Avec le bouton droit dans la méthode de détails et sélectionnez « Ajouter un affichage... » dans le menu contextuel.

![](mvc-music-store-part-3/_static/image12.png)

Nous allons créer un nouveau modèle de vue comme nous l’avons fait avant le fichier HomeController. Étant donné que nous allons la créer à partir de la StoreController générée ultérieurement utilisera par défaut dans un fichier \Views\Store\Index.cshtml.

Contrairement aux avant, nous allons pour vérifier la case à cocher de la vue « Créer un fortement typée ». Ensuite, nous allons sélectionner notre classe « Album » dans la liste « Affichage des données-classe »-downlist. Cela entraîne la boîte de dialogue « Ajouter une vue » créer un modèle de vue qui attend un Album objet sera passé pour utiliser.

![](mvc-music-store-part-3/_static/image13.png)

Lorsque vous cliquez sur le bouton « Ajouter » notre modèle de vue \Views\Store\Details.cshtml sera créé, contenant le code suivant.

[!code-cshtml[Main](mvc-music-store-part-3/samples/sample12.cshtml)]

Notez que la première ligne, ce qui indique que cette vue est fortement typée à la classe Album. Le moteur d’affichage Razor comprend qu’il a été passé un objet Album, afin que nous pouvons accéder facilement aux propriétés de modèle et même ont l’avantage d’IntelliSense dans l’éditeur de Visual Web Developer.

Mise à jour la &lt;h2&gt; balise afin qu’elle affiche la propriété de titre de l’Album en modifiant cette ligne et apparaît comme suit.

[!code-cshtml[Main](mvc-music-store-part-3/samples/sample13.cshtml)]

Notez qu’IntelliSense est déclenché lorsque vous entrez la période après la @Model mot clé, en affichant les propriétés et méthodes qui prend en charge de la classe Album.

Nous allons maintenant exécuter à nouveau votre projet et accédez à l’URL du magasin/détails/5. Nous allons voir les détails d’un Album comme ci-dessous.

![](mvc-music-store-part-3/_static/image14.png)

Maintenant, nous allons effectuer une mise à jour similaire pour la méthode d’action Parcourir du magasin. Mettre à jour de la méthode de sorte qu’elle retourne un ActionResult et modifiez la logique de la méthode pour qu’il crée un nouvel objet de Genre et retourne à la vue.

[!code-csharp[Main](mvc-music-store-part-3/samples/sample14.cs)]

Avec le bouton droit dans la méthode de parcourir et sélectionnez « Ajouter un affichage... » dans le menu contextuel, puis ajouter une vue qui est fortement typée ajoutez fortement typé à la classe de Genre.

![](mvc-music-store-part-3/_static/image15.png)

Mise à jour la &lt;h2&gt; élément dans la vue de code (dans /Views/Store/Browse.cshtml) pour afficher les informations de Genre.

[!code-cshtml[Main](mvc-music-store-part-3/samples/sample15.cshtml)]

Maintenant nous allons exécuter à nouveau votre projet et accédez à la banque/Parcourir ? Genre = URL Disco. Nous allons voir la page Parcourir affichée comme ci-dessous.

![](mvc-music-store-part-3/_static/image16.png)

Enfin, nous allons en créer une mise à jour légèrement plus complexe le **stocker l’Index** méthode d’action et pour afficher une liste de tous les Genres dans notre magasin. Nous allons effectuer cela à l’aide d’une liste de Genres notre objet modèle, plutôt que simplement un Genre unique.

[!code-csharp[Main](mvc-music-store-part-3/samples/sample16.cs)]

Avec le bouton droit dans la méthode d’action Index du magasin et sélectionnez Ajouter une vue comme avant, sélectionnez Genre comme classe de modèle et appuyez sur le bouton Ajouter.

![](mvc-music-store-part-3/_static/image17.png)

Tout d’abord, nous allons modifier le @model déclaration pour indiquer que la vue attend Genre de plusieurs objets au lieu d’un seul. Modification de la première ligne de /Store/Index.cshtml comme suit :

[!code-cshtml[Main](mvc-music-store-part-3/samples/sample17.cshtml)]

Cela indique le moteur d’affichage Razor qui vous travaillerez avec un objet de modèle qui peut contenir plusieurs objets de Genre. Nous utilisons un IEnumerable&lt;Genre&gt; au lieu d’une liste&lt;Genre&gt; , car il est plus générique, ce qui nous permet de modifier ultérieurement le type de notre modèle à n’importe quel type d’objet qui prend en charge l’interface IEnumerable.

Ensuite, nous allons parcourir les objets de Genre dans le modèle, comme indiqué dans le code terminées ci-dessous.

[!code-cshtml[Main](mvc-music-store-part-3/samples/sample18.cshtml)]

Notez que nous avons complète prise en charge d’IntelliSense à mesure que nous Entrez ce code, afin que lorsque vous tapez «@Model. » nous voir toutes les méthodes et propriétés prises en charge par un IEnumerable de type Genre.

![](mvc-music-store-part-3/_static/image18.png)

Dans notre boucle « foreach », Visual Web Developer sait que chaque élément est de type Genre, ce qui nous IntelliSence pour chaque type Genre.

![](mvc-music-store-part-3/_static/image19.png)

Ensuite, la fonctionnalité de génération de modèles automatique examiné l’objet Genre et déterminé que chacune aura une propriété Name, pour qu’il parcourt les écrit. Il génère également des liens à la modifier, les détails et les supprimer pour chaque élément individuel. Nous allons tirer parti plus loin dans le Gestionnaire de magasins, mais pour l’instant nous aimerions avoir une liste simple à la place.

Exécutez l’application et accédez à /Store, nous constatons que le nombre et la liste des Genres s’affiche.

![](mvc-music-store-part-3/_static/image20.png)

## <a name="adding-links-between-pages"></a>Ajout de liens entre les pages

Nos URL /Store qui répertorie les Genres actuellement répertorie les noms de Genre simplement en tant que texte brut. Nous allons modifier cela afin qu’au lieu de texte brut, nous avons à la place la liaison de noms Genre à l’URL de magasin/Parcourir appropriées, afin que le clic sur un genre de musique, comme « Disco » pour accéder à la banque/Parcourir ? genre = Disco URL. Nous pouvons mettre à jour notre modèle de vue \Views\Store\Index.cshtml à la sortie de ces liens à l’aide de code semblable au suivant **(ne tapez pas cela dans - nous allons améliorer son)**:

[!code-html[Main](mvc-music-store-part-3/samples/sample19.html)]

Qui fonctionne, mais il risque de problèmes plus tard, car il s’appuie sur une chaîne codée en dur. Par exemple, si nous voulions renommer le contrôleur, nous devons parcourir notre code à la recherche pour les liens qui doivent être mis à jour.

Il est une autre approche que nous pouvons utiliser pour tirer parti d’une méthode d’application d’assistance HTML. ASP.NET MVC inclut des méthodes de programme d’assistance HTML qui sont disponibles à partir de notre code de modèle de vue pour effectuer diverses tâches courantes, comme cela. La méthode d’assistance Html.ActionLink() est particulièrement utile et facilite la génération de code HTML &lt;un&gt; détails ennuyeux comme s’assurer que les chemins d’accès d’URL sont correctement encodé URL prend en charge et les liens.

Html.ActionLink() a plusieurs surcharges différentes pour autoriser la spécification de toutes les informations que vous avez besoin pour vos liens. Dans le cas le plus simple, vous devez fournir le texte du lien et la méthode d’Action à atteindre lorsque l’utilisateur clique sur le lien hypertexte sur le client. Par exemple, nous pouvons lier à « Magasin / « méthode Index() sur la page des détails de la banque d’informations avec le texte du lien « Accédez à la banque Index » à l’aide de l’appel suivant :

[!code-cshtml[Main](mvc-music-store-part-3/samples/sample20.cshtml)]

*Remarque : dans ce cas, nous n’avez besoin spécifier le nom du contrôleur, car nous avons simplement lier à une autre action dans le même contrôleur qui est rendu l’affichage actuel.*

Nos liens à la page Parcourir devez transmettre un paramètre, cependant, nous allons utiliser une autre surcharge de la méthode Html.ActionLink qui accepte trois paramètres :

- 1. Texte du lien, qui affiche le nom du Genre
- 2. Nom d’action de contrôleur (Parcourir)
- 3. Valeurs de paramètre d’itinéraire, en spécifiant le nom (Genre) et la valeur (nom du Genre)

Placement que tous les éléments, voici comment nous allons écrire ces liens à la vue de l’Index de la banque :

[!code-cshtml[Main](mvc-music-store-part-3/samples/sample21.cshtml)]

Nous allons maintenant voir une liste de genres exécuter à nouveau votre projet et d’accéder à l’URL /Store/. Chaque genre est un lien hypertexte – clic prendra nous notre magasin/Parcourir ? genre =*[genre]* URL.

![](mvc-music-store-part-3/_static/image3.jpg)

Le code HTML de la liste de genre ressemble à ceci :

[!code-html[Main](mvc-music-store-part-3/samples/sample22.html)]


>[!div class="step-by-step"]
[Précédent](mvc-music-store-part-2.md)
[Suivant](mvc-music-store-part-4.md)
