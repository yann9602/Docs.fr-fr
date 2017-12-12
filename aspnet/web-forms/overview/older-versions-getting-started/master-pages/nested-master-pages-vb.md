---
uid: web-forms/overview/older-versions-getting-started/master-pages/nested-master-pages-vb
title: "Imbriqué les Pages maîtres (VB) | Documents Microsoft"
author: rick-anderson
description: "Montre comment imbriquer une page maître dans une autre."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/28/2008
ms.topic: article
ms.assetid: 14d9aa1b-4dca-43a0-aa9d-a6e891fee019
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/master-pages/nested-master-pages-vb
msc.type: authoredcontent
ms.openlocfilehash: 41a9fd6b752252d563a0f15a420262cbb31c19ff
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="nested-master-pages-vb"></a>Pages maîtres imbriquées (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/d/6/6/d66ad554-afdd-409e-a5c3-201b774fbb31/ASPNET_MasterPages_Tutorial_10_VB.zip) ou [télécharger le PDF](http://download.microsoft.com/download/d/6/6/d66ad554-afdd-409e-a5c3-201b774fbb31/ASPNET_MasterPages_Tutorial_10_VB.pdf)

> Montre comment imbriquer une page maître dans une autre.


## <a name="introduction"></a>Introduction

Au cours des neuf derniers didacticiels, nous avons vu comment implémenter une disposition à l’échelle du site avec les pages maîtres. En bref, les pages maîtres, permettent au développeur de page, pour définir le balisage courantes dans la page maître, ainsi que des régions spécifiques qui peuvent être personnalisés pour chaque page de contenu page en fonction du contenu. Les contrôles ContentPlaceHolder dans une page maître indiquent les zones personnalisables ; le code personnalisé pour les contrôles ContentPlaceHolder sont définies dans la page de contenu via des contrôles de contenu.

Les techniques de page maître que nous avons exploré jusqu’ici sont idéaux si vous disposez d’une disposition unique utilisée sur l’ensemble du site. Toutefois, de nombreux sites Web de grande taille ont une disposition de site personnalisé sur plusieurs sections différentes. Par exemple, considérez une application de soins de santé utilisée par le personnel de l’hôpital pour gérer la facturation, des activités et des informations sur les patients. Il peut y avoir trois types de pages web de cette application :

- Les pages de membres spécifiques du personnel où les membres du personnel peuvent mettre à jour la disponibilité, afficher les planifications ou demandez à congés.
- Pages spécifiques à un patient où les membres du personnel afficher ou modifier les informations d’un patient spécifique.
- Pages de facturation spécifiques où éprouve des difficultés examiner en cours de revendication États et les rapports financiers.

Chaque page susceptibles de partager une disposition courante, par exemple un menu entre le haut et une série de liens fréquemment utilisées dans la partie inférieure. Mais les pages spécifiques à la facturation, patient- et personnel devrez peut-être personnaliser cette disposition générique. Par exemple, peut-être toutes les pages du personnel spécifiques doivent inclure une liste de calendrier et des tâches montrant la disponibilité et la planification quotidienne de l’utilisateur actuellement connecté. Peut-être toutes les pages spécifiques à un patient besoin afficher le nom, adresse et les informations d’assurance du patient dont les informations sont en cours de modification.

Il est possible de créer ces dispositions personnalisées à l’aide de *des pages maîtres imbriquées*. Pour implémenter le scénario ci-dessus, nous commence par la création d’une page maître qui a défini le contenu de mise en page, le menu et le pied de page à l’échelle du site, avec ContentPlaceHolders définissant les régions personnalisables. Il faudrait créer puis de trois pages maîtres imbriquées, une pour chaque type de page web. Chaque page maître imbriquée définirait le contenu entre le type de pages de contenu qui utilisent la page maître. En d’autres termes, la page maître imbriquée pour les pages de contenu spécifiques à un patient inclurait balisage et la logique de programmation pour afficher des informations sur le patient en cours de modification. Lors de la création d’une page spécifique d’un patient nous serait le lier à cette page maître imbriquée.

Ce didacticiel commence par la mise en évidence les avantages des pages maîtres imbriquées. Il montre ensuite comment créer et utiliser des pages maîtres imbriquées.

> [!NOTE]
> Les pages maîtres imbriquées ont été possible depuis la version 2.0 du .NET Framework. Toutefois, Visual Studio 2005 n’incluait pas de prise en charge au moment du design pour les pages maîtres imbriquées. La bonne nouvelle est que Visual Studio 2008 offre une riche expérience au moment du design pour les pages maîtres imbriquées. Si vous êtes intéressé par les pages maîtres imbriquées, mais sont toujours à l’aide de Visual Studio 2005, consultez [Scott Guthrie](https://weblogs.asp.net/scottgu/)d’entrée de blog, [conseils pour les Pages maîtres imbriquées dans la conception de Visual Studio 2005](https://weblogs.asp.net/scottgu/archive/2005/11/11/430382.aspx).


## <a name="the-benefits-of-nested-master-pages"></a>Les avantages des Pages maîtres imbriquées

De nombreux sites Web ont une conception de site principal, ainsi que plus des modèles personnalisés spécifiques à certains types de pages. Par exemple, dans notre exemple d’application web, nous avons créé une section Administration rudimentaire (les pages dans le `~/Admin` dossier). Actuellement les pages web dans le `~/Admin` dossier utilisent la même page maître en tant que ces pages dans la section administration (à savoir, `Site.master` ou `Alternate.master`, selon la sélection de l’utilisateur).

> [!NOTE]
> Pour le moment, supposons que notre site a juste une page maître, `Site.master`. Nous allons résoudre à l’aide de pages maîtres imbriquées avec les pages maîtres de deux (ou plus) en commençant par « À l’aide un imbriquée principale pour l’Administration Section de la Page » plus loin dans ce didacticiel.


Imaginons que nous avons demandées pour personnaliser la disposition des pages d’Administration pour inclure des informations supplémentaires ou des liens qui pas seraient présents dans d’autres pages du site. Il existe quatre techniques pour implémenter cette exigence :

1. Ajouter manuellement les informations spécifiques à l’Administration et les liens pour chaque page de contenu dans le `~/Admin` dossier.
2. Mise à jour le `Site.master` page maître à inclure les informations spécifiques à la section d’Administration et les liens, ajoutez ensuite du code à la page maître pour afficher ou masquer des sections suivantes selon si une des pages d’Administration est visitée.
3. Créer une nouvelle page maître spécifiquement pour la section Administration, recopiez les balises de `Site.master`, ajoutez la section des informations de l’Administration et des liens, puis mettre à jour les pages de contenu dans le `~/Admin` dossier à utiliser ce nouveau masque page.
4. Créer une page maître imbriquée qui lie à `Site.master` et que les pages de contenu de la `~/Admin` cette nouvelle utilisation des dossiers imbriqués de page maître. Cette page maître imbriquée inclut uniquement les informations et liens supplémentaires spécifiques pour les pages d’Administration et n’a pas besoin de répéter le balisage déjà défini dans `Site.master`.

La première option est la moins aisée. L’objectif de l’utilisation de pages maîtres consiste à déplacer en s’éloignant de devoir copier et coller le balisage courants pour les nouvelles pages ASP.NET manuellement. La deuxième option est acceptable, mais rend l’application moins facile à gérer que les pages maîtres par un balisage qui s’affiche qu’occasionnellement et exige des développeurs modifiez la page maître pour contourner ce balisage et d’avoir à mémoriser, il épaissit exactement, certaines balises est affiché et lorsqu’il est masqué. Cette approche serait moins tenable en tant que de personnalisations à partir des types de davantage de pages web nécessaires pour être prises en charge par cette page maître unique.

La troisième option supprime l’encombrement et de complexité problèmes la bande à la deuxième option. Toutefois, option de trois principal inconvénient est qu’il nous oblige à copier et coller la mise en page courantes à partir de `Site.master` vers la nouvelle page maître de spécifique de la section d’Administration. Si vous décidez ultérieurement de modifier la disposition à l’échelle du site, nous avons n’oubliez pas de le modifier dans deux emplacements.

La quatrième option, des pages maîtres imbriquées, nous donner le meilleur de la deuxième et la troisième option. Les informations de disposition de l’échelle du site sont conservées dans un seul fichier - la page maître de niveau supérieur - alors que le contenu spécifique à des régions particuliers est séparé dans différents fichiers.

Ce didacticiel commence par un examen de la création et l’utilisation d’une page maître imbriquée simple. Nous créons une toute nouvelle page maître de niveau supérieur, deux pages maîtres imbriquées et deux pages de contenu. À partir de « À l’aide un imbriqués Master Page pour la Section Administration », nous allons mettre à jour notre architecture existante de la page maître pour inclure l’utilisation de pages maîtres imbriquées. Plus précisément, nous créer une page maître imbriquée et il permet d’inclure du contenu personnalisé supplémentaire pour les pages de contenu dans le `~/Admin` dossier.

## <a name="step-1-creating-a-simple-top-level-master-page"></a>Étape 1 : Création d’une Simple Page maître de niveau supérieur

Création d’un maître imbriqué selon l’une du principal existant pages et de mise à jour d’une page de contenu existante pour utiliser cette nouvelle page maître imbriquée au lieu de la page maître de niveau supérieur puis implique plus complexe, car les pages de contenu existants déjà attendent que certaines Contrôles ContentPlaceHolder définis dans la page maître de niveau supérieur. Par conséquent, la page maître imbriquée doit également inclure les mêmes contrôles ContentPlaceHolder portant le même nom. En outre, notre application de démonstration particulier a deux pages maîtres (`Site.master` et `Alternate.master`) qui sont attribuées dynamiquement à une page de contenu en fonction des préférences de l’utilisateur, qui ajoute cette complexité supplémentaire. Nous allons nous intéresser à la mise à jour de l’application existante à utiliser les pages maîtres imbriquées plus loin dans ce didacticiel, mais nous allons concentrer tout d’abord sur un simple imbriqué exemple des pages maîtres.

Créez un dossier nommé `NestedMasterPages` , puis ajoutez un nouveau fichier de page maître de ce dossier nommé `Simple.master`. (Voir Figure 1 pour une capture d’écran de l’Explorateur de solutions après l’ajout de ce dossier et le fichier.) Faites glisser le `AlternateStyles.css` fichier de feuille de style à partir de l’Explorateur de solutions dans le concepteur. Cette opération ajoute une `<link>` élément pour le fichier de feuille de style dans le `<head>` élément, après laquelle la page maître `<head>` balisage de l’élément doit ressembler à :


[!code-aspx[Main](nested-master-pages-vb/samples/sample1.aspx)]

Ensuite, ajoutez le balisage suivant dans le formulaire Web de `Simple.master`:


[!code-aspx[Main](nested-master-pages-vb/samples/sample2.aspx)]

Cette balise affiche un lien intitulé « Pages maîtres imbriquées (Simple) » en haut de la page dans une grande taille de police blanc sur fond bleu foncé. Sous qui est le `MainContent` ContentPlaceHolder. La figure 1 montre la `Simple.master` page maître lors du chargement dans le concepteur Visual Studio.


[![La Page maître imbriquée définit le contenu spécifique à des Pages dans la Section Administration](nested-master-pages-vb/_static/image2.png)](nested-master-pages-vb/_static/image1.png)

**Figure 01**: l’imbriqués Master Page définit contenu spécifique à des Pages dans la Section Administration ([cliquez pour afficher l’image en taille réelle](nested-master-pages-vb/_static/image3.png))


## <a name="step-2-creating-a-simple-nested-master-page"></a>Étape 2 : Création d’une Page maître imbriquée Simple

`Simple.master`contient deux contrôles ContentPlaceHolder : le `MainContent` ContentPlaceHolder que nous avons ajouté dans le formulaire Web avec la `head` ContentPlaceHolder dans le `<head>` élément. Si vous deviez créer une page de contenu et le lier à `Simple.master` la page de contenu aurait deux contrôles de contenu faisant référence aux deux ContentPlaceHolders. De même, si nous créer une page maître imbriquée, puis liez-le à `Simple.master` puis la page maître imbriquée aura deux contrôles de contenu.

Vous allez ajouter une nouvelle page maître imbriquée à la `NestedMasterPages` dossier nommé `SimpleNested.master`. Avec le bouton droit sur le `NestedMasterPages` dossier et choisissez Ajouter un nouvel élément. Cela affiche la boîte de dialogue Ajouter un nouvel élément indiquée dans la Figure 2. Sélectionnez le type de modèle de Page maître et le nom de la nouvelle page maître. Pour indiquer que la nouvelle page maître doit être une page maître imbriquée, cochez la case à cocher « Sélectionnez page maître ».

Ensuite, cliquez sur le bouton Ajouter. Cela affichera la même, sélectionnez une boîte de dialogue de Page maître vous consultez lors de la liaison d’une page de contenu à une page maître (voir Figure 3). Choisissez le `Simple.master` page maître dans le `NestedMasterPages` dossier et cliquez sur OK.

> [!NOTE]
> Si vous avez créé votre site Web ASP.NET à l’aide du modèle de projet d’Application Web au lieu du modèle de projet de Site Web, vous ne verrez pas la case à cocher « Sélectionnez la page maître » dans la boîte de dialogue Ajouter un nouvel élément indiquée dans la Figure 2. Pour créer une page maître imbriquée lorsque vous utilisez le modèle de projet d’Application Web, vous devez choisir le modèle de Page maître imbriquée (au lieu du modèle de Page maître). Après la sélection du modèle de la Page maître imbriquée et en cliquant sur Ajouter, le même sélectionner une Page maître indiqué dans la Figure 3 apparaît.


[![Vérifiez le &quot;sélectionnez page maître&quot; case à cocher pour ajouter une Page maître imbriquée](nested-master-pages-vb/_static/image5.png)](nested-master-pages-vb/_static/image4.png)

**Figure 02**: case à cocher « Sélectionnez la page maître » pour ajouter une Page maître imbriquée ([cliquez pour afficher l’image en taille réelle](nested-master-pages-vb/_static/image6.png))


[![Lier la Page maître imbriquée à la Page maître de Simple.master](nested-master-pages-vb/_static/image8.png)](nested-master-pages-vb/_static/image7.png)

**Figure 03**: liaison de la Page maître imbriquée à la `Simple.master` Page maître ([cliquez pour afficher l’image en taille réelle](nested-master-pages-vb/_static/image9.png))


Balisage déclaratif de la page maître imbriquée, illustré ci-dessous, contient deux contrôles de contenu faisant référence à deux contrôles de ContentPlaceHolder de la page maître niveau supérieur.


[!code-aspx[Main](nested-master-pages-vb/samples/sample3.aspx)]

À l’exception de la `<%@ Master %>` directive, balisage déclaratif initiale de la page maître imbriquée est identique à la balise est initialement générée lors de la liaison d’une page de contenu à la même page maître de niveau supérieur. Comme une page de contenu `<%@ Page %>` directive, le `<%@ Master %>` directive ici inclut un `MasterPageFile` attribut qui spécifie la page maître du parent de la page maître imbriquée. La principale différence entre la page maître imbriquée et une page de contenu lié à la même page maître de niveau supérieur est que la page maître imbriquée peut inclure des contrôles ContentPlaceHolder. Les contrôles de la page maître imbriquée ContentPlaceHolder définissent les régions où les pages de contenu peuvent personnaliser le balisage.

Mettre à jour de cette page maître imbriquée afin qu’elle affiche le texte « Hello, à partir de SimpleNested ! » dans le contrôle de contenu qui correspond à la `MainContent` contrôle ContentPlaceHolder.


[!code-aspx[Main](nested-master-pages-vb/samples/sample4.aspx)]

Après avoir apporté cet ajout, enregistrez la page maître imbriquée et puis ajoutez une nouvelle page de contenu pour le `NestedMasterPages` dossier nommé `Default.aspx`, puis liez-le à le `SimpleNested.master` page maître. Lors de l’ajout de cette page vous serez peut-être surpris de voir qu’il contient sans contrôles de contenu (voir Figure 4) ! Une page de contenu peut accéder uniquement à son *parent* master ContentPlaceHolders de la page. `SimpleNested.master`ne contient pas tous les contrôles ContentPlaceHolder ; Par conséquent, n’importe quelle page de contenu liée à cette page maître ne peut pas contenir les contrôles de contenu.


[![La nouvelle Page de contenu ne contient pas les contrôles de contenu](nested-master-pages-vb/_static/image11.png)](nested-master-pages-vb/_static/image10.png)

**Figure 04**: la nouvelle Page contient aucun contenu contrôles de contenu ([cliquez pour afficher l’image en taille réelle](nested-master-pages-vb/_static/image12.png))


Nous devons faire est de mettre à jour de la page maître imbriquée (`SimpleNested.master`) pour inclure les contrôles ContentPlaceHolder. En général, vous pouvez répondre vos pages maîtres imbriquées pour inclure un espace réservé pour chaque ContentPlaceHolder défini par sa page maître parent, ce qui permet de sa page maître enfant ou une page de contenu à utiliser une des ContentPlaceHolder de la page maître niveau supérieur contrôles.

Mise à jour le `SimpleNested.master` page maître à inclure un espace réservé dans ses deux contrôles de contenu. Donner les contrôles ContentPlaceHolder le même nom que le contrôle ContentPlaceHolder à que leur contrôle de contenu fait référence. Autrement dit, ajouter un contrôle ContentPlaceHolder nommé `MainContent` pour le contenu de contrôle dans `SimpleNested.master` qui fait référence à la `MainContent` ContentPlaceHolder dans `Simple.master`. Faire la même chose dans le contrôle de contenu qui fait référence à la `head` ContentPlaceHolder.

> [!NOTE]
> Alors que je vous recommande d’affectation de noms les contrôles ContentPlaceHolder dans la page maître imbriquée identique les ContentPlaceHolders dans la page maître de niveau supérieur, cette symétrie d’affectation de noms n’est pas nécessaire. Vous pouvez attribuer les contrôles ContentPlaceHolder dans votre page maître imbriquée de n’importe quel nom de que votre choix. Toutefois, j’ai s’avérer plus facile à mémoriser les ContentPlaceHolders correspondent à quelles parties de la page si ma page maître de niveau supérieur et les pages maîtres imbriquées utilisent les mêmes noms.


Après avoir apporté ces ajouts votre `SimpleNested.master` balisage déclaratif de la page maître doit ressembler à ce qui suit :


[!code-aspx[Main](nested-master-pages-vb/samples/sample5.aspx)]

Supprimer le `Default.aspx` nous avons créé de page de contenu, puis ajouter de nouveau, liant à la `SimpleNested.master` page maître. Cette fois Visual Studio ajoute les deux contrôles de contenu pour le `Default.aspx`, référençant les ContentPlaceHolders maintenant défini dans `SimpleNested.master` (voir Figure 6). Ajoutez le texte, « Hello, à partir de Default.aspx ! » dans le contenu du contrôle référencé `MainContent`.

La figure 5 illustre les trois entités impliquées ici - `Simple.master`, `SimpleNested.master`, et `Default.aspx` - et comment elles se rapportent à un autre. Comme le montre le diagramme, la page maître imbriquée implémente des contrôles de contenu pour ContentPlaceHolder de son parent. Si ces régions doivent être accessibles à la page de contenu, la page maître imbriquée doit ajouter son propre ContentPlaceHolders aux contrôles de contenu.


[![Les Pages maîtres imbriquées et de niveau supérieur imposent la disposition de la Page de contenu](nested-master-pages-vb/_static/image14.png)](nested-master-pages-vb/_static/image13.png)

**Figure 05**: les Pages maîtres imbriquées et de niveau supérieur imposent la disposition de la Page de contenu ([cliquez pour afficher l’image en taille réelle](nested-master-pages-vb/_static/image15.png))


Ce comportement illustre la façon dont une page de contenu ou une page maître est uniquement informé de sa page maître parent. Ce comportement est également indiqué par le concepteur Visual Studio. La figure 6 illustre le concepteur pour `Default.aspx`. Alors que le Concepteur montre clairement quelles régions sont modifiables à partir de la page de contenu et les parties ne sont pas, il ne lever l’ambiguïté quelles sont les zones non modifiables de la page maître imbriquée et quelles sont les régions à partir de la page maître de niveau supérieur.


[![Maintenant Page contenu comprend des contrôles de contenu pour ContentPlaceHolders la Page maître imbriquée](nested-master-pages-vb/_static/image17.png)](nested-master-pages-vb/_static/image16.png)

**Figure 06**: le contenu Page maintenant inclut des contrôles de contenu pour les ContentPlaceHolders imbriqués principale de la Page ([cliquez pour afficher l’image en taille réelle](nested-master-pages-vb/_static/image18.png))


## <a name="step-3-adding-a-second-simple-nested-master-page"></a>Étape 3 : Ajout d’une deuxième Page maître imbriquée Simple

L’avantage de pages maîtres imbriquées est plus évident lorsqu’il y a plusieurs pages maîtres imbriquées. Pour illustrer ceci, créez une autre page maître imbriquée dans la `NestedMasterPages` dossier ; de la page maître de nom de cette nouvelle imbriqué `SimpleNestedAlternate.master` et liez-le à le `Simple.master` page maître. Ajouter des contrôles ContentPlaceHolder dans deux contrôles de contenu de la page maître imbriquée comme nous l’avons fait à l’étape 2. Également ajouter le texte, « Hello, à partir de SimpleNestedAlternate ! » dans le contrôle de contenu qui correspond à la page maître niveau supérieur `MainContent` ContentPlaceHolder. Après avoir apporté ces modifications balisage déclaratif de votre nouvelle page maître imbriquée doit ressembler à ce qui suit :


[!code-aspx[Main](nested-master-pages-vb/samples/sample6.aspx)]

Créer une page de contenu nommée `Alternate.aspx` dans les `NestedMasterPages` dossier puis liez-le à le `SimpleNestedAlternate.master` page maître imbriquée. Ajoutez le texte, « Hello, à partir de la solution de remplacement ! » dans le contrôle de contenu qui correspond à `MainContent`. La figure 7 illustre `Alternate.aspx` lorsqu’ils sont affichés par le biais du concepteur Visual Studio.


[![Alternate.aspx est lié à la Page maître de SimpleNestedAlternate.master](nested-master-pages-vb/_static/image20.png)](nested-master-pages-vb/_static/image19.png)

**Figure 07**: `Alternate.aspx` est lié à la `SimpleNestedAlternate.master` Page maître ([cliquez pour afficher l’image en taille réelle](nested-master-pages-vb/_static/image21.png))


Comparez le concepteur dans la Figure 7 vers le concepteur dans la Figure 6. Les deux pages de contenu partagent la même disposition que celui définie dans la page maître de niveau supérieur (`Simple.master`), à savoir le titre « Nested Master Pages didacticiel (Simple) ». Les deux ont encore contenu distinct défini dans leurs pages maîtres parent - le texte « Hello, à partir de SimpleNested ! » dans la Figure 6 et « Hello, à partir de SimpleNestedAlternate ! » dans la Figure 7. Accordées, ces différences sont triviales, mais vous pouvez étendre cet exemple pour inclure des différences plus significatives. Par exemple, le `SimpleNested.master` page peut inclure un menu avec les options spécifiques à ses pages de contenu, tandis que `SimpleNestedAlternate.master` peut avoir des informations pertinentes pour les pages de contenu qui s’y lier.

Maintenant, supposons que nous devions apporter une modification à la mise en page du site principal. Par exemple, supposons que nous voulons ajouter une liste de liens communes à toutes les pages de contenu. Pour cela nous mettons à jour la page maître de niveau supérieur, `Simple.master`. Toutes les modifications sont immédiatement répercutées dans les pages maîtres imbriquées et, par extension, leurs pages de contenu.

Pour illustrer la facilité avec laquelle nous pouvons modifier la disposition du site principal, ouvrez le `Simple.master` page maître et ajoutez le balisage suivant entre les `topContent` et `mainContent` `<div>` éléments :


[!code-aspx[Main](nested-master-pages-vb/samples/sample7.aspx)]

Cela ajoute deux liens en haut de chaque page qui lie à `Simple.master`, `SimpleNested.master`, ou `SimpleNestedAlternate.master`; ces modifications s’appliquent immédiatement pour les pages maîtres imbriqués et leurs pages de contenu. La figure 8 illustre `Alternate.aspx` lors de l’affichage via un navigateur. Notez l’ajout de liens en haut de la page (par rapport à la Figure 7).


[![Modifié à la Page maître de niveau supérieur sont immédiatement répercutées dans son imbriqués Master Pages et leur contenu](nested-master-pages-vb/_static/image23.png)](nested-master-pages-vb/_static/image22.png)

**Figure 08**: modifié à la Page maître de niveau supérieur sont immédiatement répercutées dans son imbriqués Master Pages et leur contenu ([cliquez pour afficher l’image en taille réelle](nested-master-pages-vb/_static/image24.png))


## <a name="using-a-nested-master-page-for-the-administration-section"></a>À l’aide d’une Page maître imbriquée pour la Section Administration

À ce stade nous avoir examiné les avantages du maître imbriquée des pages et que vous avez appris à créer et les utiliser dans une application ASP.NET. Les exemples dans les étapes 1, 2 et 3, impliqués, toutefois, la création d’une nouvelle page maître de niveau supérieur, les nouvelles pages maîtres imbriquées et les nouvelles pages de contenu. Nous allons ajouter une nouvelle page maître imbriquée à un site Web avec une page maître niveau supérieur et les pages de contenu ?

Intégration d’une page maître imbriquée dans un site Web existant et associer des pages de contenu existants à celui-ci requièrent un peu plus d’efforts qu’à partir du début. Les étapes 4, 5, 6 et 7 Explorer ces défis, comme nous améliorer notre application de démonstration pour inclure une nouvelle page maître imbriquée nommée `AdminNested.master` qui contient des instructions pour l’administrateur et est utilisé par les pages ASP.NET dans le `~/Admin` dossier.

Intégration d’une page maître imbriquée dans notre application de démonstration présente les problèmes suivants :

- Le contenu existant des pages dans le `~/Admin` dossier avoir certaines attentes dans leur page maître. Pour commencer, ils attendent certains contrôles ContentPlaceHolder d’être présents. En outre, le `~/Admin/AddProduct.aspx` et `~/Admin/Products.aspx` pages appeler public de la page maître `RefreshRecentProductsGrid` (méthode), définir son `GridMessageText` propriété, ou avoir un gestionnaire d’événements pour son `PricesDoubled` événement. Par conséquent, notre page maître imbriquée doit fournir le mêmes ContentPlaceHolders et les membres publics.
- Dans le didacticiel précédent, nous avons amélioré la `BasePage` classe pour définir dynamiquement le `Page` l’objet `MasterPageFile` propriété basée sur une variable de Session. Comment pour nous prennent en charge des pages maîtres dynamiques lors de l’utilisation des pages maîtres imbriquées ?

Ces deux défis affichera la création de la page maître imbriquée et utiliser à partir de nos pages de contenu existants. Nous allons examiner et supprimer ces problèmes lorsqu’ils surviennent.

## <a name="step-4-creating-the-nested-master-page"></a>Étape 4 : Création de la Page maître imbriquée

Notre première tâche consiste à créer la page maître imbriquée à utiliser par les pages dans la section Administration. Comme nous l’avons vu à l’étape 2, lors de l’ajout d’un nouveau nested page maître, nous devons spécifier la page maître du parent de la page maître imbriquée. Mais nous avons deux pages maîtres de niveau supérieur : `Site.master` et `Alternate.master`. Souvenez-vous que nous avons créé `Alternate.master` dans le didacticiel précédent et écrit du code le `BasePage` cet ensemble de la classe le `Page` l’objet `MasterPageFile` propriété lors de l’exécution soit `Site.master` ou `Alternate.master` selon la valeur de la `MyMasterPage` Variable de session.

Comment nous configurer notre page maître imbriquée afin qu’il utilise la page maître de niveau supérieur approprié ? Nous avons deux options :

- Créez deux pages maîtres imbriquées, `AdminNestedSite.master` et `AdminNestedAlternate.master`et les lier à un niveau supérieur pages maîtres `Site.master` et `Alternate.master`, respectivement. Dans `BasePage`, ensuite, nous affectons la `Page` l’objet `MasterPageFile` à la page maître imbriquée appropriée.
- Créer une page maître imbriquée unique et ont les pages de contenu de cette page maître spécifique. Ensuite, lors de l’exécution, nous devez définir la page maître imbriquée `MasterPageFile` propriété à la page maître de niveau supérieur appropriée lors de l’exécution. (Comme vous pouvez avoir deviné, pages maîtres ont également un `MasterPageFile` propriété.)

Nous allons utiliser la deuxième option. Créer un fichier page maître imbriquée unique dans le `~/Admin` dossier nommé `AdminNested.master`. Étant donné que les deux `Site.master` et `Alternate.master` ont le même ensemble de contrôles ContentPlaceHolder, peu importe quelle page maître lier, bien que nous vous invitons à lier à `Site.master` par souci de cohérence.


[![Ajouter une Page maître imbriquée dans le dossier ~/Admin.](nested-master-pages-vb/_static/image26.png)](nested-master-pages-vb/_static/image25.png)

**Figure 09**: ajouter une Page maître imbriquée à la `~/Admin` dossier. ([Cliquez pour afficher l’image en taille réelle](nested-master-pages-vb/_static/image27.png))


Étant donné que la page maître imbriquée est liée à une page maître avec quatre contrôles ContentPlaceHolder, Visual Studio ajoute quatre contrôles au balisage d’initiale du nouveau fichier page maître imbriquée de contenu. Comme nous l’avons fait dans les étapes 2 et 3, ajoutez un contrôle ContentPlaceHolder dans chaque contrôle de contenu, en lui attribuant le même nom que le contrôle de ContentPlaceHolder de la page maître niveau supérieur. Également ajouter le balisage suivant pour le contrôle de contenu qui correspond à la `MainContent` ContentPlaceHolder :


[!code-html[Main](nested-master-pages-vb/samples/sample8.html)]

Ensuite, définissez la `instructions` de classe CSS dans les `Styles.css` et `AlternateStyles.css` fichiers CSS. Les règles CSS suivantes provoquent des éléments HTML mis en forme avec la `instructions` classe s’affiche avec une couleur d’arrière-plan jaune et une bordure noire unie :


[!code-css[Main](nested-master-pages-vb/samples/sample9.css)]

Étant donné que cette balise a été ajoutée à la page maître imbriquée, il apparaît uniquement dans les pages qui utilisent cette page maître imbriquée (à savoir, les pages dans la section Administration).

Après avoir apporté ces ajouts à votre page maître imbriquée, son balisage déclaratif doit ressembler à ce qui suit :


[!code-aspx[Main](nested-master-pages-vb/samples/sample10.aspx)]

Notez que chaque contrôle de contenu utilise un contrôle ContentPlaceHolder et que les contrôles ContentPlaceHolder `ID` propriétés reçoivent les mêmes valeurs que les contrôles ContentPlaceHolder correspondants dans la page maître de niveau supérieur. En outre, le balisage spécifique à la section Administration apparaît dans le `MainContent` ContentPlaceHolder.

La figure 10 illustre le `AdminNested.master` page maître imbriquée lorsqu’ils sont affichés dans le Concepteur de Visual Studio. Vous pouvez voir les instructions figurant dans la zone jaune en haut de la `MainContent` contrôle de contenu.


[![La Page maître imbriquée étend la Page maître de niveau supérieur pour inclure des Instructions pour l’administrateur.](nested-master-pages-vb/_static/image29.png)](nested-master-pages-vb/_static/image28.png)

**La figure 10**: la Page maître imbriquée étend la Page maître de niveau supérieur pour inclure des Instructions pour l’administrateur. ([Cliquez pour afficher l’image en taille réelle](nested-master-pages-vb/_static/image30.png))


## <a name="step-5-updating-the-existing-content-pages-to-use-the-new-nested-master-page"></a>Étape 5 : Mise à jour les Pages de contenu existants pour utiliser la nouvelle Page maître imbriquée

Chaque fois que nous ajoutons une nouvelle page de contenu à la section Administration nous devez la lier à la `AdminNested.master` page maître que nous venons de créer. Mais qu’en est-il existants pages de contenu ? Actuellement, toutes les pages de contenu dans le site dérivent la `BasePage` classe, qui définit par programme le contenu de page maître lors de l’exécution. Cela n’est pas le comportement souhaité pour les pages de contenu dans la section Administration. Au lieu de cela, nous souhaitons ces pages de contenu à toujours utiliser le `AdminNested.master` page. Il s’agit de la responsabilité de la page maître imbriquée pour choisir la page de contenu à droite de niveau supérieur lors de l’exécution.

À la meilleure façon d’obtenir ce souhaité consiste à créer une nouvelle classe de page de base personnalisée nommée `AdminBasePage` qui étend la `BasePage` classe. `AdminBasePage`peut passer outre le `SetMasterPageFile` et définir le `Page` l’objet `MasterPageFile` à la valeur codée en dur « ~ / Admin/AdminNested.master ». De cette façon, n’importe quelle page qui dérive de `AdminBasePage` utilisera `AdminNested.master`, alors que n’importe quelle page qui dérive de `BasePage` aura son `MasterPageFile` propriété la valeur dynamiquement » ~ / Site.master » ou « ~ / Alternate.master » selon la valeur de la `MyMasterPage` Variable de session.

Commencez par ajouter un nouveau fichier de classe pour le `App_Code` dossier nommé `AdminBasePage.vb`. Avoir `AdminBasePage` étendre `BasePage` puis substituer le `SetMasterPageFile` (méthode). Dans cette méthode affecter la `MasterPageFile` la valeur « ~ / Admin/AdminNested.master ». Après avoir apporté ces modifications à votre classe fichier doit ressembler à ce qui suit :


[!code-vb[Main](nested-master-pages-vb/samples/sample11.vb)]

Nous devons maintenant pour que les pages de contenu existants dans la section dériver à partir de l’Administration `AdminBasePage` au lieu de `BasePage`. Accédez au fichier de classe code-behind pour chaque page de contenu dans le `~/Admin` dossier et apporter cette modification. Par exemple, dans `~/Admin/Default.aspx` vous pouvez modifier la déclaration de classe code-behind à partir de :


[!code-vb[Main](nested-master-pages-vb/samples/sample12.vb)]

À :


[!code-vb[Main](nested-master-pages-vb/samples/sample13.vb)]

La figure 11 illustre comment la page maître de niveau supérieur (`Site.master` ou `Alternate.master`), la page maître imbriquée (`AdminNested.master`), et les pages de contenu de la section Administration sont liées les unes aux autres.


[![La Page maître imbriquée définit le contenu spécifique à des Pages dans la Section Administration](nested-master-pages-vb/_static/image32.png)](nested-master-pages-vb/_static/image31.png)

**Figure 11**: l’imbriqués Master Page définit contenu spécifique à des Pages dans la Section Administration ([cliquez pour afficher l’image en taille réelle](nested-master-pages-vb/_static/image33.png))


## <a name="step-6-mirroring-the-master-pages-public-methods-and-properties"></a>Étape 6 : Mise en miroir des méthodes publiques et les propriétés de la Page maître

N’oubliez pas que le `~/Admin/AddProduct.aspx` et `~/Admin/Products.aspx` pages interagissent par programme avec la page maître : `~/Admin/AddProduct.aspx` appelle la page maître de publique `RefreshRecentProductsGrid` (méthode) et définit ses `GridMessageText` propriété ; `~/Admin/Products.aspx` a un gestionnaire d’événements pour le `PricesDoubled` événement. Dans le didacticiel précédent, nous avons créé un `MustInherit` `BaseMasterPage` classe qui a défini ces membres publics.

Le `~/Admin/AddProduct.aspx` et `~/Admin/Products.aspx` pages supposent que leur page maître dérive la `BaseMasterPage` classe. Le `AdminNested.master` page, cependant, actuellement étend la `System.Web.UI.MasterPage` classe. Par conséquent, lors de la visite `~/Admin/Products.aspx` un `InvalidCastException` est levée avec le message : « impossible à l’objet de conversion de type ' ASP.admin\_adminnested\_master' en type 'BaseMasterPage'. »

Pour y remédier, nous devons disposer le `AdminNested.master` classe code-behind étendre `BaseMasterPage`. Mise à jour de la déclaration de classe code-behind de la page maître imbriquée à partir de :


[!code-vb[Main](nested-master-pages-vb/samples/sample14.vb)]

À :


[!code-vb[Main](nested-master-pages-vb/samples/sample15.vb)]

Nous n’avons pas encore terminé. Nous avons besoin de substituer les membres marqués comme `MustOverride`, à savoir `RefreshRecentProductsGrid` et `GridMessageText`. Ces membres sont utilisés par les pages maîtres de niveau supérieur pour mettre à jour leurs interfaces utilisateur. (En fait, seul le `Site.master` page maître utilise ces méthodes, bien que les deux pages maîtres de niveau supérieur implémentent ces méthodes, étant donné que les deux étendent `BaseMasterPage`.)

Pendant que nous avons besoin d’implémenter ces membres dans `AdminNested.master`, ces implémentations suffit est simplement appeler le même membre de la page maître de niveau supérieur utilisée par la page maître imbriquée. Par exemple, lorsqu’une page de contenu dans la section Administration appelle la page maître imbriquée `RefreshRecentProductsGrid` , tout ce que la page maître imbriquée doit faire est, à son tour, appelez `Site.master` ou `Alternate.master`de `RefreshRecentProductsGrid` (méthode).

Pour ce faire, démarrez en ajoutant le code suivant `@MasterType` directive au début du `AdminNested.master`:


[!code-aspx[Main](nested-master-pages-vb/samples/sample16.aspx)]

N’oubliez pas que le `@MasterType` directive ajoute une propriété fortement typée à la classe code-behind nommée `Master`. Substituer la `RefreshRecentProductsGrid` et `GridMessageText` membres et délègue simplement l’appel à la `Master`correspondante de méthode du :


[!code-vb[Main](nested-master-pages-vb/samples/sample17.vb)]

Avec ce code en place, vous devez être en mesure d’utiliser les pages de contenu dans la section Administration visiter. Figure 12 montre la `~/Admin/Products.aspx` page lorsqu’ils sont affichés via un navigateur. Comme vous pouvez le voir, cette page inclut la zone des Instructions d’Administration, qui est définie dans la page maître imbriquée.


[![Les Pages de contenu dans la Section Administration incluent des Instructions en haut de chaque Page](nested-master-pages-vb/_static/image35.png)](nested-master-pages-vb/_static/image34.png)

**Figure 12**: les Pages de contenu dans la Section Administration incluent des Instructions sur le haut de chaque Page ([cliquez pour afficher l’image en taille réelle](nested-master-pages-vb/_static/image36.png))


## <a name="step-7-using-the-appropriate-top-level-master-page-at-runtime"></a>Étape 7 : À l’aide de la Page principale de niveau supérieur appropriée lors de l’exécution

Bien que toutes les pages de contenu dans la section Administration soient entièrement fonctionnelles, ils utilisent tous la même page maître de niveau supérieur et ignorer la page maître sélectionnée par l’utilisateur à `ChooseMasterPage.aspx`. Ce comportement est dû au fait que la page maître imbriquée a son `MasterPageFile` propriété statiquement la valeur `Site.master` dans son `<%@ Master %>` directive.

Pour utiliser la page maître de niveau supérieur sélectionnée par l’utilisateur final, nous devons définir le `AdminNested.master`de `MasterPageFile` valeur à la propriété dans le `MyMasterPage` variable de Session. Étant donné que nous avons défini les pages de contenu `MasterPageFile` propriétés dans `BasePage`, vous pouvez penser que nous affectons la page maître imbriquée `MasterPageFile` propriété dans `BaseMasterPage` ou dans le `AdminNested.master`de classe code-behind. Cela ne fonctionne pas, toutefois, étant donné que nous avons besoin d’avoir le `MasterPageFile` propriété à la fin de la phase PreInit. L’heure la plus tôt que nous pouvons exploiter par programme le cycle de vie de page à partir d’une page maître agit de la phase d’initialisation (ce qui se produit après l’étape PreInit).

Par conséquent, nous devons définir la page maître imbriquée `MasterPageFile` propriété à partir des pages de contenu. Seul le contenu des pages qui utilisent le `AdminNested.master` dérivent de la page maître `AdminBasePage`. Par conséquent, nous pouvons y placées cette logique. À l’étape 5 nous a substitué la `SetMasterPageFile` méthode, en définissant l’objet de la Page `MasterPageFile` propriété » ~ / Admin/AdminNested.master ». Mise à jour `SetMasterPageFile` à définir également la page maître `MasterPageFile` propriété le résultat enregistré dans la Session :


[!code-vb[Main](nested-master-pages-vb/samples/sample18.vb)]

Le `GetMasterPageFileFromSession` (méthode), ce qui nous avons ajouté à la `BasePage` classe dans le didacticiel précédent, retourne le chemin d’accès du fichier de page maître approprié en fonction de la valeur de variable de Session.

Cette modification en place, sélection de page maître de l’utilisateur reprend la section Administration. Figure 13 illustre la même page que Figure 12, mais une fois que l’utilisateur a changé sa sélection de la page maître à `Alternate.master`.


[![La Page d’Administration imbriquée utilise la Page maître de niveau supérieur sélectionné par l’utilisateur](nested-master-pages-vb/_static/image38.png)](nested-master-pages-vb/_static/image37.png)

**Figure 13**: la Page d’Administration Nested utilise la Page de principale de niveau supérieur sélectionné par l’utilisateur ([cliquez pour afficher l’image en taille réelle](nested-master-pages-vb/_static/image39.png))


## <a name="summary"></a>Résumé

Quantité pages Comment contenu like peuvent lier à une page maître, il est possible de créer une prédiction imbriquée des pages maîtres par une page maître enfant se lient à une page maître parent. La page maître enfant peut définir des contrôles de contenu pour chacun des ContentPlaceHolders de son parent ; Il peut ensuite ajouter ses propres contrôles ContentPlaceHolder (ainsi que d’autres balises) à ces contrôles de contenu. Les pages maîtres imbriquées sont très utiles dans les applications web de grande taille où toutes les pages partagent une apparence principale, mais certaines sections du site qui nécessitera unique.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Pages maîtres ASP.NET imbriquées](https://msdn.microsoft.com/en-us/library/x2b3ktt7.aspx)
- [Conseils pour les Pages maîtres imbriquées et de conception de Visual Studio 2005](https://weblogs.asp.net/scottgu/archive/2005/11/11/430382.aspx)
- [Visual Studio 2008 imbriqués prise en charge de la Page maître](https://weblogs.asp.net/scottgu/archive/2007/07/09/vs-2008-nested-master-page-support.aspx)

### <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de plusieurs manuels ASP/ASP.NET et de créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 3.5 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672329972/4guysfromrollaco). Scott peut être atteint à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4GuysFromRolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à[mitchell@4GuysFromRolla.com](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](specifying-the-master-page-programmatically-vb.md)
