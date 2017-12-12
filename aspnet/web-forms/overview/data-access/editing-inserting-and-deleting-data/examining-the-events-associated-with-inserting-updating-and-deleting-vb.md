---
uid: web-forms/overview/data-access/editing-inserting-and-deleting-data/examining-the-events-associated-with-inserting-updating-and-deleting-vb
title: "Examiner les événements associés d’insertion, de mise à jour et de suppression (VB) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, que nous allons aborder l’utilisation des événements qui se produisent avant, pendant et après une instruction insert, update ou delete de l’opération d’un contrôle Web de données ASP.NET. W...."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/17/2006
ms.topic: article
ms.assetid: c9bd10a7-eff8-4d8c-bec9-963c2aef2d6e
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/editing-inserting-and-deleting-data/examining-the-events-associated-with-inserting-updating-and-deleting-vb
msc.type: authoredcontent
ms.openlocfilehash: 5daa9d1fe63e4ad8ec8c667f84de00fadd77fefa
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="examining-the-events-associated-with-inserting-updating-and-deleting-vb"></a>Examiner les événements liés à l’insertion, de mise à jour et de suppression (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_17_VB.exe) ou [télécharger le PDF](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/datatutorial17vb1.pdf)

> Dans ce didacticiel, que nous allons aborder l’utilisation des événements qui se produisent avant, pendant et après une instruction insert, update ou delete de l’opération d’un contrôle Web de données ASP.NET. Nous verrons également comment personnaliser l’interface de modification pour mettre à jour uniquement un sous-ensemble des champs de produit.


## <a name="introduction"></a>Introduction

Lorsque vous utilisez l’intégrées d’insertion, la modification ou la suppression des fonctionnalités des contrôles GridView, DetailsView ou FormView, diverses étapes s’écouler lorsque l’utilisateur final termine le processus d’ajout d’un nouvel enregistrement ou la mise à jour ou suppression d’un enregistrement existant. Comme expliqué dans la [didacticiel précédent](an-overview-of-inserting-updating-and-deleting-data-vb.md), lorsqu’une ligne est modifiée dans le GridView, le bouton Modifier est remplacé par la mise à jour et annuler des boutons et l’activer de BoundFields dans les zones de texte. Une fois que l’utilisateur final met à jour les données et clique sur la mise à jour, les étapes suivantes sont effectuées lors de la publication :

1. Le contrôle GridView remplit son ObjectDataSource `UpdateParameters` avec un ou plusieurs champs identifiant unique de l’enregistrement modifié (via la `DataKeyNames` propriété), ainsi que les valeurs entrées par l’utilisateur
2. Le contrôle GridView appelle son ObjectDataSource `Update()` (méthode), qui à son tour appelle la méthode appropriée dans l’objet sous-jacent (`ProductsDAL.UpdateProduct`, dans notre didacticiel précédent)
3. Les données sous-jacentes, qui inclut maintenant les modifications de mise à jour, sont reliées au contrôle GridView

Au cours de cette séquence d’étapes, un nombre d’événements sont activés, ce qui nous permet de créer des gestionnaires d’événements pour ajouter une logique personnalisée lorsque cela est nécessaire. Par exemple, avant le d’étape 1, le contrôle GridView `RowUpdating` se déclenche des événements. Nous pouvons, à ce stade, annuler la demande de mise à jour s’il existe une erreur de validation. Lorsque le `Update()` méthode est appelée, l’ObjectDataSource `Updating` se déclenche l’événement, une possibilité d’ajouter ou personnaliser les valeurs de toutes les `UpdateParameters`. ObjectDataSource sous-jacent de la méthode de l’objet la fin de l’exécution, l’ObjectDataSource `Updated` événement est déclenché. Un gestionnaire d’événements pour le `Updated` événement peut examiner les détails de l’opération de mise à jour, telles que le nombre de lignes affecté et indique si une exception s’est produite. Enfin, après le d’étape 2, le contrôle GridView `RowUpdated` événement se déclenche ; un gestionnaire d’événements pour l’événement peut examiner des informations supplémentaires sur l’opération de mise à jour seulement effectuées.

La figure 1 illustre cette série d’événements et des étapes lors de la mise à jour d’un GridView. Le modèle d’événement dans la Figure 1 n’est pas unique pour la mise à jour avec un GridView. Insertion, mise à jour ou suppression de données dans le GridView, DetailsView ou FormView précipite la même séquence d’événements antérieurs et postérieurs au niveau à la fois le données Web et le contrôle ObjectDataSource.


[![Série a avant et après des événements sont déclenchés lors de la mise à jour des données dans un contrôle GridView](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image2.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image1.png)

**Figure 1**: une série de pré- et les post-événements incendie lors de la mise à jour des données dans un GridView ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image3.png))


