---
uid: web-forms/overview/data-access/editing-inserting-and-deleting-data/adding-validation-controls-to-the-editing-and-inserting-interfaces-cs
title: "Ajout de contrôles de Validation à la modification et l’insertion des Interfaces (c#) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous verrons combien il est facile pour ajouter des contrôles de validation EditItemTemplate les InsertItemTemplate d’un contrôle Web, de données pour fournir plus..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/17/2006
ms.topic: article
ms.assetid: 2086cb1a-ab78-49ae-9c0b-03891c69776a
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/editing-inserting-and-deleting-data/adding-validation-controls-to-the-editing-and-inserting-interfaces-cs
msc.type: authoredcontent
ms.openlocfilehash: b8b05705629b5e8a9acfc5d23517ef1b3cfa7cd6
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="adding-validation-controls-to-the-editing-and-inserting-interfaces-c"></a>Ajout de contrôles de Validation à la modification et l’insertion des Interfaces (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_19_CS.exe) ou [télécharger le PDF](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/datatutorial19cs1.pdf)

> Dans ce didacticiel, nous allons voir combien il est facile pour ajouter des contrôles de validation au EditItemTemplate et InsertItemTemplate d’un contrôle Web, de données pour fournir une interface utilisateur plus sans poser de problèmes.


## <a name="introduction"></a>Introduction

Les contrôles GridView et DetailsView dans les exemples, nous avons exploré au cours des trois didacticiels ont tous été composés de BoundFields et CheckBoxFields (les types de champ ajoutées automatiquement par Visual Studio lors de la liaison d’un contrôle GridView ou le contrôle DetailsView à une source de données contrôler via la balise active). Lorsque vous modifiez une ligne dans un contrôle GridView ou le contrôle DetailsView, ces BoundFields qui ne sont pas en lecture seule sont convertis en zones de texte, à partir de laquelle l’utilisateur final peut modifier les données existantes. De même, lorsque insérer un nouveau enregistrer dans un contrôle DetailsView, ces BoundFields dont `InsertVisible` est définie sur `true` (la valeur par défaut) sont rendus sous forme de zones de texte vide, dans laquelle l’utilisateur peut fournir des valeurs de champ du nouvel enregistrement. De même, CheckBoxFields, qui sont désactivées dans l’interface standard en lecture seule, sont convertis en cases à cocher activées dans la modification et l’insertion d’interfaces.

Si la valeur par défaut, modification et l’insertion des interfaces pour les BoundField et CheckBoxField peut être utile, l’interface n’a pas de n’importe quel type de validation. Si un utilisateur fait une erreur de saisie de données - telles que l’omission de la `ProductName` champ ou en entrant une valeur non valide pour `UnitsInStock` (par exemple, -50) une exception sera levée à partir de la profondeur de l’architecture d’application. Alors que cette exception peut être gérée normalement comme illustré dans le [didacticiel précédent](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-cs.md), dans l’idéal, la modification ou insertion de l’interface utilisateur inclut des contrôles de validation pour empêcher un utilisateur d’entrer ces données non valides dans le Placez d’abord.

Afin de fournir une interface de l’insertion ou une modification personnalisée, nous devons remplacer le BoundField ou CheckBoxField TemplateField. TemplateField, qui ont été le sujet de discussion dans la [à l’aide de TemplateField dans le contrôle GridView](../custom-formatting/using-templatefields-in-the-gridview-control-cs.md) et [à l’aide de TemplateField dans le contrôle DetailsView](../custom-formatting/using-templatefields-in-the-detailsview-control-cs.md) didacticiels, peut se composer de plusieurs modèles de définition de séparent les interfaces pour les différents états. De la TemplateField `ItemTemplate` est utilisé pour lors du rendu des champs en lecture seule ou des lignes dans les contrôles DetailsView ou GridView, tandis que la `EditItemTemplate` et `InsertItemTemplate` indiquent les interfaces à utiliser pour la modification et l’insertion des modes, respectivement.

Dans ce didacticiel, nous verrons combien il est facile pour ajouter des contrôles de validation à la TemplateField `EditItemTemplate` et `InsertItemTemplate` pour fournir une interface utilisateur plus sans poser de problèmes. Plus précisément, ce didacticiel prend l’exemple créé dans le [examiner les événements associés insertion, mise à jour et suppression](examining-the-events-associated-with-inserting-updating-and-deleting-cs.md) didacticiel et renforce la modification et l’insertion d’interfaces pour inclure la validation appropriées.

