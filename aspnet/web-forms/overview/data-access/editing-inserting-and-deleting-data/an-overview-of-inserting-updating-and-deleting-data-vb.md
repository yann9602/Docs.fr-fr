---
uid: web-forms/overview/data-access/editing-inserting-and-deleting-data/an-overview-of-inserting-updating-and-deleting-data-vb
title: "Une vue d’ensemble de l’insertion, mise à jour et suppression de données (VB) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous verrons comment mapper un ObjectDataSource Insert(), Update(), et des classes de méthodes Delete() aux méthodes de la couche BLL, ainsi que comment la configuration..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/17/2006
ms.topic: article
ms.assetid: 35b40b8f-2ca8-4ab3-9c19-f361a91a3647
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/editing-inserting-and-deleting-data/an-overview-of-inserting-updating-and-deleting-data-vb
msc.type: authoredcontent
ms.openlocfilehash: e7552abb30aa26d3aaceb3312c00661c6d4d6cf8
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="an-overview-of-inserting-updating-and-deleting-data-vb"></a>Une vue d’ensemble de l’insertion, mise à jour et suppression de données (Visual Basic)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_16_VB.exe) ou [télécharger le PDF](an-overview-of-inserting-updating-and-deleting-data-vb/_static/datatutorial16vb1.pdf)

> Dans ce didacticiel, nous verrons comment mapper un ObjectDataSource Insert(), Update(), et des classes de méthodes Delete() aux méthodes de la couche BLL, ainsi que comment configurer les contrôles GridView, DetailsView et FormView pour fournir des fonctions de modification de données.


## <a name="introduction"></a>Introduction

Sur les didacticiels de plusieurs cours, nous avons examiné comment afficher des données dans une page ASP.NET à l’aide de contrôles GridView, DetailsView et FormView. Ces contrôles fonctionnent avec les données fournies à ceux-ci. En règle générale, ces contrôles accéder aux données via l’utilisation d’un contrôle de source de données, tels que ObjectDataSource. Nous avons vu comment ObjectDataSource agit comme un proxy entre la page ASP.NET et les données sous-jacentes. Lorsqu’un GridView doit afficher des données, il appelle son ObjectDataSource `Select()` (méthode), qui à son tour appelle une méthode à partir de la couche BLL (Business Logic), qui appelle une méthode dans l’approprié données couche (DAL) TableAdapter, qui à son tour envoie un `SELECT` requête à la base de données Northwind.

N’oubliez pas que, lorsque nous avons créé les TableAdapters dans la couche DAL dans [notre premier didacticiel](../introduction/creating-a-data-access-layer-cs.md), Visual Studio ajouté automatiquement des méthodes d’insertion, mise à jour, et suppression de données sous-jacent de base de données de la table. En outre, dans [création d’une couche de logique métier](../introduction/creating-a-business-logic-layer-vb.md) méthodes dans la couche BLL ayant appelé sont conçus dans ces méthodes de modification de la couche DAL de données.

En plus de son `Select()` méthode ObjectDataSource a également `Insert()`, `Update()`, et `Delete()` méthodes. Comme le `Select()` (méthode), ces trois méthodes peuvent être mappés aux méthodes dans un objet sous-jacent. Lorsque configuré pour insérer, mettre à jour ou supprimer des données, les contrôles GridView, DetailsView et FormView fournissent une interface utilisateur permettant de modifier les données sous-jacentes. Cette interface utilisateur appelle la `Insert()`, `Update()`, et `Delete()` méthodes de ObjectDataSource, puis appellent l’objet sous-jacent associé à des méthodes (voir Figure 1).


[![L’ObjectDataSource Insert() Update() et les méthodes Delete() servent d’un Proxy dans la couche BLL](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image2.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image1.png)

**Figure 1**: le ObjectDataSource `Insert()`, `Update()`, et `Delete()` méthodes servir en tant que Proxy dans la couche BLL ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image3.png))


Dans ce didacticiel, nous verrons comment mapper l’ObjectDataSource `Insert()`, `Update()`, et `Delete()` méthodes aux méthodes des classes de la couche BLL, ainsi que comment configurer les contrôles GridView, DetailsView et FormView afin de fournir la modification des données fonctionnalités.

## <a name="step-1-creating-the-insert-update-and-delete-tutorials-web-pages"></a>Étape 1 : Création de l’insertion, mise à jour et supprimer des Pages Web didacticiels

Avant de commencer à Explorer comment insérer, mettre à jour et supprimer des données, nous prenons tout d’abord un moment pour créer les pages ASP.NET dans notre projet de site Web que nous aurons besoin pour ce didacticiel et celles plusieurs suivants. Commencez par ajouter un nouveau dossier nommé `EditInsertDelete`. Ensuite, ajoutez les pages ASP.NET suivantes à ce dossier, en veillant à associer chaque page avec le `Site.master` page maître :

- `Default.aspx`
- `Basics.aspx`
- `DataModificationEvents.aspx`
- `ErrorHandling.aspx`
- `UIValidation.aspx`
- `CustomizedUI.aspx`
- `OptimisticConcurrency.aspx`
- `ConfirmationOnDelete.aspx`
- `UserLevelAccess.aspx`


![Ajouter les Pages ASP.NET pour les didacticiels relatifs à la Modification de données](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image4.png)

**Figure 2**: ajouter les Pages ASP.NET pour les didacticiels relatifs à la Modification de données


Comme dans les autres dossiers, `Default.aspx` dans le `EditInsertDelete` dossier répertorie les didacticiels dans sa section. N’oubliez pas que le `SectionLevelTutorialListing.ascx` contrôle utilisateur fournit cette fonctionnalité. Par conséquent, ajoutez ce contrôle utilisateur pour `Default.aspx` en le faisant glisser à partir de l’Explorateur de solutions sur le mode de création de la page.


[![Ajouter le contrôle utilisateur de SectionLevelTutorialListing.ascx vers Default.aspx](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image6.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image5.png)

**Figure 3**: ajouter la `SectionLevelTutorialListing.ascx` contrôle utilisateur à `Default.aspx` ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image7.png))


Enfin, ajoutez les pages en tant qu’entrées pour les `Web.sitemap` fichier. En particulier, ajoutez le balisage suivant après la mise en forme personnalisée `<siteMapNode>`:


