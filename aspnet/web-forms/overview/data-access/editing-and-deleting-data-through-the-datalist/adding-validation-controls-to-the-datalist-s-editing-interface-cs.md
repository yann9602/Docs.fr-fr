---
uid: web-forms/overview/data-access/editing-and-deleting-data-through-the-datalist/adding-validation-controls-to-the-datalist-s-editing-interface-cs
title: "Ajout de contrôles de Validation pour le contrôle DataList de modification d’Interface (c#) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous verrons combien il est facile d’ajouter des contrôles de validation à EditItemTemplate de DataList afin de fournir une édition int : utilisateur sans poser de problèmes plus..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/30/2006
ms.topic: article
ms.assetid: 3ecc21c5-da0e-40ab-abb4-fac1e47398ad
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/editing-and-deleting-data-through-the-datalist/adding-validation-controls-to-the-datalist-s-editing-interface-cs
msc.type: authoredcontent
ms.openlocfilehash: b80b25933679d5c5b465af24cf6ff5d3b824b401
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="adding-validation-controls-to-the-datalists-editing-interface-c"></a>Ajout de contrôles de Validation à l’Interface de modification du contrôle DataList (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_39_CS.exe) ou [télécharger le PDF](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/datatutorial39cs1.pdf)

> Dans ce didacticiel, nous allons voir combien il est facile d’ajouter des contrôles de validation à EditItemTemplate de DataList afin de fournir une interface utilisateur de modification sans poser de problèmes plus.


## <a name="introduction"></a>Introduction

Dans le contrôle DataList modification didacticiels jusqu’ici, les modification des interfaces de DataList n’ont pas incluses aucune validation des entrées utilisateur proactive même si des entrées d’utilisateur non valide comme un nom de produit manquante ou les résultats de prix négatif dans une exception. Dans le [didacticiel précédent](handling-bll-and-dal-level-exceptions-cs.md) nous avons examiné comment ajouter des exceptions de code du contrôle DataList s `UpdateCommand` Gestionnaire d’événements afin d’intercepter et normalement afficher plus d’informations sur les exceptions qui ont été générées. Dans l’idéal, cependant, l’interface de modification inclut des contrôles de validation pour empêcher un utilisateur d’entrer ces données non valides en premier lieu.

Dans ce didacticiel, nous verrons combien il est facile pour ajouter des contrôles de validation pour le contrôle DataList s `EditItemTemplate` afin de fournir une interface utilisateur de modification plus sans poser de problèmes. Plus précisément, ce didacticiel prend l’exemple créé dans le didacticiel précédent et augmente l’interface de modification pour inclure la validation appropriées.

## <a name="step-1-replicating-the-example-fromhandling-bll--and-dal-level-exceptionshandling-bll-and-dal-level-exceptions-csmd"></a>Étape 1 : Réplication de l’exemple à partir de[la gestion des Exceptions d’au niveau de la couche BLL et de la couche DAL](handling-bll-and-dal-level-exceptions-cs.md)

Dans le [BLL - gestion et Exceptions au niveau de la couche DAL](handling-bll-and-dal-level-exceptions-cs.md) didacticiel, nous avons créé une page qui liste les noms et les prix des produits dans les deux colonnes, modifiable DataList. Notre objectif de ce didacticiel est d’enrichir l’interface de modification s DataList pour inclure les contrôles de validation. En particulier, notre logique de validation est :

- Exiger que le nom du produit s
- Assurez-vous que la valeur entrée pour le prix est un format de devise valide
- Vérifiez que la valeur entrée pour le prix est supérieur ou égal à zéro, depuis un négatif `UnitPrice` valeur est non conforme

Avant que nous pouvons examiner d’augmentation de l’exemple précédent, pour inclure la validation, nous devons tout d’abord répliquer l’exemple à partir de la `ErrorHandling.aspx` page dans le `EditDeleteDataList` dossier à la page de ce didacticiel, `UIValidation.aspx`. Pour cela nous avons besoin de copier sur les deux le `ErrorHandling.aspx` page balisage déclaratif de s et son code source. Tout d’abord copier sur le balisage déclaratif en effectuant les étapes suivantes :