Dans ce didacticiel, que nous allons aborder l’utilisation de ces événements pour étendre l’intégrées d’insertion, mise à jour et suppression de capacités des données ASP.NET Web contrôle. Nous verrons également comment personnaliser l’interface de modification pour mettre à jour uniquement un sous-ensemble des champs de produit.

## <a name="step-1-updating-a-productsproductnameandunitpricefields"></a>Étape 1 : Mise à jour d’un produit`ProductName`et`UnitPrice`champs

Dans les interfaces de modification à partir du didacticiel précédent *tous les* champs produit qui n’étaient pas en lecture seule devait être inclus. Si vous deviez supprimer un champ dans le contrôle GridView - dire `QuantityPerUnit` : lors de la mise à jour les données de contrôle Web données définiriez pas l’ObjectDataSource `QuantityPerUnit` `UpdateParameters` valeur. ObjectDataSource puis de passer une valeur de `Nothing` dans les `UpdateProduct` méthode couche BLL (Business Logic), ce qui pourrait être modifié l’enregistrement de base de données modifiées `QuantityPerUnit` colonne à un `NULL` valeur. De même, si un champ obligatoire, tel que `ProductName`, est supprimé à partir de l’interface de modification, la mise à jour échoue avec un «*colonne 'ProductName' n’autorise pas les valeurs null*« exception. La raison de ce comportement a été car ObjectDataSource a été configuré pour appeler le `ProductsBLL` de classe `UpdateProduct` méthode attendu d’un paramètre d’entrée pour chacun des champs de produit. Par conséquent, l’ObjectDataSource `UpdateParameters` collection contenue un paramètre pour chacun de la méthode d’entrée de paramètres.

Si nous voulons fournir un contrôle Web qui permet à l’utilisateur final à mettre à jour uniquement un sous-ensemble des champs de données, nous devons définir par programme manquants `UpdateParameters` valeurs dans l’ObjectDataSource `Updating` Gestionnaire d’événements ou de créer et appeler une méthode de la couche BLL qui attend uniquement un sous-ensemble des champs. Nous allons explorer cette dernière approche.

Plus précisément, nous allons créer une page qui affiche uniquement le `ProductName` et `UnitPrice` champs dans un GridView modifiable. Interface de modification de cette GridView permet uniquement à l’utilisateur à mettre à jour les deux champs affichés, `ProductName` et `UnitPrice`. Étant donné que cette interface de modification fournit uniquement un sous-ensemble des champs d’un produit, nous devons soit créer un ObjectDataSource qui utilise la couche BLL existante `UpdateProduct` (méthode) et les valeurs de champ produit manquant a défini par programmation dans son `Updating` événement Gestionnaire ou que nous avons besoin créer une nouvelle méthode BLL qui attend uniquement le sous-ensemble des champs définis dans le GridView. Pour ce didacticiel, nous allons utiliser cette dernière option et créer une surcharge de la `UpdateProduct` (méthode), qui prend seulement trois paramètres d’entrée : `productName`, `unitPrice`, et `productID`:


[!code-vb[Main](examining-the-events-associated-with-inserting-updating-and-deleting-vb/samples/sample1.vb)]

Comme l’original `UpdateProduct` cette surcharge de méthode, démarre en vérifiant s’il existe un produit dans la base de données avec l’objet `ProductID`. Si non, elle retourne `False`, ce qui indique que la demande pour mettre à jour les informations de produit a échoué. Sinon, il met à jour l’enregistrement existant produit `ProductName` et `UnitPrice` champs en conséquence et valide la mise à jour en appelant le TableAdpater `Update()` méthode, en passant le `ProductsRow` instance.

Avec cet ajout à notre `ProductsBLL` (classe), nous sommes prêts créer l’interface GridView simplifiée. Ouvrez le `DataModificationEvents.aspx` dans le `EditInsertDelete` dossier et ajouter un contrôle GridView à la page. Créer un nouveau ObjectDataSource et le configurer pour utiliser le `ProductsBLL` classe avec son `Select()` mappage de la méthode à `GetProducts` et son `Update()` mappage de la méthode à la `UpdateProduct` surcharge qui accepte uniquement dans le `productName`, `unitPrice`, et `productID` paramètres d’entrée. La figure 2 illustre l’Assistant créer une Source de données lors du mappage de l’ObjectDataSource `Update()` méthode à la `ProductsBLL` classe du nouveau `UpdateProduct` surcharge de méthode.


[![Mapper Update() méthode l’ObjectDataSource à la nouvelle surcharge de UpdateProduct](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image5.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image4.png)

**Figure 2**: mappez l’ObjectDataSource `Update()` méthode sur le nouveau `UpdateProduct` de surcharge ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image6.png))


