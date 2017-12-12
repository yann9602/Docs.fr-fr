---
uid: web-forms/overview/older-versions-getting-started/master-pages/specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs
title: "Spécifier le titre, les balises Meta et les autres en-têtes HTML dans la Page maître (c#) | Documents Microsoft"
author: rick-anderson
description: "Examine les différentes techniques permettant de définir assortis &lt;head&gt; éléments dans la Page maître à partir de la page de contenu."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/21/2008
ms.topic: article
ms.assetid: 0aa1c84f-c9e2-4699-b009-0e28643ecbc6
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/master-pages/specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs
msc.type: authoredcontent
ms.openlocfilehash: fbf980f0086e8c638a8689305d4265561a016887
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-c"></a>Spécifier le titre, les balises Meta et les autres en-têtes HTML dans la Page maître (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/e/e/f/eef369f5-743a-4a52-908f-b6532c4ce0a4/ASPNET_MasterPages_Tutorial_03_CS.zip) ou [télécharger le PDF](http://download.microsoft.com/download/8/f/6/8f6349e4-6554-405a-bcd7-9b094ba5089a/ASPNET_MasterPages_Tutorial_03_CS.pdf)

> Examine les différentes techniques permettant de définir assortis &lt;head&gt; éléments dans la Page maître à partir de la page de contenu.


## <a name="introduction"></a>Introduction

Ont de nouvelles pages maîtres créées dans Visual Studio 2008, par défaut, les deux contrôles ContentPlaceHolder : un nommé head et situé dans le `<head>` élément ; et celle nommée `ContentPlaceHolder1`, placé dans le formulaire Web. L’objectif de `ContentPlaceHolder1` consiste à définir une région dans le formulaire Web qui peut être personnalisée sur une base de page par page. Le `head` ContentPlaceHolder permet aux pages à ajouter du contenu personnalisé à la `<head>` section. (Bien entendu, ces deux ContentPlaceHolders peuvent être modifiés ou supprimés, et ContentPlaceHolder supplémentaire peut-être être ajouté à la page maître. Notre page maître, `Site.master`, a actuellement quatre contrôles ContentPlaceHolder.)

Le code HTML `<head>` élément sert de référentiel pour les informations sur le document de la page web qui ne fait pas partie du document lui-même. Ces informations indiquent notamment le titre de la page web, les méta-informations utilisé par les moteurs de recherche ou robots internes et des liens vers des ressources externes, telles que les flux RSS, JavaScript et CSS fichiers. Certaines de ces informations peuvent être pertinentes par rapport à toutes les pages du site Web. Par exemple, vous souhaiterez globalement importer les mêmes règles CSS et JavaScript fichiers pour chaque page ASP.NET. Toutefois, il existe des parties de la `<head>` élément qui sont spécifiques à la page. Le titre de page est un exemple.

Dans ce didacticiel, nous examiner comment définir globaux et spécifiques à la page `<head>` balisage section dans la page maître et ses pages de contenu.

## <a name="examining-the-master-pagesheadsection"></a>Examen de la Page maître`<head>`Section

Le fichier de page maître par défaut créé par Visual Studio 2008 contient le balisage suivant dans son `<head>` section :


[!code-aspx[Main](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/samples/sample1.aspx)]

