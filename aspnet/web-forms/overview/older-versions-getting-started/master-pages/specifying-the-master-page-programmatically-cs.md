---
uid: web-forms/overview/older-versions-getting-started/master-pages/specifying-the-master-page-programmatically-cs
title: "Spécification de la Page maître par programme (c#) | Documents Microsoft"
author: rick-anderson
description: "Recherche sur le contenu de page maître par programme via le Gestionnaire d’événements PreInit de paramètre."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/28/2008
ms.topic: article
ms.assetid: 7c4a3445-2440-4aee-b9fd-779c05e6abb2
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/master-pages/specifying-the-master-page-programmatically-cs
msc.type: authoredcontent
ms.openlocfilehash: 15efb8e2f38b7a405da0c0e12e447e5c3146f025
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="specifying-the-master-page-programmatically-c"></a>Spécification de la Page maître par programme (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/d/6/6/d66ad554-afdd-409e-a5c3-201b774fbb31/ASPNET_MasterPages_Tutorial_09_CS.zip) ou [télécharger le PDF](http://download.microsoft.com/download/d/6/6/d66ad554-afdd-409e-a5c3-201b774fbb31/ASPNET_MasterPages_Tutorial_09_CS.pdf)

> Recherche sur le contenu de page maître par programme via le Gestionnaire d’événements PreInit de paramètre.


## <a name="introduction"></a>Introduction

Depuis l’exemple de la première [ *création d’une disposition à l’échelle du Site à l’aide des Pages maîtres*](creating-a-site-wide-layout-using-master-pages-cs.md), le contenu de toutes les pages ont référencé leur page maître de manière déclarative via la `MasterPageFile` attribut dans le `@Page`la directive. Par exemple, `@Page` directive lie la page de contenu à la page maître `Site.master`:


[!code-aspx[Main](specifying-the-master-page-programmatically-cs/samples/sample1.aspx)]

Le [ `Page` classe](https://msdn.microsoft.com/library/system.web.ui.page.aspx) dans les `System.Web.UI` espace de noms inclut un [ `MasterPageFile` propriété](https://msdn.microsoft.com/library/system.web.ui.page.masterpagefile.aspx) qui retourne le chemin d’accès pour le contenu de page maître ; c’est cette propriété est définie par le `@Page` la directive. Cette propriété peut également être utilisée pour spécifier par programme le contenu de page maître. Cette approche est utile si vous souhaitez affecter dynamiquement la page maître en fonction de facteurs externes, telles que l’utilisateur visite de la page.

Dans ce didacticiel, nous ajouter une deuxième page maître à notre site Web et décider dynamiquement la page maître à utiliser lors de l’exécution.

## <a name="step-1-a-look-at-the-page-lifecycle"></a>Étape 1 : Examiner le cycle de vie de Page

Chaque fois qu’une demande arrive sur le serveur web pour une page ASP.NET qui est une page de contenu, le moteur ASP.NET doit fusible de la page contenu des contrôles dans la page maître correspondant au contrôles ContentPlaceHolder. Cette fusion crée une hiérarchie de contrôle unique qui peut se poursuit dans le cycle de vie de page classique.

La figure 1 illustre cette fusion. Étape 1 dans la Figure 1 illustre le contenu initial et les hiérarchies de contrôle de page maître. À la fin de la phase PreInit le contenu dans la page sont ajoutés aux ContentPlaceHolders correspondantes dans la page maître (étape 2). Après cette fusion, la page maître sert à la racine de la hiérarchie des contrôles fusionnés. Cette fusion contrôle hiérarchie est ensuite ajoutée à la page pour générer la hiérarchie des contrôles finalisé (étape 3). Le résultat net est que la hiérarchie de contrôle page inclut la hiérarchie des contrôles à.


[![La Page maître et des hiérarchies de contrôle de la Page de contenu sont fusionnés ensemble pendant la phase PreInit](specifying-the-master-page-programmatically-cs/_static/image2.png)](specifying-the-master-page-programmatically-cs/_static/image1.png)

**Figure 01**: la Page maître et la Page de contenu contrôle hiérarchies sont fusionnés ensemble pendant la phase PreInit ([cliquez pour afficher l’image en taille réelle](specifying-the-master-page-programmatically-cs/_static/image3.png))


## <a name="step-2-setting-themasterpagefileproperty-from-code"></a>Étape 2 : Définir le`MasterPageFile`propriété à partir du Code

Page maître partakes dans cette fusion dépend de la valeur de la `Page` l’objet `MasterPageFile` propriété. Définissant le `MasterPageFile` d’attribut dans le `@Page` directive a pour effet d’attribution de la `Page`de `MasterPageFile` propriété durant la phase d’initialisation, ce qui constitue la première étape du cycle de vie de la page. Nous pouvons également définir cette propriété par programme. Toutefois, il est impératif que cette propriété est définie avant la fusion dans la Figure 1 a lieu.

Au début de la phase PreInit le `Page` déclenche l’objet son [ `PreInit` événement](https://msdn.microsoft.com/library/system.web.ui.page.preinit.aspx) et appelle son [ `OnPreInit` méthode](https://msdn.microsoft.com/library/system.web.ui.page.onpreinit.aspx). Pour définir la page maître par programme, puis, nous pouvons créer un gestionnaire d’événements pour le `PreInit` événement ou remplacement le `OnPreInit` (méthode). Examinons les deux approches.

Commencez par ouvrir `Default.aspx.cs`, le fichier de classe code-behind pour la page d’accueil de notre site. Ajouter un gestionnaire d’événements de la page `PreInit` événements en tapant le code suivant :


[!code-csharp[Main](specifying-the-master-page-programmatically-cs/samples/sample2.cs)]

À partir d’ici, nous pouvons définir le `MasterPageFile` propriété. Mettre à jour le code afin qu’il affecte la valeur « ~ / Site.master » pour le `MasterPageFile` propriété.


[!code-csharp[Main](specifying-the-master-page-programmatically-cs/samples/sample3.cs)]

Si vous définissez un point d’arrêt et démarrer avec débogage, vous verrez que chaque fois que le `Default.aspx` page est visitée ou est chaque fois qu’une publication (postback) à cette page, le `Page_PreInit` s’exécute le Gestionnaire d’événements et les `MasterPageFile` est affectée à « ~ / Site.master ».

Ou bien, vous pouvez remplacer le `Page` la classe `OnPreInit` (méthode) et définissez le `MasterPageFile` propriété il. Pour cet exemple, nous allons définir la page maître dans une page spécifique, mais plutôt de `BasePage`. Rappelez-vous que nous avons créé une classe de page de base personnalisée (`BasePage`) dans le [ *spécifiant le titre, les balises Meta et les autres en-têtes HTML dans la Page maître* ](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs.md) didacticiel. Actuellement `BasePage` remplace le `Page` de classe `OnLoadComplete` méthode, où il définit la page `Title` propriété basée sur les données de plan de site. Nous allons mettre à jour `BasePage` également substituer la `OnPreInit` méthode pour spécifier la page maître.


[!code-csharp[Main](specifying-the-master-page-programmatically-cs/samples/sample4.cs)]

Étant donné que toutes les pages de notre contenu dérivent `BasePage`, tous les ont maintenant leur page maître assignée par programme. À ce stade le `PreInit` Gestionnaire d’événements dans `Default.aspx.cs` est superflu, n’hésitez pas à supprimer.

### <a name="what-about-thepagedirective"></a>Que se passe-t-il sur la`@Page`Directive ?

Ce qui peut être un peu déroutant est que les pages de contenu `MasterPageFile` propriétés sont désormais spécifiées dans les deux emplacements : par programmation dans le `BasePage` la classe de `OnPreInit` (méthode), ainsi que du `MasterPageFile` attribut dans chaque page de contenu `@Page` la directive.

La première étape du cycle de vie de page est l’étape d’initialisation. Pendant cette étape le `Page` l’objet `MasterPageFile` est affectée à la valeur de la `MasterPageFile` d’attribut dans le `@Page` (s’il est fourni). La phase PreInit suit l’étape d’initialisation, et c’est là où nous avons défini par programmation le `Page` l’objet `MasterPageFile` propriété, en écrasant la valeur affectée à partir de la `@Page` la directive. Car nous définissons le `Page` l’objet `MasterPageFile` propriété supprimer par programme, nous avons la `MasterPageFile` attribut à partir de la `@Page` directive sans affecter l’expérience de l’utilisateur final. Pour convaincre vous-même de ce problème, continuez et supprimez le `MasterPageFile` attribut à partir de la `@Page` directive `Default.aspx` , puis vous accédez à la page via un navigateur. Comme pour tout, la sortie est identique avant que l’attribut a été supprimé.

Si le `MasterPageFile` propriété est définie la `@Page` directive ou par programmation est sans importance pour l’expérience de l’utilisateur final. Toutefois, le `MasterPageFile` l’attribut dans la `@Page` directive est utilisée par Visual Studio au moment du design pour produire le WYSIWYG vue dans le concepteur. Si vous retournez à `Default.aspx` dans Visual Studio et accédez au concepteur, vous verrez le message « erreur de Page maître : la page offre des contrôles qui requièrent une référence de Page maître, mais aucun n’est spécifié » (voir Figure 2).

En résumé, vous devez laisser la `MasterPageFile` l’attribut dans la `@Page` directive bénéficient d’une riche expérience au moment du design dans Visual Studio.


[![Visual Studio utilise le @Page attribut MasterPageFile de la Directive pour restituer la vue Conception](specifying-the-master-page-programmatically-cs/_static/image5.png)](specifying-the-master-page-programmatically-cs/_static/image4.png)

**Figure 02**: Visual Studio utilise le `@Page` de la Directive `MasterPageFile` d’attributs à restituer la vue de conception ([cliquez pour afficher l’image en taille réelle](specifying-the-master-page-programmatically-cs/_static/image6.png))


## <a name="step-3-creating-an-alternative-master-page"></a>Étape 3 : Création d’une Page maître d’Alternative

Car un contenu de page maître peut être définie par programme lors de l’exécution, il est possible de charger dynamiquement une page maître spécifique en fonction de certains critères externes. Cette fonctionnalité peut être utile dans les situations où mise en page du site a besoin pour faire varier en fonction de l’utilisateur. Par exemple, une application web de moteur de blog peut permettre à ses utilisateurs de choisir une disposition pour le blog, où chaque disposition est associée à une autre page maître. Lors de l’exécution, lorsqu’un visiteur consulte le blog de l’utilisateur, l’application web doit déterminer la mise en page de blog et dynamiquement associer la page maître correspondante à la page de contenu.

Nous allons examiner comment charger dynamiquement une page maître lors de l’exécution en fonction de certains critères externes. Notre site Web contient actuellement qu’une seule page maître (`Site.master`). Nous avons besoin d’une autre page maître pour illustrer le choix d’une page maître lors de l’exécution. Cette étape se concentre sur la création et la configuration de la nouvelle page maître. Étape 4 examine déterminer quelle page maître à utiliser lors de l’exécution.

Créer une nouvelle page maître dans le dossier racine nommé `Alternate.master`. Également ajouter une nouvelle feuille de style pour le site Web nommé `AlternateStyles.css`.


[![Ajouter une autre Page maître et CSS de fichiers sur le site Web](specifying-the-master-page-programmatically-cs/_static/image8.png)](specifying-the-master-page-programmatically-cs/_static/image7.png)

**Figure 03**: ajout d’une autre Page maître et fichier CSS au site Web ([cliquez pour afficher l’image en taille réelle](specifying-the-master-page-programmatically-cs/_static/image9.png))


J’ai conçu la `Alternate.master` une page maître pour que le titre affiché en haut de la page, centrée et sur un arrière-plan bleu foncé. J’ai dispensé de la colonne de gauche et déplacés que le contenu sous la `MainContent` contrôle ContentPlaceHolder, qui s’étend désormais sur toute la largeur de la page. En outre, je nixed la liste non triée de leçons et remplacé par une liste horizontale ci-dessus `MainContent`. J’ai mis à jour également les polices et les couleurs utilisées par la page maître (et, par extension, ses pages de contenu). La figure 4 montre `Default.aspx` lorsque vous utilisez la `Alternate.master` page maître.

> [!NOTE]
> ASP.NET inclut la possibilité de définir *thèmes*. Un thème est une collection d’images, fichiers CSS et associées au style Web propriété paramètres de contrôle qui peuvent être appliqués à une page lors de l’exécution. Les thèmes sont la marche à suivre si les dispositions de votre site diffèrent uniquement dans les images affichées et par les règles CSS. Si les dispositions différer plus importante, par exemple à l’aide de contrôles Web différents, ou avoir une disposition radicalement différente, vous devez utiliser des pages maîtres distinctes. Consultez la section obtenir des informations supplémentaires à la fin de ce didacticiel pour plus d’informations sur les thèmes.


[![Nos Pages de contenu peuvent désormais utiliser une nouvelle apparence](specifying-the-master-page-programmatically-cs/_static/image11.png)](specifying-the-master-page-programmatically-cs/_static/image10.png)

**Figure 04**: nos Pages de contenu peuvent désormais utiliser une nouvelle apparence ([cliquez pour afficher l’image en taille réelle](specifying-the-master-page-programmatically-cs/_static/image12.png))


Lorsque le contrôleur et le balisage des pages de contenu sont fusionnées, la `MasterPage` classe vérifications pour vous assurer que le contenu de chaque contrôle dans la page de contenu fait référence à un espace réservé dans la page maître. Une exception est levée si un contrôle de contenu qui fait référence à un ContentPlaceHolder inexistante est trouvé. En d’autres termes, il est impératif que la page maître est assignée à la page de contenu ont un espace réservé pour chaque contrôle dans la page de contenu du contenu.

Le `Site.master` page maître comprend quatre contrôles ContentPlaceHolder :

- `head`
- `MainContent`
- `QuickLoginUI`
- `LeftColumnContent`

Certaines pages de contenu dans notre site Web comprennent seulement une ou deux contrôles de contenu ; d’autres incluent un contrôle de contenu pour chacun des ContentPlaceHolders disponibles. Si notre nouvelle page maître (`Alternate.master`) peut être attribuée à ces pages de contenu qui ont des contrôles de contenu pour tous les ContentPlaceHolders dans `Site.master` , il est essentiel que `Alternate.master` incluent également les mêmes contrôles ContentPlaceHolder comme `Site.master`.

Pour obtenir votre `Alternate.master` page maître pour ressembler à analyser (voir Figure 4), commencez par définir des styles de la page maître dans le `AlternateStyles.css` feuille de style. Ajouter les règles suivantes en `AlternateStyles.css`:


[!code-css[Main](specifying-the-master-page-programmatically-cs/samples/sample5.css)]

Ensuite, ajoutez le balisage déclaratif suivant à `Alternate.master`. Comme vous pouvez le voir, `Alternate.master` contient quatre contrôles ContentPlaceHolder avec le même `ID` valeurs comme les contrôles ContentPlaceHolder dans `Site.master`. En outre, il inclut un contrôle ScriptManager, qui est nécessaire pour les pages de notre site Web qui utilisent l’infrastructure ASP.NET AJAX.


[!code-aspx[Main](specifying-the-master-page-programmatically-cs/samples/sample6.aspx)]

### <a name="testing-the-new-master-page"></a>Test de la nouvelle Page maître

Pour tester cette nouvelle mise à jour de la page maître le `BasePage` la classe `OnPreInit` (méthode) afin que le `MasterPageFile` est affectée à la valeur « ~ / Alternate.maser », puis visitez le site Web. Chaque page doit-elle fonctionner sans erreur à l’exception des deux : `~/Admin/AddProduct.aspx` et `~/Admin/Products.aspx`. Ajout d’un produit pour le contrôle DetailsView dans `~/Admin/AddProduct.aspx` entraîne une `NullReferenceException` à partir de la ligne de code qui tente de définir la page maître `GridMessageText` propriété. Lors de la visite `~/Admin/Products.aspx` un `InvalidCastException` est levée lors du chargement de page avec le message : « impossible à l’objet de conversion de type ' ASP.alternate\_master' en type ' ASP.site\_master'. »

Ces erreurs se produisent, car le `Site.master` classe code-behind inclut les événements publics, propriétés et méthodes qui ne sont pas définis dans `Alternate.master`. La partie de balisage de ces deux pages ont un `@MasterType` directive qui fait référence à la `Site.master` page maître.


[!code-aspx[Main](specifying-the-master-page-programmatically-cs/samples/sample7.aspx)]

En outre, le contrôle DetailsView `ItemInserted` Gestionnaire d’événements dans `~/Admin/AddProduct.aspx` inclut du code qui effectue un cast de le faiblement typée `Page.Master` propriété à un objet de type `Site`. Le `@MasterType` directive (utilisé de cette manière) et la conversion dans le `ItemInserted` Gestionnaire d’événements associe étroitement le `~/Admin/AddProduct.aspx` et `~/Admin/Products.aspx` des pages à la `Site.master` page maître.

Pour interrompre ce couplage étroit, nous pouvons avoir `Site.master` et `Alternate.master` dérivent d’une classe de base commune qui contient des définitions pour les membres publics. Après cela, nous pouvons mettre à jour le `@MasterType` directive pour faire référence à ce type de base commun.

### <a name="creating-a-custom-base-master-page-class"></a>Création d’une classe de Page maître de Base personnalisée

Ajouter un nouveau fichier de classe pour le `App_Code` dossier nommé `BaseMasterPage.cs` et qu’il dérive `System.Web.UI.MasterPage`. Nous devons définir le `RefreshRecentProductsGrid` (méthode) et le `GridMessageText` propriété dans `BaseMasterPage`, mais nous ne pouvons pas simplement les déplacez à partir de `Site.master` parce que ces membres fonctionnent avec les contrôles Web qui sont spécifiques à la `Site.master` page maître (le `RecentProducts` GridView et `GridMessage` étiquette).

Nous devons faire est de configurer `BaseMasterPage` de telle sorte que ces membres sont définis, mais sont réellement implémentées par `BaseMasterPage`de classes dérivées (`Site.master` et `Alternate.master`). Ce type d’héritage est possible en marquant la classe et ses membres en tant que `abstract`. En bref, ajout de la `abstract` (mot clé) à ces deux membres annonce qui `BaseMasterPage` n’a pas implémenté `RefreshRecentProductsGrid` et `GridMessageText`, mais qui seront de ses classes dérivées.

Nous devons également définir le `PricesDoubled` événement dans `BaseMasterPage` et fournir un moyen par les classes dérivées pour déclencher l’événement. Le modèle utilisé dans le .NET Framework pour faciliter ce problème consiste à créer un événement public dans la classe de base et ajouter un document protégé, `virtual` méthode nommée `OnEventName`. Classes dérivées peuvent appeler ensuite cette méthode pour déclencher l’événement ou peuvent remplacer cette méthode pour exécuter du code juste avant ou après le déclenchement de l’événement.

Mise à jour votre `BaseMasterPage` classe pour qu’il contienne le code suivant :


[!code-csharp[Main](specifying-the-master-page-programmatically-cs/samples/sample8.cs)]

Ensuite, accédez à la `Site.master` code-behind de classe et de la dériver de `BaseMasterPage`. Étant donné que `BaseMasterPage` est `abstract` nous devons remplacer celles `abstract` membres ici dans `Site.master`. Ajouter le `override` mot clé pour les définitions de méthode et la propriété. Également mettre à jour le code qui déclenche le `PricesDoubled` événement dans le `DoublePrice` du bouton `Click` Gestionnaire d’événements avec un appel à la classe de base `OnPricesDoubled` (méthode).

Une fois ces modifications la `Site.master` classe code-behind doit contenir le code suivant :


[!code-csharp[Main](specifying-the-master-page-programmatically-cs/samples/sample9.cs)]

Nous devons également mettre à jour `Alternate.master`de classe code-behind pour dériver à partir de `BaseMasterPage` et remplacez les deux `abstract` membres. Mais étant donné que `Alternate.master` ne contient pas un GridView que répertorie les produits les plus récents, ni une étiquette qui affiche un message à un nouveau produit est ajoutée à la base de données, ces méthodes n’avez pas besoin d’effectuer aucune opération.


[!code-csharp[Main](specifying-the-master-page-programmatically-cs/samples/sample10.cs)]

### <a name="referencing-the-base-master-page-class"></a>Faisant référence à la classe de la Page principale de Base

Maintenant que nous avons terminé le `BaseMasterPage` classe et avez nos deux pages maîtres étendant, notre étape finale consiste à mettre à jour le `~/Admin/AddProduct.aspx` et `~/Admin/Products.aspx` pages pour faire référence à ce type. Commencez par modifier le `@MasterType` directive dans les deux pages :


[!code-aspx[Main](specifying-the-master-page-programmatically-cs/samples/sample11.aspx)]

À :


[!code-aspx[Main](specifying-the-master-page-programmatically-cs/samples/sample12.aspx)]

Au lieu de faisant référence à un chemin d’accès du fichier, le `@MasterType` propriété référence le type de base (`BaseMasterPage`). Par conséquent, le fortement typée `Master` utilisée dans les classes de code-behind les deux pages de propriété est désormais de type `BaseMasterPage` (au lieu de type `Site`). Avec cette modification en place réexaminer `~/Admin/Products.aspx`. Auparavant, cela a entraîné une erreur de conversion, car la page est configurée pour utiliser le `Alternate.master` page maître, mais la `@MasterType` directive référencée le `Site.master` fichier. Mais la page affiche maintenant sans erreur. C’est parce que le `Alternate.master` page maître peut être casté en un objet de type `BaseMasterPage` (car il étend).

Il existe une petite modification qui doit être effectuée dans `~/Admin/AddProduct.aspx`. Du contrôle DetailsView `ItemInserted` Gestionnaire d’événements utilise à la fois le fortement typée `Master` propriété et la faiblement typée `Page.Master` propriété. Nous avons résolu la référence fortement typées, lorsque nous avons mis à jour la `@MasterType` directive, mais nous convient de mettre à jour la référence faiblement typée. Remplacez la ligne de code suivante :


[!code-csharp[Main](specifying-the-master-page-programmatically-cs/samples/sample13.cs)]

Avec la commande suivante, qui effectue un cast de `Page.Master` pour le type de base :


[!code-csharp[Main](specifying-the-master-page-programmatically-cs/samples/sample14.cs)]

## <a name="step-4-determining-what-master-page-to-bind-to-the-content-pages"></a>Étape 4 : Déterminer quelle Page maître à lier aux Pages de contenu

Notre `BasePage` classe définit actuellement toutes les pages de contenu' `MasterPageFile` propriétés à une valeur codée en dur dans la phase PreInit du cycle de vie de page. Nous pouvons mettre à jour ce code pour la page maître de base sur un facteur externe. Par exemple la page maître pour charger varie selon les préférences de l’utilisateur actuellement connecté. Dans ce cas, nous devons écrire du code dans le `OnPreInit` méthode dans `BasePage` qui recherche des préférences de page maître de l’utilisateur visite actuellement.

Nous allons créer une page web qui permet à l’utilisateur à choisir la page maître à utiliser - `Site.master` ou `Alternate.master` - et enregistrer ce choix dans une variable de Session. Commencez par créer une nouvelle page web dans le répertoire racine nommé `ChooseMasterPage.aspx`. Lors de la création de cette page (ou toutes les autres pages contenus dorénavant) vous n’avez pas besoin de lier à une page maître, car la page maître est définie par programme `BasePage`. Toutefois, si vous ne liez pas la nouvelle page à une page maître balisage déclaratif de valeur par défaut de la nouvelle page constituée un formulaire Web et autre contenu fourni par la page maître. Vous devez remplacer manuellement ce balisage avec les contrôles de contenu appropriés. Pour cette raison, je trouve plus facile de lier la nouvelle page ASP.NET à une page maître.

> [!NOTE]
> Étant donné que `Site.master` et `Alternate.master` ont le même jeu de contrôles ContentPlaceHolder peu importe quelle page maître choisis lors de la création de la nouvelle page de contenu. Par souci de cohérence, je vous conseille à l’aide de `Site.master`.


[![Ajouter une nouvelle Page de contenu au site Web](specifying-the-master-page-programmatically-cs/_static/image14.png)](specifying-the-master-page-programmatically-cs/_static/image13.png)

**Figure 05**: ajouter une nouvelle Page de contenu pour le site Web ([cliquez pour afficher l’image en taille réelle](specifying-the-master-page-programmatically-cs/_static/image15.png))


Mise à jour le `Web.sitemap` fichier à inclure une entrée pour cette leçon. Ajoutez le balisage suivant sous la `<siteMapNode>` de la leçon Pages maîtres et ASP.NET AJAX :


[!code-xml[Main](specifying-the-master-page-programmatically-cs/samples/sample15.xml)]

Avant d’ajouter du contenu à la `ChooseMasterPage.aspx` page prenez un moment pour mettre à jour de la classe code-behind de la page pour qu’elle dérive `BasePage` (au lieu de `System.Web.UI.Page`). Ensuite, ajoutez un contrôle DropDownList à la page, définissez son `ID` propriété `MasterPageChoice`, et ajouter deux ListItems avec la `Text` les valeurs de « ~ / Site.master » et « ~ / Alternate.master ».

Ajoutez un contrôle bouton Web à la page et définissez ses `ID` et `Text` propriétés `SaveLayout` et « Enregistrer les choix de la mise en page », respectivement. À ce stade balisage déclaratif de votre page doit ressembler à ce qui suit :


[!code-aspx[Main](specifying-the-master-page-programmatically-cs/samples/sample16.aspx)]

Lorsque la page est visitée en premier, il faut afficher les choix de page maître actuellement sélectionnée de l’utilisateur. Créer un `Page_Load` Gestionnaire d’événements et ajoutez le code suivant :


[!code-csharp[Main](specifying-the-master-page-programmatically-cs/samples/sample17.cs)]

Le code ci-dessus s’exécute uniquement sur la première visite de page (et non sur les publications ultérieures). Il vérifie si la variable de Session `MyMasterPage` existe. Dans ce cas, il essaie de rechercher les ListItem correspondant dans le `MasterPageChoice` DropDownList. Si un élément ListItem correspondant est trouvé, son `Selected` est définie sur `true`.

Nous devons également le code qui enregistre les choix de l’utilisateur dans le `MyMasterPage` variable de Session. Créer un gestionnaire d’événements pour le `SaveLayout` du bouton `Click` événement et ajoutez le code suivant :


[!code-csharp[Main](specifying-the-master-page-programmatically-cs/samples/sample18.cs)]

> [!NOTE]
> Au moment où le `Click` Gestionnaire d’événements s’exécute lors de la publication, la page maître a déjà été sélectionnée. Par conséquent, la sélection de liste déroulante, l’utilisateur sera en vigueur jusqu'à ce que la page suivante visiter. Le `Response.Redirect` force le navigateur à demander de nouveau `ChooseMasterPage.aspx`.


Avec la `ChooseMasterPage.aspx` page complète, la dernière tâche doit avoir `BasePage` affecter le `MasterPageFile` basée sur les propriétés de la valeur de la `MyMasterPage` variable de Session. Si la variable de Session n’est pas définie `BasePage` par défaut `Site.master`.


[!code-csharp[Main](specifying-the-master-page-programmatically-cs/samples/sample19.cs)]

> [!NOTE]
> Vous avez déplacé le code qui affecte le `Page` l’objet `MasterPageFile` propriété hors de la `OnPreInit` Gestionnaire d’événements et en deux méthodes distinctes. Cette première méthode, `SetMasterPageFile`, assigne la `MasterPageFile` à la valeur retournée par la deuxième méthode, propriété `GetMasterPageFileFromSession`. J’ai apporté la `SetMasterPageFile` méthode `virtual` afin que les futures classes qui étendent `BasePage` peut éventuellement remplacer cette méthode pour implémenter une logique personnalisée, si nécessaire. Nous allons voir un exemple de substitution `BasePage`de `SetMasterPageFile` propriété dans le didacticiel suivant.


Avec ce code en place, visitez le `ChooseMasterPage.aspx` page. Au départ, le `Site.master` page maître est sélectionné (voir Figure 6), mais l’utilisateur peut choisir une autre page maître à partir de la liste déroulante.


[![Pages de contenu sont affichés à l’aide de la Page Site.master maître](specifying-the-master-page-programmatically-cs/_static/image17.png)](specifying-the-master-page-programmatically-cs/_static/image16.png)

**Figure 06**: Pages de contenu sont affichés à l’aide de la `Site.master` Page maître ([cliquez pour afficher l’image en taille réelle](specifying-the-master-page-programmatically-cs/_static/image18.png))


[![Pages de contenu sont maintenant affichés à l’aide de la Page maître de Alternate.master](specifying-the-master-page-programmatically-cs/_static/image20.png)](specifying-the-master-page-programmatically-cs/_static/image19.png)

**Figure 07**: Pages de contenu sont maintenant affichés à l’aide de la `Alternate.master` Page maître ([cliquez pour afficher l’image en taille réelle](specifying-the-master-page-programmatically-cs/_static/image21.png))


## <a name="summary"></a>Récapitulatif

Quand une page de contenu est visitée, ses contrôles de contenu sont fusionnées avec les contrôles ContentPlaceHolder de sa page maître. Le contenu de page maître est représentée par le `Page` la classe `MasterPageFile` propriété, qui est affectée à la `@Page` de la directive `MasterPageFile` attribut au cours de l’étape d’initialisation. En tant que ce didacticiel ont montré, nous pouvons affecter une valeur à la `MasterPageFile` propriété tant que nous le faire avant la fin de la phase PreInit. Possibilité de spécifier par programme la page maître, la porte pour des scénarios plus avancés, tels que la liaison dynamique d’une page de contenu à une page maître en fonction de facteurs externes s’ouvre.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Diagramme de cycle de vie de Page ASP.NET](http://emanish.googlepages.com/Asp.Net2.0Lifecycle.PNG)
- [Vue d’ensemble du cycle de vie de Page ASP.NET](https://msdn.microsoft.com/library/ms178472.aspx)
- [Vue d’ensemble de l’apparences et des thèmes ASP.NET](https://msdn.microsoft.com/library/ykzx33wh.aspx)
- [Les Pages maîtres : Conseils, astuces et pièges](http://www.odetocode.com/articles/450.aspx)
- [Thèmes dans ASP.NET](http://www.odetocode.com/articles/423.aspx)

### <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de plusieurs manuels ASP/ASP.NET et de créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 3.5 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672329972/4guysfromrollaco). Scott peut être atteint à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4GuysFromRolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Suchi Banerjee. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à[mitchell@4GuysFromRolla.com](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](master-pages-and-asp-net-ajax-cs.md)
[Suivant](nested-master-pages-cs.md)
