---
uid: web-forms/overview/data-access/introduction/master-pages-and-site-navigation-cs
title: "Les Pages maîtres et Navigation du Site (c#) | Documents Microsoft"
author: rick-anderson
description: "Une caractéristique commune des sites Web conviviales est qu’ils disposent d’un schéma de mise en page et de navigation de page cohérente au niveau du site. Ce didacticiel aborde la y..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/31/2010
ms.topic: article
ms.assetid: 5aee8202-a4e3-4aa9-8a95-cd5d156cea4c
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/introduction/master-pages-and-site-navigation-cs
msc.type: authoredcontent
ms.openlocfilehash: 32ddda8d883a99805d2448c9673e585bfe9ef2f4
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="master-pages-and-site-navigation-c"></a>Les Pages maîtres et Navigation du Site (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/4/6/3/463cf87c-4724-4cbc-b7b5-3f866f43ba50/ASPNET_Data_Tutorial_3_CS.exe) ou [télécharger le PDF](master-pages-and-site-navigation-cs/_static/datatutorial03cs1.pdf)

> Une caractéristique commune des sites Web conviviales est qu’ils disposent d’un schéma de mise en page et de navigation de page cohérente au niveau du site. Ce didacticiel examine comment vous pouvez créer une apparence et convivialité cohérentes dans toutes les pages qui peuvent être facilement mis à jour.


## <a name="introduction"></a>Introduction

Une caractéristique commune des sites Web conviviales est qu’ils disposent d’un schéma de mise en page et de navigation de page cohérente au niveau du site. ASP.NET 2.0 introduit deux nouvelles fonctionnalités qui simplifient considérablement le mettant en oeuvre à la fois une page de site à l’échelle mise en page et la navigation : pages maîtres et navigation du site. Les pages maîtres permettent aux développeurs de créer un modèle à l’échelle du site avec les zones modifiables désignés. Ce modèle peut ensuite être appliqué dans des pages ASP.NET dans le site. Ces pages ASP.NET suffit de fournir le contenu pour la page maître de spécifiés modifiables tous les autres balises dans la page maître est identique dans toutes les pages ASP.NET qui utilisent la page maître. Ce modèle permet aux développeurs de définir et centraliser une disposition de la page de l’échelle du site, ce qui le rend plus facile de créer une apparence et convivialité cohérentes dans toutes les pages qui peuvent être facilement mis à jour.

