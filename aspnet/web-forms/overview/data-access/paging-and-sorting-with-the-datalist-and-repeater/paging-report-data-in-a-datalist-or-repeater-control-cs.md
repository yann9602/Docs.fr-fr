---
uid: web-forms/overview/data-access/paging-and-sorting-with-the-datalist-and-repeater/paging-report-data-in-a-datalist-or-repeater-control-cs
title: "La pagination des données de rapport dans un contrôle de répéteur (c#) ou le contrôle DataList | Documents Microsoft"
author: rick-anderson
description: "Lors de la pagination automatique de répéteur ni DataList offre ou prise en charge de tri, ce didacticiel montre comment ajouter la prise en charge la pagination au DataList ou répéteur..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/13/2006
ms.topic: article
ms.assetid: e8e0809b-25c4-4c3b-8d12-9a17048148ae
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/paging-and-sorting-with-the-datalist-and-repeater/paging-report-data-in-a-datalist-or-repeater-control-cs
msc.type: authoredcontent
ms.openlocfilehash: 4952adff752ec834b8be5f190181be98a034ccfd
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="paging-report-data-in-a-datalist-or-repeater-control-c"></a>La pagination des données de rapport dans un contrôle DataList ou le contrôle du répéteur (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/4/a/7/4a7a3b18-d80e-4014-8e53-a6a2427f0d93/ASPNET_Data_Tutorial_44_CS.exe) ou [télécharger le PDF](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/datatutorial44cs1.pdf)

> Lors de l’offre répéteur ni DataList automatique pagination ou le tri de prise en charge, ce didacticiel montre comment ajouter la prise en charge la pagination au DataList ou répéteur, ce qui permet d’interfaces d’affichage pour la pagination et les données beaucoup plus souple.


## <a name="introduction"></a>Introduction

La pagination et le tri sont deux fonctionnalités très courantes lors de l’affichage des données dans une application en ligne. Par exemple, lorsque vous recherchez dans la documentation d’ASP.NET sur une librairie en ligne, il peut y avoir des centaines de cette documentation, mais le rapport qui répertorie les résultats de recherche répertorie seulement dix correspondances par page. En outre, les résultats peuvent être triés par titre, prix, nombre de pages, nom de l’auteur et ainsi de suite. Comme expliqué dans la [la pagination et le tri des données de rapport](../paging-and-sorting/paging-and-sorting-report-data-cs.md) didacticiel, les contrôles GridView, DetailsView et FormView tous les fournissent la prise en charge de la pagination intégrée peut être activée au battement d’une case à cocher. Le contrôle GridView inclut également la prise en charge de tri.

Malheureusement, répéteur ni DataList offrent la pagination automatique ou le tri de prise en charge. Dans ce didacticiel, nous allons examiner comment ajouter la prise en charge la pagination au DataList ou répéteur. Manuellement, nous devons créer l’interface de pagination, afficher la page d’enregistrements appropriée et n’oubliez pas de la page qui est visitée entre publications (postback). Alors que cela prend plus de temps et de code qu’avec le GridView, DetailsView ou FormView, le contrôle DataList et répéteur permettent beaucoup plus souple la pagination et les données interfaces de l’affichage.

> [!NOTE]
> Ce didacticiel se concentre exclusivement sur la pagination. Dans l’étape suivante du didacticiel, nous allons notre attention à l’ajout de fonctionnalités de tri.


## <a name="step-1-adding-the-paging-and-sorting-tutorial-web-pages"></a>Étape 1 : Ajout de la pagination et le tri des Pages Web d’un didacticiel

Avant de commencer ce didacticiel, permettent de s Prenez tout d’abord ajouter les pages ASP.NET que nous aurons besoin pour ce didacticiel et la suivante. Commencez par créer un nouveau dossier dans le projet nommé `PagingSortingDataListRepeater`. Ensuite, ajoutez les pages ASP.NET cinq suivantes à ce dossier, configuré pour utiliser la page maître de `Site.master`:

- `Default.aspx`
- `Paging.aspx`
- `Sorting.aspx`
- `SortingWithDefaultPaging.aspx`
- `SortingWithCustomPaging.aspx`


![Créez un dossier PagingSortingDataListRepeater et ajouter les Pages ASP.NET didacticiel](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image1.png)

