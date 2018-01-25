---
uid: web-forms/overview/data-access/accessing-the-database-directly-from-an-aspnet-page/implementing-optimistic-concurrency-with-the-sqldatasource-cs
title: "Implémentation de l’accès concurrentiel optimiste avec le SqlDataSource (c#) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous allons examiner l’essentiel de contrôle d’accès concurrentiel optimiste et puis examiner comment implémenter à l’aide du contrôle SqlDataSource."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2007
ms.topic: article
ms.assetid: df999966-ac48-460e-b82b-4877a57d6ab9
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/accessing-the-database-directly-from-an-aspnet-page/implementing-optimistic-concurrency-with-the-sqldatasource-cs
msc.type: authoredcontent
ms.openlocfilehash: b089a0b25aa5a520f3e20af8ec5212072ad7c7bf
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="implementing-optimistic-concurrency-with-the-sqldatasource-c"></a>Implémentation de l’accès concurrentiel optimiste avec le SqlDataSource (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/4/a/7/4a7a3b18-d80e-4014-8e53-a6a2427f0d93/ASPNET_Data_Tutorial_50_CS.exe) ou [télécharger le PDF](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/datatutorial50cs1.pdf)

> Dans ce didacticiel, nous allons examiner l’essentiel de contrôle d’accès concurrentiel optimiste et puis examiner comment implémenter à l’aide du contrôle SqlDataSource.


## <a name="introduction"></a>Introduction

Dans le didacticiel précédent, nous avons examiné comment ajouter d’insertion, de mise à jour et de suppression de fonctionnalités pour le contrôle SqlDataSource. En résumé, pour fournir ces fonctionnalités nous nécessaires afin de spécifier le correspondant `INSERT`, `UPDATE`, ou `DELETE` instruction SQL dans le contrôle s `InsertCommand`, `UpdateCommand`, ou `DeleteCommand` propriétés, ainsi que les paramètres appropriés les paramètres dans le `InsertParameters`, `UpdateParameters`, et `DeleteParameters` collections. Lors de ces propriétés et les collections peuvent être spécifiées manuellement, le bouton Avancé s de l’Assistant Configurer la Source de données offre une génération `INSERT`, `UPDATE`, et `DELETE` case à cocher des instructions qui sera création automatique de ces instructions en fonction de la `SELECT` instruction.

En même temps que la génération `INSERT`, `UPDATE`, et `DELETE` instructions case à cocher, la boîte de dialogue Options de génération SQL avancées inclut une option d’utilisation d’accès concurrentiel optimiste (voir Figure 1). Lorsqu’elle est activée, le `WHERE` clauses dans la générées automatiquement `UPDATE` et `DELETE` instructions ont été modifiées pour exécuter uniquement la mise à jour ou de suppression si t ne données base de données sous-jacente été modifiée depuis l’utilisateur dernier chargée des données dans la grille.


![Vous pouvez ajouter la prise en charge de l’accès concurrentiel optimiste d’avancées boîte de dialogue Options de génération SQL](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image1.gif)

**Figure 1**: vous pouvez ajouter la prise en charge de l’accès concurrentiel optimiste d’avancées boîte de dialogue Options de génération SQL


Dans le [implémentation de l’accès concurrentiel optimiste](../editing-inserting-and-deleting-data/implementing-optimistic-concurrency-cs.md) didacticiel, nous avons examiné les principes fondamentaux de contrôle d’accès concurrentiel optimiste et comment l’ajouter à ObjectDataSource. Dans ce didacticiel nous retoucher sur l’essentiel de contrôle d’accès concurrentiel optimiste et puis examiner comment implémenter le SqlDataSource à l’aide de.

## <a name="a-recap-of-optimistic-concurrency"></a>Un récapitulatif de l’accès concurrentiel optimiste

