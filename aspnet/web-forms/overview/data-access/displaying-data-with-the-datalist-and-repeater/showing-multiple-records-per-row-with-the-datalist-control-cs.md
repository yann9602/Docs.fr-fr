---
uid: web-forms/overview/data-access/displaying-data-with-the-datalist-and-repeater/showing-multiple-records-per-row-with-the-datalist-control-cs
title: "Affichage des enregistrements multiples par ligne avec le contrôle DataList (c#) | Documents Microsoft"
author: rick-anderson
description: "Dans ce court didacticiel, nous allons observer comment personnaliser la disposition du contrôle DataList via ses propriétés RepeatColumns et RepeatDirection."
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/13/2006
ms.topic: article
ms.assetid: cf5acaf5-d4f6-4957-badc-b89956b285f3
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/displaying-data-with-the-datalist-and-repeater/showing-multiple-records-per-row-with-the-datalist-control-cs
msc.type: authoredcontent
ms.openlocfilehash: e9f04089afdbeb1b13725536c9fe97951ee8ca5c
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="showing-multiple-records-per-row-with-the-datalist-control-c"></a>Affichage des enregistrements multiples par ligne avec le contrôle DataList (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_31_CS.exe) ou [télécharger le PDF](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/datatutorial31cs1.pdf)

> Dans ce court didacticiel, nous allons observer comment personnaliser la disposition du contrôle DataList via ses propriétés RepeatColumns et RepeatDirection.


## <a name="introduction"></a>Introduction

Les exemples de DataList nous avons vu dans les deux derniers didacticiels ont rendus chaque enregistrement à partir de sa source de données sous forme de ligne dans une seule colonne HTML `<table>`. Bien que le comportement du contrôle DataList par défaut, il est très facile de personnaliser l’affichage du contrôle DataList, tels que les éléments de source de données sont réparties sur une table à plusieurs colonne, à plusieurs lignes. En outre, il s permet de disposer de toutes les données source d’éléments affichés dans la ligne unique, plusieurs colonne DataList.

Nous pouvons personnaliser la disposition du contrôle DataList s via son `RepeatColumns` et `RepeatDirection` propriétés, qui, respectivement, indiquent le nombre de colonnes est rendu et si ces éléments sont disposés verticalement ou horizontalement. Figure 1, par exemple, montre qu’un contrôle DataList qui affiche des informations sur les produits dans une table avec trois colonnes.


[![Le contrôle DataList indique trois produits par ligne](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image2.png)](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image1.png)

**Figure 1**: le contrôle DataList affiche trois produits par ligne ([cliquez pour afficher l’image en taille réelle](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image3.png))


En affichant plusieurs éléments de source de données par ligne, le contrôle DataList peut utiliser plus efficacement d’espace d’écran horizontale. Dans ce court didacticiel, nous allons explorer ces deux propriétés de DataList.

## <a name="step-1-displaying-product-information-in-a-datalist"></a>Étape 1 : Afficher des informations de produit dans un contrôle DataList

Avant d’examiner le `RepeatColumns` et `RepeatDirection` propriétés permettent de s d’abord créer un contrôle DataList sur notre page qui répertorie les informations de produit à l’aide de la disposition de table standard d’une seule colonne, plusieurs lignes. Pour cet exemple, permettent d’afficher le nom de s de produit, la catégorie et le prix en utilisant le balisage suivant s :


[!code-html[Main](showing-multiple-records-per-row-with-the-datalist-control-cs/samples/sample1.html)]

Nous avons vu comment lier des données à un contrôle DataList dans les exemples précédents, vous allez déplacer rapidement ces étapes. Commencez par ouvrir le `RepeatColumnAndDirection.aspx` page dans le `DataListRepeaterBasics` faites glisser un contrôle DataList à partir de la boîte à outils vers le concepteur. À partir de la balise active du contrôle DataList s, choisir de créer un nouveau ObjectDataSource et configurez-le pour extraire ses données à partir de la `ProductsBLL` classe s `GetProducts` méthode, en choisissant (aucun) option à partir de l’Assistant s INSERT, UPDATE et supprimer des onglets.

