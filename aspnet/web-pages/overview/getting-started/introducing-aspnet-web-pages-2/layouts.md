---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/layouts
title: "Présentation des Pages Web ASP.NET - création d’une disposition cohérente | Documents Microsoft"
author: tfitzmac
description: "Ce didacticiel vous montre comment utiliser des dispositions pour créer une apparence cohérente pour les pages sur un site qui utilise ASP.NET Web Pages. Il suppose que vous avez terminé le..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/28/2015
ms.topic: article
ms.assetid: c85ec591-f8d7-4882-b763-de6ab9f3df7a
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/layouts
msc.type: authoredcontent
ms.openlocfilehash: 692adc5a03892f27c91fe8868c8eab6ce08f49cd
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="introducing-aspnet-web-pages---creating-a-consistent-layout"></a>Présentation des Pages Web ASP.NET - création d’une disposition cohérente
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Ce didacticiel vous montre comment utiliser *dispositions* pour créer une apparence cohérente pour les pages sur un site qui utilise ASP.NET Web Pages. Il suppose que vous avez terminé la série via [suppression des données de base de données dans les Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=251584).
> 
> Ce que vous allez apprendre :
> 
> - Est d’une page mise en page.
> - Explique comment combiner des pages de disposition avec un contenu dynamique.
> - Explique comment passer des valeurs à une page de disposition.


## <a name="about-layouts"></a>À propos des dispositions

Les pages que vous avez créé jusqu'à présent ont tous été terminées, les pages autonome. Ils appartiennent au même site, mais ils n’ont pas les éléments en commun ou un aspect standard.

