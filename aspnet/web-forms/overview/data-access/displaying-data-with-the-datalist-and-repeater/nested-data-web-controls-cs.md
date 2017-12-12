---
uid: web-forms/overview/data-access/displaying-data-with-the-datalist-and-repeater/nested-data-web-controls-cs
title: "Contrôles (c#) de Web de données imbriquées | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, que nous allons examiner comment utiliser un répéteur imbriqués à l’intérieur d’un autre répéteur. Les exemples illustre comment remplir le répéteur interne à la fois d..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/13/2006
ms.topic: article
ms.assetid: ad3cb0ec-26cf-42d7-b81b-184a34ec9f86
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/displaying-data-with-the-datalist-and-repeater/nested-data-web-controls-cs
msc.type: authoredcontent
ms.openlocfilehash: 69fa0489ff8baed1423d29ee7bfaa3157d35a76b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="nested-data-web-controls-c"></a>Contrôles Web de données imbriquées (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_32_CS.exe) ou [télécharger le PDF](nested-data-web-controls-cs/_static/datatutorial32cs1.pdf)

> Dans ce didacticiel, que nous allons examiner comment utiliser un répéteur imbriqués à l’intérieur d’un autre répéteur. Les exemples illustre comment remplir le répéteur interne de façon déclarative et par programme.


## <a name="introduction"></a>Introduction

En plus des fichiers HTML statiques et la syntaxe de liaison de données, les modèles peuvent également inclure les contrôles Web et les contrôles utilisateur. Ces contrôles Web peuvent avoir leurs propriétés affectées via la syntaxe de liaison de données déclarative, ou sont accessibles par programmation dans les gestionnaires d’événements côté serveur approprié.

En incorporant des contrôles dans un modèle, l’apparence et une expérience utilisateur peut être personnalisée et améliorée. Par exemple, dans le [à l’aide de TemplateField dans le contrôle GridView](../custom-formatting/using-templatefields-in-the-gridview-control-cs.md) didacticiel, nous avons vu comment personnaliser l’affichage de s GridView en ajoutant un contrôle calendrier dans TemplateField pour afficher un employé à la date d’embauche s ; les [Ajout Contrôles de validation à la modification et l’insertion des Interfaces](../editing-inserting-and-deleting-data/adding-validation-controls-to-the-editing-and-inserting-interfaces-cs.md) et [personnalisation de l’Interface de Modification de données](../editing-inserting-and-deleting-data/customizing-the-data-modification-interface-cs.md) didacticiels, nous avons vu comment personnaliser la modification et les interfaces d’insertion en ajoutant la validation contrôles, zones de texte, compréhension des listes et autres contrôles Web.

Modèles peuvent également contenir d’autres contrôles Web de données. Autrement dit, nous pouvons avoir un contrôle DataList qui contient un autre contrôle DataList (ou répéteur ou GridView ou DetailsView et ainsi de suite) dans ses modèles. Le défi lié à une telle interface est lié les données appropriées pour les contrôle Web de données internes. Il existe quelques approches différentes, allant des options déclaratives à l’aide de ObjectDataSource pour les campagnes par programme.

Dans ce didacticiel, que nous allons examiner comment utiliser un répéteur imbriqués à l’intérieur d’un autre répéteur. Répéteur externe contient un élément pour chaque catégorie dans la base de données, affichant le nom de catégorie et la description. Chaque élément de catégorie s répéteur interne affiche les informations concernant chaque produit appartenant à cette catégorie (voir Figure 1) dans une liste à puces. Nos exemples illustre comment remplir le répéteur interne de façon déclarative et par programme.


[![Chaque catégorie, ainsi que ses produits, sont répertoriés.](nested-data-web-controls-cs/_static/image2.png)](nested-data-web-controls-cs/_static/image1.png)

**Figure 1**: chaque catégorie, ainsi que ses produits, sont répertoriés ([cliquez pour afficher l’image en taille réelle](nested-data-web-controls-cs/_static/image3.png))


## <a name="step-1-creating-the-category-listing"></a>Étape 1 : Création de la liste des catégories

Lors de la création d’une page qui utilise des contrôles de Web de données imbriqués, I s’avérer utile pour concevoir, créer et tester le contrôle Web de données externe en premier lieu, sans se préoccuper même contrôle imbriqué interne. Par conséquent, permettent de s démarrer en parcourant les étapes nécessaires pour ajouter un répéteur à la page qui affiche le nom et la description de chaque catégorie.

