---
uid: web-forms/overview/older-versions-getting-started/master-pages/creating-a-site-wide-layout-using-master-pages-vb
title: "Création d’une disposition à l’échelle du Site à l’aide de gabarits (VB) | Documents Microsoft"
author: rick-anderson
description: "Ce didacticiel vous présente les notions de base de page maître. Quelles sont les pages maîtres, à savoir comment fait un créer une page maître, quelles sont les espaces réservés contenu, en quoi un cr..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/21/2008
ms.topic: article
ms.assetid: 30945276-8ed9-4b27-8e50-4309244d3559
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/master-pages/creating-a-site-wide-layout-using-master-pages-vb
msc.type: authoredcontent
ms.openlocfilehash: 69f73085198c79c01988aab9e63f3ce9e7647034
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-a-site-wide-layout-using-master-pages-vb"></a>Création d’une disposition à l’échelle du Site à l’aide de gabarits (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/e/e/f/eef369f5-743a-4a52-908f-b6532c4ce0a4/ASPNET_MasterPages_Tutorial_01_VB.zip) ou [télécharger le PDF](http://download.microsoft.com/download/8/f/6/8f6349e4-6554-405a-bcd7-9b094ba5089a/ASPNET_MasterPages_Tutorial_01_VB.pdf)

> Ce didacticiel vous présente les notions de base de page maître. À savoir quelles sont les pages maîtres, comment une crée une page maître, quelles sont les espaces réservés de contenu, comment une crée une page ASP.NET qui utilise une page maître, comment modifier la page maître est automatiquement répercutée dans ses pages de contenu associées et ainsi de suite.


## <a name="introduction"></a>Introduction

Un attribut d’un site Web bien conçu est une disposition cohérente des pages au niveau du site. Prenez le site Web www.asp.net, par exemple. Au moment de la rédaction, chaque page possède le même contenu en haut et bas de la page. Comme le montre la Figure 1, tout en haut de chaque page affiche une barre grise avec une liste de Communities Microsoft. Sous qui est le logo du site, la liste des langues dans lesquelles le site a été traduit et les sections principales : accueil, prise en main, en savoir plus, des téléchargements et ainsi de suite. De même, le bas de la page inclut des informations sur la publicité sur www.asp.net, une déclaration de copyright et un lien vers la déclaration de confidentialité.


[![Le site Web www.asp.net utilise une apparence et convivialité cohérentes dans toutes les Pages](creating-a-site-wide-layout-using-master-pages-vb/_static/image2.png)](creating-a-site-wide-layout-using-master-pages-vb/_static/image1.png)

**Figure 01**: le site Web www.asp.net emploie un rechercher cohérent et l’apparence sur toutes les Pages ([cliquez pour afficher l’image en taille réelle](creating-a-site-wide-layout-using-master-pages-vb/_static/image3.png))


Un autre attribut d’un site bien conçu est la facilité avec laquelle l’apparence du site peut être modifiée. La figure 1 montre la page d’accueil www.asp.net à partir de mars 2008, mais entre maintenant et publication de ce didacticiel, l’apparence a peut-être été modifié. Par exemple, les éléments de menu en haut seront développe pour inclure une nouvelle section pour l’infrastructure MVC. Ou peut-être une radicalement nouvelle conception avec différentes couleurs, polices et la disposition sera résolue. Appliquer ces modifications à l’ensemble du site doit être un processus rapide et simple qui ne requiert pas de modification des milliers de pages web qui composent le site.

Création d’un modèle de page de l’échelle du site dans ASP.NET est possible via l’utilisation de *pages maîtres*. En bref, une page maître est un type spécial de page ASP.NET qui définit le balisage qui sont commun à tous les *pages de contenu* , ainsi que des zones personnalisables sur une base de la page de contenu page en fonction du contenu. (Une page de contenu est une page ASP.NET qui est liée à la page maître.) Chaque fois que la disposition d’une page maître ou la mise en forme est modifiée, toutes en sortie ses pages de contenu est de même immédiatement mis à jour, ce qui permet d’appliquer les modifications au niveau du site apparence aussi simples que la mise à jour et de déploiement d’un seul fichier (à savoir, la page maître).

Il s’agit du premier didacticiel dans une série de didacticiels qui explorent les pages maîtres. Au cours de cette série de didacticiels nous :

- Examinez la création de pages maîtres et leurs pages de contenu associées,
- Traite de plusieurs conseils, astuces et pièges
- Identifier les pièges de la page maître et Explorer les solutions de contournement
- Découvrez comment accéder à la page maître à partir d’une page de contenu et a-inversement,
- Découvrez comment spécifier un contenu de page maître lors de l’exécution, et
- Autres avancée de rubriques de la page maître.

