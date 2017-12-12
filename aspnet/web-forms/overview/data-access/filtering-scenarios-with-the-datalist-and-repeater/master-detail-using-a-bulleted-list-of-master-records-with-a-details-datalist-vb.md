---
uid: web-forms/overview/data-access/filtering-scenarios-with-the-datalist-and-repeater/master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb
title: "Maître/détail à l’aide d’une liste à puces des enregistrements principaux avec détails DataList (VB) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel nous allons compresser le rapport de deux pages maître/détail du didacticiel précédent en une seule page affichant une liste à puces des noms de catégories sur t..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/17/2006
ms.topic: article
ms.assetid: ee20742f-6fb7-49a0-a009-058fe363aacb
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/filtering-scenarios-with-the-datalist-and-repeater/master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb
msc.type: authoredcontent
ms.openlocfilehash: 99d04c95b42402ae2bc72562a652b6edec5e9313
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="masterdetail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb"></a>Maître/détail à l’aide d’une liste à puces des enregistrements principaux avec détails DataList (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_35_VB.exe) ou [télécharger le PDF](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/datatutorial35vb1.pdf)

> Dans ce didacticiel nous allons compresser le rapport de deux pages maître/détail du didacticiel précédent en une seule page affichant une liste à puces des noms de catégories sur le côté gauche de l’écran et les produits de la catégorie sélectionnée sur la droite de l’écran.


## <a name="introduction"></a>Introduction

Dans le [didacticiel précédent](master-detail-filtering-acess-two-pages-datalist-vb.md) nous avons examiné comment diviser un rapport maître/détail sur deux pages. Dans la page maître, nous avons utilisé un contrôle du répéteur pour afficher une liste à puces de catégories. Chaque nom de catégorie a été un lien hypertexte que, lorsque vous cliquez dessus, serait prennent l’utilisateur à la page de détails, où un contrôle DataList deux colonnes ont montré les produits appartenant à la catégorie sélectionnée.

Dans ce didacticiel nous allons compresser le didacticiel de deux pages en une seule page, affichant une liste à puces des noms de catégories sur le côté gauche de l’écran avec chaque nom de catégorie restitué sous la forme d’un bouton de lien. Cliquant sur le nom de catégorie LinkButton induit une publication (postback) et lie les produits de s catégorie sélectionnée pour un contrôle DataList de deux colonnes sur la droite de l’écran. En plus d’afficher chaque nom de catégorie s, répéteur sur la gauche indique nombre total de produits pour une catégorie donnée (voir Figure 1).


[![La catégorie s nom et le nombre Total de produits sont affichent sur la gauche](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image2.png)](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image1.png)

**Figure 1**: la catégorie s nom et le nombre Total de produits sont affichent sur la gauche ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image3.png))


## <a name="step-1-displaying-a-repeater-in-the-left-portion-of-the-screen"></a>Étape 1 : Afficher un répéteur dans la partie gauche de l’écran

Pour ce didacticiel, nous devons afficher la liste à puces des catégories à gauche des produits s catégorie sélectionnée. Contenu d’une page web peut être positionné à l’aide de balises HTML standard éléments paragraphe, des espaces insécables, `<table>` s et ainsi de suite ou via les techniques de feuille de style (CSS) en cascade. Jusqu'à présent, tous les nos didacticiels ont utilisé des techniques CSS de positionnement. Lorsque nous avons créé l’interface utilisateur de navigation dans notre page maître dans le [les Pages maîtres et la Navigation sur le Site](../introduction/master-pages-and-site-navigation-vb.md) didacticiel, nous avons utilisé *positionnement absolu*, indiquant le décalage de pixel précis pour la navigation liste et le contenu principal. CSS peuvent également être utilisées pour positionner un élément à droite ou gauche d’un autre via *flottante*. Nous pouvons afficher la liste à puces des catégories à gauche des produits s catégorie sélectionnée en flottante répéteur à gauche du contrôle DataList

Ouvrez le `CategoriesAndProducts.aspx` page à partir de la `DataListRepeaterFiltering` dossier et l’ajouter à la page un répéteur et un contrôle DataList. Définir le répéteur s `ID` à `Categories` et du contrôle DataList s à `CategoryProducts`. Accédez à la vue de Source et placer les contrôles de répéteur et DataList dans leurs propres `<div>` éléments. Autrement dit, placez l’opération répétition dans un `<div>` élément premier et puis du contrôle DataList dans son propre `<div>` élément directement après le répéteur. Votre balisage à ce stade doit ressembler à ce qui suit :