La plupart des sites ont une apparence cohérente et la présentation. Par exemple, si vous accédez à la [Web Microsoft.com à](https://www.microsoft.com/web/) de site et observer, vous voyez que les pages de tous les conformes à la disposition générale et à un thème visuel :

![Page du site Web Microsoft.com à représentant la disposition de l’en-tête de zone de navigation, zone de contenu et un pied de page](layouts/_static/image1.png)

Un *inefficace* pour créer cette disposition serait pour définir un en-tête, une barre de navigation et un pied de page séparément sur chaque page. Vous serez de dupliquer le balisage même chaque fois. Si vous souhaitez modifier un objet (par exemple, mettre à jour le pied de page), vous devez modifier chaque page séparément.

C’est là *pages de disposition* sont disponibles dans. Dans ASP.NET Web Pages, vous pouvez définir une page de disposition qui fournit un conteneur global pour les pages de votre site. Par exemple, la page de disposition permettre contenir l’en-tête, la zone de navigation et le pied de page. La page de disposition inclut un espace réservé emplacement du contenu principal.

Vous pouvez ensuite définir des pages de contenu individuelles qui contiennent le balisage et le code pour que cette page. Pages de contenu n’êtes pas obligé d’être des pages HTML complètes ; ils n’ont pas d’avoir un `<body>` élément. Ils ont également une ligne de code qui indique à ASP.NET de quelle page de disposition que vous souhaitez afficher le contenu de. Voici une image qui indique à peu près le fonctionne de cette relation :

![Diagramme conceptuel qui montre les deux pages de contenu et une page de disposition dans lequel elles s’adaptent](layouts/_static/image2.png)

Cette interaction est facile à comprendre lorsque vous la voir en action. Dans ce didacticiel, vous allez modifier vos pages de films pour utiliser une disposition.

## <a name="adding-a-layout-page"></a>Ajout d’une Page de disposition

Vous allez commencer par créer une page de disposition qui définit une disposition de page classique avec un en-tête, un pied de page et une zone pour le contenu principal. Dans le site WebPagesMovies, ajouter une page CSHTML nommée  *\_Layout.cshtml*.

Le trait de soulignement ( `_` ) caractère est significatif. Si un nom de la page commence par un trait de soulignement, ASP.NET ne sont pas envoyer directement cette page au navigateur. Cette convention permet de définir les pages qui sont requis pour votre site, mais que les utilisateurs ne doivent pas être en mesure de demander directement.

Remplacez le contenu de la page avec les éléments suivants :

[!code-html[Main](layouts/samples/sample1.html)]

Comme vous pouvez le voir, ce balisage est simplement le code HTML qui utilise `<div>` éléments pour définir les trois sections de la page plus un plus `<div>` élément pour contenir les trois sections. Le pied de page contient un peu de code Razor : `@DateTime.Now.Year`, qui restituera l’année en cours à cet emplacement dans la page.

Notez qu’il existe un lien vers une feuille de style nommée *Movies.css*. La feuille de style est où les détails de la disposition physique des éléments sont définies. Vous allez créer que dans un instant.

La fonctionnalité uniquement inhabituelle dans cette  *\_Layout.cshtml* page est la `@Render.Body()` ligne. Qui est l’espace réservé où le contenu sortent cette disposition est fusionnée avec une autre page.

## <a name="adding-a-css-file"></a>Ajout d’un fichier .css

La meilleure façon de définir la disposition réelle (autrement dit, apparence) des éléments de la page consiste à utiliser les règles de feuille de style en cascade. Afin que vous allez créer un *.css* fichier dont les règles pour votre nouvelle disposition.

Dans WebMatrix, sélectionnez la racine de votre site. Ensuite, dans le **fichiers** onglet du ruban, cliquez sur la flèche sous le **nouveau** puis cliquez sur **nouveau dossier**.

![L’option « Nouveau dossier » sous Nouveau dans le ruban.](layouts/_static/image3.png)

Nommez le nouveau dossier *Styles*.

![Le nouveau dossier d’affectation de noms 'Styles'](layouts/_static/image4.png)

À l’intérieur de la nouvelle *Styles* dossier, créez un fichier nommé *Movies.css*.

![Création d’un fichier Movies.css](layouts/_static/image5.png)

Remplacez le contenu de la nouvelle *.css* fichier avec les éléments suivants :

[!code-css[Main](layouts/samples/sample2.css)]

Nous ne nous dit pas grand-chose sur ces règles CSS, sauf pour noter deux choses. Un est qu’en plus de définir les tailles et polices, les règles utilisent le positionnement absolu pour établir l’emplacement de l’en-tête, le pied de page et la zone de contenu principal. Si vous débutez de positionnement dans CSS, vous pouvez lire la [positionnement CSS](http://www.w3schools.com/css/css_positioning.asp) didacticiel sur le site W3Schools.

L’autre chose à noter est bas, que nous avons copié les règles de style qui ont été initialement défini individuellement dans les *Movies.cshtml* fichier. Ces règles ont été utilisées dans le [Introduction à l’affichage des données à l’aide des Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=251580) didacticiel pour rendre le `WebGrid` helper restituer un balisage qui a ajouté des bandes à la table. (Si vous prévoyez d’utiliser un *.css* fichier pour les définitions de style, vous pouvez également placer les règles de style pour l’ensemble du site.)

## <a name="updating-the-movies-file-to-use-the-layout"></a>Mise à jour le fichier de films pour utiliser la mise en page

Vous pouvez maintenant mettre à jour les fichiers existants dans votre site d’utiliser la nouvelle disposition. Ouvrez le *Movies.cshtml* fichier. En haut, la première ligne de code, ajoutez le code suivant :

[!code-csharp[Main](layouts/samples/sample3.cs)]

La page démarre désormais ainsi :

[!code-cshtml[Main](layouts/samples/sample4.cshtml?highlight=2)]

Cette ligne de code indique à ASP.NET que quand le *films* page s’exécute, il doit être fusionné avec le  *\_Layout.cshtml* fichier.

Étant donné que la *Movies.cshtml* fichier utilise désormais une page de disposition, vous pouvez supprimer le balisage à partir de la *Movies.cshtml* page qui a pris en charge par le  *\_Layout.cshtml*fichier. Retirez la `<!DOCTYPE>`, `<html>`, et `<body>` balises ouvrantes et fermantes. Retirez l’ensemble de `<head>` élément et son contenu, ce qui inclut les règles de style pour la grille, dans la mesure où vous avez maintenant ces règles un *.css* fichier. Alors que vous êtes, les modifier existants `<h1>` élément à un `<h2>` élément ; vous avez un `<h1>` élément dans la page de disposition est déjà. Modifier la `<h2>` texte à la « Liste de films ».

Normalement, vous n’avez pas à apporter ces types de modifications dans une page de contenu. Lorsque vous commencez votre site avec une page de disposition, vous créez des pages de contenu sans tous ces éléments pour commencer. Dans ce cas, cependant, vous êtes conversion autonome d’une page qui utilise une mise en page, par conséquent, il est un peu de nettoyage.

Lorsque vous avez terminé, le *Movies.cshtml* page doit ressembler à ce qui suit :

[!code-cshtml[Main](layouts/samples/sample5.cshtml)]

### <a name="testing-the-layout"></a>Test de la disposition

Maintenant, vous pouvez voir l’aspect de la mise en page. Dans WebMatrix, cliquez sur le *Movies.cshtml* page et sélectionnez **lancer dans un navigateur**. Lorsque le navigateur affiche la page, il semble que cette page :

![Page de films restitué à l’aide d’une mise en page](layouts/_static/image6.png)

ASP.NET a fusionné le contenu de la page Movies.cshtml dans le  *\_Layout.cshtml* page de droite où les `RenderBody` est de la méthode. Et bien sûr le  *\_Layout.cshtml* page références un *.css* fichier qui définit l’apparence de la page.

## <a name="updating-the-addmovie-page-to-use-the-layout"></a>Mise à jour de la Page AddMovie pour utiliser la mise en page

L’avantage réel de dispositions est que vous pouvez les utiliser pour toutes les pages de votre site. Ouvrez le *AddMovie.cshtml* page.

Vous pouvez n’oubliez pas que le *AddMovie.cshtml* page contenait à l’origine des règles CSS qui définissent l’apparence des messages d’erreur de validation. Étant donné que vous avez un *.css* fichier de votre site maintenant, vous pouvez déplacer ces règles à la *.css* fichier. Les en retirer le *AddMovie.cshtml* de fichiers et de les ajouter au bas de la *Movies.css* fichier. Vous déplacez les règles suivantes :

[!code-css[Main](layouts/samples/sample6.css)]

Maintenant apporter les mêmes types de modifications dans *AddMovie.cshtml* que vous avez effectué *Movies.cshtml* — ajouter `Layout="~/_Layout.cshtml;` et supprimez le balisage HTML qui est désormais superflu. Modifier la `<h1>` élément `<h2>`. Lorsque vous avez terminé, la page doit ressembler à cet exemple :

[!code-cshtml[Main](layouts/samples/sample7.cshtml)]

Exécutez la page. Il ressemble maintenant à cette illustration :

![Page Ajouter des films restituée à l’aide d’une mise en page](layouts/_static/image7.png)

Vous souhaitez apporter des modifications similaires à des pages dans le site : *EditMovie.cshtml* et *DeleteMovie.cshtml*. Toutefois, avant de procéder, vous pouvez apporter une autre modification à la disposition qui rend un peu plus flexible.

## <a name="passing-title-information-to-the-layout-page"></a>Passage des informations de titre pour la Page de disposition

Le  *\_Layout.cshtml* a de la page que vous avez créé un `<title>` élément qui est définie sur « Mon Site film ». La plupart des navigateurs affichent le contenu de cet élément en tant que le texte d’un onglet :

![La page &lt;titre&gt; élément affiché dans un onglet de navigateur](layouts/_static/image8.png)

Ces informations de titre sont génériques. Vous voulez que le texte du titre plus spécifique à la page active. (Le texte du titre est également utilisé par les moteurs de recherche pour déterminer ce qui concerne votre page.) Vous pouvez passer des informations à partir d’une page de contenu comme *Movies.cshtml* ou *AddMovie.cshtml* effectue le rendu à la page de disposition, puis utiliser ces informations pour personnaliser le contenu de la page mise en page.

Ouvrez le *Movies.cshtml* page à nouveau. Dans le code en haut, ajoutez la ligne suivante :

[!code-csharp[Main](layouts/samples/sample8.cs)]

Le `Page` objet n’est disponible sur tous les *.cshtml* pages et est à cet effet, à savoir pour partager des informations entre une page et de sa disposition.

Ouvrez le*\_Layout.cshtml* page. Modifier la `<title>` élément afin qu’il ressemble à ce balisage :

[!code-html[Main](layouts/samples/sample9.html)]

Ce code affiche tout ce qui est dans le `Page.Title` propriété à cet emplacement dans la page.

Exécutez le *Movies.cshtml* page. Cette fois l’onglet navigateur montre ce que vous avez passé en tant que la valeur de `Page.Title`:

![Un onglet de navigateur affichant le titre créé dynamiquement](layouts/_static/image9.png)

Si vous le souhaitez, affichez la source de la page dans le navigateur. Vous pouvez voir que la `<title>` élément est restitué sous la forme `<title>List Movies</title>`.

> [!TIP] 
> 
> **L’objet de la Page**
> 
> Une fonctionnalité de `Page` il s’agit d’un objet dynamique, le `Title` propriété n’est pas un nom fixe ou réservé. Vous pouvez utiliser *tout* nom d’une valeur de la `Page` objet. Par exemple, vous avez peut aussi facilement ont passé le titre à l’aide d’une propriété nommée `Page.CurrentName` ou `Page.MyPage`. La seule restriction est que le nom doit respecter les règles normales pour les propriétés qui peuvent être nommées. (Par exemple, le nom ne peut pas contenir un espace.)
> 
> Vous pouvez passer n’importe quel nombre de valeurs à l’aide de la `Page` objet. Si vous souhaitez passer des informations de vidéo à la page de disposition, vous pouvez passer des valeurs à l’aide de quelque chose comme `Page.MovieTitle` et `Page.Genre` et `Page.MovieYear`. (Ou tous les autres noms qui vous a inventé pour stocker les informations.) La seule exigence, qui est probablement évident, est que vous devez utiliser les mêmes noms dans la page de contenu et de la page de disposition.
> 
> Les informations que vous passez à l’aide de la `Page` objet n’est pas limité à tout le texte à afficher sur la page de disposition. Vous pouvez passer une valeur à la page de disposition et de code dans la page de disposition permet ensuite la valeur de décider s’il faut afficher une section de la page, ce qui *.css* pour et ainsi de suite. Les valeurs que vous passez le `Page` objet sont comme d’autres valeurs que vous utilisez dans le code. Il est simplement que les valeurs proviennent de la page de contenu et sont passés à la page de disposition.


Ouvrez le *AddMovie.cshtml* page et ajoutez une ligne vers le haut du code qui fournit un titre pour le *AddMovie.cshtml* page :

[!code-csharp[Main](layouts/samples/sample10.cs)]

Exécutez le *AddMovie.cshtml* page. Vous consultez le nouveau titre il :

![Un onglet de navigateur affichant le titre « Ajouter des films » créé dynamiquement](layouts/_static/image10.png)

## <a name="updating-the-remaining-pages-to-use-the-layout"></a>Mise à jour les Pages restantes pour utiliser la mise en page

Maintenant, vous pouvez terminer les pages restantes de votre site afin qu’ils utilisent la nouvelle disposition. Ouvrez *EditMovie.cshtml* et *DeleteMovie.cshtml* à son tour et apporter les mêmes modifications dans chaque.

Ajoutez la ligne de code qui établit un lien vers la page de disposition :

[!code-csharp[Main](layouts/samples/sample11.cs)]

Ajoutez une ligne pour définir le titre de la page :

[!code-csharp[Main](layouts/samples/sample12.cs)]

ou :

[!code-csharp[Main](layouts/samples/sample13.cs)]

Supprimer tout le balisage HTML superflu : en fait, conservez uniquement les bits qui sont à l’intérieur du `<body>` élément (plus le bloc de code en haut).

Modifier la `<h1>` élément soit un `<h2>` élément.

Lorsque vous avez apporté ces modifications, chaque test et assurez-vous qu’elle s’affiche correctement et que le titre est correct.

## <a name="parting-thoughts-about-layout-pages"></a>Joint des réflexions sur les Pages de disposition

Dans ce didacticiel, vous avez créé un  *\_Layout.cshtml* page et utilisé la `RenderBody` méthode pour fusionner le contenu à partir d’une autre page. C’est le modèle de base pour l’utilisation des dispositions dans les Pages Web.

Pages de disposition ont des fonctionnalités supplémentaires qui nous ne couvrons ici. Par exemple, vous pouvez imbriquer des pages de disposition, une page de disposition permettre référencer à son tour un autre. Dispositions imbriquées peuvent être utiles si vous travaillez avec les sous-sections d’un site qui nécessitent des dispositions différentes. Vous pouvez également utiliser des méthodes supplémentaires (par exemple, `RenderSection`) pour configurer nommé sections de la page de disposition.

La combinaison de pages de disposition et *.css* fichiers est puissant. Comme vous le verrez dans la prochaine série de didacticiels, dans WebMatrix, vous pouvez créer un site basé sur un *modèle*, qui vous donne un site qui possède des fonctionnalités prégénérées qu’elle contient. Les modèles d’exploiter au mieux de CSS pour créer des sites qui superbes et qui ont des fonctionnalités telles que les menus et les pages de disposition. Voici une capture d’écran de la page d’accueil d’un site basé sur un modèle, en affichant les fonctionnalités qui utilisent des pages de disposition et CSS :

![Mise en page créée par le modèle de site WebMatrix montrant les en-tête, zone de navigation, zone de contenu, section facultative et les liens de connexion](layouts/_static/image11.png)

## <a name="complete-listing-for-movie-page-updated-to-use-a-layout-page"></a>Liste complète pour la Page de film (mis à jour pour utiliser une Page de disposition)

[!code-cshtml[Main](layouts/samples/sample14.cshtml)]

## <a name="complete-page-listing-for-add-movie-page-updated-for-layout"></a>Page fin de liste pour ajouter une Page de film (mis à jour pour la mise en page)

[!code-cshtml[Main](layouts/samples/sample15.cshtml)]

## <a name="complete-page-listing-for-delete-movie-page-updated-for-layout"></a>Terminer l’annonce de Page pour supprimer la Page film (mis à jour pour la mise en page)

[!code-cshtml[Main](layouts/samples/sample16.cshtml)]

## <a name="complete-page-listing-for-edit-movie-page-updated-for-layout"></a>Terminer l’annonce de Page pour la Page de film de modification (mise à jour pour la mise en page)

[!code-cshtml[Main](layouts/samples/sample17.cshtml)]

## <a name="coming-up-next"></a>Prochaine

Dans l’étape suivante du didacticiel, vous allez apprendre à publier votre site sur Internet pour que tous les utilisateurs puissent le voir.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Création d’une zone de recherche cohérent](https://go.microsoft.com/fwlink/?LinkID=202891) , un article qui fournit certaines plus de détails sur l’utilisation des dispositions. Il explique également comment passer une valeur à une page de disposition qui affiche ou masque le contenu.
- [Pages de disposition avec Razor imbriquées](http://www.mikesdotnetting.com/Article/164/Nested-Layout-Pages-with-Razor) , Mike Brind blogs un exemple montrant comment imbriquer des pages de disposition. (Y compris un téléchargement des pages.)

>[!div class="step-by-step"]
[Précédent](deleting-data.md)
[Suivant](publishing.md)