[!code-xml[Main](an-overview-of-inserting-updating-and-deleting-data-vb/samples/sample1.xml)]

Après la mise à jour `Web.sitemap`, prenez un moment pour afficher le site Web des didacticiels via un navigateur. Le menu de gauche inclut désormais les éléments pour la modification, l’insertion et la suppression des didacticiels.


![Le plan de Site inclut maintenant des entrées pour la modification, l’insertion et la suppression des didacticiels](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image8.png)

**Figure 4**: le plan de Site inclut maintenant des entrées pour la modification, l’insertion et la suppression des didacticiels


## <a name="step-2-adding-and-configuring-the-objectdatasource-control"></a>Étape 2 : Ajout et configuration du contrôle ObjectDataSource

Depuis le GridView, DetailsView et FormView chacune différer dans leur modification des données et la mise en page, nous allons examiner chacun d’eux individuellement. Plutôt que d’avoir à chaque contrôle à l’aide de son propre ObjectDataSource, toutefois, nous allons simplement créer un seul ObjectDataSource que tous les exemples de contrôle trois peuvent partager.

Ouvrez le `Basics.aspx` page, faites glisser un ObjectDataSource à partir de la boîte à outils vers le concepteur, cliquez sur le lien configurer la Source de données à partir de sa balise active. Étant donné que le `ProductsBLL` est la seule classe BLL qui fournit la modification, d’insertion et de suppression des méthodes, configurent l’ObjectDataSource pour utiliser cette classe.


[![Configurer pour utiliser la classe ProductsBLL ObjectDataSource](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image10.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image9.png)

**Figure 5**: configurer l’ObjectDataSource à utiliser le `ProductsBLL` classe ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image11.png))


Dans l’écran suivant, nous pouvons spécifier quelles méthodes de la `ProductsBLL` classe sont mappées à l’ObjectDataSource `Select()`, `Insert()`, `Update()`, et `Delete()` en sélectionnant l’onglet approprié, la méthode dans la liste déroulante. Figure 6, qui doivent sembler familières à ce stade, mappe l’ObjectDataSource `Select()` méthode à la `ProductsBLL` la classe `GetProducts()` (méthode). Le `Insert()`, `Update()`, et `Delete()` méthodes peuvent être configurées en sélectionnant l’onglet approprié dans la liste en haut.


[![Avoir ObjectDataSource retourner tous les produits](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image13.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image12.png)

**Figure 6**: ont l’ObjectDataSource retourner tous les produits ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image14.png))


Onglets de figures 7, 8 et 9 montrent l’ObjectDataSource UPDATE, INSERT et DELETE. Configurer ces onglets afin que le `Insert()`, `Update()`, et `Delete()` méthodes appellent le `ProductsBLL` de classe `UpdateProduct`, `AddProduct`, et `DeleteProduct` méthodes, respectivement.


[![Mapper Update() méthode l’ObjectDataSource à UpdateProduct (méthode la classe ProductBLL)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image16.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image15.png)

**Figure 7**: mappez l’ObjectDataSource `Update()` méthode à la `ProductBLL` la classe `UpdateProduct` (méthode) ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image17.png))


[![Mapper Insert() méthode l’ObjectDataSource à AddProduct méthode la classe ProductBLL](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image19.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image18.png)

**Figure 8**: mappez l’ObjectDataSource `Insert()` méthode à la `ProductBLL` ajouter de la classe `Product` (méthode) ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image20.png))


[![Mapper Delete() méthode l’ObjectDataSource à DeleteProduct (méthode la classe ProductBLL)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image22.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image21.png)

**Figure 9**: mappez l’ObjectDataSource `Delete()` méthode à la `ProductBLL` la classe `DeleteProduct` (méthode) ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image23.png))


Vous avez peut-être remarqué que les listes déroulantes dans les onglets UPDATE, INSERT et DELETE avaient déjà ces méthodes sélectionnés. C’est grâce à notre utilisation de la `DataObjectMethodAttribute` qui décore les méthodes de `ProducstBLL`. Par exemple, la méthode DeleteProduct possède la signature suivante :


[!code-vb[Main](an-overview-of-inserting-updating-and-deleting-data-vb/samples/sample2.vb)]

Le `DataObjectMethodAttribute` attribut indique l’objectif de chaque méthode, s’il s’agit de la sélection, insertion, mise à jour, ou la suppression et indique si elle est la valeur par défaut. Si vous omettez ces attributs lors de la création de vos classes de la couche BLL, vous allez peut-être afin de sélectionner manuellement les méthodes à partir de la mise à jour, insérer et supprimer des onglets.

Après vous être assuré que les `ProductsBLL` méthodes sont mappées à l’ObjectDataSource `Insert()`, `Update()`, et `Delete()` méthodes, cliquez sur Terminer pour terminer l’Assistant.

## <a name="examining-the-objectdatasources-markup"></a>Examen balisage de l’ObjectDataSource

Après avoir configuré l’ObjectDataSource via l’Assistant, passez à la vue de Source pour examiner le balisage déclaratif généré. Le `<asp:ObjectDataSource>` balise spécifie l’objet sous-jacent et les méthodes à appeler. En outre, il existe `DeleteParameters`, `UpdateParameters`, et `InsertParameters` qui correspondent aux paramètres d’entrée pour le `ProductsBLL` de classe `AddProduct`, `UpdateProduct`, et `DeleteProduct` méthodes :


[!code-aspx[Main](an-overview-of-inserting-updating-and-deleting-data-vb/samples/sample3.aspx)]

ObjectDataSource inclut un paramètre pour chacun des paramètres d’entrée pour ses méthodes associées, tout comme une liste de `SelectParameter` s est présent lorsque l’ObjectDataSource est configurée pour appeler une méthode select qui attend un paramètre d’entrée (par exemple `GetProductsByCategoryID(categoryID)`). Comme nous allons bientôt, les valeurs de ces `DeleteParameters`, `UpdateParameters`, et `InsertParameters` sont définies automatiquement par le GridView, DetailsView et FormView avant d’appeler l’ObjectDataSource `Insert()`, `Update()`, ou `Delete()` méthode. Ces valeurs peuvent également définies par programme en fonction des besoins, comme nous verrons dans un didacticiel futures.