Après la création et la liaison de la nouvelle ObjectDataSource au contrôle DataList, Visual Studio crée automatiquement un `ItemTemplate` qui affiche le nom et la valeur pour chacun des champs de données de produit. Ajuster la `ItemTemplate` directement via le balisage déclaratif ou de modifier les modèles d’option dans la balise active du contrôle DataList s afin qu’il utilise le balisage illustré ci-dessus, en remplaçant le *Product Name*, *nom de catégorie* , et *prix* texte avec les contrôles Label qui utilisent la syntaxe de liaison de données appropriée pour affecter des valeurs à leurs `Text` propriétés. Après la mise à jour le `ItemTemplate`, votre balisage déclaratif s de page doit ressembler à ce qui suit :


[!code-aspx[Main](showing-multiple-records-per-row-with-the-datalist-control-cs/samples/sample2.aspx)]

Notez que lorsque ve inclus un spécificateur de format dans le `Eval` syntaxe de liaison de données pour le `UnitPrice`, mise en forme de la valeur retournée sous forme de devise -`Eval("UnitPrice", "{0:C}").`

Prenez un moment pour consulter votre page dans un navigateur. Comme le montre la Figure 2, le contrôle DataList est rendu sous la forme d’une table de colonne unique, plusieurs ligne de produits.


[![Par défaut, les convertisseurs de DataList en tant que colonne unique, plusieurs ligne Table](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image5.png)](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image4.png)

**Figure 2**: par défaut, le rendu de DataList sous la forme d’une seule colonne, Table de plusieurs lignes ([cliquez pour afficher l’image en taille réelle](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image6.png))


## <a name="step-2-changing-the-datalist-s-layout-direction"></a>Étape 2 : Modification de la Direction de mise en page de s de DataList