## <a name="step-1-replicating-the-example-fromexamining-the-events-associated-with-inserting-updating-and-deletingexamining-the-events-associated-with-inserting-updating-and-deleting-csmd"></a>Étape 1 : Réplication de l’exemple à partir de[examinant les événements associé d’insertion, de mise à jour et de suppression](examining-the-events-associated-with-inserting-updating-and-deleting-cs.md)

Dans le [examiner les événements associés insertion, mise à jour et suppression](examining-the-events-associated-with-inserting-updating-and-deleting-cs.md) didacticiel, nous avons créé une page qui liste les noms et les prix des produits dans un GridView modifiable. En outre, la page inclus un DetailsView dont `DefaultMode` a pris la valeur de propriété `Insert`, il est donc toujours rendu en mode insertion. À partir de ce contrôle DetailsView, l’utilisateur peut entrer le nom et le prix d’un nouveau produit et cliquez sur Insérer l’avez ajouté au système (voir Figure 1).


[![L’exemple précédent permet aux utilisateurs d’ajouter de nouveaux produits et de modifier des relations existantes](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image2.png)](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image1.png)

**Figure 1**: le précédent exemple permet aux utilisateurs pour ajouter de nouveaux produits et modifier des types existants ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image3.png))


Notre objectif de ce didacticiel est d’enrichir le DetailsView et un GridView pour fournir des contrôles de validation. En particulier, notre logique de validation est :

- Nécessitent que le nom soit fourni lors de l’insertion ou modification d’un produit
- Nécessitent que le prix soit fourni lors de l’insertion d’un enregistrement. Lorsque vous modifiez un enregistrement, nous nécessitera encore un prix, mais utilise la logique de programmation dans le GridView `RowUpdating` déjà présent dans le didacticiel antérieur Gestionnaire d’événements
- Assurez-vous que la valeur entrée pour le prix est un format de devise valide

Avant que nous pouvons examiner d’augmentation de l’exemple précédent, pour inclure la validation, nous devons tout d’abord répliquer l’exemple à partir de la `DataModificationEvents.aspx` page à la page de ce didacticiel, `UIValidation.aspx`. Pour cela nous avons besoin de copier sur les deux le `DataModificationEvents.aspx` balisage déclaratif de la page et son code source. Tout d’abord copier sur le balisage déclaratif en effectuant les étapes suivantes :

1. Ouvrez le `DataModificationEvents.aspx` page dans Visual Studio
2. Atteindre le balisage déclaratif de la page (cliquez sur le bouton de la Source au bas de la page)
3. Copiez le texte dans le `<asp:Content>` et `</asp:Content>` balises (lignes 3 à 44), comme illustré dans la Figure 2.


[![Copier le texte dans le &lt;asp : Content&gt; contrôle](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image5.png)](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image4.png)

**Figure 2**: copiez le texte dans le `<asp:Content>` contrôle ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image6.png))


1. Ouvrez le `UIValidation.aspx` page
2. Atteindre le balisage déclaratif de la page
3. Collez le texte dans le `<asp:Content>` contrôle.

Pour copier le code source, ouvrez le `DataModificationEvents.aspx.cs` page et copiez simplement le texte *dans* la `EditInsertDelete_DataModificationEvents` classe. Copiez les trois gestionnaires d’événements (`Page_Load`, `GridView1_RowUpdating`, et `ObjectDataSource1_Inserting`), mais **pas** copier la déclaration de classe ou `using` instructions. Coller le texte copié *dans* le `EditInsertDelete_UIValidation` classe dans `UIValidation.aspx.cs`.

Après avoir déplacé le contenu et le code à partir de `DataModificationEvents.aspx` à `UIValidation.aspx`, prenez un moment pour tester votre progression dans un navigateur. Vous devez voir la même sortie et rencontrer les mêmes fonctionnalités dans chacun de ces deux pages (font référence à la Figure 1 pour une capture d’écran de `DataModificationEvents.aspx` en action).

## <a name="step-2-converting-the-boundfields-into-templatefields"></a>Étape 2 : Conversion le BoundFields en TemplateField