Étant donné que notre exemple initialement doit simplement la possibilité de modifier des données, mais pas insérer ou supprimer des enregistrements, prenez un moment pour indiquer explicitement que l’ObjectDataSource `Insert()` et `Delete()` méthodes ne doivent pas être mappés avec le `ProductsBLL` méthodes de la classe en sélectionnant les onglets INSERT et DELETE, puis en choisissant (aucun) dans la liste déroulante.


[![Choisissez (aucun) dans la liste déroulante pour l’insérer et supprimer les tabulations](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image8.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image7.png)

**Figure 3**: choisissez (aucun) à partir de la liste déroulante pour l’insérer et supprimer des onglets ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image9.png))


Une fois cet Assistant terminé, vérifiez la case à cocher Activer la modification de la balise active du GridView.

À la fin de l’Assistant créer une Source de données et la liaison au contrôle GridView, Visual Studio a créé la syntaxe déclarative pour les deux contrôles. Accédez à la vue de Source pour inspecter les balisage déclaratif de ObjectDataSource, qui est indiqué ci-dessous :


[!code-aspx[Main](examining-the-events-associated-with-inserting-updating-and-deleting-vb/samples/sample2.aspx)]

Puisqu’il n’existe aucun mappage pour l’ObjectDataSource `Insert()` et `Delete()` méthodes, il n’y aucun `InsertParameters` ou `DeleteParameters` sections. En outre, depuis le `Update()` méthode mappée à la `UpdateProduct` surcharge de méthode qui n’accepte trois paramètres d’entrée, la `UpdateParameters` section a uniquement trois `Parameter` instances.

Notez que l’ObjectDataSource `OldValuesParameterFormatString` est définie sur `original_{0}`. Cette propriété est définie automatiquement par Visual Studio lors de l’utilisation de l’Assistant Configurer la Source de données. Toutefois, étant donné que nos méthodes BLL ne prévoyez pas d’origine `ProductID` valeur à passer dans, supprimez cette attribution de la propriété entièrement à partir de la syntaxe déclarative de ObjectDataSource.

> [!NOTE]
> Si vous désactivez simplement la `OldValuesParameterFormatString` valeur de propriété à partir de la fenêtre Propriétés en mode Design, la propriété existe toujours dans la syntaxe déclarative, mais sera configuré pour une chaîne vide. Supprimez la propriété complètement à partir de la syntaxe déclarative ou, à partir de la fenêtre Propriétés, définissez la valeur sur la valeur par défaut, `{0}`.


Alors que ObjectDataSource a uniquement `UpdateParameters` pour le nom du produit, prix et ID, Visual Studio a ajouté un BoundField ou CheckBoxField dans le GridView pour chacun des champs du produit.


[![GridView contient un BoundField ou le CheckBoxField pour chacun des champs du produit](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image11.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image10.png)

**Figure 4**: GridView contient un BoundField ou le CheckBoxField pour chacun des champs du produit ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image12.png))


Lorsque l’utilisateur final modifie un produit et clique sur le bouton de mise à jour, le contrôle GridView énumère les champs qui n’étaient pas en lecture seule. Il définit ensuite la valeur du paramètre correspondant dans l’ObjectDataSource `UpdateParameters` collection à la valeur entrée par l’utilisateur. S’il n’est pas un paramètre correspondant, le contrôle GridView ajoute à la collection. Par conséquent, si notre GridView contient BoundFields et CheckBoxFields pour tous les champs du produit, ObjectDataSource se retrouve appelant le `UpdateProduct` surcharge dans tous ces paramètres, en dépit du fait que l’ObjectDataSource balisage déclaratif spécifie uniquement trois paramètres d’entrée (voir Figure 5). De même, s’il existe une combinaison de non-en lecture seule produit champs dans le GridView ne correspond pas aux paramètres d’entrée pour un `UpdateProduct` de surcharge, une exception est levée lorsque vous tentez de mettre à jour.


[![Le GridView va ajouter des paramètres à l’ObjectDataSource UpdateParameters Collection](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image14.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image13.png)

**Figure 5**: GridView le sera ajouter des paramètres à l’ObjectDataSource `UpdateParameters` Collection ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image15.png))


Pour vous assurer que l’ObjectDataSource appelle la `UpdateProduct` surcharge qui accepte uniquement du produit nom, prix et ID, nous avons besoin restreindre le contrôle GridView à avoir des champs modifiables pour uniquement le `ProductName` et `UnitPrice`. Cela peut être accompli en supprimant les autres BoundFields et CheckBoxFields, en définissant à ceux des autres champs `ReadOnly` propriété `True`, ou par une combinaison des deux. Pour ce didacticiel nous allons simplement supprimer tous les champs de GridView, sauf le `ProductName` et `UnitPrice` BoundFields, après lequel le balisage déclaratif du GridView ressemble à :


