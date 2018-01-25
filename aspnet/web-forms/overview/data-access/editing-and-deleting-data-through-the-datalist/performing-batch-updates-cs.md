---
uid: web-forms/overview/data-access/editing-and-deleting-data-through-the-datalist/performing-batch-updates-cs
title: "Mise à jour de lot (c#) | Documents Microsoft"
author: rick-anderson
description: "Apprendre à créer un entièrement modifiable DataList tous ses éléments où se trouvent dans modifier le mode et dont les valeurs peuvent être enregistrées en cliquant sur un bouton « Tout mettre à jour » dans le..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/30/2006
ms.topic: article
ms.assetid: 57743ca7-5695-4e07-aed1-44b297f245a9
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/editing-and-deleting-data-through-the-datalist/performing-batch-updates-cs
msc.type: authoredcontent
ms.openlocfilehash: 46db3c5d733b9c8b6e749a9b8ff1aa9a061c36df
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="performing-batch-updates-c"></a>Mise à jour de lot (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_37_CS.exe) ou [télécharger le PDF](performing-batch-updates-cs/_static/datatutorial37cs1.pdf)

> Apprendre à créer un entièrement modifiable DataList tous ses éléments où se trouvent dans modifier le mode et dont les valeurs peuvent être enregistrées en cliquant sur un bouton « Tout mettre à jour » dans la page.


## <a name="introduction"></a>Introduction

Dans le [didacticiel précédent](an-overview-of-editing-and-deleting-data-in-the-datalist-cs.md) nous avons examiné comment créer un contrôle DataList au niveau élément. Comme le GridView modifiable standard, chaque élément dans le contrôle DataList inclus un bouton Modifier qui, lorsque vous cliquez sur, serait modifier l’élément. Alors que cela au niveau élément édition fonctionne bien pour les données qui sont uniquement mis à jour régulièrement, certains scénarios requièrent l’utilisateur de modifier le nombre d’enregistrements. Si un utilisateur a besoin de modifier des dizaines d’enregistrements et est obligé de modifier, apporter leurs modifications, puis cliquez sur la mise à jour pour chacune d’elles, la quantité d’un clic sur peut entraver sa productivité. Dans ce cas, une meilleure option consiste à fournir un contrôle DataList totalement modifiable, celui dans lequel *tous les* de ses éléments sont en mode édition et dont les valeurs peuvent être modifiées en cliquant sur un bouton tout mettre à jour sur la page (voir Figure 1).


[![Chaque élément dans un contrôle DataList modifiable entièrement peut être modifié.](performing-batch-updates-cs/_static/image2.png)](performing-batch-updates-cs/_static/image1.png)

**Figure 1**: chaque élément dans un contrôle DataList modifiable entièrement peut être modifié ([cliquez pour afficher l’image en taille réelle](performing-batch-updates-cs/_static/image3.png))


Dans ce didacticiel, nous allons examiner comment permettre aux utilisateurs de mettre à jour les informations d’adresse de fournisseurs à l’aide d’un contrôle DataList totalement modifiable.

## <a name="step-1-create-the-editable-user-interface-in-the-datalist-s-itemtemplate"></a>Étape 1 : Créer l’Interface utilisateur modifiable dans le s DataList ItemTemplate

Dans le didacticiel précédent, où nous création d’un contrôle DataList modifiable standard, au niveau élément, nous avons utilisé deux modèles :

- `ItemTemplate`contenu de l’interface utilisateur en lecture seule (les contrôles Web Label pour afficher chaque nom de produit et le prix).
- `EditItemTemplate`contenu de l’interface utilisateur en mode d’édition (les deux contrôles de zone de texte Web).

DataList s `EditItemIndex` détermine ce que la propriété `DataListItem` (le cas échéant) est restitué à l’aide du `EditItemTemplate`. En particulier, la `DataListItem` dont `ItemIndex` valeur correspond à celle du contrôle DataList s `EditItemIndex` propriété est restituée à l’aide du `EditItemTemplate`. Ce modèle fonctionne bien lorsque qu’un seul élément peut être modifié à un moment, mais se situe éloignés lors de la création d’un contrôle DataList totalement modifiable.

Pour un contrôle DataList totalement modifiable, nous souhaitons *tous les* de le `DataListItem` s pour effectuer le rendu à l’aide de l’interface modifiable. La façon la plus simple pour y parvenir consiste à définir l’interface modifiable dans le `ItemTemplate`. Pour modifier les informations d’adresse de fournisseurs, l’interface modifiable contient le nom du fournisseur en tant que texte et des zones de texte pour l’adresse, ville et les valeurs de pays.