Pour ajouter des contrôles de validation pour les interfaces de modification et d’insertion, les BoundFields utilisées par les contrôles DetailsView et GridView doivent être converties en TemplateField. Pour ce faire, cliquez sur les liens de modifier les colonnes et modifier les champs dans le GridView et de DetailsView balises actives, respectivement. Sélectionnez le BoundFields et cliquez sur le lien « Convertir ce champ en TemplateField ».


[![Chacun des BoundFields du contrôle DetailsView et du contrôle GridView convertir en TemplateField](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image8.png)](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image7.png)

**Figure 3**: convertir chacun du du contrôle DetailsView et du contrôle GridView BoundFields dans TemplateField ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image9.png))


Conversion d’un BoundField en TemplateField via la boîte de dialogue champs génère TemplateField présentant les mêmes interfaces en lecture seule, de modifier et insérer en tant que le BoundField lui-même. Le balisage suivant illustre la syntaxe déclarative pour le `ProductName` champ dans le contrôle DetailsView après qu’il a été converti en TemplateField :

[!code-aspx[Main](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/samples/sample1.aspx)]

Notez que cette TemplateField avait trois modèles créés automatiquement `ItemTemplate`, `EditItemTemplate`, et `InsertItemTemplate`. Le `ItemTemplate` affiche une valeur de champ de données unique (`ProductName`) à l’aide d’un contrôle Web Label, tandis que la `EditItemTemplate` et `InsertItemTemplate` présenter la valeur de champ de données dans un contrôle de zone de texte Web qui associe le champ des données de la zone de texte `Text` propriété à l’aide de la liaison de données bidirectionnelle. Étant donné que nous utilisons uniquement le contrôle DetailsView dans cette page pour insérer, vous pouvez supprimer la `ItemTemplate` et `EditItemTemplate` dans les deux TemplateField, bien qu’il n’existe pas de risque de laisser les.

Étant donné que le contrôle GridView ne prend pas en charge intégrés insertion des fonctionnalités de DetailsView, conversion de GridView `ProductName` champ en TemplateField entraîne uniquement un `ItemTemplate` et `EditItemTemplate`:

[!code-aspx[Main](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/samples/sample2.aspx)]

En cliquant sur le « convertir ce champ en TemplateField », Visual Studio a créé un TemplateField dont modèles simulent l’interface utilisateur de la BoundField converti. Vous pouvez le vérifier en accédant à cette page via un navigateur. Vous trouverez que l’apparence et le comportement de la TemplateField est identique à l’expérience lorsque BoundFields étaient utilisées à la place.

> [!NOTE]
> N’hésitez pas à personnaliser les interfaces de modification dans les modèles en fonction des besoins. Par exemple, nous pouvons choisir de disposer de la zone de texte dans le `UnitPrice` TemplateField restitué sous la forme d’une zone de texte plus petite que la `ProductName` zone de texte. Pour ce faire, vous pouvez définir la zone de texte `Columns` propriété une valeur appropriée ou fournissez une largeur absolue via le `Width` propriété. Dans l’étape suivante du didacticiel, nous verrons comment personnaliser entièrement l’interface de modification en remplaçant la zone de texte avec une entrée de données alternatif contrôle Web.


## <a name="step-3-adding-the-validation-controls-to-the-gridviewsedititemtemplate-s"></a>Étape 3 : Ajout de contrôles de Validation à du GridView`EditItemTemplate` s

Lors de la construction des formulaires de saisie de données, il est important que les utilisateurs entrent tous les champs obligatoires et que toutes les entrées fournies sont des valeurs juridiques, mis en forme correctement. Pour vous assurer que les entrées d’un utilisateur sont valides, ASP.NET fournit les cinq contrôles de validation intégrées qui sont conçues pour être utilisées pour valider la valeur d’un contrôle d’entrée unique :

