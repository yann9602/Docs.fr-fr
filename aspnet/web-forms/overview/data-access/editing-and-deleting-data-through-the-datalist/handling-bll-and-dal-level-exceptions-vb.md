---
uid: web-forms/overview/data-access/editing-and-deleting-data-through-the-datalist/handling-bll-and-dal-level-exceptions-vb
title: La gestion des Exceptions de niveau BLL et DAL (VB) | Documents Microsoft
author: rick-anderson
description: "Dans ce didacticiel, nous verrons comment gérer les exceptions levées au cours du flux de travail mis à jour d’une DataList modifiable tact."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/30/2006
ms.topic: article
ms.assetid: ca665073-b379-4239-9404-f597663ca65e
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/editing-and-deleting-data-through-the-datalist/handling-bll-and-dal-level-exceptions-vb
msc.type: authoredcontent
ms.openlocfilehash: 2bd2ccb13c44d104e8945840705a21738d8abd5c
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="handling-bll--and-dal-level-exceptions-vb"></a>La gestion des Exceptions de niveau BLL et DAL (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_38_VB.exe) ou [télécharger le PDF](handling-bll-and-dal-level-exceptions-vb/_static/datatutorial38vb1.pdf)

> Dans ce didacticiel, nous verrons comment gérer les exceptions levées au cours du flux de travail mis à jour d’une DataList modifiable tact.


## <a name="introduction"></a>Introduction

Dans le [vue d’ensemble de la modification et suppression des données dans le contrôle DataList](an-overview-of-editing-and-deleting-data-in-the-datalist-cs.md) didacticiel, nous avons créé un contrôle DataList proposés simple de modification et de suppression de fonctionnalités. Bien qu’entièrement fonctionnelles, il est difficilement convivial, comme une erreur s’est produite pendant la modification ou la suppression du processus a généré une exception non gérée. Par exemple, omission du nom de produit s ou lorsque vous modifiez un produit, vous entrez la valeur prix très abordable !, lève une exception. Étant donné que cette exception n’est pas interceptée dans le code, elle se propage jusqu'à l’exécution ASP.NET, qui affiche ensuite les détails de l’exception s dans la page web.

Comme nous l’avons vu dans la [BLL - gestion et des Exceptions au niveau de la couche DAL dans une Page ASP.NET](../editing-inserting-and-deleting-data/handling-bll-and-dal-level-exceptions-in-an-asp-net-page-cs.md) (didacticiel), si une exception est levée à partir de la profondeur de la logique métier ou de couches d’accès aux données, les détails d’exception sont retournés à ObjectDataSource et sur le contrôle GridView. Nous avons vu comment gérer correctement ces exceptions en créant `Updated` ou `RowUpdated` gestionnaires d’événements pour l’ObjectDataSource ou GridView, la vérification pour une exception et puis indiquant que l’exception a été gérée.

Nos DataList didacticiels, toutefois, t ne se trouvent pas à l’aide de ObjectDataSource pour la mise à jour et suppression de données. Au lieu de cela, nous travaillons directement par rapport à la couche BLL. Afin de détecter les exceptions provenant de la couche BLL ou la couche DAL, nous devons implémenter le code dans le code-behind de notre page relative à ASP.NET de gestion des exceptions. Dans ce didacticiel, nous verrons comment plus tact gérer les exceptions levées pendant un s DataList modifiable mise à jour de flux de travail.

> [!NOTE]
> Dans le *une vue d’ensemble de modification et suppression des données dans le contrôle DataList* didacticiel évoqué différentes techniques pour la modification et suppression de données à partir du contrôle DataList, certaines techniques impliquées à l’aide d’un ObjectDataSource pour mettre à jour et la suppression. Si vous utilisez ces techniques, vous pouvez gérer les exceptions à partir de la couche BLL ou DAL via le s ObjectDataSource `Updated` ou `Deleted` gestionnaires d’événements.


## <a name="step-1-creating-an-editable-datalist"></a>Étape 1 : Création d’un contrôle DataList modifiable

