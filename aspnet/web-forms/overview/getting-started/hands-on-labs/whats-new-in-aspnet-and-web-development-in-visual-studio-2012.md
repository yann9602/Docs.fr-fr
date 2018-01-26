---
uid: web-forms/overview/getting-started/hands-on-labs/whats-new-in-aspnet-and-web-development-in-visual-studio-2012
title: "Nouveautés de développement ASP.NET et Web dans Visual Studio 2012 | Documents Microsoft"
author: rick-anderson
description: "La nouvelle version de Visual Studio intègre plusieurs améliorations centrée sur l’amélioration de l’expérience et les performances lorsque vous travaillez avec des technologies Web..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/18/2013
ms.topic: article
ms.assetid: 6d40d276-1642-4a77-b6c9-02ac914f6805
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/getting-started/hands-on-labs/whats-new-in-aspnet-and-web-development-in-visual-studio-2012
msc.type: authoredcontent
ms.openlocfilehash: f0818cce2a82ede80556b3471cec9d965c3e987f
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="whats-new-in-aspnet-and-web-development-in-visual-studio-2012"></a>Nouveautés de développement ASP.NET et Web dans Visual Studio 2012
====================
par [Web Camps équipe](https://twitter.com/webcamps)

> La nouvelle version de Visual Studio présente plusieurs améliorations centrée sur l’amélioration de l’expérience et les performances lorsque vous travaillez avec les technologies Web. Les éditeurs de Visual Studio pour CSS, JavaScript et HTML ont été complètement repensées pour inclure de nombreux les aides de code plus dans la demande, comme IntelliSense et de mise en retrait automatique. En ce qui concerne les performances, groupement et la minimisation sont désormais intégrées comme le temps de chargement des fonctionnalités intégrées pour réduire facilement la page.
> 
> Visual Studio vous permet de travailler avec les dernières technologies de site Web. Vous pouvez utiliser des extraits de code CSS3 entre navigateurs pour vous assurer que votre site fonctionne indépendamment de la plateforme du client tout en bénéficiant des éléments de HTML5 les nouvelles fonctionnalités.
> 
> L’écriture et le profilage de code JavaScript doivent être plus faciles avec cette version de Visual Studio. Les listes IntelliSense, fonctionnalités de navigation et de la documentation XML intégrées sont désormais disponibles pour le code JavaScript. Vous avez maintenant le catalogue JavaScript à portée de main. En outre, vous pouvez vérifier la conformité ECMAScript5 avec vos scripts et détecter des erreurs de syntaxe à un stade précoce.
> 
> Dernier mais pas moins, cette version de Visual Studio implémente groupement intégrés et la minimisation. Vos fichiers de script et les feuilles de style sont compressées et compressés afin que le site s’exécute plus vite.
> 
> Ce laboratoire présente les améliorations et nouvelles fonctionnalités décrites précédemment en appliquant les modifications mineures à un exemple d’application Web dans le dossier Source.
> 
> Tous les exemples de code et des extraits de code sont inclus dans le Kit de formation Camps Web, disponible à l’adresse [https://go.microsoft.com/fwlink/?LinkID=248297&clcid=0x409](https://go.microsoft.com/fwlink/?LinkID=248297&clcid=0x409).


<a id="Objectives"></a>

<a id="Objectives"></a>
### <a name="objectives"></a>Objectifs

Dans ce laboratoire pratique, vous allez apprendre comment :

- Utiliser les nouvelles fonctionnalités et améliorations dans l’éditeur CSS
- Utiliser les nouvelles fonctionnalités et améliorations dans l’éditeur HTML
- Utiliser les nouvelles fonctionnalités et améliorations dans l’éditeur JavaScript
- Configurer et utiliser le groupement et la minimisation

<a id="Prerequisites"></a>

<a id="Prerequisites"></a>
### <a name="prerequisites"></a>Prérequis

- [Microsoft Visual Studio Express 2012 pour Web](https://www.microsoft.com/visualstudio/eng/products/visual-studio-express-for-web) ou supérieure (lecture [annexe A](#AppendixA) pour obtenir des instructions sur l’installation).
- [Windows PowerShell](https://support.microsoft.com/kb/968930/) (pour les scripts de configuration - déjà installés sur Windows 8 et Windows Server 2008 R2)
- [Internet Explorer 10](https://windows.microsoft.com/internet-explorer/products/ie/home) - ou un navigateur compatible HTML5

<a id="Exercises"></a>

<a id="Exercises"></a>
## <a name="exercises"></a>Exercices

Ce laboratoire pratique sur comprend les exercices suivants :

1. [Exercice 1 : Quelles sont les nouveautés dans l’éditeur CSS](#Exercise1)
2. [Exercice 2 : Quelles sont les nouveautés dans l’éditeur HTML](#Exercise2)
3. [Exercice 3 : Quelles sont les nouveautés dans l’éditeur JavaScript](#Exercise3)
4. [Exercice 4 : Groupement et minimisation](#Exercise4)

Durée estimée pour effectuer ce laboratoire : **60 minutes**.

<a id="Exercise1"></a>

<a id="Exercise_1_Whats_New_in_the_CSS_Editor"></a>
### <a name="exercise-1-whats-new-in-the-css-editor"></a>Exercice 1 : Quelles sont les nouveautés dans l’éditeur CSS

Les développeurs Web doivent être familiarisés avec la plupart des difficultés liées à la modification de CSS. Une des plus grandes difficultés de styles CSS est la compatibilité entre navigateurs. Il arrive souvent que, après l’application des styles à votre site, vous remarquerez qu’il se présente différemment si vous l’ouvrez dans un autre navigateur ou l’appareil. Par conséquent, vous pouvez passer un temps considérable pour résoudre ces problèmes visual pour réaliser que, lorsque vous enfin faciliter le travail dans un navigateur, elle est scindée dans les autres.

Visual Studio inclut désormais des fonctionnalités qui aident les développeurs d’accès, de travail et d’organiser efficacement les feuilles de style CSS. Tout au long de cet exercice, vous répond aux nouvelles fonctionnalités pour une organisation efficace et d’édition, ainsi que les extraits de Code CSS3 pour la compatibilité entre navigateurs.

<a id="Ex1Task1"></a>

<a id="Task_1_-_New_Editor_Features"></a>
#### <a name="task-1---new-editor-features"></a>Tâche 1 : nouvelles fonctionnalités de l’éditeur

Dans cette tâche, vous allez découvrir les nouvelles fonctionnalités de l’éditeur CSS. Ce nouvel éditeur vous aidera à augmenter votre productivité en tirant parti de la nouvelle mise en retrait intelligente, les commentaires de code améliorée et la liste IntelliSense améliorée.

1. Démarrer **Visual Studio** et ouvrez le **WhatsNewASPNET.sln** solution situé dans le **Source\WhatsNewASPNET** dossier de ce laboratoire.
2. Dans l’Explorateur de solutions, ouvrez le **Site.css** fichier situé dans le **Styles** dossier. Assurez-vous que le **éditeur de texte** outils sont visibles dans la barre d’outils. Pour ce faire, sélectionnez le **vue** | **barres d’outils** option de menu et recherchez le **éditeur de texte** options. Vous remarquerez que, depuis cette nouvelle version, le **commentaire** bouton (![-bouton commentaires](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image1.png) ) et le **Décommentez** bouton (![supprimez les commentaires sur le bouton](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image2.png)) sont également activés pour l’éditeur CSS.

    ![L’activation de l’éditeur et outils CSS](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image3.png "l’activation de l’éditeur et outils CSS")

    *L’activation de l’éditeur et outils CSS*
3. Faites défiler le code et sélectionnez une définition de classe CSS. Cliquez sur le **commentaire** (![-bouton commentaires](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image4.png) ) bouton pour commenter les lignes sélectionnées. Ensuite, cliquez sur le **Décommentez** (![sur le bouton Supprimer les commentaires](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image5.png) ) bouton pour annuler les modifications.
4. Cliquez sur le **réduire** (![réduire](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image6.png) ) et **Expand** (![développez](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image7.png) ) les boutons situés dans la marge gauche du texte. Notez que vous pouvez à présent masquer les styles que vous n’utilisez pas d’avoir une vue plus conviviale.

    ![Réduction des classes CSS](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image8.png "classes CSS de réduction")

    *Réduction des classes CSS*
5. Assurez-vous que la fonctionnalité de mise en retrait intelligente est activée. Sélectionnez le **outils** | **Options** option de menu, puis sélectionnez le **éditeur de texte** | **CSS**  |  **Mise en forme** page dans le volet gauche de l’écran. Vérifiez le **mise en retrait hiérarchique** option.

    ![L’activation de mise en retrait hiérarchique](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image9.png "l’activation de mise en retrait hiérarchique")

    *L’activation de mise en retrait hiérarchique*
6. Recherchez la définition de la classe principale (.main) et ajouter un style aux éléments div. Vous remarquerez que le code s’aligne automatiquement, aider les utilisateurs pour trouver les classes parent un coup de œil.

    CSS

    [!code-css[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample1.css)]

    ![Alignement hiérarchique dans CSS](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image10.png "alignement hiérarchique dans CSS")

    *Alignement hiérarchique dans CSS*
7. À l’intérieur de **.main div** de classe, recherchez le curseur à la fin de **bordure : 0px ;** et appuyez sur **entrée** pour afficher la liste IntelliSense. Commencez à taper **haut** et notez la façon dont la liste est filtrée en cours de frappe. La liste affiche les éléments qui contiennent des **haut** à n’importe quelle partie du mot (dans les versions antérieures de Visual Studio, la liste est filtrée par les éléments qui *commencer* avec le terme).

    ![Améliorations d’IntelliSense dans CSS](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image11.png "améliorations IntelliSense dans CSS")

    *Améliorations d’IntelliSense dans CSS*

<a id="Ex1Task2"></a>

<a id="Task_2_-_The_Color_Picker"></a>
#### <a name="task-2---the-color-picker"></a>Tâche 2 : le sélecteur de couleurs

Dans cette tâche, vous allez découvrir que le nouveau sélecteur de couleurs CSS intégré dans Visual Studio IntelliSense.

1. Dans **Site.css,** trouver la définition de classe d’en-tête (.header) et placez le curseur à côté **couleur d’arrière-plan** attribut entre les &quot;:&quot; et &quot; # &quot; caractères sur la ligne de code **.**

    ![Localisation du curseur](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image12.png "recherche le curseur")

    *Localisation du curseur*
2. Supprimer le **deux-points** ( :) et de les écrire à nouveau pour afficher le sélecteur de couleurs. Notez que les couleurs de premier que vous verrez les couleurs les plus fréquentes de votre site. Si vous cliquez sur la couleur blanche, son code de couleur HTML (#fff) remplace le code de couleur actuelle dans la feuille de style.

    ![Sélecteur de couleurs](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image13.png "sélecteur de couleurs")

    *Sélecteur de couleurs*
3. Appuyez sur la **Expand** (![com](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image14.png) ) bouton sur le sélecteur de couleurs pour afficher le dégradé de couleur, puis faites glisser le curseur de dégradé pour sélectionner une couleur différente. Après cela, cliquez sur le **pipette** bouton et sélectionner une couleur à partir de l’écran. Notez que valeur de couleur d’arrière-plan change de manière dynamique pendant que vous déplacez le curseur.

    ![Dégradé de sélecteur de couleurs](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image15.png "dégradé de sélecteur de couleurs")

    *Dégradé de sélecteur de couleurs*
4. Dans le **opacité** curseur, déplacez le sélecteur pour le centre de la barre pour réduire l’opacité. Notez que la valeur de couleur d’arrière-plan s’affiche désormais son échelle RVBA.

    ![Sélecteur de couleurs opacité](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image16.png "sélecteur Couleur d’opacité")

    *Sélecteur Couleur d’opacité*

    > [!NOTE]
    > La définition de la couleur RGBA (rouge, vert, bleu, Alpha) dans CSS3 vous permet de définir la valeur d’opacité de couleur pour un seul élément. Contrairement aux **opacité -** un attribut CSS semblable  **-**  RGBA couleurs sont également compatibles avec les navigateurs les plus récents.

<a id="Ex1Task3"></a>

<a id="Task_3_-_CSS_Compatible_Code_Snippets"></a>
#### <a name="task-3---css-compatible-code-snippets"></a>Tâche 3 : les extraits de Code Compatible de CSS

Dans cette tâche, vous allez apprendre comment utiliser des extraits de code de CSS3 compatibles entre navigateurs pour implémenter certaines fonctionnalités dans votre site Web.

1. Dans le **Site.css** de fichiers, recherchez le **en-tête** CSS (.header) de définition de classe et placez le curseur ci-dessous le  **/ \*radius de bordure\* /**  espace réservé pour ajouter un nouvel extrait de code. Appuyez sur **entrée** pour afficher la liste IntelliSense et le type **radius** pour filtrer la liste. Sélectionnez le **valeurs border-radius** option dans la liste avec un double-clic, puis appuyez sur la **onglet** touche pour insérer l’extrait de code. Ensuite, tapez une taille de rayon en pixels et appuyez sur **entrée**. Par exemple, tapez **15px**.

    Les attributs CSS3 ajoutées par l’extrait de code seront restituées bordures arrondies dans la plupart des navigateurs de conformité HTML5, y compris Mozilla et les navigateurs WebKit.

    ![À l’aide d’un extrait de valeurs border-radius](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image17.png "à l’aide d’un extrait de valeurs border-radius")

    *À l’aide d’un extrait de valeurs border-radius*
2. Appliquer la même **bordure** des extraits de code dans le style de page (.page).

    CSS

    [!code-css[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample2.css)]
3. Appuyez sur **F5** pour exécuter la solution. Notez que chaque page possède désormais arrondi des bordures.

    ![Des angles arrondis](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image18.png "des angles arrondis")

    *Angles arrondis*
4. Fermez le navigateur et revenez à Visual Studio.
5. Ouvrez le **Custom.css** fichier situé dans le **Styles** dossier et placez le curseur à l’intérieur de **div.images ul li img** définition de classe.
6. Appuyez sur ENTRÉE pour afficher la liste IntelliSense, type **clichés instantanés de la boîte de** et appuyez sur la **onglet** clé à deux reprises pour insérer l’extrait de code de clichés instantanés par défaut à l’intérieur de la définition de classe. Définissez les valeurs de clichés instantanés sur **10px 10px 5px #888**. Ensuite, tapez **valeurs border-radius** et insérer l’extrait de code. Type **15px** pour définir la taille du rayon et appuyez sur **entrée**.

    ![Arrondi des angles avec ombre](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image19.png "arrondi des angles avec ombre")

    *Angles arrondis avec ombre*

    > [!NOTE]
    > Pour l’instant, l’attribut de clichés instantanés est insérée avec le préfixe correspondant (moz webkit, o) pour prendre en charge de Mozilla et les navigateurs Webkit (Chrome, Safari, Konkeror).
7. Créer une nouvelle classe **div.images ul li img:hover** sous le **div.images ul li img** définition de classe et placez le curseur entre les crochets **.**

    CSS

    [!code-css[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample3.css)]
8. Type **transformer** et appuyez sur la **onglet** clé à deux reprises pour insérer l’extrait de code de transformation. Ensuite, entrez **rotate(-15deg)** pour modifier la valeur d’angle de rotation lorsque trouve des images.

    CSS

    [!code-css[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample4.css)]
9. Appuyez sur **F5** pour exécuter la solution, puis accédez à la page CSS3. Notez que les images ont des angles arrondis et zone ombres. Pointez la souris sur les images et les regarder à faire pivoter.

    ![Transformer l’extrait de faire pivoter une image](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image20.png "extrait de code de transformation de rotation d’une image")

    *Transformer l’extrait de faire pivoter une image*

    > [!NOTE]
    > Si vous utilisez Internet Explorer 10 et que vous ne pouvez pas voir les ombres, assurez-vous que le mode de document est défini sur les normes Internet Explorer 10. Appuyez sur **F12** pour ouvrir les outils de développement Internet Explorer sur **Mode Document** pour convertir des normes Internet Explorer 10.

    ![about-us](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image21.png)

<a id="Exercise2"></a>

<a id="Exercise_2_Whats_New_in_the_HTML_Editor"></a>
### <a name="exercise-2-whats-new-in-the-html-editor"></a>Exercice 2 : Quelles sont les nouveautés dans l’éditeur HTML

Visual Studio propose un éditeur HTML amélioré. Les améliorations incluses dans cette version sont la mise en retrait intelligente dans des documents HTML, des extraits de code HTML5, début HTML et correspondance de balise de fin et la validation HTML. Dans cet exercice, vous verrez comment ces modifications améliorent votre maîtrise lorsque vous travaillez dans le balisage de site Web.

Comme l’éditeur CSS, l’éditeur HTML a été également améliorée. La plupart de ces améliorations est petits qui facilitent la durée de vie du développeur Web. Par exemple plusieurs extraits de code pour HTML5, mise en retrait intelligente, correspondance des balises de début et de fin lors de l’édition et validation ciblant le DOCTYPE du document HTML sont certaines de ces améliorations.

<a id="Ex2Task1"></a>

<a id="Task_1_-_Improved_DOCTYPE_Validation"></a>
#### <a name="task-1---improved-doctype-validation"></a>Tâche 1 : Validation de DOCTYPE améliorée

L’éditeur HTML a désormais la possibilité de vérifier le type de document de votre page, même si la définition peut être dans la page maître. Selon le type de document de votre page, l’éditeur HTML valide avec l’ensemble approprié de règles et filtre la liste IntelliSense prise en compte les éléments de type de document.

Dans cette tâche, vous allez modifier la déclaration DOCTYPE d’une page pour voir comment le comportement de l’éditeur HTML change en conséquence.

1. Si pas déjà ouvert, démarrez **Visual Studio** et ouvrez le **WhatsNewASPNET.sln** solution situé dans le **Source\WhatsNewASPNET** dossier de ce laboratoire.
2. Ouvrez le **Site.Master** page.
3. Notez que le schéma cible pour la barre d’outils de la Validation. Le comportement de l’éditeur HTML (Validation, IntelliSense, etc.) correctement change pour ajuster le type de document sélectionné.

    ![Utiliser Doctype dans la barre d’outils de modification de code Source HTML](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image22.png "Doctype d’utilisation dans la barre d’outils de modification de code Source HTML")

    *Utilisez le type de document dans la barre d’outils de modification de code Source HTML*
4. Modifier le schéma cible au format HTML 4.01.

    ![La modification de type de document dans la barre d’outils de modification de code Source HTML](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image23.png "modifier le type de document dans la barre d’outils de modification de code Source HTML")

    *La modification de type de document dans la barre d’outils de modification de code Source HTML*
5. Placez le curseur sous le **corps** élément et commencez à taper le nom d’un élément HTML5 (par exemple, **vidéo**). Notez que l’élément n’est pas disponible dans la liste IntelliSense.

    ![Ne pas dans la liste des éléments de HTML5](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image24.png "ne pas dans la liste des éléments de HTML5")

    *Éléments HTML5 non répertoriés*
6. Annuler les modifications apportées au schéma cible pour la barre d’outils de la Validation, prélèvement DOCTYPE : XHTML5 dans la liste déroulante.

    ![Utiliser Doctype dans la barre d’outils de modification de code Source HTML](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image25.png "Doctype d’utilisation dans la barre d’outils de modification de code Source HTML")

    *Réinitialiser le type de document dans la barre d’outils de modification de code Source HTML*
7. Placez le curseur sous le **corps** élément et commencez à taper de nouveau un élément HTML5 (par exemple, comme **vidéo**). Notez que les éléments HTML5 sont maintenant disponibles dans la liste IntelliSense.

    ![Éléments HTML5 en cours répertoriés](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image26.png "HTML5 énumérées en cours")

    *HTML5 énumérées en cours*

<a id="Ex2Task2"></a>

<a id="Task_2_-_StartEnd_Tags_Automatic_Update"></a>
#### <a name="task-2---startend-tags-automatic-update"></a>Tâche 2 - début et de fin mise à jour automatique des étiquettes

Visual Studio met maintenant à jour le code HTML ouverture ou la fermeture des balises de l’élément que vous modifiez pour correspondre à l’autre. Cette nouvelle fonctionnalité améliore votre productivité lorsque vous modifiez des balises HTML.

1. Sur le **Default.aspx** , ajoutez une **H3** élément avec un titre (par exemple, Visual Studio 2012 Rocks !).


    [!code-aspx[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample5.aspx)]
2. Modifier la **H3** balise et le type **H2** ou **H1.**

    Notez que la balise de fin sont automatiquement mises à jour. Vous pouvez également modifier la balise de fin pour voir que la balise de début des mises à jour en conséquence trop.

    ![Mise à jour automatique de la balise de fin](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image27.png "mise à jour automatique de la balise de fin")

    *Mise à jour automatique de la balise de fin*

<a id="Ex2Task3"></a>

<a id="Task_3_-_New_HTML5_Code_Snippets"></a>
#### <a name="task-3---new-html5-code-snippets"></a>Tâche 3 : nouveaux extraits de Code HTML5

Visual Studio inclut désormais plusieurs extraits de code HTML5. Dans cette tâche, vous allez utiliser certains de ces extraits de code.

1. Ajouter un nouveau dossier nommé **audio** à la racine du dossier du site web. Ouvrez l’Explorateur Windows et copiez tout fichier audio dans le **audio** dossier de la **WhatsNewASPNET.sln** solution.
2. Dans le **Default.aspx** , recherchez le curseur sous le Rocks Web11 !! En-tête. Type **audio** et appuyez sur la touche TAB.

    Le nouvel éditeur HTML inclut des extraits de code pour le contenu de HTML5. Pensez à utiliser la définition de type de document appropriée pour activer les extraits de code HTML5.

    ![Insertion d’extraits de Code HTML5](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image28.png "insertion d’extraits de Code HTML5")

    *Insertion d’extraits de Code HTML5*
3. Mettre à jour la source audio pour pointer vers un fichier audio.


    [!code-aspx[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample6.aspx)]

    > [!NOTE]
    > Vous devez ajouter le fichier audio à la solution.
4. Appuyez sur **F5** pour exécuter le site et de lecture du fichier audio.

    ![Exécution du contrôle audio](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image29.png "en cours d’exécution du contrôle audio")

    *Exécution du contrôle audio*

    > [!NOTE]
    > Vous pouvez également essayer plusieurs extraits de code inclus dans Visual Studio, telles que la vidéo, figure, etc.
5. À présent, essayez d’insérer un contrôle dans une partie de la page. Par exemple, essayez d’insérer un **GridView** (contrôle), mais au lieu de taper  **&lt;Gri,** commencez à taper  **&lt;GV**. Notez que la liste IntelliSense affiche la **asp : GridView** contrôle.

    IntelliSense dans l’éditeur HTML fournit désormais la recherche de titre de la casse, mais aussi partielle correspondance (la récupération de tous les éléments qui contient le terme).

    ![Insertion d’un GridView avec les listes IntelliSense](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image30.png "insérant un GridView avec les listes IntelliSense")

    *Insertion d’un GridView avec les listes IntelliSense*

    Si vous tapez  **&lt;grille** vous obtenez tous les éléments qui correspondent au terme, mais Visual Studio propose le **gridview** contrôle :

    ![Insertion d’un GridView avec les listes IntelliSense et la correspondance partielle](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image31.png "insérant un GridView avec les listes IntelliSense et la correspondance partielle")

    *Insertion d’un GridView avec les listes IntelliSense et la correspondance partielle*

<a id="Ex2Task4"></a>

<a id="Task_4_-_HTML_Editor_Smart_Tags"></a>
#### <a name="task-4---html-editor-smart-tags"></a>Tâche 4 : balises à puce de l’éditeur HTML

Une autre amélioration dans l’éditeur HTML est la fonctionnalité de balises actives. Balises actives permettent de facilement effectuer les tâches de développement courantes ou répétitives sur une base par contrôle. Cette fonctionnalité était déjà disponible dans le Concepteur HTML, mais pas dans l’éditeur HTML.

1. Ouvrez **Site.Master** et recherchez le **asp : Menu** élément. Placez le curseur sur la balise de début et notez que le petit glyphe affiché en bas de l’élément - cliquez dessus pour ouvrir le menu de balise active. Notez que vous avez un accès rapide à certaines tâches liées au contrôle de Menu.

    ![Actives des tâches pour le contrôle de Menu](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image32.png "actives des tâches pour le contrôle de Menu")

    *Tâches actives pour le contrôle de Menu*

<a id="Ex2Task5"></a>

<a id="Task_5_-_Smart_Indentation"></a>
#### <a name="task-5---smart-indentation"></a>Tâche 5 : mise en retrait intelligente

Une des meilleures pratiques de HTML est mise en retrait les éléments imbriqués pour que le code reste accessible en lecture. Dans Visual Studio 2012, vous remarquerez que l’éditeur met en retrait automatiquement les éléments lors de l’écriture du code.

> [!NOTE]
> Dans la version précédente de Visual Studio, la mise en retrait intelligente était disponible dans l’éditeur XML mais pas dans l’éditeur HTML.


1. Assurez-vous que la configuration de mise en retrait dans l’éditeur HTML est définie pour la mise en retrait intelligente. Pour ce faire, sélectionnez le **outils | Options** option de menu, puis sélectionnez le **éditeur de texte | HTML | Onglets** page dans le volet gauche de l’écran. Sélectionnez l’option de mise en retrait intelligente.

    ![Paramètres de l’éditeur HTML](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image33.png "paramètres de l’éditeur HTML")

    *Paramètres de l’éditeur HTML*
2. Sur le **Default.aspx** page, supprimez tout le contenu sous l’élément audio.
3. Placez le curseur à la fin de l’ouverture **audio** élément et appuyez sur **entrée**.

    Notez que la nouvelle position de curseur a un niveau supplémentaire de mise en retrait.

    ![Active la mise en retrait dans l’éditeur HTML](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image34.png "actives de la mise en retrait dans l’éditeur HTML")

    *Mise en retrait intelligente dans l’éditeur HTML*
4. Restaurer la balise audio avec le contenu que vous avez supprimé, ou fermez **Default.aspx** sans enregistrer les modifications.

<a id="Ex2Task6"></a>

<a id="Task_6_-_Extract_to_User_Control"></a>
#### <a name="task-6---extract-to-user-control"></a>Tâche 6 : extraire à contrôle utilisateur

Les outils de refactorisation inclus dans Visual Studio, telles que l’extraction d’une partie du code à une fonction, sont des fonctionnalités qui facilitent l’amélioration et la refactorisation du code existant. L’extraction de code HTML à un contrôle utilisateur est l’équivalent pour les pages ASP.NET. Effectuer l’opération manuellement implique plusieurs étapes, comme la création d’un contrôle utilisateur, le déplacement de la section de code pour le contrôle utilisateur, l’inscription d’un préfixe de balise pour le contrôle utilisateur et, enfin, l’instanciation du contrôle utilisateur dans les pages. Désormais, le nouveau *extraire à contrôle utilisateur* outil effectue automatiquement toutes ces étapes pour vous.

Dans cette tâche, vous utiliserez l’extraction de nouveau à l’opération contextuelle de contrôle utilisateur pour générer un nouveau contrôle utilisateur à partir du code sélectionné.

1. Sur le **Default.aspx** page, sélectionnez le **H2** et **audio** éléments.
2. Clic droit et sélectionnez **extraire à contrôle utilisateur**.

    ![Extraire à l’option de menu de contrôle utilisateur](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image35.png "extraire à l’option de menu de contrôle utilisateur")

    *Extraire à l’option de menu de contrôle utilisateur*
3. Tapez un nom pour le nouveau contrôle utilisateur. Par exemple, **Jukebox.ascx**, puis cliquez sur **OK**.

    ![L’enregistrement du contrôle utilisateur extrait](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image36.png "l’enregistrement du contrôle utilisateur extrait")

    *L’enregistrement du contrôle utilisateur extrait*
4. Notez que le code sélectionné a été extrait dans un contrôle utilisateur et l’emplacement d’origine du code sélectionné a été remplacée par une instance du nouveau contrôle utilisateur.

    ![Page mise à jour automatiquement pour utiliser le nouveau contrôle utilisateur](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image37.png "Page automatiquement mis à jour pour utiliser le nouveau contrôle utilisateur")

    *Page mise à jour automatiquement pour utiliser le nouveau contrôle utilisateur*
5. Appuyez sur **F5** pour exécuter la page et vérifiez que le contrôle fonctionne.

<a id="Exercise3"></a>

<a id="Exercise_3_Whats_New_in_the_JavaScript_Editor"></a>
### <a name="exercise-3-whats-new-in-the-javascript-editor"></a>Exercice 3 : Quelles sont les nouveautés dans l’éditeur JavaScript

L’écriture ou de la modification du code JavaScript n’est pas une tâche facile, en particulier lorsque votre application commence à croître et vous devez traitement de fichiers longs et des centaines de fonctions. Les développeurs de script ont généralement à effectuer du travail supplémentaire pour gérer la lisibilité du code et naviguez dans des fichiers. Avec l’inclusion des bibliothèques JavaScript comme jQuery, navigation de script est devenue un défi en raison de la longueur du code.

Visual Studio a renouvelé l’éditeur JavaScript avec la promesse pour rendre le mode code accessible et organisée. Plusieurs fonctionnalités de Visual Studio qui existaient déjà dans les éditeurs de code c# ou VB sont maintenant implémentées dans l’éditeur JavaScript : atteindre la définition, de mise en retrait automatique, de documentation et de validation lorsque vous écrivez. Avec la liste IntelliSense renouvelée, vous aurez le catalogue de la fonction JavaScript à portée de main.

Dans cet exercice, vous allez apprendre quelques-unes des nouvelles fonctionnalités et améliorations de l’éditeur JavaScript. Vous allez parcourir les exemples de fichiers et découvrir chacune des nouvelles caractéristiques qui permettront à votre programmation JavaScript plus efficace au sein de Visual Studio 2012.

<a id="Ex3Task1"></a>

<a id="Task_1_-_JavaScript_Editor_New_Features"></a>
#### <a name="task-1---javascript-editor-new-features"></a>Tâche 1 : nouvelles fonctionnalités de l’éditeur JavaScript

Cette tâche vous présentent les nouvelles fonctionnalités JavaScript éditeur qui reposent sur l’organisation de votre code et les mettre une meilleure expérience utilisateur.

1. Si pas déjà ouvert, démarrez **Visual Studio** et ouvrez le **WhatsNewASPNET.sln** solution situé dans le **Source\WhatsNewASPNET** dossier de ce laboratoire.
2. Appuyez sur **F5** pour exécuter l’application, puis cliquez sur le lien JavaScript dans la barre de navigation. Actualisez la page plusieurs fois et vérifiez comment l’incrémentation du compteur.

    ![Compteur de page](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image38.png "compteur de Page")

    *Compteur de page*
3. Fermez le navigateur et revenez à Visual Studio.
4. Ouvrez le **JavaScript.aspx** page et recherchez le  **&lt;script&gt;**  bloc (voir ci-dessous).

    Le code suivant utilise le stockage local de HTML5 pour stocker un *pageLoadCount* variable qui stocke le nombre de fois que la page a été visitée par l’utilisateur actuel. Stockage local est une base de données côté client clé-valeur introduite avec la norme HTML5. Les données sont enregistrées sur l’ordinateur local, à l’intérieur du navigateur de l’utilisateur.

    [!code-html[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample7.html)]

    > [!NOTE]
    > Vérifiez que la déclaration DOCTYPE est définie à XHTML5 avant de procéder aux étapes suivantes.
5. Modifier le code et notez que IntelliSense pour JavaScript inclut les fonctionnalités HTML5, telles que le stockage local et leurs méthodes internes.

    ![Fonctionnalités HTML5 JavaScript JavaScript](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image39.png "fonctionnalités HTML5 JavaScript JavaScript")

    *Fonctionnalités HTML5 JavaScript JavaScript*
6. Cliquez sur n’importe quel crochet ouvrant (**{**) à partir de l’écriture de scripts de code et notez que les crochets sont mis en surbrillance.

    ![Crochets sont mises en surbrillance](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image40.png "crochets sont mis en surbrillance.")

    *Crochets sont mis en surbrillance.*
7. Supprimez les commentaires de la fonction **testAutoAlign()** (sélectionnez les trois lignes et vous pouvez utiliser **CTRL** + **K**; **CTRL** + **U**) et recherchez le curseur à l’intérieur du code de fonction. Appuyez sur ENTRÉE pour ajouter une deuxième ligne. Notez que le code est désormais **aligné** et **mise en retrait automatique**.

    ![Code JavaScript est automatiquement aligné](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image41.png "code JavaScript est automatiquement aligné")

    *Code JavaScript est automatiquement aligné*

<a id="Ex3Task2"></a>

<a id="Task_2_-_Validating_JavaScript"></a>
#### <a name="task-2---validating-javascript"></a>Tâche 2 - validation JavaScript

Dans cette tâche, vous allez découvrir la nouvelle validation JavaScript de la norme ECMAScript5. Cette fonctionnalité vous aidera à écrire un code JavaScript conforme, tout en empêchant de problèmes liés aux scripts avant le déploiement de site.

> [!NOTE]
> Visual Studio 2010 implémentée ECMAStript3 conformité, alors que Visual Studio 2012 fournit ECMAScript5 conformité.


1. Ouvrez **ECMA5script5.js** situé sous le **Scripts\custom** dossier du projet. Vous allez maintenant tester la validation pour ECMAScript5 standard.

    [!code-html[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample8.html)]

    Vous pouvez extraire le &quot; **utilisez strict** &quot; direction dans la première ligne du fichier, ce qui permet de ECMAScript5 **mode strict**. Ce mode se compose d’un sous-ensemble du langage qui clarifie les ambiguïtés de l’édition précédente et ajoute certaines nouvelles fonctionnalités, telles que les accesseurs Get et Set, prise en charge de JSON et réflexion plus complète sur les propriétés de l’objet.
2. Ouvrez le **liste d’erreurs** si pas déjà ouvert (**vue** menu | **Liste d’erreurs**). Notez le **fonction** déclaration est soulignée. Il s’agit, car en ECMA5 fonctions standard ne peuvent pas être imbriquées au sein de structures de langage. Dans l’erreur, la liste ci-dessous, vous verrez les détails de l’avertissement.

    ![Message d’erreur de validation JavaScript](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image42.png "message d’erreur de validation JavaScript")

    *Message d’erreur de validation JavaScript*
3. Commentez la  **&quot;utilisez strict&quot;**  direction et notez que les erreurs disparaissent, mais les avertissements restent.
4. Dans la dernière ligne du fichier, écrivez n’importe quelle chaîne comme  **&quot;test&quot;**  (inclure les guillemets pour indiquer qu’il est en tant que chaîne). Écrire une période en regard de la chaîne à afficher la liste IntelliSense, puis sélectionnez le **trim** option.

    ECMAScript5 standard, variables et valeurs de chaîne ont également les méthodes de chaîne définies, telles que trim, majuscules, rechercher et remplacer.

    ![Liste IntelliSense dans JavaScript](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image43.png "liste IntelliSense JavaScript")

    *Liste IntelliSense JavaScript*

<a id="Ex3Task3"></a>

<a id="Task_3_-_XML_Documentation_for_JavaScript"></a>
#### <a name="task-3---xml-documentation-for-javascript"></a>Tâche 3 - Documentation XML pour JavaScript

Dans cette tâche, vous allez explorer les fonctionnalités de Visual Studio pour obtenir une documentation XML dans JavaScript. Vous verrez que la liste de JavaScript IntelliSense affiche maintenant la documentation XML de chaque fonction. En outre, vous allez découvrir la fonctionnalité de navigation dans JavaScript.

1. Ouvrez **XMLDoc.js** fichier situé dans **Scripts/custom** dossier du projet. Ce fichier contient la documentation XML sur chacune des fonctions JavaScript.

    ![Documentation XML du JavaScript intégré à IntelliSense](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image44.png "documentation XML du JavaScript intégré à IntelliSense")

    *Documentation XML JavaScript intégrée à IntelliSense*
2. Ci-dessous **ajouter** fonctionner dans **XMLDoc.js** de fichiers, créez une fonction nommée **test**.
3. Dans le **test** de fonction, appelez le **multiplier** fonction qui reçoit deux paramètres. Notez la zone d’info-bulle affiche le **multiplier** documentation de la fonction.

    [!code-javascript[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample9.js)]

    ![Documentation XML pour les fonctions JavaScript](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image45.png "documentation XML pour les fonctions JavaScript")

    *Documentation XML pour les fonctions JavaScript*
4. Terminer l’instruction d’appel de fonction et le type une *point* pour ouvrir la liste IntelliSense sur la valeur retournée. Notez que Visual Studio détecte les **retourner** valeur dans la documentation traitant la valeur en tant que nombre.

    ![Documentation XML pour les types de retour](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image46.png "documentation XML pour les types de retour")

    *Documentation XML pour les types de retour*
5. À présent, insérez un appel à ajouter une fonction. Notez que l’éditeur JavaScript prend désormais en charge les surcharges de fonction. Lorsque vous écrivez un nom de fonction, vous serez en mesure de choisir une des surcharges disponibles spécifiés dans la documentation.

    ![Documentation XML pour les surcharges](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image47.png "documentation XML pour les surcharges")

    *Documentation XML pour les surcharges*
6. Ouvrez **GotoDefinition.js** de fichiers et recherchez le **$().html()** l’appel de fonction. Localiser le curseur sur **html**.
7. Appuyez sur **F12** et accéder à la définition. Notez que vous pouvez maintenant accéder et parcourir votre code JavaScript sans utiliser le **trouver** outil.
8. Recherchez le curseur sur l’instruction jQuery avant le bloc de signature au bas du fichier de code. Appuyez sur **F12**. Vous accéderez au fichier bibliothèque jQuery. Notez que vous pouvez également accéder à l’aide de fichiers jQuery **F12**.

    ![Naviguer vers les définitions de jQuery](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image48.png "naviguer vers les définitions de jQuery")

    *Naviguer vers les définitions de jQuery*

> [!NOTE]
> Assurez-vous que GotoDefinition.js ne qu’aucune erreur de syntaxe avant l’enregistrement du fichier.


<a id="Exercise4"></a>

<a id="Exercise_4_Bundling_and_Minification"></a>
### <a name="exercise-4-bundling-and-minification"></a>Exercice 4 : Groupement et minimisation

Combien de fois vos sites Web qui incluent des fichiers plusieurs JavaScript ou CSS ? Il s’agit d’un scénario très courant où groupement et la minimisation peuvent aider à réduire la taille de fichier et que le site sont plus rapides. La nouvelle fonctionnalité de regroupement dans ASP.NET 4.5 les packs d’un ensemble de fichiers JS ou CSS dans un élément unique et sa taille diminue de réduire le contenu (suppression des espaces vides non requises, supprimer des commentaires, ce qui réduit les identificateurs).

Groupement et la minimisation dans ASP.NET 4.5 est effectuée pendant l’exécution, afin que le processus peut identifier l’agent utilisateur (par exemple Internet Explorer, Mozilla, etc.) et par conséquent, améliorer la compression en ciblant le navigateur de l’utilisateur (par exemple, supprimez ce qui est spécifique de Mozilla Lorsque la demande provient d’Internet Explorer).

Dans cet exercice, vous allez apprendre à activer et utiliser les différents types de groupement et la minimisation dans ASP.NET 4.5.

<a id="Ex4Task1"></a>

<a id="Task_1_-_Installing_the_Bundling_and_Minification_Package_from_NuGet"></a>
#### <a name="task-1---installing-the-bundling-and-minification-package-from-nuget"></a>Tâche 1 : installer le groupement et minimisation un Package à partir de NuGet

1. Si pas déjà ouvert, démarrez **Visual Studio** et ouvrez le **WhatsNewASPNET.sln** solution situé dans le **Source\WhatsNewASPNET** dossier de ce laboratoire.
2. Ouvrez la Console du Gestionnaire de Package NuGet. Pour ce faire, utilisez le menu **vue** | **autres fenêtres** | **Package Manager Console**.

    ![Ouvrir le file:///C:/Users/User/AppData/Local/Temp/Marker3744//media/44462/Multiple-Stylesheets-and-JavaScript-files-in-the-application.pngconsole manager package](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image49.png "ouverture de la console Gestionnaire de package")

    *Ouverture de la console Gestionnaire de package*
3. Dans le **Console du Gestionnaire de Package,** type **Install-Package Microsoft.Web.Optimization** et appuyez sur **entrée**.

<a id="Ex4Task2"></a>

<a id="Task_2_-_Default_Bundles"></a>
#### <a name="task-2---default-bundles"></a>Tâche 2 : les groupes par défaut

Pour utiliser le groupement et la minimisation, le plus simple consiste à activer les regroupements par défaut. Cette méthode utilise les conventions pour vous permettre de faire référence à la version groupée et réduite pour les fichiers JS et CSS dans un dossier.

Dans cette tâche, vous allez apprendre à activer et référencer les fichiers regroupés et réduites JS et CSS et afficher le résultat.

1. Si pas déjà ouvert, démarrez **Visual Studio** et ouvrez le **WhatsNewASPNET.sln** solution situé dans le **Source\WhatsNewASPNET** dossier de ce laboratoire.
2. Dans le **l’Explorateur de solutions**, développez le **Styles**, **Scripts\custom** et **Scripts\bundle** dossiers.

    Notez que l’application utilise le CSS et JS les plus d’un fichier.

    ![Fichiers de plusieurs feuilles de style et de JavaScript dans l’application](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image50.png "fichiers plusieurs feuilles de style et de JavaScript dans l’application")

    *Plusieurs fichiers de feuilles de style et de JavaScript dans l’application*
3. Ouvrez le **Global.asax.cs** fichier.

    Notez que la nouvelle **Microsoft.Web.Optimization** espace de noms est commentée au début du fichier. Supprimez les commentaires d’à l’aide de la directive pour inclure les fonctionnalités de groupement et la minimisation.


    [!code-csharp[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample10.cs)]
4. Recherchez le **Application\_Démarrer** (méthode).

    Dans cette méthode, supprimez l’appel de EnableDefaultBundles comme indiqué dans l’extrait de code ci-dessous. Cela permet de faire référence à une collection groupés sur des fichiers CSS dans un dossier en utilisant le chemin d’accès à ce dossier, ainsi que les &quot;CSS&quot; ou &quot;JS&quot; suffixe.


    [!code-csharp[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample11.cs)]
5. Ouvrez le **Optimization.aspx** de fichiers et recherchez le contrôle de contenu pour **HeadContent**.

    Notez les fichiers CSS et JS avoir une seule balise référencée.


    [!code-aspx[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample12.aspx)]

    > [!NOTE]
    > Ce code est à des fins de démonstration. Dans l’idéal, vous ferez référence les regroupements dans le fichier Site.Master. Dans cet exemple de code, vous constaterez que certains des fichiers regroupés sont également référencés par le fichier Site.Master, qui effectue cette dernière référence redondant.
6. Notez que les liens utilisent les conventions de regroupement dans la **href** attribut pour obtenir les fichiers de tous les CSS ou JS des Styles et Scripts\custom dossier respectivement.

    Vous pouvez utiliser le chemin d’accès **personnalisé/Scripts/JS** comme indiqué ci-dessous pour regrouper et minimiser tous les fichiers JS à l’intérieur d’un **Scripts/custom** dossier. Il s’agit du comportement par défaut avec les regroupements par défaut.


    [!code-aspx[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample13.aspx)]
7. Ouvrez le **Styles\Site.css** fichier.

    Notez que le fichier CSS d’origine contienne code en retrait, les espaces vides et les commentaires qui agrandissent le fichier. (Le fichier JavaScript contient également les commentaires et espaces vides).

    ![Une CSS d’origine des fichiers dans le dossier Scripts](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image51.png "une CSS d’origine des fichiers dans le dossier de Scripts")

    *L’un des fichiers CSS d’origine dans le dossier de Scripts*
8. Appuyez sur **F5** pour exécuter l’application et de naviguer vers le **optimisation** page.
9. Cliquez sur le **CSS regroupement** lien pour télécharger et ouvrir le fichier.

    Consultez le fichier regroupé réduite. Vous remarquerez que tous les espaces vides, les commentaires et les caractères de mise en retrait ont été supprimées, génération d’un fichier plus petit.

    ![Fourni des fichiers CSS](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image52.png "fichiers regroupés de CSS")

    *Fichiers CSS regroupés*
10. Maintenant, cliquez sur le **JS regroupement** lien pour ouvrir le fichier JavaScript fourni. Vous pouvez ignorer sans risque l’avertissement de l’Explorateur. Notez les fichiers JavaScript sous la **personnalisé** dossier sont également fournies et réduites.

    ![Fourni des fichiers JavaScript](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image53.png "fichiers regroupés de JavaScript")

    *Fichiers JavaScript regroupés*

    Activation de la compression des fichiers CSS ou JS a été beaucoup plus complexe dans la précédente version ASP.NET. Maintenant, comme vous l’avez vu, vous devez simplement ajouter une ligne dans le *Global.asax* pour activer le regroupement de fichiers et ensuite référencer les fichiers regroupés à partir de votre site.

<a id="Ex4Task3"></a>

<a id="Task_3_-_Static_Bundles"></a>
#### <a name="task-3---static-bundles"></a>Tâche 3 : groupes statiques

L’approche de regroupement statique vous permet de personnaliser l’ensemble de fichiers pour le regroupement, la référence et la méthode de réduction qui sera utilisée.

Dans cette tâche, vous allez configurer un groupe statique pour définir un ensemble spécifique de fichiers à regrouper et minimiser.

1. Fermez le navigateur.
2. Ouvrez le **Global.asax.cs** de fichiers et recherchez le **Application\_Démarrer** (méthode).
3. Ne pas commenter le code de l’offre groupée statique comme indiqué dans le code ci-dessous.

    Vous définissez un groupe statique qui sera référencé avec le &quot; **~/StaticBundle** &quot; chemin d’accès virtuel et utilisez **JsMinify** pour la réduction de tous les fichiers spécifiés avec le **AddFile** (méthode). Enfin, vous ajoutez le groupe statique pour le **BundleTable** et son activation.

    Notez que les fichiers ne se trouvent pas au même endroit ; Il s’agit d’un autre avantage sur le regroupement par défaut.


    [!code-csharp[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample14.cs)]
4. Ouvrez le **Optimization.aspx** fichier.

    Notez que le lien vers **statique JS regroupement** utilise le chemin d’accès que vous avez déclaré lorsque vous avez configuré l’application statique dans le fichier Global.asax.cs : **/StaticBundle**.


    [!code-aspx[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample15.aspx)]
5. Appuyez sur **F5** et exécuter l’application, puis accédez à la **optimisation** page.
6. Cliquez sur le **statique JS regroupement** lien pour ouvrir le fichier.

    Notez que le réduite regroupé fichier JavaScript est la sortie de tous les fichiers JavaScript configuré dans le fichier d’offre groupée statique sous le chemin d’accès &quot;/StaticBundle&quot;.

    ![Regroupement de fichiers statique JavaScript](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image54.png "groupe de fichiers statiques JavaScript")

    *Regroupement de fichiers statique JavaScript*
7. Fermez le navigateur et revenez à Visual Studio.

<a id="Ex4Task4"></a>

<a id="Task_4_-_Dynamic_Folder_Bundles"></a>
#### <a name="task-4---dynamic-folder-bundles"></a>Tâche 4 : groupes de dossier dynamique

Dans cette tâche, vous allez apprendre à configurer des groupes de dossier dynamique. La puissance d’un regroupement dynamique est que vous pouvez inclure JavaScript statique, ainsi que d’autres fichiers dans les langages qui compile en JavaScript et par conséquent, nécessitent un traitement avant le regroupement est exécutée.

Dans cet exemple, vous allez apprendre à utiliser le **DynamicFolderBundle** classe pour créer un regroupement dynamique pour les fichiers écrits dans CofeeScript. CofeeScript est un langage de programmation qui compile en JavaScript et fournit une syntaxe plus simple pour l’écriture de code JavaScript, l’amélioration du JavaScript souci de concision et une meilleure lisibilité.

1. Ouvrez le **Global.asax.cs** de fichiers et recherchez le **Application\_Démarrer** (méthode).
2. Ne pas commenter le code de groupe dynamique, comme indiqué dans le code ci-dessous.

    Vous définissez un regroupement de dossiers dynamique qui utilisera le **CoffeeMinify** processeur minimisation personnalisé qui s’applique uniquement aux fichiers avec le &quot; **.coffee** &quot; extension () Fichiers CoffeeScript). Notez que vous pouvez utiliser un modèle de recherche pour sélectionner les fichiers à regrouper dans un dossier, tel que «\*.coffee ».


    [!code-csharp[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample16.cs)]
3. Ouvrez la Console du Gestionnaire de Package NuGet. Pour ce faire, utilisez le menu **vue** | **autres fenêtres** | **Package Manager Console**.
4. Dans le **Console du Gestionnaire de Package,** type **Install-Package CoffeeSharp** et appuyez sur **entrée**.
5. Cliquez sur le **afficher tous les fichiers** situé dans le **l’Explorateur de solutions** fenêtre

    ![Affichage de tous les fichiers](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image55.png "affichant tous les fichiers")

    *Affichage de tous les fichiers*
6. Bouton droit sur le **CoffeeMinify.cs** de fichiers dans le **l’Explorateur de solutions** et sélectionnez **inclure dans le projet**

    ![Inclure le fichier CoffeeMinify.cs dans le projet](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image56.png "inclure le fichier CoffeeMinify.cs dans le projet")

    *Inclure le fichier CoffeeMinify.cs dans le projet*
7. Ouvrez le **CoffeeMinify.cs** fichier.

    Cette classe hérite de JsMinify à minimiser la sortie JavaScript résultant de la compilation de code CoffeeScript. Il appelle le compilateur CoffeeScript pour générer le code JavaScript tout d’abord, puis il transmet à la méthode JsMinify.Process à minimiser le code résultant.


    [!code-csharp[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample17.cs)]
8. Ouvrez le **Script1.coffee** et **Script2.coffee** fichiers à partir de la **/groupe de Scripts** dossier.

    Ces fichiers inclut le code de CoffeScript peut être compilé lors de l’exécution de l’intégration avec la classe CoffeeMinify.

    Pour des raisons de simplicité, les fichiers CoffeeScript fournis incluez uniquement CoffeeScript exemple de code. Les commentaires sont exclus par le processus JsMinify.

    ![Les fichiers CoffeeScript](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image57.png "CoffeeScript fichiers")

    *Fichiers CoffeeScript*

    > [!NOTE]
    > [CofeeScript](https://github.com/jashkenas/coffeescript/) fournit une syntaxe plus simple pour l’écriture de code JavaScript, amélioration du JavaScript souci de concision et une meilleure lisibilité, ainsi que pour ajouter d’autres fonctionnalités telles que les critères spéciaux et compréhension du tableau.
9. Ouvrez le **Optimization.aspx** de fichiers et recherchez les liens d’offre groupée.

    Notez que le lien vers **dynamique JS regroupement** fait référence à la **/groupe de Scripts** dossier à l’aide de la **/café** suffixe que vous avez configuré pour le regroupement de dossiers dynamique.


    [!code-aspx[Main](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/samples/sample18.aspx)]
10. Appuyez sur **F5** et exécuter l’application, puis accédez à la **optimisation** page.
11. Cliquez sur le **dynamique JS regroupement** lien pour ouvrir le fichier généré.

    Notez que le contenu qui a été inclus dans ce groupe contient uniquement **.coffee** fichiers. Vous pouvez également voir que le code CoffeeScript a été compilé en JavaScript et les lignes commentées a été supprimé.

    ![Regroupement des fichiers JS dynamiques](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image58.png "groupe de fichiers JS dynamique")

    *Regroupement de fichiers JS dynamique*

> [!NOTE]
> En outre, vous pouvez déployer cette application à Sites Web Windows Azure suit [annexe b : publication une Application ASP.NET MVC 4, à l’aide de Web Deploy](#AppendixB).


<a id="Summary"></a>
## <a name="summary"></a>Récapitulatif

Cet atelier vous aide à comprendre ce qui sont nouveau dans ASP.NET et le développement Web dans Visual Studio 2012 et comment tirer parti de la diversité des améliorations dans Visual Studio 2012.

À la fin de cet atelier pratique, vous avez appris comment utiliser les nouvelles fonctionnalités et améliorations dans les éditeurs de Visual Studio 2012 pour CSS, JavaScript et HTML. En outre, vous avez appris comment Visual Studio 2012 implémente groupement intégrés et la minimisation.

<a id="AppendixA"></a>

<a id="Appendix_A_Installing_Visual_Studio_Express_2012_for_Web"></a>
## <a name="appendix-a-installing-visual-studio-express-2012-for-web"></a>Annexe a : installation de Visual Studio Express 2012 pour le Web

Vous pouvez installer **Microsoft Visual Studio Express 2012 pour Web** ou un autre &quot;Express&quot; à l’aide de la version du  **[Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx)** . Les instructions suivantes vous guident à travers les étapes requises pour installer *Visual studio Express 2012 pour le Web* à l’aide de *Microsoft Web Platform Installer*.

1. Accédez à [ [https://go.microsoft.com/?linkid=9810169](https://go.microsoft.com/?linkid=9810169)](https://go.microsoft.com/?linkid=9810169). Sinon, si vous avez déjà installé Web Platform Installer, vous pouvez ouvrir il et recherchez le produit &quot; *Visual Studio Express 2012 pour le Web avec Windows Azure SDK*&quot;.
2. Cliquez sur **installer maintenant**. Si vous n’avez pas **Web Platform Installer** vous allez être redirigé pour télécharger et installer tout d’abord.
3. Une fois **Web Platform Installer** est ouvert, cliquez sur **installer** pour démarrer le programme d’installation.

    ![Installer Visual Studio Express](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image59.png "installer Visual Studio Express")

    *Installer Visual Studio Express*
4. Lisez les termes et les licences de tous les produits et cliquez sur **J’accepte** pour continuer.

    ![Accepter les termes du contrat de licence](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image60.png)

    *Accepter les termes du contrat de licence*
5. Attendez que le processus de téléchargement et l’installation se termine.

    ![Progression de l'installation](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image61.png)

    *Progression de l’installation*
6. Une fois l’installation terminée, cliquez sur **Terminer**.

    ![Installation est terminée](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image62.png)

    *Installation est terminée*
7. Cliquez sur **Exit** fermer Web Platform Installer.
8. Pour ouvrir Visual Studio Express pour le Web, accédez à la **Démarrer** écran et démarrer l’écriture &quot; **VS Express**&quot;, puis cliquez sur le **Visual Studio Express pour le Web** vignette.

    ![VS Express pour la vignette du Web](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image63.png)

    *VS Express pour la vignette du Web*

* * *

<a id="AppendixB"></a>

<a id="Appendix_B_Publishing_an_ASPNET_MVC_4_Application_using_Web_Deploy"></a>
## <a name="appendix-b-publishing-an-aspnet-mvc-4-application-using-web-deploy"></a>Annexe b : publication d’une Application ASP.NET MVC 4, à l’aide de Web Deploy

Cette annexe sera vous montrent comment créer un nouveau site web à partir du portail de gestion Windows Azure et de publier l’application que vous avez obtenu en suivant le laboratoire, en tirant parti de la fonctionnalité de publication Web Deploy fournie par Windows Azure.

<a id="Ex1Task1"></a>

<a id="Task_1_-_Creating_a_New_Web_Site_from_the_Windows_Azure_Portal"></a>
#### <a name="task-1---creating-a-new-web-site-from-the-windows-azure-portal"></a>Tâche 1 - Création d’un Site Web à partir de Windows Azure Portal

1. Accédez à la [portail de gestion Windows Azure](https://manage.windowsazure.com/) et connectez-vous en utilisant les informations d’identification Microsoft associées à votre abonnement.

    > [!NOTE]
    > Avec Windows Azure, vous pouvez héberger des Sites Web ASP.NET 10 gratuitement et puis faire évoluer à mesure que votre trafic augmente. Vous pouvez vous inscrire [ici](http://aka.ms/aspnet-hol-azure).

    ![Ouvrez une session sur le portail Windows Azure](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image64.png "connectez-vous au portail Windows Azure")

    *Ouvrez une session sur le portail de gestion Azure Windows*
2. Cliquez sur **nouveau** sur la barre de commandes.

    ![Création d’un Site Web](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image65.png "création d’un Site Web")

    *Création d’un Site Web*
3. Cliquez sur **de calcul** | **Site Web**. Puis sélectionnez **création rapide** option. Fournir une URL disponible pour le nouveau site web et cliquez sur **créer un Site Web**.

    > [!NOTE]
    > Un Site Web de Windows Azure est l’hôte pour une application web en cours d’exécution dans le cloud que vous pouvez contrôler et gérer. L’option Création rapide vous permet de déployer une application web complète pour le Site Web Windows Azure à partir d’à l’extérieur du portail. Il n’inclut pas les étapes de configuration d’une base de données.

    ![Création d’un Site Web à l’aide de la création rapide](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image66.png "création d’un Site Web à l’aide de la création rapide")

    *Création d’un Site Web à l’aide de la création rapide*
4. Attendez que la nouvelle **Site Web** est créé.
5. Une fois que le Site Web est créé, cliquez sur le lien situé sous le **URL** colonne. Vérifiez que le nouveau Site Web fonctionne.

    ![Navigation vers le nouveau site web](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image67.png "exploration vers le nouveau site web")

    *Navigation vers le nouveau site web*

    ![Site Web en cours d’exécution](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image68.png "site Web en cours d’exécution")

    *Site Web en cours d’exécution*
6. Revenez au portail et cliquez sur le nom du site web sous le **nom** colonne pour afficher les pages de gestion.

    ![Ouvrir les pages de gestion du site web](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image69.png "ouvrir les pages de gestion de site web")

    *Ouvrir les pages de gestion de Site Web*
7. Dans le **tableau de bord** sous le **coup de œil rapide** , cliquez sur le **télécharger le profil de publication** lien.

    > [!NOTE]
    > Le *le profil de publication* contient toutes les informations nécessaires pour publier une application web sur un site Web de Windows Azure pour chaque méthode de publication activée. Le profil de publication contient les URL, les informations d’identification utilisateur et les chaînes de base de données requis pour se connecter à et de s’authentifier auprès de chacun des points de terminaison pour laquelle une méthode de publication est activée. **Microsoft WebMatrix 2**, **Microsoft Visual Studio Express pour Web** et **Microsoft Visual Studio 2012** prise en charge la lecture de publier les profils pour automatiser la configuration de ces programmes pour publication d’applications web pour les sites Web Windows Azure.

    ![Profil de publication de téléchargement du site web](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image70.png "profil de publication de téléchargement du site web")

    *Profil de publication de téléchargement du Site Web*
8. Téléchargez le fichier de profil de publication à un emplacement connu. Davantage dans cet exercice, vous verrez comment utiliser ce fichier pour publier une application web à un Sites Web Windows Azure à partir de Visual Studio.

    ![L’enregistrement du fichier de profil de publication](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image71.png "l’enregistrement du profil de publication")

    *L’enregistrement du fichier de profil de publication*

<a id="Ex1Task2"></a>

<a id="Task_2_-_Configuring_the_Database_Server"></a>
#### <a name="task-2---configuring-the-database-server"></a>Tâche 2 : configuration du serveur de base de données

Si votre application se sert de SQL Server vous devez créer un serveur de base de données SQL des bases de données. Si vous souhaitez déployer une application simple qui n’utilise pas de SQL Server, vous pouvez ignorer cette tâche.

1. Vous devez un serveur de base de données SQL pour stocker la base de données de l’application. Vous pouvez afficher les serveurs de base de données SQL à partir de votre abonnement dans le portail de gestion Windows Azure à **bases de données Sql** | **serveurs** | **du serveur Tableau de bord**. Si vous ne disposez pas d’un serveur créé, vous pouvez créer un à l’aide de la **ajouter** bouton sur la barre de commandes. Prenez note de la **nom du serveur et les URL, nom de connexion d’administrateur et un mot de passe**, comme vous allez l’utiliser dans les tâches suivantes. Ne créez pas encore, la base de données telle qu’elle sera créée dans une étape ultérieure.

    ![Tableau de bord de serveur SQL de base de données](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image72.png "tableau de bord de serveur SQL de base de données")

    *Tableau de bord de serveur SQL de base de données*
2. Dans la tâche suivante, vous allez tester la connexion de base de données à partir de Visual Studio, pour cette raison, vous devez inclure votre adresse IP locale dans la liste du serveur de **adresses IP autorisées**. Pour ce faire, cliquez sur **configurer**, sélectionnez l’adresse IP à partir de **adresse IP du Client actuel** et le coller dans le **adresse IP de début** et **adresse IP de fin** zones de texte. Entrez un nom pour la règle, cliquez sur le ![add-client-ip-address-ok-button](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image73.png) bouton.

    ![Ajout d’adresse IP du Client](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image74.png)

    *Ajout d’adresse IP du Client*
3. Une fois la **adresse IP du Client** est ajouté aux adresses IP autorisées de liste, cliquez sur **enregistrer** pour confirmer les modifications.

    ![Confirmer les modifications](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image75.png)

    *Confirmer les modifications*

<a id="Ex1Task3"></a>

<a id="Task_3_-_Publishing_an_ASPNET_MVC_4_Application_using_Web_Deploy"></a>
#### <a name="task-3---publishing-an-aspnet-mvc-4-application-using-web-deploy"></a>Tâche 3 : publication d’une Application ASP.NET MVC 4, à l’aide de Web Deploy

1. Revenez à la solution ASP.NET MVC 4. Dans le **l’Explorateur de solutions**, cliquez sur le projet de site web et sélectionnez **publier**.

    ![Publication de l’Application](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image76.png "publication de l’Application")

    *Publier le site web*
2. Importer le profil de publication que vous avez enregistré dans la première tâche.

    ![L’importation du profil de publication](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image77.png "l’importation du profil de publication")

    *Importation du profil de publication*
3. Cliquez sur **valider la connexion**. Une fois la Validation terminée. Cliquez sur **suivant**.

    > [!NOTE]
    > La validation est terminée une fois que vous voyez une coche verte apparaît en regard du bouton Valider la connexion.

    ![Validation de la connexion](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image78.png "validation de la connexion")

    *Validation de la connexion*
4. Dans le **paramètres** sous le **bases de données** , cliquez sur le bouton en regard de la zone de texte de la connexion de votre base de données (par exemple, **DefaultConnection**).

    ![Configuration de déploiement Web](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image79.png "configuration de déploiement Web")

    *Configuration de déploiement Web*
5. Configurer la connexion de base de données comme suit :

    - Dans le **nom du serveur** tapez votre URL de base de données SQL server à l’aide du *tcp :* préfixe.
    - Dans **nom d’utilisateur** tapez le nom de connexion de votre administrateur de serveur.
    - Dans **mot de passe** votre mot de passe du compte de connexion administrateur serveur.
    - Tapez un nouveau nom de base de données, par exemple : *MVC4SampleDB*.

    ![Configuration de chaîne de connexion de destination](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image80.png "configuration de chaîne de connexion de destination")

    *Configuration de chaîne de connexion de destination*
6. Cliquez ensuite sur **OK**. Lorsque vous êtes invité à créer la base de données, cliquez sur **Oui**.

    ![Création de la base de données](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image81.png "création de la chaîne de la base de données")

    *Création de la base de données*
7. La chaîne de connexion que vous allez utiliser pour se connecter à la base de données SQL dans Windows Azure est indiquée dans la zone de texte par défaut de connexion. Cliquez ensuite sur **Suivant**.

    ![Chaîne de connexion pointant vers la base de données SQL](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image82.png "chaîne de connexion pointant vers la base de données SQL")

    *Chaîne de connexion pointant vers la base de données SQL*
8. Dans le **aperçu** , cliquez sur **publier**.

    ![Publication de l’application web](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image83.png "publication de l’application web")

    *Publication de l’application web*
9. Une fois le processus de publication terminé, votre navigateur par défaut s’ouvre le site web publié.

    ![Publication d’application sur Windows Azure](whats-new-in-aspnet-and-web-development-in-visual-studio-2012/_static/image84.png "Application publiée dans Windows Azure")

    *Application de publication sur Windows Azure*