Commencez par ouvrir le `BatchUpdate.aspx` page, ajoutez un contrôle DataList et définir son `ID` propriété `Suppliers`. À partir de la balise active du contrôle DataList s, choisir d’ajouter un nouveau contrôle ObjectDataSource nommé `SuppliersDataSource`.


[![Créer un nouveau ObjectDataSource nommé SuppliersDataSource](performing-batch-updates-cs/_static/image5.png)](performing-batch-updates-cs/_static/image4.png)

**Figure 2**: créer un nouveau nommé de ObjectDataSource `SuppliersDataSource` ([cliquez pour afficher l’image en taille réelle](performing-batch-updates-cs/_static/image6.png))


Configurer l’ObjectDataSource pour récupérer des données à l’aide de la `SuppliersBLL` classe s `GetSuppliers()` (méthode) (voir Figure 3). Comme avec le didacticiel précédent, plutôt que de mettre à jour les informations de fournisseur via ObjectDataSource, nous allons fonctionne directement avec la couche de logique métier. Par conséquent, définissez la liste déroulante (aucun) dans l’onglet de mise à jour (voir Figure 4).


[![Récupérer les informations de fournisseur à l’aide de la méthode GetSuppliers()](performing-batch-updates-cs/_static/image8.png)](performing-batch-updates-cs/_static/image7.png)

**Figure 3**: informations fournisseur récupérer à l’aide du `GetSuppliers()` (méthode) ([cliquez pour afficher l’image en taille réelle](performing-batch-updates-cs/_static/image9.png))


[![Définir la liste déroulante (aucun) dans l’onglet mise à jour](performing-batch-updates-cs/_static/image11.png)](performing-batch-updates-cs/_static/image10.png)

**Figure 4**: définir la liste déroulante (aucun) dans l’onglet mise à jour ([cliquez pour afficher l’image en taille réelle](performing-batch-updates-cs/_static/image12.png))


Après la fin de l’Assistant, Visual Studio génère automatiquement du contrôle DataList s `ItemTemplate` pour afficher chaque champ de données retourné par la source de données dans un contrôle Web Label. Nous devons modifier ce modèle afin qu’il fournit l’interface de modification à la place. Le `ItemTemplate` peuvent être personnalisés par le biais du concepteur à l’aide de l’option Modifier les modèles à partir de la balise active du contrôle DataList s ou directement par le biais de la syntaxe déclarative.

Prenez un moment pour créer une interface d’édition qui affiche le nom du fournisseur s sous forme de texte, mais inclut des zones de texte pour le fournisseur s adresse, ville et les valeurs de pays. Après avoir apporté ces modifications, votre syntaxe déclarative s de page doit ressembler à ce qui suit :


[!code-aspx[Main](performing-batch-updates-cs/samples/sample1.aspx)]

> [!NOTE]
> Comme avec le didacticiel précédent, le contrôle DataList dans ce didacticiel doit avoir son état d’affichage.


Dans le `ItemTemplate` je m à l’aide de deux nouvelles classes CSS, `SupplierPropertyLabel` et `SupplierPropertyValue`, qui ont été ajouté à la `Styles.css` classe et configuré pour utiliser les mêmes paramètres de style que le `ProductPropertyLabel` et `ProductPropertyValue` des classes CSS.


[!code-css[Main](performing-batch-updates-cs/samples/sample2.css)]

Après avoir apporté ces modifications, visitez cette page via un navigateur. Comme le montre la Figure 5, chaque élément DataList affiche le nom du fournisseur en tant que texte et utilise des zones de texte pour afficher l’adresse, ville et pays.


[![Chaque fournisseur dans le contrôle DataList est modifiable](performing-batch-updates-cs/_static/image14.png)](performing-batch-updates-cs/_static/image13.png)

**Figure 5**: chaque fournisseur dans le contrôle DataList est modifiable ([cliquez pour afficher l’image en taille réelle](performing-batch-updates-cs/_static/image15.png))


## <a name="step-2-adding-an-update-all-button"></a>Étape 2 : Ajout d’une mise à jour bouton tout