[!code-aspx[Main](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/samples/sample1.aspx)]

Pour détacher le répéteur à gauche du contrôle DataList, nous devons utiliser la `float` attribut de style CSS, comme suit :


[!code-html[Main](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/samples/sample2.html)]

Le `float: left;` flotte la première `<div>` élément à gauche de la seconde. Le `width` et `padding-right` paramètres indiquent la première `<div>` s `width` et la marge intérieure est ajoutée entre le `<div>` contenu de l’élément s et sa marge de droite. Pour plus d’informations sur flottant des éléments dans CSS extraire le [Floatutorial](http://css.maxdesign.com.au/floatutorial/).

Plutôt que de spécifier le paramètre de style directement par le biais de la première `<p>` élément s `style` d’attribut, permettent de s à la place créer une classe CSS dans `Styles.css` nommé `FloatLeft`:


[!code-css[Main](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/samples/sample3.css)]

Ensuite, nous pouvons remplacer le `<div>` avec `<div class="FloatLeft">`.

Après l’ajout de la classe CSS et la configuration de la balise dans le `CategoriesAndProducts.aspx` page, accédez au concepteur. Vous devez voir le répéteur à virgule flottante à gauche du contrôle DataList (bien que le droit à la fois juste apparaissent maintenant comme cases grises dans la mesure où ve encore à configurer leurs sources de données ou de modèles).


[![La répétition est affichée à gauche du contrôle DataList](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image5.png)](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image4.png)

**Figure 2**: le répéteur est affichée à gauche du contrôle DataList ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image6.png))


## <a name="step-2-determining-the-number-of-products-for-each-category"></a>Étape 2 : Déterminer le nombre de produits pour chaque catégorie

S répéteur et DataList entourant le balisage terminée, nous re prêt à lier les données de catégorie à la répétition de contrôler. Toutefois, comme le montre la liste à puces des catégories dans la Figure 1, en plus de chaque nom catégorie nous devons également afficher le nombre de produits de la catégorie. Pour accéder à ces informations, nous pouvons :

- **Déterminer ces informations à partir de la classe code-behind de s de page ASP.NET.** Étant donné un particulier  *`categoryID`*  nous pouvons déterminer le nombre de produits associés en appelant le `ProductsBLL` classe s `GetProductsByCategoryID(categoryID)` (méthode). Cette méthode retourne un `ProductsDataTable` dont l’objet `Count` propriété indique combien `ProductsRow` s existe, qui est le nombre de produits spécifié  *`categoryID`* . Nous pouvons créer un `ItemDataBound` Gestionnaire d’événements pour le répéteur qui, pour chaque catégorie liée à l’opération de répétition, appelle le `ProductsBLL` classe s `GetProductsByCategoryID(categoryID)` (méthode) et inclut son nombre dans la sortie.
- **Mise à jour la `CategoriesDataTable` dans le DataSet typé à inclure un `NumberOfProducts` colonne.** Nous pouvons ensuite mettre à jour le `GetCategories()` méthode dans le `CategoriesDataTable` d’inclure ces informations, vous pouvez également `GetCategories()` comme-est et créer un nouveau `CategoriesDataTable` méthode appelée `GetCategoriesAndNumberOfProducts()`.

Permettent d’Explorer ces deux techniques s. La première approche est plus simple à implémenter, car nous n’avez pas besoin de mettre à jour de la couche d’accès aux données ; Toutefois, elle nécessite plus de communications avec la base de données. L’appel à la `ProductsBLL` classe s `GetProductsByCategoryID(categoryID)` méthode dans le `ItemDataBound` Gestionnaire d’événements ajoute un appel de la base de données supplémentaire pour chaque catégorie affichée dans le répéteur. Avec cette technique des *N* + appels de base de 1 données, où *N* est le nombre de catégories affichées dans le répéteur. Avec la deuxième approche, le nombre de produits est retourné avec des informations sur chaque catégorie de la `CategoriesBLL` classe s `GetCategories()` (ou `GetCategoriesAndNumberOfProducts()`) méthode, entraînant voyage qu’une seule à la base de données.

## <a name="determining-the-number-of-products-in-the-itemdatabound-event-handler"></a>Déterminer le nombre de produits dans le Gestionnaire d’événement ItemDataBound