- [RequiredFieldValidator](https://msdn.microsoft.com/library/5hbw267h(VS.80).aspx) garantit qu’une valeur a été fournie.
- [CompareValidator](https://msdn.microsoft.com/library/db330ayw(VS.80).aspx) valide une valeur à une autre valeur de contrôle Web ou une valeur constante ou garantit que le format de la valeur est autorisé pour un type de données spécifié
- [RangeValidator](https://msdn.microsoft.com/library/f70d09xt.aspx) garantit qu’une valeur est comprise dans une plage de valeurs
- [RegularExpressionValidator](https://msdn.microsoft.com/library/eahwtc9e.aspx) valide une valeur par rapport à un [expression régulière](http://en.wikipedia.org/wiki/Regular_expression)
- [CustomValidator](https://msdn.microsoft.com/library/9eee01cx(VS.80).aspx) valide une valeur par rapport à une méthode personnalisée, définie par l’utilisateur

Pour plus d’informations sur ces cinq contrôles, passez en revue les [section contrôles de Validation](https://quickstarts.asp.net/quickstartv20/aspnet/doc/ctrlref/validation/default.aspx) de la [didacticiels de démarrage rapide ASP.NET](https://asp.net/QuickStart/aspnet/).

Pour notre didacticiel nous devez utiliser un contrôle RequiredFieldValidator dans le DetailsView et du contrôle GridView `ProductName` TemplateField et un contrôle RequiredFieldValidator dans le contrôle de DetailsView `UnitPrice` TemplateField. En outre, nous devrons ajouter un CompareValidator pour les deux contrôles `UnitPrice` TemplateField qui garantit que le prix entré a une valeur supérieure ou égale à 0 et est présenté dans un format monétaire valide.

> [!NOTE]
> Alors que ASP.NET 1.x avait ces mêmes contrôles de cinq validation, ASP.NET 2.0 a ajouté un certain nombre d’améliorations, principal deux en cours d’un script côté client prend en charge les navigateurs Internet Explorer et permettent aux contrôles de validation de partition sur une page dans groupes de validation. Pour plus d’informations sur les nouvelles fonctionnalités de contrôle de validation dans la version 2.0, reportez-vous à [ayant été Disséqué les contrôles de Validation dans ASP.NET 2.0](http://aspnet.4guysfromrolla.com/articles/112305-1.aspx).


Commençons par l’ajout de contrôles de validation nécessaire pour le `EditItemTemplate` s dans TemplateField du GridView. Pour ce faire, cliquez sur le lien Modifier les modèles à partir de la balise d’Active du GridView pour afficher l’interface de modification de modèle. À ce stade, vous pouvez sélectionner le modèle à modifier dans la liste déroulante. Étant donné que vous souhaitez augmenter l’interface de modification, nous devons ajouter des contrôles de validation pour le `ProductName` et `UnitPrice`de `EditItemTemplate` s.


[![Nous avons besoin d’étendre le nom du produit et EditItemTemplates du prix unitaire](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image11.png)](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image10.png)

**Figure 4**: nous avons besoin pour étendre le `ProductName` et `UnitPrice`de `EditItemTemplate` s ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image12.png))


Dans le `ProductName` `EditItemTemplate`, ajoutez un contrôle RequiredFieldValidator en le faisant glisser à partir de la boîte à outils dans l’interface de modification de modèle, placer après la zone de texte.


[![Ajoutez un contrôle RequiredFieldValidator à ProductName EditItemTemplate](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image14.png)](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image13.png)

**Figure 5**: ajouter un contrôle RequiredFieldValidator à la `ProductName` `EditItemTemplate` ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image15.png))


Tous les contrôles de validation de travail à valider l’entrée d’un contrôle Web ASP.NET unique. Par conséquent, nous devons indiquer que nous venons d’ajouter RequiredFieldValidator doit valider par rapport à la zone de texte dans le `EditItemTemplate`; cela est accompli en définissant le contrôle de validation [propriété ControlToValidate](https://msdn.microsoft.com/library/system.web.ui.webcontrols.basevalidator.controltovalidate(VS.80).aspx) à la `ID` du contrôle Web approprié. La zone de texte n’a actuellement le plutôt apercevoir `ID` de `TextBox1`, mais nous allons utiliser un nom plus approprié. Cliquez sur la zone de texte dans le modèle et à partir de la fenêtre Propriétés, modifiez la `ID` de `TextBox1` à `EditProductName`.


[![Modifier l’ID de la zone de texte à EditProductName](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image17.png)](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image16.png)

**Figure 6**: modifier la zone de texte `ID` à `EditProductName` ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image18.png))


Ensuite, définissez le RequiredFieldValidator `ControlToValidate` propriété `EditProductName`. Enfin, définissez la [propriété ErrorMessage](https://msdn.microsoft.com/library/system.web.ui.webcontrols.basevalidator.errormessage(VS.80).aspx) à « Vous devez fournir le nom du produit » et le [Text (propriété)](https://msdn.microsoft.com/library/system.web.ui.webcontrols.basevalidator.text(VS.80).aspx) à «\*». Le `Text` valeur de propriété, si fourni, est le texte affiché par le contrôle de validation si la validation échoue. Le `ErrorMessage` les valeurs de propriété, qui est requis, sont utilisé par le contrôle ValidationSummary ; si le `Text` valeur de propriété est omise, la `ErrorMessage` valeur de propriété est également le texte affiché par le contrôle de validation d’entrée non valide.

Après avoir défini ces trois propriétés de RequiredFieldValidator, votre écran doit ressembler à la Figure 7.


[![Définir les propriétés de texte, de message d’erreur et de ControlToValidate de RequiredFieldValidator](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image20.png)](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image19.png)

**Figure 7**: définir le RequiredFieldValidator `ControlToValidate`, `ErrorMessage`, et `Text` propriétés ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image21.png))


Avec RequiredFieldValidator ajouté à la `ProductName` `EditItemTemplate`, tous les que reste plus qu’à ajouter la validation nécessaire à la `UnitPrice` `EditItemTemplate`. Étant donné que nous avons décidé que, pour cette page, le `UnitPrice` est facultatif lorsque modification d’un enregistrement, nous n’avez pas besoin ajouter un contrôle RequiredFieldValidator. Nous ne, toutefois, besoin d’ajouter un CompareValidator pour vous assurer que le `UnitPrice`, si fourni, est correctement mise en forme comme une valeur monétaire et est supérieur ou égal à 0.

Avant d’ajouter le contrôle CompareValidator pour le `UnitPrice` `EditItemTemplate`, nous allons d’abord modifier les ID du contrôle de zone de texte Web à partir de `TextBox2` à `EditUnitPrice`. Après avoir apporté cette modification, ajoutez le contrôle CompareValidator, définition de sa `ControlToValidate` propriété `EditUnitPrice`, ses `ErrorMessage` à la propriété « le prix doit être supérieur ou égal à zéro et ne peut pas inclure le symbole monétaire » et son `Text` propriété "\*".

Pour indiquer que le `UnitPrice` valeur doit être supérieure ou égale à 0, définir de CompareValidator [propriété Operator](https://msdn.microsoft.com/library/system.web.ui.webcontrols.comparevalidator.operator(VS.80).aspx) à `GreaterThanEqual`, ses [propriété ValueToCompare](https://msdn.microsoft.com/library/system.web.ui.webcontrols.comparevalidator.valuetocompare(VS.80).aspx) à « 0 » et ses [ La propriété de type](https://msdn.microsoft.com/library/system.web.ui.webcontrols.basecomparevalidator.type.aspx) à `Currency`. L’exemple de syntaxe déclarative suivant le `UnitPrice` de TemplateField `EditItemTemplate` après avoir apporté ces modifications :

[!code-aspx[Main](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/samples/sample3.aspx)]

Après avoir apporté ces modifications, ouvrez la page dans un navigateur. Si vous tentez d’omettre le nom ou entrez une valeur de prix non valide lors de la modification d’un produit, un astérisque apparaît en regard de la zone de texte. Comme le montre la Figure 8, une valeur de prix qui inclut le symbole monétaire, telles que $19.95 est considéré comme non valide. De CompareValidator `Currency` `Type` permet de séparateurs de chiffres (par exemple, des virgules ou des points, selon les paramètres de culture) et un signe de début plus ou moins, mais *pas* autoriser un symbole de devise. Ce comportement peut perplex utilisateurs comme l’interface de modification actuellement rend le `UnitPrice` en utilisant le format monétaire.

> [!NOTE]
> N’oubliez pas que dans les *événements associés à l’insertion, mise à jour et suppression* didacticiel, nous avons défini le BoundField `DataFormatString` propriété `{0:c}` pour mettre en forme comme une valeur monétaire. En outre, nous avons défini le `ApplyFormatInEditMode` true à la propriété, à l’origine le GridView d’édition interface à mettre en forme le `UnitPrice` sous forme de devise. Lorsque vous convertissez la BoundField en TemplateField, Visual Studio avez noté ces paramètres et mise en forme de la zone de texte `Text` propriété sous forme de devise à l’aide de la syntaxe de liaison de données `<%# Bind("UnitPrice", "{0:c}") %>`.


[![Un astérisque apparaît en regard des zones de texte avec une entrée non valide](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image23.png)](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image22.png)

**Figure 8**: un astérisque apparaît à côté de zones de texte avec une entrée non valide ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image24.png))