1. Ouvrez le `ErrorHandling.aspx` page dans Visual Studio
2. Atteindre le balisage déclaratif page s (cliquez sur le bouton de la Source au bas de la page)
3. Copiez le texte dans le `<asp:Content>` et `</asp:Content>` balises (lignes 3 et 32), comme illustré dans la Figure 1.


[![Copier le texte dans le &lt;asp : Content&gt; contrôle](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image2.png)](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image1.png)

**Figure 1**: copiez le texte dans le `<asp:Content>` contrôle ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image3.png))


1. Ouvrez le `UIValidation.aspx` page
2. Atteindre le balisage déclaratif s de page
3. Collez le texte dans le `<asp:Content>` contrôle.

Pour copier le code source, ouvrez le `ErrorHandling.aspx.vb` page et copiez simplement le texte *dans* la `EditDeleteDataList_ErrorHandling` classe. Copiez les trois gestionnaires d’événements (`Products_EditCommand`, `Products_CancelCommand`, et `Products_UpdateCommand`) avec le `DisplayExceptionDetails` (méthode), mais ne **pas** copier la déclaration de classe ou `using` instructions. Coller le texte copié *dans* le `EditDeleteDataList_UIValidation` classe dans `UIValidation.aspx.vb`.

Après avoir déplacé le contenu et le code à partir de `ErrorHandling.aspx` à `UIValidation.aspx`, prenez un moment pour tester les pages dans un navigateur. Vous devez voir le même résultat et rencontrer les mêmes fonctionnalités dans chacune de ces deux pages (voir Figure 2).


[![La Page UIValidation.aspx reproduit la fonctionnalité de ErrorHandling.aspx](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image5.png)](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image4.png)

**Figure 2**: le `UIValidation.aspx` Page reproduit la fonctionnalité de `ErrorHandling.aspx` ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image6.png))


## <a name="step-2-adding-the-validation-controls-to-the-datalist-s-edititemtemplate"></a>Étape 2 : Ajout de contrôles de Validation à EditItemTemplate s DataList

Lors de la construction des formulaires de saisie de données, il est important que les utilisateurs entrent tous les champs obligatoires et que leurs données d’entrée fournies sont des valeurs juridiques, mis en forme correctement. Pour aider à garantir que d’une entrée utilisateur s est valides, ASP.NET fournit les cinq contrôles de validation intégrées qui sont conçues pour valider la valeur d’un contrôle Web d’entrée unique :

