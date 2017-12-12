---
uid: web-forms/overview/getting-started/hands-on-labs/using-page-inspector-in-visual-studio-2012
title: "À l’aide de l’inspecteur de Page dans Visual Studio 2012 | Documents Microsoft"
author: rick-anderson
description: "Dans cet atelier pratique, vous allez découvrir un nouvel outil pour rechercher et corriger les problèmes de la page web dans Visual Studio - l’inspecteur de Page. L’inspecteur de page est un nouvel outil que b..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/18/2013
ms.topic: article
ms.assetid: 73232292-a5fe-4720-82a1-8f6553effd1f
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/getting-started/hands-on-labs/using-page-inspector-in-visual-studio-2012
msc.type: authoredcontent
ms.openlocfilehash: 1a9e093faae2cea1c27c582e22aebc908f78addb
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-page-inspector-in-visual-studio-2012"></a>À l’aide de l’inspecteur de Page dans Visual Studio 2012
====================
par [Web Camps équipe](https://twitter.com/webcamps)

> Dans cet atelier pratique, vous allez découvrir un nouvel outil pour rechercher et corriger les problèmes de la page web dans Visual Studio - l’inspecteur de Page.
> 
> L’inspecteur de page est un nouvel outil qui fournit des outils de diagnostics de navigateur à Visual Studio et fournit une expérience intégrée entre le navigateur, ASP.NET et code source. Il restitue une page web (HTML, Web Forms, ASP.NET MVC ou Web Pages) directement dans l’IDE de Visual Studio et vous permet d’examiner le code source et le résultat. L’inspecteur de page vous permet de décomposer facilement un site Web, de créer rapidement des pages de toutes pièces et de diagnostiquer rapidement les problèmes.
> 
> Aujourd'hui, nous avons plusieurs infrastructures Web qui créent des sites Web flexibles et évolutives en temps voulu, tels que ASP.NET MVC et les Web Forms. En revanche, il obtient plus difficile à identifier les problèmes sur les pages, car l’IDE ne prend pas en charge le mode concepteur dans les pages basées sur le modèle et le contenu dynamique. Par conséquent, ces sites Web doit être ouverte dans un navigateur pour voir comment elles apparaissent à un utilisateur.
> 
> Les développeurs Web utilisent des outils externes pour rechercher les problèmes qui exécutent régulièrement dans le navigateur. Ensuite, ils revenir à l’IDE et corrigé. Dans les deux sens activité entre l’IDE, le navigateur et les outils de profilage peut être inefficace, il requiert parfois un chaque fois que vous souhaitez reproduire un problème de nettoyage du cache et le nouveau déploiement.
> 
> L’inspecteur de page réduit un écart dans le développement Web entre le client (outils de navigation) et le serveur (ASP.NET et le code source) par le meilleur des deux mondes à l’aide d’un ensemble de fonctionnalités.
> 
> À l’aide de l’inspecteur de Page, vous pouvez voir les éléments dans les fichiers sources (y compris le code côté serveur) qui ont généré le balisage HTML à restituer dans le navigateur. L’inspecteur de page vous permet également de modifier les propriétés CSS et les attributs de l’élément DOM pour voir les modifications reflétées immédiatement dans le navigateur.
> 
> Cet atelier pratique sera vous guident à travers les fonctionnalités de l’inspecteur de Page et vous montrent comment vous pouvez les utiliser pour résoudre les problèmes dans les applications Web. **Cet atelier contient deux exercices à l’aide de flux semblables mais ciblant des différentes technologies. Si vous êtes un développeur d’ASP.NET MVC, suivez l’exercice Si vous êtes un exercice de suivi de développeur WebForms deux**.
> 
> Ce laboratoire présente les améliorations et nouvelles fonctionnalités décrites précédemment en appliquant les modifications mineures à un exemple d’application Web dans le dossier Source.
> 
> Tous les exemples de code et des extraits de code sont inclus dans le Kit de formation Camps Web, disponible à l’adresse [https://go.microsoft.com/fwlink/?LinkID=248297&clcid=0x409](https://go.microsoft.com/fwlink/?LinkID=248297&clcid=0x409).


<a id="Objectives"></a>

<a id="Objectives"></a>
### <a name="objectives"></a>Objectifs

Dans cet atelier pratique, vous allez apprendre comment :

- Décomposer un Site Web à l’aide de l’inspecteur de Page
- Inspecter et afficher un aperçu des modifications de styles CSS avec l’inspecteur de Page
- Détecter et résoudre les problèmes dans vos pages web à l’aide de l’inspecteur de Page

<a id="Prerequisites"></a>

<a id="Prerequisites"></a>
### <a name="prerequisites"></a>Conditions préalables

Vous devez disposer des éléments suivants pour effectuer ce laboratoire :

- [Microsoft Visual Studio Express 2012 pour Web](https://www.microsoft.com/visualstudio/eng/products/visual-studio-express-for-web) ou supérieure (lecture [annexe A](#AppendixA) pour obtenir des instructions sur l’installation).
- Internet Explorer 9 ou version ultérieure

* * *

<a id="Exercises"></a>

<a id="Exercises"></a>
## <a name="exercises"></a>Exercices

Cet atelier pratique inclut les exercices suivants :

1. [Exercice 1 : À l’aide de l’inspecteur de Page dans les projets ASP.NET MVC](#Exercise1)
2. [Exercice 2 : À l’aide de l’inspecteur de Page dans les projets Web Forms](#Exercise2)

> [!NOTE]
> Chaque exercice est accompagnée d’une solution de départ, située dans le dossier de début de l’exercice, ce qui vous permet de suivre chaque exercice indépendamment des autres. Dans le code source pour un exercice, vous y trouverez également un dossier de fin contenant une solution Visual Studio avec le code qui résulte de la procédure dans l’exercice correspondant. Si vous avez besoin d’aide au cours de cet atelier, vous pouvez utiliser ces solutions comme guide.


Durée estimée pour effectuer ce laboratoire : **30 minutes**.

<a id="Exercise1"></a>

<a id="Exercise_1_Using_Page_Inspector_in_ASPNET_MVC_Projects"></a>
### <a name="exercise-1-using-page-inspector-in-aspnet-mvc-projects"></a>Exercice 1 : À l’aide de l’inspecteur de Page dans les projets ASP.NET MVC

Dans cet exercice, vous allez apprendre comment afficher un aperçu et de déboguer un **ASP.NET MVC 4** à l’aide de la solution **l’inspecteur de Page**. Tout d’abord, vous allez effectuer une brève visite guidée de l’outil pour en savoir plus les fonctionnalités qui facilitent le processus de débogage de Web. Ensuite, vous allez travailler dans une page web qui contient les problèmes de conception de styles. Vous allez apprendre à utiliser l’inspecteur de Page pour rechercher le code source qui génère le problème et le résoudre.

<a id="Ex1Task1"></a>

<a id="Task_1_-_Exploring_Page_Inspector"></a>
#### <a name="task-1---exploring-page-inspector"></a>Tâche 1 - exploration de l’inspecteur de Page

Dans cette tâche, vous allez apprendre à utiliser l’inspecteur de Page dans le contexte d’un projet ASP.NET MVC 4 montre une galerie de photos.

1. Ouvrez le **commencer** solution situé dans **début/MVC4-Ex1/Source/** dossier.

    1. Vous devez télécharger des packages NuGet manquants avant de poursuivre. Pour ce faire, cliquez sur le **projet** menu et sélectionnez **gérer les Packages NuGet**.
    2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **restaurer** afin de télécharger les packages manquants.
    3. Enfin, générez la solution en cliquant sur **générer** | **générer la Solution**.

    > [!NOTE]
    > Un des avantages de l’utilisation de NuGet est que vous ne devez expédier toutes les bibliothèques dans votre projet, ce qui réduit la taille du projet. Avec NuGet Power Tools, en spécifiant les versions de package dans le fichier Packages.config, vous serez en mesure de télécharger toutes les bibliothèques requises à la première fois que vous exécutez le projet. C’est pourquoi vous devez exécuter ces étapes après avoir ouvert une solution existante à partir de ce laboratoire.
2. Dans l’Explorateur de solutions, recherchez **Index.cshtml** afficher sous le **/vues/Home** dossier de projet, faites un clic droit et sélectionnez **afficher dans l’inspecteur de Page**.

    ![Sélection d’un fichier pour afficher un aperçu de l’inspecteur de Page](using-page-inspector-in-visual-studio-2012/_static/image1.png "en sélectionnant un fichier pour afficher un aperçu de l’inspecteur de Page")

    *Sélection d’un fichier pour afficher un aperçu de l’inspecteur de Page*
3. La fenêtre de l’inspecteur de Page affiche le */Home/Index* URL mappée à la source de vue que vous avez sélectionné.

    ![ThefirstcontactwithPageInspector](using-page-inspector-in-visual-studio-2012/_static/image2.png)

    *Le premier contact avec l’inspecteur de Page*

    L’outil de l’inspecteur de Page est intégré dans votre environnement Visual Studio. L’inspecteur contient un navigateur intégré, ainsi que d’un profileur HTML puissant. Notez que vous n’avez pas à exécuter la solution pour voir comment vos pages.

    > [!NOTE]
    > Lorsque la largeur du navigateur de l’inspecteur de Page est inférieure à la largeur de la page ouverte, vous ne verrez pas la page correctement. Si cela se produit, ajustez la largeur de l’inspecteur de Page.
4. Cliquez sur le **fichiers** onglet dans l’inspecteur de Page.

    Vous verrez tous les fichiers sources qui sont composées de la page d’Index. Cette fonctionnalité permet d’identifier tous les éléments en un coup de œil, surtout lorsque vous travaillez avec les vues partielles et les modèles. Notez que vous pouvez également ouvrir chacun des fichiers si vous cliquez sur les liens.

    ![L’onglet de fichiers](using-page-inspector-in-visual-studio-2012/_static/image3.png)

    *L’onglet fichiers*
5. Cliquez sur le **basculer vers le Mode Inspection** bouton situé à gauche des onglets.

    Cet outil vous permettra de sélectionner un élément de la page et de voir son code HTML et Razor.

    ![Bouton bascule-Inspection-Mode](using-page-inspector-in-visual-studio-2012/_static/image4.png)

    *Bouton bascule du Mode d’Inspection*
6. Dans le navigateur de l’inspecteur de Page, déplacez le pointeur de la souris sur les éléments de page. Lorsque vous déplacez le pointeur de la souris sur n’importe quelle partie de la page rendue, le type d’élément est affiché et le balisage source ou le code correspondant est mis en surbrillance dans l’éditeur Visual Studio.

    ![Inspectionmodeinaction](using-page-inspector-in-visual-studio-2012/_static/image5.png)

    *Mode d’inspection en action*

    > [!NOTE]
    > Pas agrandir la fenêtre de l’inspecteur de Page, ou vous ne pourrez pas voir l’onglet Aperçu affichant le code source. Si vous cliquez sur l’élément dans l’inspecteur de Page lorsqu’elle est agrandie, le code source de la sélection s’affiche, mais il sera masquer la fenêtre de l’inspecteur de Page.

    Si vous faites attention à la **Index.cshtml** fichier, vous pouvez remarquer que la partie du code source qui génère l’élément sélectionné est mis en surbrillance. Cette fonctionnalité facilite la modification des fichiers de sources de long, offrant ainsi un moyen direct et rapide pour accéder au code.

    ![Inspectingelements](using-page-inspector-in-visual-studio-2012/_static/image6.png)

    *L’inspection des éléments*
7. Cliquez sur le **basculer vers le Mode Inspection** bouton (![sélectionnez l’onglet HTML pour afficher le code HTML restitué dans le navigateur de l’inspecteur de Page.] (using-page-inspector-in-visual-studio-2012/_static/image7.png "Sélectionnez l’onglet HTML pour afficher le code HTML restitué dans le navigateur de l’inspecteur de Page.") ) pour désactiver le curseur.
8. Sélectionnez le **HTML** onglet pour afficher le code HTML restitué dans le navigateur de l’inspecteur de Page.
9. Dans le balisage HTML, recherchez l’élément de liste avec le lien Koala et sélectionnez-le.

    Notez que lorsque vous sélectionnez le code, la sortie correspondante est automatiquement mis en surbrillance dans le navigateur. Cette fonctionnalité est utile pour voir comment un bloc de code HTML est rendu sur la page.

    ![Élément HTML de sélection dans la page](using-page-inspector-in-visual-studio-2012/_static/image8.png "élément HTML de sélection dans la page")

    *Sélection d’élément HTML dans la page*
10. Cliquez sur le **basculer vers le Mode Inspection** bouton pour activer *Mode d’Inspection* et cliquez sur la barre de navigation. Sur la droite du code HTML, dans le volet Styles, vous verrez une liste avec les styles CSS appliquées à l’élément sélectionné.

    > [!NOTE]
    > Étant donné que l’en-tête est une partie de la mise en page du site, l’inspecteur de Page ouvre également \_Layout.cshtml fichier et mettez en surbrillance le segment de code affecté.

    ![Discoveringstyles](using-page-inspector-in-visual-studio-2012/_static/image9.png)

    *Détection des styles et les fichiers source d’un élément sélectionné*
11. Avec le pointeur de contrôle toggle activé, placez le pointeur sous la barre bleue de proposée, puis cliquez sur le cercle moitié.

    ![Sélection d’un élément](using-page-inspector-in-visual-studio-2012/_static/image10.png "sélectionner un élément")

    *Sélection d’un élément*
12. Dans le volet Styles, localisez le **image d’arrière-plan** d’élément sous la **.main-content** groupe. **Décochez la case** le **image d’arrière-plan** et observez le résultat. Vous remarquerez que le navigateur reflète les modifications immédiatement et le cercle est masqué.

    > [!NOTE]
    > Les modifications appliquées sur l’onglet Styles de l’inspecteur de Page n’affectent pas la feuille de style d’origine. Vous pouvez désactiver des styles ou modifier leurs valeurs à chaque fois que vous le souhaitez, mais elles seront restaurées après l’actualisation de la page.

    ![Activation et désactivation de styles CSS](using-page-inspector-in-visual-studio-2012/_static/image11.png "activation et désactivation de styles CSS")

    *Activation et désactivation de styles CSS*
13. Maintenant, cliquez sur le «**votre logo ici**' texte de l’en-tête à l’aide du mode d’inspection.
14. Dans le **Styles** onglet, recherchez la **taille de police** CSS attribut sous la **.site-titre** groupe. Double-cliquez sur la valeur d’attribut et remplacez la valeur 2.3 em avec **em 3**, puis appuyez sur **entrée**. Notez que le titre de la recherche la plus grand.

    ![La modification de valeurs CSS dans l’inspecteur de Page](using-page-inspector-in-visual-studio-2012/_static/image12.png "valeurs CSS de la modification dans l’inspecteur de Page")

    *Modification des valeurs CSS dans l’inspecteur de Page*
15. Cliquez sur le **suivre les Styles** onglet, situé dans le volet droit de l’inspecteur de Page. Il s’agit d’une autre façon de voir tous les styles appliqués à la sélection, classée par nom d’attribut.

    ![CSSstylestracing](using-page-inspector-in-visual-studio-2012/_static/image13.png)

    *Suivi des styles CSS de l’élément sélectionné*
16. Une autre fonctionnalité de l’inspecteur de Page est le volet de disposition. Le mode d’inspection, sélectionnez la barre de navigation, puis cliquez sur le **disposition** onglet dans le volet droit. Vous verrez la taille exacte de l’élément sélectionné, ainsi que sa taille des bordures, offset, marge et marge intérieure. Notez que vous pouvez également modifier les valeurs de cette vue.

    ![Disposition de l’élément dans l’inspecteur de Page](using-page-inspector-in-visual-studio-2012/_static/image14.png "disposition de l’élément dans l’inspecteur de Page")

    *Disposition de l’élément dans l’inspecteur de Page*

<a id="Ex1Task2"></a>

<a id="Task_2_-_Finding_and_Fixing_Style_Issues_in_the_Photo_Gallery"></a>
#### <a name="task-2---finding-and-fixing-style-issues-in-the-photo-gallery"></a>Tâche 2 : recherche et la résolution des problèmes de Style dans la galerie de photos

Comment serait pour diagnostiquer les problèmes de pages Web avec les versions précédentes de Visual Studio ? Vous êtes probablement habitué à web outils de débogage qui sont exécutées en dehors de l’IDE de Visual Studio, tels que les outils de développement Internet Explorer ou Firebug. Navigateurs ne comprennent que HTML, scripts et les styles, une infrastructure sous-jacente génère le code HTML qui est affiché. Pour cette raison, vous devez souvent déployer l’ensemble du site pour voir comment les pages web ressemblent.

Vous aviez probablement suivi ces étapes lorsque vous souhaitez détecter et résoudre un problème dans votre site web :

1. Exécutez la Solution à partir de Visual Studio, ou déployer la page sur le serveur web.
2. Dans le navigateur, ouvrez les outils de développement, vous utilisez, ou ouvrez simplement le code source et les styles et tentez de faire correspondre le problème. Pour rechercher les fichiers concernés, vous auriez utilisé le &quot;recherche&quot; ou &quot;recherche dans les fichiers&quot; fonctionnalités avec le nom des classes de style.
3. Une fois que l’erreur est détectée, arrêtez le navigateur Web et le serveur.
4. Effacez le cache du navigateur.
5. Revenez à Visual Studio pour appliquer un correctif. Répétez les étapes à tester.

Comme il n’existe aucun WYSIWYG réel dans ASP.NET MVC 4, la plupart des problèmes de style est détectée sur un stade ultérieur, après l’exécution ou le déploiement de l’application web. Désormais, avec l’inspecteur de Page, il est possible afficher un aperçu de n’importe quelle page sans exécuter la solution.

Dans cette tâche, vous utilisez l’inspecteur de Page et résoudre des problèmes de l’application de la galerie de photos.

1. À l’aide de l’inspecteur de Page, recherchez le **inscrire** et **connectez-vous** des liens situés à gauche de l’en-tête.

    Notez que les liens ne sont pas affichés à l’emplacement attendu sur la droite, et elles sont affichées comme une liste à puces. Vous allez maintenant aligner les liens vers la droite et les remplacer en conséquence.

    ![Recherche le Registre et le journal dans les liens](using-page-inspector-in-visual-studio-2012/_static/image15.png "recherche le Registre et le journal dans les liens")

    *Recherche le Registre et le journal dans les liens*
2. Basculer vers le Mode Inspection sélectionné, cliquez sur Fermer pour, mais pas sur le lien de s’inscrire pour ouvrir son code.

    Notez que le code source des liens se trouve dans le  **\_LoginPartial.cshtml** de fichiers, pas le Index.cshtml ni le \_Layout.cshtml, qui sont les emplacements que vous peut se présenter en premier lieu. Vous avez été placé directement dans le fichier de code source approprié.
3. Dans le **Styles** onglet, recherchez et cliquez sur le  **<section> #login</section>**  élément, qui est le conteneur HTML pour que ces liens.

    Notez que la **#login** style se trouve automatiquement dans **Site.css** après avoir cliqué sur. En outre, le code est maintenant en surbrillance.

    ![En sélectionnant les styles CSS](using-page-inspector-in-visual-studio-2012/_static/image16.png "en sélectionnant les styles CSS")

    *En sélectionnant les styles CSS*
4. Ne pas commenter le **d’alignement du texte** d’attribut dans le code en surbrillance en supprimant les caractères ouvrant et fermant et enregistrer le **Site.css** fichier.

    L’inspecteur de page tient compte de tous les fichiers qui composent la page actuelle, et il peut détecter lorsqu’un de ces fichiers change. Il vous avertit chaque fois que la page actuelle dans le navigateur n’est pas synchronisée avec les fichiers sources.
5. Dans le navigateur de l’inspecteur de Page, cliquez sur la barre située sous la barre d’adresses pour recharger la page.

    ![Recharger la page](using-page-inspector-in-visual-studio-2012/_static/image17.png)

    *Recharger la page*

    Les liens sont désormais à droite, mais ils ressemblent toujours une liste à puces. Maintenant, vous allez supprimer les puces et aligner les liens horizontalement.

    ![Page mise à jour](using-page-inspector-in-visual-studio-2012/_static/image18.png)

    *Page mise à jour*
6. À l’aide du mode d’inspection, sélectionnez un de la  **&lt;li&gt;**  les éléments qui contiennent la &quot;inscrire&quot; et &quot;connectez-vous&quot; des liens. Ensuite, cliquez sur le  **&lt;section&gt; #login** d’élément pour l’accès **Styles.css** code.

    ![Recherche le style](using-page-inspector-in-visual-studio-2012/_static/image19.png "recherche le style")

    *Recherche le style*
7. Dans **Style.css**, les commentaires du code pour **#login li** éléments. Le style que vous ajoutez sera masquer la puce et afficher les éléments horizontalement.

    ![Modification du style les liens de connexion](using-page-inspector-in-visual-studio-2012/_static/image20.png "modification du style les liens de connexion")

    *Modification du style les liens de connexion*
8. Enregistrer **Style.css** de fichier et cliquez une fois dans la barre située au-dessous de l’adresse pour recharger la page. Notez que les liens apparaissent correctement.

    ![Liens alignement à droite](using-page-inspector-in-visual-studio-2012/_static/image21.png "liens alignement à droite")

    *Liens alignement à droite*
9. Enfin, vous allez modifier le titre de l’en-tête. Utilisez le mode d’inspection pour cliquer sur **votre logo ici** texte et obtenir le code source qui la génère.
10. Maintenant vous êtes dans  **\_Layout.cshtml**, remplacez «**votre logo ici**'text avec'**la galerie de photos**'. Enregistrer et mettre à jour le navigateur de l’inspecteur de Page.

    ![Affectation d’un nouveau titre](using-page-inspector-in-visual-studio-2012/_static/image22.png "affectation d’un nouveau titre")

    *Affectation d’un nouveau titre*

    ![PhotoGallerypage](using-page-inspector-in-visual-studio-2012/_static/image23.png)

    *Page de la galerie de photos mis à jour*
11. Enfin, sélectionnez le **PhotoGallery** projet, puis appuyez sur **F5** pour exécuter l’application. Vérifiez toutes les modifications fonctionnent comme prévu.

* * *

<a id="Exercise2"></a>

<a id="Exercise_2_Using_Page_Inspector_in_WebForms_Projects"></a>
### <a name="exercise-2-using-page-inspector-in-webforms-projects"></a>Exercice 2 : À l’aide de l’inspecteur de Page dans les projets Web Forms

Dans cet exercice, vous allez apprendre comment afficher un aperçu et de déboguer une solution Web Forms à l’aide de l’inspecteur de Page. Vous allez tout d’abord effectuer une brève visite guidée de l’outil pour en savoir plus les fonctionnalités de l’inspecteur de Page qui facilitent le processus de débogage de Web. Ensuite, vous allez travailler dans une page web qui contient les problèmes de conception de styles. Vous allez apprendre à utiliser l’inspecteur de Page pour rechercher le code source qui génère le problème et le résoudre.

<a id="Ex2Task1"></a>

<a id="Task_1_-_Exploring_Page_Inspector"></a>
#### <a name="task-1---exploring-page-inspector"></a>Tâche 1 - exploration de l’inspecteur de Page

Dans cette tâche, vous allez apprendre comment utiliser les fonctionnalités de l’inspecteur de Page dans le contexte d’un projet Web Forms qui affiche une galerie de photos.

1. Ouvrez le **commencer** solution situé dans **Source/Ex2-WebForms/début/** dossier.

    1. Vous devez télécharger des packages NuGet manquants avant de poursuivre. Pour ce faire, cliquez sur le **projet** menu et sélectionnez **gérer les Packages NuGet**.
    2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **restaurer** afin de télécharger les packages manquants.
    3. Enfin, générez la solution en cliquant sur **générer** | **générer la Solution**.

    > [!NOTE]
    > Un des avantages de l’utilisation de NuGet est que vous ne devez expédier toutes les bibliothèques dans votre projet, ce qui réduit la taille du projet. Avec NuGet Power Tools, en spécifiant les versions de package dans le fichier Packages.config, vous serez en mesure de télécharger toutes les bibliothèques requises à la première fois que vous exécutez le projet. C’est pourquoi vous devez exécuter ces étapes après avoir ouvert une solution existante à partir de ce laboratoire.
2. Dans l’Explorateur de solutions, recherchez **Default.aspx** page, faites un clic droit et sélectionnez **afficher dans l’inspecteur de Page**.

    ![Ouvrir Default.aspx avec l’inspecteur de Page](using-page-inspector-in-visual-studio-2012/_static/image24.png "ouverture Default.aspx avec l’inspecteur de Page")

    *Default.aspx ouverture avec l’inspecteur de Page*
3. Affiche la fenêtre de l’inspecteur de Page Default.aspx.

    ![Affichez l’inspecteur de Page Default.aspx](using-page-inspector-in-visual-studio-2012/_static/image25.png "affichage Default.aspx dans l’inspecteur de Page")

    *Affichage de Default.aspx dans l’inspecteur de Page*

    L’outil de l’inspecteur de Page est intégré dans votre environnement Visual Studio. L’inspecteur contient un navigateur intégré, ainsi que d’un profileur puissant HTML qui affiche le code sélectionné. Notez que vous n’avez pas à exécuter la solution pour voir comment vos pages.

    > [!NOTE]
    > Lorsque la largeur du navigateur de l’inspecteur de Page est inférieure à la largeur de la page ouverte, vous ne verrez pas la page correctement. Si cela se produit, ajustez la largeur de l’inspecteur de Page.
4. Cliquez sur le **fichiers** onglet dans l’inspecteur de Page.

    Vous verrez tous les fichiers sources qui sont composées de la page rendue par défaut. Il s’agit d’une fonctionnalité utile pour identifier tous les éléments en un coup de œil, surtout lorsque vous travaillez avec des contrôles utilisateur et les Pages maîtres. Notez que vous pouvez également accéder à chacun des fichiers.

    ![L’onglet fichiers](using-page-inspector-in-visual-studio-2012/_static/image26.png "les fichiers (onglet)")

    *L’onglet fichiers*
5. Cliquez sur le **basculer vers le Mode Inspection** bouton situé à gauche des onglets.

    Cet outil vous permettra de sélectionner un élément de la page et de voir son code et .aspx la source HTML.

    ![Bouton bascule du Mode d’Inspection](using-page-inspector-in-visual-studio-2012/_static/image27.png "bouton de basculer vers le Mode de contrôle")

    *Bouton bascule du Mode d’Inspection*
6. Dans le navigateur de l’inspecteur de Page, déplacez la souris sur les éléments de page. Lorsque vous déplacez le pointeur de la souris sur n’importe quelle partie de la page rendue, le type d’élément est affiché et le balisage source ou le code correspondant est mis en surbrillance dans l’éditeur Visual Studio.

    ![Mode d’inspection en action](using-page-inspector-in-visual-studio-2012/_static/image28.png "mode d’Inspection en action")

    *Mode d’inspection en action*

    > [!NOTE]
    > Pas agrandir la fenêtre de l’inspecteur de Page, ou vous ne pourrez pas voir l’onglet Aperçu affichant le code source. Si vous cliquez sur l’élément dans l’inspecteur de Page lorsqu’elle est agrandie, le code source de la sélection s’affiche, mais il sera masquer la fenêtre de l’inspecteur de Page.

    Si vous faites attention à **Default.aspx** fichier, vous pouvez remarquer que la partie du code source qui génère l’élément sélectionné est mis en surbrillance. Cette fonctionnalité facilite l’édition des fichiers de sources de long, offrant ainsi un moyen direct et rapide pour accéder au code.

    ![Inspection des éléments](using-page-inspector-in-visual-studio-2012/_static/image29.png "inspecter des éléments")

    *L’inspection des éléments*
7. Cliquez sur le **basculer vers le Mode Inspection** bouton (![Select-the-HTML-tab-to-display-the-HTML-code-rendered-in-the-Page-Inspector-browser.] (using-page-inspector-in-visual-studio-2012/_static/image30.png "Select-the-HTML-tab-to-display-the-HTML-code-rendered-in-the-Page-Inspector-browser.") ), situé dans les onglets de l’inspecteur de Page, pour désactiver le curseur.
8. Sélectionnez le **HTML** onglet pour afficher le code HTML restitué dans le navigateur de l’inspecteur de Page.
9. Dans le code HTML, recherchez l’élément de liste avec le lien Koala et sélectionnez-le.

    Notez que, lorsque vous sélectionnez le code, la sortie correspondante est automatiquement mis en surbrillance de navigateur. Cette fonctionnalité est utile pour voir comment un bloc de code HTML est rendu sur la page.

    ![Sélection d’un élément HTML dans la page](using-page-inspector-in-visual-studio-2012/_static/image31.png "en sélectionnant un élément HTML dans la page")

    *Sélection d’un élément HTML dans la page*
10. Cliquez sur le **basculer vers le Mode Inspection** bouton pour activer *Mode d’Inspection* et cliquez sur la barre de navigation. Sur la droite du code HTML, dans le volet Styles, vous verrez une liste avec les styles CSS appliquées à l’élément sélectionné.

    > [!NOTE]
    > Étant donné que l’en-tête est une partie de la mise en page du site, l’inspecteur de Page sera également ouvrir le fichier Site.Master et mettre en surbrillance le segment de code affecté.

    ![DiscoveringstylesWebForms](using-page-inspector-in-visual-studio-2012/_static/image32.png "découvrir les styles et les fichiers source d’un élément sélectionné")

    *Détection des styles et les fichiers source d’un élément sélectionné*
11. Avec le pointeur de contrôle toggle activé, placez le pointeur sous la barre de menus et cliquez sur le cercle moitié vide.

    ![Sélection d’un élément](using-page-inspector-in-visual-studio-2012/_static/image33.png "sélectionner un élément")

    *Sélection d’un élément*
12. Dans le volet Styles, localisez le **image d’arrière-plan** d’élément sous la **.main-content** groupe. **Décochez la case** le **image d’arrière-plan** et observez le résultat. Vous remarquerez que le navigateur reflète les modifications immédiatement et le cercle est masqué.

    > [!NOTE]
    > Les modifications appliquées sur l’onglet Styles de l’inspecteur de Page n’affectent pas la feuille de style d’origine. Vous pouvez désactiver des styles ou modifier leurs valeurs à chaque fois que vous le souhaitez, mais elles seront restaurées après l’actualisation de la page.

    ![Activation et désactivation de CSS styles2](using-page-inspector-in-visual-studio-2012/_static/image34.png "activation et désactivation de styles CSS")

    *Activation et désactivation de styles CSS*
13. Maintenant, cliquez sur le «**votre** **logo ici '** texte de l’en-tête à l’aide du mode d’inspection.
14. Dans le **Styles** onglet, recherchez la **taille de police** CSS attribut sous la **.site-titre** groupe. Double-cliquez sur l’attribut à la fois pour modifier sa valeur. Valeur de remplacement le 2.3em avec **3em**, puis appuyez sur ENTRÉE. Notez que le titre de la recherche la plus grand.

    ![Modifier les valeurs CSS dans la Page Inspector2](using-page-inspector-in-visual-studio-2012/_static/image35.png "valeurs CSS de la modification dans l’inspecteur de Page")

    *Modification des valeurs CSS dans l’inspecteur de Page*
15. Cliquez sur le **suivre les Styles** onglet, situé dans le volet droit de l’inspecteur de Page. Il s’agit d’une autre façon de voir tous les styles appliqués à la sélection, classée par nom d’attribut.

    ![Le suivi des styles CSS de l’élément sélectionné](using-page-inspector-in-visual-studio-2012/_static/image36.png "le suivi des styles CSS de l’élément sélectionné")

    *Suivi des styles CSS de l’élément sélectionné*
16. Une autre fonctionnalité de l’inspecteur de Page est le volet de disposition. Le mode d’inspection, sélectionnez la barre de navigation, puis cliquez sur le **disposition** onglet dans le volet droit. Vous verrez la taille exacte de l’élément sélectionné, ainsi que sa taille des bordures, offset, marge et marge intérieure. Notez que vous pouvez également modifier les valeurs de cette vue.

    ![Disposition de l’élément dans l’inspecteur de Page](using-page-inspector-in-visual-studio-2012/_static/image37.png "disposition de l’élément dans l’inspecteur de Page")

    *Disposition de l’élément dans l’inspecteur de Page*

<a id="Ex2Task2"></a>

<a id="Task_2_-_Finding_and_Fixing_Style_Issues_in_the_Photo_Gallery"></a>
#### <a name="task-2---finding-and-fixing-style-issues-in-the-photo-gallery"></a>Tâche 2 : recherche et la résolution des problèmes de Style dans la galerie de photos

Comment serait pour diagnostiquer les problèmes de pages Web avec les versions précédentes de Visual Studio ? Vous êtes probablement habitué à web outils de débogage qui sont exécutées en dehors de l’IDE de Visual Studio, tels que les outils de développement Internet Explorer ou Firebug. Navigateurs ne comprennent que HTML, scripts et les styles, une infrastructure sous-jacente génère le code HTML qui est affiché. Pour cette raison, vous devez souvent déployer l’ensemble du site pour voir comment les pages web ressemblent.

Vous aviez probablement suivi ces étapes lorsque vous souhaitez détecter et résoudre un problème dans votre site web :

1. Exécutez la Solution à partir de Visual Studio, ou déployer la page sur le serveur web.
2. Dans le navigateur, ouvrez les outils de développement, vous utilisez, ou ouvrez simplement le code source et les styles et tentez de faire correspondre le problème. Pour rechercher les fichiers concernés, vous auriez utilisé le &quot;recherche&quot; ou &quot;recherche dans les fichiers&quot; fonctionnalités avec le nom des classes de style.
3. Une fois que l’erreur est détectée, arrêtez le navigateur Web et le serveur.
4. Effacez le cache du navigateur.
5. Revenez à Visual Studio pour appliquer un correctif. Répétez les étapes à tester.

Comme il n’existe aucun réel WYSIWYG dans ASP.NET WebForms, certains problèmes de style sont détectés sur un stade ultérieur, après l’exécution ou le déploiement. Désormais, avec l’inspecteur de Page, il est possible afficher un aperçu de n’importe quelle page sans exécuter la solution.

Dans cette tâche, vous allez utiliser l’inspecteur de Page pour la résolution des problèmes de l’application de la galerie de photos. Dans les étapes suivantes, vous allez détecter et résoudre rapidement un problème de style simple dans l’en-tête.

1. À l’aide du contrôle de Page, recherchez le **inscrire** et **journal dans** des liens situés à gauche de l’en-tête.

    Notez que le lien n’est pas affiché à l’emplacement attendu sur la droite. Vous allez maintenant aligner le lien vers la droite et remplacer en conséquence.

    ![Connectez-vous au lien positionné sur la gauche](using-page-inspector-in-visual-studio-2012/_static/image38.png "connecter lien positionné sur la gauche")

    *Journal de liaison positionné à gauche*
2. Basculer vers le Mode Inspection sélectionné, sélectionnez le lien se connecter pour ouvrir son code.

    Notez que le code source de liaison se trouve dans le **Site.Master** pas de fichiers, dans la page Default.aspx, qui est l’emplacement que vous peut se présenter en premier lieu ; vous avez été placés directement dans le fichier de code source approprié.
3. Dans le **Styles** onglet, recherchez et cliquez sur le  **&lt;section&gt; #login** élément, qui est le conteneur HTML pour que ces liens.

    Notez que la **#login** style se trouve automatiquement dans **Site.css** après avoir cliqué sur. En outre, le code est maintenant en surbrillance.

    ![En sélectionnant les styles CSS](using-page-inspector-in-visual-studio-2012/_static/image39.png "en sélectionnant les styles CSS")

    *En sélectionnant les styles CSS*
4. Ne pas commenter le **d’alignement du texte** d’attribut dans le code en surbrillance en supprimant les caractères ouvrant et fermant et enregistrer le **Site.css** fichier.

    L’inspecteur de page tient compte de tous les fichiers qui composent la page actuelle, et il peut détecter lorsqu’un de ces fichiers change. Il vous avertit chaque fois que la page actuelle dans le navigateur n’est pas synchronisée avec les fichiers sources.
5. Dans le navigateur de l’inspecteur de Page, cliquez sur la barre située sous la barre d’adresses pour enregistrer les modifications et recharger la page.

    ![Reloadingthepage](using-page-inspector-in-visual-studio-2012/_static/image40.png)

    *Recharger la page*

    Les liens sont désormais à droite, mais ils ressemblent toujours une liste à puces. Maintenant, vous allez supprimer les puces et aligner les liens horizontalement.

    ![Page mise à jour](using-page-inspector-in-visual-studio-2012/_static/image41.png)

    *Page mise à jour*
6. À l’aide du mode d’inspection, sélectionnez un de la  **&lt;li&gt;**  les éléments qui contiennent la &quot;inscrire&quot; et &quot;connectez-vous&quot; des liens. Ensuite, cliquez sur le  **&lt;section&gt; #login** d’élément pour l’accès **Styles.css** code.

    ![Recherche le style](using-page-inspector-in-visual-studio-2012/_static/image42.png "recherche le style")

    *Recherche le style*
7. Dans **Style.css**, les commentaires du code pour **#login li** éléments. Le style que vous ajoutez sera masquer la puce et afficher les éléments horizontalement.

    ![Modification du style les liens de connexion](using-page-inspector-in-visual-studio-2012/_static/image43.png "modification du style les liens de connexion")

    *Modification du style les liens de connexion*
8. Enregistrer **Style.css** de fichier et cliquez une fois dans la barre située au-dessous de l’adresse pour recharger la page. Notez que les liens apparaissent correctement.

    ![Liens alignement à droite](using-page-inspector-in-visual-studio-2012/_static/image44.png "liens alignement à droite")

    *Liens alignement à droite*
9. Enfin, vous allez modifier le titre de l’en-tête. Au lieu de la recherche de la «**votre logo ici '** texte dans tous les fichiers, utilisez le mode d’inspection cliquer sur le texte et d’atteindre le code source qui génère.

    ![Recherche le titre du site](using-page-inspector-in-visual-studio-2012/_static/image45.png "recherche le titre du site")

    *Recherche le titre du site*
10. Maintenant vous êtes dans **Site.Master**, remplacez le «**votre logo ici**'text avec'**la galerie de photos**'. Enregistrer et mettre à jour le navigateur de l’inspecteur de Page.

    ![Page de la galerie de photos mis à jour](using-page-inspector-in-visual-studio-2012/_static/image46.png "page de la galerie de photos mis à jour")

    *Page de la galerie de photos mis à jour*
11. Enfin appuyez **F5** pour exécuter l’application de la vérification de toutes les modifications fonctionnent comme prévu.

* * *

<a id="Summary"></a>

<a id="Summary"></a>
## <a name="summary"></a>Résumé

À la fin de cet atelier pratique, vous avez appris comment utiliser l’inspecteur de Page pour afficher un aperçu de votre application Web sans avoir à reconstruire, puis exécuter le site Web dans un navigateur. En outre, vous avez appris comment rapidement rechercher et résoudre les bogues en accédant directement à partir de la sortie rendue au code source.

<a id="AppendixA"></a>

<a id="Appendix_A_Installing_Visual_Studio_Express_2012_for_Web"></a>
## <a name="appendix-a-installing-visual-studio-express-2012-for-web"></a>Annexe a : installation de Visual Studio Express 2012 pour le Web

Vous pouvez installer **Microsoft Visual Studio Express 2012 pour Web** ou un autre &quot;Express&quot; à l’aide de la version du  **[Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx)** . Les instructions suivantes vous guident à travers les étapes requises pour installer *Visual studio Express 2012 pour le Web* à l’aide de *Microsoft Web Platform Installer*.

1. Accédez à [ [https://go.microsoft.com/?linkid=9810169](https://go.microsoft.com/?linkid=9810169)](https://go.microsoft.com/?linkid=9810169). Sinon, si vous avez déjà installé Web Platform Installer, vous pouvez ouvrir il et recherchez le produit &quot; *Visual Studio Express 2012 pour le Web avec Windows Azure SDK*&quot;.
2. Cliquez sur **installer maintenant**. Si vous n’avez pas **Web Platform Installer** vous allez être redirigé pour télécharger et installer tout d’abord.
3. Une fois **Web Platform Installer** est ouvert, cliquez sur **installer** pour démarrer le programme d’installation.

    ![Installer Visual Studio Express](using-page-inspector-in-visual-studio-2012/_static/image47.png "installer Visual Studio Express")

    *Installer Visual Studio Express*
4. Lisez les termes et les licences de tous les produits et cliquez sur **J’accepte** pour continuer.

    ![Accepter les termes du contrat de licence](using-page-inspector-in-visual-studio-2012/_static/image48.png)

    *Accepter les termes du contrat de licence*
5. Attendez que le processus de téléchargement et l’installation se termine.

    ![Progression de l'installation](using-page-inspector-in-visual-studio-2012/_static/image49.png)

    *Progression de l’installation*
6. Une fois l’installation terminée, cliquez sur **Terminer**.

    ![Installation est terminée](using-page-inspector-in-visual-studio-2012/_static/image50.png)

    *Installation est terminée*
7. Cliquez sur **Exit** fermer Web Platform Installer.
8. Pour ouvrir Visual Studio Express pour le Web, accédez à la **Démarrer** écran et démarrer l’écriture &quot; **VS Express**&quot;, puis cliquez sur le **Visual Studio Express pour le Web** vignette.

    ![VS Express pour la vignette du Web](using-page-inspector-in-visual-studio-2012/_static/image51.png)

    *VS Express pour la vignette du Web*
