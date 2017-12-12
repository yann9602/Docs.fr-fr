---
uid: web-forms/overview/data-access/editing-inserting-and-deleting-data/limiting-data-modification-functionality-based-on-the-user-cs
title: "Limitant les fonctionnalités de Modification de données basé sur l’utilisateur (c#) | Documents Microsoft"
author: rick-anderson
description: "Dans une application web qui permet aux utilisateurs de modifier des données, différents comptes d’utilisateurs peuvent avoir des privilèges de modification de données différents. Dans ce didacticiel, nous allons examiner comment t..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/17/2006
ms.topic: article
ms.assetid: 2b251c82-77cf-4e36-baa9-b648eddaa394
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/editing-inserting-and-deleting-data/limiting-data-modification-functionality-based-on-the-user-cs
msc.type: authoredcontent
ms.openlocfilehash: 44d0c192e082a7ad123096acb57fd053f6dcaeb1
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="limiting-data-modification-functionality-based-on-the-user-c"></a>Limitant les fonctionnalités de Modification de données en fonction de l’utilisateur (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_23_CS.exe) ou [télécharger le PDF](limiting-data-modification-functionality-based-on-the-user-cs/_static/datatutorial23cs1.pdf)

> Dans une application web qui permet aux utilisateurs de modifier des données, différents comptes d’utilisateurs peuvent avoir des privilèges de modification de données différents. Dans ce didacticiel, nous allons examiner comment ajuster dynamiquement les fonctionnalités de modification de données en fonction de l’utilisateur visite.


## <a name="introduction"></a>Introduction

Un nombre d’applications web prennent en charge les comptes d’utilisateur et fournit des différentes options, les rapports et les fonctionnalités basées sur l’utilisateur connecté. Par exemple, avec nos didacticiels souhaitions permettent aux utilisateurs des sociétés à ouvrir une session sur les site et mise à jour des informations générales sur leurs produits - leur nom et la quantité par unité, peut-être, ainsi que les informations du fournisseur, telles que leur nom de société, fournisseur adresse, les informations de la personne à contacter s et ainsi de suite. En outre, nous pouvons si vous le souhaitez inclure certains comptes d’utilisateur pour les personnes à partir de notre société afin qu’ils puissent se connecter et mettre à jour des informations sur les produits comme unités en stock, niveau de réapprovisionnement et ainsi de suite. Notre application web a également peut autoriser des utilisateurs anonymes à visiter (personnes n’êtes pas connecté), mais les limite à afficher uniquement les données. Avec un tel utilisateur compte système en place, nous voulons les données des contrôles Web dans nos pages ASP.NET permet d’offrir l’insertion, modification et suppression des privilèges appropriés pour l’utilisateur actuellement connecté.

Dans ce didacticiel, nous allons examiner comment ajuster dynamiquement les fonctionnalités de modification de données en fonction de l’utilisateur visite. En particulier, nous allons créer une page qui affiche les informations de fournisseurs dans un contrôle modifiable DetailsView avec un GridView qui répertorie les produits fournis par le fournisseur. Si l’utilisateur visite de la page est à partir de notre entreprise, ils peuvent : afficher des informations de fournisseur s ; modifier leur adresse ; et modifier les informations pour tous les produits fournis par le fournisseur. Si, toutefois, l’utilisateur est d’une société particulière, ils peuvent uniquement afficher et modifier leurs propres informations d’adresse et ne peuvent modifier leurs produits qui n’ont pas été marqués comme étant suspendus.


[![Un utilisateur à partir de notre société peut modifier les informations relatives aux fournisseurs s](limiting-data-modification-functionality-based-on-the-user-cs/_static/image2.png)](limiting-data-modification-functionality-based-on-the-user-cs/_static/image1.png)

**Figure 1**: un utilisateur à partir de notre société peut modifier n’importe quel fournisseur s informations ([cliquez pour afficher l’image en taille réelle](limiting-data-modification-functionality-based-on-the-user-cs/_static/image3.png))


[![Un utilisateur à partir d’un fournisseur donné peut uniquement afficher et les modifier leurs informations](limiting-data-modification-functionality-based-on-the-user-cs/_static/image5.png)](limiting-data-modification-functionality-based-on-the-user-cs/_static/image4.png)