Un effet secondaire de l’utilisation de l’Assistant Configuration pour ObjectDataSource est que Visual Studio définit les [propriété OldValuesParameterFormatString](https://msdn.microsoft.com/library/system.web.ui.webcontrols.objectdatasource.oldvaluesparameterformatstring(VS.80).aspx) à `original_{0}`. Valeur de cette propriété est utilisée pour inclure les valeurs d’origine des données en cours de modification et s’avère utile dans deux scénarios :

- Si, lorsque vous modifiez un enregistrement, les utilisateurs peuvent modifier la valeur de clé primaire. Dans ce cas, la nouvelle valeur de clé primaire et la valeur de clé primaire d’origine doivent être fournis afin que l’enregistrement avec la valeur de clé primaire d’origine peut être trouvée et afficher sa valeur mis à jour en conséquence.
- Lors de l’utilisation de l’accès concurrentiel optimiste. Optimiste d’accès concurrentiel est une technique pour vous assurer que deux utilisateurs simultanés ne pas écraser les modifications par un autre et la rubrique pour un didacticiel futures.

Le `OldValuesParameterFormatString` propriété indique le nom des paramètres d’entrée dans la mise à jour de l’objet sous-jacent et les méthodes de suppression pour les valeurs d’origine. Nous aborderons cette propriété et son objectif plus en détail lorsque nous Explorer l’accès concurrentiel optimiste. Je faire fonctionner maintenant, toutefois, étant donné que les méthodes de notre BLL n’attendent pas les valeurs d’origine et par conséquent, il est important que nous supprimer cette propriété. En laissant le `OldValuesParameterFormatString` propriété une valeur autre que celle par défaut (`{0}`) provoque une erreur lorsqu’un contrôle Web de données tente d’appeler l’ObjectDataSource `Update()` ou `Delete()` méthodes car ObjectDataSource Essayez de passer à la fois dans le `UpdateParameters` ou `DeleteParameters` spécifié, ainsi que les paramètres de valeur d’origine.

Si ce n’est pas très clair à partir de là, ne vous inquiétez pas, nous allons examiner cette propriété et son utilité dans un didacticiel futures. Pour l’instant, simplement être sûr de supprimer cette déclaration de propriété entièrement à partir de la syntaxe déclarative ou définissez la valeur sur la valeur par défaut ({0}).

> [!NOTE]
> Si vous désactivez simplement la `OldValuesParameterFormatString` valeur de propriété à partir de la fenêtre Propriétés en mode Design, la propriété existe toujours dans la syntaxe déclarative, mais être définie sur une chaîne vide. Cette opération, malheureusement, toujours entraîne le même problème que celui décrit ci-dessus. Par conséquent, supprimez la propriété complètement à partir de la syntaxe déclarative ou, à partir de la fenêtre Propriétés, la valeur par défaut, `{0}`.


## <a name="step-3-adding-a-data-web-control-and-configuring-it-for-data-modification"></a>Étape 3 : Ajout d’un contrôle Web de données et sa configuration pour la Modification des données

Une fois l’ObjectDataSource a été ajouté à la page et configuré, nous sommes prêts à ajouter des contrôles Web de données à la page pour afficher les données et fournissent un moyen pour l’utilisateur final pour la modifier. Nous allons examiner le GridView, DetailsView et FormView séparément, que ces contrôles Web de données diffèrent dans leur modification des données et la configuration.

Comme nous allons le voir dans le reste de cet article, ajout de modification très simple, l’insertion et la suppression de la prise en charge via le GridView, DetailsView, et contrôle du FormView est vraiment aussi simple que la vérification des deux cases à cocher. Il existe plusieurs subtilités et dans le monde réel, des cases de bord qui rendent ces fonctionnalités plus complexe que la simple pointer-cliquer. Ce didacticiel, toutefois, consacrée uniquement prouver une modification des données très simplifié. Didacticiels futures maintenant examiner les problèmes surviendront sans aucun doute dans un paramètre du monde réel.

## <a name="deleting-data-from-the-gridview"></a>Suppression de données dans le contrôle GridView

Commencez par faire glisser un contrôle GridView à partir de la boîte à outils vers le concepteur. Ensuite, liez ObjectDataSource au GridView en le sélectionnant dans la liste déroulante dans la balise active du GridView. À ce stade balisage déclaratif du GridView sera :


[!code-aspx[Main](an-overview-of-inserting-updating-and-deleting-data-vb/samples/sample4.aspx)]

Liaison de contrôle GridView à ObjectDataSource via sa balise active présente deux avantages :

- BoundFields et CheckBoxFields sont automatiquement créés pour chacun des champs retournés par ObjectDataSource. En outre, les propriétés de la BoundField et de CheckBoxField sont définies en fonction sur les métadonnées du champ sous-jacent. Par exemple, le `ProductID`, `CategoryName`, et `SupplierName` les champs sont marqués en lecture seule dans le `ProductsDataTable` et par conséquent ne doit pas être mis à jour lors de la modification. Pour prendre en compte cette, ces BoundFields [propriétés ReadOnly](https://msdn.microsoft.com/library/system.web.ui.webcontrols.boundfield.readonly(VS.80).aspx) ont la valeur `True`.
- Le [propriété DataKeyNames](https://msdn.microsoft.com/library/system.web.ui.webcontrols.gridview.datakeynames(VS.80).aspx) est attribué pour les champs de clé primaires de l’objet sous-jacent. Ceci est essentiel quand à l’aide de GridView pour modifier ou supprimer des données, car cette propriété indique que le champ (ou un ensemble de champs) unique qui identifie chaque enregistrement. Pour plus d’informations sur la `DataKeyNames` propriété, faire référence à la [maître/détail à l’aide d’un GridView maître avec un DetailView détail](../masterdetail/master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs.md) didacticiel.

Alors que le contrôle GridView peut être lié à ObjectDataSource via la fenêtre Propriétés ou de la syntaxe déclarative, cela vous oblige à ajouter manuellement le BoundField approprié et `DataKeyNames` balisage.

Le contrôle GridView prend en charge intégrée au niveau des lignes modification et suppression. Configuration d’un GridView pour prendre en charge la suppression ajoute une colonne de boutons de suppression. Lorsque l’utilisateur final clique sur le bouton Supprimer pour une ligne particulière, se poursuivent d’une publication (postback), le contrôle GridView effectue les étapes suivantes :

1. L’ObjectDataSource `DeleteParameters` ou les valeurs sont affectées.
2. L’ObjectDataSource `Delete()` méthode est appelée, la suppression de l’enregistrement spécifié
3. Le contrôle GridView se lie au ObjectDataSource en appelant son `Select()` (méthode)

Les valeurs assignées à la `DeleteParameters` sont les valeurs de la `DataKeyNames` champ (s) de la ligne dont bouton Supprimer. Par conséquent, il est essentiel que d’un GridView `DataKeyNames` propriété être définie correctement. S’il est manquant, le `DeleteParameters` sera attribué une valeur de `Nothing` à l’étape 1, qui à son tour ne provoque pas de tous les enregistrements supprimés à l’étape 2.

> [!NOTE]
> Le `DataKeys` collection est stockée dans l’état du contrôle GridView s, ce qui signifie que le `DataKeys` valeurs seront sauvegardées sur la publication (postback) même si l’état d’affichage GridView s a été désactivé. Toutefois, il est très important que l’état d’affichage reste activée pour les contrôles GridView qui prennent en charge la modification ou la suppression (le comportement par défaut). Si vous définissez le GridView s `EnableViewState` propriété `false`, la modification et suppression de comportement fonctionnent correctement pour un seul utilisateur, mais s’il existe des utilisateurs simultanés, la suppression des données, il est possible que ces utilisateurs simultanés peuvent accidentellement supprimer ou modifier des enregistrements qu’ils ne t envisagez. Voir mon billet de blog, [Avertissement : problème de concurrence avec ASP.NET 2.0 contrôles GridView/DetailsView/FormViews cette modification de la prise en charge et/ou la suppression et la dont l’état d’affichage est désactivé](http://scottonwriting.net/sowblog/posts/10054.aspx), pour plus d’informations.


Cet avertissement s’applique également aux DetailsViews et FormViews.

Pour ajouter des fonctionnalités de suppression à un GridView, simplement accéder à sa balise active et activez la case à cocher Activer la suppression.


![Vérifier la suppression de case à cocher Activer les](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image24.png)

**La figure 10**: vérifier la suppression de case à cocher Activer les


La vérification de la case à cocher Activer la suppression de la balise active d’ajoute un CommandField au GridView. Le CommandField restitue une colonne dans le GridView avec des boutons permettant d’effectuer une ou plusieurs des tâches suivantes : sélection d’un enregistrement, de modification d’un enregistrement et de suppression d’un enregistrement. Nous avons vu précédemment le CommandField en cours avec la sélection des enregistrements dans la [maître/détail à l’aide d’un GridView maître avec un DetailView détail](../masterdetail/master-detail-using-a-selectable-master-gridview-with-a-details-detailview-cs.md) didacticiel.

Le CommandField contient un nombre de `ShowXButton` propriétés qui indiquent quelles séries de boutons sont affichés dans le CommandField. En activant la case à cocher Activer la suppression un CommandField dont `ShowDeleteButton` propriété `True` a été ajouté à la collection de colonnes de GridView.


[!code-aspx[Main](an-overview-of-inserting-updating-and-deleting-data-vb/samples/sample5.aspx)]

À ce stade, que vous le croyez ou non, nous avons terminé à l’ajout de prise en charge de suppression pour le contrôle GridView ! Comme le montre la Figure 11, lorsque cette page via un navigateur, une colonne de boutons de suppression se trouve.


[![Le CommandField ajoute une colonne de boutons de suppression](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image26.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image25.png)

**Figure 11**: le CommandField ajoute une colonne de boutons Supprimer ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image27.png))


Si vous aviez créé ce didacticiel d’emblée à vous-même, lorsque vous testez cette page en cliquant sur le bouton Supprimer lève une exception. Continuez à lire pour en savoir plus sur pourquoi ces exceptions ont été levées et comment les résoudre.

> [!NOTE]
> Si vous programmez à l’aide du téléchargement qui accompagne ce didacticiel, ces problèmes ont déjà été pris en compte. Toutefois, je vous encourage à lire les détails ci-dessous pour aider à identifier les problèmes qui peuvent survenir et les solutions de contournement appropriées.


Si, lors de la tentative de suppression d’un produit, vous obtenez une exception dont le message est similaire à «*ObjectDataSource 'ObjectDataSource1' n’a pas trouvé une méthode non générique 'DeleteProduct' qui a des paramètres : productID, d’origine\_ ProductID*, « vous avez oublié probablement à supprimer la `OldValuesParameterFormatString` propriété ObjectDataSource. Avec la `OldValuesParameterFormatString` propriété spécifiée, ObjectDataSource tente de passer à la fois dans `productID` et `original_ProductID` d’entrée de paramètres pour le `DeleteProduct` (méthode). `DeleteProduct`, toutefois, il accepte uniquement un paramètre d’entrée unique, par conséquent, l’exception. Suppression de la `OldValuesParameterFormatString` propriété (ou la valeur `{0}`) fait en sorte que ObjectDataSource au lieu d’essayer de passer le paramètre d’entrée d’origine.


[![Assurez-vous que la propriété OldValuesParameterFormatString a été effacée](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image29.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image28.png)

**Figure 12**: Vérifiez que le `OldValuesParameterFormatString` propriété a été effacé Out ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image30.png))


Même si vous aviez supprimé le `OldValuesParameterFormatString` propriété, vous toujours obtenez une exception lors de la tentative de suppression d’un produit avec le message : «*l’instruction DELETE est en conflit avec la contrainte de référence ' FK\_commande\_détails \_Produits*. » La base de données Northwind contient une contrainte de clé étrangère entre les `Order Details` et `Products` table, ce qui signifie que qu’un produit ne peut pas être supprimé du système s’il existe un ou plusieurs enregistrements pour lui dans la `Order Details` table. Étant donné que chaque produit dans la base de données Northwind possède au moins un enregistrement `Order Details`, nous ne pouvons pas supprimer tous les produits jusqu'à ce que nous avons d’abord supprimer les enregistrements de détails de commande associée du produit.


[![Une contrainte Foreign Key empêche la suppression de produits](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image32.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image31.png)

**Figure 13**: une contrainte de clé étrangère interdit la suppression de produits ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image33.png))


Pour notre didacticiel, nous allons simplement supprimer tous les enregistrements à partir de la `Order Details` table. Dans une application réelle nous devez soit :

- Écran d’un autre pour gérer les informations de détails de commande
- Augmenter la `DeleteProduct` pour inclure la logique permettant de supprimer les détails de la commande du produit spécifié (méthode)
- Modifier la requête SQL utilisée par le TableAdapter pour inclure la suppression des détails de commande du produit spécifié

Nous allons simplement supprimer tous les enregistrements à partir de la `Order Details` table contourner les mesures et la contrainte de clé étrangère. Accédez à l’Explorateur de serveurs dans Visual Studio, avec le bouton droit sur le `NORTHWND.MDF` nœud, puis choisissez la nouvelle requête. Puis, dans la fenêtre de requête, exécutez l’instruction SQL suivante :`DELETE FROM [Order Details]`


[![Supprimer tous les enregistrements à partir de la Table Order Details](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image35.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image34.png)

**La figure 14**: supprimer tous les enregistrements à partir de la `Order Details` Table ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image36.png))