[!code-aspx[Main](examining-the-events-associated-with-inserting-updating-and-deleting-vb/samples/sample3.aspx)]

Bien que le `UpdateProduct` surcharge attend trois paramètres d’entrée, nous avons deux BoundFields dans notre GridView. C’est parce que le `productID` paramètre d’entrée est une valeur de clé primaire et passé à l’aide de la valeur de la `DataKeyNames` à la ligne modifiée.

Notre GridView, avec la `UpdateProduct` permet à un utilisateur à modifier uniquement le nom et le prix d’un produit sans perte de tous les autres champs de produit de la surcharge.


[![L’Interface autorise la modification uniquement du produit nom et prix](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image17.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image16.png)

**Figure 6**: le permet d’Interface modifiant simplement du produit nom et les prix ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image18.png))


> [!NOTE]
> Comme indiqué dans le didacticiel précédent, il est essentiel que le contrôle GridView s vue état activé (comportement par défaut). Si vous définissez le GridView s `EnableViewState` propriété `false`, vous courez le risque d’avoir des utilisateurs simultanés involontairement supprimer ou de modifier des enregistrements. Consultez [Avertissement : problème de concurrence avec ASP.NET 2.0 contrôles GridView/DetailsView/FormViews cette modification de la prise en charge et/ou la suppression et la dont l’état d’affichage est désactivé](http://scottonwriting.net/sowblog/posts/10054.aspx) pour plus d’informations.


## <a name="improving-theunitpriceformatting"></a>Améliorer la`UnitPrice`mise en forme

Lors de l’exemple de GridView indiqué dans la Figure 6 fonctionne, le `UnitPrice` champ n’est pas formaté, ce qui entraîne un affichage de prix qui ne dispose pas de toutes les devises des symboles et a quatre positions décimales. Pour appliquer une mise en forme pour les lignes non modifiable de devise, il suffit de définir la `UnitPrice` de BoundField `DataFormatString` propriété `{0:c}` et son `HtmlEncode` propriété `False`.


[![Définir du prix unitaire DataFormatString HtmlEncode propriétés en conséquence](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image20.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image19.png)

**Figure 7**: définir le `UnitPrice`de `DataFormatString` et `HtmlEncode` propriétés en conséquence ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image21.png))


Avec cette modification, les lignes non modifiables mettre en forme le prix sous forme de devise ; Toutefois, la ligne modifiée, affiche toujours la valeur sans le symbole monétaire et avec quatre décimales.


[![Les lignes Non modifiables sont désormais mises en forme en tant que valeurs de devise](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image23.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image22.png)

**Figure 8**: les lignes Non modifiables sont désormais mises en forme en tant que valeurs de devise ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image24.png))


Les instructions de mise en forme spécifiées dans le `DataFormatString` propriété peut être appliquée à l’interface de modification en définissant le BoundField `ApplyFormatInEditMode` propriété `True` (la valeur par défaut est `False`). Prenez un moment pour définir cette propriété sur `True`.


[![Propriété de ApplyFormatInEditMode du UnitPrice BoundField la valeur True](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image26.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image25.png)

**Figure 9**: définir le `UnitPrice` de BoundField `ApplyFormatInEditMode` propriété `True` ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image27.png))


Avec cette modification, la valeur de la `UnitPrice` affichées dans le ligne est également mise en forme comme une valeur monétaire.


[![Valeur du prix unitaire de la ligne modifiée est maintenant mis en forme comme une devise](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image29.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image28.png)

**La figure 10**: la modification la ligne du `UnitPrice` valeur est maintenant mis en forme comme une valeur monétaire ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image30.png))


Toutefois, la mise à jour un produit avec le symbole monétaire dans la zone de texte tel que $ 19 h 00 lève une `FormatException`. Lorsque le contrôle GridView tente d’assigner les valeurs fournies par l’utilisateur à l’ObjectDataSource `UpdateParameters` collection, il est impossible de convertir le `UnitPrice` de chaîne « 19 h 00 » dans le `Decimal` requis par le paramètre (voir Figure 11). Pour résoudre ce problème, nous pouvons créer un gestionnaire d’événements pour le contrôle du GridView `RowUpdating` événements et l’analyse fournie par l’utilisateur `UnitPrice` comme un format monétaire `Decimal`.