Ces didacticiels sont destinées à être concis et fournissent des instructions détaillées avec beaucoup de captures d’écran pour vous guider dans le processus visuellement. Chaque didacticiel est disponible dans les versions de Visual Basic et c# et inclut un téléchargement de l’intégralité du code utilisé.

Ce didacticiel première commence par un examen des concepts de base de page maître. Nous décrivent le fonctionnement des pages maître, création d’une page maître et des pages de contenu associées à l’aide de Visual Web Developer et voir comment les modifications apportées à une page maître sont immédiatement répercutées dans ses pages de contenu. C’est parti !

## <a name="understanding-how-master-pages-work"></a>Comprendre le fonctionnement des Pages maître

Création d’un site Web avec une disposition cohérente des pages au niveau du site requiert que chaque page web émettre un balisage de mise en forme commune en plus de son contenu personnalisé. Par exemple, chaque publication didacticiel ou forum sur www.asp.net ont leur propre contenu unique, chacune de ces pages également rendre une série de commun `<div>` éléments qui affichent les liens de la section de niveau supérieur : accueil, prise en main, en savoir plus et ainsi de suite.

Il existe de nombreuses techniques pour créer des pages web avec une apparence et convivialité cohérentes. Une approche naïve consiste à simplement copier et coller le balisage de mise en forme commune dans toutes les pages web, mais cette approche présente plusieurs inconvénients. Pour commencer, chaque fois qu’une nouvelle page est créée, vous devez vous rappeler copier et coller le contenu partagé dans la page. Ce type copier- coller les opérations sont pour l’erreur que vous pouvez accidentellement copier uniquement un sous-ensemble de la balise partagée dans une nouvelle page. Et pour conclure, cette approche permet de remplacer l’apparence du site existant avec un autre une véritable faibles, car chaque page dans le site doit être modifiée pour pouvoir utiliser la nouvelle apparence.