**Figure 1**: créer un `PagingSortingDataListRepeater` dossier et ajouter les Pages ASP.NET didacticiel


Ensuite, ouvrez le `Default.aspx` page et faites glisser le `SectionLevelTutorialListing.ascx` contrôle utilisateur à partir de la `UserControls` dossier sur l’aire de conception. Ce contrôle utilisateur, que nous avons créé dans le [les Pages maîtres et Navigation du Site](../introduction/master-pages-and-site-navigation-cs.md) didacticiel, énumère le plan de site et affiche ces didacticiels dans la section en cours dans une liste à puces.


[![Ajouter le contrôle utilisateur de SectionLevelTutorialListing.ascx vers Default.aspx](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image3.png)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image2.png)

**Figure 2**: ajouter la `SectionLevelTutorialListing.ascx` contrôle utilisateur à `Default.aspx` ([cliquez pour afficher l’image en taille réelle](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image4.png))


Pour disposer d’afficher la pagination et le tri des didacticiels, que nous allons créer la liste à puces, nous devons les ajouter à la carte de site. Ouvrez le `Web.sitemap` et ajoutez le balisage suivant après la modification et la suppression par le balisage de nœud mappage DataList site :


[!code-xml[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample1.xml)]


![Mettre à jour le plan de Site pour inclure les nouvelles Pages ASP.NET](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image5.png)

**Figure 3**: mettre à jour le plan de Site pour inclure les nouvelles Pages ASP.NET


## <a name="a-review-of-paging"></a>Une revue de pagination

Dans les didacticiels précédents, nous avons vu comment parcourir les données dans les contrôles GridView, DetailsView et FormView. Ces trois contrôles offrent une forme simple de pagination appelée *pagination par défaut* qui peut être implémentée en simplement en activant l’option Activer la pagination dans la balise active du contrôle s. Avec la pagination de la valeur par défaut, chaque fois qu’une page de données est demandée sur la première page visitez quand l’utilisateur navigue vers une autre page de données le GridView, DetailsView, ou contrôle FormView demande nouveau *tous les* des données à partir de la ObjectDataSource. Il puis captures out le jeu d’enregistrements à afficher fonction de l’index de la page demandée et le nombre d’enregistrements à afficher par page. Nous avons abordée pagination par défaut en détail dans les [la pagination et le tri des données de rapport](../paging-and-sorting/paging-and-sorting-report-data-cs.md) didacticiel.

Étant donné que la pagination par défaut demande nouveau tous les enregistrements pour chaque page, il n’est pas pratique lors de la pagination via suffisamment grandes quantités de données. Par exemple, imaginez la pagination des 50 000 enregistrements avec une taille de page de 10. Chaque fois que l’utilisateur se déplace vers une nouvelle page, tous les 50 000 enregistrements doivent être récupérées à partir de la base de données, même si seuls dix d'entre eux est affichés.