Du contrôle GridView `RowUpdating` événement accepte comme deuxième paramètre, un objet de type [GridViewUpdateEventArgs](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridviewupdateeventargs(VS.80).aspx), qui inclut un `NewValues` dictionnaire en tant qu’une de ses propriétés qui contient les valeurs fournies par l’utilisateur est prêts à être attribué à l’ObjectDataSource `UpdateParameters` collection. Nous pouvons remplacer existants `UnitPrice` valeur dans le `NewValues` collection avec une valeur décimale analysée utilisant le format monétaire avec les lignes suivantes du code dans le `RowUpdating` Gestionnaire d’événements :


[!code-vb[Main](examining-the-events-associated-with-inserting-updating-and-deleting-vb/samples/sample4.vb)]

Si l’utilisateur a fourni un `UnitPrice` valeur (par exemple, « $ 19 h 00 »), cette valeur est remplacée par la valeur décimale calculée par [Decimal.Parse](https://msdn.microsoft.com/en-us/library/system.decimal.parse(VS.80).aspx), l’analyse de la valeur sous forme de devise. Cette analyse correctement la virgule décimale dans le cas des symboles monétaires, des virgules, décimal et ainsi de suite et utilise le [énumération NumberStyles](https://msdn.microsoft.com/en-US/library/system.globalization.numberstyles(VS.80).aspx) dans les [System.Globalization](https://msdn.microsoft.com/en-US/library/abeh092z(VS.80).aspx) espace de noms.

Figure 11 illustre à la fois le problème causé par les symboles monétaires dans fournie par l’utilisateur `UnitPrice`, ainsi que de la façon de GridView `RowUpdating` Gestionnaire d’événements peut être utilisé pour analyser correctement ce type d’entrée.


[![Valeur du prix unitaire de la ligne modifiée est maintenant mis en forme comme une devise](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image32.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image31.png)

**Figure 11**: la modification la ligne du `UnitPrice` valeur est maintenant mis en forme comme une valeur monétaire ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image33.png))


## <a name="step-2-prohibitingnull-unitprices"></a>Étape 2 : interdiction`NULL UnitPrices`

Alors que la base de données est configuré pour autoriser `NULL` des valeurs dans le `Products` la table `UnitPrice` colonne, nous pouvons que vous voulez empêcher les utilisateurs visitant cette page particulière de spécifier un `NULL` `UnitPrice` valeur. Autrement dit, si un utilisateur ne parvient pas à entrer un `UnitPrice` valeur lors de la modification d’une ligne de produit, plutôt que d’enregistrer les résultats dans la base de données que nous souhaitons afficher un message informant l’utilisateur que, sur cette page, tous les produits modifiés doivent avoir un prix spécifié.

Le `GridViewUpdateEventArgs` objet passé dans le GridView `RowUpdating` Gestionnaire d’événements contient un `Cancel` propriété qui, si la valeur `True`, met fin au processus de mise à jour. Nous allons étendre le `RowUpdating` Gestionnaire d’événements pour définir `e.Cancel` à `True` et affichera un message expliquant la raison pour laquelle si le `UnitPrice` valeur dans le `NewValues` collection a la valeur `Nothing`.

Commencez par ajouter un contrôle Web Label vers la page nommée `MustProvideUnitPriceMessage`. Ce contrôle Label s’affiche si l’utilisateur ne parvient pas à spécifier un `UnitPrice` valeur lors de la mise à jour d’un produit. Définir l’étiquette `Text` propriété « Vous devez fournir un prix du produit ». J’ai créé également une classe CSS dans `Styles.css` nommé `Warning` avec la définition suivante :


[!code-css[Main](examining-the-events-associated-with-inserting-updating-and-deleting-vb/samples/sample5.css)]

Enfin, définissez l’étiquette `CssClass` propriété `Warning`. À ce stade le concepteur doit afficher le message d’avertissement dans un rouge, gras, italique, taille de police de très nombreuses ci-dessus le GridView, comme indiqué dans la Figure 12.


[![Une étiquette a été ajoutée au-dessus du contrôle GridView](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image35.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image34.png)

**Figure 12**: une étiquette a été ajouté ci-dessus GridView ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image36.png))


Par défaut, cette étiquette doit être masquée, par conséquent, définissez son `Visible` propriété `False` dans le `Page_Load` Gestionnaire d’événements :


[!code-vb[Main](examining-the-events-associated-with-inserting-updating-and-deleting-vb/samples/sample6.vb)]

Si l’utilisateur tente de mettre à jour d’un produit sans spécifier le `UnitPrice`, vous souhaitez annuler la mise à jour et afficher l’étiquette d’avertissement. Augmenter de GridView `RowUpdating` Gestionnaire d’événements comme suit :


[!code-vb[Main](examining-the-events-associated-with-inserting-updating-and-deleting-vb/samples/sample7.vb)]