Après avoir effacé le `Order Details` table en cliquant sur le bouton de suppression va supprimer le produit sans erreur. Si vous cliquez sur le bouton de suppression ne supprime pas le produit, vérifiez que du GridView `DataKeyNames` est définie sur le champ de clé primaire (`ProductID`).

> [!NOTE]
> Lorsque vous cliquez sur le bouton Supprimer une publication (postback) s’ensuit et l’enregistrement est supprimé. Cela peut être dangereuse, car il est facile d’accidentellement cliquez sur le bouton de suppression de la ligne incorrect. Dans un didacticiel futur, nous verrons comment ajouter une confirmation côté client lors de la suppression d’un enregistrement.


## <a name="editing-data-with-the-gridview"></a>Modification des données avec le contrôle GridView

En même temps que la suppression, le contrôle GridView fournit également une prise en charge intégrée de modification au niveau des lignes. Configuration d’un GridView pour prendre en charge la modification ajoute une colonne de boutons de la modifier. Du point de vue de l’utilisateur final, en cliquant sur les causes du bouton Modifier d’une ligne ligne deviennent modifiables, activez les cellules dans les zones de texte qui contient les valeurs existantes et en remplaçant le bouton Modifier avec mise à jour et les boutons Annuler. Après avoir apporté les modifications souhaitées, l’utilisateur final peut cliquez sur le bouton de mise à jour pour valider les modifications ou le bouton Annuler pour les ignorer. Dans les deux cas, après avoir cliqué sur mise à jour ou annuler le GridView retourne à son état avant la modification.

