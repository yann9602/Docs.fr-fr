---
uid: mvc/overview/older-versions-1/models-data/performing-simple-validation-vb
title: "Exécution de la Validation Simple (VB) | Documents Microsoft"
author: StephenWalther
description: "Découvrez comment effectuer la validation dans une application ASP.NET MVC. Dans ce didacticiel, Stephen Walther présente l’état du modèle et l’application d’assistance de la validation HTML..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/02/2009
ms.topic: article
ms.assetid: df6cf4b7-0bb3-4c4e-b17a-bd78a759a6bc
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/models-data/performing-simple-validation-vb
msc.type: authoredcontent
ms.openlocfilehash: 2bc4cdbcd267bcdd3e71abc4c52664ae62c5c02e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="performing-simple-validation-vb"></a>Exécution de la Validation Simple (VB)
====================
par [Stephen Walther](https://github.com/StephenWalther)

> Découvrez comment effectuer la validation dans une application ASP.NET MVC. Dans ce didacticiel, Stephen Walther présente vous à l’état du modèle et les programmes d’assistance HTML de la validation.


L’objectif de ce didacticiel est d’expliquer comment vous pouvez effectuer la validation au sein d’une application ASP.NET MVC. Par exemple, vous découvrez comment empêcher une personne de l’envoi d’un formulaire qui ne contient pas une valeur pour un champ obligatoire. Vous apprenez à utiliser l’état du modèle et les programmes d’assistance HTML de la validation.

## <a name="understanding-model-state"></a>État de modèle de présentation

Vous utilisez - état du modèle, ou plus précisément, le dictionnaire d’états de modèle - pour représenter des erreurs de validation. Par exemple, l’action dans la liste 1 Create() valide les propriétés d’une classe de produit avant d’ajouter la classe de produit pour une base de données.


Je ne suis pas recommandation que vous ajoutez votre logique de validation ou de la base de données à un contrôleur. Un contrôleur doit contenir uniquement la logique relatives au contrôle de flux d’application. Nous prenons un raccourci pour simplifier les choses.


**La liste 1 - Controllers\ProductController.vb**

[!code-vb[Main](performing-simple-validation-vb/samples/sample1.vb)]

Dans la liste de 1, les propriétés de nom, Description et UnitsInStock de la classe de produit sont validées. Si une de ces propriétés échoue un test de validation une erreur est ajoutée au dictionnaire d’états du modèle (représenté par la propriété ModelState de la classe de contrôleur).

S’il existe des erreurs dans l’état de modèle la propriété ModelState.IsValid retourne false. Dans ce cas, le formulaire HTML pour la création d’un nouveau produit s’affiche de nouveau. Sinon, s’il n’y a aucune erreur de validation, le nouveau produit est ajouté à la base de données.

## <a name="using-the-validation-helpers"></a>À l’aide de programmes d’assistance des Validation

L’infrastructure ASP.NET MVC inclut les deux programmes d’assistance de validation : l’application d’assistance Html.ValidationMessage() et l’application d’assistance Html.ValidationSummary(). Ces deux programmes d’assistance dans une vue vous permet d’afficher des messages d’erreur de validation.

Les programmes d’assistance Html.ValidationMessage() et Html.ValidationSummary() sont utilisés dans les créer et modifier des vues qui sont générés automatiquement par la génération de modèles automatique ASP.NET MVC. Suivez ces étapes pour générer la vue de créer :

1. Cliquez sur l’action Create() dans le contrôleur de produit et sélectionnez l’option de menu **ajouter une vue** (voir Figure 1).
2. Dans le **ajouter une vue** boîte de dialogue, cochez la case intitulée **créer une vue fortement typée** (voir Figure 2).
3. À partir de la **afficher la classe de données** liste déroulante, sélectionnez la classe de produit.
4. À partir de la **afficher le contenu** liste déroulante, sélectionnez Créer.
5. Cliquez sur le bouton **Ajouter**.


Assurez-vous que vous générez votre application avant l’ajout d’une vue. Dans le cas contraire, la liste de classes n’apparaît plus dans le **afficher la classe de données** liste déroulante.


[![La boîte de dialogue Nouveau projet](performing-simple-validation-vb/_static/image1.jpg)](performing-simple-validation-vb/_static/image1.png)

**Figure 01**: ajout d’une vue ([cliquez pour afficher l’image en taille réelle](performing-simple-validation-vb/_static/image2.png))


[![La boîte de dialogue Nouveau projet](performing-simple-validation-vb/_static/image2.jpg)](performing-simple-validation-vb/_static/image3.png)

**Figure 02**: création d’une vue fortement typée ([cliquez pour afficher l’image en taille réelle](performing-simple-validation-vb/_static/image4.png))


Après avoir effectué ces étapes, vous obtenez la vue de créer dans la liste 2.

**Listing 2 - Views\Product\Create.aspx**

[!code-aspx[Main](performing-simple-validation-vb/samples/sample2.aspx)]

Dans la liste 2, l’application d’assistance Html.ValidationSummary() est appelée immédiatement au-dessus du formulaire HTML. Ce programme d’assistance est utilisé pour afficher la liste des messages d’erreur de validation. L’application d’assistance Html.ValidationSummary() restitue les erreurs dans une liste à puces.

L’application d’assistance Html.ValidationMessage() est appelée en regard de chacun des champs de formulaire HTML. Ce programme d’assistance est utilisé pour afficher un message d’erreur à droite en regard d’un champ de formulaire. Dans le cas de liste 2, l’application d’assistance Html.ValidationMessage() affiche un astérisque lorsqu’il existe une erreur.

La page dans la Figure 3 illustre les messages d’erreur rendus par les programmes d’assistance de validation lorsque le formulaire est envoyé avec les champs manquants et des valeurs non valides.


[![La boîte de dialogue Nouveau projet](performing-simple-validation-vb/_static/image3.jpg)](performing-simple-validation-vb/_static/image5.png)

**Figure 03**: créer la vue, soumise avec des problèmes ([cliquez pour afficher l’image en taille réelle](performing-simple-validation-vb/_static/image6.png))


Notez que l’apparence du contenu HTML d’entrée, les champs sont également modifiés lorsqu’il existe une erreur de validation. Les convertisseurs d’assistance de Html.TextBox() un *classe = « erreur de validation d’entrée »* attribut lorsqu’il existe une erreur de validation associé à la propriété restituée par l’application d’assistance Html.TextBox().

Il existe trois classes de feuille de style en cascade utilisées pour contrôler l’apparence des erreurs de validation :

- entrée-validation-erreur - appliquée à la &lt;d’entrée&gt; balise rendue par l’application d’assistance Html.TextBox().
- champ-validation-erreur - appliquée à la &lt;span&gt; balise rendue par l’application d’assistance Html.ValidationMessage().
- validation-résumé-erreurs - appliquée à la &lt;ul&gt; balise rendue par l’application d’assistance Html.ValidationSumamry().

Vous pouvez modifier ces classes de feuille de style en cascade et par conséquent de modifier l’apparence des erreurs de validation, en modifiant le fichier Site.css situé dans le dossier de contenu.

> [!NOTE] 
> 
> La classe HtmlHelper inclut les propriétés statiques en lecture seule pour les noms de la validation de la récupération des connexes CSS classes. Ces propriétés statiques sont nommées ValidationInputCssClassName, ValidationFieldCssClassName et ValidationSummaryCssClassName.


## <a name="prebinding-validation-and-postbinding-validation"></a>Prebinding de Validation et la Validation de Postbinding

Si vous envoyez le formulaire HTML pour la création d’un produit et que vous entrez une valeur non valide pour le champ prix et aucune valeur pour le champ UnitsInStock, vous obtenez les messages de validation affichées dans la Figure 4. D'où proviennent ces messages d’erreur de validation ?


[![La boîte de dialogue Nouveau projet](performing-simple-validation-vb/_static/image4.jpg)](performing-simple-validation-vb/_static/image7.png)

**Figure 04**: erreurs de Validation Prebinding ([cliquez pour afficher l’image en taille réelle](performing-simple-validation-vb/_static/image8.png))


Il existe en fait deux types de messages d’erreur de validation - ceux générés avant que les champs de formulaire HTML sont liés à une classe et ceux générés une fois les champs de formulaire sont liés à la classe. En d’autres termes, il existe prebinding des erreurs de validation et postbinding des erreurs de validation.

L’action Create() exposée par le contrôleur de produit dans la liste 1 accepte une instance de la classe de produit. La signature de la méthode Create ressemble à ceci :

[!code-vb[Main](performing-simple-validation-vb/samples/sample3.vb)]

Les valeurs des champs de formulaire HTML dans le formulaire de création sont liés à la classe productToCreate par ce que l'on appelle un classeur de modèles. Le classeur de modèles par défaut ajoute automatiquement un message d’erreur à l’état du modèle lorsqu’il ne peut pas lier un champ de formulaire à une propriété de formulaire.

Le classeur de modèles par défaut ne peut pas lier la chaîne « apple » à la propriété de prix de la classe de produit. Vous ne pouvez pas affecter une chaîne à une propriété décimale. Par conséquent, le classeur de modèles ajoute une erreur à l’état du modèle.

Le classeur de modèles par défaut également Impossible d’attribuer la valeur Nothing à une propriété qui n’accepte pas la valeur Nothing. En particulier, le binder de modèle ne peut pas affecter la valeur Nothing à la propriété UnitsInStock. Une fois encore, le classeur de modèles abandonne et ajoute un message d’erreur à l’état du modèle.

Si vous souhaitez personnaliser l’apparence de ces messages d’erreur de prebinding vous devez créer des chaînes de ressources pour ces messages.

## <a name="summary"></a>Résumé

L’objectif de ce didacticiel a pour décrire les mécanismes de base de validation dans l’infrastructure ASP.NET MVC. Vous avez appris à utiliser l’état du modèle et les programmes d’assistance HTML de la validation. Nous avons abordé également la distinction entre prebinding et postbinding de validation. Dans d’autres didacticiels, nous aborderons diverses stratégies pour le déplacement de votre code de validation en dehors de vos contrôleurs et dans vos classes de modèle.

>[!div class="step-by-step"]
[Précédent](displaying-a-table-of-database-data-vb.md)
[Suivant](validating-with-the-idataerrorinfo-interface-vb.md)