Si un utilisateur tente d’enregistrer un produit sans spécifier un prix, la mise à jour est annulée et un message utile s’affiche. Lors de la base de données (et la logique métier) permet de `NULL` `UnitPrice` s, cette page ASP.NET particulière n’est pas.


[![Un utilisateur ne peut pas quitter le prix unitaire vide](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image38.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image37.png)

**Figure 13**: un utilisateur ne peut pas quitter `UnitPrice` vide ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image39.png))


Jusqu'à présent, nous avons vu comment utiliser le contrôle du GridView `RowUpdating` événement pour modifier par programme les valeurs de paramètre affectées à l’ObjectDataSource `UpdateParameters` collection ainsi que la manière d’annuler la mise à jour de traiter complètement. Ces concepts s’appliquent aux contrôles DetailsView et FormView et s’appliquent également à l’insertion et la suppression.

Ces tâches peuvent également être effectuées au niveau de ObjectDataSource via les gestionnaires d’événements pour son `Inserting`, `Updating`, et `Deleting` événements. Ces événements se déclenche avant l’appel de la méthode associée de l’objet sous-jacent et fournissent une opportunité de dernière chance de modifier la collection de paramètres d’entrée ou d’annuler l’opération ferme. Les gestionnaires d’événements pour ces trois événements sont passés à un objet de type [ObjectDataSourceMethodEventArgs](https://msdn.microsoft.com/en-US/library/system.web.ui.webcontrols.objectdatasourcemethodeventargs(VS.80).aspx) qui possède deux propriétés intéressantes :

- [Annuler](https://msdn.microsoft.com/en-US/library/system.componentmodel.canceleventargs.cancel(VS.80).aspx), qui, si la valeur `True`, annule l’opération en cours d’exécution
- [InputParameters](https://msdn.microsoft.com/en-US/library/system.web.ui.webcontrols.objectdatasourcemethodeventargs.inputparameters(VS.80).aspx), qui est la collection de `InsertParameters`, `UpdateParameters`, ou `DeleteParameters`, selon si le Gestionnaire d’événements pour le `Inserting`, `Updating`, ou `Deleting` événement

Pour illustrer l’utilisation avec les valeurs de paramètre au niveau de l’ObjectDataSource, nous allons inclure un contrôle DetailsView dans notre page qui permet aux utilisateurs d’ajouter un nouveau produit. Ce contrôle DetailsView permet de fournir une interface pour ajouter rapidement un nouveau produit à la base de données. Pour conserver une interface utilisateur cohérente lors de l’ajout d’un nouveau produit permet d’autoriser l’utilisateur à entrer uniquement des valeurs pour le `ProductName` et `UnitPrice` champs. Par défaut, ces valeurs ne sont pas fournies dans l’interface de l’insertion du DetailsView seront fixés à un `NULL` de base de données de valeur. Toutefois, nous pouvons utiliser l’ObjectDataSource `Inserting` événements d’injecter des valeurs par défaut différentes, comme nous le verrons dans quelques instants.

## <a name="step-3-providing-an-interface-to-add-new-products"></a>Étape 3 : Fournir une Interface pour ajouter de nouveaux produits

Faites glisser un contrôle DetailsView à partir de la boîte à outils vers le concepteur ci-dessus le GridView, effacer son `Height` et `Width` propriétés et les lier à ObjectDataSource déjà présents sur la page. Cette opération ajoute un BoundField ou le CheckBoxField pour chacun des champs du produit. Comme nous voulons que ce contrôle DetailsView permet d’ajouter de nouveaux produits, nous devons vérifier l’option Activer l’insertion de la balise active ; Toutefois, aucune option n’est ce type, car l’ObjectDataSource `Insert()` méthode n’est pas mappée à une méthode dans la `ProductsBLL` classe (n’oubliez pas que nous avons défini ce mappage (aucun) lors de la configuration de la source de données voir la Figure 3).

Pour configurer l’ObjectDataSource, sélectionnez le lien configurer la Source de données à partir de sa balise active, lancement de l’Assistant. Le premier écran vous permet de modifier l’objet sous-jacent Qu'objectdatasource est lié Laissez-la sur `ProductsBLL`. L’écran suivant répertorie les mappages entre les méthodes de l’ObjectDataSource à l’objet sous-jacent. Bien que nous indique explicitement que la `Insert()` et `Delete()` méthodes ne doivent pas être mappées à des méthodes, si vous accédez aux onglets INSERT et DELETE vous verrez existe-t-il un mappage. C’est parce que le `ProductsBLL`de `AddProduct` et `DeleteProduct` méthodes utilisent le `DataObjectMethodAttribute` attribut pour indiquer qu’ils sont les méthodes par défaut pour `Insert()` et `Delete()`, respectivement. Par conséquent, l’Assistant ObjectDataSource sélectionne ces chaque fois que vous exécutez l’Assistant, sauf s’il existe une autre valeur spécifiée explicitement.

Laissez le `Insert()` méthode pointant vers le `AddProduct` (méthode), mais la valeur à nouveau liste déroulante la suppression de l’onglet de, à (None).


[![La valeur liste déroulante de l’onglet Insérer, la méthode AddProduct](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image41.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image40.png)

**La figure 14**: la valeur liste déroulante de l’onglet Insérer, le `AddProduct` (méthode) ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image42.png))


[![Définir la liste déroulante de l’onglet du supprimer à (None)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image44.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image43.png)

**Figure 15**: définition de la liste déroulante de l’onglet supprimer (None) ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image45.png))


Après avoir apporté ces modifications, la syntaxe déclarative de ObjectDataSource est développée pour inclure un `InsertParameters` collection, comme indiqué ci-dessous :


[!code-aspx[Main](examining-the-events-associated-with-inserting-updating-and-deleting-vb/samples/sample8.aspx)]

Réexécuter l’Assistant rajouté le `OldValuesParameterFormatString` propriété. Prenez un moment pour effacer cette propriété en lui affectant la valeur par défaut (`{0}`) ou le supprimer complètement de la syntaxe déclarative.

Avec ObjectDataSource en fournissant des fonctionnalités d’insertion, la balise active du DetailsView inclut maintenant la case à cocher Activer l’insertion ; Revenez dans le concepteur et sélectionnez cette option. Ensuite, alléger le contrôle DetailsView afin qu’il puisse uniquement deux BoundFields - `ProductName` et `UnitPrice` - et le CommandField. À ce stade, la syntaxe déclarative de DetailsView doit ressembler à :


[!code-aspx[Main](examining-the-events-associated-with-inserting-updating-and-deleting-vb/samples/sample9.aspx)]

La figure 16 montre cette page lors de l’affichage via un navigateur à ce stade. Comme vous pouvez le voir, le contrôle DetailsView répertorie le nom et le prix du produit premier (Tran). Le résultat escompté, toutefois, est une interface d’insertion qui fournit un moyen de l’utilisateur ajouter rapidement un nouveau produit à la base de données.


[![Le contrôle DetailsView est actuellement affiché en Mode lecture seule](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image47.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image46.png)

**Figure 16**: le contrôle DetailsView est actuellement affiché en Mode lecture seule ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image48.png))


