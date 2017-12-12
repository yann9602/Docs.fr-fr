---
uid: web-forms/overview/data-access/working-with-batched-data/batch-deleting-cs
title: Suppression (c#) par lots | Documents Microsoft
author: rick-anderson
description: "Découvrez comment supprimer plusieurs enregistrements de base de données en une seule opération. Dans la couche d’Interface utilisateur, nous reposent sur un GridView améliorée créé dans une version tut..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/26/2007
ms.topic: article
ms.assetid: ac6916d0-a5ab-4218-9760-7ba9e72d258c
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/working-with-batched-data/batch-deleting-cs
msc.type: authoredcontent
ms.openlocfilehash: 71c4b323c2604d9e775f75272bb9565505580522
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="batch-deleting-c"></a>Traitement par lots de suppression (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/3/9/f/39f92b37-e92e-4ab3-909e-b4ef23d01aa3/ASPNET_Data_Tutorial_65_CS.zip) ou [télécharger le PDF](batch-deleting-cs/_static/datatutorial65cs1.pdf)

> Découvrez comment supprimer plusieurs enregistrements de base de données en une seule opération. Dans la couche d’Interface utilisateur, nous reposent sur un GridView améliorée créé dans un didacticiel antérieur. Dans la couche d’accès aux données, nous encapsuler les plusieurs opérations de suppression dans une transaction pour vous assurer que toutes les suppressions réussissent ou que toutes les suppressions sont annulées.


## <a name="introduction"></a>Introduction

Le [didacticiel précédent](batch-updating-cs.md) exploré la création d’un lot de modification d’interface à l’aide d’un GridView totalement modifiable. Dans les situations où les utilisateurs couramment modifiez plusieurs enregistrements à la fois, une interface de modification d’une sélection nécessiteront beaucoup moins publications (postback) et le contexte de clavier de la souris commutateurs, ce qui améliore l’efficacité de s utilisateur final. Cette technique est utile de la même façon pour les pages où il est courant pour les utilisateurs à supprimer le nombre d’enregistrements d’un coup.

Toute personne qui a utilisé un client de messagerie en ligne est déjà familiarisé avec l’un des plus courantes suppression par lots interfaces : une case à cocher de chaque ligne dans une grille avec un correspondant supprimer tous les éléments sélectionnés bouton (voir Figure 1). Ce didacticiel est assez court, car nous avons déjà fait tout le travail dans les didacticiels précédents lors de la création de l’interface web et une méthode pour supprimer une série d’enregistrements en une seule opération atomique. Dans le [Ajout d’une colonne de GridView des cases à cocher](../enhancing-the-gridview/adding-a-gridview-column-of-checkboxes-cs.md) didacticiel, nous avons créé un GridView avec une colonne de cases à cocher et le [encapsulant les Modifications de base de données d’une Transaction](wrapping-database-modifications-within-a-transaction-cs.md) didacticiel, nous avons créé une méthode dans la couche BLL par une transaction pour supprimer un `List<T>` de `ProductID` valeurs. Dans ce didacticiel, nous liées et nos expériences précédentes pour créer un exemple de suppression du lot de travail de fusion.


[![Chaque ligne inclut une case à cocher](batch-deleting-cs/_static/image1.gif)](batch-deleting-cs/_static/image1.png)

**Figure 1**: chaque ligne inclut une case à cocher ([cliquez pour afficher l’image en taille réelle](batch-deleting-cs/_static/image2.png))


## <a name="step-1-creating-the-batch-deleting-interface"></a>Étape 1 : Création du lot de suppression d’Interface

Étant donné que nous avons déjà créé le lot de suppression d’interface dans le [Ajout d’une colonne de GridView des cases à cocher](../enhancing-the-gridview/adding-a-gridview-column-of-checkboxes-cs.md) didacticiel, nous pouvons simplement copier `BatchDelete.aspx` au lieu de sa création à partir de zéro. Commencez par ouvrir le `BatchDelete.aspx` page dans le `BatchData` dossier et le `CheckBoxField.aspx` page dans le `EnhancedGridView` dossier. À partir de la `CheckBoxField.aspx` page, accédez à la vue de Source et copiez le balisage entre les `<asp:Content>` balises comme indiqué dans la Figure 2.


[![Copiez le balisage déclaratif de CheckBoxField.aspx dans le Presse-papiers](batch-deleting-cs/_static/image2.gif)](batch-deleting-cs/_static/image3.png)

**Figure 2**: copiez le balisage déclaratif de `CheckBoxField.aspx` dans le Presse-papiers ([cliquez pour afficher l’image en taille réelle](batch-deleting-cs/_static/image4.png))


Ensuite, accédez à la vue de Source de `BatchDelete.aspx` et collez le contenu du Presse-papiers dans le `<asp:Content>` balises. Également copier et coller le code à partir de la classe code-behind dans `CheckBoxField.aspx.cs` au sein de la classe code-behind dans `BatchDelete.aspx.cs` (le `DeleteSelectedProducts` bouton s `Click` Gestionnaire d’événements, le `ToggleCheckState` (méthode) et le `Click` gestionnaires d’événements pour le `CheckAll` et `UncheckAll` boutons). Après avoir copié sur ce contenu, la `BatchDelete.aspx` classe code-behind de la page s doit contenir le code suivant :


[!code-csharp[Main](batch-deleting-cs/samples/sample1.cs)]

Après avoir copié sur le balisage déclaratif et le code source, prenez un moment pour tester `BatchDelete.aspx` en l’affichant via un navigateur. Vous devez voir un GridView qui répertorie les dix premiers produits dans un GridView avec chaque ligne qui répertorie le nom de produit s, catégorie et prix, ainsi que d’une case à cocher. Il doit y avoir trois boutons : tout désélectionner tout et supprimer les produits sélectionnés. En cliquant sur le bouton Vérifier tous les de sélectionne toutes les cases à cocher, contrairement à désélectionner tout supprime toutes les cases à. En cliquant sur Supprimer des produits sélectionnés affiche un message qui répertorie les `ProductID` les valeurs des produits sélectionnés, mais ne supprime ne pas réellement les produits.


[![L’Interface à partir de CheckBoxField.aspx a été déplacé vers BatchDeleting.aspx](batch-deleting-cs/_static/image3.gif)](batch-deleting-cs/_static/image5.png)

**Figure 3**: l’Interface à partir de `CheckBoxField.aspx` a été déplacé vers `BatchDeleting.aspx` ([cliquez pour afficher l’image en taille réelle](batch-deleting-cs/_static/image6.png))


## <a name="step-2-deleting-the-checked-products-using-transactions"></a>Étape 2 : Supprimer les produits activés à l’aide de Transactions

Suppression d’interface correctement copiée sur le traitement du lot `BatchDeleting.aspx`, tous les que reste plus qu’à mettre à jour le code afin que le bouton Supprimer les produits sélectionnés supprime les produits activés à l’aide de la `DeleteProductsWithTransaction` méthode dans la `ProductsBLL` classe. Cette méthode, ajoutée dans le [encapsulant les Modifications de base de données d’une Transaction](wrapping-database-modifications-within-a-transaction-cs.md) didacticiel, accepte en entrée un `List<T>` de `ProductID` les valeurs et supprime chaque correspondants `ProductID` dans l’étendue d’un transaction.

Le `DeleteSelectedProducts` bouton s `Click` Gestionnaire d’événements utilise actuellement ce qui suit `foreach` pour effectuer une itération sur chaque ligne GridView :


[!code-csharp[Main](batch-deleting-cs/samples/sample2.cs)]

Pour chaque ligne, la `ProductSelector` contrôle de case à cocher Web est référencé par programmation. Si elle est activée, la ligne s `ProductID` est récupéré à partir de la `DataKeys` collection et la `DeleteResults` étiquette s `Text` propriété est mise à jour pour inclure un message indiquant que la ligne a été sélectionnée pour suppression.

Le code ci-dessus ne supprime pas réellement d’enregistrements en tant que l’appel à la `ProductsBLL` classe s `Delete` méthode est commentée. Ont été cette logique delete à appliquer, le code supprime les produits, mais pas dans une opération atomique. Autrement dit, si les suppressions peu premier dans la séquence a réussi, mais une version plus récente a échoué (peut-être en raison d’une violation de contrainte de clé étrangère), une exception est levée, mais ces produits déjà supprimés reste supprimés.

Afin de garantir l’atomicité, nous devons utiliser à la place la `ProductsBLL` classe s `DeleteProductsWithTransaction` (méthode). Étant donné que cette méthode accepte une liste de `ProductID` valeurs, nous devons d’abord compiler cette liste à partir de la grille et puis passez-le en tant que paramètre. Nous créons d’abord une instance d’un `List<T>` de type `int`. Dans le `foreach` boucle, nous devons ajouter les produits sélectionnés `ProductID` valeurs à cette `List<T>`. Après la boucle cela `List<T>` doit être passée à la `ProductsBLL` classe s `DeleteProductsWithTransaction` (méthode). Mise à jour la `DeleteSelectedProducts` bouton s `Click` Gestionnaire d’événements avec le code suivant :


[!code-csharp[Main](batch-deleting-cs/samples/sample3.cs)]

Le code de mise à jour crée un `List<T>` de type `int` (`productIDsToDelete`) et le remplit avec les `ProductID` valeurs à supprimer. Après le `foreach` de boucle, s’il existe au moins un produit sélectionné, le `ProductsBLL` classe s `DeleteProductsWithTransaction` méthode est appelée et reçoit de cette liste. Le `DeleteResults` étiquette s’affiche également et les données reliée au GridView (de sorte que les enregistrements qui vient d’être supprimés n’apparaissent plus comme des lignes dans la grille).

La figure 4 montre le contrôle GridView après qu’un nombre de lignes qui ont été sélectionné pour suppression. La figure 5 illustre l’écran immédiatement après la suppression des produits sélectionnés a cliqué. Notez que dans la Figure 5 la `ProductID` valeurs des enregistrements supprimés sont affichées dans l’étiquette située sous le contrôle GridView et les lignes ne sont plus dans le GridView.


[![Les produits sélectionnés seront supprimés.](batch-deleting-cs/_static/image4.gif)](batch-deleting-cs/_static/image7.png)

**Figure 4**: le sélectionné produits seront supprimés ([cliquez pour afficher l’image en taille réelle](batch-deleting-cs/_static/image8.png))


[![Les valeurs de ProductID produits supprimés sont répertoriés sous le GridView.](batch-deleting-cs/_static/image5.gif)](batch-deleting-cs/_static/image9.png)

**Figure 5**: les produits supprimés `ProductID` les valeurs sont répertoriées sous GridView ([cliquez pour afficher l’image en taille réelle](batch-deleting-cs/_static/image10.png))


> [!NOTE]
> Pour tester le `DeleteProductsWithTransaction` atomicité de la méthode s, ajoutez manuellement une entrée pour un produit dans le `Order Details` de table, puis que vous essayez de supprimer ce produit (ainsi que d’autres utilisateurs). Vous recevrez une violation de contrainte de clé étrangère lorsque vous tentez de supprimer le produit avec une commande associée, mais notez comment les autres suppressions de produits sélectionnés sont restaurées.


## <a name="summary"></a>Résumé

Création d’un lot de suppression d’interface implique l’ajout d’un GridView avec une colonne de cases à cocher et un bouton de serveur Web de contrôle qui, lorsque vous cliquez sur, supprime toutes les lignes sélectionnées en une seule opération atomique. Dans ce didacticiel, nous avons créé une telle interface par le travail effectué dans deux didacticiels précédents, réorganisation [Ajout d’une colonne de GridView des cases à cocher](../enhancing-the-gridview/adding-a-gridview-column-of-checkboxes-cs.md) et [encapsulant les Modifications de base de données d’une Transaction](wrapping-database-modifications-within-a-transaction-cs.md). Dans le premier didacticiel, nous avons créé un GridView avec une colonne de cases à cocher et dans le second nous avons implémenté la méthode dans la couche BLL qui, lorsqu’il est passé un `List<T>` de `ProductID` valeurs supprimés intégralement dans le cadre d’une transaction.

Dans l’étape suivante du didacticiel, nous allons créer une interface permettant d’effectuer des insertions du lot.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Hilton Giesenow et Teresa Murphy. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](batch-updating-cs.md)
[Suivant](batch-inserting-cs.md)