Déterminer le nombre de produits pour chaque catégorie dans le répéteur s `ItemDataBound` Gestionnaire d’événements ne nécessite pas les modifications apportées à la couche d’accès aux données existantes. Toutes les modifications peuvent être apportées directement dans le `CategoriesAndProducts.aspx` page. Commencez par ajouter un nouveau ObjectDataSource nommé `CategoriesDataSource` via la balise active de répéteur s. Ensuite, configurez le `CategoriesDataSource` ObjectDataSource afin qu’il récupère ses données à partir de la `CategoriesBLL` classe s `GetCategories()` (méthode).


[![Configurer l’ObjectDataSource pour utiliser la classe CategoriesBLL s GetCategories() (méthode)](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image8.png)](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image7.png)

**Figure 3**: configurer l’ObjectDataSource à utiliser le `CategoriesBLL` classe s `GetCategories()` (méthode) ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image9.png))


Chaque élément dans le `Categories` répéteur doit être interactif et, lorsque vous cliquez dessus, provoquer le `CategoryProducts` DataList pour afficher les produits pour la catégorie sélectionnée. Cela peut être accompli en rendant chaque catégorie d’un lien hypertexte, liaison, revenez à cette page même (`CategoriesAndProducts.aspx`), mais en passant le `CategoryID` via la chaîne de requête, comme nous l’avons vu dans le didacticiel précédent. L’avantage de cette approche est qu’une page affiche une catégorie particulière s de produits peut être contenant un signet et indexée par un moteur de recherche.

Aussi, nous pouvons effectuer chaque catégorie un LinkButton, qui est l’approche que nous allons utiliser pour ce didacticiel. Le LinkButton est rendu dans le navigateur de l’utilisateur s’en tant qu’un lien hypertexte, mais, lorsque vous cliquez dessus, induit une publication (postback) ; lors de la publication, le s DataList ObjectDataSource doit être actualisé pour afficher les produits appartenant à la catégorie sélectionnée. Pour ce didacticiel, à l’aide d’un lien hypertexte plus judicieux que l’utilisation d’un LinkButton ; Toutefois, il peut être autres scénarios où il est plus avantageux d’à l’aide d’un bouton de lien. Tandis que l’approche de lien hypertexte est idéale pour cet exemple, vous permettent de s à la place Explorer à l’aide du bouton de lien. Comme nous allons le voir, à l’aide d’un LinkButton présente certains défis ne seraient pas sinon survenir avec un lien hypertexte. Par conséquent, à l’aide d’un LinkButton dans ce didacticiel est mettre en évidence ces défis et contribuer à fournir des solutions pour ces scénarios, nous pouvons pouvez utiliser un LinkButton au lieu d’un lien hypertexte.

> [!NOTE]
> Il est conseillé de répéter ce didacticiel à l’aide d’un contrôle de lien hypertexte ou `<a>` élément à la place le LinkButton.


Le balisage suivant illustre la syntaxe déclarative pour le répéteur et ObjectDataSource. Notez que les modèles de s répéteur restituer une liste à puces avec chaque élément comme un bouton de lien :


[!code-aspx[Main](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/samples/sample4.aspx)]