Notez que la `<head>` élément contient un `runat="server"` attribut, ce qui indique qu’il s’agit d’un contrôle serveur (plutôt que du code HTML statique). Toutes les pages ASP.NET dérivent la [ `Page` classe](https://msdn.microsoft.com/en-us/library/system.web.ui.page.aspx), qui se trouve dans le `System.Web.UI` espace de noms. Cette classe contient un `Header` propriété qui fournit l’accès à la page `<head>` région. À l’aide de la [ `Header` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.page.header.aspx) nous pouvons définir le titre d’une page ASP.NET ou ajouter des balises supplémentaires pour le rendu `<head>` section. Il est possible, puis, pour personnaliser une page de contenu `<head>` élément en écrivant un peu de code dans la page `Page_Load` Gestionnaire d’événements. Nous examinons comment définir par programme le titre de la page à l’étape 1.

Le balisage indiqué dans le `<head>` élément ci-dessus inclut également un contrôle ContentPlaceHolder nommé head. Ce contrôle ContentPlaceHolder n’est pas nécessaire, comme les pages de contenu peuvent ajouter du contenu personnalisé à la `<head>` élément par programmation. Il est utile, cependant, dans les situations où une page de contenu doit ajouter le balisage statique pour le `<head>` élément en tant que le balisage statique peut être ajouté de façon déclarative pour le contrôle de contenu correspondant plutôt que par programmation.

Outre la `<title>` d’élément et head ContentPlaceHolder, la page maître `<head>` élément doit contenir les `<head>`-niveau balisage qui est commun à toutes les pages. Dans notre site Web, toutes les pages d’utilisent les règles CSS définies dans le `Styles.css` fichier. Par conséquent, nous avons mis à jour le `<head>` élément dans le [ *création d’une disposition à l’échelle du Site avec les Pages maîtres* ](creating-a-site-wide-layout-using-master-pages-cs.md) didacticiel à inclure correspondante `<link>` élément. Notre `Site.master` actuelle de page maître `<head>` balisage est indiqué ci-dessous.


[!code-aspx[Main](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/samples/sample2.aspx)]

## <a name="step-1-setting-a-content-pages-title"></a>Étape 1 : Définir le titre d’une Page de contenu

Titre de la page web est spécifié via la `<title>` élément. Il est important de définir le titre de chaque page à une valeur appropriée. Lorsque vous visitez une page, son titre s’affiche dans la barre de titre du navigateur. En outre, lorsqu’une page de la création des signets, les navigateurs utilisent le titre de la page comme nom suggéré pour le signet. En outre, plusieurs moteurs de recherche affichent le titre de la page lors de l’affichage des résultats de la recherche.

> [!NOTE]
> Par défaut, Visual Studio affecte la `<title>` élément dans la page maître pour « Page sans titre ». De même, les nouvelles pages ASP.NET ont leur `<title>` trop la valeur « Page sans titre, ». Car il est facile d’oublier de définir le titre de la page à une valeur appropriée, cela signifie qu’il n’y a de nombreuses pages sur Internet avec le titre « Page sans titre ». Recherche de Google pour les pages web avec ce titre retourne à peu près les 2,460,000 résultats. Même Microsoft est vulnérable à la publication de pages web avec le titre « Page sans titre ». Au moment de la rédaction, une recherche Google signalé 236 ces pages web dans le domaine Microsoft.com.


Une page ASP.NET permettre spécifier son titre dans une des manières suivantes :

- En plaçant la valeur directement dans le `<title>` élément
- À l’aide de la `Title` d’attribut dans le `<%@ Page %>` (directive)
- Définition par programme de la page `Title` propriété à l’aide de code comme `Page.Title="title"` ou `Page.Header.Title="title"`.

Contenu de pages n’ont pas un `<title>` élément, tel qu’il est défini dans la page maître. Par conséquent, pour définir le titre d’une page de contenu, vous pouvez utiliser la `<%@ Page %>` de la directive `Title` attribut ou définir par programme.

### <a name="setting-the-pages-title-declaratively"></a>Définition de façon déclarative titre de la Page

Titre d’une page de contenu permet de façon déclarative par la `Title` attribut de la [ `<%@ Page %>` directive](https://msdn.microsoft.com/en-us/library/ydy4x04a.aspx). Cette propriété peut être définie en modifiant directement le `<%@ Page %>` directive ou via la fenêtre Propriétés. Examinons les deux approches.

À partir de la vue de Source, recherchez la `<%@ Page %>` directive, qui est en haut du balisage déclaratif de la page. Le `<%@ Page %>` directive `Default.aspx` suit :


[!code-aspx[Main](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/samples/sample3.aspx)]

Le `<%@ Page %>` directive spécifie les attributs spécifiques à la page utilisés par le moteur ASP.NET lors de l’analyse et la compilation de la page. Cela inclut son fichier de page maître, l’emplacement de son fichier de code et son titre, parmi d’autres informations.

Par défaut, lorsque vous créez une nouvelle page de contenu Visual Studio définit le `Title` attribut Page sans titre. Modification `Default.aspx`de `Title` d’attribut à partir de la Page « sans titre » à « Didacticiels de Page maître », puis afficher la page via un navigateur. La figure 1 montre la barre de titre du navigateur, ce qui reflète le nouveau titre de page.


![La barre de titre affiche maintenant &quot;didacticiels de Page maître&quot; au lieu de &quot;Page sans titre&quot;](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image1.png)

**Figure 01**: la barre de titre affiche maintenant « Didacticiels de Page maître » au lieu de « Page sans titre »


Titre de la page peut également être défini à partir de la fenêtre Propriétés. À partir de la fenêtre Propriétés, sélectionnez le DOCUMENT dans la liste déroulante à la charge de niveau de la page Propriétés, qui inclut le `Title` propriété. La figure 2 illustre la fenêtre Propriétés après `Title` a été défini sur « Didacticiels de Page maître ».


![Vous pouvez configurer le titre de la fenêtre Propriétés, trop](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image2.png)

**Figure 02**: vous pouvez configurer le titre de la fenêtre Propriétés, trop


### <a name="setting-the-pages-title-programmatically"></a>Titre de la Page de la définition par programmation

La page maître `<head runat="server">` balisage est convertie en un [ `HtmlHead` classe](https://msdn.microsoft.com/en-us/library/system.web.ui.htmlcontrols.htmlhead.aspx) instance lorsque la page est rendue par le moteur ASP.NET. Le `HtmlHead` classe a un [ `Title` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.htmlcontrols.htmlhead.title.aspx) dont la valeur est répercutée dans le rendu `<title>` élément. Cette propriété est accessible à partir de la classe de code-behind d’une page ASP.NET via `Page.Header.Title`; ce même propriété est également accessibles `Page.Title`.

Exercez-vous à titre de la page par programme, accédez à la `About.aspx` code-behind de la page de classe et de créer un gestionnaire d’événements de la page `Load` événement. Ensuite, définissez le titre de la page « didacticiels de Page maître :: sur :: *date*», où *date* est la date actuelle. Après avoir ajouté ce code votre `Page_Load` Gestionnaire d’événements doit ressembler à ce qui suit :


[!code-csharp[Main](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/samples/sample4.cs)]

La figure 3 montre la barre de titre du navigateur lors de la visite du `About.aspx` page.


![Titre de la Page est définie par programme et inclut la Date actuelle](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image3.png)

**Figure 03**: titre de la Page est définie par programme et inclut la Date actuelle


## <a name="step-2-automatically-assigning-a-page-title"></a>Étape 2 : Affecter automatiquement un titre de Page

Comme nous l’avons vu à l’étape 1, titre d’une page peut être définie de manière déclarative ou par programme. Si vous oubliez de modifier explicitement le titre à un nom plus descriptif, toutefois, votre page aura le titre par défaut, « Page sans titre ». Dans l’idéal, titre de la page est automatiquement défini pour nous si nous ne spécifiez pas explicitement sa valeur. Par exemple, si lors de l’exécution le titre de la page est « Page sans titre », nous pouvons souhaitez ont pour titre automatiquement mis à jour pour être le même que le nom de fichier de la page ASP.NET. La bonne nouvelle est qu’avec un peu de travail initial, qu'il est possible d’avoir le titre assigné automatiquement.

Toutes les pages de web ASP.NET dérivent la `Page` classe dans le `System.Web.UI` espace de noms. Le `Page` classe définit les fonctionnalités minimales requises par une page ASP.NET et expose des propriétés essentielles, telles que `IsPostBack`, `IsValid`, `Request`, et `Response`, entre autres. Souvent, chaque page dans une application web requiert des fonctionnalités supplémentaires ou une fonctionnalité. Une méthode courante pour fournir ce est de créer une classe de page de base personnalisée. Une classe de page de base personnalisée est une classe que vous créez et qui dérive de la `Page` classe et inclut des fonctionnalités supplémentaires. Une fois que cette classe de base a été créée, vous pouvez avoir vos pages ASP.NET dérivent (plutôt que la `Page` classe), ce qui offre les fonctionnalités étendues à vos pages ASP.NET.

Dans cette étape, nous créer une page de base qui définit automatiquement le titre de la page au nom de fichier de la page ASP.NET si le titre n'a pas été explicitement défini autrement. Étape 3 examine la définition du titre de la page selon le plan de site.

> [!NOTE]
> Un examen approfondi de la création et l’utilisation des classes de page de base personnalisée est dépasse le cadre de cette série de didacticiels. Pour plus d’informations, consultez [à l’aide d’une classe de Base personnalisée pour les Classes de Code-Behind de vos Pages ASP.NET](http://aspnet.4guysfromrolla.com/articles/041305-1.aspx).


### <a name="creating-the-base-page-class"></a>Création de la classe de Page de Base

Notre première tâche consiste à créer une classe de page de base, qui est une classe qui étend la `Page` classe. Commencez par ajouter un `App_Code` dossier à votre projet en cliquant sur le nom du projet dans l’Explorateur de solutions, en choisissant Ajouter le dossier ASP.NET, puis en sélectionnant `App_Code`. Ensuite, avec le bouton droit sur le `App_Code` dossier et ajouter une nouvelle classe nommée `BasePage.cs`. La figure 4 montre l’Explorateur de solutions après le `App_Code` dossier et `BasePage.cs` classe ont été ajoutés.


![Ajouter un dossier App_Code et une classe nommée BasePage](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image4.png)

**Figure 04**: ajouter un `App_Code` dossier et une classe nommée`BasePage`


> [!NOTE]
> Visual Studio prend en charge deux modes de gestion de projet : projets de Site Web et les projets d’Application Web. Le `App_Code` dossier est conçu pour être utilisé avec le modèle de projet de Site Web. Si vous utilisez le modèle de projet d’Application Web, placez le `BasePage.cs` classe dans un dossier autre que `App_Code`, tel que `Classes`. Pour plus d’informations sur ce sujet, reportez-vous à [migration d’un projet de Site Web à un projet d’Application Web](http://webproject.scottgu.com/CSharp/Migration2/Migration2.aspx).


Étant donné que la page de base personnalisée sert de classe de base pour les classes de code-behind des pages ASP.NET, il doit étendre la `Page` classe.


[!code-csharp[Main](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/samples/sample5.cs)]

Chaque fois qu’une page ASP.NET est demandée, elle parcoure une série d’étapes, sanctionné dans la page demandée est rendue en HTML. Nous pouvons exploiter une étape en remplaçant le `Page` la classe `OnEvent` (méthode). Pour notre base page Nous allons définir automatiquement le titre s’il n’a pas été explicitement spécifié par le `LoadComplete` étape (qui, comme vous l’aurez deviné, se produit après la `Load` étape).

Pour ce faire, vous devez remplacer le `OnLoadComplete` (méthode) et entrez le code suivant :


[!code-csharp[Main](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/samples/sample6.cs)]

Le `OnLoadComplete` méthode commence par déterminer si le `Title` propriété n'a pas encore été définie explicitement. Si le `Title` propriété `null`, une chaîne vide, ou a la valeur « Page sans titre », elle est assignée au nom de fichier de la page ASP.NET demandée. Le chemin d’accès physique à la page ASP.NET demandée - `C:\MySites\Tutorial03\Login.aspx`, par exemple - est accessible via la `Request.PhysicalPath` propriété. Le `Path.GetFileNameWithoutExtension` méthode est utilisée pour extraire uniquement la partie du nom de fichier, et ce nom de fichier est ensuite assigné à la `Page.Title` propriété.

> [!NOTE]
> Je vous invite à améliorer cette logique pour améliorer le format du titre. Par exemple, si le nom de fichier de la page est `Company-Products.aspx`, le code ci-dessus génère le titre « Produits de la société », mais dans l’idéal, le tiret aurait été remplacé par un espace, comme dans « Produits de l’entreprise ». En outre, envisagez d’ajouter un espace chaque fois qu’il existe un changement de casse. Autrement dit, envisagez d’ajouter du code qui transforme le nom de fichier `OurBusinessHours.aspx` par le titre de « notre heures d’ouverture ».


### <a name="having-the-content-pages-inherit-the-base-page-class"></a>Avec les Pages de contenu d’hériter de la classe de Page de Base

Nous devons maintenant mettre à jour les pages ASP.NET dans notre site dériver à partir de la page de base personnalisée (`BasePage`) au lieu du `Page` classe. Pour accomplir cela, pour chaque classe code-behind et modifiez la déclaration de classe à partir de :


[!code-csharp[Main](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/samples/sample7.cs)]

À :


[!code-csharp[Main](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/samples/sample8.cs)]

Après cela, visitez le site via un navigateur. Si vous visitez une page dont le titre est défini de manière explicite, telle que `Default.aspx` ou `About.aspx`, le titre spécifié de façon explicite est utilisé. Si, toutefois, vous visitez une page dont le titre n’a pas été modifié à partir de la valeur par défaut (« Page sans titre »), la classe de page de base définit le titre au nom de fichier de la page.

La figure 5 illustre le `MultipleContentPlaceHolders.aspx` page lorsqu’ils sont affichés via un navigateur. Notez que le titre est précisément la page Nom du fichier (moins l’extension), « MultipleContentPlaceHolders ».


[![Si un titre n’est pas explicitement spécifié, le nom de fichier de la Page est automatiquement utilisé](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image6.png)](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image5.png)

**Figure 05**: si un titre n’est pas explicitement spécifié, le nom de fichier de la Page est automatiquement utilisé ([cliquez pour afficher l’image en taille réelle](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image7.png))


## <a name="step-3-basing-the-page-title-on-the-site-map"></a>Étape 3 : Baser le titre de la Page sur le plan de Site

ASP.NET propose une infrastructure de carte de site fiable qui permet aux développeurs de page définir un plan de site hiérarchiques dans une ressource externe (par exemple, une table de base de données ou fichier XML), ainsi que les contrôles Web pour afficher des informations sur le plan de site (par exemple, le contrôle SiteMapPath, Menus et contrôles TreeView).

La structure de plan de site est également accessibles par programme à partir de la classe de code-behind d’une page ASP.NET. De cette manière nous pouvons automatiquement définir le titre d’une page au titre de son nœud correspondant dans le plan de site. Nous allons améliorer la `BasePage` classe créée à l’étape 2, afin qu’il offre cette fonctionnalité. Mais tout d’abord, nous devons créer un plan de site pour notre site.

> [!NOTE]
> Ce didacticiel suppose que le lecteur est déjà familiarisé avec ASP. Fonctionnalités de mappage de sites du réseau. Pour plus d’informations sur l’utilisation de la carte de site, consultez mon série d’articles de plusieurs parties, [ASP d’examen. Navigation du Site de NET](http://aspnet.4guysfromrolla.com/articles/111605-1.aspx).


### <a name="creating-the-site-map"></a>Création du mappage de Site

Le système de mappage de site est construit sur le [modèle de fournisseur](http://aspnet.4guysfromrolla.com/articles/101905-1.aspx), qui dissocie le plan de site API à partir de la logique qui sérialise les informations de plan de site entre la mémoire et un magasin persistant. Le .NET Framework est fourni avec le [ `XmlSiteMapProvider` classe](https://msdn.microsoft.com/en-us/library/system.web.xmlsitemapprovider.aspx), qui est le fournisseur de plan de site par défaut. Comme son nom l’indique, `XmlSiteMapProvider` utilise un fichier XML comme magasin de mappage de site. Nous allons utiliser ce fournisseur pour la définition de notre plan de site.

Commencez par créer un fichier de mappage de site dans le dossier de racine du site Web nommé `Web.sitemap`. Pour ce faire, avec le bouton droit sur le nom de site Web dans l’Explorateur de solutions, choisissez Ajouter un nouvel élément, sélectionnez le modèle de plan de Site. Vérifiez que le fichier est nommé `Web.sitemap` et cliquez sur Ajouter.


[![Ajoutez un fichier nommé Web.sitemap au dossier racine du site Web de](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image9.png)](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image8.png)

**Figure 06**: ajouter un fichier nommé `Web.sitemap` au dossier racine du site Web de ([cliquez pour afficher l’image en taille réelle](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image10.png))


Ajoutez le code XML suivant à la `Web.sitemap` fichier :


[!code-xml[Main](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/samples/sample9.xml)]

Ce code XML définit la structure de plan de site hiérarchique indiquée dans la Figure 7.


![Le plan de Site est actuellement composé de trois nœuds](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image11.png)

**Figure 07**: le plan de Site est actuellement composé de trois nœuds


Nous mettrons à jour la structure de plan de site dans les didacticiels futures que nous ajoutons de nouveaux exemples.

### <a name="updating-the-master-page-to-include-navigation-web-controls"></a>Mise à jour de la Page maître pour les contrôles de Navigation Web

Maintenant que nous avons un plan de site défini, nous allons mettre à jour la page maître pour les contrôles de navigation Web. Plus précisément, vous allez ajouter un contrôle ListView à la colonne de gauche dans la section de leçons qui restitue une liste non triée avec un élément de liste pour chaque nœud défini dans le plan de site.

> [!NOTE]
> Le contrôle ListView est nouveau dans ASP.NET version 3.5. Si vous utilisez une version antérieure d’ASP.NET, utilisez plutôt le contrôle du répéteur. Pour plus d’informations sur le contrôle ListView, consultez [à l’aide de ASP.NET 3.5 contrôles ListView et DataPager](http://aspnet.4guysfromrolla.com/articles/122607-1.aspx).


Démarrez en supprimant la balise de liste non triée existante à partir de la section leçons. Ensuite, faites glisser un contrôle ListView à partir de la boîte à outils et placez-le sous les leçons titre. ListView se trouve dans la section données de la boîte à outils, en même temps que les autres contrôles d’affichage : le GridView, DetailsView et FormView. Affectez la propriété de ListView `LessonsList`.

À partir de l’Assistant Configuration de Source de données, choisissez de lier des ListView à un nouveau contrôle SiteMapDataSource nommé `LessonsDataSource`. Le contrôle SiteMapDataSource retourne la structure hiérarchique à partir du système de mappage de site.


[![Lier un contrôle SiteMapDataSource au contrôle ListView de LessonsList](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image13.png)](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image12.png)

**Figure 08**: lier un contrôle SiteMapDataSource le `LessonsList` ListView, contrôle ([cliquez pour afficher l’image en taille réelle](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image14.png))


Après avoir créé le contrôle SiteMapDataSource, nous devons définir les modèles de ListView pour qu’il affiche une liste non triée avec un élément de liste pour chaque nœud retourné par le contrôle SiteMapDataSource. Cela peut être accompli à l’aide de la balise de modèle suivante :


[!code-aspx[Main](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/samples/sample10.aspx)]

Le `LayoutTemplate` génère le balisage pour une liste non triée (`<ul>...</ul>`) alors que le `ItemTemplate` affiche chaque élément retourné par SiteMapDataSource comme un élément de liste (`<li>`) qui contient un lien vers la leçon particulier.

Après avoir configuré les modèles de ListView, visitez le site Web. Comme le montre la Figure 9, la section de leçons contient un seul élément de liste à puces, accueil. Où se trouvent l’et à l’aide de leçons de contrôles ContentPlaceHolder plusieurs ? SiteMapDataSource est conçue pour retourner un ensemble hiérarchique de données, mais le contrôle ListView peut uniquement afficher un seul niveau de la hiérarchie. Par conséquent, uniquement le premier niveau de nœuds retournées par SiteMapDataSource s’affiche.


[![La Section leçons contient un seul élément de liste](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image16.png)](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image15.png)

**Figure 09**: la Section leçons contient un seul élément de liste ([cliquez pour afficher l’image en taille réelle](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image17.png))


Pour afficher plusieurs niveaux, nous avons imbriquer plusieurs ListViews dans le `ItemTemplate`. Cette technique a été examinée dans le [ *Pages maîtres et la Navigation sur le Site* didacticiel](../../data-access/introduction/master-pages-and-site-navigation-cs.md) de mon [utilisation de la série de didacticiels données](../../data-access/index.md). Toutefois, pour cette série de didacticiels notre plan de site contient uniquement un deux niveaux : accueil (niveau supérieur) ; et chaque leçon en tant qu’enfant d’accueil. Au lieu de l’élaboration d’une liste imbriquée, nous pouvons indiquer à la place à SiteMapDataSource pour ne pas retourner le nœud de démarrage en définissant son [ `ShowStartingNode` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.sitemapdatasource.showstartingnode.aspx) à `false`. L’effet net est que SiteMapDataSource démarre en retournant le deuxième niveau de nœuds.

Avec cette modification, ListView affiche les éléments à puce pour l’et à l’aide de plusieurs contrôles ContentPlaceHolder leçons, mais omet un élément de puce Home. Pour résoudre ce problème, nous pouvons ajouter explicitement un élément de liste à puces Home dans le `LayoutTemplate`:


[!code-aspx[Main](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/samples/sample11.aspx)]

En configurant SiteMapDataSource pour omettre le nœud de démarrage et en ajoutant explicitement un élément de puce d’accueil, la section leçons affiche maintenant la sortie prévue.


[![La Section leçons contient un élément de puce pour la page d’accueil et chaque nœud enfant](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image19.png)](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image18.png)

**La figure 10**: la Section leçons contient un élément de puce pour la page d’accueil et chaque nœud enfant ([cliquez pour afficher l’image en taille réelle](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image20.png))


### <a name="setting-the-title-based-on-the-site-map"></a>Définition du titre basé sur le plan de Site

Avec le plan de site en place, nous pouvons mettre à jour notre `BasePage` classe à utiliser le titre spécifié dans le plan de site. Comme nous l’avons fait à l’étape 2, nous voulons uniquement utiliser les titre du nœud de plan de site si le titre de la page n’a pas été défini explicitement par le développeur de pages. Si la page demandée n’a pas défini explicitement titre de la page et se trouve pas dans le plan de site, puis nous revenons au nom de la page demandée (moins l’extension), comme nous l’avons fait à l’étape 2. La figure 11 illustre ce processus de décision.


![En l’Absence d’un explicitement définir titre de la Page, titre du Site carte nœud correspondant est utilisé](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image21.png)

**Figure 11**: en l’Absence d’un explicitement définir titre de la Page, titre du Site carte nœud correspondant est utilisé


Mise à jour la `BasePage` de classe `OnLoadComplete` méthode pour inclure le code suivant :


[!code-csharp[Main](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/samples/sample12.cs)]

Comme précédemment, la `OnLoadComplete` méthode commence par déterminer si le titre de la page a été défini explicitement. Si `Page.Title` est `null`, une chaîne vide, ou la valeur « Page sans titre » est affectée, puis le code affecte automatiquement une valeur à `Page.Title`.

Pour déterminer le titre à utiliser, le code commence par référencer le [ `SiteMap` classe](https://msdn.microsoft.com/en-us/library/system.web.sitemap.aspx)de [ `CurrentNode` propriété](https://msdn.microsoft.com/en-us/library/system.web.sitemap.currentnode.aspx). `CurrentNode`Retourne le [ `SiteMapNode` ](https://msdn.microsoft.com/en-us/library/system.web.sitemapnode.aspx) instance dans le plan de site qui correspond à la page actuellement demandée. En supposant que la page actuellement demandée se trouve dans le plan de site, le `SiteMapNode`de `Title` est affectée au titre de la page. Si la page actuellement demandée n’est pas dans le plan de site, `CurrentNode` retourne `null` et nom de fichier de la page demandée est utilisé comme titre (a été effectuée à l’étape 2).

Figure 12 montre la `MultipleContentPlaceHolders.aspx` page lorsqu’ils sont affichés via un navigateur. Titre de cette page n’est pas définie explicitement, titre du son site carte nœud correspondant est utilisé à la place.


![Titre de la MultipleContentPlaceHolders.aspx Page est extraite du plan de Site](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/_static/image22.png)

**Figure 12**: le `MultipleContentPlaceHolders.aspx` titre de la Page est extraite du plan de Site


## <a name="step-4-adding-other-page-specific-markup-to-theheadsection"></a>Étape 4 : Ajout d’autres balises spécifiques à la Page à la`<head>`Section

Les étapes 1, 2 et 3 a examiné de personnalisation de la `<title>` élément sur une base de page par page. En plus de `<title>`, le `<head>` section peut contenir `<meta>` éléments et `<link>` éléments. Comme indiqué précédemment dans ce didacticiel, `Site.master`de `<head>` section comprend un `<link>` élément `Styles.css`. Étant donné que cela `<link>` élément est défini dans la page maître, il est inclus dans le `<head>` section pour toutes les pages de contenu. Mais comment puis-je sur l’ajout de `<meta>` et `<link>` éléments sur une page par page de base ?

Le moyen le plus simple d’ajouter un contenu spécifique à la page le `<head>` section est en créant un contrôle ContentPlaceHolder dans la page maître. Nous avons déjà un telle ContentPlaceHolder (nommé `head`). Par conséquent, pour ajouter personnalisé `<head>` balisage, créez un correspondant contrôle dans la page de contenu et de placer le balisage il.

Pour illustrer l’ajout personnalisé `<head>` balisage à une page, nous allons inclure un `<meta>` élément description à notre ensemble actuel de pages de contenu. Le `<meta>` élément description fournit une brève description sur la page web ; la plupart des moteurs de recherche intègrent ces informations dans une forme lors de l’affichage des résultats de la recherche.

A `<meta>` élément description a la forme suivante :


[!code-html[Main](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/samples/sample13.html)]

Pour ajouter ce balisage à une page de contenu, ajoutez le texte ci-dessus pour le contrôle de contenu qui est mappé à l’en-tête de la page maître ContentPlaceHolder. Par exemple, pour définir un `<meta>` élément description pour `Default.aspx`, ajoutez le balisage suivant :


[!code-aspx[Main](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/samples/sample14.aspx)]

Étant donné que la tête ContentPlaceHolder n’est pas dans le corps de la page HTML, le balisage ajouté au contrôle de contenu n’est pas affiché dans la vue de conception. Pour voir les `<meta>` description élément visite `Default.aspx` via un navigateur. Une fois que la page a été chargée, afficher la source et notez que le `<head>` comprend le balisage spécifié dans le contrôle de contenu.

Prenez un moment pour ajouter `<meta>` des éléments de description à `About.aspx`, `MultipleContentPlaceHolders.aspx`, et `Login.aspx`.

### <a name="programmatically-adding-markup-to-theheadregion"></a>Ajoutant par programme la balise à le`<head>`région

L’en-tête du ContentPlaceHolder permet d’ajouter des balises personnalisées à la page maître de manière déclarative `<head>` région. Balisage personnalisée peut également être ajouté par programmation. N’oubliez pas que le `Page` de classe `Header` propriété retourne le `HtmlHead` instance définie dans la page maître (la `<head runat="server">`).

La possibilité d’ajouter par programme un contenu le `<head>` région est utile lorsque le contenu à ajouter est dynamique. Peut-être qu’il est basé sur l’utilisateur visite de la page ; peut-être qu’il est chargé à partir d’une base de données. Quelle que soit la raison, vous pouvez ajouter du contenu à la `HtmlHead` en ajoutant des contrôles à la collection de contrôles comme suit :


[!code-csharp[Main](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs/samples/sample15.cs)]

Le code ci-dessus ajoute le `<meta>` mots clés, élément pour les `<head>` région, qui fournit une liste délimitée par des virgules des mots clés qui décrivent la page. Notez que pour ajouter un `<meta>` balise que vous créez un [ `HtmlMeta` ](https://msdn.microsoft.com/en-us/library/system.web.ui.htmlcontrols.htmlmeta.aspx) de l’instance, définissez son `Name` et `Content` propriétés, puis l’ajouter à la `Header`de `Controls` collection. De même, pour ajouter par programmation un `<link>` élément, créer un [ `HtmlLink` ](https://msdn.microsoft.com/en-us/library/system.web.ui.htmlcontrols.htmllink.aspx) de l’objet, définissez ses propriétés, puis ajoutez-le à la `Header`de `Controls` collection.

> [!NOTE]
> Pour ajouter le balisage arbitraire, créez un [ `LiteralControl` ](https://msdn.microsoft.com/en-us/library/system.web.ui.literalcontrol.aspx) de l’instance, définissez son `Text` propriété, puis l’ajouter à la `Header`de `Controls` collection.


## <a name="summary"></a>Résumé

Dans ce didacticiel, nous avons étudié de différentes façons pour ajouter `<head>` balisage région sur une base de page par page. Une page maître doit inclure un `HtmlHead` instance (`<head runat="server">`) avec un espace réservé. Le `HtmlHead` instance autorise des pages de contenu pour accéder par programme le `<head>` région et de façon déclarative et par programme de la page Définir le titre, le contrôle ContentPlaceHolder permet de balisage personnalisée à ajouter à la `<head>` section de façon déclarative par le biais d’un contrôle de contenu.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Définition dynamique de titre de la Page dans ASP.NET](http://aspnet.4guysfromrolla.com/articles/051006-1.aspx)
- [Examen des ASP. Navigation du Site de réseau](http://aspnet.4guysfromrolla.com/articles/111605-1.aspx)
- [Comment utiliser des balises HTML Meta](http://searchenginewatch.com/showPage.html?page=2167931)
- [Pages maîtres dans ASP.NET](http://www.odetocode.com/articles/419.aspx)
- [À l’aide d’ASP.NET 3.5 contrôles ListView et DataPager](http://aspnet.4guysfromrolla.com/articles/122607-1.aspx)
- [À l’aide d’une classe de Base personnalisée pour les Classes de Code-Behind de vos Pages ASP.NET](http://aspnet.4guysfromrolla.com/articles/041305-1.aspx)

### <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de plusieurs manuels ASP/ASP.NET et de créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 3.5 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Scott peut être atteint à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4GuysFromRolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Zack Jones et Suchi Banerjee. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4GuysFromRolla.com).

>[!div class="step-by-step"]
[Précédent](multiple-contentplaceholders-and-default-content-cs.md)
[Suivant](urls-in-master-pages-cs.md)