Pour les applications web qui permettent à plusieurs utilisateurs simultanés pour modifier ou supprimer les mêmes données, il existe un risque qu’un utilisateur peut remplacer accidentellement des modifications s un autre. Dans le [implémentation de l’accès concurrentiel optimiste](../editing-inserting-and-deleting-data/implementing-optimistic-concurrency-cs.md) didacticiel I fourni à l’exemple suivant :

Imaginez que deux utilisateurs, Jisun et Sam, ont tous deux accédant à une page dans une application qui a autorisé les visiteurs de mettre à jour et supprimer des produits via un contrôle GridView. Cliquez sur le bouton Modifier pour Tran en même temps. Jisun modifie le nom du produit pour Tran thé et clique sur le bouton de mise à jour. Le résultat net est un `UPDATE` instruction est envoyée à la base de données définit *tous les* des champs modifiables produit s (même si Jisun mise à jour uniquement un champ, `ProductName`). À ce stade, la base de données a les valeurs Tran thé, la catégorie boissons, liquides exotiques fournisseur, et ainsi de suite de ce produit particulier. Toutefois, le contrôle GridView à l’écran de s Sam indique toujours le nom du produit dans la ligne GridView modifiable en tant que Tran. Quelques secondes après que les modifications de s Jisun ont été validées, Sam mises à jour de la catégorie à Condiments et clique sur la mise à jour. Cela entraîne une `UPDATE` instruction envoyée à la base de données qui définit le nom du produit pour Tran, le `CategoryID` à l’ID de catégorie Condiments correspondant et ainsi de suite. Modifications de s Jisun au nom du produit ont été remplacées.

La figure 2 illustre cette interaction.


[![Lorsque deux utilisateurs mettre à jour simultanément un enregistrement il s pour un seul utilisateur s les modifications potentielles pour remplacer les autres s](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image2.gif)](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image1.png)

**Figure 2**: lorsque deux utilisateurs simultanément mise à jour un enregistrement il s potentiel pour un seul utilisateur s modifie pour remplacer les autres s ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image2.png))


Pour empêcher le dépliage, un formulaire de ce scénario [contrôle d’accès concurrentiel](http://en.wikipedia.org/wiki/Concurrency_control) doit être implémentée. [L’accès concurrentiel optimiste](http://en.wikipedia.org/wiki/Optimistic_concurrency_control) l’objectif de ce didacticiel fonctionne sur l’hypothèse que les conflits d’accès concurrentiel peut être ainsi, la plupart du temps ces conflits a gagné t surviennent. Par conséquent, en cas de conflit, contrôle d’accès concurrentiel optimiste simplement informe l’utilisateur enregistrer leur modifications impossible, car un autre utilisateur a modifié les mêmes données.

> [!NOTE]
> Pour les applications où il est supposé qu’il y aura de conflits d’accès concurrentiel ou si ces conflits ne sont pas acceptable, puis contrôle d’accès concurrentiel pessimiste peut être utilisé à la place. Faire référence à la [implémentation de l’accès concurrentiel optimiste](../editing-inserting-and-deleting-data/implementing-optimistic-concurrency-cs.md) didacticiel pour une discussion plus approfondie sur le contrôle d’accès concurrentiel pessimiste.


Contrôle d’accès concurrentiel optimiste de s’assurer que l’enregistrement en cours de mise à jour ou supprimé possède les mêmes valeurs, comme il était au moment de démarrer la mise à jour ou la suppression du processus. Par exemple, lorsque vous cliquez sur le bouton Modifier dans un GridView modifiable, les valeurs de l’enregistrement s sont lues à partir de la base de données et affichés dans les zones de texte et autres contrôles Web. Ces valeurs d’origine sont enregistrés par le contrôle GridView. Plus tard, une fois l’utilisateur effectue ses modifications et qu’il clique sur le bouton de mise à jour, la `UPDATE` instruction utilisée doit prendre en compte les valeurs d’origine ainsi que les nouvelles valeurs et uniquement mettre à jour l’enregistrement de base de données sous-jacent si les valeurs d’origine que l’utilisateur a commencé à modifier sont identiques aux valeurs toujours dans la base de données. La figure 3 illustre cette séquence d’événements.


[![Pour la mise à jour ou la suppression aboutisse, les valeurs d’origine doivent être égales aux valeurs de base de données en cours](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image3.gif)](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image3.png)

**Figure 3**: pour la mise à jour ou supprimer pour réussir, le d’origine valeurs doit être égale aux valeurs de base de données ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image4.png))