> [!NOTE]
> Pour ce didacticiel répéteur doit avoir son état d’affichage (l’omission de la `EnableViewState="False"` à partir de la syntaxe déclarative répéteur s). À l’étape 3 nous allons créer un gestionnaire d’événements pour le répéteur s `ItemCommand` événements dans lequel nous mettrons à jour du contrôle DataList s ObjectDataSource s `SelectParameters` collection. Répéteur s `ItemCommand`, toutefois, a gagné incendie de t si l’état d’affichage est désactivé. Consultez [question difficile de A une question ASP.NET](http://scottonwriting.net/sowblog/posts/1263.aspx) et [sa solution](http://scottonwriting.net/sowBlog/posts/1268.aspx) pour plus d’informations sur la raison pour laquelle l’état d’affichage doit être activé pour un répéteur s `ItemCommand` l’événement.


Le LinkButton le `ID` valeur de propriété `ViewCategory` n’a pas son `Text` jeu de propriétés. Si nous avions voulions afficher le nom de catégorie, nous aurait défini la propriété de texte de façon déclarative, via la syntaxe de liaison de données, comme suit :


[!code-aspx[Main](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/samples/sample5.aspx)]

Toutefois, nous souhaitons afficher à la fois le nom de catégorie s *et* le nombre de produits appartenant à cette catégorie. Ces informations peuvent être extraites de répéteur s `ItemDataBound` Gestionnaire d’événements en effectuant un appel à la `ProductBLL` classe s `GetCategoriesByProductID(categoryID)` (méthode) et déterminer le nombre d’enregistrements qui est retourné dans la boîte `ProductsDataTable`, comme le code suivant décrit :


[!code-vb[Main](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/samples/sample6.vb)]

Nous commençons en s’assurant que nous re fonctionne avec un élément de données (un dont `ItemType` est `Item` ou `AlternatingItem`), puis référencez la `CategoriesRow` instance a simplement été liée à actuel `RepeaterItem`. Ensuite, nous déterminons le nombre de produits pour cette catégorie en créant une instance de la `ProductsBLL` l’appel de la classe son `GetCategoriesByProductID(categoryID)` (méthode) et déterminer le nombre d’enregistrements renvoyés à l’aide de la `Count` propriété. Enfin, le `ViewCategory` LinkButton dans ItemTemplate est references et son `Text` est définie sur *CategoryName* (*NumberOfProductsInCategory*), où  *NumberOfProductsInCategory* est mise en forme comme un nombre avec une décimale.

> [!NOTE]
> Ou bien, nous pouvons ajouter un *mise en forme de la fonction* à la classe code-behind de pages ASP.NET qui accepte une catégorie s `CategoryName` et `CategoryID` les valeurs et retourne le `CategoryName` concaténé avec le nombre de produits de la catégorie (comme déterminé en appelant le `GetCategoriesByProductID(categoryID)` méthode). Les résultats d’une telle fonction mise en forme peut être attribués de façon déclarative avec le s LinkButton Text (propriété) en remplaçant la nécessité pour le `ItemDataBound` Gestionnaire d’événements. Reportez-vous à la [à l’aide de TemplateField dans le contrôle GridView](../custom-formatting/using-templatefields-in-the-gridview-control-vb.md) ou [mise en forme le contrôle DataList et données répéteur en fonction de](../displaying-data-with-the-datalist-and-repeater/formatting-the-datalist-and-repeater-based-upon-data-vb.md) didacticiels pour plus d’informations sur l’utilisation de fonctions de mise en forme.


Après avoir ajouté ce gestionnaire d’événements, prenez un moment pour tester la page via un navigateur. Notez la façon dont chaque catégorie est répertorié dans une liste à puces, affichant le nom de catégorie s et le nombre de produits de la catégorie (voir Figure 4).


[![Chaque nom de catégorie et le nombre de produits sont affichés.](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image11.png)](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image10.png)

**Figure 4**: catégorie s nom et le nombre de produits sont affichés ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image12.png))


## <a name="updating-thecategoriesdatatableandcategoriestableadapterto-include-the-number-of-products-for-each-category"></a>Mise à jour la`CategoriesDataTable`et`CategoriesTableAdapter`pour inclure le nombre de produits pour chaque catégorie

Au lieu de déterminer le nombre de produits pour chaque catégorie, telle qu’elle s liée à l’opération de répétition, nous pouvons rationaliser ce processus en ajustant la `CategoriesDataTable` et `CategoriesTableAdapter` dans la couche d’accès aux données à inclure ces informations en mode natif. Pour ce faire, nous devons ajouter une nouvelle colonne à `CategoriesDataTable` pour contenir le nombre de produits associés. Pour ajouter une nouvelle colonne à un DataTable, ouvrez le DataSet typé (`App_Code\DAL\Northwind.xsd`), avec le bouton droit sur la table de données à modifier et cliquez sur Ajouter / colonne. Ajouter une nouvelle colonne à la `CategoriesDataTable` (voir Figure 5).


[![Ajouter une nouvelle colonne à la CategoriesDataSource](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image14.png)](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image13.png)

**Figure 5**: ajouter une nouvelle colonne à la `CategoriesDataSource` ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image15.png))


Cela ajoutera une nouvelle colonne nommée `Column1`, que vous pouvez modifier en tapant simplement un autre nom. Renommer cette colonne `NumberOfProducts`. Ensuite, nous avons besoin configurer les propriétés de cette colonne s. Cliquez sur la nouvelle colonne et accédez à la fenêtre Propriétés. Modifier la colonne s `DataType` propriété à partir de `System.String` à `System.Int32` et définir le `ReadOnly` propriété `True`, comme illustré dans la Figure 6.