Commencez par ouvrir le `NestedControls.aspx` page dans le `DataListRepeaterBasics` dossier et ajouter un contrôle de répéteur à la page, en définissant ses `ID` propriété `CategoryList`. À partir de la balise active de répéteur s, choisir de créer un nouveau ObjectDataSource nommé `CategoriesDataSource`.


[![Nom de la nouvelle ObjectDataSource CategoriesDataSource](nested-data-web-controls-cs/_static/image5.png)](nested-data-web-controls-cs/_static/image4.png)

**Figure 2**: nom de la nouvelle ObjectDataSource `CategoriesDataSource` ([cliquez pour afficher l’image en taille réelle](nested-data-web-controls-cs/_static/image6.png))


Configurer l’ObjectDataSource afin qu’il extrait ses données à partir de la `CategoriesBLL` classe s `GetCategories` (méthode).


[![Configurer pour utiliser la méthode GetCategories CategoriesBLL classe s ObjectDataSource](nested-data-web-controls-cs/_static/image8.png)](nested-data-web-controls-cs/_static/image7.png)

**Figure 3**: configurer l’ObjectDataSource à utiliser le `CategoriesBLL` classe s `GetCategories` (méthode) ([cliquez pour afficher l’image en taille réelle](nested-data-web-controls-cs/_static/image9.png))


Pour spécifier le modèle de s répéteur contenu nous devons accéder à la vue de Source et d’entrer manuellement la syntaxe déclarative. Ajouter un `ItemTemplate` qui affiche le nom de catégorie s dans une `<h4>` élément et la description de la catégorie s dans un élément de paragraphe (`<p>`). En outre, s permettent de séparer chaque catégorie avec une barre horizontale (`<hr>`). Après avoir apporté ces modifications, votre page doit contenir la syntaxe déclarative pour le répéteur et ObjectDataSource est semblable au suivant :


[!code-aspx[Main](nested-data-web-controls-cs/samples/sample1.aspx)]

La figure 4 illustre notre progression lors de l’affichage via un navigateur.


[![Chaque nom de catégorie et la Description sont répertorié, séparés par une barre horizontale](nested-data-web-controls-cs/_static/image11.png)](nested-data-web-controls-cs/_static/image10.png)

**Figure 4**: catégorie s nom et la Description est répertorié, séparés par une barre horizontale ([cliquez pour afficher l’image en taille réelle](nested-data-web-controls-cs/_static/image12.png))


## <a name="step-2-adding-the-nested-product-repeater"></a>Étape 2 : Ajout de répéteur produit imbriqués

Avec la liste complète des catégories, la tâche suivante consiste à ajouter un répéteur à la `CategoryList` s `ItemTemplate` qui affiche des informations sur les produits appartenant à la catégorie appropriée. Il existe de plusieurs façons, que nous pouvons récupérer les données pour cet répéteur interne, parmi lesquels nous allons Explorer peu de temps. Pour l’instant, s permettent de créer uniquement les produits répéteur au sein de la `CategoryList` répéteur s `ItemTemplate`. Plus précisément, permettent de s l’affichage répéteur chaque produit dans une liste à puces avec chaque élément de liste incluant le nom de produit s et le prix du produit.

Pour créer cet répéteur que nous avons besoin d’entrer manuellement les la syntaxe déclarative répéteur s interne et les modèles dans le `CategoryList` s `ItemTemplate`. Ajoutez le balisage suivant dans le `CategoryList` répéteur s `ItemTemplate`:


[!code-aspx[Main](nested-data-web-controls-cs/samples/sample2.aspx)]

## <a name="step-3-binding-the-category-specific-products-to-the-productsbycategorylist-repeater"></a>Étape 3 : Liaison de produits spécifiques à la catégorie à répéteur ProductsByCategoryList

Si vous visitez la page via un navigateur à ce stade, l’écran doit être le même que dans la Figure 4, car nous ve encore pour lier les données répéteur. Il existe plusieurs façons de nous permet de récupérer les enregistrements appropriées du produit et les lier à la répétition, plus efficace que d’autres. Le principal défi ici consiste à obtenir dans les produits appropriés pour la catégorie spécifiée.

Les données à lier au contrôle du répéteur interne soit accessibles de manière déclarative, via un ObjectDataSource dans le `CategoryList` répéteur s `ItemTemplate`, ou par programme, à partir de la page de code-behind s de page ASP.NET. De même, ces données peuvent être liées à répéteur interne soit de façon déclarative - via le répéteur interne s `DataSourceID` propriété ou via la syntaxe de liaison de données déclarative ou par programmation en référençant le répéteur interne dans le `CategoryList` répéteur s `ItemDataBound` Gestionnaire d’événements, définition par programme de ses `DataSource` propriété et en appelant sa `DataBind()` (méthode). Permettent d’Explorer chacune de ces approches s.