Lors de la validation fonctionne comme-est, l’utilisateur doit supprimer manuellement le symbole monétaire lors de la modification d’un enregistrement qui n’est pas acceptable. Pour résoudre ce problème, nous avons trois options :

1. Configurer le `EditItemTemplate` afin que le `UnitPrice` valeur n’est pas mise en forme comme une valeur monétaire.
2. Autoriser l’utilisateur à entrer un symbole monétaire en supprimant les CompareValidator et en le remplaçant par RegularExpressionValidator qui vérifie une valeur monétaire correctement mis en forme correctement. Le problème ici est que l’expression régulière pour valider une valeur de devise n’est pas automatique et nécessite l’écriture de code si nous voulons incorporer des paramètres de culture.
3. Retirez le contrôle de validation et s’appuient sur la logique de validation côté serveur dans du GridView `RowUpdating` Gestionnaire d’événements.

Attardons-nous avec l’option #1 pour cet exercice. Actuellement le `UnitPrice` est mise en forme comme une valeur monétaire en raison de l’expression de liaison de données pour la zone de texte dans le `EditItemTemplate`: `<%# Bind("UnitPrice", "{0:c}") %>`. Remplacez l’instruction de liaison par `Bind("UnitPrice", "{0:n2}")`, qui met en forme le résultat sous forme de nombre à deux chiffres de précision. Procéder directement par le biais de la syntaxe déclarative ou en cliquant sur le lien Modifier les DataBindings à partir de la `EditUnitPrice` zone de texte dans le `UnitPrice` de TemplateField `EditItemTemplate` (voir les Figures 9 et 10).


