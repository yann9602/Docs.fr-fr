---
uid: web-forms/overview/older-versions-getting-started/master-pages/control-id-naming-in-content-pages-vb
title: "Contrôler l’ID d’affectation de noms dans les Pages de contenu (VB) | Documents Microsoft"
author: rick-anderson
description: "Montre comment les contrôles ContentPlaceHolder servent de conteneur d’attribution de noms et donc l’utilisation par programmation un contrôle difficile (via FindConrol)..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/10/2008
ms.topic: article
ms.assetid: dbb024a6-f043-4fc5-ad66-56556711875b
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/master-pages/control-id-naming-in-content-pages-vb
msc.type: authoredcontent
ms.openlocfilehash: 9523fe5b241b6ff45927f142eb844a716822336b
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="control-id-naming-in-content-pages-vb"></a>ID de contrôle d’affectation de noms dans les Pages de contenu (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/e/e/f/eef369f5-743a-4a52-908f-b6532c4ce0a4/ASPNET_MasterPages_Tutorial_05_VB.zip) ou [télécharger le PDF](http://download.microsoft.com/download/8/f/6/8f6349e4-6554-405a-bcd7-9b094ba5089a/ASPNET_MasterPages_Tutorial_05_VB.pdf)

> Explique comment les contrôles ContentPlaceHolder servent de conteneur d’attribution de noms et donc l’utilisation par programmation un contrôle difficile (via FindConrol). Examine ce problème et les solutions de contournement. Explique également comment accéder par programmation à la valeur ClientID obtenue.


## <a name="introduction"></a>Introduction

Tous les contrôles de serveur ASP.NET incluent une `ID` propriété qui identifie le contrôle et est le moyen par lequel le contrôle est accessible par programmation dans la classe code-behind. De même, les éléments dans un document HTML peuvent inclure un `id` attribut qui identifie de façon unique l’élément ; ces `id` valeurs sont souvent utilisées dans un script côté client pour faire référence à un élément HTML particulier par programmation. Compte tenu de cela, vous pouvez supposer que lorsqu’un contrôle serveur ASP.NET est rendu en HTML, son `ID` valeur est utilisée comme la `id` la valeur de l’élément HTML rendu. Cela n’est pas nécessairement le cas, car dans certaines circonstances, un seul contrôle avec une seule `ID` valeur peut apparaître plusieurs fois dans le balisage restitué. Prenons un contrôle GridView qui inclut un TemplateField avec un contrôle Web Label avec une `ID` valeur `ProductName`. Lorsque le contrôle GridView est lié à sa source de données lors de l’exécution, cette étiquette est utilisée une seule fois pour chaque ligne GridView. Rendus besoins d’étiquette unique `id` valeur.

Pour gérer ces scénarios, ASP.NET permet à certains contrôles être désigné comme conteneurs d’affectation de noms. Un conteneur d’attribution de noms sert un nouveau `ID` espace de noms. Les contrôles serveur qui s’affichent dans le conteneur d’attribution de noms ont leur rendu `id` value préfixé avec la `ID` du contrôle conteneur d’attribution de noms. Par exemple, le `GridView` et `GridViewRow` classes sont des conteneurs d’affectation de noms. Par conséquent, un contrôle d’étiquette défini dans GridView TemplateField avec `ID` `ProductName` reçoit un rendu `id` valeur `GridViewID_GridViewRowID_ProductName`. Étant donné que *GridViewRowID* est unique pour chaque ligne GridView, résultant `id` valeurs sont uniques.

> [!NOTE]
> Le [ `INamingContainer` interface](https://msdn.microsoft.com/library/system.web.ui.inamingcontainer.aspx) est utilisé pour indiquer qu’un contrôle de serveur ASP.NET particulier doit fonctionner comme un conteneur d’attribution de noms. Le `INamingContainer` interface n’écrivez pas toutes les méthodes que le contrôle serveur doit implémenter ; au lieu de cela, il est utilisé comme un marqueur. Pour générer le rendu du balisage, si un contrôle implémente cette interface ensuite le moteur ASP.NET ajoute automatiquement le son `ID` valeur pour ses descendants rendue `id` des valeurs d’attribut. Cette procédure est décrite plus en détail à l’étape 2.


Les conteneurs d’affectation de noms non seulement modifier le rendu `id` valeur d’attribut, mais également affecter la façon dont le contrôle peut être référencé par programme à partir de la classe code-behind de la page ASP.NET. Le `FindControl("controlID")` méthode est couramment utilisée pour référencer par programme un contrôle Web. Toutefois, `FindControl` ne pas pénétrer par le biais d’affectation de noms conteneurs. Par conséquent, vous ne pouvez pas utiliser directement le `Page.FindControl` méthode pour référencer des contrôles dans un contrôle GridView ou un autre conteneur d’attribution de noms.

Comme vous l’avez peut-être présumez, pages maîtres et les ContentPlaceHolders sont implémentés comme conteneurs d’affectation de noms. Dans ce didacticiel, nous allons examiner comment master pages affectent HTML élément `id` valeurs et méthodes pour le référencer par programmation des contrôles Web au sein d’une page de contenu à l’aide `FindControl`.

## <a name="step-1-adding-a-new-aspnet-page"></a>Étape 1 : Ajout d’une nouvelle Page ASP.NET

Pour illustrer les concepts abordés dans ce didacticiel, vous allez ajouter une nouvelle page ASP.NET à notre site Web. Créer une nouvelle page de contenu nommée `IDIssues.aspx` dans le dossier racine, la liaison à la `Site.master` page maître.


![Ajouter le contenu IDIssues.aspx de Page dans le dossier racine](control-id-naming-in-content-pages-vb/_static/image1.png)

**Figure 01**: ajouter la Page de contenu `IDIssues.aspx` dans le dossier racine


Visual Studio crée automatiquement un contrôle de contenu pour chacun des quatre ContentPlaceHolders de celui de la page maître. Comme indiqué dans le [ *ContentPlaceHolders multiples et contenu par défaut* ](multiple-contentplaceholders-and-default-content-vb.md) didacticiel, si un contrôle de contenu n’est pas présent le contenu de la page maître par défaut ContentPlaceHolder est émis à la place. Étant donné que la `QuickLoginUI` et `LeftColumnContent` ContentPlaceHolders contiennent des balises par défaut convenable pour cette page, continuez et supprimer leurs correspondantes des contrôles de contenu à partir de `IDIssues.aspx`. À ce stade, le balisage déclaratif de la page de contenu doit ressembler à ce qui suit :


[!code-aspx[Main](control-id-naming-in-content-pages-vb/samples/sample1.aspx)]

Dans le [ *spécifiant le titre, les balises Meta et les autres en-têtes HTML dans la Page maître* ](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-vb.md) didacticiel, nous avons créé une classe de page de base personnalisée (`BasePage`) qui configure automatiquement le titre de la page si elle est ne sont pas explicitement définie. Pour le `IDIssues.aspx` page pour utiliser cette fonctionnalité, la classe code-behind de la page doit dériver de la `BasePage` classe (au lieu de `System.Web.UI.Page`). Modifiez la définition de la classe code-behind afin qu’il ressemble à ceci :


[!code-vb[Main](control-id-naming-in-content-pages-vb/samples/sample2.vb)]

Enfin, mettre à jour le `Web.sitemap` fichier à inclure une entrée pour cette leçon de nouveau. Ajouter un `<siteMapNode>` et définissez son `title` et `url` attributs « ID d’affectation de noms problèmes liés au contrôle » et `~/IDIssues.aspx`, respectivement. Après avoir établi la cet ajout votre `Web.sitemap` les balises du fichier doivent ressembler à ce qui suit :


[!code-xml[Main](control-id-naming-in-content-pages-vb/samples/sample3.xml)]

Comme le montre la Figure 2, la nouvelle entrée de mappage de site dans `Web.sitemap` est immédiatement répercutée dans la section de leçons dans la colonne de gauche.


![La Section leçons inclut maintenant un lien vers &quot;ID d’affectation de noms problèmes de contrôle&quot;](control-id-naming-in-content-pages-vb/_static/image2.png)

**Figure 02**: la Section leçons inclut désormais un lien vers « ID de contrôle d’affectation de noms problèmes »


## <a name="step-2-examining-the-renderedidchanges"></a>Étape 2 : Examen rendu`ID`modifications

Pour mieux comprendre les modifications ASP.NET moteur permet le rendu `id` contrôle les valeurs du serveur, vous allez ajouter des contrôles Web à la `IDIssues.aspx` page, puis afficher le balisage rendu envoyé au navigateur. En particulier, tapez le texte « Veuillez entrer votre âge : » suivie d’un contrôle zone de texte Web. Plus bas dans la page Ajouter un contrôle bouton Web et un contrôle Web Label. Définir la zone de texte `ID` et `Columns` propriétés `Age` et 3, respectivement. Affectez à la `Text` et `ID` propriétés de « Submit » et `SubmitButton`. Vider l’étiquette `Text` propriété et définissez son `ID` à `Results`.

À ce stade balisage déclaratif de contenu de votre contrôle doit ressembler à ce qui suit :


[!code-aspx[Main](control-id-naming-in-content-pages-vb/samples/sample4.aspx)]

Figure 3 montre la page via le Concepteur de Visual Studio.


[![La Page inclut trois contrôles Web : une zone de texte, bouton et étiquette](control-id-naming-in-content-pages-vb/_static/image4.png)](control-id-naming-in-content-pages-vb/_static/image3.png)

**Figure 03**: la Page inclut trois contrôles Web : une zone de texte, un bouton et une étiquette ([cliquez pour afficher l’image en taille réelle](control-id-naming-in-content-pages-vb/_static/image5.png))


Visitez la page via un navigateur et affichez la source HTML. En tant que le balisage ci-dessous montre, la `id` les valeurs des éléments HTML pour les contrôles de zone de texte, bouton et étiquette Web sont une combinaison de la `ID` les valeurs des contrôles Web et la `ID` les valeurs des conteneurs d’affectation de noms dans la page.


[!code-html[Main](control-id-naming-in-content-pages-vb/samples/sample5.html)]

Comme indiqué précédemment dans ce didacticiel, la page maître et ses ContentPlaceHolders servent de conteneurs d’affectation de noms. Par conséquent, les deux contribuent rendu `ID` les valeurs de leurs contrôles imbriqués. Prendre la zone de texte `id` d’attribut, par exemple : `ctl00_MainContent_Age`. N’oubliez pas que le contrôle de zone de texte `ID` valeur a été `Age`. Il est précédé de son contrôle ContentPlaceHolder `ID` valeur, `MainContent`. En outre, cette valeur est préfixée avec la page maître `ID` valeur, `ctl00`. L’effet net est un `id` valeur d’attribut consistant en la `ID` les valeurs de la page maître, le contrôle ContentPlaceHolder et la zone de texte.

La figure 4 illustre ce comportement. Pour déterminer le rendu `id` de la `Age` zone de texte, commencez avec la `ID` la valeur du contrôle zone de texte, `Age`. Ensuite, travailler dans la hiérarchie des contrôles. À chaque conteneur d’attribution de noms (les nœuds avec une couleur pêche), préfixe actuel rendu `id` avec du conteneur d’attribution de noms `id`.


![Les attributs d’id de rendu sont basés sur les valeurs d’ID des conteneurs d’affectation de noms](control-id-naming-in-content-pages-vb/_static/image6.png)

**Figure 04**: rendu le `id` sont des attributs basés sur la `ID` les valeurs des conteneurs d’affectation de noms


> [!NOTE]
> Comme expliqué, la `ctl00` partie rendue `id` attribut constitue le `ID` la valeur de la page maître, mais vous vous demandez peut-être ce `ID` valeur fournie. Nous n’avons pas le spécifié de n’importe où dans notre page maître ou de contenu. La plupart des contrôles de serveur dans une page ASP.NET sont ajoutés explicitement par un balisage déclaratif de la page. Le `MainContent` contrôle ContentPlaceHolder a été spécifié explicitement dans le balisage de `Site.master`; le `Age` zone de texte a été défini `IDIssues.aspx`du balisage. Nous pouvons spécifier le `ID` valeurs pour ces types de contrôles via la fenêtre Propriétés ou à partir de la syntaxe déclarative. Autres contrôles, comme la page maître, ne sont pas définies dans le balisage déclaratif. Par conséquent, leur `ID` doivent générer automatiquement des valeurs pour nous. Le moteur ASP.NET définit le `ID` valeurs lors de l’exécution de ces contrôles dont les ID n’ont pas été définis explicitement. Elle utilise le modèle d’affectation de noms `ctlXX`, où *XX* est une valeur entière de façon séquentielle.


Étant donné que la page maître elle-même sert comme un conteneur d’attribution de noms, les contrôles Web définis dans la page maître également modifiés rendu `id` des valeurs d’attribut. Par exemple, le `DisplayDate` étiquette que nous avons ajouté à la page maître dans le [ *création d’une disposition à l’échelle du Site avec les Pages maîtres* ](creating-a-site-wide-layout-using-master-pages-vb.md) didacticiel a rendu des balises suivantes :


[!code-html[Main](control-id-naming-in-content-pages-vb/samples/sample6.html)]

Notez que la `id` attribut inclut à la fois de la page maître `ID` valeur (`ctl00`) et le `ID` valeur du contrôle Web Label (`DateDisplay`).

## <a name="step-3-programmatically-referencing-web-controls-viafindcontrol"></a>Étape 3 : Référencement par programme via des contrôles Web`FindControl`

Chaque contrôle de serveur ASP.NET inclut un `FindControl("controlID")` méthode qui consiste à rechercher les descendants d' un contrôle pour un contrôle nommé *controlID*. Si un tel contrôle est trouvé, il est retourné ; Si aucun contrôle correspondant n’est trouvé, `FindControl` retourne `Nothing`.

`FindControl`est utile dans les scénarios où vous devez accéder à un contrôle, mais que vous avez une référence directe à celui-ci. Lorsque vous travaillez avec des données de contrôles Web comme le GridView, par exemple, les contrôles dans les champs de GridView sont définies une seule fois dans la syntaxe déclarative, mais lors de l’exécution, une instance du contrôle est créée pour chaque ligne GridView. Par conséquent, les contrôles générés lors de l’exécution existent, mais ne pas avoir une référence directe disponible à partir de la classe code-behind. Par conséquent, nous devons utiliser `FindControl` utiliser par programmation un contrôle spécifique dans les champs de GridView. (Pour plus d’informations sur l’utilisation de `FindControl` pour accéder aux contrôles dans les modèles d’un contrôle Web de données, consultez [personnalisé de mise en forme à données](../../data-access/custom-formatting/custom-formatting-based-upon-data-vb.md).) Ce scénario se produit lors de l’ajout dynamique de contrôles Web à un formulaire Web, une rubrique présentés dans [création d’Interfaces utilisateur dynamique données entrée](https://msdn.microsoft.com/library/aa479330.aspx).

Pour illustrer l’utilisation de la `FindControl` méthode pour rechercher des contrôles dans une page de contenu, créez un gestionnaire d’événements pour le `SubmitButton`de `Click` événements. Dans le Gestionnaire d’événements, ajoutez le code suivant, qui fait référence à par programme le `Age` zone de texte et `Results` d’étiquette à l’aide de la `FindControl` (méthode), puis affiche un message dans `Results` en fonction de l’entrée d’utilisateur.

> [!NOTE]
> Bien entendu, nous n’avez pas besoin d’utiliser `FindControl` pour référencer les contrôles Label et TextBox pour cet exemple. Nous avons y fait référence directement via leurs `ID` valeurs de propriété. Utiliser `FindControl` ici pour illustrer ce qui se passe lorsque vous utilisez `FindControl` à partir d’une page de contenu.


[!code-vb[Main](control-id-naming-in-content-pages-vb/samples/sample7.vb)]

Bien que la syntaxe utilisée pour appeler le `FindControl` méthode diffère légèrement dans les deux premières lignes de `SubmitButton_Click`, ils sont sémantiquement équivalents. Souvenez-vous que tous les contrôles de serveur ASP.NET incluent une `FindControl` (méthode). Cela inclut la `Page` (classe), à partir de quels ASP.NET toutes les classes code-behind doivent dériver de. Par conséquent, l’appel `FindControl("controlID")` équivaut à appeler la méthode `Page.FindControl("controlID")`, en supposant que vous n’avez pas remplacé le `FindControl` méthode dans votre classe code-behind ou dans une classe de base personnalisée.

Après avoir entré ce code, visitez le `IDIssues.aspx` page via un navigateur, entrez votre âge, puis cliquez sur le bouton « Submit ». Lorsque vous cliquez sur le bouton « Submit » un `NullReferenceException` est déclenché (voir Figure 5).


[![Une exception NullReferenceException est déclenchée.](control-id-naming-in-content-pages-vb/_static/image8.png)](control-id-naming-in-content-pages-vb/_static/image7.png)

**Figure 05**: A `NullReferenceException` est déclenché ([cliquez pour afficher l’image en taille réelle](control-id-naming-in-content-pages-vb/_static/image9.png))


Si vous définissez un point d’arrêt dans le `SubmitButton_Click` Gestionnaire d’événements vous verrez que les deux appels à `FindControl` retourner `Nothing`. Le `NullReferenceException` est déclenché quand vous essayez d’accéder à la `Age` la zone de texte `Text` propriété.

Le problème est que `Control.FindControl` recherche uniquement *contrôle*de descendants qui se trouvent dans le même conteneur d’attribution de noms. Étant donné que la page maître constitue un conteneur d’attribution de noms, un appel à `Page.FindControl("controlID")` jamais présente dans l’objet de la page maître `ctl00`. (Reportez-vous à la Figure 4 pour afficher la hiérarchie de contrôle qui affiche le `Page` objet en tant que le parent de l’objet de la page maître `ctl00`.) Par conséquent, le `Results` étiquette et `Age` zone de texte sont introuvables et `ResultsLabel` et `AgeTextBox` sont affectées des valeurs de `Nothing`.

Il existe deux solutions de contournement à ce défi : nous pouvons descendre, un conteneur d’attribution de noms à la fois, pour le contrôle approprié ; ou nous pouvons créer notre propre `FindControl` méthode qui est présente dans les conteneurs d’affectation de noms. Examinons chacune de ces options.

### <a name="drilling-into-the-appropriate-naming-container"></a>L’exploration dans le conteneur d’affectation de noms appropriés

Pour utiliser `FindControl` référence le `Results` étiquette ou `Age` zone de texte, il faut appeler `FindControl` à partir d’un contrôle de l’ancêtre dans le même conteneur d’attribution de noms. Comme montré de la Figure 4, le `MainContent` contrôle ContentPlaceHolder est l’ancêtre uniquement de `Results` ou `Age` qui est dans le même conteneur d’attribution de noms. En d’autres termes, l’appel du `FindControl` méthode à partir de la `MainContent` contrôle, comme indiqué dans l’extrait de code ci-dessous, correctement retourne une référence à la `Results` ou `Age` contrôles.


[!code-vb[Main](control-id-naming-in-content-pages-vb/samples/sample8.vb)]

Toutefois, nous ne pouvons pas travailler avec le `MainContent` ContentPlaceHolder à partir de la classe code-behind de notre page de contenu à l’aide de la syntaxe ci-dessus car ContentPlaceHolder est défini dans la page maître. Au lieu de cela, nous devons utiliser `FindControl` pour obtenir une référence à `MainContent`. Remplacez le code dans le `SubmitButton_Click` Gestionnaire d’événements avec les modifications suivantes :


[!code-vb[Main](control-id-naming-in-content-pages-vb/samples/sample9.vb)]

Si vous visitez la page via un navigateur, entrez votre âge et cliquez sur le bouton « Submit », un `NullReferenceException` est déclenché. Si vous définissez un point d’arrêt dans le `SubmitButton_Click` Gestionnaire d’événements vous verrez que cette exception se produit lors de la tentative d’appeler le `MainContent` l’objet `FindControl` (méthode). Le `MainContent` objet est égal à `Nothing` , car le `FindControl` méthode ne peut pas localiser un objet nommé « MainContent ». La raison sous-jacente est la même qu’avec la `Results` étiquette et `Age` des contrôles de zone de texte : `FindControl` démarre sa recherche à partir du haut de la hiérarchie de contrôle et de ne pas pénétrer d’affectation de noms conteneurs, mais la `MainContent` ContentPlaceHolder est dans la page maître est un conteneur d’attribution de noms.

Avant de pouvoir utiliser `FindControl` pour obtenir une référence à `MainContent`, nous devons tout d’abord une référence au contrôle de page maître. Une fois que nous avons une référence à la page maître, nous pouvons obtenir une référence à la `MainContent` ContentPlaceHolder via `FindControl` et, à partir de là, les références à la `Results` étiquette et `Age` zone de texte (là encore, l’utilisation de `FindControl`). Mais comment obtenir une référence à la page maître ? En examinant le `id` attributs dans le balisage de rendu, il est évident que la page maître `ID` valeur est `ctl00`. Par conséquent, nous pouvons utiliser `Page.FindControl("ctl00")` pour obtenir une référence à la page maître, puis utiliser cet objet pour obtenir une référence à `MainContent`, et ainsi de suite. L’extrait de code suivant illustre cette logique :


[!code-vb[Main](control-id-naming-in-content-pages-vb/samples/sample10.vb)]

Bien que ce code certainement fonctionnent, il part du principe que générées automatiquement de la page maître `ID` sera toujours `ctl00`. Il n’est jamais une bonne idée de faire des hypothèses sur les valeurs générées automatiquement.

Heureusement, une référence à la page maître est accessible via la `Page` de classe `Master` propriété. Par conséquent, au lieu de devoir utiliser `FindControl("ctl00")` pour obtenir une référence de la page maître pour accéder à la `MainContent` ContentPlaceHolder, nous pouvons à la place utiliser `Page.Master.FindControl("MainContent")`. Mise à jour le `SubmitButton_Click` Gestionnaire d’événements avec le code suivant :


[!code-vb[Main](control-id-naming-in-content-pages-vb/samples/sample11.vb)]

Cette fois-ci, visitez la page via un navigateur, en entrant votre âge et en cliquant sur le bouton « Submit » affiche le message dans le `Results` d’étiquette, comme prévu.


[![Âge de l’utilisateur est affiché dans l’étiquette](control-id-naming-in-content-pages-vb/_static/image11.png)](control-id-naming-in-content-pages-vb/_static/image10.png)

**Figure 06**: âge de l’utilisateur est affiché dans l’étiquette ([cliquez pour afficher l’image en taille réelle](control-id-naming-in-content-pages-vb/_static/image12.png))


### <a name="recursively-searching-through-naming-containers"></a>Recherche dans les conteneurs d’affectation de noms de manière récursive

La raison pour laquelle l’exemple de code précédent référencé le `MainContent` contrôle ContentPlaceHolder à partir de la page maître, puis le `Results` étiquette et `Age` des contrôles de zone de texte `MainContent`, est, car le `Control.FindControl` méthode recherche uniquement dans *contrôle*conteneur d’attribution de noms. Avoir `FindControl` rester au sein du conteneur d’attribution de noms de sens dans la plupart des scénarios, car les deux contrôles dans les deux conteneurs d’affectation des noms différents peuvent avoir les mêmes `ID` valeurs. Prenons le cas d’un GridView qui définit un contrôle Web Label nommé `ProductName` dans une de ses TemplateField. Lorsque les données sont liées au contrôle GridView à l’exécution, un `ProductName` étiquette est créée pour chaque ligne GridView. Si `FindControl` recherches à l’aide d’affectation de noms tous les conteneurs et nous avons appelé `Page.FindControl("ProductName")`, quelle instance de l’étiquette doit le `FindControl` de retour ? Le `ProductName` étiquette dans la première ligne GridView ? Celui de la dernière ligne ?

Par conséquent, avoir `Control.FindControl` rechercher simplement *contrôle*d’attribution de noms conteneur est logique dans la plupart des cas. Mais il existe des autres cas, comme celle faisant face à us, où nous avons unique `ID` pour tous les conteneurs d’attribution et souhaitez éviter d’avoir à référencer méticuleusement de chaque conteneur d’attribution de noms dans la hiérarchie des contrôles à un contrôle d’accès. Ayant un `FindControl` variante de recherche de manière récursive tous les conteneurs d’affectation de noms rend DETECTION, trop. Malheureusement, le .NET Framework n’inclut pas une telle méthode.

La bonne nouvelle est que nous pouvons créer notre propre `FindControl` méthode ce récursive recherche tous les conteneurs d’affectation de noms. En fait, à l’aide de *les méthodes d’extension* nous pouvons ajouter un `FindControlRecursive` méthode à la `Control` classe pour accompagner ses existant `FindControl` (méthode).

> [!NOTE]
> Méthodes d’extension sont une fonctionnalité nouvelle de c# 3.0 et Visual Basic 9, qui sont les langues qui sont fournis avec le .NET Framework version 3.5 et Visual Studio 2008. En bref, les méthodes d’extension permettent à un développeur créer une nouvelle méthode pour un type de classe existant à une syntaxe spéciale. Pour plus d’informations sur cette fonctionnalité utile, reportez-vous à mon article, [extension des fonctionnalités de Type de Base avec des méthodes d’Extension](http://aspnet.4guysfromrolla.com/articles/120507-1.aspx).


Pour créer la méthode d’extension, ajoutez un nouveau fichier à la `App_Code` dossier nommé `PageExtensionMethods.vb`. Ajouter une méthode d’extension nommée `FindControlRecursive` qui prend comme entrée un `String` paramètre nommé `controlID`. Pour les méthodes d’extension fonctionne correctement, il est vital que la classe est marquée comme un `Module` et que les méthodes d’extension avoir pour préfixe le `<Extension()>` attribut. En outre, toutes les méthodes d’extension doivent accepter comme leur premier paramètre, un objet du type auquel s’applique la méthode d’extension.

Ajoutez le code suivant à la `PageExtensionMethods.vb` fichier pour définir cette `Module` et `FindControlRecursive` méthode d’extension :


[!code-vb[Main](control-id-naming-in-content-pages-vb/samples/sample12.vb)]

Avec ce code en place, revenir à la `IDIssues.aspx` classe code-behind de la page et commentez actuel `FindControl` les appels de méthode. Remplacez-les par des appels à `Page.FindControlRecursive("controlID")`. Bien trouvée concernant les méthodes d’extension est qu’ils n’apparaissent directement dans les listes déroulantes IntelliSense. Comme le montre la Figure 7, lorsque vous tapez `Page` , puis appuyez sur la période, le `FindControlRecursive` (méthode) est incluse dans la liste déroulante, ainsi que l’autre IntelliSense `Control` méthodes de la classe.


[![Méthodes d’extension sont inclus dans les IntelliSense-listes déroulantes](control-id-naming-in-content-pages-vb/_static/image14.png)](control-id-naming-in-content-pages-vb/_static/image13.png)

**Figure 07**: les méthodes d’Extension sont inclus dans les IntelliSense les zones déroulantes ([cliquez pour afficher l’image en taille réelle](control-id-naming-in-content-pages-vb/_static/image15.png))


Entrez le code suivant dans le `SubmitButton_Click` Gestionnaire d’événements, puis de le tester en visitant la page, en entrant votre âge et en cliquant sur le bouton « Submit ». Comme indiqué dans la Figure 6, le résultat sera le message, « Vous êtes ans d’âge ! »


[!code-vb[Main](control-id-naming-in-content-pages-vb/samples/sample13.vb)]

> [!NOTE]
> Comme méthodes d’extension sont nouvelles pour c# 3.0 et Visual Basic 9, si vous utilisez Visual Studio 2005, vous ne pouvez pas utiliser les méthodes d’extension. Au lieu de cela, vous devez implémenter la `FindControlRecursive` méthode dans une classe d’assistance. [Rick Strahl](http://www.west-wind.com/WebLog/default.aspx) a un exemple de ce type dans ce billet de blog, [Maser des Pages ASP.NET et `FindControl` ](http://www.west-wind.com/WebLog/posts/5127.aspx).


## <a name="step-4-using-the-correctidattribute-value-in-client-side-script"></a>Étape 4 : À l’aide du`id`valeur dans un Script côté Client de l’attribut

Comme indiqué dans l’introduction de ce didacticiel, un contrôle Web du rendu `id` attribut est souvent utilisé dans un script côté client pour faire référence à un élément HTML particulier par programmation. Par exemple, le code JavaScript suivant fait référence à un élément HTML par son `id` , puis affiche sa valeur dans une boîte de dialogue modale :


[!code-csharp[Main](control-id-naming-in-content-pages-vb/samples/sample14.cs)]

Souvenez-vous que dans ASP.NET, les pages qui n’incluent pas d’affectation de noms d’un conteneur, l’élément HTML rendu `id` attribut est identique à d' un contrôle Web `ID` valeur de propriété. Pour cette raison, il est tentant de coder en dur dans `id` des valeurs d’attribut dans le code JavaScript. Autrement dit, si vous savez que vous souhaitez accéder à la `Age` contrôle de zone de texte Web via un script côté client, le faire via un appel à `document.getElementById("Age")`.

Le problème avec cette approche est que, lors de l’utilisation des pages maîtres (ou autres contrôles de conteneur d’attribution de noms), le code HTML restitué `id` n’est pas synonyme d' un contrôle Web `ID` propriété. Votre première inclination peut-être pour visiter la page via un navigateur et affichez la source pour déterminer le réel `id` attribut. Une fois que vous connaissez le rendu `id` valeur, vous pouvez le coller dans l’appel à `getElementById` pour accéder à l’élément HTML que vous avez besoin pour travailler avec via un script côté client. Cette approche est loin d’être idéale, car certaines modifications apportées à de la page la hiérarchie des contrôles ou modifications apportées à la `ID` modifieront les propriétés des contrôles d’affectation de noms résultant `id` attribut, ainsi avec rupture votre code JavaScript.

La bonne nouvelle est que le `id` valeur d’attribut qui est rendu est accessible dans le code côté serveur par le biais d' un contrôle Web [ `ClientID` propriété](https://msdn.microsoft.com/library/system.web.ui.control.clientid.aspx). Vous devez utiliser cette propriété pour déterminer le `id` utilisé dans un script côté client de valeur d’attribut. Par exemple, pour ajouter une fonction JavaScript à la page qui, lorsqu’elle est appelée, affiche la valeur de la `Age` zone de texte dans une boîte de dialogue modale, ajoutez le code suivant à la `Page_Load` Gestionnaire d’événements :


[!code-vb[Main](control-id-naming-in-content-pages-vb/samples/sample15.vb)]

Le code ci-dessus injecte la valeur de la `Age` la zone de texte `ClientID` propriété dans l’appel de JavaScript à `getElementById`. Si vous visitez cette page via un navigateur et affichez la source HTML, vous pouvez consulter le code JavaScript suivant :


[!code-html[Main](control-id-naming-in-content-pages-vb/samples/sample16.html)]

Notez comment le bon `id` valeur de l’attribut `ctl00_MainContent_Age`, apparaît dans l’appel à `getElementById`. Étant donné que cette valeur est calculée au moment de l’exécution, il fonctionne indépendamment des modifications ultérieures apportées à la hiérarchie de contrôle de page.

> [!NOTE]
> Cet exemple JavaScript explique comment ajouter une fonction JavaScript qui référence correctement l’élément HTML rendu par un contrôle serveur. Pour utiliser cette fonction, vous devez créer le code JavaScript pour appeler la fonction lors du chargement du document ou lorsqu’une action utilisateur spécifique apparaît. Pour plus d’informations sur ces et lire les rubriques connexes [fonctionne avec un Script côté Client](https://msdn.microsoft.com/library/aa479302.aspx).


## <a name="summary"></a>Récapitulatif

Certains contrôles serveur ASP.NET servent de conteneurs d’affectation de noms, ce qui affecte le rendu `id` attribut les valeurs de leurs contrôles descendants, ainsi que l’étendue des contrôles canvassed par le `FindControl` (méthode). En ce qui concerne les pages maîtres, la page maître elle-même et ses contrôles ContentPlaceHolder nommez des conteneurs. Par conséquent, nous avons besoin de placer les deux sens un peu plus de travail pour faire référence à la page de contenu à l’aide des contrôles par programme `FindControl`. Dans ce didacticiel, nous avons examiné deux techniques : extraction dans le contrôle ContentPlaceHolder et l’appel son `FindControl` méthode ; et le déploiement de notre propre `FindControl` implémentation de manière récursive qui parcourt tous les conteneurs d’affectation de noms.

Outre les problèmes côté serveur introduisent des conteneurs de dénomination en ce qui concerne le référencement des contrôles Web, il existe également des problèmes côté client. En l’absence de conteneurs d’attribution, le contrôle Web de `ID` valeur de propriété et la restitution de `id` valeur d’attribut sont identiques. Mais avec l’ajout de conteneur d’attribution de noms, le rendu `id` attribut comprend à la fois le `ID` valeurs du contrôle Web et les conteneurs d’affectation de noms dans ascendance de sa hiérarchie contrôle. Ces problèmes d’affectation de noms sont un problème tant que vous utilisez le contrôle de Web `ClientID` propriété pour déterminer le rendu `id` valeur dans votre script côté client de l’attribut.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Pages maîtres ASP.NET et`FindControl`](http://www.west-wind.com/WebLog/posts/5127.aspx)
- [Création d’Interfaces utilisateur dynamique des données](https://msdn.microsoft.com/library/aa479330.aspx)
- [Extension des fonctionnalités de Type de Base avec des méthodes d’Extension](http://aspnet.4guysfromrolla.com/articles/120507-1.aspx)
- [Comment : Référencer le contenu de la Page maître ASP.NET](https://msdn.microsoft.com/library/xxwa0ff0.aspx)
- [Master Pages : Conseils, astuces et interruptions](http://www.odetocode.com/articles/450.aspx)
- [Utilisation de Script côté Client](https://msdn.microsoft.com/library/aa479302.aspx)

### <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de plusieurs manuels ASP/ASP.NET et de créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 3.5 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Scott peut être atteint à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4GuysFromRolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Zack Jones et Suchi Barnerjee. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4GuysFromRolla.com).

>[!div class="step-by-step"]
[Précédent](urls-in-master-pages-vb.md)
[Suivant](interacting-with-the-master-page-from-the-content-page-vb.md)