Pour afficher le contrôle DetailsView dans son mode insertion, nous devons définir le `DefaultMode` propriété `Inserting`. Cela rend le contrôle DetailsView en mode insertion lors de la première visite et le conserve après avoir inséré un nouvel enregistrement. Comme le montre la Figure 17, un telle DetailsView fournit une interface rapide pour l’ajout d’un nouvel enregistrement.


[![Le contrôle DetailsView fournit une Interface pour ajouter rapidement un nouveau produit](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image50.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image49.png)

**Figure 17**: le contrôle DetailsView fournit une Interface pour ajouter rapidement un nouveau produit ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image51.png))


Lorsque l’utilisateur entre un nom de produit et le prix (par exemple, « Acme eau » et 1,99, comme dans la Figure 17) et clique sur Insert, se poursuivent d’une publication (postback), le flux de travail insertion commence, sanctionné dans un nouvel enregistrement de produit qui est ajouté à la base de données. Le contrôle DetailsView conserve son interface d’insertion et le contrôle GridView est automatiquement reliée à sa source de données afin d’inclure le nouveau produit, comme indiqué dans la Figure 18.


![Le produit](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image52.png)

**La figure 18**: le produit « Eau Acme » a été ajouté à la base de données


Alors que le contrôle GridView dans la Figure 18, n’affiche pas les champs de produit en l’absence de l’interface de DetailsView `CategoryID`, `SupplierID`, `QuantityPerUnit`, et ainsi de suite sont affectés `NULL` valeurs de base de données. Vous pouvez le constater en effectuant les étapes suivantes :

1. Accédez à l’Explorateur de serveurs dans Visual Studio
2. Développer le `NORTHWND.MDF` nœud de base de données
3. Avec le bouton droit sur le `Products` nœud de la table de base de données
4. Sélectionnez Afficher les données de Table

Énumère tous les enregistrements dans la `Products` table. Comme dans Figure 19, toutes les colonnes de notre nouveau produit autre que `ProductID`, `ProductName`, et `UnitPrice` ont `NULL` valeurs.