[![Cliquez sur le lien Modifier les DataBindings de la zone de texte](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image26.png)](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image25.png)

**Figure 9**: cliquez sur le lien Modifier les DataBindings de la zone de texte ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image27.png))


[![Spécifiez le spécificateur de Format dans l’instruction de liaison](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image29.png)](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image28.png)

**La figure 10**: spécifiez le spécificateur de Format dans le `Bind` instruction ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image30.png))


Avec cette modification, le prix mis en forme dans l’interface de modification inclut des virgules comme séparateurs de groupe et un point comme séparateur décimal, mais laisse hors tension le symbole monétaire.

> [!NOTE]
> Le `UnitPrice` `EditItemTemplate` n’inclut pas un RequiredFieldValidator, ce qui permet la publication de surgir et la logique de mise à jour pour commencer. Toutefois, le `RowUpdating` Gestionnaire d’événements copiée à partir de la *examiner les événements associés insertion, mise à jour et suppression* didacticiel inclut un contrôle par programmation garantit que le `UnitPrice` est fourni. N’hésitez pas à supprimer cette logique, laissez-le dans en tant que-est, ou ajoutez un contrôle RequiredFieldValidator à la `UnitPrice` `EditItemTemplate`.


## <a name="step-4-summarizing-data-entry-problems"></a>Étape 4 : Synthèse des problèmes de saisie de données

Outre les contrôles de cinq validation, ASP.NET inclut les [contrôle ValidationSummary](https://msdn.microsoft.com/library/f9h59855(VS.80).aspx), qui affiche le `ErrorMessage` s de ces contrôles de validation qui a détecté des données non valides. Ces données peuvent être affichées sous forme de texte sur la page web ou via un messagebox modale, côté client. Nous allons améliorer ce didacticiel pour inclure un messagebox côté client synthèse de tous les problèmes de validation.

Pour ce faire, faites glisser un contrôle ValidationSummary à partir de la boîte à outils vers le concepteur. L’emplacement du contrôle de Validation n’importe vraiment, étant donné que nous allons configurer pour afficher uniquement la synthèse comme un messagebox. Après avoir ajouté le contrôle, définissez son [ShowSummary, propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.validationsummary.showsummary(VS.80).aspx) à `false` et son [à la propriété ShowMessageBox](https://msdn.microsoft.com/library/system.web.ui.webcontrols.validationsummary.showmessagebox(VS.80).aspx) à `true`. Avec cet ajout, des erreurs de validation sont résumées dans un messagebox côté client.


[![Les erreurs de Validation sont résumés dans un Messagebox côté Client](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image32.png)](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image31.png)

**Figure 11**: les erreurs de Validation sont résumés dans un Messagebox côté Client ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image33.png))