Avant de nous soucier de la gestion des exceptions qui se produisent pendant le flux de travail de mise à jour, permettent de s tout d’abord créer un contrôle DataList modifiable. Ouvrir le `ErrorHandling.aspx` page dans le `EditDeleteDataList` ajouter un contrôle DataList vers le concepteur, un ensemble de dossier, ses `ID` propriété `Products`, et ajouter un nouveau ObjectDataSource nommé `ProductsDataSource`. Configurer l’ObjectDataSource à utiliser le `ProductsBLL` classe s `GetProducts()` méthode de sélection des enregistrements, de définir les listes déroulantes dans l’instruction INSERT, UPDATE et de supprimer des onglets à (None).


[![Retourne les informations de produit à l’aide de la méthode GetProducts()](handling-bll-and-dal-level-exceptions-vb/_static/image2.png)](handling-bll-and-dal-level-exceptions-vb/_static/image1.png)

**Figure 1**: renvoyer les informations de produit à l’aide de la `GetProducts()` (méthode) ([cliquez pour afficher l’image en taille réelle](handling-bll-and-dal-level-exceptions-vb/_static/image3.png))


Après avoir terminé l’Assistant ObjectDataSource, Visual Studio crée automatiquement un `ItemTemplate` pour le contrôle DataList. Remplacez ceci avec un `ItemTemplate` qui affiche chaque nom de produit s et les prix et inclut un bouton Modifier. Ensuite, créez un `EditItemTemplate` avec un contrôle Web de la zone de texte pour le nom et les prix et les boutons Annuler et de mise à jour. Enfin, définissez le contrôle DataList s `RepeatColumns` propriété à 2.

Après ces modifications, votre balisage déclaratif s de page doit ressembler à ce qui suit. Assurez-vous que la modification, Annuler, et les boutons de la mise à jour ont leur `CommandName` propriétés définies pour le modifier, Annuler et mettre à jour, respectivement.


[!code-aspx[Main](handling-bll-and-dal-level-exceptions-vb/samples/sample1.aspx)]

> [!NOTE]
> Pour ce didacticiel du contrôle DataList état d’affichage s doit être activée.


Prenez un moment pour afficher la progression via un navigateur (voir Figure 2).


[![Chaque produit comprend un bouton Modifier](handling-bll-and-dal-level-exceptions-vb/_static/image5.png)](handling-bll-and-dal-level-exceptions-vb/_static/image4.png)

**Figure 2**: chaque produit comprend un bouton Modifier ([cliquez pour afficher l’image en taille réelle](handling-bll-and-dal-level-exceptions-vb/_static/image6.png))


Actuellement, le bouton Modifier n'entraîne qu’une publication (postback) il n’encore modifier le produit. Pour activer la modification, nous devons créer des gestionnaires d’événements pour le contrôle DataList s `EditCommand`, `CancelCommand`, et `UpdateCommand` événements. Le `EditCommand` et `CancelCommand` événements mettre à jour du contrôle DataList s `EditItemIndex` propriété et les données du contrôle DataList la reliaison :


[!code-vb[Main](handling-bll-and-dal-level-exceptions-vb/samples/sample2.vb)]

Le `UpdateCommand` Gestionnaire d’événements est un peu plus complexe. Il doit lire dans le produit modifié s `ProductID` à partir de la `DataKeys` collection ainsi que le nom de produit s et le prix à partir des zones de texte dans le `EditItemTemplate`, puis appelez le `ProductsBLL` classe s `UpdateProduct` méthode avant le retour du contrôle DataList à son état avant la modification.

Pour l’instant, s permettent d’utiliser simplement le même code à partir de la `UpdateCommand` Gestionnaire d’événements dans le *vue d’ensemble de la modification et suppression des données dans le contrôle DataList* didacticiel. Nous allons ajouter le code pour gérer correctement les exceptions à l’étape 2.


[!code-vb[Main](handling-bll-and-dal-level-exceptions-vb/samples/sample3.vb)]

En dépit d’une entrée non valide, qui peut être sous la forme d’un prix unitaire incorrectement mis en forme, une valeur du prix unitaire non conforme comme - 5,00$ ou l’omission du nom s produit qu'une exception sera levée. Étant donné que le `UpdateCommand` Gestionnaire d’événements n’inclut pas de toute exception à la gestion du code à ce stade, l’exception se propage jusqu'à l’exécution d’ASP.NET, où il sera affiché à l’utilisateur final (voir Figure 3).