[![Champs produit fourni pas dans le contrôle DetailsView sont affectées des valeurs NULL](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image54.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image53.png)

**Figure 19**: le produit champs pas fourni dans le contrôle DetailsView assignés `NULL` valeurs ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image55.png))


Nous pouvons choisir de fournir une valeur par défaut autre que `NULL` pour un ou plusieurs de ces valeurs de colonne, soit parce que `NULL` n’est pas la meilleure option par défaut ou parce que la colonne de base de données lui-même n’autorise pas `NULL` s. Pour y parvenir nous pouvons définir par programme les valeurs des paramètres du DetailsView `InputParameters` collection. Cette attribution peut être effectuée soit dans l’événement gestionnaire pour le contrôle du DetailsView `ItemInserting` événement ou de l’ObjectDataSource `Inserting` événement. Étant donné que nous avons déjà présenté à l’aide de données Web les événements antérieurs et postérieurs au niveau niveau de contrôle, intéressons-nous à l’aide d’événements de l’ObjectDataSource instant.

## <a name="step-4-assigning-values-to-thecategoryidandsupplieridparameters"></a>Étape 4 : Assigner des valeurs à la`CategoryID`et`SupplierID`paramètres

Pour ce didacticiel Imaginons que notre application lors de l’ajout d’un nouveau produit via cette interface il doit être affectée à un `CategoryID` et `SupplierID` la valeur 1. Comme mentionné précédemment, ObjectDataSource doté d’une paire d’événements antérieurs et postérieurs au niveau déclenchant pendant le processus de modification de données. Lors de son `Insert()` méthode est appelée, ObjectDataSource déclenche d’abord son `Inserting` événement, puis appelle la méthode de son `Insert()` méthode a été mappée à et enfin déclenche le `Inserted` événement. Le `Inserting` Gestionnaire d’événements nous offre une possibilité d’optimiser les paramètres d’entrée ou d’annuler l’opération ferme.

> [!NOTE]
> Dans une application réelle il faudrait probablement de laisser l’utilisateur spécifient la catégorie et le fournisseur ou serait choisissez cette valeur en fonction de certains critères ou entreprise logique (plutôt qu’à l’aveugle en sélectionnant un ID de 1). Tous les cas, l’exemple illustre comment définir par programme la valeur de paramètre d’entrée à partir de l’événement de niveau préalable de ObjectDataSource.


Prenez un moment pour créer un gestionnaire d’événements pour l’ObjectDataSource `Inserting` événement. Notez que second paramètre du Gestionnaire d’événements est un objet de type `ObjectDataSourceMethodEventArgs`, qui a une propriété pour accéder à la collection de paramètres (`InputParameters`) et une propriété pour annuler l’opération (`Cancel`).


[!code-vb[Main](examining-the-events-associated-with-inserting-updating-and-deleting-vb/samples/sample10.vb)]

À ce stade, le `InputParameters` propriété contient l’ObjectDataSource `InsertParameters` collection avec les valeurs assignées à partir du DetailsView. Pour modifier la valeur de l’un de ces paramètres, utilisez simplement : `e.InputParameters("paramName") = value`. Par conséquent, pour définir le `CategoryID` et `SupplierID` aux valeurs 1, ajustez le `Inserting` Gestionnaire d’événements à l’aspect suivant :


[!code-vb[Main](examining-the-events-associated-with-inserting-updating-and-deleting-vb/samples/sample11.vb)]

Cette fois lors de l’ajout d’un nouveau produit (par exemple, Soda Acme), le `CategoryID` et `SupplierID` les colonnes du nouveau produit sont définis sur 1 (voir Figure 20).


[![Nouveaux produits ont maintenant leur CategoryID et par SupplierID valeur 1](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image57.png)](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image56.png)

**Figure 20**: nouveaux produits maintenant avoir leurs `CategoryID` et `SupplierID` la valeur 1 ([cliquez pour afficher l’image en taille réelle](examining-the-events-associated-with-inserting-updating-and-deleting-vb/_static/image58.png))


## <a name="summary"></a>Résumé

Lors de la modification, insertion et la suppression de processus, à la fois le données Web et le contrôle ObjectDataSource parcourez un nombre d’événements préalables- et post-niveau. Dans ce didacticiel, nous examiner les événements de niveau préalable et vu comment les utiliser pour personnaliser les paramètres d’entrée ou d’annuler l’opération de modification de données entièrement à la fois le données et événements contrôle Web ObjectDataSource. Dans l’étape suivante du didacticiel, nous allons examiner la création et l’utilisation des gestionnaires d’événements pour les événements post-niveau.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Jackie Goor et Liz Shulok. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](an-overview-of-inserting-updating-and-deleting-data-vb.md)
[Suivant](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-vb.md)
