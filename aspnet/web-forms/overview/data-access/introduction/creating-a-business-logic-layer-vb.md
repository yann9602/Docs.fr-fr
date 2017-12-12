---
uid: web-forms/overview/data-access/introduction/creating-a-business-logic-layer-vb
title: "Création d’une couche de logique métier (VB) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous verrons comment centraliser vos règles d’entreprise dans une couche BLL (Business Logic) qui sert d’intermédiaire pour l’échange de données entre t..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/31/2010
ms.topic: article
ms.assetid: 142e5181-29ce-4bb9-907b-2a0becf7928b
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/introduction/creating-a-business-logic-layer-vb
msc.type: authoredcontent
ms.openlocfilehash: 7722ed54e333515f641f1c1adf647c4ec08dfb6b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-a-business-logic-layer-vb"></a>Création d’une couche de logique métier (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/5/d/7/5d7571fc-d0b7-4798-ad4a-c976c02363ce/ASPNET_Data_Tutorial_2_VB.exe) ou [télécharger le PDF](creating-a-business-logic-layer-vb/_static/datatutorial02vb1.pdf)

> Dans ce didacticiel, nous verrons comment centraliser vos règles d’entreprise dans une couche BLL (Business Logic) qui sert d’intermédiaire pour l’échange de données entre la couche présentation et la couche DAL.


## <a name="introduction"></a>Introduction

La couche DAL (Data Access) est créé dans le [premier didacticiel](creating-a-data-access-layer-vb.md) proprement sépare les données d’accès logique à partir de la logique de présentation. Toutefois, alors que la couche DAL sépare clairement les détails d’accès aux données à partir de la couche de présentation, il n’applique pas les règles d’entreprise qui peuvent s’appliquer. Par exemple, pour notre application, nous pouvons adopter interdire la `CategoryID` ou `SupplierID` champs de la `Products` table d’être modifiées lorsque le `Discontinued` champ est défini sur 1, ou que nous voulons appliquer des règles d’ancienneté, interdiction des situations dans lesquelles un employé est gérée par une personne qui a été embauché après ces derniers. Un autre scénario courant est d’autorisation peut-être que seuls les utilisateurs dans un rôle particulier peuvent supprimer des produits ou peuvent modifier le `UnitPrice` valeur.

Dans ce didacticiel, nous verrons comment centraliser ces règles d’entreprise dans une couche BLL (Business Logic) qui sert d’intermédiaire pour l’échange de données entre la couche présentation et la couche DAL. Dans une application réelle, la couche BLL doit être implémentée en tant qu’un projet de bibliothèque de classes distinct ; Toutefois, pour ces didacticiels nous allons implémenter la couche BLL comme une série de classes dans notre `App_Code` dossier afin de simplifier la structure de projet. La figure 1 illustre les relations architecture entre la couche de présentation, BLL et DAL.


![La couche BLL sépare la couche de présentation à partir de la couche d’accès aux données et impose des règles d’entreprise](creating-a-business-logic-layer-vb/_static/image1.png)

**Figure 1**: la couche BLL sépare la couche de présentation à partir de la couche d’accès aux données et impose des règles d’entreprise