![Lorsqu’une Exception non gérée se produit, l’utilisateur final peut voir une Page d’erreur](handling-bll-and-dal-level-exceptions-vb/_static/image7.png)

**Figure 3**: lorsqu’une Exception non gérée se produit, l’utilisateur final peut voir une Page d’erreur


## <a name="step-2-gracefully-handling-exceptions-in-the-updatecommand-event-handler"></a>Étape 2 : Gérer correctement les Exceptions dans le Gestionnaire d’événements de UpdateCommand

Lors de la mise à jour du flux de travail, les exceptions peuvent se produire dans le `UpdateCommand` Gestionnaire d’événements, la couche BLL ou la couche DAL. Par exemple, si un utilisateur entre un prix de trop coûteuses, les `Decimal.Parse` instruction dans le `UpdateCommand` Gestionnaire d’événements lève une `FormatException` exception. Si l’utilisateur l’omet le nom du produit s ou si le prix a une valeur négative, la couche DAL lève une exception.

Lorsqu’une exception se produit, nous souhaitons afficher un message d’information dans la page elle-même. Ajouter un site Web d’étiquette de contrôle à la page dont `ID` a la valeur `ExceptionDetails`. Configurer le texte d’étiquette s pour afficher en rouge, très volumineux, une police en gras et italique en attribuant son `CssClass` propriété le `Warning` classe CSS qui est défini dans le `Styles.css` fichier.

Lorsqu’une erreur se produit, nous voulons uniquement l’étiquette à afficher une seule fois. Autrement dit, pour les publications ultérieures, le message d’avertissement étiquette s devrait disparaître. Cela peut être accompli en supprimant soit l’étiquette s `Text` propriété ou des paramètres de son `Visible` propriété `False` dans les `Page_Load` Gestionnaire d’événements (comme nous l’avons fait dans le [BLL - gestion et des Exceptions au niveau de la couche DAL dans une page ASP Page .NET](../editing-inserting-and-deleting-data/handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb.md) didacticiel) ou en désactivant la prise en charge de l’étiquette s vue état. Permettent d’utiliser cette dernière option s.


[!code-aspx[Main](handling-bll-and-dal-level-exceptions-vb/samples/sample4.aspx)]

Lorsqu’une exception est levée, vous devez entrer les détails de l’exception pour le `ExceptionDetails` Label, contrôle s `Text` propriété. Étant donné que son état d’affichage est désactivé, sur les publications ultérieures le `Text` les modifications par programme la propriété s seront perdues, revenez au texte par défaut (une chaîne vide), masquant ainsi le message d’avertissement.