À partir de notre perspective en tant que développeur de pages, lorsque l’utilisateur final clique sur le bouton Modifier pour une ligne particulière, une publication (postback) s’ensuit et le GridView effectue les étapes suivantes :

1. Le contrôle du GridView `EditItemIndex` est affectée à l’index de la ligne dont bouton Modifier
2. Le contrôle GridView se lie au ObjectDataSource en appelant son `Select()` (méthode)
3. L’index de ligne qui correspond à la `EditItemIndex` est rendu en mode d’édition «. » Dans ce mode, le bouton Modifier est remplacé par les boutons Annuler et de mise à jour et BoundFields dont `ReadOnly` propriétés ont la valeur False (valeur par défaut) sont rendus en tant que contrôles de zone de texte Web dont `Text` propriétés sont attribuées aux valeurs de champs de données.

À ce stade, le balisage est renvoyé au navigateur, permettant à l’utilisateur final d’apporter des modifications aux données de la ligne. Lorsque l’utilisateur clique sur le bouton de mise à jour, une publication (postback) se produit et le GridView effectue les étapes suivantes :

1. L’ObjectDataSource `UpdateParameters` ou les valeurs sont affectées les valeurs entrées par l’utilisateur final dans l’interface de modification de GridView
2. L’ObjectDataSource `Update()` méthode est appelée, la mise à jour de l’enregistrement spécifié
3. Le contrôle GridView se lie au ObjectDataSource en appelant son `Select()` (méthode)

Les valeurs de clé primaires affectées à la `UpdateParameters` à l’étape 1 provenir les valeurs spécifiées dans le `DataKeyNames` propriété, alors que les valeurs de clé non primaire sont fournis à partir du texte dans les contrôles Web de la zone de texte pour la ligne modifiée. Comme avec la suppression, il est essentiel que d’un GridView `DataKeyNames` propriété être définie correctement. S’il est manquant, le `UpdateParameters` valeur de clé primaire sera attribué une valeur de `Nothing` à l’étape 1, qui à son tour ne provoque pas de tous les enregistrements mis à jour à l’étape 2.

Fonctionnalités d’édition peuvent être activée en simplement en activant la case à cocher Activer la modification dans la balise active du GridView.


![Vérifiez l’activer la case à cocher de modification](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image37.png)

**Figure 15**: Vérifiez l’activer la case à cocher de modification


Vérification de la case à cocher Activer la modification ajoutera un CommandField (si nécessaire) et définissez son `ShowEditButton` propriété `True`. Comme nous l’avons vu précédemment, le CommandField contient un nombre de `ShowXButton` propriétés qui indiquent quelles séries de boutons sont affichés dans le CommandField. La vérification de la case à cocher Activer la modification ajoute le `ShowEditButton` propriété le CommandField existant :


[!code-aspx[Main](an-overview-of-inserting-updating-and-deleting-data-vb/samples/sample6.aspx)]

C’est tout existe à l’ajout d’une prise en charge édition rudimentaire. Comme le montre Figure16, l’interface de modification est plutôt brute chaque BoundField dont `ReadOnly` est définie sur `False` (la valeur par défaut) est restitué sous la forme d’une zone de texte. Cela inclut les champs comme `CategoryID` et `SupplierID`, qui sont des clés à d’autres tables.


[![Bouton de modification en cliquant sur Tran s affiche la ligne en Mode Édition](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image39.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image38.png)

**Figure 16**: en cliquant sur le Tran s bouton Modifier affiche la ligne en Mode Édition ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image40.png))


En plus de demander aux utilisateurs de modifier directement les valeurs de clé étrangère, interface de l’interface modification n’a pas de plusieurs manières :

