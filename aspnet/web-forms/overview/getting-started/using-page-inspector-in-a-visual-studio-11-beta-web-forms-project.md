---
uid: web-forms/overview/getting-started/using-page-inspector-in-a-visual-studio-11-beta-web-forms-project
title: "À l’aide de l’inspecteur de Page pour Visual Studio 2012 dans ASP.NET Web Forms | Documents Microsoft"
author: rick-anderson
description: "L’inspecteur de page pour Visual Studio 2012 est un outil de développement web avec un navigateur intégré. Sélectionner un élément dans le navigateur intégré et l’inspecteur de Page..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/15/2012
ms.topic: article
ms.assetid: 2ece0bf4-aae5-4ff4-8f62-28e0819d4f86
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/getting-started/using-page-inspector-in-a-visual-studio-11-beta-web-forms-project
msc.type: authoredcontent
ms.openlocfilehash: a2ac8334e62e6ab7af7042572cfd5950c687001b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-page-inspector-for-visual-studio-2012-in-aspnet-web-forms"></a>À l’aide de l’inspecteur de Page pour Visual Studio 2012 dans ASP.NET Web Forms
====================
par Tim Ammann

> L’inspecteur de page pour Visual Studio 2012 est un outil de développement web avec un navigateur intégré. Sélectionner un élément dans le navigateur intégré et l’inspecteur de Page instantanément met en surbrillance l’élément source et CSS. Vous pouvez rechercher n’importe quelle page dans votre application, rapidement rechercher les sources de balisage rendu et utiliser les outils de navigation à droite dans l’environnement Visual Studio.
> 
> Ce didacticiel shwos comment activer le Mode d’Inspection rapidement localiser et modifier les règles CSS et du texte dans votre projet web. Ce didacticiel utilise un projet d’Application Web Forms, mais vous pouvez également utiliser l’inspecteur de Page pour les projets de Site Web et [MVC](https://go.microsoft.com/?linkid=9802002) applications.
> 
> Le didacticiel comprend les sections suivantes :
> 
> [Composants requis](#_1_prerequisites)
> 
> [Créer une Application Web](#_2_creating_a)
> 
> [Utilisation de l’inspecteur de Page pour afficher l’Application](#_3_using_page)
> 
> [Activer le Mode d’Inspection](#_4_inspection_mode)
> 
> [Utilisation de l’inspecteur de Page pour apporter des modifications au balisage](#_5_using_page)
> 
> [Mode d’inspection et de la fenêtre de code HTML](#_6_inspection_mode)
> 
> [Aperçu des modifications dans la fenêtre Styles CSS](#_7_previewing_css)
> 
> [Synchronisation automatique CSS](#css_auto_sync)
> 
> [À l’aide du sélecteur de couleurs CSS](#css_color_picker)


<a id="_prerequisites"></a><a id="_1_prerequisites"></a>

## <a name="prerequisites"></a>Conditions préalables

- [Visual Studio 2012](https://www.microsoft.com/visualstudio/11/en-us) ou [Visual Studio Express 2012 pour Web](https://www.microsoft.com/visualstudio/11/en-us/downloads#express-web).

> [!NOTE]
> Pour obtenir la dernière version de l’inspecteur de Page, utilisez [Web Platform Installer](https://go.microsoft.com/fwlink/?LinkId=255386) pour installer le Kit de développement logiciel Azure pour .NET 2.0.


L’inspecteur de page est fourni avec les outils de développement Web de Microsoft. La version la plus récente est 1.3. Pour vérifier quelle version vous avez, exécutez Visual Studio, puis sélectionnez **sur Microsoft Visual Studio** à partir de la **aide** menu.

<a id="_creating_a_web"></a><a id="_2_creating_a"></a>

## <a name="create-a-web-application"></a>Créer une Application Web

Tout d’abord, vous allez créer une application web que vous utiliserez avec l’inspecteur de Page. Dans Visual Studio, choisissez **fichier** &gt; **nouveau projet**. Sur la gauche, développez **Visual C#**, sélectionnez **Web**, puis sélectionnez **Application ASP.NET Web Forms**.

![Nouvelle Application Web Forms](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image1.png)

Cliquez sur **OK**.

L’application s’ouvre dans **Source** vue.

![Nouvelle Application Web Forms en mode Source](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image2.png)

Maintenant que vous avez une application fonctionne avec, vous pouvez utiliser l’inspecteur de Page pour examiner et modifier.

<a id="_starting_page_inspector"></a><a id="_3_starting_page"></a><a id="_3_using_page"></a>

## <a name="use-page-inspector-to-view-the-application"></a>Utilisation de l’inspecteur de Page pour afficher l’Application

Ensuite, vous allez afficher l’application avec l’inspecteur de Page. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **afficher dans l’inspecteur de Page**.

![Afficher dans l’inspecteur de Page](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image3.png)

Par défaut, au démarrage de l’inspecteur de Page pour la première fois, il est ancré dans une fenêtre étroite sur le côté gauche de l’environnement Visual Studio. Laisser ancrée sur le côté gauche et définissez-le sur une largeur qui est à l’aise pour vous, ou dans un des zones outil ancre sur le haut, bas ou à droite :

![Position d’ancrage de l’inspecteur de page](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image4.png)

Si vous détacher de la fenêtre de l’inspecteur de Page, vous pouvez le placer en dehors de Visual Studio, ou même sur un deuxième écran si vous en avez. Toutefois, dans la commande ALT + TAB entre l’inspecteur de Page et de Visual Studio lorsque la fenêtre de l’inspecteur de Page n’est pas ancrée, accédez à **outils** &gt; **Options** &gt;  **Environnement** &gt; **onglets et fenêtres**et sous **onglet bien**, désactivez la case à cocher appelée **fenêtres d’outils flottante toujours au premier plan de la fenêtre principale**:

![Décochez la case de windows outil flottante ALT + TAB entre Visual Studio et la fenêtre de l’inspecteur de Page non ancrée](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image5.png)

Le volet supérieur de la fenêtre de l’inspecteur de Page affiche la page actuelle dans une fenêtre de navigateur. Le volet inférieur affiche la page dans le balisage HTML sur la gauche, et certains onglets à droite qui vous permettent d’inspecter les différents aspects de la page. Le volet inférieur est similaire à la [outils de développement F12](https://msdn.microsoft.com/en-us/ie/aa740478) dans Internet Explorer. (Toutefois, contrairement aux outils de développement, vous pouvez utiliser l’inspecteur de Page à droite dans Visual Studio.)

![Inspecteur de page](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image6.png)

Dans ce didacticiel, vous allez utiliser le volet Explorateur de l’inspecteur de Page et le **HTML** et **Styles** onglets pour vous aider à rapidement accéder et apporter des modifications à l’application.

<a id="_4_inspection_mode"></a>
## <a name="enable-inspection-mode"></a>Activer le Mode d’Inspection

Ensuite, vous verrez comment le Mode d’Inspection de l’inspecteur de Page fonctionne. Dans la fenêtre de l’inspecteur de Page, cliquez sur le **inspecter** bouton.

![Inspecter l’élément](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image7.png)

Pour afficher le mode d’inspection en action, déplacez votre souris sur différentes parties de la page dans la fenêtre du navigateur l’inspecteur de Page. Comme vous le faites, le pointeur de la souris se transforme en signe plus grand, et la mise en surbrillance de l’élément en dessous :

![Survol de wrapper de div.content](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image8.png)

Lorsque vous déplacez le pointeur de la souris, notez que

- Le contenu de **Source** afficher change pour afficher le balisage correspondant à l’élément sélectionné dans la page. La balise pertinente est mis en surbrillance. Si la source est dans un autre fichier, ce fichier est ouvert en mode Source avec la balise pertinente mis en surbrillance.

- Le balisage affiché dans le **HTML** onglet dans l’inspecteur de Page également modifie pour correspondre à l’élément sélectionné dans la page. Dans le **HTML** onglet, le balisage approprié est indiqué.

- Le **Styles** onglet affiche les règles CSS relatives à la sélection actuelle.

<a id="_5_using_page"></a>

## <a name="use-page-inspector-to-make-changes-to-markup"></a>Utilisation de l’inspecteur de Page pour apporter des modifications au balisage

Vous voyez à présent comment vous pouvez utiliser l’inspecteur de Page pour rechercher et d’apporter des modifications à la balise ou de texte dont l’emplacement ne soit pas immédiatement évident.

Placer l’inspecteur de Page en Mode d’Inspection, puis faites défiler vers le bas de la page d’accueil.

Dès que vous entrez la zone de pied de page, l’inspecteur de Page ouvre le *Site.Master* fichier de disposition dans **Source** vue sous un onglet temporaire à droite des autres onglets et met en surbrillance de la section du maître page que vous avez avez sélectionné. Cela vous indique comment l’inspecteur de Page peuvent rechercher et afficher le contenu sur une page qui proviennent en fait un fichier différent de celui que vous avez ouvert.

![Met en évidence de pied de page en Mode d’Inspection](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image9.png)

Dans la fenêtre du navigateur l’inspecteur de Page, placez le pointeur de la souris au-dessus de la ligne avec les informations de copyright <a id="a"> </a>Notez.

Dans le *Site.Master* page, la ligne correspondante est mise en surbrillance.

![Ligne de pied de page copyright mis en surbrillance](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image10.png)

Ajouter du texte à la fin de la ligne dans le *Site.Master* fichier.

&lt;p&gt;&amp;copier ; &lt;% : DateTime.Now.Year %&gt; -mon Rocks d’Application ASP.NET !&lt; /p&gt;

Maintenant, appuyez sur Ctrl + Alt + Entrée ou cliquez sur la barre de mise à jour pour afficher les résultats dans la fenêtre de navigateur de l’inspecteur de Page.

![Mon Rocks d’Application ASP.NET !](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image11.png)

Vous pouvez avoir pensions que le pied de page sur la *Default.aspx* page, mais il est avéré dans la page maître et l’inspecteur de Page trouvé pour vous.

<a id="_6_inspection_mode"></a>

## <a name="inspection-mode-and-the-html-window"></a>Mode d’inspection et de la fenêtre de code HTML

Ensuite, vous aurez un coup de œil rapide à la fenêtre de code HTML et comment il mappe des éléments pour vous.

Placez l’inspecteur de Page en Mode d’Inspection.

![Inspecter l’élément](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image12.png)

Cliquez sur la partie supérieure de la page indiquant que « votre logo ici ». Vous examinez un élément particulier plus en détail, afin de l’affichage dans la fenêtre du navigateur ne change plus lorsque vous déplacez le pointeur de la souris.

Maintenant déplacer le pointeur de la souris vers le **HTML** fenêtre. Lorsque vous déplacez le pointeur de la souris, l’inspecteur de Page décrit l’élément dans le **HTML** fenêtre et met en surbrillance l’élément correspondant dans la fenêtre du navigateur.

![Fenêtre HTML](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image13.png)

Comme auparavant, l’inspecteur de Page ouvre le *Site.Master* fichier pour vous dans un onglet temporaire. Cliquez sur l’onglet Site.Master, et le balisage correspondant est mis en surbrillance dans le &lt;en-tête&gt; section :

![Balise en surbrillance](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image14.png)

<a id="_using_page_inspector"></a><a id="_7_previewing_css"></a>

## <a name="preview-css-changes-in-the-styles-window"></a>Aperçu des modifications dans la fenêtre Styles CSS

Ensuite, vous verrez comment vous pouvez utiliser l’inspecteur de Page **Styles** fenêtre pour afficher un aperçu des modifications apportées à CSS.

Cliquez sur le **inspecter** bouton à placer l’inspecteur de Page en Mode d’Inspection.

Dans la fenêtre de navigateur de l’inspecteur de Page, déplacez le pointeur de la souris sur la section « Page d’accueil » jusqu'à ce que le **div.content-wrapper** étiquette s’affiche.

![Vous pointez sur des éléments](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image15.png)

Cliquez une fois dans la section div.content-wrapper, puis déplacez le pointeur de la souris pour la **Styles** fenêtre. Dans le sélecteur de classe wrapper .content .featured, désactivez et activez la case à cocher pour la propriété de couleur d’arrière-plan.

![Couleur d’arrière-plan clair](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image16.png)

Notez comment la modification affiche un aperçu d’instantanément dans la fenêtre de navigateur de l’inspecteur de Page.

Sélectionnez la case à cocher à nouveau, puis double-cliquez sur la valeur de propriété et la remplacer par `red`. La modification s’affiche immédiatement :

![Couleur d’arrière-plan rouge](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image17.png)

Le **Styles** rend fenêtre faciles à tester et afficher un aperçu de CSS change avant de valider les modifications apportées au style sheet lui-même.

<a id="css_auto_sync"></a>
## <a name="css-auto-sync"></a>Synchronisation automatique CSS

> [!NOTE]
> Cette fonctionnalité requiert la version 1.3 de l’inspecteur de Page.


La fonctionnalité de synchronisation automatique CSS vous permet de modifier directement un fichier CSS et voir les modifications immédiatement dans le navigateur de l’inspecteur de Page.

Cliquez sur **inspecter** à placer l’inspecteur de Page en Mode d’Inspection.

Dans le navigateur de l’inspecteur de Page, déplacez le pointeur de la souris sur la section « Page d’accueil » jusqu'à ce que le **div.content-wrapper** étiquette s’affiche. Cliquez une fois pour sélectionner cet élément.

Le **styles** fenêtre affiche toutes les règles CSS pour cet élément. Faites défiler jusqu'à un sélecteur de classe rechercher .featured .content-wrapper. Cliquez sur « .featured .content-wrapper ». L’inspecteur de page ouvre le fichier CSS qui définit ce style (Site.css) et met en surbrillance le style CSS correspondant.

![Fichier CSS](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image18.png)

Maintenant modifiez la valeur de `background-color` « rouge ». La modification s’affiche immédiatement dans le navigateur de l’inspecteur de Page.

![Navigateur de l’inspecteur de page](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image19.png)

<a id="css_color_picker"></a>

## <a name="using-the-css-color-picker"></a>À l’aide du sélecteur de couleurs CSS

Ensuite, vous allez apprendre à utiliser l’inspecteur de Page pour rechercher rapidement et de modifier le code CSS texte mis en surbrillance dans l’application par défaut. Dans cet exemple, vous avez décidé que vous ne la surbrillance bleue et souhaitez Remplacez-la par une autre couleur.

Cliquez sur le **inspecter** bouton.

![Inspecter l’élément](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image20.png)

Dans la fenêtre de navigateur de l’inspecteur de Page, déplacez le pointeur de la souris sur la mise en surbrillance « vidéos, didacticiels et exemples » texte afin que le code CSS « marquer « étiquette s’affiche.

![Vous pointez sur l’élément de la marque](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image21.png)

Cliquez sur le texte pour le sélectionner. Le sélecteur de marque CSS correspondant s’affiche au bas de la **Styles** fenêtre.

![Marquer le sélecteur dans la fenêtre Styles](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image22.png)

Cliquez sur le sélecteur de marque. Cette opération ouvre le *Site.css* fichier de l’application web. Cliquez sur l’onglet Site.css et le CSS correspondant pour le sélecteur est mis en surbrillance :

![Marquer le sélecteur dans la feuille de style](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image23.png)

Sélectionnez et supprimez la ligne avec la propriété de couleur d’arrière-plan.

Vous allez maintenant utiliser le nouveau sélecteur de couleurs CSS de 2012 Visual Studio pour choisir une nouvelle couleur pour le **marquer** propriété de couleur d’arrière-plan.

<a id="_using_the_visual"></a>

### <a name="using-the-visual-studio-2012-css-color-picker"></a>À l’aide du sélecteur de couleurs CSS de Visual Studio 2012

L’éditeur de CSS dans Visual Studio 2012 a un sélecteur de couleurs qui permet de facilement choisir et insérer des couleurs. Il possède une barre de couleur simple et un sélecteur « pop-down » qui offre un contrôle plus précis.

Le sélecteur de couleurs inclut une palette standard de couleurs, prend en charge les noms de couleurs standard, les codes de hachage, les couleurs RVB, RGBA, HSL et HSLA et conserve une liste des couleurs que vous avez utilisé le plus récemment dans le document.

Sur la ligne où la propriété de couleur d’arrière-plan a été, tapez « bc » et appuyez une fois sur la flèche vers le bas.

Lorsque vous tapez le premier caractère de chaque mot dans une propriété séparées par des tirets comme « couleur d’arrière-plan », IntelliSense filtre la liste pour afficher uniquement les propriétés qui correspondent à :

![IntelliSense filtré des valeurs](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image24.png)

Maintenant, tapez un signe deux-points. Lorsque vous procédez ainsi, le nom de propriété de couleur d’arrière-plan complète est inséré. Type  **#**  ou **RVB (**, et la barre de sélecteur de couleurs s’affiche :

![La barre de sélecteur de couleurs CSS](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image25.png)

Pour voir comment fonctionne de la barre de sélecteur de couleurs, cliquez sur les couleurs avec le pointeur de la souris, ou appuyez sur la touche flèche bas et puis utiliser les touches de direction gauche et droite pour parcourir les couleurs. Lorsque vous visitez une couleur, la valeur correspondante pour la propriété de couleur d’arrière-plan est affichée en aperçu :

![valeur de propriété de couleur d’arrière-plan aperçu](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image26.png)

À ce stade, vous pouvez appuyer sur ENTRÉE pour sélectionner la valeur, puis un point-virgule ( ;) pour terminer l’entrée CSS. Pour l’instant, passez à la section suivante afin que vous pouvez voir comment le sélecteur de couleur pop-bas fonctionne.

#### <a name="using-the-color-picker-pop-down"></a>À l’aide du sélecteur de couleur Pop en bas

Lorsque la barre de couleurs ne dispose pas la couleur exacte que vous recherchez, vous pouvez utiliser le sélecteur de couleurs pop vers le bas.

Pour l’ouvrir, cliquez sur le chevron double à l’extrémité droite de la barre de couleurs, ou appuyez sur la flèche vers le bas une ou deux fois sur le clavier.

![Sélecteur de couleurs CSS Pop-bas](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image27.png)

Cliquez sur une couleur dans la barre verticale sur la droite. Cela indique un dégradé de cette couleur dans la fenêtre principale. Choisissez une couleur directement à partir de la barre verticale en appuyant sur entrée, ou cliquez sur n’importe quel point dans la fenêtre principale pour choisir avec une précision supérieure.

Si votre écran de l’ordinateur que vous souhaitez utiliser est une couleur (ne doit pas nécessairement être à l’intérieur de l’interface utilisateur de Visual Studio), vous pouvez capturer sa valeur à l’aide de l’outil Pipette dans la partie inférieure droite.

Vous pouvez également modifier l’opacité d’une couleur en déplaçant le curseur en bas du sélecteur de couleurs. Ainsi, modifications de couleur aux valeurs RGBA, car le format RVBA peut représenter l’opacité.

Une fois que vous avez choisi une couleur, appuyez sur entrée, puis tapez un point-virgule pour terminer l’entrée de la couleur d’arrière-plan dans le *Site.css* fichier.

<a id="_the_update_bar"></a>

### <a name="the-page-inspector-update-bar"></a>La barre de mise à jour de l’inspecteur de Page

L’inspecteur de page détecte immédiatement la modification apportée à la *Site.css* fichier (ou à n’importe quel fichier dans l’application) et affiche une alerte dans une barre de mise à jour.

![Barre de mise à jour](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image28.png)

Pour enregistrer tous vos fichiers et actualiser le navigateur de l’inspecteur de Page, appuyez sur Ctrl + Alt + Entrée ou cliquez sur la barre de mise à jour. La modification de la couleur de surbrillance s’affiche dans le navigateur :

![Couleur de surbrillance modifié](using-page-inspector-in-a-visual-studio-11-beta-web-forms-project/_static/image29.png)

<a id="_using_page_inspector_1"></a>Notez que vous actualisé aisément le navigateur de l’inspecteur de Page directement à partir de l’environnement Visual Studio. Au lieu d’un navigateur externe à l’aide de l’inspecteur de Page vous permet de rester dans l’éditeur lorsque vous développez vos applications web.

## <a name="see-also"></a>Voir aussi

[Présentation de l’inspecteur de Page](https://channel9.msdn.com/posts/visual-studio-vnext-introducing-page-inspector) (vidéo Channel 9)

[Messages d’erreur de l’inspecteur de page](https://go.microsoft.com/?linkid=9813062) (MSDN)