Il existe différentes approches pour implémenter l’accès concurrentiel optimiste (consultez [Peter A. Bromberg](http://www.eggheadcafe.com/articles/pbrombergresume.asp) s [logique de mise à jour d’accès concurrentiel Optmistic](http://www.eggheadcafe.com/articles/20050719.asp) pour un certain nombre d’options brièvement). La technique utilisée par le SqlDataSource (ainsi que par le typés ADO.NET utilisé dans la couche d’accès aux données) augmente la `WHERE` clause pour inclure une comparaison de toutes les valeurs d’origine. Les éléments suivants `UPDATE` instruction, par exemple, des mises à jour le nom et le prix d’un produit uniquement si les valeurs actuelles de la base de données sont égales aux valeurs qui ont été récupérées à l’origine lors de la mise à jour de l’enregistrement dans le GridView. Le `@ProductName` et `@UnitPrice` paramètres contiennent les nouvelles valeurs entrées par l’utilisateur, tandis que `@original_ProductName` et `@original_UnitPrice` contiennent les valeurs qui ont été chargées à l’origine dans le GridView, lorsque l’utilisateur a cliqué sur le bouton Modifier :


[!code-sql[Main](implementing-optimistic-concurrency-with-the-sqldatasource-cs/samples/sample1.sql)]

Comme nous allons le voir dans ce didacticiel, l’activation du contrôle d’accès concurrentiel optimiste avec le SqlDataSource est aussi simple que la vérification de la case à cocher.

## <a name="step-1-creating-a-sqldatasource-that-supports-optimistic-concurrency"></a>Étape 1 : Création d’un SqlDataSource qui prend en charge l’accès concurrentiel optimiste

Commencez par ouvrir le `OptimisticConcurrency.aspx` page à partir de la `SqlDataSource` dossier. Faites glisser un contrôle SqlDataSource à partir de la boîte à outils vers le concepteur, les paramètres de son `ID` propriété `ProductsDataSourceWithOptimisticConcurrency`. Ensuite, cliquez sur le lien configurer la Source de données à partir de la balise active de contrôle s. Dans le premier écran de l’Assistant, choisissez de travailler avec le `NORTHWINDConnectionString` et cliquez sur Suivant.


[![Choisissez pour une utilisation avec le NORTHWINDConnectionString](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image4.gif)](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image5.png)

**Figure 4**: choisissez d’utiliser le `NORTHWINDConnectionString` ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image6.png))


Pour cet exemple, nous allons ajouter un GridView qui permet aux utilisateurs de modifier le `Products` table. Par conséquent, à partir de la configuration de l’écran de l’instruction Select, choisissez le `Products` de table dans la liste déroulante et sélectionnez le `ProductID`, `ProductName`, `UnitPrice`, et `Discontinued` des colonnes, comme indiqué dans la Figure 5.


[![Revenir à partir de la Table Products, ProductID, ProductName, UnitPrice et les colonnes supprimées](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image5.gif)](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image7.png)

**Figure 5**: à partir de la `Products` Table, retournez le `ProductID`, `ProductName`, `UnitPrice`, et `Discontinued` colonnes ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image8.png))


Après avoir sélectionné les colonnes, cliquez sur le bouton Avancé pour afficher la boîte de dialogue Options de génération SQL avancées. Vérifiez la génération `INSERT`, `UPDATE`, et `DELETE` instructions et utilisez les cases à cocher de l’accès concurrentiel optimiste, puis cliquez sur OK (voir Figure 1 pour une capture d’écran). Terminez l’Assistant en cliquant sur Suivant, puis sur Terminer.