**Figure 2**: un utilisateur à partir d’un particulier fournisseur peuvent uniquement afficher et modifier leurs informations ([cliquez pour afficher l’image en taille réelle](limiting-data-modification-functionality-based-on-the-user-cs/_static/image6.png))


Let s commencer !

> [!NOTE]
> ASP.NET 2.0 système d’appartenance s fournit une plateforme standardisée et extensible pour la création, la gestion et la validation des comptes d’utilisateur. Un examen du système d’appartenance étant dépasse le cadre de ces didacticiels, ce didacticiel à la place « fakes » l’appartenance en autorisant les visiteurs anonymes à choisir s’ils sont d’un fournisseur particulier ou à partir de notre entreprise. Pour plus d’informations sur l’appartenance, reportez-vous à mon [examen ASP.NET 2.0 s l’appartenance, rôles et profil](http://aspnet.4guysfromrolla.com/articles/120705-1.aspx) série d’articles.


## <a name="step-1-allowing-the-user-to-specify-their-access-rights"></a>Étape 1 : Permettant à l’utilisateur de spécifier leurs droits d’accès

Dans une application web du monde réel, les informations de compte d’utilisateur s inclurait si tout a fonctionné pour notre société ou d’un fournisseur particulier, et ces informations peuvent être accessibles par programme à partir de nos pages ASP.NET, une fois que l’utilisateur a ouvert une session le site. Cette information peut être capturée via le système de rôles ASP.NET 2.0 s, en tant qu’informations de compte au niveau de l’utilisateur via le système de profil, ou par des moyens personnalisés.

Étant donné que l’objectif de ce didacticiel est d’illustrer l’ajustement de la modification des données en fonction de l’utilisateur connecté et n’est pas destiné à l’appartenance de s showcase ASP.NET 2.0, les rôles et les systèmes de profil, nous allons utiliser un mécanisme très simple pour déterminer le fonctionnalités de l’utilisateur sur la page - DropDownList à partir de laquelle l’utilisateur peut indiquer si elles doivent être en mesure de les afficher et modifier les informations des fournisseurs ou, vous pouvez également ce que les informations de fournisseur particulier s’ils peuvent afficher et modifier. Si l’utilisateur indique qu’elle peut afficher et modifier toutes les informations de fournisseur (la valeur par défaut), elle peut parcourir tous les fournisseurs, modifiez toutes les informations d’adresse fournisseur s et modifier le nom et la quantité par unité pour tous les produits fournis par le fournisseur sélectionné. Si l’utilisateur indique qu’elle peut uniquement afficher et modifier un fournisseur particulier, toutefois, puis elle peut uniquement afficher les détails et les produits pour qu’un fournisseur et peut uniquement mettre à jour le nom et la quantité par les informations d’unité pour les produits qui sont *pas* abandonné.

Notre première étape de ce didacticiel, est ensuite, pour créer cette DropDownList et le remplir avec les fournisseurs dans le système. Ouvrez le `UserLevelAccess.aspx` page dans le `EditInsertDelete` dossier, ajouter une liste déroulante dont `ID` est définie sur `Suppliers`et lier cette DropDownList pour un nouveau ObjectDataSource nommé `AllSuppliersDataSource`.


[![Créer un nouveau ObjectDataSource nommé AllSuppliersDataSource](limiting-data-modification-functionality-based-on-the-user-cs/_static/image8.png)](limiting-data-modification-functionality-based-on-the-user-cs/_static/image7.png)

**Figure 3**: créer un nouveau nommé de ObjectDataSource `AllSuppliersDataSource` ([cliquez pour afficher l’image en taille réelle](limiting-data-modification-functionality-based-on-the-user-cs/_static/image9.png))


Étant donné que nous voulons que ce DropDownList pour inclure tous les fournisseurs, configurer ObjectDataSource pour appeler le `SuppliersBLL` classe s `GetSuppliers()` (méthode). Vérifiez que le s ObjectDataSource `Update()` méthode mappée à la `SuppliersBLL` classe s `UpdateSupplierAddress` (méthode), comme cette ObjectDataSource sera également être utilisé par le contrôle DetailsView à l’étape 2, nous allons ajouter.

Après avoir terminé l’Assistant ObjectDataSource, effectuez les étapes en configurant le `Suppliers` DropDownList tel qu’il indique le `CompanyName` champ de données et utilise le `SupplierID` comme valeur pour chaque champ des données `ListItem`.


[![Configurer les fournisseurs DropDownList pour utiliser les champs de données de SupplierID CompanyName](limiting-data-modification-functionality-based-on-the-user-cs/_static/image11.png)](limiting-data-modification-functionality-based-on-the-user-cs/_static/image10.png)

**Figure 4**: configurer le `Suppliers` DropDownList à utiliser le `CompanyName` et `SupplierID` des champs de données ([cliquez pour afficher l’image en taille réelle](limiting-data-modification-functionality-based-on-the-user-cs/_static/image12.png))


À ce stade, DropDownList répertorie les noms de société des fournisseurs dans la base de données. Toutefois, nous devons également inclure une option « Afficher/modifier tous les fournisseurs » pour DropDownList. Pour ce faire, affectez le `Suppliers` DropDownList s `AppendDataBoundItems` propriété `true` , puis ajoutez un `ListItem` dont `Text` propriété est « Afficher/modifier tous les fournisseurs » et dont la valeur est `-1`. Il peut être ajouté directement via le balisage déclaratif ou via le concepteur en accédant à la fenêtre Propriétés en cliquant sur le bouton de sélection dans le s DropDownList `Items` propriété.

> [!NOTE]
> Faire référence à la [ *filtrage de maître/détail avec un DropDownList* ](../masterdetail/master-detail-filtering-with-a-dropdownlist-cs.md) didacticiel pour plus d’informations sur l’ajout d’un élément Select All à un lié aux données DropDownList.


Après le `AppendDataBoundItems` propriété a été définie et la `ListItem` ajouté, le balisage déclaratif de s DropDownList doit ressembler à :


[!code-aspx[Main](limiting-data-modification-functionality-based-on-the-user-cs/samples/sample1.aspx)]

La figure 5 illustre une capture d’écran de notre progression en cours, lorsqu’ils sont affichés via un navigateur.


[![DropDownList fournisseurs contient un diaporama ListItem tous, Plus un pour chaque fournisseur](limiting-data-modification-functionality-based-on-the-user-cs/_static/image14.png)](limiting-data-modification-functionality-based-on-the-user-cs/_static/image13.png)

**Figure 5**: le `Suppliers` DropDownList contient afficher tout `ListItem`, Plus un pour chaque fournisseur ([cliquez pour afficher l’image en taille réelle](limiting-data-modification-functionality-based-on-the-user-cs/_static/image15.png))


Étant donné que vous souhaitez mettre à jour l’interface utilisateur immédiatement après que l’utilisateur a changé sa sélection, définir le `Suppliers` DropDownList s `AutoPostBack` propriété `true`. À l’étape 2, nous allons créer un contrôle DetailsView qui affiche les informations pour les fournisseurs en fonction de la sélection DropDownList. Puis, à l’étape 3, nous allons créer un gestionnaire d’événements pour ce s DropDownList `SelectedIndexChanged` événement, dans lequel nous allons ajouter le code qui lie les informations de fournisseur approprié pour le contrôle DetailsView basé sur le fournisseur sélectionné.

## <a name="step-2-adding-a-detailsview-control"></a>Étape 2 : Ajout d’un contrôle DetailsView

Permettent d’utiliser un contrôle DetailsView pour afficher les informations de fournisseur s. Pour l’utilisateur qui peut afficher et modifier tous les fournisseurs, le contrôle DetailsView prendra en charge la pagination, permettant à l’utilisateur de parcourir la fournisseur d’informations d’un enregistrement à la fois. Si l’utilisateur travaille pour un fournisseur particulier, toutefois, le contrôle DetailsView affiche uniquement ce fournisseur des informations sur s et n’inclut pas une interface de pagination. Dans les deux cas, le contrôle DetailsView doit autoriser l’utilisateur à modifier le fournisseur s’adresse, ville et des champs de pays.

Ajouter un contrôle DetailsView à la page sous le `Suppliers` DropDownList, définissez son `ID` propriété `SupplierDetails`, puis liez-le à le `AllSuppliersDataSource` ObjectDataSource créé à l’étape précédente. Ensuite, vérifiez les cases à cocher Activer la pagination et activer la modification de la balise active de DetailsView s.

> [!NOTE]
> Si vous ne pas voir une option Activer la modification dans le s DetailsView Active marquer s, car vous n’avez pas mappé les s ObjectDataSource `Update()` méthode à la `SuppliersBLL` classe s `UpdateSupplierAddress` (méthode). Prenez un moment pour revenir en arrière et modifier cette configuration, après laquelle l’option Activer la modification doit apparaître dans la balise active de DetailsView s.


Étant donné que la `SuppliersBLL` classe s `UpdateSupplierAddress` méthode n’accepte quatre paramètres - `supplierID`, `address`, `city`, et `country` -modifier le s DetailsView BoundFields afin que le `CompanyName` et `Phone` BoundFields sont en lecture seule. En outre, supprimez le `SupplierID` BoundField complètement. Enfin, le `AllSuppliersDataSource` ObjectDataSource a actuellement son `OldValuesParameterFormatString` propriété `original_{0}`. Prenez un moment pour supprimer ce paramètre de propriété complètement de la syntaxe déclarative ou affectez-lui la valeur par défaut, `{0}`.

Après avoir configuré le `SupplierDetails` DetailsView et `AllSuppliersDataSource` ObjectDataSource, nous devons le balisage déclaratif suivant :


[!code-aspx[Main](limiting-data-modification-functionality-based-on-the-user-cs/samples/sample2.aspx)]

À ce stade, le contrôle DetailsView peut être contacté par le biais, et les informations d’adresse fournisseur sélectionné s peuvent être mis à jour, quelle que soit la sélection effectuée dans le `Suppliers` DropDownList (voir Figure 6).


[![Les fournisseurs d’informations peuvent être affichées, et son adresse mise à jour](limiting-data-modification-functionality-based-on-the-user-cs/_static/image17.png)](limiting-data-modification-functionality-based-on-the-user-cs/_static/image16.png)

**Figure 6**: Any fournisseurs informations peuvent être consultées et son adresse mise à jour ([cliquez pour afficher l’image en taille réelle](limiting-data-modification-functionality-based-on-the-user-cs/_static/image18.png))


## <a name="step-3-displaying-only-the-selected-supplier-s-information"></a>Étape 3 : Afficher uniquement les informations de s fournisseur sélectionné

Actuellement, notre page affiche les informations pour tous les fournisseurs, indépendamment de si un fournisseur a été sélectionné à partir de la `Suppliers` DropDownList. Afin d’afficher uniquement les informations de fournisseur pour le fournisseur sélectionné, nous devons ajouter un autre ObjectDataSource à notre page, qui Récupère des informations sur un fournisseur particulier.

Ajouter un nouveau ObjectDataSource à la page, en le nommant `SingleSupplierDataSource`. À partir de sa balise active, cliquez sur le lien configurer la Source de données et il avoir à utiliser le `SuppliersBLL` classe s `GetSupplierBySupplierID(supplierID)` (méthode). Comme avec la `AllSuppliersDataSource` ObjectDataSource, ont le `SingleSupplierDataSource` ObjectDataSource s `Update()` méthode mappée à la `SuppliersBLL` classe s `UpdateSupplierAddress` (méthode).


[![Configurer pour utiliser la méthode GetSupplierBySupplierID(supplierID) SingleSupplierDataSource ObjectDataSource](limiting-data-modification-functionality-based-on-the-user-cs/_static/image20.png)](limiting-data-modification-functionality-based-on-the-user-cs/_static/image19.png)

**Figure 7**: configurer le `SingleSupplierDataSource` ObjectDataSource à utiliser le `GetSupplierBySupplierID(supplierID)` (méthode) ([cliquez pour afficher l’image en taille réelle](limiting-data-modification-functionality-based-on-the-user-cs/_static/image21.png))


Ensuite, nous re invité à spécifier le paramètre source pour le `GetSupplierBySupplierID(supplierID)` méthode s `supplierID` paramètre d’entrée. Étant donné que vous voulez afficher les informations pour le fournisseur sélectionné dans la liste déroulante, utilisez la `Suppliers` DropDownList s `SelectedValue` propriété en tant que le paramètre source.


[![Utiliser des fournisseurs DropDownList en tant que Source du paramètre supplierID](limiting-data-modification-functionality-based-on-the-user-cs/_static/image23.png)](limiting-data-modification-functionality-based-on-the-user-cs/_static/image22.png)

**Figure 8**: utilisez le `Suppliers` DropDownList en tant que le `supplierID` Source du paramètre ([cliquez pour afficher l’image en taille réelle](limiting-data-modification-functionality-based-on-the-user-cs/_static/image24.png))


Même avec cette ObjectDataSource deuxième ajouté, le contrôle DetailsView est actuellement configuré pour toujours utiliser le `AllSuppliersDataSource` ObjectDataSource. Nous devons ajouter la logique pour ajuster la source de données utilisée par le contrôle DetailsView selon le `Suppliers` DropDownList élément sélectionné. Pour ce faire, créez un `SelectedIndexChanged` Gestionnaire d’événements pour les fournisseurs DropDownList. Il est possible que celle-ci peut être plus facilement créée en double-cliquant sur DropDownList dans le concepteur. Ce gestionnaire d’événements doit déterminer quelle source de données à utiliser et doit lier de nouveau les données au contrôle DetailsView. Cela est accompli par le code suivant :


[!code-csharp[Main](limiting-data-modification-functionality-based-on-the-user-cs/samples/sample3.cs)]

Le Gestionnaire d’événements commence en déterminant si l’option « Afficher/modifier tous les fournisseurs » a été sélectionnée. S’il est, elle définit le `SupplierDetails` DetailsView s `DataSourceID` à `AllSuppliersDataSource` et renvoie l’utilisateur vers le premier enregistrement dans le jeu de fournisseurs en définissant le `PageIndex` 0 à la propriété. Si, toutefois, l’utilisateur a sélectionné un fournisseur dans la liste déroulante, le contrôle DetailsView s `DataSourceID` est affectée à `SingleSuppliersDataSource`. Quel que soit les données source est utilisée, le `SuppliersDetails` mode est restauré vers le mode lecture seule et les données sont reliées au contrôle DetailsView par un appel à la `SuppliersDetails` contrôle s `DataBind()` (méthode).

Avec ce gestionnaire d’événements en place, le contrôle DetailsView affiche maintenant le fournisseur sélectionné, sauf si l’option « Afficher/modifier tous les fournisseurs » a été sélectionnée, auquel cas des fournisseurs peuvent être consultés via l’interface de pagination. La figure 9 illustre la page avec l’option « Afficher/modifier tous les fournisseurs » sélectionnée ; Notez que l’interface de la pagination est présent, permettant à l’utilisateur à consulter et mettre à jour n’importe quel fournisseur. La figure 10 illustre la page avec le fournisseur de Ma Maison sélectionné. Seules les informations de s Ma Maison sont visibles et modifiables dans ce cas.


[![Toutes les informations de fournisseurs peuvent être affichées et modifiées](limiting-data-modification-functionality-based-on-the-user-cs/_static/image26.png)](limiting-data-modification-functionality-based-on-the-user-cs/_static/image25.png)

**Figure 9**: tous les fournisseurs d’informations peuvent être affichées et modifiées ([cliquez pour afficher l’image en taille réelle](limiting-data-modification-functionality-based-on-the-user-cs/_static/image27.png))


[![Seules les informations du fournisseur sélectionné s peuvent être affichées et modifiées](limiting-data-modification-functionality-based-on-the-user-cs/_static/image29.png)](limiting-data-modification-functionality-based-on-the-user-cs/_static/image28.png)

**La figure 10**: seules les opérations de mappage sélectionné un fournisseur d’informations peuvent être affichage et modifiée ([cliquez pour afficher l’image en taille réelle](limiting-data-modification-functionality-based-on-the-user-cs/_static/image30.png))


> [!NOTE]
> Pour ce didacticiel, le contrôle DetailsView et DropDownList contrôlent s `EnableViewState` doit avoir la valeur `true` (la valeur par défaut), car le s DropDownList `SelectedIndex` et le contrôle DetailsView `DataSourceID` modifications apportées aux propriétés s doit être mémorisées entre publications (postback).


## <a name="step-4-listing-the-suppliers-products-in-an-editable-gridview"></a>Étape 4 : Répertorier les fournisseurs de produits dans un GridView modifiable

Avec le contrôle DetailsView terminé, l’étape suivante est d’inclure un GridView modifiable qui répertorie les produits fournis par le fournisseur sélectionné. Ce contrôle GridView doit autoriser les modifications aux seuls le `ProductName` et `QuantityPerUnit` champs. En outre, si l’utilisateur sur la page provient d’un fournisseur particulier, il ne doit autoriser les mises à jour sur les produits qui sont *pas* abandonné. Pour y parvenir nous devez tout d’abord ajouter une surcharge de la `ProductsBLL` classe s `UpdateProducts` méthode qui accepte uniquement le `ProductID`, `ProductName`, et `QuantityPerUnit` champs en tant qu’entrées. Nous ve à ce processus pas au préalable dans nombreux didacticiels, laissez donc s seulement rechercher ici, le code qui doit être ajouté à `ProductsBLL`:


[!code-csharp[Main](limiting-data-modification-functionality-based-on-the-user-cs/samples/sample4.cs)]

Avec cette surcharge créée, nous re prêt à ajouter le contrôle GridView et son ObjectDataSource associé. Ajouter un nouveau GridView à la page, définissez son `ID` propriété `ProductsBySupplier`et le configurer pour utiliser un nouveau ObjectDataSource nommé `ProductsBySupplierDataSource`. Étant donné que nous voulons que ce contrôle GridView à répertorier les produits par le fournisseur sélectionné, utilisez le `ProductsBLL` classe s `GetProductsBySupplierID(supplierID)` (méthode). Également mapper les `Update()` (méthode) vers le nouveau `UpdateProduct` surcharge que nous venons de créer.


[![Configurer l’ObjectDataSource pour utiliser la surcharge UpdateProduct venez de créer](limiting-data-modification-functionality-based-on-the-user-cs/_static/image32.png)](limiting-data-modification-functionality-based-on-the-user-cs/_static/image31.png)

**Figure 11**: configurer l’ObjectDataSource à utiliser le `UpdateProduct` surcharge venez de créer ([cliquez pour afficher l’image en taille réelle](limiting-data-modification-functionality-based-on-the-user-cs/_static/image33.png))


Nous re vous y êtes invité à sélectionner la source de paramètre pour le `GetProductsBySupplierID(supplierID)` méthode s `supplierID` paramètre d’entrée. Étant donné que nous voulons afficher les produits pour le fournisseur sélectionné dans le contrôle DetailsView, utilisez le `SuppliersDetails` le contrôle DetailsView s `SelectedValue` propriété en tant que le paramètre source.


[![Utilisez la propriété SelectedValue de s SuppliersDetails DetailsView comme Source du paramètre](limiting-data-modification-functionality-based-on-the-user-cs/_static/image35.png)](limiting-data-modification-functionality-based-on-the-user-cs/_static/image34.png)

**Figure 12**: utilisez le `SuppliersDetails` DetailsView s `SelectedValue` propriété comme Source du paramètre ([cliquez pour afficher l’image en taille réelle](limiting-data-modification-functionality-based-on-the-user-cs/_static/image36.png))


Retour au GridView, supprimez tous les champs à l’exception de GridView `ProductName`, `QuantityPerUnit`, et `Discontinued`, marquage le `Discontinued` CheckBoxField en lecture seule. Vérifiez également l’option Activer la modification de la balise active de GridView s. Après ont apporté ces modifications, le balisage déclaratif pour le contrôle GridView et ObjectDataSource doit ressembler à ce qui suit :


[!code-aspx[Main](limiting-data-modification-functionality-based-on-the-user-cs/samples/sample5.aspx)]

Comme avec notre ObjectDataSources précédente, ce un s `OldValuesParameterFormatString` est définie sur `original_{0}`, ce qui entraîne des problèmes lorsque vous tentez de mettre à jour un nom de produit s ou de la quantité par unité. Supprimez cette propriété à partir de la syntaxe déclarative entièrement ou lui affectez la valeur par défaut, `{0}`.

Avec cette configuration terminée, notre page répertorie maintenant les produits fournis par le fournisseur sélectionné dans le contrôle GridView (voir la Figure 13). Actuellement *tout* quantité par unité ou nom de produit s peut être mis à jour. Toutefois, nous devons mettre à jour notre logique de page afin que cette fonctionnalité est interdite pour les produits supprimées pour les utilisateurs associés à un fournisseur particulier. Nous allons aborder cette dernière partie à l’étape 5.


[![Les produits fournis par le fournisseur sélectionné sont affichés.](limiting-data-modification-functionality-based-on-the-user-cs/_static/image38.png)](limiting-data-modification-functionality-based-on-the-user-cs/_static/image37.png)

**Figure 13**: les produits fournis par le fournisseur sélectionné sont affichés ([cliquez pour afficher l’image en taille réelle](limiting-data-modification-functionality-based-on-the-user-cs/_static/image39.png))


> [!NOTE]
> Avec l’ajout de ce contrôle GridView modifiable le `Suppliers` DropDownList s `SelectedIndexChanged` Gestionnaire d’événements doit être mis à jour pour retourner le contrôle GridView à l’état en lecture seule. Sinon, si un autre fournisseur est sélectionné au milieu de la modification des informations de produit, l’index correspondant dans le GridView pour le nouveau fournisseur est également modifiables. Pour éviter ce problème, définissez simplement le GridView s `EditIndex` propriété `-1` dans le `SelectedIndexChanged` Gestionnaire d’événements.


En outre, rappelez-vous qu’il est important que le contrôle GridView s vue état activé (comportement par défaut). Si vous définissez le GridView s `EnableViewState` propriété `false`, vous courez le risque d’avoir des utilisateurs simultanés involontairement supprimer ou de modifier des enregistrements. Consultez [Avertissement : problème de concurrence avec ASP.NET 2.0 contrôles GridView/DetailsView/FormViews cette modification de la prise en charge et/ou la suppression et la dont l’état d’affichage est désactivé](http://scottonwriting.net/sowblog/posts/10054.aspx) pour plus d’informations.

## <a name="step-5-disallow-editing-for-discontinued-products-when-showedit-all-suppliers-is-not-selected"></a>Étape 5 : Interdire la modification pour abandonné produits quand afficher/modifier tous les fournisseurs n’est pas sélectionnée

Alors que le `ProductsBySupplier` GridView est entièrement fonctionnelle, il actuellement accorde un trop grand nombre d’accès aux utilisateurs d’un fournisseur particulier. Par ses règles d’entreprise, ces utilisateurs ne doivent pas être en mesure de mettre à jour des produits abandonnés. Pour appliquer cette recommandation, nous pouvons masquer (ou désactiver) le bouton Modifier dans les lignes de GridView avec les produits supprimées lorsque la page est en cours visitée par un utilisateur à partir d’un fournisseur.

Créer un gestionnaire d’événements pour le contrôle GridView s `RowDataBound` événement. Dans ce gestionnaire d’événements, nous devons déterminer si l’utilisateur est associé à un fournisseur, qui, pour ce didacticiel, peut être déterminé en vérifiant le s fournisseurs DropDownList `SelectedValue` propriété - si elle s quelque chose d’autre que -1, l’utilisateur est associé à un fournisseur particulier. Dans ce cas, nous devons déterminer si le produit n’est plus disponible. Nous pouvons saisir une référence à l’élément réel `ProductRow` instance liée à la ligne GridView via la `e.Row.DataItem` propriété, comme indiqué dans le [ *affichant des informations de résumé dans le pied de page/s GridView* ](../custom-formatting/displaying-summary-information-in-the-gridview-s-footer-cs.md) didacticiel. Si le produit n’est plus disponible, nous pouvons saisir une référence de programmation pour le bouton Modifier dans le s GridView CommandField utilisant les techniques présentées dans le didacticiel précédent, [ *Ajout côté Client Confirmation lors de la suppression de* ](adding-client-side-confirmation-when-deleting-cs.md). Une fois que nous avons une référence, nous pouvons ensuite masquer ou désactiver le bouton.


[!code-csharp[Main](limiting-data-modification-functionality-based-on-the-user-cs/samples/sample6.cs)]

Avec cet événement gestionnaire en place, lorsque vous visitez cette page en tant qu’utilisateur auprès d’un fournisseur spécifique les produits qui sont supprimés ne sont pas modifiables, comme le bouton Modifier est masquée pour ces produits. Par exemple, Chef Anton s Gumbo Mix est un produit supprimée pour le fournisseur New Orleans Cajun Delights. Lorsque vous visitez la page pour ce fournisseur, le bouton Modifier pour ce produit est masqué (voir Figure 14). Toutefois, lors de la visite à l’aide de la « afficher/modifier tous les fournisseurs », le bouton Modifier est disponible (voir Figure 15).


[![Pour les utilisateurs de fournisseur spécifique, le bouton Modifier pour Chef Anton s Gumbo Mix est masqué](limiting-data-modification-functionality-based-on-the-user-cs/_static/image41.png)](limiting-data-modification-functionality-based-on-the-user-cs/_static/image40.png)

**La figure 14**: des utilisateurs de fournisseurs le bouton Modifier pour Chef Anton s Gumbo Mix est masqué ([cliquez pour afficher l’image en taille réelle](limiting-data-modification-functionality-based-on-the-user-cs/_static/image42.png))


[![Pour afficher/modifier tous les fournisseurs s’affiche le bouton Modifier pour Chef Anton s Gumbo Mix](limiting-data-modification-functionality-based-on-the-user-cs/_static/image44.png)](limiting-data-modification-functionality-based-on-the-user-cs/_static/image43.png)

**Figure 15**: pour afficher/modifier tous les fournisseurs les utilisateurs, le bouton Modifier pour s Chef Anton Gumbo Mix s’affiche ([cliquez pour afficher l’image en taille réelle](limiting-data-modification-functionality-based-on-the-user-cs/_static/image45.png))


## <a name="checking-for-access-rights-in-the-business-logic-layer"></a>La vérification des droits d’accès de la couche de logique métier

Dans ce didacticiel ASP.NET page gère toute la logique en ce qui concerne les informations que l’utilisateur peut voir et les produits qu’il peut mettre à jour. Dans l’idéal, cette logique serait également présente au niveau de la couche de logique métier. Par exemple, le `SuppliersBLL` classe s `GetSuppliers()` (méthode) (qui retourne tous les fournisseurs) peut inclure une vérification pour vous assurer que l’utilisateur actuellement connecté est *pas* associé à un fournisseur spécifique. De même, la `UpdateSupplierAddress` méthode peut inclure une vérification pour vous assurer que l’utilisateur actuellement connecté, soit travaillé pour notre société (et par conséquent peut mettre à jour toutes les informations d’adresse de fournisseurs) ou est associé avec le fournisseur dont les données sont en cours de mise à jour.

Je n’incluait pas ces contrôles de la couche BLL ici, car notre didacticiel les droits d’utilisateur s sont déterminées par une liste déroulante dans la page, les classes de la couche BLL ne peut pas accéder. Lorsque vous utilisez le système d’appartenance ou un des schémas d’authentification hors à l’emploi fournies par ASP.NET (par exemple, l’authentification Windows), actuellement connecté sur utilisateur s plus d’informations et des informations sur les rôles sont accessibles à partir de la couche BLL, rendant ce type d’accès droits vérifie possible à la présentation et les couches de la couche BLL.

## <a name="summary"></a>Résumé

La plupart des sites qui fournissent des comptes d’utilisateur doivent personnaliser l’interface de modification de données en fonction de l’utilisateur connecté. Les utilisateurs administratifs peuvent être en mesure de supprimer et de modifier n’importe quel enregistrement, alors que les utilisateurs non-administrateurs peuvent être limités à uniquement la mise à jour ou supprimer des enregistrements, ils ont créés eux-mêmes. Quel que soit le scénario peut être, les contrôles Web, ObjectDataSource, données et classes de la couche de logique métier peuvent être étendues pour ajouter ou de refus de certaines fonctionnalités en fonction de l’utilisateur connecté. Dans ce didacticiel, nous avons vu comment limiter les données visibles et modifiables selon si l’utilisateur a été associé à un fournisseur particulier ou si tout a fonctionné pour notre société.

Ce didacticiel conclut notre examen d’insertion, de mise à jour et de suppression des données à l’aide de contrôles GridView, DetailsView et FormView. À partir de l’étape suivante du didacticiel, nous allons notre attention à l’ajout de la pagination et le tri de prise en charge.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

>[!div class="step-by-step"]
[Précédent](adding-client-side-confirmation-when-deleting-cs.md)
[Suivant](an-overview-of-inserting-updating-and-deleting-data-vb.md)