*La pagination personnalisée* résout les problèmes de performances de pagination par défaut en saisissant uniquement le sous-ensemble précis d’enregistrements à afficher sur la page demandée. Lors de l’implémentation de la pagination personnalisée, nous devons écrire la requête SQL qui retourne uniquement le jeu d’enregistrements approprié efficacement. Nous avons vu comment créer une telle requête à l’aide de SQL Server 2005 s nouvelle [ `ROW_NUMBER()` mot clé](http://www.4guysfromrolla.com/webtech/010406-1.shtml) dans les [efficacement la pagination via grandes quantités de données](../paging-and-sorting/efficiently-paging-through-large-amounts-of-data-cs.md) didacticiel.

Pour implémenter la pagination par défaut dans les contrôles DataList ou répéteur, nous pouvons utiliser le [ `PagedDataSource` classe](https://msdn.microsoft.com/library/system.web.ui.webcontrols.pageddatasource.aspx) comme wrapper pour le `ProductsDataTable` dont le contenu est en cours de pagination. Le `PagedDataSource` classe a un `DataSource` propriété qui peut être affectée à n’importe quel objet énumérable et [ `PageSize` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.pageddatasource.pagesize.aspx) et [ `CurrentPageIndex` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.pageddatasource.currentpageindex.aspx) propriétés qui indiquent le nombre d’enregistrements à Afficher par page et l’index de page actuel. Une fois que ces propriétés ont été définies, le `PagedDataSource` peut être utilisé comme source de données de toutes les données de contrôle Web. Le `PagedDataSource`, lors de l’énumération, retournent seulement le sous-ensemble approprié d’enregistrements de son interne `DataSource` selon la `PageSize` et `CurrentPageIndex` propriétés. La figure 4 illustre les fonctionnalités de la `PagedDataSource` classe.


![Le PagedDataSource encapsule un objet énumérable avec une Interface paginable](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image6.png)

**Figure 4**: le `PagedDataSource` encapsule un objet énumérable avec une Interface paginable


Le `PagedDataSource` objet peut être créé et configuré directement à partir de la couche de logique métier et lié à un contrôle DataList ou un répéteur via un ObjectDataSource, ou peut être créée et configurée directement dans la classe code-behind de pages ASP.NET. Si cette dernière approche est utilisée, nous devons renoncer à l’aide de ObjectDataSource et lie les données paginées au DataList ou répéteur par programme.

Le `PagedDataSource` objet possède également des propriétés pour prendre en charge la pagination personnalisée. Toutefois, nous pouvons contourner à l’aide un `PagedDataSource` pour la pagination personnalisée parce que nous avons déjà des méthodes de la couche BLL la `ProductsBLL` classe conçue pour la pagination personnalisée qui retournent les enregistrements précis à afficher.

Dans ce didacticiel, nous examinerons implémenter la pagination de la valeur par défaut dans un contrôle DataList en ajoutant une nouvelle méthode pour le `ProductsBLL` classe renvoyant configuré convenablement `PagedDataSource` objet. Dans l’étape suivante du didacticiel, nous verrons comment utiliser la pagination personnalisée.

## <a name="step-2-adding-a-default-paging-method-in-the-business-logic-layer"></a>Étape 2 : Ajout d’une méthode de la pagination par défaut de la couche de logique métier

Le `ProductsBLL` classe a actuellement une méthode pour retourner toutes les informations de produit `GetProducts()` et un pour retourner un sous-ensemble de produits à un index de départ `GetProductsPaged(startRowIndex, maximumRows)`. Avec la pagination de la valeur par défaut, le GridView, DetailsView et FormView contrôle utilisent tous le `GetProducts()` méthode pour récupérer tous les produits, mais utilisez ensuite une `PagedDataSource` en interne pour afficher uniquement le sous-ensemble correct d’enregistrements. Pour répliquer cette fonctionnalité avec les contrôles DataList et répéteur, nous pouvons créer une nouvelle méthode dans la couche BLL qui reproduit ce comportement.

Ajoutez une méthode à la `ProductsBLL` classe nommée `GetProductsAsPagedDataSource` qui accepte deux paramètres d’entrée entière :

- `pageIndex`l’index de la page à afficher, indexé à zéro, et
- `pageSize`le nombre d’enregistrements à afficher par page.

`GetProductsAsPagedDataSource`commence par récupérer *tous les* des enregistrements à partir de `GetProducts()`. Elle crée ensuite un `PagedDataSource` objet, en définissant ses `CurrentPageIndex` et `PageSize` propriétés aux valeurs du passé en `pageIndex` et `pageSize` paramètres. La méthode se termine par retour de ce type de configuration `PagedDataSource`:


[!code-csharp[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample2.cs)]

## <a name="step-3-displaying-product-information-in-a-datalist-using-default-paging"></a>Étape 3 : Afficher des informations de produit dans un contrôle DataList à l’aide de la pagination par défaut

Avec la `GetProductsAsPagedDataSource` méthode ajoutée à la `ProductsBLL` (classe), nous pouvons de maintenant créer un contrôle DataList ou un répéteur qui fournit la pagination par défaut. Commencez par ouvrir le `Paging.aspx` page dans le `PagingSortingDataListRepeater` faites glisser un contrôle DataList à partir de la boîte à outils vers le concepteur, la définition du contrôle DataList s `ID` propriété `ProductsDefaultPaging`. À partir de la balise active du contrôle DataList s, créez un nouveau ObjectDataSource nommé `ProductsDefaultPagingDataSource` et le configurer afin qu’il récupère des données à l’aide du `GetProductsAsPagedDataSource` (méthode).


[![Créer un ObjectDataSource et configurez-le pour utiliser le () GetProductsAsPagedDataSource (méthode)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image8.png)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image7.png)

**Figure 5**: créer un ObjectDataSource et le configurer pour utiliser le `GetProductsAsPagedDataSource` `()` (méthode) ([cliquez pour afficher l’image en taille réelle](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image9.png))


Définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None).


[![Définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image11.png)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image10.png)

**Figure 6**: définir les listes déroulantes dans la mise à jour, insérer et supprimer des onglets à (None) ([cliquez pour afficher l’image en taille réelle](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image12.png))


Étant donné que la `GetProductsAsPagedDataSource` méthode attend deux paramètres d’entrée, l’Assistant nous invite pour la source de ces valeurs de paramètre.

Les index de la page et les valeurs de taille de page doivent être mémorisées entre publications (postback). Ils peuvent être stockées dans l’état d’affichage, rendues persistantes dans la chaîne de requête, stockées dans des variables de session ou conservés à l’aide d’une autre technique. Pour ce didacticiel, nous allons utiliser la chaîne de requête, qui présente l’avantage de permettre à une page spécifique de données pour être le signet.

En particulier, utilisez la querystring champs pageIndex et pageSize pour le `pageIndex` et `pageSize` paramètres, respectivement (voir la Figure 7). Prenez un moment pour définir les valeurs par défaut pour ces paramètres, comme les valeurs de chaîne de requête a gagné t être présent lorsque l’utilisateur visite tout d’abord cette page. Pour `pageIndex`, la valeur par défaut la valeur 0 (qui affiche la première page de données) et `pageSize` valeur par défaut 4 s.


[![Utiliser la chaîne de requête comme Source pour les paramètres de pageIndex et de pageSize](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image14.png)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image13.png)

**Figure 7**: utiliser la chaîne de requête comme Source pour le `pageIndex` et `pageSize` paramètres ([cliquez pour afficher l’image en taille réelle](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image15.png))


Après avoir configuré l’ObjectDataSource, Visual Studio crée automatiquement un `ItemTemplate` pour le contrôle DataList. Personnaliser le `ItemTemplate` afin qu’uniquement le nom de produit s, catégorie et fournisseur sont affichés. Également définir du contrôle DataList s `RepeatColumns` propriété à 2, son `Width` à 100 % et sa `ItemStyle` s `Width` à 50 %. Ces paramètres de largeur fournira un espacement identique pour les deux colonnes.

Après avoir apporté ces modifications, le balisage s DataList et ObjectDataSource doit ressembler à ce qui suit :


[!code-aspx[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample3.aspx)]

> [!NOTE]
> Étant donné que nous n’effectuez pas une mise à jour ou supprimer des fonctionnalités dans ce didacticiel, vous pouvez désactiver l’état d’affichage DataList s pour réduire la taille de la page rendue.


Lorsque initialement vous visitez cette page via un navigateur, ni le `pageIndex` ni `pageSize` paramètres querystring sont fournies. Par conséquent, les valeurs par défaut de 0 et 4 sont utilisés. Comme le montre la Figure 8, cela entraîne un contrôle DataList qui affiche les quatre premiers produits.


[![Quatre premiers produits sont répertoriés.](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image17.png)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image16.png)

**Figure 8**: le premier quatre produits répertoriés ([cliquez pour afficher l’image en taille réelle](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image18.png))


Sans une interface de pagination, là s actuellement non simple signifie que pour un utilisateur accéder à la deuxième page de données. Nous allons créer une interface d’échange à l’étape 4. Pour l’instant, cependant, la pagination uniquement faire en spécifiant directement les critères de la pagination dans la chaîne de requête. Par exemple, pour afficher la deuxième page, modifiez l’URL dans la barre d’adresses du navigateur s `Paging.aspx` à `Paging.aspx?pageIndex=2` et appuyez sur ENTRÉE. Cela provoque la deuxième page de données à afficher (voir la Figure 9).


[![La deuxième Page de données s’affiche.](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image20.png)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image19.png)

**Figure 9**: la deuxième Page de données s’affiche ([cliquez pour afficher l’image en taille réelle](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image21.png))


## <a name="step-4-creating-the-paging-interface"></a>Étape 4 : Création de l’Interface de pagination

Il existe une variété de différentes interfaces de pagination qui peuvent être implémentées. Les contrôles GridView, DetailsView et FormView fournissent quatre interfaces différentes choisir entre :

- **Ensuite, précédente** les utilisateurs peuvent déplacer une page à la fois, à l’une de suivante ou précédente.
- **Suivant, précédent, le premier, dernier** en plus des boutons suivant et précédent, cette interface inclut les premier et dernier boutons permettant de passer à la page de la première ou dernière.
- **Numérique** répertorie les numéros de page dans l’interface de pagination, qui permet à un utilisateur accéder rapidement à une page particulière.
- **Numérique, tout d’abord, dernier** outre les numéros de page numérique comprend des boutons permettant de passer à la page de la première ou dernière.

Pour le contrôle DataList et un répéteur, nous ne sommes responsables pour le choix d’une interface de pagination et la mise en œuvre. Cela implique la création de contrôles Web nécessaires dans la page et affichage de la page demandée clic sur un bouton de la pagination interface. En outre, certains contrôles d’interface de pagination peut-être doit être désactivée. Par exemple, lors de l’affichage de la première page de données à l’aide de la prochaine, précédent, tout d’abord, dernière d’interface, les boutons à la fois le premier et le précédent sont désactivées.

Pour ce didacticiel, s permettent, utilisez le suivant, précédent, tout d’abord, dernière interface. Ajoutez quatre contrôles Web de bouton à la page et définissez leurs `ID` s pour `FirstPage`, `PrevPage`, `NextPage`, et `LastPage`. Définir le `Text` propriétés &lt; &lt; tout d’abord, &lt; Prev, en regard &gt;et le dernier &gt; &gt; .


[!code-aspx[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample4.aspx)]

Ensuite, créez un `Click` Gestionnaire d’événements pour chacun de ces boutons. Dans un instant, nous allons ajouter le code nécessaire pour afficher la page demandée.

## <a name="remembering-the-total-number-of-records-being-paged-through"></a>Mémoriser le nombre Total d’enregistrements en cours par le biais de pagination

Quelle que soit l’interface d’échange sélectionné, nous devons de calcul et n’oubliez pas le nombre total d’enregistrements en cours de pagination via. Le nombre total de lignes (conjointement avec la taille de page) détermine le nombre total de pages de données sont en cours paginés, qui détermine les contrôles d’interface de pagination sont ajoutés ou sont activés. Suivant, précédent, premier, dernier interface que nous créons, le nombre de pages est utilisé de deux manières :

- Pour déterminer si nous visualisons la dernière page, auquel cas les boutons suivant et dernier sont désactivées.
- Si l’utilisateur clique sur le bouton dernière, que nous devons les tous à la dernière page, dont l’index est un inférieur à la page compte.

Le nombre de pages est calculé comme le plafond du nombre total de lignes divisée par la taille de page. Par exemple, si nous examinons la pagination dans les 79 enregistrements avec quatre enregistrements par page, le nombre de pages est 20 (le plafond de 79 / 4). Si nous utilisons l’interface de pagination numériques, ces informations nous informant que le nombre de boutons de page numériques à afficher ; Si notre interface de pagination propose les boutons suivant ou la dernière, le nombre de pages est utilisé pour déterminer le moment désactiver les boutons suivant ou dernier.

Si l’interface de pagination inclut un bouton en dernier, il est impératif que le nombre total d’enregistrements transmise par radiomessagerie à être mémorisées entre publications (postback) afin que lorsque l’utilisateur clique sur le bouton de dernière nous pouvons déterminer le dernier index de la page. Pour faciliter ce processus, créez un `TotalRowCount` propriété dans la classe code-behind de pages ASP.NET qui conserve sa valeur à l’état d’affichage :


[!code-csharp[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample5.cs)]

En plus de `TotalRowCount`, prenez une minute pour créer des propriétés au niveau des pages en lecture seule pour facilement l’accès à l’index de page, la taille de la page et du nombre de pages :


[!code-csharp[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample6.cs)]

## <a name="determining-the-total-number-of-records-being-paged-through"></a>Déterminer le nombre Total d’enregistrements en cours par le biais de pagination

Le `PagedDataSource` objet retourné par le s ObjectDataSource `Select()` méthode est qu’il contient *tous les* des enregistrements produit, même si uniquement un sous-ensemble d'entre elles s’affichent dans le contrôle DataList. Le `PagedDataSource` s [ `Count` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.pageddatasource.count.aspx) retourne uniquement le nombre d’éléments qui s’affichera dans le contrôle DataList ; le [ `DataSourceCount` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.pageddatasource.datasourcecount.aspx) retourne le nombre total d’éléments dans le `PagedDataSource`. Par conséquent, nous devons attribuer ASP.NET page s `TotalRowCount` la valeur de la propriété de la `PagedDataSource` s `DataSourceCount` propriété.

Pour ce faire, créez un gestionnaire d’événements pour ObjectDataSource s `Selected` événement. Dans le `Selected` Gestionnaire d’événements, nous avons accès à la valeur de retour de la s ObjectDataSource `Select()` méthode dans ce cas, le `PagedDataSource`.


[!code-csharp[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample7.cs)]

## <a name="displaying-the-requested-page-of-data"></a>Affichage de la Page demandée de données

Lorsque l’utilisateur clique sur l’un des boutons de l’interface de pagination, nous devons afficher la page demandée de données. Étant donné que les paramètres de pagination sont spécifiées via la chaîne de requête pour afficher la page de données demandée `Response.Redirect(url)` pour que l’utilisateur s navigateur redemander le `Paging.aspx` page avec les paramètres appropriés de la pagination. Par exemple, pour afficher la deuxième page de données, nous redirige l’utilisateur à `Paging.aspx?pageIndex=1`.

Pour faciliter ce processus, vous devez créer un `RedirectUser(sendUserToPageIndex)` méthode qui redirige l’utilisateur à `Paging.aspx?pageIndex=sendUserToPageIndex`. Ensuite, appelez cette méthode à partir du bouton quatre `Click` gestionnaires d’événements. Dans le `FirstPage` `Click` Gestionnaire d’événements, appelez `RedirectUser(0)`, pour les envoyer à la première page, dans le `PrevPage` `Click` Gestionnaire d’événements, utilisez `PageIndex - 1` en tant que l’index de page ; et ainsi de suite.


[!code-csharp[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample8.cs)]

Avec la `Click` terminer des gestionnaires d’événements, les enregistrements de DataList s peuvent être contactés via en cliquant sur les boutons. Prenez un moment pour l’essayer !

## <a name="disabling-paging-interface-controls"></a>La désactivation de la pagination des contrôles d’Interface

Actuellement, les quatre boutons sont activés, quelle que soit la page affichée. Toutefois, vous souhaitez désactiver les boutons de la première et précédent lors de l’affichage de la première page de données et les boutons suivant et dernier lors de l’affichage de la dernière page. Le `PagedDataSource` objet retourné par le s ObjectDataSource `Select()` méthode possède des propriétés [ `IsFirstPage` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.pageddatasource.isfirstpage.aspx) et [ `IsLastPage` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.pageddatasource.islastpage.aspx) que nous pouvons examiner pour déterminer si nous visualisons la première ou dernière page de données.

Ajoutez le code suivant au s ObjectDataSource `Selected` Gestionnaire d’événements :


[!code-csharp[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample9.cs)]

Grâce à ce complément, les boutons premier et précédent seront désactivées lors de l’affichage de la première page, tandis que les boutons suivant et dernier seront désactivées lors de l’affichage de la dernière page.

S permettent de réaliser l’interface de pagination en informant l’utilisateur qu’ils page re actuellement affiché et le nombre total de pages existent. Ajoutez un contrôle Web Label à la page et définissez ses `ID` propriété `CurrentPageNumber`. Définir son `Text` propriété ObjectDataSource s sélectionnés Gestionnaire d’événements tels qu’il inclut la page actuelle affichée (`PageIndex + 1`) et le nombre total de pages (`PageCount`).


[!code-csharp[Main](paging-report-data-in-a-datalist-or-repeater-control-cs/samples/sample10.cs)]

La figure 10 illustre `Paging.aspx` lors de la première visite. Étant donné que la chaîne de requête est vide, la valeur par défaut est DataList montrant les quatre premiers produits ; les boutons premier et précédent sont désactivés. Cliquez sur Suivant pour afficher les quatre enregistrements (voir Figure 11) ; le premier et précédent sont maintenant activées.


[![La première Page de données s’affiche.](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image23.png)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image22.png)

**La figure 10**: la première Page de données s’affiche ([cliquez pour afficher l’image en taille réelle](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image24.png))


[![La deuxième Page de données s’affiche.](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image26.png)](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image25.png)

**Figure 11**: la deuxième Page de données s’affiche ([cliquez pour afficher l’image en taille réelle](paging-report-data-in-a-datalist-or-repeater-control-cs/_static/image27.png))


> [!NOTE]
> L’interface de la pagination peut être amélioré en permettant à l’utilisateur de spécifier le nombre de pages pour afficher par page. Par exemple, la liste des options de taille de page comme 5, 10, 25, 50 et tous les pu être ajoutée à une liste déroulante. Lors de la sélection d’une taille de page, l’utilisateur doit être redirigé vers `Paging.aspx?pageIndex=0&pageSize=selectedPageSize`. Je laisse la mise en œuvre de cette amélioration en guise d’exercice pour le lecteur.


## <a name="using-custom-paging"></a>À l’aide de la pagination personnalisée

Les pages de DataList via ses données à l’aide de la technique de la pagination par défaut inefficace. Lors de la pagination via suffisamment grandes quantités de données, il est impératif que la pagination personnalisée est utilisée. Bien que les détails d’implémentation diffèrent légèrement, les concepts sous-jacents à l’implémentation de la pagination personnalisée dans un contrôle DataList sont identiques à celles de la pagination par défaut. Avec la pagination personnalisée, utilisez la `ProductBLL` classe s `GetProductsPaged` (méthode) (au lieu de `GetProductsAsPagedDataSource`). Comme indiqué dans le [efficacement la pagination via grandes quantités de données](../paging-and-sorting/efficiently-paging-through-large-amounts-of-data-cs.md) didacticiel, `GetProductsPaged` doit être passé à start index et maximum numéro de ligne de lignes à retourner. Ces paramètres peuvent être gérés via la chaîne de requête, tout comme le `pageIndex` et `pageSize` les paramètres utilisés par défaut la pagination.

Depuis s’il n’y a aucun `PagedDataSource` avec la pagination personnalisée, autres techniques doivent être utilisés pour déterminer le nombre total d’enregistrements en cours de pagination via et si nous re d’affichage de la première ou dernière page de données. Le `TotalNumberOfProducts()` méthode dans la `ProductsBLL` classe retourne le nombre total de produits en cours par le biais de pagination. Pour déterminer si la première page de données est affichée, examinez l’index de ligne de début s’il est égal à zéro, puis la première page est affichée. Si l’index de ligne de début plus le nombre de lignes maximal à retourner est supérieur ou égal au nombre total d’enregistrements averti par le biais de la dernière page est affichée.

Nous allons explorer l’implémentation de la pagination personnalisée plus en détail dans le didacticiel suivant.

## <a name="summary"></a>Récapitulatif

Alors que le contrôle DataList ni un répéteur offre hors de la prise en charge la pagination trouvé dans le GridView, DetailsView et FormView contrôle, ces fonctionnalités peuvent être ajoutées avec un minimum d’effort. Le moyen le plus simple pour implémenter la pagination par défaut est d’encapsuler l’ensemble des produits au sein d’un `PagedDataSource` , puis lier la `PagedDataSource` à DataList ou répéteur. Dans ce didacticiel, nous avons ajouté la `GetProductsAsPagedDataSource` méthode à la `ProductsBLL` classe pour retourner le `PagedDataSource`. Le `ProductsBLL` classe contient déjà les méthodes nécessaires pour la pagination personnalisée `GetProductsPaged` et `TotalNumberOfProducts`.

En même temps que la récupération de l’ensemble précis d’enregistrements à afficher pour la pagination personnalisée ou tous les enregistrements dans un `PagedDataSource` pour la pagination par défaut, nous devons également ajouter manuellement l’interface de pagination. Pour ce didacticiel, nous avons créé un suivant, le précédent, tout d’abord, dernière interface avec quatre contrôles bouton Web. En outre, un contrôle d’étiquette indiquant le numéro de page actuel et le nombre total de pages a été ajouté.

Dans l’étape suivante du didacticiel, nous verrons comment ajouter prise en charge de tri pour le contrôle DataList et répéteur. Nous verrons également comment créer un contrôle DataList qui peut être paginé et trié (avec exemples à l’aide de la valeur par défaut et la pagination personnalisée).

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont Liz Shulok, Ken Pespisa et Bernadette Leigh. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Next](sorting-data-in-a-datalist-or-repeater-control-cs.md)