Après avoir terminé l’Assistant Configurer la Source de données, prenez un moment pour examiner les résultats `DeleteCommand` et `UpdateCommand` propriétés et le `DeleteParameters` et `UpdateParameters` collections. Pour ce faire, le plus simple consiste à cliquer sur l’onglet Source dans l’angle inférieur gauche pour afficher la syntaxe déclarative s de page. Vous y trouverez un `UpdateCommand` valeur de :


[!code-sql[Main](implementing-optimistic-concurrency-with-the-sqldatasource-cs/samples/sample2.sql)]

Avec sept paramètres dans le `UpdateParameters` collection :


[!code-aspx[Main](implementing-optimistic-concurrency-with-the-sqldatasource-cs/samples/sample3.aspx)]

De même, la `DeleteCommand` propriété et `DeleteParameters` collection doit se présenter comme suit :


[!code-sql[Main](implementing-optimistic-concurrency-with-the-sqldatasource-cs/samples/sample4.sql)]


[!code-aspx[Main](implementing-optimistic-concurrency-with-the-sqldatasource-cs/samples/sample5.aspx)]

En plus de l’augmentation de la `WHERE` clauses de la `UpdateCommand` et `DeleteCommand` propriétés (et ajout de paramètres supplémentaires pour les collections de paramètres respectifs), en sélectionnant l’utilisation optimiste option d’accès concurrentiel s’ajuste à deux autres propriétés :

- Modifications du [ `ConflictDetection` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.sqldatasource.conflictdetection.aspx) de `OverwriteChanges` (la valeur par défaut) pour`CompareAllValues`
- Modifications du [ `OldValuesParameterFormatString` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.sqldatasource.oldvaluesparameterformatstring.aspx) à partir de {0} (la valeur par défaut) à original\_{0}.

Lorsque les données de contrôle Web appelant le SqlDataSource s `Update()` ou `Delete()` (méthode), il transmet les valeurs d’origine. Si les opérations de mappage SqlDataSource `ConflictDetection` est définie sur `CompareAllValues`, ces valeurs d’origine sont ajoutées à la commande. Le `OldValuesParameterFormatString` propriété fournit le modèle d’affectation de noms utilisé pour ces paramètres à valeur d’origine. L’Assistant Configurer la Source de données utilise d’origine\_{0} et les noms de chaque paramètre d’origine dans le `UpdateCommand` et `DeleteCommand` propriétés et `UpdateParameters` et `DeleteParameters` collections en conséquence.

> [!NOTE]
> Étant donné que nous n’utilisez ne pas les opérations de mappage contrôle SqlDataSource insertion des fonctions, n’hésitez pas à supprimer la `InsertCommand` propriété et sa `InsertParameters` collection.


## <a name="correctly-handlingnullvalues"></a>Gérer correctement`NULL`valeurs

Malheureusement, l’augmentée `UPDATE` et `DELETE` générées automatiquement les instructions par l’Assistant Configurer la Source de données lors de l’utilisation de l’accès concurrentiel optimiste faire *pas* fonctionnent avec les enregistrements qui contiennent `NULL` valeurs. Pour comprendre pourquoi, envisagez de notre s SqlDataSource `UpdateCommand`:


[!code-sql[Main](implementing-optimistic-concurrency-with-the-sqldatasource-cs/samples/sample6.sql)]

Le `UnitPrice` colonne dans la `Products` table peut avoir `NULL` valeurs. Si un enregistrement particulier a un `NULL` valeur `UnitPrice`, le `WHERE` partie de la clause `[UnitPrice] = @original_UnitPrice` sera *toujours* ont la valeur False, car `NULL = NULL` retourne toujours False. Par conséquent, les enregistrements qui contiennent `NULL` valeurs ne peut pas être modifiés ou supprimés, comme le `UPDATE` et `DELETE` instructions `WHERE` clauses a gagné t retournent toutes les lignes pour mettre à jour ou supprimer.