![Définir le type de données et les propriétés en lecture seule de la nouvelle colonne](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image16.png)

**Figure 6**: définir le `DataType` et `ReadOnly` propriétés de la nouvelle colonne


Alors que le `CategoriesDataTable` a maintenant un `NumberOfProducts` colonne, sa valeur n’est pas définie par une requête s TableAdapter correspondant. Nous pouvons mettre à jour le `GetCategories()` retourné de méthode pour retourner ces informations si nous voulons que ces informations chaque fois que les informations de catégorie sont récupérées. Si, toutefois, nous avons besoin uniquement de saisir le nombre de produits associés pour les catégories dans de rares cas (par exemple, tout simplement pour ce didacticiel), nous pouvons laisser `GetCategories()` comme-est et créer une nouvelle méthode qui retourne cette information. S permettent d’utiliser cette dernière approche, création d’une nouvelle méthode nommée `GetCategoriesAndNumberOfProducts()`.

Pour ajouter de nouveaux `GetCategoriesAndNumberOfProducts()` (méthode), avec le bouton droit sur le `CategoriesTableAdapter` et cliquez sur Nouvelle requête. Cette opération restaure le TableAdapter interroger Assistant de Configuration, ce qui nous ve utilisé plusieurs fois dans les didacticiels précédents à distance. Pour cette méthode, démarrez l’Assistant en indiquant que la requête utilise une instruction SQL ad hoc qui retourne des lignes.


[![Créez la méthode à l’aide d’une instruction SQL de Ad Hoc](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image18.png)](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image17.png)

**Figure 7**: créer une méthode à l’aide une instruction SQL de Ad-Hoc ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image19.png))


[![L’instruction SQL retourne des lignes](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image21.png)](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image20.png)

**Figure 8**: les lignes de retourne instruction SQL ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image22.png))


L’écran suivant de l’Assistant vous invite à entrer d’us pour la requête à utiliser. Pour retourner chaque catégorie s `CategoryID`, `CategoryName`, et `Description` champs, ainsi que le nombre de produits de la catégorie, utilisent ce qui suit `SELECT` instruction :


[!code-sql[Main](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/samples/sample7.sql)]


[![Spécifiez la requête à utiliser](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image24.png)](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image23.png)

**Figure 9**: spécifiez la requête à utiliser ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image25.png))


Notez que la sous-requête qui calcule le nombre de produits de la catégorie est un alias comme `NumberOfProducts`. Cette correspondance d’affectation de noms provoque la valeur retournée par cette sous-requête à associer à la `CategoriesDataTable` s `NumberOfProducts` colonne.

Après avoir entré cette requête, la dernière étape consiste à choisir le nom de la nouvelle méthode. Utilisez `FillWithNumberOfProducts` et `GetCategoriesAndNumberOfProducts` pour le remplissage un DataTable et retourner un DataTable des modèles, respectivement.


[![Nom de la nouvelle méthodes FillWithNumberOfProducts TableAdapter s et GetCategoriesAndNumberOfProducts](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image27.png)](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image26.png)

**La figure 10**: nommez le nouveau TableAdapter s méthodes `FillWithNumberOfProducts` et `GetCategoriesAndNumberOfProducts` ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image28.png))


À ce stade, la couche d’accès aux données a été étendue pour inclure le nombre de produits par catégorie. Étant donné que notre couche achemine tous les appels à la couche DAL via une couche de logique métier séparé nous devons ajouter correspondante `GetCategoriesAndNumberOfProducts` méthode à la `CategoriesBLL` classe :


[!code-vb[Main](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/samples/sample8.vb)]

Avec la couche DAL et de la couche BLL terminée, nous re prêt à lier ces données à la `Categories` répéteur dans `CategoriesAndProducts.aspx`! Si vous avez déjà créé un ObjectDataSource pour la répétition de la détermination du nombre de produits dans le `ItemDataBound` section du Gestionnaire d’événements, supprimer cette ObjectDataSource et répéteur s `DataSourceID` propriété configuration ; ses également le Répéteur s `ItemDataBound` événement du Gestionnaire d’événements en supprimant le `Handles Categories.OnItemDataBound` syntaxe dans la classe code-behind ASP.NET.