- Si l’utilisateur entre un `CategoryID` ou `SupplierID` qui n’existe pas dans la base de données, le `UPDATE` enfreint une contrainte de clé étrangère, à l’origine du déclenchement d’une exception.
- Aucune validation n’inclut pas l’interface de modification. Si vous ne fournissez pas une valeur requise (tels que `ProductName`), ou entrez une valeur de chaîne dans laquelle une valeur numérique est attendue (par exemple, entrez « Trop ! » dans la `UnitPrice` zone de texte), une exception sera levée. Un didacticiel futures examine comment ajouter des contrôles de validation à l’interface utilisateur de modification.
- Actuellement, *tous les* des champs de produits qui ne sont pas en lecture seule doivent être inclus dans le GridView. Si nous avons pour supprimer un champ dans le GridView, par exemple `UnitPrice`, lors de la mise à jour les données de GridView définiriez pas le `UnitPrice` `UpdateParameters` valeur, qui pourrait être modifié l’enregistrement de base de données `UnitPrice` à un `NULL` valeur. De même, si un champ obligatoire, tel que `ProductName`, est supprimé du contrôle GridView, la mise à jour échoue avec le même «*colonne 'ProductName' n’autorise pas les valeurs null*« exception mentionnés ci-dessus.
- La mise en forme interface édition laisse beaucoup à désirer. Le `UnitPrice` est affiché avec quatre décimales. Dans l’idéal, le `CategoryID` et `SupplierID` valeurs contiendrait la compréhension des listes répertoriant les catégories et les fournisseurs dans le système.

Il s’agit de tous les défauts que nous allons pendant maintenant, mais sera résolu dans les didacticiels futures.

## <a name="inserting-editing-and-deleting-data-with-the-detailsview"></a>Insertion, de modification et de suppression des données avec le contrôle DetailsView

Comme nous l’avons vu dans les didacticiels antérieures, le contrôle DetailsView affiche un enregistrement à la fois et, comme le GridView, permet la modification et suppression de l’enregistrement actuellement affiché. Expérience à la fois de l’utilisateur final avec la modification et suppression d’éléments à partir d’un contrôle DetailsView et le flux de travail à partir de la partie d’ASP.NET est identique à celle du contrôle GridView. Où le contrôle DetailsView est différent du contrôle GridView est qu’il fournit également une prise en charge l’insertion intégrée.

Pour illustrer les fonctionnalités de modification de données du contrôle GridView, démarrez en ajoutant un contrôle DetailsView pour le `Basics.aspx` page au-dessus du GridView existant et la lier à ObjectDataSource existant via la balise active du DetailsView. Ensuite, désactivez hors du DetailsView `Height` et `Width` propriétés et activez l’option Activer la pagination de la balise active. Pour activer la modification, insertion et la suppression de la prise en charge, cochez simplement les cases à cocher Activer la modification, activer l’insertion et activer la suppression de la balise active.


![Configurer le contrôle DetailsView pour prendre en charge de modification, d’insertion et de suppression](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image41.png)

**Figure 17**: configurer le contrôle DetailsView pour prendre en charge de modification, d’insertion et de suppression


Comme avec le GridView, ajout de modification, insertion ou la suppression de prise en charge ajoute un CommandField au contrôle DetailsView, comme le montre la syntaxe déclarative suivant :


[!code-aspx[Main](an-overview-of-inserting-updating-and-deleting-data-vb/samples/sample7.aspx)]

Notez que pour le contrôle DetailsView le CommandField apparaît à la fin de la collection de colonnes par défaut. Étant donné que les champs de DetailsView sont rendus sous forme de lignes, le CommandField apparaît sous la forme d’une ligne à insérer, modifier et supprimer des boutons au bas de DetailsView.


[![Configurer le contrôle DetailsView pour prendre en charge de modification, d’insertion et de suppression](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image43.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image42.png)

**La figure 18**: configurer le contrôle DetailsView à la prise en charge de modification, insertion et de suppression ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image44.png))


En cliquant sur le bouton de suppression commence la même séquence d’événements que le contrôle GridView : une publication (postback) ; suivi par le contrôle DetailsView remplissage de son ObjectDataSource `DeleteParameters` selon la `DataKeyNames` ; les valeurs et s’est terminée avec un appel de ses ObjectDataSource `Delete()` (méthode), ce qui en fait supprime le produit à partir de la base de données. Modification dans le contrôle DetailsView fonctionne également de manière identique à celle du contrôle GridView.

Pour l’insertion, l’utilisateur final est présenté avec un nouveau bouton qui, lorsque vous cliquez dessus, restitue le contrôle DetailsView en « mode d’insertion ». Avec le « mode insertion » le bouton Nouveau est remplacé par les boutons Insérer et annuler et uniquement ces BoundFields dont `InsertVisible` est définie sur `True` (la valeur par défaut) sont affichés. Les champs de données identifiés en tant que champs de l’incrémentation automatique, telles que `ProductID`, ont leurs [InsertVisible propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.datacontrolfield.insertvisible(VS.80).aspx) définie sur `False` lors de la liaison du DetailsView à la source de données via la balise active.

Lors de la liaison d’une source de données à un contrôle DetailsView via la balise active, Visual Studio définit les `InsertVisible` propriété `False` uniquement pour les champs de l’incrémentation automatique. Les champs en lecture seule, tels que `CategoryName` et `SupplierName`, apparaît dans l’interface utilisateur de « mode insert », sauf si leur `InsertVisible` est explicitement définie sur `False`. Prenez un moment pour définir ces deux champs `InsertVisible` propriétés `False`, soit via la syntaxe déclarative de DetailsView ou modifier les champs lien dans la balise active. Figure 19 présente l’affectation du `InsertVisible` propriétés `False` en cliquant sur Modifier les champs lien.


[![Northwind Traders propose désormais Acme thé](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image46.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image45.png)

**Figure 19**: Northwind Traders maintenant offre Acme thé ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image47.png))


Après avoir défini la `InsertVisible` propriétés, d’afficher le `Basics.aspx` page dans un navigateur et cliquez sur le bouton Nouveau. Figure 20 montre le contrôle DetailsView lors de l’ajout d’une nouvelle boisson, thé Acme, pour notre ligne de produits.


[![Northwind Traders propose désormais Acme thé](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image49.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image48.png)

**Figure 20**: Northwind Traders maintenant offre Acme thé ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image50.png))