Alors que chaque fournisseur dans la Figure 5 a son adresse, ville et des champs de pays affichés dans une zone de texte, il n’est actuellement aucun bouton de mise à jour disponible. Au lieu d’avoir un bouton de mise à jour par élément, avec DataList totalement modifiable il y a généralement un seul bouton tout mettre à jour dans la page qui, lorsque vous cliquez dessus, met à jour *tous les* des enregistrements dans le contrôle DataList. Pour ce didacticiel, permettent d’ajouter deux boutons tout mettre à jour - un en haut de la page et l’autre en bas (bien qu’en cliquant sur des boutons aura le même effet) s.

Commencez par ajouter un contrôle bouton Web au-dessus du contrôle DataList et un ensemble son `ID` propriété `UpdateAll1`. Ensuite, ajoutez le deuxième contrôle bouton Web sous le contrôle DataList de définir son `ID` à `UpdateAll2`. Définir le `Text` propriétés pour les deux boutons à tout mettre à jour. Enfin, créez des gestionnaires d’événements pour les deux boutons `Click` événements. Au lieu de dupliquer la logique de mise à jour dans chacun des gestionnaires d’événements, s permettent de refactoriser cette logique à une troisième méthode, `UpdateAllSupplierAddresses`, avec les gestionnaires d’événements simplement appeler cette méthode tiers.


[!code-csharp[Main](performing-batch-updates-cs/samples/sample3.cs)]

La figure 6 montre la page après que les mise à jour tous les boutons ont été ajoutés.


[![Deux boutons de toutes les mises à jour ont été ajoutés à la Page](performing-batch-updates-cs/_static/image17.png)](performing-batch-updates-cs/_static/image16.png)

**Figure 6**: deux boutons de toutes les mises à jour ont été ajoutés à la Page ([cliquez pour afficher l’image en taille réelle](performing-batch-updates-cs/_static/image18.png))


## <a name="step-3-updating-all-of-the-suppliers-address-information"></a>Étape 3 : Mise à jour toutes les informations d’adresse de fournisseurs

Avec tous les éléments DataList s afficher l’interface de modification et l’ajout des mise à jour tous les boutons, il reste écrit le code pour effectuer la mise à jour par lots. Plus précisément, nous avons besoin parcourir les éléments du contrôle DataList s et appelez le `SuppliersBLL` classe s `UpdateSupplierAddress` méthode pour chacun d’eux.

La collection de `DataListItem` instances utilisés DataList sont accessibles par le biais du contrôle DataList s [ `Items` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.datalist.items.aspx). Avec une référence à un `DataListItem`, nous pouvons saisir correspondant `SupplierID` à partir de la `DataKeys` collection et par programme de référence des contrôles Web de la zone de texte dans le `ItemTemplate` comme l’illustre le code suivant :


[!code-csharp[Main](performing-batch-updates-cs/samples/sample4.cs)]

Lorsque l’utilisateur clique sur l’un des boutons tout mettre à jour, le `UpdateAllSupplierAddresses` méthode itère au sein de chaque `DataListItem` dans les `Suppliers` DataList et appelle le `SuppliersBLL` classe s `UpdateSupplierAddress` méthode, en passant les valeurs correspondantes. Une valeur non entré pour l’adresse, ville ou pays passe est une valeur de `Nothing` à `UpdateSupplierAddress` (au lieu d’une chaîne vide), ce qui entraîne une base de données `NULL` pour les champs d’enregistrement s sous-jacentes.

> [!NOTE]
> En tant qu’une amélioration, vous voudrez ajouter un contrôle Web Label de l’état à la page qui fournit un message de confirmation une fois la mise à jour par lots est effectuée.


## <a name="updating-only-those-addresses-that-have-been-modified"></a>Mise à jour que les adresses qui ont été modifiés

L’algorithme de mise à jour de lot utilisé pour les appels de ce didacticiel le `UpdateSupplierAddress` méthode pour *chaque* fournisseur dans le contrôle DataList, indépendamment de si leurs informations d’adresse a été modifiées. Alors que ce blind des mises à jour ne sont pas toujours généralement un problème de performances, elles peuvent entraîner des enregistrements superflus si vous re l’audit des modifications à la table de base de données. Par exemple, si vous utilisez des déclencheurs pour enregistrer toutes les `UPDATE` s pour le `Suppliers` table à une table d’audit, chaque fois qu’un utilisateur clique sur le bouton tout mettre à jour un enregistrement d’audit nouvelle sera créé pour chaque fournisseur dans le système, indépendamment de si l’utilisateur fait une modifications.