Répéteur dans son état d’origine, d’ajouter un nouveau ObjectDataSource nommé `CategoriesDataSource` via la balise active de répéteur s. Configurer ObjectDataSource à utiliser le `CategoriesBLL` (classe), mais il lieu d’utiliser le `GetCategories()` (méthode), ont il utiliser `GetCategoriesAndNumberOfProducts()` à la place (voir Figure 11).


[![Configurer pour utiliser la méthode GetCategoriesAndNumberOfProducts ObjectDataSource](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image30.png)](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image29.png)

**Figure 11**: configurer l’ObjectDataSource à utiliser le `GetCategoriesAndNumberOfProducts` (méthode) ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image31.png))


Ensuite, mettez à jour le `ItemTemplate` afin que le s LinkButton `Text` propriété reçoit façon déclarative à l’aide de la syntaxe de liaison de données et comprend à la fois le `CategoryName` et `NumberOfProducts` des champs de données. Le balisage déclaratif complet pour le répéteur et `CategoriesDataSource` ObjectDataSource suivantes :


[!code-aspx[Main](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/samples/sample9.aspx)]

La sortie rendue en mettant à jour de la couche DAL pour inclure un `NumberOfProducts` colonne est identique à l’aide de la `ItemDataBound` approche de gestionnaire d’événements (font référence à la Figure 4 pour voir un écran de capture du répéteur montrant les noms de catégorie et le nombre de produits).

## <a name="step-3-displaying-the-selected-category-s-products"></a>Étape 3 : Afficher les produits de s catégorie sélectionnée

À ce stade, nous avons le `Categories` répéteur affichant la liste des catégories, ainsi que le nombre de produits dans chaque catégorie. Répéteur utilise un LinkButton pour chaque catégorie que, lorsque vous cliquez dessus, provoque une publication (postback) à laquelle les points de nous nécessaires pour afficher les produits pour la catégorie sélectionnée dans le `CategoryProducts` DataList.

Un seul nos consiste à disposer du contrôle DataList à afficher uniquement les produits de la catégorie sélectionnée. Dans le [maître/détail à l’aide d’un GridView maître avec un contrôle DetailsView détails](../masterdetail/master-detail-using-a-selectable-master-gridview-with-a-details-detailview-vb.md) didacticiel, nous avons vu comment créer un GridView dont les lignes peut être sélectionné, avec la ligne sélectionnée s Détails s’affiche dans un contrôle DetailsView sur la même page. Le GridView ObjectDataSource renvoyées plus d’informations sur tous les produits à l’aide de la `ProductsBLL` s `GetProducts()` méthode lors de la s DetailsView ObjectDataSource récupérées plus d’informations sur le produit sélectionné à l’aide de la `GetProductsByProductID(productID)` (méthode). Le  *`productID`*  la valeur du paramètre a été fournie de manière déclarative en l’associant à la valeur de mappage GridView `SelectedValue` propriété. Malheureusement, la répétition n’a pas un `SelectedValue` propriété et ne peut pas servir à une source de paramètre.

> [!NOTE]
> Il s’agit d’une des ces défis qui s’affichent lorsque vous utilisez le bouton de lien dans un répéteur. Nous avions utilisé un lien hypertexte pour transmettre le `CategoryID` via la chaîne de requête à la place, nous pourrions utiliser ce champ de chaîne de requête comme source pour la valeur du paramètre s.


Avant de nous soucier de l’absence d’un `SelectedValue` propriété répéteur, cependant, permettre de s tout d’abord lier le contrôle DataList à un ObjectDataSource spécifier et ses `ItemTemplate`.

À partir de la balise active du contrôle DataList s, choisir d’ajouter un nouveau ObjectDataSource nommé `CategoryProductsDataSource` et configurez-le pour utiliser le `ProductsBLL` classe s `GetProductsByCategoryID(categoryID)` (méthode). Étant donné que le contrôle DataList dans ce didacticiel propose une interface en lecture seule, n’hésitez pas à définir les listes déroulantes dans l’instruction INSERT, UPDATE et supprimer des onglets à (None).


[![Configurer pour utiliser la méthode de classe de ProductsBLL s GetProductsByCategoryID(categoryID) ObjectDataSource](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image33.png)](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image32.png)

**Figure 12**: configurer l’ObjectDataSource à utiliser `ProductsBLL` classe s `GetProductsByCategoryID(categoryID)` (méthode) ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image34.png))