## <a name="step-5-adding-the-validation-controls-to-the-detailsviewsinsertitemtemplate"></a>Étape 5 : Ajout de contrôles de Validation à du contrôle DetailsView`InsertItemTemplate`

Le reste de ce didacticiel qu’à ajouter les contrôles de validation à l’interface de l’insertion du DetailsView. Le processus d’ajout de contrôles de validation pour les modèles de DetailsView est identique à celui examiné à l’étape 3 ; Par conséquent, nous allons breeze via la tâche dans cette étape. Comme nous l’avons fait de GridView `EditItemTemplate` s, nous vous invitons à renommer le `ID` s des zones de texte à partir de l’apercevoir `TextBox1` et `TextBox2` à `InsertProductName` et `InsertUnitPrice`.

Ajoutez un contrôle RequiredFieldValidator à la `ProductName` `InsertItemTemplate`. Définir le `ControlToValidate` à la `ID` de la zone de texte dans le modèle, son `Text` propriété »\*» et ses `ErrorMessage` propriété « Vous devez fournir le nom du produit ».

Étant donné que la `UnitPrice` est requis pour cette page lors de l’ajout d’un nouvel enregistrement, ajoutez un contrôle RequiredFieldValidator à la `UnitPrice` `InsertItemTemplate`, le paramètre son `ControlToValidate`, `Text`, et `ErrorMessage` propriétés en conséquence. Enfin, ajoutez un contrôle CompareValidator à la `UnitPrice` `InsertItemTemplate` configuration, son `ControlToValidate`, `Text`, `ErrorMessage`, `Type`, `Operator`, et `ValueToCompare` propriétés comme nous l’avons fait avec la `UnitPrice`de CompareValidator dans du GridView `EditItemTemplate`.

Après avoir ajouté ces contrôles de validation, un nouveau produit ne peut pas être ajouté au système si son nom n’est pas fourni ou si son prix est un nombre négatif ou illégalement mis en forme.


[![La logique de validation a été ajoutée à l’Interface d’insertion du DetailsView](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image35.png)](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image34.png)

**Figure 12**: la logique de Validation a été ajoutée à l’Interface d’insertion du DetailsView ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image36.png))


## <a name="step-6-partitioning-the-validation-controls-into-validation-groups"></a>Étape 6 : Partitionnement les contrôles de Validation des groupes de Validation

Notre page se compose de deux jeux logiquement disparates de contrôles de validation : ceux qui correspondent au GridView d’édition interface et ceux qui correspondent au contrôle DetailsView de l’insertion d’interface. Par défaut, lorsqu’une publication (postback) se produit *tous les* des contrôles de validation sur la page sont vérifiées. Toutefois, lorsque vous modifiez un enregistrement, nous ne voulons pour valider des contrôles de validation de l’interface de l’insertion du DetailsView. Figure 13 illustre notre dilemme actuel lorsqu’un utilisateur modifie un produit avec des valeurs autorisées parfaitement, mise à jour en cliquant sur provoque une erreur de validation, car les valeurs de nom et le prix de l’interface d’insertion sont vides.


[![Mise à jour d’un produit provoque des contrôles de Validation de l’Interface insertion incendie](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image38.png)](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image37.png)

**Figure 13**: mise à jour d’un produit provoque des contrôles de Validation de l’Interface insertion incendie ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image39.png))


Les contrôles de validation dans ASP.NET 2.0 peuvent être partitionnés en groupes de validation via leurs `ValidationGroup` propriété. Pour associer un ensemble de contrôles de validation dans un groupe, il suffit de définir leurs `ValidationGroup` à la même valeur de propriété. Pour notre didacticiel, définissez la `ValidationGroup` propriétés des contrôles de validation dans TemplateField du GridView à `EditValidationControls` et `ValidationGroup` propriétés de TemplateField du DetailsView à `InsertValidationControls`. Ces modifications peuvent être effectuées directement dans le balisage déclaratif ou via la fenêtre Propriétés lors de l’utilisation du concepteur modifier l’interface de modèle.