- [RequiredFieldValidator](https://msdn.microsoft.com/library/5hbw267h(VS.80).aspx) garantit qu’une valeur a été fournie.
- [CompareValidator](https://msdn.microsoft.com/library/db330ayw(VS.80).aspx) valide une valeur à une autre valeur de contrôle Web ou une valeur constante ou garantit que le format de valeur s est autorisé pour un type de données spécifié
- [RangeValidator](https://msdn.microsoft.com/library/f70d09xt.aspx) garantit qu’une valeur est comprise dans une plage de valeurs
- [RegularExpressionValidator](https://msdn.microsoft.com/library/eahwtc9e.aspx) valide une valeur par rapport à un [expression régulière](http://en.wikipedia.org/wiki/Regular_expression)
- [CustomValidator](https://msdn.microsoft.com/library/9eee01cx(VS.80).aspx) valide une valeur par rapport à une méthode personnalisée, définie par l’utilisateur

Pour plus d’informations sur ces cinq contrôles faire référence à la [Ajout de contrôles de Validation à la modification et l’insertion des Interfaces](../editing-inserting-and-deleting-data/adding-validation-controls-to-the-editing-and-inserting-interfaces-cs.md) didacticiel ou retirer le [section contrôles de Validation](https://quickstarts.asp.net/QuickStartv20/aspnet/doc/ctrlref/validation/default.aspx) de la [Les didacticiels de démarrage rapide ASP.NET](https://quickstarts.asp.net).

Pour notre didacticiel, nous devrons utiliser un contrôle RequiredFieldValidator pour vous assurer qu’une valeur pour le nom de produit a été fournie et un CompareValidator pour vous assurer que le prix entré a une valeur supérieure ou égale à 0 et est présenté dans un format monétaire valide.

> [!NOTE]
> Alors que ASP.NET 1.x avait ces mêmes contrôles de cinq validation, ASP.NET 2.0 a ajouté un certain nombre d’améliorations, principal deux en cours d’un script côté client prend en charge les navigateurs Internet Explorer et permettent aux contrôles de validation de partition sur une page dans groupes de validation. Pour plus d’informations sur les nouvelles fonctionnalités de contrôle de validation dans la version 2.0, reportez-vous à [ayant été Disséqué les contrôles de Validation dans ASP.NET 2.0](http://aspnet.4guysfromrolla.com/articles/112305-1.aspx).


Permettent de commencer par ajouter les contrôles de validation nécessaires au contrôle DataList s s `EditItemTemplate`. Cette tâche peut être effectuée par le biais du concepteur en cliquant sur le lien Modifier les modèles à partir de la balise active de DataList s, ou la syntaxe déclarative. Permettent de parcourir le s le processus à l’aide de l’option Modifier les modèles à partir de la vue de conception. Après avoir choisi de modifier le contrôle DataList s `EditItemTemplate`, ajoutez un contrôle RequiredFieldValidator en le faisant glisser à partir de la boîte à outils dans l’interface de modification de modèle, placer après le `ProductName` zone de texte.


[![Ajoutez un contrôle RequiredFieldValidator à EditItemTemplate après la zone de texte ProductName](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image8.png)](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image7.png)

**Figure 3**: ajouter un contrôle RequiredFieldValidator à la `EditItemTemplate After` le `ProductName` zone de texte ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image9.png))


Tous les contrôles de validation de travail à valider l’entrée d’un contrôle Web ASP.NET unique. Par conséquent, nous devons indiquer que nous venons d’ajouter RequiredFieldValidator doit valider par rapport à la `ProductName` zone de texte ; cela en définissant le contrôle de validation s [ `ControlToValidate` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.basevalidator.controltovalidate(VS.80).aspx) à la `ID` de le contrôle Web approprié (`ProductName`, dans cette instance). Ensuite, définissez la [ `ErrorMessage` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.basevalidator.errormessage(VS.80).aspx) à, vous devez fournir le nom de produit s et [ `Text` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.basevalidator.text(VS.80).aspx) à \*. Le `Text` valeur de propriété, si fourni, est le texte affiché par le contrôle de validation si la validation échoue. Le `ErrorMessage` les valeurs de propriété, qui est requis, sont utilisé par le contrôle ValidationSummary ; si le `Text` valeur de propriété est omise, la `ErrorMessage` valeur de propriété est affichée par le contrôle de validation d’entrée non valide.

Après avoir défini ces trois propriétés de RequiredFieldValidator, votre écran doit ressembler à la Figure 4.


[![Définir le RequiredFieldValidator s ControlToValidate, message d’erreur et les propriétés de texte](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image11.png)](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image10.png)

**Figure 4**: définir le s RequiredFieldValidator `ControlToValidate`, `ErrorMessage`, et `Text` propriétés ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image12.png))


Avec RequiredFieldValidator ajouté à la `EditItemTemplate`, tous les que reste plus qu’à ajouter la validation nécessaire pour le prix du produit s zone de texte. Étant donné que le `UnitPrice` est facultatif lorsque vous modifiez un enregistrement, nous n’avez pas besoin pour ajouter un contrôle RequiredFieldValidator. Nous ne, toutefois, besoin d’ajouter un CompareValidator pour vous assurer que le `UnitPrice`, si fourni, est correctement mise en forme comme une valeur monétaire et est supérieur ou égal à 0.

Ajouter le contrôle CompareValidator dans le `EditItemTemplate` et définir son `ControlToValidate` propriété `UnitPrice`, ses `ErrorMessage` propriété le prix doit être supérieur ou égal à zéro et ne peut pas inclure le symbole monétaire et son `Text` propriété \*. Pour indiquer que le `UnitPrice` valeur doit être supérieure ou égale à 0, définir le s CompareValidator [ `Operator` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.comparevalidator.operator(VS.80).aspx) à `GreaterThanEqual`, ses [ `ValueToCompare` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.comparevalidator.valuetocompare(VS.80).aspx) 0, et son [ `Type` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.basecomparevalidator.type.aspx) à `Currency`.

Après l’ajout de ces deux contrôles de validation, du contrôle DataList s `EditItemTemplate` la syntaxe déclarative s doit ressembler à ce qui suit :


[!code-aspx[Main](adding-validation-controls-to-the-datalist-s-editing-interface-cs/samples/sample1.aspx)]

Après avoir apporté ces modifications, ouvrez la page dans un navigateur. Si vous tentez d’omettre le nom ou entrez une valeur de prix non valide lors de la modification d’un produit, un astérisque apparaît en regard de la zone de texte. Comme le montre la Figure 5, une valeur de prix qui inclut le symbole monétaire, telles que $19.95 est considéré comme non valide. Les s CompareValidator `Currency` `Type` permet de séparateurs de chiffres (par exemple, des virgules ou des points, selon les paramètres de culture) et un signe de début plus ou moins, mais *pas* autoriser un symbole de devise. Ce comportement peut perplex utilisateurs comme l’interface de modification actuellement rend le `UnitPrice` en utilisant le format monétaire.


[![Un astérisque apparaît en regard des zones de texte avec une entrée non valide](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image14.png)](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image13.png)

**Figure 5**: un astérisque apparaît à côté de zones de texte avec une entrée non valide ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image15.png))


Lors de la validation fonctionne comme-est, l’utilisateur doit supprimer manuellement le symbole monétaire lors de la modification d’un enregistrement qui n’est pas acceptable. En outre, s’il ne sont pas valides entrées dans la modification de l’interface ni la mise à jour ni annuler les boutons, lorsque vous cliquez sur, appellent une publication (postback). Dans l’idéal, le bouton Annuler retournerait du contrôle DataList à son état avant modification, quelle que soit la validité des entrées utilisateur s. En outre, nous devons pour vous assurer que les données de la page s sont valides avant de mettre à jour les informations de produit dans le contrôle DataList s `UpdateCommand` Gestionnaire d’événements, comme les contrôles de validation logique côté client peut être ignorée par les utilisateurs dont les navigateurs aucune n en charge JavaScript ou pour la prise en charge est désactivée.

## <a name="removing-the-currency-symbol-from-the-edititemtemplate-s-unitprice-textbox"></a>Supprimer le symbole de devise à partir de la zone de texte EditItemTemplate s UnitPrice

Lorsque vous utilisez le s CompareValidator `Currency``Type`, l’entrée en cours de validation ne doit pas inclure tous les symboles monétaires. La présence de ces symboles entraîne CompareValidator marquer l’entrée comme étant non valide. Toutefois, notre interface de modification comprend actuellement un symbole de devise dans la `UnitPrice` zone de texte, ce qui signifie que l’utilisateur doit supprimer explicitement le symbole monétaire avant d’enregistrer leurs modifications. Pour résoudre ce problème, nous avons trois options :

1. Configurer le `EditItemTemplate` afin que le `UnitPrice` valeur de la zone de texte n’est pas mis en forme comme une valeur monétaire.
2. Autoriser l’utilisateur à entrer un symbole monétaire en supprimant les CompareValidator et en remplaçant par RegularExpressionValidator qui vérifie si une valeur de devise correctement mis en forme. Le défi ici est que l’expression régulière pour valider une valeur de devise n’est pas aussi simple que CompareValidator et nécessite l’écriture de code si nous voulons incorporer des paramètres de culture.
3. Retirez le contrôle de validation et s’appuient sur la logique de validation personnalisée côté serveur dans le GridView s `RowUpdating` Gestionnaire d’événements.

Permettent de s go avec l’option 1 pour ce didacticiel. Actuellement le `UnitPrice` est mise en forme en tant que valeur monétaire en raison de l’expression de liaison de données pour la zone de texte dans le `EditItemTemplate`: `<%# Eval("UnitPrice", "{0:c}") %>`. Modifier la `Eval` instruction `Eval("UnitPrice", "{0:n2}")`, qui met en forme le résultat sous forme de nombre à deux chiffres de précision. Cela est possible directement par le biais de la syntaxe déclarative ou en cliquant sur le lien Modifier les DataBindings à partir de la `UnitPrice` zone de texte dans le contrôle DataList s `EditItemTemplate`.

Avec cette modification, le prix mis en forme dans l’interface de modification inclut des virgules comme séparateurs de groupe et un point comme séparateur décimal, mais laisse hors tension le symbole monétaire.

> [!NOTE]
> Lorsque vous supprimez le format monétaire à partir de l’interface modifiable, je trouve utile pour placer le symbole monétaire sous forme de texte en dehors de la zone de texte. Ceci comme un indicateur à l’utilisateur qu’ils n’avez pas besoin de fournir le symbole monétaire.


## <a name="fixing-the-cancel-button"></a>Correction du bouton Annuler

Par défaut, les contrôles de validation Web émettent JavaScript pour effectuer la validation côté client. Lorsque l’utilisateur clique sur un bouton, LinkButton ou ImageButton, les contrôles de validation sur la page sont vérifiées sur la côté client avant la publication. S’il existe des données non valides, la publication est annulée. Pour certains boutons, cependant, la validité des données peut être immatérielle ; Dans ce cas, la publication annulée en raison de données non valides est une simple nuisance.

Le bouton Annuler est un exemple. Imaginez qu’un utilisateur entre des données non valides, tels que l’omission du nom de produit s, puis décide she ne t voulez-vous enregistrer le produit une fois toutes les et appuie sur le bouton Annuler. Actuellement, le bouton Annuler déclenche les contrôles de validation sur la page, ce qui signalent que le nom du produit est manquant et empêcher la publication (postback). Notre utilisateur dispose à taper du texte dans le `ProductName` zone de texte pour annuler le processus de modification.

Heureusement, le bouton, LinkButton et ImageButton ont un [ `CausesValidation` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.button.causesvalidation.aspx) qui peut indiquer ou non en cliquant sur le bouton doit initialiser la logique de validation (valeur par défaut est `True`). Définir le bouton Annuler s `CausesValidation` propriété `False`.

## <a name="ensuring-the-inputs-are-valid-in-the-updatecommand-event-handler"></a>Assurer les entrées sont valides dans le Gestionnaire d’événements UpdateCommand

En raison du script côté client émis par les contrôles de validation, si un utilisateur entre des entrées non valides les contrôles de validation annuler toutes les publications (postback) initié par un bouton, LinkButton, ou ImageButton contrôles dont `CausesValidation` propriétés sont `True` (le valeur par défaut). Toutefois, si un utilisateur visite avec un navigateur archaïque ou dont prise en charge JavaScript a été désactivée, les vérifications de validation côté client seront exécute pas.

Tous les contrôles de validation ASP.NET Répétez la logique de validation immédiatement lors de la publication et signaler la validité globale des entrées page s via le [ `Page.IsValid` propriété](https://msdn.microsoft.com/library/system.web.ui.page.isvalid.aspx). Toutefois, le flux de page n’est pas interrompu ou arrêté dans une façon basée sur la valeur de `Page.IsValid`. En tant que développeurs, il est de notre responsabilité pour vous assurer que le `Page.IsValid` a la valeur de propriété `True` avant de continuer avec le code qui suppose valid les données d’entrée.

Si un utilisateur a désactivé JavaScript, visite notre page, modifie un produit, entre une valeur du prix de trop coûteuse et clique sur le bouton de mise à jour, la validation côté client est ignorée et résulte d’une publication (postback). Lors de la publication, la page s ASP.NET `UpdateCommand` s’exécute le Gestionnaire d’événements et une exception est levée lors de la tentative d’analyse trop coûteuses à un `Decimal`. Étant donné que nous avons la gestion des exceptions, cette exception sera gérée normalement, mais nous aurions pu empêcher les données non valides glissement via en premier lieu par la procédure uniquement avec les `UpdateCommand` Gestionnaire d’événements si `Page.IsValid` a la valeur `True`.

Ajoutez le code suivant au début de la `UpdateCommand` Gestionnaire d’événements, immédiatement avant le `Try` bloc :


[!code-csharp[Main](adding-validation-controls-to-the-datalist-s-editing-interface-cs/samples/sample2.cs)]

Avec cet ajout, le produit va tenter de mettre à jour uniquement si les données envoyées seront valides. La plupart des utilisateurs a gagné t être en mesure de publication (postback) de données non valide en raison des scripts de côté client de contrôles de validation, mais les utilisateurs dont les navigateurs n en charge JavaScript ou qui ont JavaScript prend en charge est désactivé, peuvent ignorer les contrôles côté client et envoyer des données non valides.

> [!NOTE]
> Le lecteur perspicace sera n’oubliez pas que lors de la mise à jour des données avec le contrôle GridView, nous n’a besoin de vérifier explicitement la `Page.IsValid` propriété dans notre classe code-behind de la page s. C’est parce que le contrôle GridView consulte le `Page.IsValid` propriété pour nous et seulement se poursuit avec la mise à jour uniquement si elle retourne une valeur de `True`.


## <a name="step-3-summarizing-data-entry-problems"></a>Étape 3 : Synthèse des problèmes de saisie de données

Outre les contrôles de cinq validation, ASP.NET inclut les [contrôle ValidationSummary](https://msdn.microsoft.com/library/f9h59855(VS.80).aspx), qui affiche le `ErrorMessage` s de ces contrôles de validation qui a détecté des données non valides. Ces données peuvent être affichées sous forme de texte sur la page web ou via un messagebox modale, côté client. Permettent d’améliorer ce didacticiel pour inclure la synthèse de tous les problèmes de validation côté client messagebox s.

Pour ce faire, faites glisser un contrôle ValidationSummary à partir de la boîte à outils vers le concepteur. L’emplacement de t ne contrôle ValidationSummary vraiment a d’importance, car nous allons configurer pour uniquement afficher le résumé dans messagebox. Après avoir ajouté le contrôle, définissez son [ `ShowSummary` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.validationsummary.showsummary(VS.80).aspx) à `False` et son [ `ShowMessageBox` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.validationsummary.showmessagebox(VS.80).aspx) à `True`. Avec cet ajout, des erreurs de validation sont résumés dans un messagebox côté client (voir Figure 6).


[![Les erreurs de Validation sont résumés dans un Messagebox côté Client](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image17.png)](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image16.png)

**Figure 6**: les erreurs de Validation sont résumés dans un Messagebox côté Client ([cliquez pour afficher l’image en taille réelle](adding-validation-controls-to-the-datalist-s-editing-interface-cs/_static/image18.png))


## <a name="summary"></a>Récapitulatif

Dans ce didacticiel, nous avons vu comment réduire la probabilité d’exceptions à l’aide de contrôles de validation proactive vous assurer que les entrées de nos utilisateurs sont valides avant d’essayer de les utiliser dans le flux de travail de mise à jour. ASP.NET fournit les cinq contrôles Web validation qui sont conçues pour inspecter un site Web particulier contrôlent s d’entrée et rapport sur la validité d’entrée s. Dans ce didacticiel nous avons utilisé deux de ces cinq contrôles RequiredFieldValidator et CompareValidator pour vous assurer que le nom du produit s a été fourni et que le prix est un format monétaire avec une valeur supérieure ou égale à zéro.

Ajout de contrôles de validation à la s DataList modification d’interface est aussi simple qu’en les faisant glisser sur le `EditItemTemplate` à partir de la boîte à outils et en définissant un certain nombre de propriétés. Par défaut, les contrôles de validation émettent automatiquement le script de validation côté client ; elles fournissent également la validation côté serveur lors de la publication, en stockant le résultat cumulé dans le `Page.IsValid` propriété. Pour ignorer la validation côté client lorsque l’utilisateur clique sur un bouton, LinkButton ou ImageButton, le bouton Définir s `CausesValidation` propriété `False`. En outre, avant d’effectuer des tâches avec les données fournies lors de la publication, vérifiez que le `Page.IsValid` propriété renvoie `True`.

Tous du contrôle DataList modification didacticiels nous ve examiné jusqu'à présent ont des interfaces de modification très simples une zone de texte pour le nom de produit s et l’autre pour le prix. L’interface de modification, toutefois, peut contenir une combinaison de contrôles Web différents, tels que la compréhension des listes, des calendriers, des cases d’option, des cases à cocher et ainsi de suite. Dans notre étape suivante du didacticiel, nous allons examiner de création d’une interface qui utilise un certain nombre de contrôles Web.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont Dennis Patterson, Ken Pespisa et Liz Shulok. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](handling-bll-and-dal-level-exceptions-cs.md)
[Suivant](customizing-the-datalist-s-editing-interface-cs.md)