Le [système de site](http://aspnet.4guysfromrolla.com/articles/111605-1.aspx) fournit un mécanisme permettant aux développeurs de page définir un plan de site et une API pour ce plan de site à interroger par programme. Les nouveaux contrôles de navigation Web que le Menu, TreeView et SiteMapPath facilitent son rendent tout ou partie du plan de site dans un élément d’interface utilisateur navigation commune. Nous utilisons le fournisseur de navigation de site par défaut, ce qui signifie que que notre plan de site est défini dans un fichier au format XML.

Pour illustrer ces concepts et faciliter l’utilisation de notre site Web des didacticiels, prenons cette leçon définition d’une mise en page de l’échelle du site, la mise en œuvre un plan de site et l’ajout de l’interface utilisateur de navigation. À la fin de ce didacticiel, nous aurons une conception de site Web poli pour la création de notre didacticiel des pages web.


[![Le résultat final de ce didacticiel](master-pages-and-site-navigation-cs/_static/image2.png)](master-pages-and-site-navigation-cs/_static/image1.png)

**Figure 1**: résultat final le de ce didacticiel ([cliquez pour afficher l’image en taille réelle](master-pages-and-site-navigation-cs/_static/image3.png))


## <a name="step-1-creating-the-master-page"></a>Étape 1 : Création de la Page maître

La première étape consiste à créer la page maître pour le site. Avec le bouton droit maintenant notre site Web est constitué exclusivement au DataSet typé (`Northwind.xsd`, dans le `App_Code` dossier), les classes de la couche BLL (`ProductsBLL.cs`, `CategoriesBLL.cs`, et ainsi de suite, dans le `App_Code` dossier), la base de données (`NORTHWND.MDF`, dans le `App_Data` dossier), le fichier de configuration (`Web.config`) et un fichier de feuille de style CSS (`Styles.css`). J’ai supprimé ces pages et les fichiers qui montre à l’aide de la couche DAL et la couche BLL à partir des deux premiers didacticiels dans la mesure où nous sera de réexamen ces exemples plus en détail dans les didacticiels futures.


![Les fichiers dans notre projet](master-pages-and-site-navigation-cs/_static/image4.png)

**Figure 2**: les fichiers dans notre projet


Pour créer une page maître, avec le bouton droit sur le nom du projet dans l’Explorateur de solutions et choisissez Ajouter un nouvel élément. Sélectionnez le type de Page maître à partir de la liste des modèles, puis nommez-le `Site.master`.


[![Ajouter une nouvelle Page maître au site Web](master-pages-and-site-navigation-cs/_static/image6.png)](master-pages-and-site-navigation-cs/_static/image5.png)

**Figure 3**: ajouter une nouvelle Page maître pour le site Web ([cliquez pour afficher l’image en taille réelle](master-pages-and-site-navigation-cs/_static/image7.png))


Définir la disposition de la page de l’échelle du site ici dans la page maître. Vous pouvez utiliser le mode Design et ajouter quelle que soit vous avez besoin des contrôles de disposition ou Web, ou vous pouvez ajouter manuellement le balisage manuellement dans la vue de Source. Dans la page maître utiliser [des feuilles de style en cascade](http://www.w3schools.com/css/default.asp) de positionnement et styles avec les paramètres de CSS définies dans le fichier externe `Style.css`. Pendant que vous ne savez pas à partir du balisage illustré ci-dessous, les règles CSS sont définies telles que le volet de navigation `<div>`du contenu est positionné afin qu’il apparaît à gauche et a une largeur fixe de 200 pixels.

Site.master


[!code-aspx[Main](master-pages-and-site-navigation-cs/samples/sample1.aspx)]

Une page maître définit la disposition de page statiques et les régions qui peuvent être modifiées par les pages ASP.NET qui utilisent la page maître. Ces zones de contenu modifiable sont indiquées par le contrôle de ContentPlaceHolder, qui peut être consulté dans le contenu `<div>`. Notre page maître a un seul ContentPlaceHolder (`MainContent`), mais la page maître peut avoir plusieurs ContentPlaceHolders.

Le balisage ci-dessus, basculer en mode Design affiche mise en page de la page maître. Toutes les pages ASP.NET qui utilisent cette page maître aura cette disposition uniforme, avec la possibilité de spécifier le balisage de la `MainContent` région.


[![La Page maître, lorsqu’ils sont affichés dans la vue de conception](master-pages-and-site-navigation-cs/_static/image9.png)](master-pages-and-site-navigation-cs/_static/image8.png)

**Figure 4**: la Page maître, quand affichés via le mode création ([cliquez pour afficher l’image en taille réelle](master-pages-and-site-navigation-cs/_static/image10.png))


## <a name="step-2-adding-a-homepage-to-the-website"></a>Étape 2 : Ajout d’une page d’accueil pour le site Web

Avec la page maître définie, nous pouvons ajouter les pages ASP.NET pour le site Web. Nous allons commencer par ajouter `Default.aspx`, page d’accueil de notre site Web. Avec le bouton droit sur le nom du projet dans l’Explorateur de solutions et choisissez Ajouter un nouvel élément. Choisissez l’option de formulaire Web à partir de la liste des modèles et le nom du fichier `Default.aspx`. En outre, la case « Sélectionnez page maître ».


[![Ajoutez un nouveau formulaire Web, la vérification de la page maître Select case à cocher](master-pages-and-site-navigation-cs/_static/image12.png)](master-pages-and-site-navigation-cs/_static/image11.png)

**Figure 5**: ajouter un nouveau formulaire Web, la vérification de la page maître Select case à cocher ([cliquez pour afficher l’image en taille réelle](master-pages-and-site-navigation-cs/_static/image13.png))


Après avoir cliqué sur le bouton OK, nous avons demandé à choisir quelle page maître cette nouvelle page ASP.NET doit utiliser. Vous pouvez avoir plusieurs pages maîtres dans votre projet, nous n'avoir qu’un seul.


[![Choisissez la Page principale de que cette Page ASP.NET doit utiliser](master-pages-and-site-navigation-cs/_static/image15.png)](master-pages-and-site-navigation-cs/_static/image14.png)

**Figure 6**: cliquez sur cette Page ASP.NET doit utilisent la Page maître ([cliquez pour afficher l’image en taille réelle](master-pages-and-site-navigation-cs/_static/image16.png))


Après avoir sélectionné la page maître, les nouvelles pages ASP.NET contient le balisage suivant :

Default.aspx


[!code-aspx[Main](master-pages-and-site-navigation-cs/samples/sample2.aspx)]

Dans le `@Page` la directive il est une référence au fichier de page maître utilisé (`MasterPageFile="~/Site.master"`), et le balisage de la page ASP.NET contient un contrôle de contenu pour chacun des contrôles ContentPlaceHolder définis dans la page maître, avec du contrôle `ContentPlaceHolderID` le contenu de mappage contrôle ContentPlaceHolder spécifique. Le contrôle de contenu est l’endroit où vous placez le balisage vous souhaitez voir apparaître dans ContentPlaceHolder correspondant. Définir le `@Page` de la directive `Title` d’attribut à l’accueil et ajouter du contenu de question au contrôle de contenu :

Default.aspx


[!code-aspx[Main](master-pages-and-site-navigation-cs/samples/sample3.aspx)]

Le `Title` d’attribut dans le `@Page` directive nous permet de définir le titre de la page à partir de la page ASP.NET, même si le `<title>` élément est défini dans la page maître. Nous pouvons également définir le titre par programme, à l’aide de `Page.Title`. Notez également que les références à des feuilles de style de la page maître (tel que `Style.css`) sont automatiquement mis à jour afin qu’ils fonctionnent dans n’importe quelle page ASP.NET, quel que soit le répertoire de la page ASP.NET présente par rapport à la page maître.

Basculer en mode Design, que nous pouvons voir l’aspect de notre page dans un navigateur. Notez que, dans la conception, afficher, de la page ASP.NET que seuls les zones modifiables contenus sont modifiables, le balisage ContentPlaceHolder-non défini dans la page maître est grisée.


[![Le mode de création de la Page ASP.NET montre les zones modifiables et Non modifiables](master-pages-and-site-navigation-cs/_static/image18.png)](master-pages-and-site-navigation-cs/_static/image17.png)

**Figure 7**: le mode Design pour l’ASP.NET Page montre à la fois le modifiable et Non-modifiable régions ([cliquez pour afficher l’image en taille réelle](master-pages-and-site-navigation-cs/_static/image19.png))


Lorsque le `Default.aspx` est consultée par un navigateur, le moteur ASP.NET fusionne automatiquement le contenu de la page maître de la page et ASP. NET du contenu et restitue le contenu fusionné dans le code HTML final est envoyé au navigateur demandeur. Lorsque le contenu de la page maître est mis à jour, toutes les pages ASP.NET qui utilisent cette page maître ont leur contenu refusionnée avec la nouvelle page maître contenue la prochaine fois qu’ils sont demandés. En bref, le modèle de page maître permet une seule page modèle de disposition pour être défini (la page maître) dont les modifications sont immédiatement répercutées dans l’ensemble du site.

## <a name="adding-additional-aspnet-pages-to-the-website"></a>Ajout de Pages ASP.NET supplémentaires pour le site Web

Prenons un moment pour ajouter des stubs de page ASP.NET supplémentaires au site qui conserve finalement les démonstrations de la création de rapports différents. Il y a plus de 35 démonstrations au total, par conséquent, au lieu de créer toutes les pages de stub, nous allons juste créer les premiers. Dans la mesure où il y aura également la plupart des catégories des démonstrations, démonstrations Ajouter pour mieux gérer un dossier pour les catégories. Pour l’instant, ajoutez les trois dossiers suivants :

- `BasicReporting`
- `Filtering`
- `CustomFormatting`

Enfin, ajoutez de nouveaux fichiers comme indiqué dans l’Explorateur de solutions dans la Figure 8. Lorsque vous ajoutez chaque fichier, n’oubliez pas de case à cocher « Sélectionnez page maître ».


![Ajouter les fichiers suivants](master-pages-and-site-navigation-cs/_static/image20.png)

**Figure 8**: ajouter les fichiers suivants


## <a name="step-2-creating-a-site-map"></a>Étape 2 : Création d’un plan de Site

Un des défis de la gestion d’un site Web composé de plus de quelques pages fournit un moyen simple pour les visiteurs de naviguer dans le site. Pour commencer, la structure de navigation du site doit être définie. Ensuite, cette structure doit être traduite en éléments d’interface utilisateur navigable, tels que des menus ou des vues miniatures. Enfin, ce processus doit être géré et mis à jour lorsque de nouvelles pages sont ajoutés au site et existants supprimés. Avant d’ASP.NET 2.0, les développeurs ont dû être sur la création de leur propre structure de navigation du site, sa gestion et traduire en éléments d’interface utilisateur navigable. Avec ASP.NET 2.0, toutefois, les développeurs peuvent utiliser très flexible générés dans le système de navigation de site.

Le système de navigation du site ASP.NET 2.0 fournit un moyen pour un développeur pour définir un plan de site et ensuite accéder à ces informations via une API de programmation. ASP.NET est fourni avec un fournisseur de plan de site qui attend des données de plan de site à stocker dans un fichier XML mis en forme d’une manière particulière. Mais, étant donné que le système de navigation de site est créé sur le [modèle de fournisseur](http://aspnet.4guysfromrolla.com/articles/101905-1.aspx) il peut être étendu pour prendre en charge d’autres méthodes pour sérialiser les informations de plan de site. L’article de Jeff Prosise, [le Site carte fournisseur vous avez déjà été en attente pour SQL](https://msdn.microsoft.com/msdnmag/issues/06/02/WickedCode/default.aspx) montre comment créer un fournisseur de plan de site qui stocke le plan de site dans une base de données SQL Server ; une autre option consiste à créer [un fournisseur de plan de site basé sur la structure de système de fichiers](http://aspnet.4guysfromrolla.com/articles/020106-1.aspx).

Pour ce didacticiel, toutefois, utilisons le fournisseur de plan de site par défaut qui est fourni avec ASP.NET 2.0. Pour créer le plan de site, simplement avec le bouton droit sur le nom du projet dans l’Explorateur de solutions, choisissez Ajouter un nouvel élément, choisissez l’option de plan de Site. Conservez le nom `Web.sitemap` et cliquez sur le bouton Ajouter.


[![Ajouter un plan de Site à votre projet](master-pages-and-site-navigation-cs/_static/image22.png)](master-pages-and-site-navigation-cs/_static/image21.png)

**Figure 9**: ajouter un plan de Site à votre projet ([cliquez pour afficher l’image en taille réelle](master-pages-and-site-navigation-cs/_static/image23.png))


Le fichier de mappage de site est un fichier XML. Notez que Visual Studio fournit IntelliSense pour la structure de plan de site. Le fichier de mappage de site doit avoir le `<siteMap>` nœud en tant que son nœud racine, qui doit contenir précisément l’un `<siteMapNode>` élément enfant. D’abord `<siteMapNode>` élément peut contenir un nombre arbitraire de descendant `<siteMapNode>` éléments.

Définissez le plan de site pour reproduire la structure de système de fichiers. Autrement dit, ajouter un `<siteMapNode>` élément pour chacun des trois dossiers, les enfants `<siteMapNode>` éléments pour chacune des pages ASP.NET dans ces dossiers, comme suit :

Web.sitemap


[!code-xml[Main](master-pages-and-site-navigation-cs/samples/sample4.xml)]

Le plan de site définit la structure de navigation du site Web, qui est une hiérarchie qui décrit les différentes sections du site. Chaque `<siteMapNode>` élément `Web.sitemap` représente une section de la structure de navigation du site.


[![Le plan de Site représente une Structure hiérarchique de navigation](master-pages-and-site-navigation-cs/_static/image25.png)](master-pages-and-site-navigation-cs/_static/image24.png)

**La figure 10**: le plan de Site représente une Structure hiérarchique de navigation ([cliquez pour afficher l’image en taille réelle](master-pages-and-site-navigation-cs/_static/image26.png))


ASP.NET expose la structure du plan de site par le biais du .NET Framework [SiteMap classe](https://msdn.microsoft.com/en-us/library/system.web.sitemap.aspx). Cette classe a un `CurrentNode` propriété, qui retourne des informations sur la section que l’utilisateur visite actuellement ; le `RootNode` propriété retourne la racine du plan de site (accueil, dans notre plan de site). À la fois le `CurrentNode` et `RootNode` propriétés retour [SiteMapNode](https://msdn.microsoft.com/en-us/library/system.web.sitemapnode.aspx) instances qui ont des propriétés comme `ParentNode`, `ChildNodes`, `NextSibling`, `PreviousSibling`, et ainsi de suite, qui permettent du plan de site hiérarchie à traiter.

## <a name="step-3-displaying-a-menu-based-on-the-site-map"></a>Étape 3 : Afficher un Menu basé sur le plan de Site

L’accès aux données dans ASP.NET 2.0 peut être effectué par programmation, comme dans ASP.NET 1.x, ou de façon déclarative, via le nouveau [contrôles de source de données](https://msdn.microsoft.com/en-us/library/ms227679.aspx). Il existe plusieurs contrôles de source de données intégrés tels que le contrôle SqlDataSource, pour accéder aux données de la base de données relationnelle, le contrôle ObjectDataSource, pour accéder aux données à partir de classes et d’autres. Vous pouvez même créer votre propre [contrôles de source de données personnalisé](https://msdn.microsoft.com/asp.net/reference/data/default.aspx?pull=/library/en-us/dnvs05/html/DataSourceCon1.asp).

Les contrôles de source de données font Office de proxy entre votre page ASP.NET et les données sous-jacentes. Pour afficher les données récupérées d’un contrôle de source de données, nous allons généralement ajouter un autre contrôle à la page et la lier au contrôle de source de données. Pour lier un contrôle Web à un contrôle de source de données, définissez simplement le contrôle de Web `DataSourceID` propriété la valeur du contrôle de source de données `ID` propriété.

Pour vous aider à l’utilisation des données de la carte de site, ASP.NET inclut le contrôle SiteMapDataSource, ce qui vous permet de lier un contrôle Web sur le plan de site de notre site Web. Deux contrôles TreeView et Menu sont couramment utilisés pour fournir une interface utilisateur de navigation. Pour lier les données de plan de site à un de ces deux contrôles, ajoutez simplement un SiteMapDataSource à la page avec un contrôle TreeView ou Menu contrôle dont `DataSourceID` propriété est définie en conséquence. Par exemple, nous pouvons ajouter un contrôle de Menu à la page maître à l’aide de la balise suivante :


[!code-aspx[Main](master-pages-and-site-navigation-cs/samples/sample5.aspx)]

Pour un meilleur niveau de contrôle sur le code HTML émis, nous pouvons lier le contrôle SiteMapDataSource pour le contrôle du répéteur, comme suit :


[!code-aspx[Main](master-pages-and-site-navigation-cs/samples/sample6.aspx)]

Le contrôle SiteMapDataSource retourne le niveau de hiérarchie celui de mappage de site à la fois, en commençant par le nœud racine du plan de site (accueil, dans notre plan de site), puis le niveau suivant (rapports de base, le filtrage des rapports et de mise en forme personnalisée) et ainsi de suite. Lors de la liaison SiteMapDataSource pour un répéteur, elle énumère le premier niveau retourné et instancie les `ItemTemplate` pour chaque `SiteMapNode` instance dans ce premier niveau. Pour accéder à une propriété particulière de la `SiteMapNode`, nous pouvons utiliser `Eval(propertyName)`, qui est la manière d’obtenir chacune `SiteMapNode`de `Url` et `Title` propriétés pour le contrôle de lien hypertexte.

L’exemple de répéteur ci-dessus sera restitué le balisage suivant :


[!code-html[Main](master-pages-and-site-navigation-cs/samples/sample7.html)]

Ces nœuds de plan de site (rapports de base, le filtrage des rapports et de mise en forme personnalisée) constituent le *deuxième* au niveau du plan de site en cours de rendu, pas la première. C’est parce que de SiteMapDataSource `ShowStartingNode` est définie sur False, à l’origine SiteMapDataSource ignorer le nœud racine du plan de site et de commencer à la place en retournant le second niveau dans la hiérarchie de plan de site.

Pour afficher les enfants pour le rapport de base, le filtrage des rapports et personnaliser la mise en forme `SiteMapNode` s, nous pouvons ajouter un autre répéteur à l’opération de répétition initiale `ItemTemplate`. Cette deuxième répéteur sera lié à la `SiteMapNode` l’instance `ChildNodes` propriété, comme suit :


[!code-aspx[Main](master-pages-and-site-navigation-cs/samples/sample8.aspx)]

Ces deux répéteurs génèrent dans le balisage suivant (certaines balises a été supprimé par souci de concision) :


[!code-html[Main](master-pages-and-site-navigation-cs/samples/sample9.html)]

À l’aide de CSS styles choisis à partir de [Rachel Andrew](http://www.rachelandrew.co.uk/)du livre [le CSS anthologie : 101 conseils essentiels, astuces, &amp; Hacks](https://www.amazon.com/gp/product/0957921888/qid=1137565739/sr=8-1/ref=pd_bbs_1/103-0562306-3386214?n=507846&amp;s=books&amp;v=glance), le `<ul>` et `<li>` éléments ont un style telles que le balisage génère la sortie de visual suivante :


![Un Menu composé de deux répéteurs et du code CSS](master-pages-and-site-navigation-cs/_static/image27.png)

**Figure 11**: un Menu est composé de deux répéteurs et du code CSS


Ce menu est dans la page maître et lié à la carte de site définie dans `Web.sitemap`, ce qui signifie que toute modification apportée à la carte de site est immédiatement répercutée sur toutes les pages qui utilisent le `Site.master` page maître.

## <a name="disabling-viewstate"></a>Désactivez ViewState

Tous les contrôles ASP.NET peuvent persister éventuellement leur état à la [l’état d’affichage](https://msdn.microsoft.com/msdnmag/issues/03/02/CuttingEdge/), ce qui est sérialisé comme un champ masqué dans le code HTML restitué. État d’affichage utilisée par les contrôles à mémoriser leur état modifiée par programmation sur des publications, telles que les données liées à un contrôle Web de données. Alors que l’état d’affichage permet d’informations pour être conservés sur des publications, il augmente la taille de la balise qui doit être envoyée au client et risque d’encombrement de la page grave si ne sont pas étroitement surveillés. Données des contrôles Web en particulier le GridView sont particulièrement connus pour l’ajout des dizaines de kilo-octets supplémentaires du balisage à une page. Cette augmentation peut être négligeable pour les utilisateurs à large bande ou l’intranet, l’état d’affichage peut ajouter des quelques secondes à l’aller-retour pour les utilisateurs d’accès à distance.

Pour voir l’impact de l’état d’affichage, visitez une page dans un navigateur, puis ensuite afficher la source envoyée par la page web (dans Internet Explorer, accédez au menu Affichage et choisissez l’option Source). Vous pouvez également activer [le traçage des pages](https://msdn.microsoft.com/en-us/library/sfbfw58f.aspx) pour voir l’allocation d’état d’affichage utilisée par chacun des contrôles sur la page. Afficher les informations d’état sont sérialisées dans un champ de formulaire masqué appelé `__VIEWSTATE`, situé dans un `<div>` élément situé juste après l’ouverture `<form>` balise. État d’affichage est conservée uniquement lorsqu’il existe un formulaire Web est utilisée ; Si votre page ASP.NET n’inclut pas un `<form runat="server">` dans sa syntaxe déclarative n’aura pas un `__VIEWSTATE` champ de formulaire masqué dans le balisage restitué.

Le `__VIEWSTATE` champ de formulaire généré par la page maître ajoute environ 1 800 octets au balisage généré de la page. Cette augmentation supplémentaire est principalement due au contrôle du répéteur, comme le contenu du contrôle SiteMapDataSource est conservée à l’état d’affichage. Pendant un 1 800 octets supplémentaires peut ne pas sembler rien heureux, lors de l’utilisation d’un GridView avec de nombreux champs et enregistrements, l’état d’affichage peut augmenter facilement par un facteur de 10 ou plus.

État d’affichage peut être désactivé au niveau de la page ou un contrôle en définissant le `EnableViewState` propriété `false`, ce qui en réduisant la taille du balisage restitué. Depuis l’état d’affichage pour un Web contrôle conserve les données liées aux données de contrôle Web entre des publications (postback) de données, lors de la désactivation de l’état d’affichage pour des données de contrôle Web les données doivent être liées à chaque publication (postback). Dans la version ASP.NET 1.x cette responsabilité est descendu en solliciter au développeur de pages ; avec ASP.NET 2.0, toutefois, les contrôles Web données sont relié à leur contrôle de source de données sur chaque publication (postback) si nécessaire.

Pour réduire l’état d’affichage page Nous allons définie le contrôle de répéteur `EnableViewState` propriété `false`. Cela est possible via la fenêtre Propriétés dans le concepteur ou de façon déclarative dans la vue de Source. Après avoir apporté cette modification, la balise déclarative de répéteur doit ressembler à :


[!code-aspx[Main](master-pages-and-site-navigation-cs/samples/sample10.aspx)]

Une fois cette modification, la page rendu affichage taille de l’état est réduit à un simple 52 octets, une réduction de 97 % dans l’affichage taille de l’état ! Dans les didacticiels de cette série nous allons désactiver l’état d’affichage des données des contrôles Web par défaut afin de réduire la taille du balisage restitué. Dans la plupart des exemples de la `EnableViewState` propriété sera définie `false` et fait sans mention. La seule fois affichage État aborderons se trouve dans les scénarios où il doit être activé dans l’ordre pour les données Web contrôle à fournir ses fonctionnalités attendues.

## <a name="step-4-adding-breadcrumb-navigation"></a>Étape 4 : Ajout d’une Navigation de la barre de navigation

Pour terminer la page maître, vous allez ajouter un élément de l’interface utilisateur de navigation de barre de navigation à chaque page. La barre de navigation montre rapidement des utilisateurs leur position actuelle dans la hiérarchie du site. Ajout d’une barre de navigation dans ASP.NET 2.0 est simplement facile d’ajouter un contrôle SiteMapPath à la page ; Aucun code n’est nécessaire.

Pour notre site, l’ajouter à l’en-tête `<div>`:


[!code-aspx[Main](master-pages-and-site-navigation-cs/samples/sample11.aspx)]

La barre de navigation affiche la page actuelle visite de l’utilisateur dans la hiérarchie de plan de site, ainsi que ce site « ancêtres, « du nœud de plan jusqu'à la racine (accueil, dans notre plan de site).


![La barre de navigation affiche la Page active et de ses ancêtres dans le Site de mappent de la hiérarchie](master-pages-and-site-navigation-cs/_static/image28.png)

**Figure 12**: la barre de navigation affiche la Page active et de ses ancêtres dans le Site de mappent de la hiérarchie


## <a name="step-5-adding-the-default-page-for-each-section"></a>Étape 5 : Ajout de la Page par défaut pour chaque Section.

Les didacticiels de notre site sont décomposent en différentes catégories de rapports de base, le filtrage, la mise en forme personnalisée, et ainsi de suite avec un dossier pour chaque catégorie et les didacticiels correspondantes que les pages ASP.NET dans ce dossier. En outre, chaque dossier contient un `Default.aspx` page. De cette page par défaut, s’affichent tous les didacticiels pour la section en cours. Autrement dit, pour le `Default.aspx` dans les `BasicReporting` dossier nous aurait des liens vers des `SimpleDisplay.aspx`, `DeclarativeParams.aspx`, et `ProgrammaticParams.aspx`. Ici, là encore, nous pouvons utiliser le `SiteMap` classe et un contrôle Web pour afficher ces informations en fonction de la carte de site de données définis dans `Web.sitemap`.

Nous allons afficher une liste non triée à l’aide d’un répéteur, mais cette fois, que nous allons afficher le titre et la description des didacticiels. Étant donné que le balisage et le code pour accomplir ce sera doivent être répété pour chaque `Default.aspx` page, nous pouvons encapsuler cette logique de l’interface utilisateur dans un [contrôle utilisateur](https://msdn.microsoft.com/en-us/library/y6wb1a0e.aspx). Créez un dossier dans le site Web appelé `UserControls` et ajouter à celle d’un nouvel élément du type de contrôle utilisateur Web nommé `SectionLevelTutorialListing.ascx`et ajoutez le balisage suivant :


[![Ajouter un nouveau contrôle utilisateur Web dans le dossier de contrôles utilisateur](master-pages-and-site-navigation-cs/_static/image30.png)](master-pages-and-site-navigation-cs/_static/image29.png)

**Figure 13**: ajouter un nouveau contrôle Web à le `UserControls` dossier ([cliquez pour afficher l’image en taille réelle](master-pages-and-site-navigation-cs/_static/image31.png))


SectionLevelTutorialListing.ascx


[!code-aspx[Main](master-pages-and-site-navigation-cs/samples/sample12.aspx)]

SectionLevelTutorialListing.ascx.cs


[!code-csharp[Main](master-pages-and-site-navigation-cs/samples/sample13.cs)]

Dans l’exemple précédent de répéteur nous lié le `SiteMap` données répéteur déclarative ; le `SectionLevelTutorialListing` contrôle utilisateur, toutefois, le fait par programme. Dans la `Page_Load` Gestionnaire d’événements, une vérification est effectuée pour s’assurer que cette page s URL mappe à un nœud dans le plan de site. Si ce contrôle utilisateur est utilisé dans une page qui n’a pas correspondante `<siteMapNode>` entrée, `SiteMap.CurrentNode` retournera `null` et aucune donnée n’est liée à la répétition. En supposant que nous avez un `CurrentNode`, nous lier son `ChildNodes` collection au répéteur. Étant donné que notre plan de site est configuré tel que le `Default.aspx` page dans chaque section est le nœud parent de tous les didacticiels de cette section, ce code affiche des liens vers les et les descriptions de tous les didacticiels de la section, comme illustré dans la capture d’écran ci-dessous.

Une fois que cette répéteur a été créé, ouvrez le `Default.aspx` pages dans chacun des dossiers, accédez à la vue de conception et faites simplement glisser le contrôle utilisateur à partir de l’Explorateur de solutions vers l’aire de conception où vous souhaitez voir apparaître la liste didacticiel.


[![Le contrôle utilisateur a été ajouté au fichier Default.aspx](master-pages-and-site-navigation-cs/_static/image33.png)](master-pages-and-site-navigation-cs/_static/image32.png)

**La figure 14**: le contrôle utilisateur a été ajouté à `Default.aspx` ([cliquez pour afficher l’image en taille réelle](master-pages-and-site-navigation-cs/_static/image34.png))


[![Les didacticiels de Reporting base sont répertoriés.](master-pages-and-site-navigation-cs/_static/image36.png)](master-pages-and-site-navigation-cs/_static/image35.png)

**Figure 15**: la base des didacticiels Reporting sont répertoriés ([cliquez pour afficher l’image en taille réelle](master-pages-and-site-navigation-cs/_static/image37.png))


## <a name="summary"></a>Résumé

Avec le plan de site défini et la page maître terminée, nous avons maintenant un schéma de mise en page et de navigation de page cohérente pour nos didacticiels liées aux données. Quel que soit le nombre de pages nous ajoutons à notre site, mise à jour les informations de navigation à l’échelle du site page mise en page ou le site est un processus simple et rapide en raison de ces informations est centralisées. Plus précisément, les informations de mise en page sont définies dans la page maître `Site.master` et le site mapper dans `Web.sitemap`. Nous avons n’a pas besoin d’écrire *tout* de code pour obtenir ce mécanisme de mise en page et de navigation de page de l’échelle du site, et nous conservent une prise en charge complète WYSIWYG de concepteur dans Visual Studio.

À l’issue de la couche d’accès aux données et la couche de logique métier et ayant une navigation de page cohérente mise en page et le site définie, nous pouvons commencer à Explorer les modèles de création de rapports courants. Dans le [suivant](../basic-reporting/displaying-data-with-the-objectdatasource-cs.md) trois didacticiels que nous allons examiner les tâches de création de rapports de base afficher les données récupérées à partir de la couche BLL dans le GridView, DetailsView, et contrôle du FormView.

Bonne programmation !

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Vue d’ensemble des Pages maîtres ASP.NET](https://msdn.microsoft.com/en-us/library/wtxbf3hh.aspx)
- [Pages maîtres dans ASP.NET 2.0](http://odetocode.com/Articles/419.aspx)
- [Modèles ASP.NET 2.0](https://msdn.microsoft.com/asp.net/reference/design/templates/default.aspx)
- [Vue d’ensemble de Navigation de Site ASP.NET](https://msdn.microsoft.com/en-us/library/e468hxky.aspx)
- [Examen de ASP.NET 2.0 de Navigation du Site](http://aspnet.4guysfromrolla.com/articles/111605-1.aspx)
- [Fonctionnalités de Navigation ASP.NET 2.0 de Site](https://weblogs.asp.net/scottgu/archive/2005/11/20/431019.aspx)
- [État d’affichage ASP.NET présentation](https://msdn.microsoft.com/library/default.asp?url=/library/en-us/dnaspp/html/viewstate.asp)
- [Comment : activer le traçage d’une Page ASP.NET](https://msdn.microsoft.com/en-us/library/94c55d08%28VS.80%29.aspx)
- [Contrôles utilisateur ASP.NET](https://msdn.microsoft.com/en-us/library/y6wb1a0e.aspx)

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont Liz Shulok, Dennis Patterson et Hilton Giesenow. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](creating-a-business-logic-layer-cs.md)
[Suivant](creating-a-data-access-layer-vb.md)