> [!NOTE]
> Ce bogue a été signalé tout d’abord à Microsoft en juin 2004 dans [SqlDataSource génère des instructions SQL inexactes](https://connect.microsoft.com/VisualStudio/feedback/ViewFeedback.aspx?FeedbackID=93937) et est planifié pouvaient être corrigé dans la prochaine version de ASP.NET.


Pour résoudre ce problème, nous devons mettre à jour manuellement la `WHERE` clauses à la fois dans le `UpdateCommand` et `DeleteCommand` propriétés **tous les** colonnes pouvant avoir `NULL` valeurs. En général, modifier `[ColumnName] = @original_ColumnName` à :


[!code-sql[Main](implementing-optimistic-concurrency-with-the-sqldatasource-cs/samples/sample7.sql)]

Cette modification peut être créé directement par le biais de la balise déclarative, via les options UpdateQuery ou DeleteQuery à partir de la fenêtre Propriétés, ou la mise à jour et supprimer des onglets dans la spécifier une instruction SQL personnalisée ou une option de procédure stockée dans les données de configuration Assistant source. Là encore, cette modification doit être effectuée pour *chaque* colonne dans la `UpdateCommand` et `DeleteCommand` s `WHERE` clause pouvant contenir `NULL` valeurs.

Cette application à notre exemple donne modifié `UpdateCommand` et `DeleteCommand` valeurs :


[!code-sql[Main](implementing-optimistic-concurrency-with-the-sqldatasource-cs/samples/sample8.sql)]

## <a name="step-2-adding-a-gridview-with-edit-and-delete-options"></a>Étape 2 : Ajout d’un GridView avec l’édition et les Options de suppression

Avec le SqlDataSource configuré pour prendre en charge l’accès concurrentiel optimiste, il reste qu’à ajouter un contrôle Web de données à la page qui utilise ce contrôle d’accès concurrentiel. Pour ce didacticiel, permettent d’ajouter un GridView qui fournit à la fois modifier et supprimer des fonctionnalités s. Pour ce faire, faites glisser un contrôle GridView à partir de la boîte à outils vers le concepteur et un ensemble de ses `ID` à `Products`. À partir de la balise active de s GridView, liez-le à la `ProductsDataSourceWithOptimisticConcurrency` contrôle SqlDataSource ajouté à l’étape 1. Enfin, vérifiez les options Activer la modification et activer la suppression de la balise active.


[![Lier le contrôle GridView au SqlDataSource et activer la modification et suppression](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image6.gif)](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image9.png)

**Figure 6**: lier le contrôle GridView à SqlDataSource et d’activer la modification et de suppression ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image10.png))


Après avoir ajouté le GridView, configurez son apparence en supprimant le `ProductID` BoundField, modification de la `ProductName` BoundField s `HeaderText` propriété produit et la mise à jour le `UnitPrice` BoundField afin que ses `HeaderText` propriété est Il vous suffit de prix. Dans l’idéal, nous d améliorer l’interface de modification pour inclure un contrôle RequiredFieldValidator pour le `ProductName` valeur et un CompareValidator pour le `UnitPrice` valeur (pour s’assurer qu’elle s une valeur numérique au format correct). Reportez-vous à la [personnalisation de l’Interface de Modification de données](../editing-inserting-and-deleting-data/customizing-the-data-modification-interface-cs.md) didacticiel pour une plus approfondies sur personnalisation s GridView modification d’interface.

> [!NOTE]
> Le contrôle GridView état d’affichage s doit être activée dans la mesure où les valeurs d’origine passées du contrôle GridView au SqlDataSource sont stockées dans la vue état.


Après avoir apporté ces modifications au contrôle GridView, le balisage déclaratif GridView et SqlDataSource doit ressembler à ce qui suit :