Au lieu de créer des classes distinctes pour implémenter notre [logique métier](http://en.wikipedia.org/wiki/Business_logic), nous avons également placer cette logique directement dans le DataSet typé avec des classes partielles. Pour obtenir un exemple de création et l’extension d’un DataSet typé, font référence au premier didacticiel.

## <a name="step-1-creating-the-bll-classes"></a>Étape 1 : Création des Classes de couche BLL

Notre BLL sera composé de quatre classes, une pour chaque TableAdapter dans la couche DAL ; chacune de ces classes de la couche BLL aura des méthodes pour la récupération, d’insertion, de mise à jour et de suppression de TableAdapter respectif dans la couche DAL, en appliquant les règles d’entreprise approprié.

Au plus correctement séparer les classes lié à la couche DAL et la couche BLL, nous allons créer deux sous-dossiers dans le `App_Code` dossier, `DAL` et `BLL`. Simplement avec le bouton droit sur le `App_Code` dossier dans l’Explorateur de solutions et choisissez un nouveau dossier. Après avoir créé ces deux dossiers, déplacer le groupe de données typés créés dans le premier didacticiel en le `DAL` sous-dossier.

Ensuite, créez les quatre fichiers de classe BLL dans le `BLL` sous-dossier. Pour ce faire, cliquez sur le `BLL` sous-dossier, cliquez sur Ajouter un nouvel élément, puis choisissez le modèle de classe. Nommez les quatre classes `ProductsBLL`, `CategoriesBLL`, `SuppliersBLL`, et `EmployeesBLL`.


![Ajoutez quatre nouvelles Classes vers le dossier App_Code](creating-a-business-logic-layer-vb/_static/image2.png)

**Figure 2**: ajoutez quatre nouvelles Classes pour la `App_Code` dossier


Ensuite, nous allons ajouter des méthodes à chacune des classes simplement d’intégrer les méthodes définies pour les TableAdapters dans le premier didacticiel. Pour l’instant, ces méthodes appellera simplement directement dans la couche DAL ; Nous allons revenir plus tard pour ajouter la logique métier nécessaire.

> [!NOTE]
> Si vous utilisez Visual Studio Standard Edition ou version ultérieure (autrement dit, vous êtes *pas* à l’aide de Visual Web Developer), vous pouvez concevoir éventuellement vos classes visuellement à l’aide de la [le Concepteur de classes](https://msdn.microsoft.com/library/default.asp?url=/library/en-us/dv_vstechart/html/clssdsgnr.asp). Reportez-vous à la [classe concepteur Blog](https://blogs.msdn.com/classdesigner/default.aspx) pour plus d’informations sur cette nouvelle fonctionnalité de Visual Studio.


Pour le `ProductsBLL` nous devons ajouter un total de sept méthodes de classe :

- `GetProducts()`Retourne tous les produits
- `GetProductByProductID(productID)`Retourne le produit avec l’ID de produit spécifié
- `GetProductsByCategoryID(categoryID)`Retourne tous les produits à partir de la catégorie spécifiée
- `GetProductsBySupplier(supplierID)`Retourne tous les produits du fournisseur spécifié
- `AddProduct(productName, supplierID, categoryID, quantityPerUnit, unitPrice, unitsInStock, unitsOnOrder, reorderLevel, discontinued)`Insère un nouveau produit dans la base de données en utilisant les valeurs passées dans ; Retourne le `ProductID` la valeur de l’enregistrement qui vient d’être inséré.
- `UpdateProduct(productName, supplierID, categoryID, quantityPerUnit, unitPrice, unitsInStock, unitsOnOrder, reorderLevel, discontinued, productID)`met à jour un produit existant dans la base de données en utilisant les valeurs passées dans ; Retourne `True` précisément une ligne a été mise à jour, `False` dans le cas contraire
- `DeleteProduct(productID)`Supprime le produit spécifié à partir de la base de données

ProductsBLL.vb


[!code-vb[Main](creating-a-business-logic-layer-vb/samples/sample1.vb)]

Les méthodes qui retournent des données `GetProducts`, `GetProductByProductID`, `GetProductsByCategoryID`, et `GetProductBySuppliersID` sont assez simples qu’ils simplement vers le bas dans la couche DAL. Alors que dans certains scénarios il peut y avoir des règles d’entreprise qui doivent être implémentées à ce niveau (par exemple, les règles d’autorisation basée sur l’utilisateur actuellement connecté ou le rôle auquel appartient l’utilisateur), nous allons simplement laisser ces méthodes en tant que-est. Pour ces méthodes, puis, la couche BLL sert simplement un proxy via lequel la couche de présentation accède aux données sous-jacentes à partir de la couche d’accès aux données.

Le `AddProduct` et `UpdateProduct` méthodes à la fois prendre dans en tant que paramètres, les valeurs pour les différents champs de produit et ajouter un nouveau produit ou mettre à jour d’un existant, respectivement. Depuis de nombreuses le `Product` des colonnes de la table peuvent accepter `NULL` valeurs (`CategoryID`, `SupplierID`, et `UnitPrice`, à titre d’exemple), les paramètres d’entrée pour `AddProduct` et `UpdateProduct` qui correspondent à une telle utilisation de colonnes [types nullable](https://msdn.microsoft.com/en-us/library/1t3y8s4s(v=vs.80).aspx). Types Nullable sont des nouveautés de .NET 2.0 et fournir une technique de qui indique si un type valeur doit donc être `Nothing`. Faire référence à la [Paul Vick](http://www.panopticoncentral.net/)d’entrée de blog [la vérité sur les Types Nullable et VB](http://www.panopticoncentral.net/archive/2004/06/04/1180.aspx) et la documentation technique de la [Nullable](https://msdn.microsoft.com/en-US/library/b3h38hb0%28VS.80%29.aspx) structure pour plus d’informations.

Les trois méthodes retournent une valeur booléenne indiquant si une ligne a été insérée, mise à jour ou supprimée, car l’opération ne peut pas entraîner une ligne concernée. Par exemple, si le développeur de pages appelle `DeleteProduct` en passant un `ProductID` pour un produit inexistant, le `DELETE` instruction émise à la base de données n’a aucun effet et par conséquent la `DeleteProduct` méthode retournera `False`.

Notez que lors de l’ajout d’un nouveau produit ou de mise à jour un nous prendre dans les valeurs de champ du produit nouveau ou modifié sous forme de liste de valeurs scalaires par opposition à l’acceptation un `ProductsRow` instance. Cette approche a été choisie parce que le `ProductsRow` classe dérive de ADO.NET `DataRow` (classe), qui n’absence pas un constructeur sans paramètre par défaut. Pour créer un nouveau `ProductsRow` instance, nous devons tout d’abord créer un `ProductsDataTable` de l’instance, puis appelez ses `NewProductRow()` (méthode) (qui nous le faisons dans `AddProduct`). Cet inconvénient élevant sa tête lorsque nous tourner vers insertion et de mise à jour de produits à l’aide de ObjectDataSource. En bref, ObjectDataSource tente de créer une instance des paramètres d’entrée. Si la méthode de la couche BLL attend un `ProductsRow` instance, ObjectDataSource va tenter de créer un, mais échouer en raison de l’absence d’un constructeur sans paramètre par défaut. Pour plus d’informations sur ce problème, consultez les publications de Forums ASP.NET deux suivantes : [ObjectDataSources de mise à jour des jeux de données fortement typée](https://forums.asp.net/1098630/ShowPost.aspx), et [problème avec ObjectDataSource et fortement typée DataSet](https://forums.asp.net/1048212/ShowPost.aspx).

Ensuite, dans les deux `AddProduct` et `UpdateProduct`, le code crée un `ProductsRow` de l’instance et le remplit avec les valeurs passées uniquement. Lorsque vous attribuez des valeurs à DataColumns d’un DataRow plusieurs vérifications de validation au niveau du champ peuvent se produire. Par conséquent, remettre manuellement les valeurs passées dans un DataRow permet de garantir la validité des données transmises à la méthode de la couche BLL. Malheureusement, les classes de DataRow fortement typé générés par Visual Studio n’utilisent pas les types nullable. Au lieu de cela, pour indiquer qu’une colonne de données spécifique dans un DataRow doit correspondre à un `NULL` de base de données de valeur, nous devons utiliser la `SetColumnNameNull()` (méthode).

Dans `UpdateProduct` nous d’abord charger dans le produit à l’aide de `GetProductByProductID(productID)`. Cela peut sembler un aller-retour inutile vers la base de données, ce trajet supplémentaire s’avère utile dans les didacticiels futures qui explorent d’accès concurrentiel optimiste. L’accès concurrentiel optimiste est une technique pour vous assurer que deux utilisateurs qui travaillent simultanément sur les mêmes données ne pas écraser accidentellement les modifications par un autre. En saisissant l’ensemble de l’enregistrement rend également plus facile de créer des méthodes de mise à jour dans la couche BLL qui modifient les uniquement un sous-ensemble de colonnes de DataRow. Lorsque nous Explorez le `SuppliersBLL` nous allons voir un exemple de classe.

Enfin, notez que le `ProductsBLL` classe a la [attribut DataObject](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataobjectattribute.aspx) appliquée (la `[System.ComponentModel.DataObject]` syntaxe juste avant l’instruction de classe vers le haut du fichier) et les méthodes ont [ Les attributs DataObjectMethodAttribute](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataobjectmethodattribute.aspx). Le `DataObject` attribut marque la classe comme étant un objet pouvant être lié à un [contrôle ObjectDataSource](https://msdn.microsoft.com/en-us/library/9a4kyhcx.aspx), alors que le `DataObjectMethodAttribute` indique l’objectif de la méthode. Comme nous allons le voir dans les futures didacticiels, ASP.NET 2.0 ObjectDataSource facilite de façon déclarative à accéder aux données à partir d’une classe. Pour vous aider à filtrer la liste de classes possible pour établir une liaison dans l’Assistant de l’ObjectDataSource, par défaut, uniquement les classes marquées comme `DataObjects` sont affichés dans la liste déroulante de l’Assistant. La `ProductsBLL` classe fonctionne aussi bien sans ces attributs, mais ajouter rend plus facile à utiliser dans l’Assistant de l’ObjectDataSource.

## <a name="adding-the-other-classes"></a>Ajout d’autres Classes

Avec la `ProductsBLL` classe terminée, nous devons encore ajouter des classes pour l’utilisation des catégories, des fournisseurs et des employés. Prenez un moment pour créer les classes et les concepts de l’exemple ci-dessus d’à l’aide des méthodes suivantes :

- **CategoriesBLL.cs**

    - `GetCategories()`
    - `GetCategoryByCategoryID(categoryID)`
- **SuppliersBLL.cs**

    - `GetSuppliers()`
    - `GetSupplierBySupplierID(supplierID)`
    - `GetSuppliersByCountry(country)`
    - `UpdateSupplierAddress(supplierID, address, city, country)`
- **EmployeesBLL.cs**

    - `GetEmployees()`
    - `GetEmployeeByEmployeeID(employeeID)`
    - `GetEmployeesByManager(managerID)`

La méthode à noter est la `SuppliersBLL` la classe `UpdateSupplierAddress` (méthode). Cette méthode fournit une interface pour mettre à jour uniquement les informations d’adresse du fournisseur. En interne, cette méthode lit dans le `SupplierDataRow` objet spécifié `supplierID` (à l’aide de `GetSupplierBySupplierID`), définit ses propriétés associées à l’adresse et appelle ensuite vers le bas dans la `SupplierDataTable`de `Update` (méthode). Le `UpdateSupplierAddress` méthode suit :


[!code-vb[Main](creating-a-business-logic-layer-vb/samples/sample2.vb)]

Reportez-vous au téléchargement de cet article pour mon implémentation complète de classes de la couche BLL.

## <a name="step-2-accessing-the-typed-datasets-through-the-bll-classes"></a>Étape 2 : L’accès à des groupes de données typés via les Classes de la couche BLL

Dans le premier didacticiel, nous avons vu exemples de travailler directement avec le DataSet typé par programme, mais avec l’ajout de classes de notre BLL, la couche de présentation doit fonctionner par rapport à la couche BLL à la place. Dans le `AllProducts.aspx` exemple à partir du premier didacticiel, la `ProductsTableAdapter` a été utilisée pour lier la liste des produits pour un GridView, comme indiqué dans le code suivant :


[!code-vb[Main](creating-a-business-logic-layer-vb/samples/sample3.vb)]

Pour utiliser la couche BLL de nouvelles classes, tout ce qui doit être modifiée est la première ligne de code remplacez simplement la `ProductsTableAdapter` de l’objet avec un `ProductBLL` objet :


[!code-vb[Main](creating-a-business-logic-layer-vb/samples/sample4.vb)]

Les classes de la couche BLL sont également accessibles déclarative (comme le DataSet typé) à l’aide de ObjectDataSource. Nous allons décrivons ObjectDataSource plus en détail dans les didacticiels suivants.


[![La liste de produits s’affiche dans un contrôle GridView](creating-a-business-logic-layer-vb/_static/image4.png)](creating-a-business-logic-layer-vb/_static/image3.png)

**Figure 3**: la liste de produits s’affiche dans un contrôle GridView ([cliquez pour afficher l’image en taille réelle](creating-a-business-logic-layer-vb/_static/image5.png))


## <a name="step-3-adding-field-level-validation-to-the-datarow-classes"></a>Étape 3 : Ajout d’une Validation au niveau du champ dans les Classes de DataRow

La validation au niveau du champ sont les vérifications concernant les valeurs de propriété des objets métier lors de l’insertion ou de mise à jour. Certaines règles de validation au niveau du champ pour les produits sont les suivantes :

- Le `ProductName` champ doit comporter 40 caractères au maximum
- Le `QuantityPerUnit` champ doit être de 20 caractères ou moins
- Le `ProductID`, `ProductName`, et `Discontinued` champs sont obligatoires, mais tous les autres champs sont facultatifs
- Le `UnitPrice`, `UnitsInStock`, `UnitsOnOrder`, et `ReorderLevel` champs doivent être supérieure ou égale à zéro

Ces règles peuvent et doivent être exprimés au niveau de la base de données. La limite de caractères sur la `ProductName` et `QuantityPerUnit` champs sont capturées par les types de données de ces colonnes dans le `Products` table (`nvarchar(40)` et `nvarchar(20)`, respectivement). Si les champs sont obligatoires et facultatifs sont exprimés en si la colonne de table de base de données autorise `NULL` s. Quatre [contraintes check](https://msdn.microsoft.com/en-us/library/ms188258.aspx) existe visant à garantir que seules les valeurs supérieures ou égales à zéro peuvent rendre dans le `UnitPrice`, `UnitsInStock`, `UnitsOnOrder`, ou `ReorderLevel` colonnes.

En plus de l’application de ces règles à la base de données qu’ils doivent également être appliquées au niveau du jeu de données. En fait, la longueur de champ et si une valeur est obligatoire ou facultatif sont déjà capturés pour l’ensemble de chaque DataTable de DataColumns. Pour afficher la validation au niveau du champ existante fournie automatiquement, accédez au Concepteur de DataSet, sélectionnez un champ parmi les tables de données et puis accédez à la fenêtre Propriétés. Comme le montre la Figure 4, le `QuantityPerUnit` DataColumn dans le `ProductsDataTable` a une longueur maximale de 20 caractères et autorise `NULL` valeurs. Si vous essayez de définir la `ProductsDataRow`de `QuantityPerUnit` propriété à une valeur de chaîne plue de 20 caractères un `ArgumentException` sera levée.


[![L’objet DataColumn fournit une Validation au niveau du champ de base](creating-a-business-logic-layer-vb/_static/image7.png)](creating-a-business-logic-layer-vb/_static/image6.png)

**Figure 4**: le DataColumn fournit au niveau du champ Validation de base ([cliquez pour afficher l’image en taille réelle](creating-a-business-logic-layer-vb/_static/image8.png))


Malheureusement, nous ne pouvons pas spécifier des contrôles de limites, telles que la `UnitPrice` valeur doit être supérieure ou égale à zéro, via la fenêtre Propriétés. Pour fournir ce type de validation de champ, nous devons créer un gestionnaire d’événements pour la table de données [ColumnChanging](https://msdn.microsoft.com/en-us/library/system.data.datatable.columnchanging%28VS.80%29.aspx) événement. Comme indiqué dans le [didacticiel précédent](creating-a-data-access-layer-vb.md), les objets DataSet, DataTables et DataRow créés par le DataSet typé peuvent être étendus via l’utilisation des classes partielles. À l’aide de cette technique, nous pouvons créer un `ColumnChanging` Gestionnaire d’événements pour le `ProductsDataTable` classe. Commencez par créer une classe dans le `App_Code` dossier nommé `ProductsDataTable.ColumnChanging.vb`.


[![Ajoutez une nouvelle classe dans le dossier App_Code](creating-a-business-logic-layer-vb/_static/image10.png)](creating-a-business-logic-layer-vb/_static/image9.png)

**Figure 5**: ajouter une nouvelle classe à la `App_Code` dossier ([cliquez pour afficher l’image en taille réelle](creating-a-business-logic-layer-vb/_static/image11.png))


Ensuite, créez un gestionnaire d’événements pour le `ColumnChanging` événement qui garantit que le `UnitPrice`, `UnitsInStock`, `UnitsOnOrder`, et `ReorderLevel` les valeurs de colonne (si ce n’est pas `NULL`) est supérieur ou égal à zéro. Si une telle colonne est hors limites, levez une `ArgumentException`.

ProductsDataTable.ColumnChanging.vb


[!code-vb[Main](creating-a-business-logic-layer-vb/samples/sample5.vb)]

## <a name="step-4-adding-custom-business-rules-to-the-blls-classes"></a>Étape 4 : Ajout de règles d’entreprise personnalisées pour les Classes de la couche BLL

En plus de la validation au niveau du champ, il peut y avoir des règles d’entreprise personnalisées haut niveau qui impliquent des différentes entités ou des concepts ne pouvant pas être exprimées au niveau de la colonne unique, tel que :

- Si un produit n’est plus disponible, ses `UnitPrice` ne peut pas être mis à jour
- Pays de l’employé de résidence doit être le même que le pays de son gestionnaire de résidence
- Un produit ne peut pas être supprimé s’il s’agit du seul produit fourni par le fournisseur

Les classes de la couche BLL doivent contenir des contrôles pour garantir le respect des règles d’entreprise de l’application. Ces contrôles peuvent être ajoutés directement aux méthodes auquel ils s’appliquent.

Imaginez que les règles d’entreprise imposent qu’un produit ont pas pu être marqué supprimée s’il s’agit du seul produit à partir d’un fournisseur donné. Autrement dit, si produit *X* a été le seul produit nous achetés à partir du fournisseur *Y*, nous ne pouvons pas marquer *X* comme abandonnés ; si, toutefois, fournisseur *Y*nous fournie avec trois produits, *A*, *B*, et *C*, nous avons Marquer tout, puis tous ces éléments comme supprimée. Une règle d’entreprise impair, mais les règles d’entreprise et le bon sens ne sont pas alignées toujours !

Pour appliquer cette règle d’entreprise dans le `UpdateProducts` méthode commence par vérifier si nous `Discontinued` a été définie sur `True` et, si nous serait donc appeler `GetProductsBySupplierID` pour déterminer combien de produits nous acheté à partir de ce fournisseur. Si seul un produit est acheté auprès de ce fournisseur, nous levons une `ApplicationException`.


[!code-vb[Main](creating-a-business-logic-layer-vb/samples/sample6.vb)]

## <a name="responding-to-validation-errors-in-the-presentation-tier"></a>Réponse aux erreurs de Validation dans la couche de présentation

Lors de l’appel de la couche BLL à partir de la couche de présentation, nous pouvons choisir s’il faut essayer de gérer toutes les exceptions qui peuvent être déclenchées ou leur permettant de se propagent dans ASP.NET (ce qui déclenchera le `HttpApplication`de `Error` événements). Pour gérer une exception lors de l’utilisation de la couche BLL par programme, nous pouvons utiliser un [essayez... Catch](https://msdn.microsoft.com/en-us/library/fk6t46tz%28VS.80%29.aspx) bloc, comme le montre l’exemple suivant :


[!code-vb[Main](creating-a-business-logic-layer-vb/samples/sample7.vb)]

Comme nous le verrons dans les futures didacticiels, gestion des exceptions qui se propagent de la couche BLL lors de l’aide de données Web de contrôle pour l’insertion, mise à jour, ou la suppression des données peut être gérée directement dans un gestionnaire d’événements au lieu d’encapsuler le code dans `Try...Catch` blocs.

## <a name="summary"></a>Résumé

Une application bien conçue est spécialement conçue en couches distinctes, chacune encapsulant un rôle particulier. Dans le premier didacticiel de cette série de l’article, nous avons créé une couche d’accès aux données à l’aide de données typés ; Dans ce didacticiel nous intégré une couche de logique métier comme une série de classes de notre application `App_Code` dossier appel descendant dans notre DAL. La couche BLL implémente la logique au niveau du champ et au niveau de l’entreprise pour notre application. Outre la création d’une couche BLL distinct, comme nous l’avons fait dans ce didacticiel, une autre option consiste à étendre les méthodes des TableAdapters via l’utilisation des classes partielles. Toutefois, à l’aide de cette technique ne permet pas de substituer des méthodes existantes ni il sépare notre DAL et notre BLL est correctement l’approche que nous avons effectuée dans cet article.

Avec la couche DAL et de la couche BLL terminée, nous sommes prêts à démarrer sur la couche de présentation. Dans le [didacticiel suivant](master-pages-and-site-navigation-vb.md) nous allons prendre un détournement brève à partir des rubriques d’accès aux données et définir une disposition cohérente des pages à utiliser dans les didacticiels.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Liz Shulok, Dennis Patterson, Carlos Santos et Hilton Giesenow. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](creating-a-data-access-layer-vb.md)
[Suivant](master-pages-and-site-navigation-vb.md)
