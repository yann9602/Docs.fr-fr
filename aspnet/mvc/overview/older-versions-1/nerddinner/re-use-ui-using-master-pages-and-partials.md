---
uid: mvc/overview/older-versions-1/nerddinner/re-use-ui-using-master-pages-and-partials
title: "Réutilisation de l’interface utilisateur à l’aide de Pages maîtres et aucun | Documents Microsoft"
author: microsoft
description: "Étape 7 examine les méthodes que nous pouvons appliquer le principe de secs dans nos modèles de vue pour éliminer la duplication de code, à l’aide de pages maîtres et les modèles de vue partielle."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/27/2010
ms.topic: article
ms.assetid: d4243a4a-e91c-4116-9ae0-5c08e5285677
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/nerddinner/re-use-ui-using-master-pages-and-partials
msc.type: authoredcontent
ms.openlocfilehash: c42cd6aca40b08a9f8461532fbfd0589901b64ad
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="re-use-ui-using-master-pages-and-partials"></a>Réutilisation de l’interface utilisateur à l’aide de Pages maîtres et aucun
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le PDF](http://aspnetmvcbook.s3.amazonaws.com/aspnetmvc-nerdinner_v1.pdf)

> Il s’agit d’étape 7 a gratuit [« « l’application NerdDinner](introducing-the-nerddinner-tutorial.md) qui parcours à comment générer un petit, mais se termine, application web à l’aide d’ASP.NET MVC 1.
> 
> Étape 7 examine les méthodes que nous pouvons appliquer le principe « sec » dans nos modèles de vue pour éliminer la duplication de code, à l’aide de pages maîtres et les modèles de vue partielle.
> 
> Si vous utilisez ASP.NET MVC 3, nous vous recommandons de suivre les [mise en route a démarré avec MVC 3](../../older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) ou [magasin de musique MVC](../../older-versions/mvc-music-store/mvc-music-store-part-1.md) didacticiels.


## <a name="nerddinner-step-7-partials-and-master-pages"></a>NerdDinner étape 7 : Aucun et les Pages maîtres

La philosophie de conception QU'ASP.NET MVC prend en compte est le principe « Faire pas se répéter » (communément appelé « Sec »). Une conception sec permet d’éviter la duplication de code et de logique, ce qui rend finalement les applications plus rapides pour générer et plus faciles à gérer.

Nous avons déjà vu le principe sec appliqué dans plusieurs de nos scénarios NerdDinner. Voici quelques exemples : notre logique de validation est implémenté dans la couche de notre modèle, qui lui permet d’être appliqués sur les deux modifier et créer des scénarios dans notre contrôleur ; Nous utilisons nouveau le modèle d’affichage « NotFound » sur les méthodes d’action Modifier, les détails et les supprimer ; Nous utilisons un convention d’affectation de noms motif avec nos modèles de vue, ce qui élimine le besoin de spécifier explicitement le nom de nous appeler la méthode d’assistance View() ; et nous la classe DinnerFormViewModel réutilisez pour les modifier et créer des scénarios d’action.

Passons maintenant que nous pouvons appliquer le principe « sec » des méthodes dans nos modèles de vue pour éliminer ainsi de duplication de code il.

### <a name="re-visiting-our-edit-and-create-view-templates"></a>Revisiter notre modifier et créer des modèles d’affichage

Actuellement, nous utilisons deux modèles d’affichage différent – « Edit.aspx » et « Create.aspx » – pour afficher notre interface utilisateur du formulaire dîner. Une comparaison rapide visual d'entre eux met en évidence le degré de similitude qu’ils sont. Voici à quoi ressemble le formulaire de création :

![](re-use-ui-using-master-pages-and-partials/_static/image1.png)

Et Voici à quoi ressemble notre formulaire « Modifier » :

![](re-use-ui-using-master-pages-and-partials/_static/image2.png)

Pas beaucoup de différence existe-t-il ? Autre que le texte de titre et l’en-tête, les contrôles de disposition et l’entrée du formulaire sont identiques.

Si nous ouvrons la « Edit.aspx » et « Create.aspx » afficher les modèles, vous trouverez qui ils contient du code de contrôle de mise en page et entrée forme identique. Cette duplication signifie que nous nous retrouvons devoir apporter des modifications à deux reprises chaque fois que nous introduire ou modifier une nouvelle propriété dîner - qui n’est pas correcte.

### <a name="using-partial-view-templates"></a>À l’aide de modèles de vue partielle

ASP.NET MVC prend en charge la possibilité de définir des modèles de « vue partielle » qui peuvent être utilisés pour encapsuler la logique de rendu de vue pour une partie secondaire d’une page. « Aucun » fournit une méthode utile pour définir la logique de rendu de vue qu’une seule fois, puis réutiliser dans plusieurs emplacements dans une application.

Pour aider à « sec » notre Edit.aspx et duplication de modèle de vue Create.aspx, nous pouvons créer un modèle de vue partielle nommé « DinnerForm.ascx » qui encapsule la disposition du formulaire et des éléments d’entrée communs aux deux. Nous verrons comment procéder en cliquant sur notre répertoire préparés/vues et en choisissant la « Add -&gt;vue « commande de menu :

![](re-use-ui-using-master-pages-and-partials/_static/image3.png)

Cette action affiche la boîte de dialogue « Ajouter une vue ». Nous allons nommer la nouvelle vue créer des « DinnerForm », sélectionnez la case à cocher « Créer une vue partielle » dans la boîte de dialogue et nous indiquer que nous transmettons il une classe DinnerFormViewModel :

![](re-use-ui-using-master-pages-and-partials/_static/image4.png)

Lorsque vous cliquez sur le bouton « Ajouter », Visual Studio créera un nouveau modèle de vue « DinnerForm.ascx » pour nous dans le répertoire « \Views\Dinners ».

Nous pouvons ensuite copier/coller la disposition du formulaire en double / d’entrée de code à partir de notre Edit.aspx/ Create.aspx afficher les modèles de contrôle dans notre nouveau modèle de vue partielle « DinnerForm.ascx » :

[!code-aspx[Main](re-use-ui-using-master-pages-and-partials/samples/sample1.aspx)]

Nous pouvons ensuite à jour notre afficher modifier et créer les modèles pour appeler le modèle partiels DinnerForm et éviter la duplication du formulaire. Vous pouvez le faire en appelant Html.RenderPartial("DinnerForm") dans nos modèles de vue :

##### <a name="createaspx"></a>Create.aspx

[!code-aspx[Main](re-use-ui-using-master-pages-and-partials/samples/sample2.aspx)]

##### <a name="editaspx"></a>Edit.aspx

[!code-aspx[Main](re-use-ui-using-master-pages-and-partials/samples/sample3.aspx)]

Vous pouvez qualifier explicitement le chemin d’accès du modèle partiels lors de l’appel Html.RenderPartial (par exemple : ~ Views/Dinners/DinnerForm.ascx »). Dans notre code ci-dessus, cependant, nous sommes tirant parti du modèle d’affectation de noms basé sur une convention dans ASP.NET MVC et uniquement en spécifiant « DinnerForm » comme nom de partielle à restituer. Lorsque cela, nous ASP.NET MVC recherche premier dans le répertoire de vues basées sur une convention (DinnersController/vues préparés serait). S’il ne trouve pas le modèle partiels il il sera recherchez-le dans le répertoire /Views/Shared.

Lorsque Html.RenderPartial() est appelée uniquement avec le nom de la vue partielle, ASP.NET MVC passe à la vue partielle du même modèle et ViewData dictionnaire des objets utilisés par le modèle d’affichage appelant. Il existe également des versions surchargées de Html.RenderPartial() qui vous permettent de passer un objet de modèle de remplacement et/ou de dictionnaire ViewData de la vue partielle à utiliser. Cela est utile pour les scénarios où vous souhaitez uniquement passer un sous-ensemble de modèle/ViewModel complète.

| **Rubrique de côté : Pourquoi &lt;%%&gt; au lieu de &lt;% = %&gt;?** |
| --- |
| Une des opérations légères que vous avez peut-être remarqué avec le code ci-dessus est que nous utilisons un &lt;%%&gt; bloquer au lieu d’un &lt;% = %&gt; bloquer lors de l’appel Html.RenderPartial(). &lt;% = %&gt; blocs dans ASP.NET indiquent qu’un développeur souhaite afficher une valeur spécifiée (par exemple : &lt;% = « Hello »&gt; rendrait « Hello »). &lt;%%&gt; blocs indiquent plutôt que le développeur souhaite exécuter du code, et qu’un rendu de la sortie au sein de celles-ci doit être effectué explicitement (par exemple : &lt;Response.Write("Hello") %&gt;. La raison pour laquelle nous utilisons un &lt;%%&gt; bloc avec notre code Html.RenderPartial ci-dessus est, car la méthode Html.RenderPartial() ne retourne pas une chaîne et à la place fournit en sortie le contenu directement à l’appelant le modèle d’affichage du flux de sortie. Il procède ainsi pour des raisons d’efficacité de performances et en procédant ainsi, elle évite la nécessité de créer un objet chaîne temporaire (potentiellement très grande). Cela réduit l’utilisation de la mémoire et améliore le débit global d’applications. Une erreur fréquente lorsqu’à l’aide de Html.RenderPartial() de doit pas oublié d’ajouter un point-virgule à la fin de l’appel lorsqu’il se trouve dans un &lt;%%&gt; bloc. Par exemple, ce code entraîne une erreur du compilateur : &lt;Html.RenderPartial("DinnerForm") %&gt; à la place vous devez écrire : &lt;: Html.RenderPartial("DinnerForm") ; %&gt; effet &lt;%%&gt; blocs sont des instructions de code autonome et lors de l’utilisation de code c# instructions doivent se terminer par un point-virgule. |

### <a name="using-partial-view-templates-to-clarify-code"></a>À l’aide de modèles de vue partielle à clarifier le Code

Nous avons créé le modèle de vue partielle « DinnerForm » pour éviter de dupliquer la logique de rendu d’affichage à plusieurs endroits. Il s’agit de la cause la plus courante pour créer des modèles de vue partielle.

Parfois, il est toujours utile de créer des vues partielles même lorsqu’elles sont appelées uniquement dans un emplacement unique. Modèles d’affichage très complexe peuvent devenir beaucoup plus faciles à lire lorsque leur logique de rendu de vue est extraite et partitionnée en une seule ou plus correctement nommé modèles partielles.

Par exemple, considérez la ci-dessous extrait de code à partir du fichier Site.master dans notre projet (ce qui nous prendrons en peu de temps). Le code est relativement simple à lire, en partie parce que la logique pour afficher une connexion/déconnexion situé en haut à droite de l’écran est encapsulée dans partielle « LogOnUserControl » :

[!code-aspx[Main](re-use-ui-using-master-pages-and-partials/samples/sample4.aspx)]

Chaque fois que vous vous trouvez confondre la tentative de comprendre le balisage html/le code dans un modèle de vue, envisagez de si elle ne serait plus claire si certaines de ces informations a été extraite et refactorisés dans les vues partielles correctement nommés.

### <a name="master-pages"></a>Pages maîtres

Prise en charge de vues partielles, ASP.NET MVC prend également en charge la possibilité de créer des modèles de « page maître » qui peuvent être utilisés pour définir la disposition courante et html de niveau supérieur d’un site. Espace réservé de contrôles peuvent ensuite être ajoutés à la page maître pour identifier les zones remplaçables qui peuvent être remplacées ou « remplis » par les vues de contenu. Cela fournit un moyen très efficace (et sec) pour appliquer une mise en page courantes dans une application.

Par défaut, les nouveaux projets ASP.NET MVC ont un modèle de page maître ajouté automatiquement. Cette page maître est nommée « Site.master » et se trouve dans le dossier \Views\Shared\ :

![](re-use-ui-using-master-pages-and-partials/_static/image5.png)

Le fichier Site.master par défaut se présente comme ci-dessous. Il définit le code externe html d’un site, ainsi qu’un menu de navigation en haut. Il contient deux contrôles d’espace réservé de contenu remplaçables : une pour le titre et l’autre pour où le contenu principal d’une page doit être remplacé :

[!code-aspx[Main](re-use-ui-using-master-pages-and-partials/samples/sample5.aspx)]

Tous les modèles d’affichage que nous avons créé pour notre application NerdDinner (« List », « Details », « Modifier », « Créer », « NotFound », etc.) ont été basés sur ce modèle de Site.master. Cela est indiqué par l’attribut « MasterPageFile » qui a été ajouté par défaut vers le haut &lt;% @ Page %&gt; directive lorsque nous avons créé notre vues à l’aide de la boîte de dialogue « Ajouter une vue » :

[!code-aspx[Main](re-use-ui-using-master-pages-and-partials/samples/sample6.aspx)]

Cela signifie que nous pouvons modifier le contenu Site.master et ont les modifications automatiquement appliquées et utilisé lorsque nous restituer les modèles d’affichage.

Nous allons mettre à jour section d’en-tête de notre Site.master afin que l’en-tête de notre application est « NerdDinner » au lieu de « My Application MVC ». Nous allons également mettre à jour notre menu de navigation afin que le premier onglet est « Rechercher un dîner » (géré par la méthode d’action du HomeController Index()) et nous allons ajouter un nouvel onglet appelé « Hôte un dîner » (géré par la méthode d’action de la DinnersController Create()) :

[!code-aspx[Main](re-use-ui-using-master-pages-and-partials/samples/sample7.aspx)]

Lorsque nous enregistrez le fichier Site.master et actualiser notre navigateur nous verrons notre en-tête modifications afficher jusqu'à à toutes les vues dans une application. Exemple :

![](re-use-ui-using-master-pages-and-partials/_static/image6.png)

Et avec le */Dinners/modification / [id]* URL :

![](re-use-ui-using-master-pages-and-partials/_static/image7.png)

### <a name="next-step"></a>Étape suivante

Aucun et les pages maîtres fournissent des options très flexibles qui vous permettent d’organiser correctement les vues. Vous trouverez qu’ils permettent de vous éviter de dupliquer la vue de contenu / de code et rendre vos modèles de vue plus facile à lire et à gérer.

Nous allons maintenant revoir le scénario de liste que nous avons créé précédemment et activer la prise en charge de la pagination évolutive.

>[!div class="step-by-step"]
[Précédent](use-viewdata-and-implement-viewmodel-classes.md)
[Suivant](implement-efficient-data-paging.md)