Pour déterminer lorsqu’une erreur a été levée pour afficher un message utile sur la page, nous devons ajouter une `Try ... Catch` bloquer à le `UpdateCommand` Gestionnaire d’événements. Le `Try` partie contient du code qui peut provoquer une exception, tandis que le `Catch` bloc contient du code qui est exécuté en cas d’une exception. Extraire le [notions de base la gestion des exceptions](https://msdn.microsoft.com/library/2w8f0bss.aspx) section dans la documentation .NET Framework pour plus d’informations sur la `Try ... Catch` bloc.


[!code-vb[Main](handling-bll-and-dal-level-exceptions-vb/samples/sample5.vb)]

Lorsqu’une exception de n’importe quel type est levée par le code dans le `Try` bloc, le `Catch` code de bloc s commence l’exécution. Le type d’exception qui est levée `DbException`, `NoNullAllowedException`, `ArgumentException`et ainsi de suite dépend, exactement, a déclenché l’erreur en premier lieu. Si cet emplacement s un problème au niveau de la base de données, un `DbException` sera levée. Si une valeur non valide est entrée pour le `UnitPrice`, `UnitsInStock`, `UnitsOnOrder`, ou `ReorderLevel` champs, un `ArgumentException` est levée, car nous avons ajouté du code pour valider les valeurs de ces champs dans le `ProductsDataTable` classe (voir la [ Création d’une couche de logique métier](../introduction/creating-a-business-logic-layer-vb.md) didacticiel).

Nous pouvons fournir une explication plus utile à l’utilisateur final à partir du texte du message sur le type d’exception interceptée. Le code suivant qui a été utilisé dans un formulaire presque identique à la [BLL - gestion et des Exceptions au niveau de la couche DAL dans une Page ASP.NET](../editing-inserting-and-deleting-data/handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb.md) didacticiel fournit ce niveau de détail :


[!code-vb[Main](handling-bll-and-dal-level-exceptions-vb/samples/sample6.vb)]

Pour effectuer ce didacticiel, appelez simplement le `DisplayExceptionDetails` méthode à partir de la `Catch` bloc en passant l’interceptée `Exception` instance (`ex`).

Avec la `Try ... Catch` bloquer en place, les utilisateurs sont présentées avec un message d’erreur plus explicites, comme les Figures 4 et 5 afficher. Notez que, en cas d’une exception du contrôle DataList reste dans le mode édition. Il s’agit, car une fois l’exception se produit, le flux de contrôle est immédiatement redirigé vers le `Catch` bloc, en ignorant le code qui renvoie du contrôle DataList à son état avant la modification.


[![Un Message d’erreur s’affiche si un utilisateur n’indique pas un champ obligatoire](handling-bll-and-dal-level-exceptions-vb/_static/image9.png)](handling-bll-and-dal-level-exceptions-vb/_static/image8.png)

**Figure 4**: un Message d’erreur s’affiche si un utilisateur n’indique pas un champ obligatoire ([cliquez pour afficher l’image en taille réelle](handling-bll-and-dal-level-exceptions-vb/_static/image10.png))


[![Un Message d’erreur est affiché quand un prix négatif entrant](handling-bll-and-dal-level-exceptions-vb/_static/image12.png)](handling-bll-and-dal-level-exceptions-vb/_static/image11.png)

**Figure 5**: un Message d’erreur est affiché quand un prix négatif entrant ([cliquez pour afficher l’image en taille réelle](handling-bll-and-dal-level-exceptions-vb/_static/image13.png))


## <a name="summary"></a>Récapitulatif

Le GridView et ObjectDataSource fournissent des gestionnaires d’événements post-niveau qui incluent des informations sur les exceptions qui ont été générées pendant le flux de travail de mise à jour et suppression, ainsi que les propriétés qui peuvent être définies pour indiquer si l’exception a été géré. Ces fonctionnalités, toutefois, ne sont pas disponibles lorsque utilisez du contrôle DataList et à l’aide de la couche BLL directement. Au lieu de cela, nous sommes responsables de l’implémentation de la gestion des exceptions.

Dans ce didacticiel, nous avons vu comment ajouter des exceptions pour un s DataList modifiable mise à jour de flux de travail en ajoutant un `Try ... Catch` bloquer à le `UpdateCommand` Gestionnaire d’événements. Si une exception est levée pendant la mise à jour du flux de travail, le `Catch` code de bloc s’exécute, affichant des informations utiles dans le `ExceptionDetails` étiquette.

À ce stade, le contrôle DataList ne fait aucun effort pour éviter les exceptions de se produire en premier lieu. Bien que nous savons que négatif entraîne une exception, nous vous n avez encore t encore ajouté toutes les fonctionnalités pour proactive empêcher un utilisateur d’entrer ce type d’entrée non valide. Dans notre étape suivante du didacticiel, nous verrons comment aider à réduire les exceptions provoquées par l’utilisateur non valide en ajoutant des contrôles de validation dans le `EditItemTemplate`.

Bonne programmation !

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Instructions de conception pour les exceptions](https://msdn.microsoft.com/library/ms298399.aspx)
- [Modules de journalisation de l’erreur et les gestionnaires (ELMAH)](http://workspaces.gotdotnet.com/elmah) (une bibliothèque open source pour la journalisation des erreurs)
- [Enterprise Library pour .NET Framework 2.0](https://www.microsoft.com/downloads/details.aspx?familyid=5A14E870-406B-4F2A-B723-97BA84AE80B5&amp;displaylang=en) (y compris le bloc d’Application de gestion des exceptions)

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Ken Pespisa. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](performing-batch-updates-vb.md)
[Suivant](adding-validation-controls-to-the-datalist-s-editing-interface-vb.md)