## <a name="accessing-the-data-declaratively-with-an-objectdatasource-control-and-theitemdataboundevent-handler"></a>Accès aux données de façon déclarative avec un contrôle ObjectDataSource et`ItemDataBound`Gestionnaire d’événements

Dans la mesure où la ve utilisé ObjectDataSource largement tout au long de cette série de didacticiels, le choix le plus naturel pour l’accès aux données pour cet exemple est à dire ObjectDataSource. Le `ProductsBLL` classe a un `GetProductsByCategoryID(categoryID)` méthode qui retourne des informations sur les produits qui appartiennent à l’objet  *`categoryID`* . Par conséquent, nous pouvons ajouter un ObjectDataSource à la `CategoryList` répéteur s `ItemTemplate` et configurez-le pour accéder à ses données à partir de cette méthode de classe s.

Malheureusement, t ne répéteur autoriser ses modèles d’être modifié via la vue de conception de sorte que nous devons ajouter la syntaxe déclarative pour ce contrôle ObjectDataSource manuellement. L’exemple de syntaxe suivant le `CategoryList` répéteur s `ItemTemplate` après l’ajout de cette nouvelle ObjectDataSource (`ProductsByCategoryDataSource`) :


[!code-aspx[Main](nested-data-web-controls-cs/samples/sample3.aspx)]

Lorsque vous utilisez l’approche ObjectDataSource nous devons définir le `ProductsByCategoryList` répéteur s `DataSourceID` propriété le `ID` de ObjectDataSource (`ProductsByCategoryDataSource`). Remarquez également que notre ObjectDataSource a un `<asp:Parameter>` élément qui spécifie le  *`categoryID`*  valeur qui sera passée dans le `GetProductsByCategoryID(categoryID)` (méthode). Mais comment spécifier cette valeur ? Dans l’idéal, nous d pouvoir définir uniquement les `DefaultValue` propriété de la `<asp:Parameter>` élément à l’aide de la syntaxe de liaison de données, comme suit :


[!code-aspx[Main](nested-data-web-controls-cs/samples/sample4.aspx)]

Malheureusement, la syntaxe de liaison de données est uniquement valide dans les contrôles qui ont un `DataBinding` événement. La `Parameter` classe ne dispose pas d’un tel événement et par conséquent, la syntaxe ci-dessus est non conforme et génère une erreur d’exécution.

Pour définir cette valeur, nous devons créer un gestionnaire d’événements pour le `CategoryList` répéteur s `ItemDataBound` événement. N’oubliez pas que le `ItemDataBound` événement est déclenché une fois pour chaque élément lié à la répétition. Par conséquent, chaque fois que cet événement se déclenche pour le répéteur externe nous pouvons attribuer actuel `CategoryID` valeur le `ProductsByCategoryDataSource` ObjectDataSource s `CategoryID` paramètre.

Créer un gestionnaire d’événements pour le `CategoryList` répéteur s `ItemDataBound` événement avec le code suivant :


[!code-csharp[Main](nested-data-web-controls-cs/samples/sample5.cs)]

Ce gestionnaire d’événements démarre en veillant à ce que nous re traitement comportant des données d’élément au lieu de l’élément d’en-tête, un pied de page ou séparateur. Ensuite, nous référençons réel `CategoriesRow` instance a simplement été liée à actuel `RepeaterItem`. Enfin, nous référençons ObjectDataSource dans le `ItemTemplate` et affecter sa `CategoryID` valeur de paramètre à la `CategoryID` actuelles `RepeaterItem`.

Avec ce gestionnaire d’événements, le `ProductsByCategoryList` répéteur dans chaque `RepeaterItem` est liée à ces produits dans le `RepeaterItem` catégorie de s. La figure 5 illustre une capture d’écran de la sortie.


[![Répéteur externe répertorie chaque catégorie ; L’objet interne répertorie les produits de la catégorie](nested-data-web-controls-cs/_static/image14.png)](nested-data-web-controls-cs/_static/image13.png)

**Figure 5**: le répéteur répertorie chaque catégorie externe ; les listes d’un interne les produits de la catégorie ([cliquez pour afficher l’image en taille réelle](nested-data-web-controls-cs/_static/image15.png))


## <a name="accessing-the-products-by-category-data-programmatically"></a>Accès par programme aux produits par les données de catégorie