Les classes ADO.NET DataTable et DataAdapter sont conçues pour prendre en charge les mises à jour par lots où les résultats uniquement modifié, supprimé et le nouvel enregistrement dans toute communication de base de données. Chaque ligne de la table de données a un [ `RowState` propriété](https://msdn.microsoft.com/library/system.data.datarow.rowstate.aspx) qui indique si la ligne a été ajoutée à la table de données, supprimé, modifié, ou s’il reste inchangée. Lorsqu’un DataTable est initialement renseigné, toutes les lignes sont marquées inchangées. Modification de la valeur de toutes les colonnes de s de ligne de marque la ligne modifiée.

Dans le `SuppliersBLL` classe nous mettre à jour les informations d’adresse de fournisseur spécifié s en première lecture dans l’enregistrement du fournisseur unique dans un `SuppliersDataTable` , puis définissez le `Address`, `City`, et `Country` les valeurs de colonne utilisant le code suivant :


[!code-csharp[Main](performing-batch-updates-cs/samples/sample5.cs)]

Ce code affecte naïvement la passé dans l’adresse, ville et un pays à le `SuppliersRow` dans le `SuppliersDataTable` , quelle que soit ou non les valeurs ont changé. Ces modifications provoquent la `SuppliersRow` s `RowState` propriété sera marquée comme modifiée. Lorsque s de la couche d’accès aux données `Update` est appelée, elle constate que le `SupplierRow` a été modifié et par conséquent envoie un `UPDATE` commande à la base de données.

Toutefois, supposons que nous avons ajouté du code à cette méthode uniquement affecter la passé dans l’adresse, ville et pays si elles diffèrent le `SuppliersRow` les valeurs existantes s. Dans le cas où l’adresse, ville et pays en sont les mêmes que les données existantes, aucune modification ne sera apportée et `SupplierRow` s `RowState` gauche marqué comme reste la même. Le résultat net est que lorsque la couche DAL s `Update` est appelée, aucun appel de la base de données n’est effectuée, car le `SuppliersRow` n’a pas été modifié.

Pour activer cette modification, remplacez les instructions à l’aveugle affecter la passé dans l’adresse, ville et pays avec le code suivant :


[!code-csharp[Main](performing-batch-updates-cs/samples/sample6.cs)]

Avec cette ajouté du code, la couche DAL s `Update` méthode envoie un `UPDATE` instruction à la base de données pour que les enregistrements dont les valeurs associées à l’adresse ont été modifiés.

Ou bien, nous pouvons effectuer le suivi indique s’il existe des différences entre les champs d’adresse dans le passé et la base de données et, s’il en existe aucun, il vous suffit contourner l’appel à la couche DAL s `Update` (méthode). Cette approche fonctionne bien si vous utilisez la base de données directe (méthode), étant donné que la méthode directe de base de données n’est pas passé un `SuppliersRow` dont l’instance `RowState` peut être vérifiée pour déterminer si un appel de la base de données est réellement nécessaire.

> [!NOTE]
> Chaque fois que le `UpdateSupplierAddress` méthode est appelée, un appel est fait à la base de données pour récupérer des informations sur l’enregistrement mis à jour. Ensuite, si des modifications sont effectuées dans les données, un autre appel à la base de données est effectué pour mettre à jour la ligne de table. Ce flux de travail peut être optimisé en créant un `UpdateSupplierAddress` surcharge de méthode qui accepte un `EmployeesDataTable` instance a *tous les* des modifications de la `BatchUpdate.aspx` page. Ensuite, il peut être un appel à la base de données pour obtenir tous les enregistrements de la `Suppliers` table. Deux jeux de résultats peut ensuite être énumérés, et seuls les enregistrements où les modifications ont été pu être mis à jour.


## <a name="summary"></a>Récapitulatif

Dans ce didacticiel, nous avons vu comment créer un contrôle DataList totalement modifiable, permettant à un utilisateur de modifier rapidement les informations d’adresse pour plusieurs fournisseurs. Nous avons commencé en définissant un contrôle Web de la zone de texte pour le fournisseur s adresse, ville et les valeurs de pays de l’interface de modification dans le contrôle DataList s `ItemTemplate`. Ensuite, nous avons ajouté la mise à jour tous les boutons au-dessus et au-dessous du contrôle DataList. Une fois un utilisateur a effectué ses modifications et cliqué sur un des boutons tout mettre à jour, le `DataListItem` s sont énumérées et un appel à la `SuppliersBLL` classe s `UpdateSupplierAddress` méthode est effectuée.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Zack Jones et Ken Pespisa. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](an-overview-of-editing-and-deleting-data-in-the-datalist-cs.md)
[Suivant](handling-bll-and-dal-level-exceptions-cs.md)