Avant la version 2.0 d’ASP.NET, les développeurs souvent placés balisage courantes dans la page [contrôles utilisateur](https://msdn.microsoft.com/en-us/library/y6wb1a0e.aspx) puis à ajouter ces contrôles utilisateur à chaque page. Cette approche requis que le développeur de pages n’oubliez pas d’ajouter manuellement les contrôles utilisateur à chaque nouvelle page, mais autorisée pour les modifications à l’échelle du site plus faciles, car lors de la mise à jour de la balise courantes que les contrôles utilisateur doivent être modifiées. Malheureusement, Visual Studio .NET 2002 et 2003 - les versions de Visual Studio permet de créer des applications ASP.NET 1.x - rendues des contrôles utilisateur dans la vue de conception sous forme de zones grises. Par conséquent, les développeurs de page à l’aide de cette approche bénéficient pas d’un environnement au moment du design WYSIWYG.

Les points faibles de l’utilisation de contrôles utilisateur ont été traités dans la version 2.0 d’ASP.NET et Visual Studio 2005 avec l’introduction de *pages maîtres*. Une page maître est un type spécial de page ASP.NET qui définit les deux le balisage au niveau du site et le *régions* où associés *pages de contenu* définir leur balisage personnalisée. Comme nous allons le voir à l’étape 1, ces régions sont définies par les contrôles ContentPlaceHolder. Le contrôle ContentPlaceHolder indique simplement une position dans la hiérarchie des contrôles de la page maître où le contenu personnalisé peut être ajoutée à une page de contenu.

> [!NOTE]
> Les principaux concepts et fonctionnalités de pages maîtres n’a pas changé depuis la version 2.0 d’ASP.NET. Toutefois, Visual Studio 2008 offre au moment du design prise en charge pour les pages maîtres imbriquées, une fonctionnalité qui a été manque dans Visual Studio 2005. Nous allons nous intéresser à l’aide de pages maîtres imbriquées dans un didacticiel futures.


Figure 2 montre à quoi peut ressembler la page maître pour www.asp.net. Notez que la page maître définit la disposition à l’échelle du site common - le balisage en haut, bas et à droite de chaque page - ainsi que d’un espace réservé en milieu à gauche, où se trouve le contenu unique pour chaque page web.


![Une Page maître définit la disposition à l’échelle du Site et les zones modifiables sur une base de Page contenu Page en fonction du contenu](creating-a-site-wide-layout-using-master-pages-vb/_static/image4.png)

**Figure 02**: une Page maître définit la disposition à l’échelle du Site et les zones modifiables sur une base de Page contenu Page en fonction du contenu


Une fois qu’une page maître a été définie, il peut être lié aux nouvelles pages ASP.NET via les graduations d’une case à cocher. Ces pages ASP.NET - appelées pages de contenu - incluent un contrôle de contenu pour chacun des contrôles de ContentPlaceHolder de la page maître. Lorsque la page de contenu est visitée via un navigateur le moteur ASP.NET crée la hiérarchie des contrôles de la page maître et injecte hiérarchie de contrôle de la page de contenu dans les emplacements appropriés. Cette hiérarchie de contrôle combinée est rendue et le code HTML résultant est renvoyé au navigateur de l’utilisateur final. Par conséquent, la page de contenu émet le balisage commun définies dans sa page maître en dehors des contrôles ContentPlaceHolder et le balisage de page spécifique défini au sein de ses propres contrôles de contenu. La figure 3 illustre ce concept.


[![Fusion de balisage de la Page demandée dans la Page maître](creating-a-site-wide-layout-using-master-pages-vb/_static/image6.png)](creating-a-site-wide-layout-using-master-pages-vb/_static/image5.png)

**Figure 03**: fusion de balisage de la Page demandée dans la Page maître ([cliquez pour afficher l’image en taille réelle](creating-a-site-wide-layout-using-master-pages-vb/_static/image7.png))


Maintenant que nous l’avons vu comment des pages maîtres, examinons une création d’une page maître et des pages de contenu associées à l’aide de Visual Web Developer.

> [!NOTE]
> Pour atteindre l’audience la plus large possible, le site Web ASP.NET que nous créons tout au long de cette série de didacticiels sera créé à l’aide d’ASP.NET 3.5 avec la version gratuite de Microsoft Visual Studio 2008, [Visual Web Developer 2008](https://www.microsoft.com/express/vwd/). Si vous n’avez pas encore mis à niveau vers ASP.NET 3.5, ne vous inquiétez pas - les concepts abordés dans ces didacticiels de travail indifféremment avec ASP.NET 2.0 et Visual Studio 2005. Toutefois, certaines applications de démonstration peuvent utiliser les nouvelles fonctionnalités introduites dans .NET Framework version 3.5 ; Lorsque les fonctionnalités spécifiques à 3.5 sont utilisées, inclure une remarque qui explique comment implémenter une fonctionnalité similaire dans la version 2.0. Gardez à l’esprit que les applications de démonstration disponibles pour téléchargement à partir de chaque didacticiel cible le .NET Framework version 3.5, ce qui entraîne un `Web.config` fichier qui inclut les éléments de configuration spécifiques à 3.5. En résumé, si vous devez encore installer .NET 3.5 sur votre ordinateur, puis l’application web téléchargeable ne fonctionnera pas sans supprimer au préalable le balisage spécifique 3.5 à partir de `Web.config`. Consultez [ayant été Disséqué ASP.NET Version 3.5 `Web.config` fichier](http://www.4guysfromrolla.com/articles/121207-1.aspx) pour plus d’informations sur cette rubrique.


## <a name="step-1-creating-a-master-page"></a>Étape 1 : Création d’une Page maître

Nous pouvons découvrir la création et l’utilisation des pages maîtres et de contenu, nous devons tout d’abord un site Web ASP.NET. Commencez par créer un nouveau fichier basé sur le système site Web ASP.NET. Pour ce faire, lancez Visual Web Developer et dans le menu fichier et cliquez sur Nouveau Site Web, affichage de la boîte de dialogue Nouveau Site Web boîte (voir Figure 4). Choisissez le modèle de Site Web ASP.NET, la valeur de la liste déroulante emplacement système de fichiers, choisissez un dossier pour placer le site web et définir le langage Visual Basic. Cela crée un nouveau site web avec un `Default.aspx` page ASP.NET, une `App_Data` dossier et un `Web.config` fichier.

> [!NOTE]
> Visual Studio prend en charge deux modes de gestion de projet : projets de Site Web et les projets d’Application Web. Projets de Site Web n’ont pas un fichier projet, alors que les projets d’Application Web simulent l’architecture de projet dans Visual Studio .NET 2002/2003 - ils incluent un fichier projet et compiler le code de source du projet dans un assembly unique, qui est placé dans le `/bin` dossier. Visual Studio 2005 seulement Site Web pris en charge les projets, bien que le modèle de projet d’Application Web a été réintroduit avec Service Pack 1 ; Visual Studio 2008 propose les deux modèles de projet. Visual Web Developer 2005 et 2008 éditions, toutefois, ne prennent en charge les projets de Site Web. Utiliser le modèle de projet de Site Web pour mon démonstrations de cette série de didacticiels. Si vous utilisez une édition Express non et que vous souhaitez utiliser le [modèle de projet d’Application Web](https://msdn.microsoft.com/en-us/library/aa730880(vs.80).aspx) au lieu de cela, n’hésitez pas à effectuer cette opération, mais gardez à l’esprit qu’il peut y avoir des différences entre ce que vous voyez sur votre écran et les étapes à effectuer par rapport à la captures d’écran indiqués et les instructions fournies dans ces didacticiels.


[![Créer un Site Web de système de nouveaux fichiers](creating-a-site-wide-layout-using-master-pages-vb/_static/image9.png)](creating-a-site-wide-layout-using-master-pages-vb/_static/image8.png)

**Figure 04**: créer un Site Web de New File System-Based ([cliquez pour afficher l’image en taille réelle](creating-a-site-wide-layout-using-master-pages-vb/_static/image10.png))


Ensuite, ajoutez une page maître pour le site dans le répertoire racine en cliquant sur le nom du projet, en choisissant Ajouter un nouvel élément, en sélectionnant le modèle de Page maître. Notez que les pages maîtres se terminent par l’extension `.master`. Nommez cette nouvelle page maître `Site.master` et cliquez sur Ajouter.


[![Ajouter une Page maître nommée Site.master pour le site Web](creating-a-site-wide-layout-using-master-pages-vb/_static/image12.png)](creating-a-site-wide-layout-using-master-pages-vb/_static/image11.png)

**Figure 05**: ajouter un nommé de la Page maître `Site.master` au site Web ([cliquez pour afficher l’image en taille réelle](creating-a-site-wide-layout-using-master-pages-vb/_static/image13.png))


L’ajout d’un nouveau fichier de page maître via Visual Web Developer de crée une page maître avec le balisage déclaratif suivant :

[!code-aspx[Main](creating-a-site-wide-layout-using-master-pages-vb/samples/sample1.aspx)]

La première ligne dans le balisage déclaratif est la [ `@Master` directive](https://msdn.microsoft.com/en-us/library/ms228176.aspx). Le `@Master` la directive est similaire à la [ `@Page` directive](https://msdn.microsoft.com/en-us/library/ydy4x04a.aspx) qui s’affiche dans les pages ASP.NET. Il définit la langue de côté serveur (VB) et le plus d’informations sur l’emplacement et l’héritage de classe code-behind de la page maître.

Le `DOCTYPE` et le balisage déclaratif de la page s’affiche sous le `@Master` la directive. Cette page inclut HTML statique avec quatre contrôles côté serveur :

- **Un formulaire Web (le `<form runat="server">`)** - parce que toutes les pages ASP.NET généralement un formulaire Web - et, car la page maître peut inclure des contrôles Web qui doivent apparaître dans un formulaire Web - veillez à ajouter le formulaire Web de votre page maître (plutôt que d’ajouter un formulaire Web e page contenu Cci).
- **Un contrôle ContentPlaceHolder nommé `ContentPlaceHolder1`**  -ce contrôle ContentPlaceHolder apparaît dans le formulaire Web et sert à la région de l’interface utilisateur de la page de contenu.
- **Un côté serveur `<head>` élément** - le `<head>` élément a le `runat="server"` attribut, en rendant accessible via le code côté serveur. Le `<head>` élément est implémenté de cette façon afin que titre de la page et autres `<head>`-liées balisage peut être ajouté ou ajusté par programmation. Par exemple, la définition d’une page ASP.NET `Title` modifications apportées aux propriétés du `<title>` élément restitué par le `<head>` contrôle serveur.
- **Un contrôle ContentPlaceHolder nommé `head`**  -ce contrôle ContentPlaceHolder apparaît dans le `<head>` serveur de contrôle et peut être utilisé pour ajouter de façon déclarative le contenu à la `<head>` élément.

Ce balisage déclaratif de page maître par défaut sert de point de départ pour concevoir vos propres pages maîtres. N’hésitez pas à modifier le code HTML ou d’ajouter des contrôles Web supplémentaires ou ContentPlaceHolders à la page maître.

> [!NOTE]
> Lors de la conception d’une page maître vous assurer que la page maître contient un formulaire Web et au moins un contrôle ContentPlaceHolder s’affiche dans ce Web Form.


### <a name="creating-a-simple-site-layout"></a>Création d’une disposition de Site Simple

Examinons `Site.master`du balisage déclaratif par défaut pour créer une disposition de site où toutes les pages partagent : un en-tête commun ; une colonne de gauche avec la navigation, actualités et autre contenu à l’échelle du site ; et un pied de page qui affiche l’icône « Alimenté par Microsoft ASP.NET ». La figure 6 illustre le résultat final de la page maître lorsqu’une de ses pages de contenu est affichée via un navigateur. La région CERCLÉE rouge dans la Figure 6 est spécifique à la page qui est visitée (`Default.aspx`) ; le reste du contenu est définie dans la page maître et par conséquent cohérent dans toutes les pages de contenu.


[![La Page maître définit le balisage pour le haut, gauche et des parties de bas](creating-a-site-wide-layout-using-master-pages-vb/_static/image15.png)](creating-a-site-wide-layout-using-master-pages-vb/_static/image14.png)

**Figure 06**: la Page maître définit le balisage de gauche, haut et bas parties ([cliquez pour afficher l’image en taille réelle](creating-a-site-wide-layout-using-master-pages-vb/_static/image16.png))


Pour obtenir la mise en page de site indiqué dans la Figure 6, commencez par mettre à jour le `Site.master` page maître pour qu’il contienne le balisage déclaratif suivant :

[!code-aspx[Main](creating-a-site-wide-layout-using-master-pages-vb/samples/sample2.aspx)]

Mise en page de la page maître est défini à l’aide d’une série de `<div>` éléments HTML. Le `topContent` `<div>` contient le balisage qui apparaît en haut de chaque page, tandis que la `mainContent`, `leftContent`, et `footerContent` `<div>` s sont utilisés pour afficher le contenu de la page, la colonne de gauche et le « alimenté par Microsoft Icône d’ASP.NET », respectivement. Outre l’ajout de ces `<div>` éléments, j’ai également renommé le `ID` propriété du contrôle ContentPlaceHolder principal à partir de `ContentPlaceHolder1` à `MainContent`.

Les règles de mise en forme et la disposition de ces assortis `<div>` éléments est indiquée dans le [feuille de style en cascade (CSS)](http://en.wikipedia.org/wiki/Cascading_Style_Sheets) fichier `Styles.css`, qui est spécifiée un `<link>` élément dans la page maître `<head>`élément. Ces différentes règles définissent l’apparence de chaque `<div>` élément indiqués ci-dessus. Par exemple, le `topContent` `<div>` élément, qui affiche le texte de « Didacticiels de Pages maître » et le lien, a ses règles de mise en forme spécifiées dans `Styles.css` comme suit :

[!code-css[Main](creating-a-site-wide-layout-using-master-pages-vb/samples/sample3.css)]

Si vous suivez le long de votre ordinateur, vous devez télécharger le code d’accompagnement de ce didacticiel et ajouter la `Styles.css` fichier à votre projet. De même, vous devez également créer un dossier nommé `Images` et copiez l’icône « Alimenté par Microsoft ASP.NET » depuis le site Web de démonstration téléchargé à votre projet.

> [!NOTE]
> En savoir plus sur le code CSS et la mise en forme de la page web est dépasse le cadre de cet article. Pour plus d’informations sur CSS, passez en revue les [CSS didacticiels](http://www.w3schools.com/css/default.asp) à [W3Schools.com](http://www.w3schools.com/). J’ai également vous encourage à télécharger le code d’accompagnement de ce didacticiel et manipuler les paramètres de CSS dans `Styles.css` pour voir les effets de différentes règles de mise en forme.


### <a name="creating-a-master-page-using-an-existing-design-template"></a>Création d’une Page maître à l’aide d’un modèle existant

Au fil des années, j’ai créé un nombre d’applications web ASP.NET pour les entreprises de petite à moyenne taille. Certains de mes clients avaient une disposition de site existant, qu'ils voulaient utiliser ; d’autres a embauché un concepteur graphique compétente. Quelques confiée me pour concevoir la disposition du site Web. Comme indiqué par la Figure 6, des tâches de programmation pour concevoir la mise en page d’un site Web sont généralement judicieux que qu’effectuer open-heart surgery Contrairement à votre médecin vos impôts votre comptable.

Heureusement, il existe des sites Web innumerous qui proposent des modèles de conception HTML libres - Google a renvoyé les résultats de plus de six millions pour le terme de recherche « modèles de site Web gratuit ». Un de mes préférés est [OpenDesigns.org](http://opendesigns.org/). Une fois que vous trouvez un modèle de site Web que vous le souhaitez, ajoutez les fichiers CSS et les images à votre projet de site Web et intégrer HTML du modèle de votre page maître.

> [!NOTE]
> Microsoft propose également un certain nombre de [libre ASP.NET démarrer Kit modèles](https://msdn.microsoft.com/en-us/asp.net/aa336613.aspx) qui s’intègrent dans la boîte de dialogue Nouveau Site Web dans Visual Studio.


## <a name="step-2-creating-associated-content-pages"></a>Étape 2 : Création de Pages de contenu associées

La page maître créée, nous sommes prêts à commencer à créer des pages ASP.NET qui sont liés à la page maître. Ces pages sont appelés *pages de contenu*.

Nous allons ajouter une nouvelle page ASP.NET au projet et le lier à la `Site.master` page maître. Avec le bouton droit sur le nom du projet dans l’Explorateur de solutions et choisissez l’option Ajouter un nouvel élément. Sélectionnez le modèle de formulaire Web, entrez le nom `About.aspx`, puis cochez la case à cocher « Page maître sélectionnez » comme indiqué dans la Figure 7. Cela affiche l’instruction Select une boîte de dialogue de Page maître zone (voir la Figure 8) où vous pouvez choisir la page maître à utiliser.

> [!NOTE]
> Si vous avez créé votre site Web ASP.NET à l’aide du modèle de projet d’Application Web au lieu du modèle de projet de Site Web, vous ne verrez pas la case à cocher « Sélectionnez la page maître » dans la boîte de dialogue Ajouter un nouvel élément indiquée dans la Figure 7. Pour créer un contenu de page lorsque le projet d’Application Web à l’aide de modèle que vous devez choisir le modèle de formulaire de contenu Web au lieu du modèle de formulaire Web. Après la sélection du modèle de formulaire de contenu Web et en cliquant sur Ajouter, le même sélectionner une Page maître indiqué dans la Figure 8 de boîte de dialogue s’affiche.


[![Ajouter une nouvelle Page de contenu](creating-a-site-wide-layout-using-master-pages-vb/_static/image18.png)](creating-a-site-wide-layout-using-master-pages-vb/_static/image17.png)

**Figure 07**: ajouter une nouvelle Page de contenu ([cliquez pour afficher l’image en taille réelle](creating-a-site-wide-layout-using-master-pages-vb/_static/image19.png))


[![Sélectionnez la Page Site.master principale](creating-a-site-wide-layout-using-master-pages-vb/_static/image21.png)](creating-a-site-wide-layout-using-master-pages-vb/_static/image20.png)

**Figure 08**: sélectionnez le `Site.master` Page maître ([cliquez pour afficher l’image en taille réelle](creating-a-site-wide-layout-using-master-pages-vb/_static/image22.png))


Comme le montre le balisage déclaratif suivant, une nouvelle page de contenu contient un `@Page` directive que pointe en retour vers son masque de page et un contrôle de contenu pour chacun des contrôles de ContentPlaceHolder de la page maître.

[!code-aspx[Main](creating-a-site-wide-layout-using-master-pages-vb/samples/sample4.aspx)]

> [!NOTE]
> Dans la section « Création d’un Simple Site disposition » à l’étape 1, j’ai renommé `ContentPlaceHolder1` à `MainContent`. Si vous n’avez pas renommé de ce contrôle ContentPlaceHolder `ID` de la même façon, balisage déclaratif de votre page de contenu est légèrement différentes à partir du balisage indiqué ci-dessus. À savoir, le deuxième contrôle de contenu `ContentPlaceHolderID` reflète le `ID` du ContentPlaceHolder correspondant de contrôle dans votre page maître.


Lors du rendu d’une page de contenu, le moteur ASP.NET doit fusible de la page de contenu des contrôles avec des contrôles de ContentPlaceHolder de sa page maître. Le moteur ASP.NET détermine le contenu de page maître à partir de la `@Page` de la directive `MasterPageFile` attribut. Comme le montre le balisage ci-dessus, cette page de contenu est liée à `~/Site.master`.

Étant donné que la page maître a deux contrôles ContentPlaceHolder - `head` et `MainContent` -Visual Web Developer générés deux contrôles de contenu. Chaque contrôle de contenu fait référence à un ContentPlaceHolder particulière via son `ContentPlaceHolderID` propriété.

Où les pages maîtres éclairer sur les techniques de site à l’échelle du modèle précédent est la prise en charge au moment du design. La figure 9 illustre la `About.aspx` page de contenu lorsqu’ils sont affichés via la vue de conception de Visual Web Developer. Notez que lorsque le contenu de la page maître est visible, il est grisé et ne peut pas être modifié. Les contrôles de contenu correspondant à ContentPlaceHolders la page maître existe, toutefois, modifiable. Et tout comme avec n’importe quelle autre page ASP.NET, vous pouvez créer l’interface de la page de contenu en ajoutant des contrôles Web via les vues de Source ou de la conception.


[![Mode de création de la Page contenu affiche à la fois le contenu de Page maître et spécifiques à la Page](creating-a-site-wide-layout-using-master-pages-vb/_static/image24.png)](creating-a-site-wide-layout-using-master-pages-vb/_static/image23.png)

**Figure 09**: le contenu de la Page conception vue affiche à la fois la Page spécifique et contenu de la Page maître ([cliquez pour afficher l’image en taille réelle](creating-a-site-wide-layout-using-master-pages-vb/_static/image25.png))


### <a name="adding-markup-and-web-controls-to-the-content-page"></a>Ajout de contrôles Web et balisage à la Page de contenu

Prenez un moment pour créer du contenu pour le `About.aspx` page. Comme vous pouvez voir dans la Figure 10, que j’ai entré un titre « À propos de l’auteur » et les deux paragraphes de texte, mais vous pouvez ajouter des contrôles Web, trop. Après la création de cette interface, visitez le `About.aspx` page via un navigateur.


[![Visitez la Page About.aspx via un navigateur](creating-a-site-wide-layout-using-master-pages-vb/_static/image27.png)](creating-a-site-wide-layout-using-master-pages-vb/_static/image26.png)

**La figure 10**: visitez le `About.aspx` Page via un navigateur ([cliquez pour afficher l’image en taille réelle](creating-a-site-wide-layout-using-master-pages-vb/_static/image28.png))


Il est important de comprendre que la page de contenu demandée et sa page maître associée sont fusionnés et restitués sous la forme d’un entier entièrement sur le serveur web. Navigateur de l’utilisateur final est ensuite envoyé le HTML qui en résulte, fusionné. Pour le vérifier, afficher le code HTML reçu par le navigateur en allant dans le menu Affichage, en choisissant de Source. Notez qu’il n’y a aucune image ou des autres techniques spécialisées pour l’affichage de deux pages web différentes dans une fenêtre unique.

### <a name="binding-a-master-page-to-an-existing-aspnet-page"></a>Liaison d’une Page maître à une Page ASP.NET existante

Comme nous l’avons vu dans cette étape, l’ajout de qu'une nouvelle page de contenu pour une application web ASP.NET est aussi simple que la vérification de la case à cocher « Sélectionnez la page maître » et la sélection de la page maître. Malheureusement, il n’est pas aussi simple de conversion d’une page ASP.NET existante vers une page maître.

Pour lier une page maître à une page ASP.NET existante, vous devez effectuer les étapes suivantes :

1. Ajouter le `MasterPageFile` l’attribut de la page ASP.NET `@Page` directive, qu’il pointe vers la page maître appropriée.
2. Ajouter des contrôles de contenu pour chacun des ContentPlaceHolders dans la page maître.
3. Sélectivement coupez et collez le contenu existant de la page ASP.NET dans les contrôles de contenu appropriés. Dire « sélectivement » ici, car ASP.NET page probablement contient le balisage qui est déjà exprimé par la page maître, par exemple le `DOCTYPE`, le `<html>` élément et le formulaire Web.

Pour obtenir des instructions détaillées sur ce processus, ainsi que des captures d’écran, consultez [Scott Guthrie](https://weblogs.asp.net/scottgu/)de [à l’aide des Pages maîtres et Navigation du Site](http://webproject.scottgu.com/VisualBasic/MasterPages/MasterPages.aspx) didacticiel. La « mise à jour `Default.aspx` et `DataSample.aspx` pour utiliser la Page maître « section décrit en détail ces étapes.

Comme il est beaucoup plus facile de créer de nouvelles pages de contenu plutôt qu’à convertir des pages ASP.NET existantes dans les pages de contenu, je vous recommande que chaque fois que vous créez un site Web ASP.NET ajouter une page maître au site. Lier toutes les nouvelles pages ASP.NET à cette page maître. Ne vous inquiétez pas si la page maître initiale est très simple ou normal ; Vous pouvez mettre à jour ultérieurement la page maître.

> [!NOTE]
> Lorsque vous créez une application ASP.NET, Visual Web Developer ajoute un `Default.aspx` page qui n’est pas lié à une page maître. Si vous souhaitez des exercices pratiques de la conversion d’une page ASP.NET existante en une page de contenu, continuez et faire avec `Default.aspx`. Vous pouvez également supprimer `Default.aspx` , puis ajoutez de nouveau, mais cette fois la vérification de la case à cocher « Sélectionnez page maître ».


## <a name="step-3-updating-the-master-pages-markup"></a>Étape 3 : Mise à jour de balisage de la Page maître

Un des principaux avantages des pages maîtres est qu’une seule page maître peut être utilisée pour définir la disposition générale de nombreuses pages sur le site. Par conséquent, l’apparence du site de mise à jour nécessite la mise à jour d’un seul fichier - la page maître.

Pour illustrer ce problème, nous allons mettre à jour notre page maître pour inclure la date actuelle au haut de la colonne de gauche. Ajouter une étiquette nommée `DateDisplay` à la `leftContent` `<div>`.

[!code-aspx[Main](creating-a-site-wide-layout-using-master-pages-vb/samples/sample5.aspx)]

Ensuite, créez un `Page_Load` Gestionnaire d’événements pour le contrôleur de page et ajoutez le code suivant :

[!code-vb[Main](creating-a-site-wide-layout-using-master-pages-vb/samples/sample6.vb)]

Le code ci-dessus définit l’étiquette `Text` propriété à la date et l’heure sous la forme du jour de la semaine, le nom du mois et le jour à deux chiffres (voir Figure 11). Avec cette modification, modifier une de vos pages de contenu. Comme le montre la Figure 11, le balisage résultant est immédiatement mis à jour pour inclure la modification apportée à la page maître.


[![Les modifications apportées à la Page maître sont répercutées lors de l’affichage d’une Page de contenu](creating-a-site-wide-layout-using-master-pages-vb/_static/image30.png)](creating-a-site-wide-layout-using-master-pages-vb/_static/image29.png)

**Figure 11**: les modifications à la Page maître sont répercutées lors de l’affichage d’une Page de contenu ([cliquez pour afficher l’image en taille réelle](creating-a-site-wide-layout-using-master-pages-vb/_static/image31.png))


> [!NOTE]
> Comme l’illustre cet exemple, les pages maîtres peuvent contenir des contrôles serveur Web, de code et de gestionnaires d’événements.


## <a name="summary"></a>Résumé

Les pages maîtres permettent aux développeurs ASP.NET concevoir une disposition cohérente au niveau du site qui est facilement modifiable. Création de pages maîtres et leurs pages de contenu associées est aussi simple que la création des pages ASP.NET standard, comme Visual Web Developer offre une prise en charge au moment du design enrichie.

L’exemple de page maître que nous avons créé dans ce didacticiel a deux contrôles ContentPlaceHolder, head et MainContent. Nous avons uniquement spécifié un balisage pour le contrôle MainContent ContentPlaceHolder dans notre page de contenu, toutefois. Dans le didacticiel suivant nous allons aborder l’utilisation du contenu de plusieurs contrôles dans la page de contenu. Nous expliquons également comment définir la valeur par défaut le balisage pour le contenu contrôle dans la page maître, ainsi que la manière dont pour passer à l’aide de la valeur par défaut balisage définis dans le masque de page et en fournissant des balises personnalisées à partir de la page de contenu.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [ASP.NET pour les concepteurs : libérer les modèles de conception et des conseils sur la création de sites Web ASP.NET à l’aide des normes Web](https://msdn.microsoft.com/en-us/asp.net/aa336602.aspx)
- [Vue d’ensemble des Pages maîtres ASP.NET](https://msdn.microsoft.com/en-us/library/wtxbf3hh.aspx)
- [Didacticiels de feuilles de style (CSS) en cascade](http://www.w3schools.com/css/default.asp)
- [Définition dynamique de titre de la Page](http://aspnet.4guysfromrolla.com/articles/051006-1.aspx)
- [Pages maîtres dans ASP.NET](http://www.odetocode.com/articles/419.aspx)
- [Didacticiels de démarrage rapide des Pages maîtres](https://quickstarts.asp.net/QuickStartv20/aspnet/doc/masterpages/default.aspx)

### <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de plusieurs manuels ASP/ASP.NET et de créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 3.5 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Scott peut être atteint à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4GuysFromRolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements

Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4GuysFromRolla.com).

>[!div class="step-by-step"]
[Précédent](nested-master-pages-cs.md)
[Suivant](multiple-contentplaceholders-and-default-content-vb.md)