Après la saisie des détails pour Acme thé et en cliquant sur le bouton Insérer, s’ensuit une publication (postback) et le nouvel enregistrement est ajouté à la `Products` table de base de données. Étant donné que ce contrôle DetailsView répertorie les produits dans l’ordre d’apparition dans la table de base de données, nous devons de page au dernier produit afin de voir le nouveau produit.


[![Détails de Acme thé](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image52.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image51.png)

**Figure 21**: détails pour Acme thé ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image53.png))


> [!NOTE]
> Le contrôle du DetailsView [CurrentMode propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.detailsview.currentmode(VS.80).aspx) indique l’interface qui est affichée et peut prendre l’une des valeurs suivantes : `Edit`, `Insert`, ou `ReadOnly`. Le [DefaultMode propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.detailsview.defaultmode(VS.80).aspx) indique le mode de DetailsView retourne vers après une modification ou insérer est terminé et est utile pour afficher un contrôle DetailsView est définitivement en mode édition ou en mode insertion.


Le point et cliquez sur Insertion et la modification des fonctionnalités de DetailsView concernées par les mêmes limitations que le contrôle GridView : l’utilisateur doit entrer existant `CategoryID` et `SupplierID` valeurs à une zone de texte ; les n’a pas d’interface de logique de validation ; tous les les champs de produit qui n’autorisent pas `NULL` valeurs ou vous n’avez pas une valeur par défaut valeur spécifiée au niveau de la base de données doit être inclus dans l’interface de l’insertion et ainsi de suite.

Les techniques que nous allons examiner d’étendre et d’amélioration interface de modification du GridView dans les futures articles peuvent être appliquées à la modification du contrôle DetailsView et insertion également les interfaces.

## <a name="using-the-formview-for-a-more-flexible-data-modification-user-interface"></a>À l’aide du FormView pour une Interface utilisateur de Modification des données plus Flexible

Le FormView offre une prise en charge intégrée pour l’insertion, la modification et suppression de données, mais, car il utilise des modèles au lieu de champs il n’existe pas de place pour ajouter le BoundFields ou le CommandField utilisé par les contrôles GridView et DetailsView pour fournir les données interface de modification. Au lieu de cela, cette interface, les contrôles Web pour la collecte d’utilisateur d’entrée lors de l’ajout d’un nouvel élément ou de modification d’une existante, ainsi que de la créer, modifier, supprimer, Insert, Update et annuler les boutons doit être ajoutée manuellement aux modèles appropriés. Heureusement, Visual Studio crée automatiquement l’interface requise lors de la liaison du FormView à une source de données dans la liste déroulante dans sa balise active.

Pour illustrer ces techniques, démarrez en ajoutant un FormView à le `Basics.aspx` page et, à partir de la balise active du FormView, liez-le à ObjectDataSource déjà créé. Cette opération génère une `EditItemTemplate`, `InsertItemTemplate`, et `ItemTemplate` pour le contrôle FormView avec les contrôles Web de la zone de texte pour la collecte d’entrée et les contrôles Web de bouton de l’utilisateur pour le nouveau, modifier, supprimer, insérer, mettre à jour et annuler des boutons. En outre, le FormView `DataKeyNames` est définie sur le champ de clé primaire (`ProductID`) de l’objet retourné par ObjectDataSource. Enfin, vérifiez l’option Activer la pagination dans la balise active du FormView.

L’exemple suivant montre le balisage déclaratif pour FormView `ItemTemplate` après le FormView a été lié à ObjectDataSource. Par défaut, chaque champ de produit de valeur non booléenne est lié à la `Text` propriété d’un contrôle Web Label lors de chaque champ de valeur booléenne (`Discontinued`) est lié à la `Checked` propriété d’un contrôle de case à cocher Web désactivé. Pour les boutons Nouveau, modifier et supprimer déclencher certain comportement FormView quand vous cliquez dessus, il est impératif que leur `CommandName` valeurs valeur `New`, `Edit`, et `Delete`, respectivement.


[!code-aspx[Main](an-overview-of-inserting-updating-and-deleting-data-vb/samples/sample8.aspx)]

La figure 22 montre FormView `ItemTemplate` lors de l’affichage via un navigateur. Chaque champ de produit est répertorié avec les boutons Nouveau, modifier et supprimer du bas.


[![Par défaut FormView ItemTemplate répertorie chaque champ de produit, ainsi que de nouveaux, modifier et supprimer des boutons](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image55.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image54.png)

**La figure 22**: le défaut FormView `ItemTemplate` répertorie chaque produit champ sur un nouveau, modifier et supprimer des boutons ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image56.png))


Comme avec le GridView et DetailsView, en cliquant sur le bouton Supprimer ou n’importe quel bouton, LinkButton ou ImageButton dont `CommandName` propriété a la valeur Delete provoque une publication (postback), remplit l’ObjectDataSource `DeleteParameters` basé sur le FormView `DataKeyNames`valeur et appelle l’ObjectDataSource `Delete()` (méthode).

Lorsque vous cliquez sur le bouton Modifier se poursuivent d’une publication (postback), les données sont reliées à la `EditItemTemplate`, qui est responsable du rendu de l’interface de modification. Cette interface inclut les contrôles Web pour la modification des données, ainsi que les boutons Annuler et de mise à jour. La valeur par défaut `EditItemTemplate` généré par Visual Studio contient une étiquette pour tous les champs de l’incrémentation automatique (`ProductID`), une zone de texte pour chaque champ de valeur non booléenne et une case à cocher pour chaque champ de valeur booléenne. Ce comportement est très similaire à la BoundFields généré automatiquement dans les contrôles GridView et DetailsView.

> [!NOTE]
> Un problème de petits avec la génération FormView automatique de la `EditItemTemplate` est qu’il affiche Web de la zone de texte pour les champs qui sont en lecture seule, telles que les contrôles de `CategoryName` et `SupplierName`. Nous verrons comment tenir compte peu de temps.