Lors du comportement par défaut pour le contrôle DataList est de présenter ses éléments verticalement dans une table une colonne unique, plusieurs ligne, ce comportement facilement modifiables par le biais du contrôle DataList s [ `RepeatDirection` propriété](https://msdn.microsoft.com/system.web.ui.webcontrols.datalist.repeatdirection.aspx). Le `RepeatDirection` propriété peut accepter une des deux valeurs possibles : `Horizontal` ou `Vertical` (la valeur par défaut).

En modifiant le `RepeatDirection` propriété à partir de `Vertical` à `Horizontal`, DataList restitue ses enregistrements dans une seule ligne, la création d’une colonne par un élément de source de données. Pour illustrer cela, cliquez sur le contrôle DataList dans le concepteur et à partir de la fenêtre Propriétés, modifiez la `RepeatDirection` propriété à partir de `Vertical` à `Horiztonal`. Immédiatement lors de cette opération, le concepteur ajuste la disposition du contrôle DataList s, création d’une interface de ligne unique, plusieurs colonne (voir Figure 3).


[![Les éléments de RepeatDirection propriété dicte comment la Direction du contrôle DataList s sont placés Out](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image8.png)](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image7.png)

**Figure 3**: le `RepeatDirection` propriété détermine comment les éléments de la Direction du contrôle DataList s sont placés Out ([cliquez pour afficher l’image en taille réelle](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image9.png))


Lors de l’affichage de petites quantités de données, une seule ligne, plusieurs colonne table peut être une façon idéale pour optimiser l’espace d’écran. Toutefois, pour les volumes de grande capacité de données, une seule ligne requièrent plusieurs colonnes, les pousse les éléments que t peut adapter à l’écran hors tension à droite. La figure 4 illustre les produits lors du rendu d’une seule ligne de DataList. Comme il existe de nombreux produits (plus de 80), l’utilisateur aura faire défiler vers la droite pour afficher des informations sur chacun des produits.


[![Pour les Sources de données suffisamment élevée, une seule colonne de DataList nécessitera un défilement Horizontal](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image11.png)](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image10.png)

**Figure 4**: pour suffisamment grandes Sources de données, une seule colonne DataList sera nécessitent un défilement Horizontal ([cliquez pour afficher l’image en taille réelle](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image12.png))


## <a name="step-3-displaying-data-in-a-multi-column-multi-row-table"></a>Étape 3 : Affichage de données dans une Table à plusieurs colonne, portant sur plusieurs lignes

Pour créer une DataList à plusieurs colonne, portant sur plusieurs lignes, nous devons définir le [ `RepeatColumns` propriété](https://msdn.microsoft.com/system.web.ui.webcontrols.datalist.repeatcolumns.aspx) au nombre de colonnes à afficher. Par défaut, le `RepeatColumns` est définie sur 0, ce qui provoque le contrôle DataList afficher l’ensemble de ses éléments dans une seule ligne ou une colonne (selon la valeur de la `RepeatDirection` propriété).

Dans notre exemple, permettent d’afficher les trois produits par ligne de table s. Par conséquent, définissez la `RepeatColumns` 3 à la propriété. Après avoir apporté cette modification, prenez un moment pour afficher les résultats dans un navigateur. Comme le montre la Figure 5, les produits sont désormais répertoriées dans un tableau de trois colonnes et plusieurs ligne.


[![Trois produits sont affichées par ligne](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image14.png)](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image13.png)

**Figure 5**: trois produits sont affichées par ligne ([cliquez pour afficher l’image en taille réelle](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image15.png))


Le `RepeatDirection` propriété affecte la disposition des éléments dans le contrôle DataList. La figure 5 illustre les résultats avec la `RepeatDirection` propriété `Horizontal`. Notez que les trois premiers produits Tran et suivi modifications affiché sont disposés de gauche à droite, de haut en bas. Les trois produits (à partir de s Chef Anton Cajun Seasoning) s’affichent dans une ligne sous les trois premières. Modification de la `RepeatDirection` propriété revenir `Vertical`, toutefois, ces produits de haut en bas est disposé, gauche à droite, comme le montre la Figure 6.


[![Ici, les produits sont placés verticale](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image17.png)](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image16.png)

**Figure 6**: ici, les produits sont placés verticale ([cliquez pour afficher l’image en taille réelle](showing-multiple-records-per-row-with-the-datalist-control-cs/_static/image18.png))


Le nombre de lignes affichées dans la table résultante varie selon le nombre total d’enregistrements liée au contrôle DataList. Précisément, elle s le plafond du nombre total d’éléments de source de données divisé par le `RepeatColumns` valeur de propriété. Étant donné que le `Products` table possède actuellement des 84 produits, qui est divisible par 3, il existe des 28 lignes. Si le nombre d’éléments dans la source de données et la `RepeatColumns` valeur de propriété n’est pas divisible, puis la dernière ligne ou la colonne aura des cellules vides. Si le `RepeatDirection` a la valeur `Vertical`, puis la dernière colonne aura des cellules vides ; si `RepeatDirection` est `Horizontal`, puis la dernière ligne aura les cellules vides.

## <a name="summary"></a>Récapitulatif

Le contrôle DataList par défaut, affiche ses éléments dans une table contenant la colonne unique, plusieurs ligne, qui reproduit la disposition d’un GridView avec TemplateField unique. Pendant cette mise en page par défaut est acceptable, nous pouvons optimiser écran immobilier en affichant plusieurs éléments de source de données par ligne. Il s’agissait est simplement de la définition du contrôle DataList s `RepeatColumns` propriété au nombre de colonnes à afficher par ligne. En outre, le contrôle DataList s `RepeatDirection` propriété peut être utilisée pour indiquer si le contenu de la table à plusieurs colonne, à plusieurs lignes doit être disposé horizontalement de gauche à droite, de haut en bas ou verticalement, de haut en bas, de gauche à droite.

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été John Suru. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](formatting-the-datalist-and-repeater-based-upon-data-cs.md)
[Suivant](nested-data-web-controls-cs.md)