Au lieu d’utiliser un ObjectDataSource pour extraire les produits pour la catégorie actuelle, nous pouvons créer une méthode dans notre classe code-behind de pages ASP.NET (ou dans le `App_Code` dossier ou dans un projet de bibliothèque de classes distinct) qui retourne l’ensemble approprié de produits lorsqu’ils sont passés dans un `CategoryID`. Imaginons que nous avons eu une telle méthode dans notre classe code-behind de pages ASP.NET et qu’il a été nommé `GetProductsInCategory(categoryID)`. Avec cette méthode en place, nous avons lier des produits pour la catégorie actuelle à répéteur interne à l’aide de la syntaxe déclarative suivante :


[!code-aspx[Main](nested-data-web-controls-cs/samples/sample6.aspx)]

Répéteur s `DataSource` propriété utilise la syntaxe de liaison de données pour indiquer que les données proviennent de la `GetProductsInCategory(categoryID)` (méthode). Étant donné que `Eval("CategoryID")` retourne une valeur de type `Object`, nous effectuer un cast de l’objet à un `Integer` avant de le transmettre dans le `GetProductsInCategory(categoryID)` (méthode). Notez que la `CategoryID` accessible via la liaison de données syntaxe Voici le `CategoryID` dans les *externe* répéteur (`CategoryList`), celui s liés aux enregistrements de la `Categories` table. Par conséquent, nous savons que `CategoryID` ne peut pas être une base de données `NULL` valeur, c’est pourquoi nous pouvons aveugle effectué le `Eval` méthode sans vérifier si nous re traiter un `DBNull`.

Avec cette approche, nous devons créer la `GetProductsInCategory(categoryID)` (méthode) et de récupérer l’ensemble approprié de produits donné fourni  *`categoryID`* . Ce faire, nous pouvons simplement retourner la `ProductsDataTable` retournée par le `ProductsBLL` classe s `GetProductsByCategoryID(categoryID)` (méthode). S permettent de créer la `GetProductsInCategory(categoryID)` méthode dans la classe code-behind pour notre `NestedControls.aspx` page. Pour cela utiliser le code suivant :


[!code-csharp[Main](nested-data-web-controls-cs/samples/sample7.cs)]

Cette méthode crée simplement une instance de la `ProductsBLL` méthode et retourne les résultats de la `GetProductsByCategoryID(categoryID)` (méthode). Notez que la méthode doit être marquée `Public` ou `Protected`; si la méthode est marquée comme `Private`, il ne sera pas accessible à partir du balisage déclaratif de pages ASP.NET.

Après avoir apporté ces modifications à utiliser cette nouvelle technologie, prenez un moment pour afficher la page via un navigateur. La sortie doit être identique à la sortie lors de l’utilisation de ObjectDataSource et `ItemDataBound` approche de gestionnaire d’événements (voir la Figure 5 pour voir un écran : WordGame).

> [!NOTE]
> Il peut sembler busywork pour créer le `GetProductsInCategory(categoryID)` méthode dans la classe code-behind de pages ASP.NET. Après tout, cette méthode crée simplement une instance de la `ProductsBLL` classe et retourne les résultats de ses `GetProductsByCategoryID(categoryID)` (méthode). Pourquoi ne pas simplement appeler cette méthode directement à partir de la syntaxe de liaison de données dans le répéteur interne, telles que : `DataSource='<%# ProductsBLL.GetProductsByCategoryID((int)(Eval("CategoryID"))) %>'`? Bien que cette syntaxe a gagné fonctionne avec notre implémentation actuelle de la `ProductsBLL` classe (étant donné que la `GetProductsByCategoryID(categoryID)` méthode est une méthode d’instance), vous pouvez modifier `ProductsBLL` à inclure statique `GetProductsByCategoryID(categoryID)` (méthode) ou l’inclure dans la classe statique `Instance()` méthode pour retourner une nouvelle instance de la `ProductsBLL` classe.


Alors que ces modifications élimine la nécessité pour le `GetProductsInCategory(categoryID)` méthode dans la classe code-behind de pages ASP.NET, la méthode de classe code-behind nous offre plus de souplesse dans l’utilisation avec les données extraites, comme nous le verrons dans quelques instants.

## <a name="retrieving-all-of-the-product-information-at-once"></a>La récupération de toutes les informations de produit à la fois