Étant donné que la `GetProductsByCategoryID(categoryID)` méthode attend un paramètre d’entrée (*`categoryID`*), l’Assistant Configurer la Source de données permet de spécifier la source du paramètre s. Étaient les catégories répertoriées dans un GridView ou d’un contrôle DataList, nous d définition de la liste de déroulante paramètre source pour le contrôle et le ControlID à la `ID` des données de contrôle Web. Toutefois, puisque le manque de répéteur un `SelectedValue` il ne peut pas être utilisé comme source de paramètre de propriété. Si vous enregistrez, vous trouverez que la liste déroulante ControlID contienne uniquement un contrôle `ID``CategoryProducts`, le `ID` de DataList.

Pour l’instant, définition de la liste de liste déroulante de source de paramètre None. Nous allons finir d’assignation par programme cette valeur de paramètre lorsqu’une catégorie d’un clic sur LinkButton dans le répéteur.


[![Effectuez pas de spécifier une Source de paramètre pour la paramètre categoryID](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image36.png)](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image35.png)

**Figure 13**: ne spécifiez pas d’une Source de paramètre pour le  *`categoryID`*  paramètre ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image37.png))


Après avoir terminé l’Assistant Configurer la Source de données, Visual Studio génère automatiquement du contrôle DataList s `ItemTemplate`. Remplacez cette valeur par défaut `ItemTemplate` avec le modèle nous utilisé dans le didacticiel précédent ; en outre, définissez du contrôle DataList s `RepeatColumns` propriété à 2. Après avoir apporté ces modifications le balisage déclaratif pour votre DataList et son ObjectDataSource associé doit se présenter comme suit :


[!code-aspx[Main](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/samples/sample10.aspx)]

Actuellement, le `CategoryProductsDataSource` ObjectDataSource s  *`categoryID`*  paramètre n’est jamais défini, aucun produit ne s’affichent lors de l’affichage de la page. Nous devons est ont cette valeur de paramètre en fonction de la `CategoryID` de la catégorie sélectionnée dans le répéteur. Cet article présente deux défis : tout d’abord, comment nous déterminer quand un LinkButton dans le répéteur s `ItemTemplate` a été cliquée ; et le deuxième comment pouvons nous déterminons le `CategoryID` de la catégorie correspondante, l’utilisateur a cliqué sur dont LinkButton ?

Le LinkButton comme les contrôles Button et ImageButton a un `Click` événement et un [ `Command` événement](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.linkbutton.command.aspx). Le `Click` événement est conçu pour simplement Notez que le bouton de lien a été cliqué. Dans certains cas, toutefois, en plus de noter que le bouton de lien a été cliqué nous devons également passer des informations supplémentaires au gestionnaire d’événements. Si c’est le cas, le LinkButton s [ `CommandName` ](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.linkbutton.commandname.aspx) et [ `CommandArgument` ](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.linkbutton.commandargument.aspx) propriétés peuvent être attribuées à ces informations supplémentaires. Ensuite, lorsque l’utilisateur clique sur le bouton de lien, son `Command` se déclenche des événements (au lieu de son `Click` événement) et le Gestionnaire d’événements reçoit les valeurs de la `CommandName` et `CommandArgument` propriétés.

Lorsqu’un `Command` événement est déclenché à partir d’un modèle dans le répéteur, s répéteur [ `ItemCommand` événement](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.repeater.itemcommand.aspx) est activé et est passé le `CommandName` et `CommandArgument` les valeurs du contrôle LinkButton un clic sur (ou bouton ou ImageButton). Par conséquent, pour déterminer quand une LinkButton dans la répétition de la catégorie a été activée, nous devons effectuer les opérations suivantes :

1. Définir le `CommandName` propriété du contrôle LinkButton dans le répéteur s `ItemTemplate` à une valeur (Je ve utilisé ListProducts). En définissant ce paramètre `CommandName` valeur, le s LinkButton `Command` événement est déclenché lorsque l’utilisateur clique sur le bouton de lien.
2. Définir le LinkButton s `CommandArgument` valeur à la propriété de l’élément actuel s `CategoryID`.
3. Créer un gestionnaire d’événements pour le répéteur s `ItemCommand` événement. Dans le gestionnaire, jeu d’événements le `CategoryProductsDataSource` ObjectDataSource s `CategoryID` paramètre à la valeur du passé en `CommandArgument`.