Contrôle de la zone de texte dans le `EditItemTemplate` ont leur `Text` propriété liée à la valeur de leur champ de données correspondant à l’aide *liaison de données bidirectionnelle*. Liaison de données bidirectionnelle, dénoté par `<%# Bind("dataField") %>`, effectue la liaison de données à la fois lors de la liaison de données au modèle et lors du remplissage des paramètres de l’ObjectDataSource d’insertion ou modification d’enregistrements. Autrement dit, lorsque l’utilisateur clique sur le bouton Modifier à partir de la `ItemTemplate`, le `Bind()` méthode retourne la valeur de champ de données spécifié. Une fois que l’utilisateur apporte les modifications et clique sur la mise à jour, les valeurs publiée qui correspondent aux champs de données spécifiées à l’aide `Bind()` sont appliquées à l’ObjectDataSource `UpdateParameters`. Ou bien, liaison de données à sens unique, dénoté par `<%# Eval("dataField") %>`, uniquement récupère les valeurs de champ de données lors de la liaison de données au modèle et est *pas* retourner les valeurs entrées par l’utilisateur aux paramètres de la source de données de publication (postback).

Le balisage déclaratif suivant montre les FormView `EditItemTemplate`. Notez que la `Bind()` méthode est utilisée dans la syntaxe de la liaison de données et les contrôles de mise à jour et Web de bouton Annuler leur `CommandName` propriétés définies en conséquence.


[!code-aspx[Main](an-overview-of-inserting-updating-and-deleting-data-vb/samples/sample9.aspx)]

Notre `EditItemTemplate`, à ce point, provoquera une exception levée si nous tentons de l’utiliser. Le problème est que le `CategoryName` et `SupplierName` les champs sont rendus en tant que contrôles de zone de texte Web dans le `EditItemTemplate`. Nous devons soit modifier ces zones de texte pour les étiquettes ou les supprimer complètement. Nous allons simplement les supprimer complètement le `EditItemTemplate`.

Figure 23 illustre le FormView dans un navigateur après est effectué sur le bouton Modifier pour Tran. Notez que la `SupplierName` et `CategoryName` champs affichés dans le `ItemTemplate` ne sont plus présentes, car nous avons supprimé uniquement à partir du `EditItemTemplate`. Lorsque vous cliquez sur le bouton de mise à jour le FormView traversent la même séquence d’étapes que les contrôles GridView et DetailsView.


[![Par défaut, EditItemTemplate affiche chaque champ modifiable produit comme une zone de texte ou une case à cocher](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image58.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image57.png)

**Figure 23**: par défaut, le `EditItemTemplate` montre chaque produit champ modifiable en tant que zone de texte ou case à cocher ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image59.png))


Lorsque l’insertion bouton FormView `ItemTemplate` une publication (postback) s’ensuit. Toutefois, aucune donnée n’est liée à FormView, car un nouvel enregistrement est ajouté. Le `InsertItemTemplate` interface comprend les contrôles Web pour ajouter un nouvel enregistrement, ainsi que les boutons Insérer et Annuler. La valeur par défaut `InsertItemTemplate` généré par Visual Studio contient une zone de texte pour chaque champ de valeur non booléenne et une case à cocher pour chaque champ de valeur booléenne, semblable à générées automatiquement `EditItemTemplate`de l’interface. Les contrôles de zone de texte ont leur `Text` propriété liée à la valeur de leur champ de données correspondant à l’aide de la liaison de données bidirectionnelle.

Le balisage déclaratif suivant montre les FormView `InsertItemTemplate`. Notez que la `Bind()` méthode est utilisée dans la syntaxe de la liaison de données et les contrôles d’insertion et de Web de bouton Annuler leur `CommandName` propriétés définies en conséquence.


[!code-aspx[Main](an-overview-of-inserting-updating-and-deleting-data-vb/samples/sample10.aspx)]

Il existe plusieurs avec la génération FormView automatique de la `InsertItemTemplate`. Plus précisément, les contrôles de zone de texte Web sont créés même pour les champs qui sont en lecture seule, telles que `CategoryName` et `SupplierName`. Par exemple lors de la `EditItemTemplate`, vous devez supprimer ces zones de texte à partir de la `InsertItemTemplate`.

Figure 24 montre le FormView dans un navigateur lors de l’ajout d’un nouveau produit, Acme café. Notez que le `SupplierName` et `CategoryName` champs affichés dans le `ItemTemplate` ne sont plus présentes, que nous avons simplement les a supprimés. Clic sur le bouton Insérer le produit FormView via la même séquence d’étapes que le contrôle DetailsView, ajoutez un nouvel enregistrement pour la `Products` table. Figure 25 affiche les détails du produit Acme café dans le FormView a été inséré.


[![L’élément InsertItemTemplate détermine l’Interface d’insertion FormView](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image61.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image60.png)

**Figure 24**: le `InsertItemTemplate` détermine l’Interface d’insertion FormView ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image62.png))


[![Les détails pour le nouveau produit Acme café, sont affichés dans le contrôle FormView](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image64.png)](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image63.png)

**Figure 25**: détails pour le nouveau produit Acme café, s’affichent dans le FormView ([cliquez pour afficher l’image en taille réelle](an-overview-of-inserting-updating-and-deleting-data-vb/_static/image65.png))


En séparant en lecture seule, modification et l’insertion des interfaces dans les trois modèles distincts, le FormView permet un meilleur niveau de contrôle sur ces interfaces que le contrôle DetailsView et GridView.

> [!NOTE]
> Comme le contrôle DetailsView, le contrôle FormView `CurrentMode` propriété indique l’interface qui est affichée et son `DefaultMode` propriété indique le mode du FormView retourne à après une modification ou d’insertion est terminée.


## <a name="summary"></a>Récapitulatif

Dans ce didacticiel, nous avons examiné les principes fondamentaux de l’insertion, de modification et de suppression des données à l’aide du contrôle GridView, DetailsView et FormView. Les trois de ces contrôles permettent un certain degré de modification des données intégrées qui peuvent être utilisées sans écrire une seule ligne de code dans la page ASP.NET, les données des contrôles Web et ObjectDataSource. Toutefois, la simple point et cliquez sur rendu techniques relativement affaiblie naïve interface utilisateur de modification des données. Pour fournir la validation, injecter des valeurs par programme, gérer correctement les exceptions, personnaliser l’interface utilisateur, et ainsi de suite, nous allons devoir s’appuient sur une multitude de techniques qui seront abordés sur plusieurs didacticiels suivants.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

>[!div class="step-by-step"]
[Précédent](limiting-data-modification-functionality-based-on-the-user-cs.md)
[Suivant](examining-the-events-associated-with-inserting-updating-and-deleting-vb.md)