[!code-aspx[Main](implementing-optimistic-concurrency-with-the-sqldatasource-cs/samples/sample9.aspx)]

Pour afficher le contrôle d’accès concurrentiel optimiste en action, ouvrez deux fenêtres de navigateur et charger la `OptimisticConcurrency.aspx` page dans les deux. Cliquez sur les boutons de modification pour le premier produit de deux navigateurs. Dans un navigateur, modifiez le nom de produit et cliquez sur la mise à jour. Le navigateur est publiée et le contrôle GridView retourne en mode édition préalable, montrant le nouveau nom de produit pour l’enregistrement venez de modifier.

Dans la deuxième fenêtre de navigateur, modifiez le prix (mais laisser le nom du produit en tant que sa valeur d’origine), puis cliquez sur mise à jour. Lors de la publication, la grille retourne à son mode de modification préalable, mais la modification au prix n’est pas enregistrée. Le second navigateur montre le nouveau nom du produit avec l’ancien prix à la même valeur que le premier. Les modifications apportées dans la deuxième fenêtre de navigateur ont été perdues. En outre, les modifications ont été perdues plutôt en mode silencieux, car il a été aucune exception ou un message indiquant qu’une violation d’accès concurrentiel simplement s’est produite.


[![Les modifications dans la deuxième fenêtre de navigateur ont été perdues en mode silencieux](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image7.gif)](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image11.png)

**Figure 7**: les modifications dans la deuxième navigateur fenêtre ont été silencieusement perdus ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image12.png))


Pourquoi les modifications de navigateur s deuxième n’ont pas étées était dû, car le `UPDATE` instruction s `WHERE` clause filtré tous les enregistrements et par conséquent n’a affecté aucune ligne. S permettent d’examiner le `UPDATE` instruction à nouveau :


[!code-sql[Main](implementing-optimistic-concurrency-with-the-sqldatasource-cs/samples/sample10.sql)]

Lorsque la deuxième fenêtre de navigateur met à jour l’enregistrement, le nom de produit d’origine spécifié dans le `WHERE` clause ne t correspondance configuration avec le nom de produit existant (dans la mesure où il a été modifié par le navigateur en premier). Par conséquent, l’instruction `[ProductName] = @original_ProductName` renvoie la valeur False et le `UPDATE` n’affecte pas d’enregistrement.

> [!NOTE]
> Fonctionnement de Delete de la même manière. Avec deux fenêtres de navigateur ouverts, démarrez en modifiant un produit donné à l’aide, puis en enregistrant les modifications. Après avoir enregistré les modifications dans le navigateur, cliquez sur le bouton Supprimer pour le même produit dans l’autre. Étant donné que le don de valeurs d’origine t correspondre dans le `DELETE` instruction s `WHERE` clause, la suppression échoue en mode silencieux.


L’utilisateur final s du point de vue dans la deuxième fenêtre de navigateur, après avoir cliqué sur le bouton de mise à jour la grille retourne au mode d’édition avant, mais leurs modifications ont été perdues. Toutefois, cet emplacement s aucun retour visuel qui leur t n’a de modifications coller. Dans l’idéal, si un utilisateur s modifications sont perdues une violation d’accès concurrentiel, nous d les notifier et, peut-être conserver la grille en mode édition. Permettent de s examiner comment accomplir cette tâche.

## <a name="step-3-determining-when-a-concurrency-violation-has-occurred"></a>Étape 3 : Déterminer quand une Violation d’accès concurrentiel s’est produite