Les éléments suivants `ItemTemplate` balisage de répéteur catégories implémente les étapes 1 et 2. Notez comment la `CommandArgument` est assignée à l’élément de données s `CategoryID` à l’aide de la syntaxe de liaison de données :


[!code-aspx[Main](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/samples/sample11.aspx)]

Chaque fois que la création d’un `ItemCommand` Gestionnaire d’événements qu’il est préférable de toujours vérifier d’abord entrant `CommandName` , car la valeur *tout* `Command` événement déclenché par *tout* Button, LinkButton, ou ImageButton dans le répéteur entraîne le `ItemCommand` l’événement. Alors que nous avons actuellement uniquement avoir une telle LinkButton maintenant, à l’avenir nous (ou un autre développeur de notre équipe) peux ajouter contrôles Web supplémentaires à répéteur qui, lorsque vous cliquez dessus, déclenche le même `ItemCommand` Gestionnaire d’événements. Par conséquent, il s préférable de toujours Vérifiez le `CommandName` propriété et poursuivre votre logique de programmation uniquement si elle correspond à la valeur attendue.

Après avoir vérifié que le passé dans `CommandName` ListProducts a la valeur, le Gestionnaire d’événements puis assigne le `CategoryProductsDataSource` ObjectDataSource s `CategoryID` paramètre à la valeur du passé en `CommandArgument`. Cette modification dans le s ObjectDataSource `SelectParameters` entraîne automatiquement la du contrôle DataList lui-même lier de nouveau à la source de données, affichant les produits de la catégorie qui vient d’être sélectionnée.


[!code-vb[Main](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/samples/sample12.vb)]

Avec ces ajouts, notre didacticiel est terminé ! Prenez un moment pour le tester dans un navigateur. La figure 14 illustre l’écran lors de la première visite la page. Dans la mesure où une catégorie doit encore être sélectionné, aucun produit n’est affichés. En cliquant sur une catégorie, tels que produit, affiche les produits dans la catégorie de produits dans une vue de deux colonnes (voir Figure 15).


[![Aucun produit n’est affichés lors de la première visite de la Page](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image39.png)](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image38.png)

**La figure 14**: No produits sont affichées lors de la première visite de la Page ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image40.png))


[![En cliquant sur les listes de catégories de produit les produits correspondants à droite](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image42.png)](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image41.png)

**Figure 15**: en cliquant sur la catégorie de produits répertorie les produits correspondant à la droite ([cliquez pour afficher l’image en taille réelle](master-detail-using-a-bulleted-list-of-master-records-with-a-details-datalist-vb/_static/image43.png))


## <a name="summary"></a>Résumé

Comme nous l’avons vu dans ce didacticiel et précédente, les rapports maître/détail peuvent être répartis dans deux pages ou consolidées sur un. Affichage d’un rapport maître/détail sur une seule page, toutefois, présente des défis sur la façon de mieux le maître de disposition et les enregistrements de détails sur la page. Dans le *maître/détail à l’aide d’un GridView maître avec un contrôle DetailsView détails* didacticiel, nous avons dû les enregistrements de détails sont affichés ci-dessus les enregistrements maîtres ; dans ce didacticiel, nous avons utilisé les techniques CSS d’avoir la valeur flottante principaux pour le gauche des détails.

Ainsi que d’afficher les rapports maître/détails, nous avons dû également la possibilité d’examiner comment extraire le nombre de produits associés à chaque catégorie, ainsi que la manière d’effectuer une logique côté serveur lorsqu’un LinkButton (ou bouton ou ImageButton) vous cliquez sur à partir d’un Répéteur.

Ce didacticiel termine notre examen des rapports maître/détail avec le contrôle DataList et répéteur. Notre jeu de didacticiels suivant illustre comment ajouter une modification et suppression de fonctionnalités pour le contrôle DataList.

Bonne programmation !

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Floatutorial](http://css.maxdesign.com.au/floatutorial/) un didacticiel sur flottante éléments CSS avec CSS
- [Positionnement CSS](http://www.brainjar.com/css/positioning/) plus d’informations sur le positionnement des éléments de CSS
- [Disposition des contenus avec du code HTML](http://www.w3schools.com/html/html_layout.asp) à l’aide de `<table>` s et autres éléments HTML de positionnement

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Zack Jones. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](master-detail-filtering-acess-two-pages-datalist-vb.md)