En plus de la validation des contrôles, le bouton et liés à un bouton dans ASP.NET 2.0 incluent également un `ValidationGroup` propriété. Programmes de validation d’un groupe de validation sont vérifiées pour la validité uniquement lorsqu’une publication (postback) ne soit induit par un bouton qui a le même `ValidationGroup` paramètre de propriété. Par exemple, dans l’ordre pour le bouton d’insertion du DetailsView pour déclencher le `InsertValidationControls` groupe validation nous devons définir le CommandField `ValidationGroup` propriété `InsertValidationControls` (voir Figure 14). En outre, définissez du GridView de CommandField `ValidationGroup` propriété `EditValidationControls`.


[![Propriété de ValidationGroup de CommandField à InsertValidationControls du jeu le contrôle DetailsView](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image41.png)](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image40.png)

**Figure 14**: définir le contrôle de DetailsView de CommandField `ValidationGroup` propriété `InsertValidationControls` ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/_static/image42.png))


Après ces modifications, le contrôle DetailsView du contrôle GridView TemplateField et CommandFields doit ressembler à ce qui suit :

Le contrôle du DetailsView TemplateField et CommandField

[!code-aspx[Main](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/samples/sample4.aspx)]

Le contrôle du GridView CommandField et TemplateField

[!code-aspx[Main](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/samples/sample5.aspx)]

À ce stade les contrôles de validation spécifique à l’édition déclenchent uniquement lorsque du GridView mise à jour bouton et le feu de contrôles de validation spécifique à l’insertion uniquement lors du DetailsView Insert bouton, résoudre le problème de mise en surbrillance par la Figure 13. Toutefois, cette modification notre contrôle ValidationSummary n’affiche plus lors de la saisie des données non valides. Le contrôle ValidationSummary contient également un `ValidationGroup` propriété et présente uniquement des informations récapitulatives pour ces contrôles de validation dans son groupe de validation. Par conséquent, nous devons disposer de deux contrôles de validation dans cette page, un pour le `InsertValidationControls` groupe de validation et l’autre pour `EditValidationControls`.

[!code-aspx[Main](adding-validation-controls-to-the-editing-and-inserting-interfaces-cs/samples/sample6.aspx)]

Avec cet ajout notre didacticiel est terminé !

## <a name="summary"></a>Récapitulatif

Tandis que BoundFields peut fournir à la fois une interface d’insertion et d’édition, l’interface n’est pas personnalisable. En règle générale, vous souhaitez ajouter des contrôles de validation à la modification et l’insertion d’interface pour vous assurer que l’utilisateur entre les entrées requises dans un format conforme. Pour ce faire, nous devons convertir les BoundFields TemplateField et ajouter les contrôles de validation pour les modèles appropriés. Dans ce didacticiel, nous avons étendu l’exemple à partir de la *examiner les événements associés insertion, mise à jour et suppression* (didacticiel), ajout de contrôles de validation à deux DetailsView de l’insertion d’interface et du contrôle GridView interface de modification. En outre, nous avons vu comment afficher les informations de résumé de validation à l’aide du contrôle ValidationSummary et comment partitionner les contrôles de validation sur la page dans des groupes distincts de validation.

Comme nous l’avons vu dans ce didacticiel, TemplateField autorise les interfaces de modification et d’insertion être augmentés pour inclure les contrôles de validation. TemplateField peut également être étendu pour inclure les contrôles Web d’entrée supplémentaires, l’activation de la zone de texte à remplacer par un contrôle Web plus adapté. Dans notre étape suivante du didacticiel, nous verrons comment remplacer le contrôle de zone de texte avec un contrôle lié aux données DropDownList, qui est idéale lors de la modification d’une clé étrangère (tel que `CategoryID` ou `SupplierID` dans le `Products` table).

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Liz Shulok et Zack Jones. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](handling-bll-and-dal-level-exceptions-in-an-asp-net-page-cs.md)
[Suivant](customizing-the-data-modification-interface-cs.md)