Dans la mesure où une violation d’accès concurrentiel rejette les modifications a été, il serait intéressant avertir l’utilisateur lorsqu’une violation d’accès concurrentiel s’est produite. Pour avertir l’utilisateur, s permettent d’ajouter un contrôle Web Label en haut de la page nommée `ConcurrencyViolationMessage` dont `Text` propriété affiche le message suivant : vous avez tenté de mettre à jour ou supprimer un enregistrement qui a été mises à jour simultanément par un autre utilisateur. Passez en revue les modifications de l’autre utilisateur puis rétablir votre mise à jour ou supprimer. Définir le contrôle d’étiquette s `CssClass` propriété à avertissement, qui est une classe CSS définie dans `Styles.css` qui affiche le texte dans une police rouge, italique, gras et volumineuse. Enfin, affectez à l’étiquette s `Visible` et `EnableViewState` propriétés `false`. Cela permet de masquer l’étiquette, à l’exception uniquement ces publications où nous avons défini explicitement son `Visible` propriété `true`.


[![Ajoutez un contrôle Label à la Page pour afficher l’avertissement](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image8.gif)](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image13.png)

**Figure 8**: ajouter un contrôle Label à la Page pour afficher l’avertissement ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image14.png))


Lorsque vous effectuez une mise à jour ou de suppression, le s GridView `RowUpdated` et `RowDeleted` gestionnaires d’événements sont activés après son contrôle de source de données a effectué la mise à jour demandée ou la suppression. Nous pouvons déterminer le nombre de lignes affecté par l’opération à partir de ces gestionnaires d’événements. Si aucune ligne ont été affectés, nous voulons afficher le `ConcurrencyViolationMessage` étiquette.

Créez un gestionnaire d’événements pour les deux le `RowUpdated` et `RowDeleted` événements et ajoutez le code suivant :


[!code-csharp[Main](implementing-optimistic-concurrency-with-the-sqldatasource-cs/samples/sample11.cs)]

Dans les deux gestionnaires d’événements, nous vérifions le `e.AffectedRows` propriété et, si elle est égale à 0, définissez la `ConcurrencyViolationMessage` étiquette s `Visible` propriété `true`. Dans le `RowUpdated` Gestionnaire d’événements, nous indiquons également le contrôle GridView à rester en mode édition, en définissant le `KeepInEditMode` true à la propriété. Ce faisant, nous devons lier de nouveau les données à la grille afin que les autres données de l’utilisateur se sont chargées dans l’interface de modification. Cela est accompli en appelant le s GridView `DataBind()` (méthode).

Comme le montre la Figure 9, ces deux gestionnaires d’événements, un message évidente s’affiche chaque fois qu’une violation d’accès concurrentiel se produit.


[![Un Message s’affiche en cas de défaillance une Violation d’accès concurrentiel](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image9.gif)](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image15.png)

**Figure 9**: un Message s’affiche en cas de défaillance une Violation d’accès concurrentiel ([cliquez pour afficher l’image en taille réelle](implementing-optimistic-concurrency-with-the-sqldatasource-cs/_static/image16.png))


## <a name="summary"></a>Récapitulatif

Lorsque vous créez une application web où de multiples utilisateurs peuvent modifier les mêmes données, il est important de considérer les options de contrôle d’accès concurrentiel. Par défaut, les données ASP.NET de contrôles Web et les contrôles de source de données n’appliquent pas de n’importe quel contrôle d’accès concurrentiel. Comme nous l’avons vu dans ce didacticiel, l’implémentation de contrôle d’accès concurrentiel optimiste avec le SqlDataSource est relativement simple et rapide. Le SqlDataSource gère la plupart du gros du travail pour votre ajout augmentée `WHERE` clauses pour le générées automatiquement `UPDATE` et `DELETE` , mais les instructions sont quelques subtilités dans la gestion des `NULL` valeur des colonnes, comme indiqué dans les La gestion correcte de `NULL` les valeurs de section.

Ce didacticiel conclut notre examen du SqlDataSource. Nos didacticiels restants retournera à l’utilisation des données à l’aide de la ObjectDataSource et l’architecture à plusieurs niveaux.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

>[!div class="step-by-step"]
[Précédent](inserting-updating-and-deleting-data-with-the-sqldatasource-cs.md)
[Suivant](querying-data-with-the-sqldatasource-control-vb.md)