Les deux techniques précédente nous ve examiné saisir ces produits pour la catégorie actuelle en effectuant un appel à la `ProductsBLL` classe s `GetProductsByCategoryID(categoryID)` (méthode) (la première approche a fait via un ObjectDataSource, les deuxième et le `GetProductsInCategory(categoryID)` méthode dans le classe code-behind). Chaque fois que cette méthode est appelée, les appels de la couche de logique métier à la couche d’accès aux données, qui interroge la base de données avec une instruction SQL qui retourne des lignes à partir de la `Products` table dont `CategoryID` champ correspond au paramètre d’entrée fourni.

Fonction *N* filets de catégories dans le système, cette approche *N* + 1 appelle à la requête d’une base de données de base de données pour obtenir toutes les catégories, puis *N* appels pour obtenir les produits spécifiques à chaque catégorie. Nous pouvons, toutefois, récupérer toutes les données nécessaires dans base de données de deux appels un seul appel pour obtenir toutes les catégories et l’autre pour obtenir tous les produits. Une fois tous les produits, nous pouvons donc filtrer ces produits que seuls les produits en cours de mise en correspondance `CategoryID` sont liés à cette catégorie s répéteur interne.

Pour fournir cette fonctionnalité, nous avons besoin uniquement rendre une légère modification de la `GetProductsInCategory(categoryID)` méthode dans notre classe code-behind de pages ASP.NET. Plutôt que de manière aveugle renvoyer les résultats de la `ProductsBLL` classe s `GetProductsByCategoryID(categoryID)` (méthode), vous pouvez à la place d’abord accéder au *tous les* des produits (si elles vous n avez encore t été accessibles déjà), puis revenez simplement la vue filtrée de la en fonction des produits dans le passé dans `CategoryID`.


[!code-csharp[Main](nested-data-web-controls-cs/samples/sample8.cs)]

Notez l’ajout de la variable de niveau page, `allProducts`. Il contient des informations sur tous les produits et est rempli à la première fois le `GetProductsInCategory(categoryID)` méthode est appelée. Après vous être assuré que les `allProducts` objet a été créé et rempli, la méthode filtre les résultats de s DataTable tels seulement ceux dont les lignes `CategoryID` correspond à l’élément spécifié `CategoryID` sont accessibles. Cette approche réduit le nombre de fois où la base de données est accessible à partir de *N* + 1... deux.

Cette amélioration n’introduit pas de toute modification apportée au balisage de la page rendu, ni apporte dans moins d’enregistrements que l’autre approche. Simplement, il réduit le nombre d’appels à la base de données.

> [!NOTE]
> Un peut intuitivement motif que ce qui réduit le nombre d’accès de la base de données assuredly améliorera les performances. Toutefois, cela ne peut pas être le cas. Si vous avez un grand nombre de produits dont `CategoryID` est `NULL`, par exemple, puis l’appel à la `GetProducts` méthode retourne un nombre de produits qui ne sont jamais affichés. En outre, le retour de tous les produits peut être inutile si vous re affiche seulement un sous-ensemble des catégories, ce qui peut être le cas si vous avez implémenté la pagination.


Comme toujours, lorsqu’il s’agit de l’analyse des performances de ces deux techniques, la mesure uniquement sûr consiste à exécuter les tests contrôlés adaptées pour votre application s cas des scénarios courants.

## <a name="summary"></a>Résumé

Dans ce didacticiel, nous avons vu comment imbriquer des données d’un contrôle Web dans une autre, plus particulièrement examiner comment utiliser un répéteur externe pour afficher un élément pour chaque catégorie avec un répéteur interne qui répertorie les produits pour chaque catégorie dans une liste à puces. Le principal défi de la création d’une interface utilisateur imbriqués se trouve dans l’accès et la liaison des données correctes pour le contrôle Web de données interne. Il existe de nombreuses techniques disponibles, que parmi lesquels nous avons examiné dans ce didacticiel. La première approche examinée utilisé un ObjectDataSource dans les données externes, le contrôle Web s `ItemTemplate` qui a été liée au contrôle Web interne de données via son `DataSourceID` propriété. La seconde technique accédé aux données via une méthode dans une classe code-behind de s de page ASP.NET. Cette méthode peut alors être liée aux données internes le contrôle Web s `DataSource` propriété via la syntaxe de liaison de données.

Lors de l’interface utilisateur imbriqués examinée dans ce didacticiel un répéteur imbriqué dans un répéteur, ces techniques peuvent être étendues à d’autres contrôles Web de données. Vous pouvez imbriquer un répéteur dans un GridView ou un GridView au sein de DataList et ainsi de suite.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Zack Jones et Liz Shulok. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](showing-multiple-records-per-row-with-the-datalist-control-cs.md)
[Suivant](displaying-data-with-the-datalist-and-repeater-controls-vb.md)
