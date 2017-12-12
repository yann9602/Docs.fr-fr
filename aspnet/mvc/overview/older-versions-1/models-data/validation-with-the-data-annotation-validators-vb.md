---
uid: mvc/overview/older-versions-1/models-data/validation-with-the-data-annotation-validators-vb
title: "Validation avec les validateurs d’Annotation de données (VB) | Documents Microsoft"
author: microsoft
description: "Tirer parti de classeur de modèles de données d’Annotation pour effectuer la validation au sein d’une application ASP.NET MVC. Découvrez comment utiliser les différents types de programme de validation..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/29/2009
ms.topic: article
ms.assetid: 0d23ff2b-f2ec-434a-be3b-1180beeccba3
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/models-data/validation-with-the-data-annotation-validators-vb
msc.type: authoredcontent
ms.openlocfilehash: 227c1acb5e478047c4e5cdc7dbddedd703e91292
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="validation-with-the-data-annotation-validators-vb"></a>Validation avec les validateurs d’Annotation de données (Visual Basic)
====================
par [Microsoft](https://github.com/microsoft)

> Tirer parti de classeur de modèles de données d’Annotation pour effectuer la validation au sein d’une application ASP.NET MVC. Découvrez comment utiliser les différents types d’attributs de validateur et de les manipuler dans Microsoft Entity Framework.


Dans ce didacticiel, vous allez apprendre à utiliser les validateurs d’Annotation de données pour effectuer la validation dans une application ASP.NET MVC. L’avantage d’utiliser les validateurs d’Annotation de données est qu’ils permettent de vous permet d’effectuer la validation en ajoutant un ou plusieurs attributs, tels que les paramètres requis ou StringLength attribut simplement : pour une propriété de classe.

Avant de pouvoir utiliser les validateurs d’Annotation de données, vous devez télécharger le classeur de modèles des Annotations de données. Vous pouvez télécharger l’exemple de classeur du modèle d’Annotations de données depuis le site Web CodePlex en cliquant sur [ici](http://aspnet.codeplex.com/Release/ProjectReleases.aspx?ReleaseId=24471).


Il est important de comprendre que le classeur de modèles des Annotations de données n’est pas officiellement partie de l’infrastructure Microsoft ASP.NET MVC. Bien que le classeur de modèles des Annotations de données a été créé par l’équipe Microsoft ASP.NET MVC, Microsoft n’offre pas de prise en charge officielle du produit pour le classeur de modèles des Annotations de données décrites et utilisé dans ce didacticiel.


## <a name="using-the-data-annotation-model-binder"></a>À l’aide du classeur de modèles de données d’Annotation

Pour utiliser le classeur de modèles des Annotations de données dans une application ASP.NET MVC, vous devez d’abord ajouter une référence à l’assembly Microsoft.Web.Mvc.DataAnnotations.dll et le fichier System.ComponentModel.DataAnnotations.dll. Sélectionnez l’option de menu **projet, ajouter une référence**. Cliquez ensuite sur le **Parcourir** onglet et accédez à l’emplacement où vous avez téléchargé (et décompressé) l’exemple de classeur de modèles de données Annotations (consultez **Figure 1**).

[![](validation-with-the-data-annotation-validators-vb/_static/image2.png)](validation-with-the-data-annotation-validators-vb/_static/image1.png)

**Figure 1**: ajout d’une référence vers le classeur de modèles de données Annotations ([cliquez pour afficher l’image en taille réelle](validation-with-the-data-annotation-validators-vb/_static/image3.png))

Sélectionnez l’assembly Microsoft.Web.Mvc.DataAnnotations.dll et l’assembly de fichier System.ComponentModel.DataAnnotations.dll et cliquez sur le **OK** bouton.


Vous ne pouvez pas utiliser l’assembly de fichier System.ComponentModel.DataAnnotations.dll inclus avec Service Pack 1 de .NET Framework avec le classeur de modèles des Annotations de données. Vous devez utiliser la version de l’assembly de fichier System.ComponentModel.DataAnnotations.dll inclus avec le téléchargement de l’exemple de classeur de données Annotations modèle.


Enfin, vous devez enregistrer le classeur de modèles DataAnnotations dans le fichier Global.asax. Ajoutez la ligne de code suivante à l’Application\_Start() Gestionnaire d’événements pour que l’Application\_méthode Start() ressemble à ceci :

[!code-vb[Main](validation-with-the-data-annotation-validators-vb/samples/sample1.vb)]

Cette ligne de code enregistre le DataAnnotationsModelBinder en tant que le classeur de modèles par défaut pour l’ensemble de l’application ASP.NET MVC.

## <a name="using-the-data-annotation-validator-attributes"></a>En utilisant les attributs de validateur d’Annotation données

Lorsque vous utilisez le classeur de modèles des Annotations de données, vous utilisez les attributs de validateur pour effectuer la validation. L’espace de noms System.ComponentModel.DataAnnotations inclut les attributs du programme de validation suivants :

- Plage : vous permet de vérifier si la valeur d’une propriété se situe entre une plage de valeurs spécifiée.
- ReqularExpression – permet de valider si la valeur d’une propriété correspond à un modèle d’expression régulière spécifié.
- Requis : vous permet de marquer une propriété en fonction des besoins.
- StringLength – permet de spécifier une longueur maximale pour une propriété de chaîne.
- Validation – la classe de base pour tous les attributs de validateur.

> [!NOTE] 
> 
> Si vos besoins de validation ne sont pas satisfaites par les validateurs standards vous avez toujours la possibilité de créer un attribut de validateur personnalisé en héritant d’un nouvel attribut de validateur de l’attribut de Validation de base.


La classe de produit dans **liste 1** illustre l’utilisation de ces attributs de validateur. Les propriétés de nom, Description et prix unitaire sont marquées comme requis. La propriété Name doit avoir une longueur de chaîne qui est inférieur à 10 caractères. Enfin, la propriété UnitPrice doit correspondre à un modèle d’expression régulière qui représente un montant en devise.

[!code-vb[Main](validation-with-the-data-annotation-validators-vb/samples/sample2.vb)]

**La liste 1**: Models\Product.vb

La classe Product illustre comment utiliser un attribut supplémentaire : l’attribut DisplayName. L’attribut DisplayName vous permet de modifier le nom de la propriété lorsque la propriété est affichée dans un message d’erreur. Au lieu d’afficher le message d’erreur « le champ prix unitaire est requis », vous pouvez afficher le message d’erreur « le champ prix est requis ».

> [!NOTE] 
> 
> Si vous souhaitez personnaliser entièrement le message d’erreur affiché par un programme de validation vous pouvez affecter un message d’erreur personnalisé à la propriété ErrorMessage du validateur comme suit :`<Required(ErrorMessage:="This field needs a value!")>`


Vous pouvez utiliser la classe de produit dans **liste 1** avec l’action de contrôleur Create() dans **liste 2**. Cette action de contrôleur actualise la vue Create lorsque l’état du modèle contient des erreurs.

[!code-vb[Main](validation-with-the-data-annotation-validators-vb/samples/sample3.vb)]

**Listing 2**: Controllers\ProductController.vb

Enfin, vous pouvez créer la vue dans **liste 3** en cliquant sur l’action Create() et en sélectionnant l’option de menu **ajouter une vue**. Créer une vue fortement typée avec la classe de produit en tant que la classe de modèle. Sélectionnez **créer** à partir de la liste déroulante contenu afficher (voir **Figure 2**).

[![](validation-with-the-data-annotation-validators-vb/_static/image5.png)](validation-with-the-data-annotation-validators-vb/_static/image4.png)

**Figure 2**: ajout de la vue de créer

[!code-aspx[Main](validation-with-the-data-annotation-validators-vb/samples/sample4.aspx)]

**La liste 3**: Views\Product\Create.aspx

> [!NOTE] 
> 
> Supprimez le champ Id le formulaire de création généré par le **ajouter une vue** option de menu. Étant donné que le champ Id correspond à une colonne d’identité, vous ne souhaitez autoriser les utilisateurs à entrer une valeur pour ce champ.


Si vous envoyez le formulaire pour la création d’un produit et que vous n’entrez pas de valeurs pour les champs obligatoires, alors que l’erreur de validation des messages dans **Figure 3** sont affichés.

[![](validation-with-the-data-annotation-validators-vb/_static/image7.png)](validation-with-the-data-annotation-validators-vb/_static/image6.png)

**Figure 3**: les champs requis manquants

Si vous entrez un montant en devise non valide, puis le message d’erreur dans **Figure 4** s’affiche.

[![](validation-with-the-data-annotation-validators-vb/_static/image9.png)](validation-with-the-data-annotation-validators-vb/_static/image8.png)

**Figure 4**: montant en devise non valide

## <a name="using-data-annotation-validators-with-the-entity-framework"></a>À l’aide de validateurs d’Annotation de données avec Entity Framework

Si vous utilisez Microsoft Entity Framework pour générer vos classes de modèle de données vous ne pouvez pas appliquer les attributs de validateur directement à vos classes. Étant donné que le Concepteur d’Entity Framework génère les classes du modèle, les modifications apportées aux classes de modèle seront remplacées la prochaine fois que vous apportez des modifications dans le concepteur.

Si vous souhaitez utiliser les validateurs avec les classes générées par Entity Framework vous devez créer des classes de données de métadonnées. Vous appliquez les validateurs à la classe méta de données au lieu d’appliquer les validateurs à la classe réelle.

Par exemple, imaginez que vous avez créé une classe de film à l’aide d’Entity Framework (voir **Figure 5**). En outre, imaginez que vous souhaitez rendre le titre du film et le directeur de propriétés requis. Dans ce cas, vous pouvez créer la classe partielle et la classe méta de données dans **liste 4**.

[![](validation-with-the-data-annotation-validators-vb/_static/image11.png)](validation-with-the-data-annotation-validators-vb/_static/image10.png)

**Figure 5**: classe de film générée par Entity Framework

[!code-vb[Main](validation-with-the-data-annotation-validators-vb/samples/sample5.vb)]

**La liste 4**: Models\Movie.vb

Le fichier dans **liste 4** contient deux classes nommées film et MovieMetaData. La classe de film est une classe partielle. Il correspond à la classe partielle générée par Entity Framework qui est contenue dans le fichier DataModel.Designer.vb.

Actuellement, le .NET framework ne prend pas en charge les propriétés partielles. Par conséquent, il n’existe aucun moyen d’appliquer les attributs de validateur pour les propriétés de la classe de film définie dans le fichier DataModel.Designer.vb en appliquant les attributs de validateur pour les propriétés de la classe de film définie dans le fichier **liste 4**.

Notez que la classe partielle du film est décorée avec un attribut MetadataType qui pointe vers la classe MovieMetaData. La classe MovieMetaData contient les propriétés du proxy pour les propriétés de la classe de film.

Les attributs du programme de validation sont appliquées aux propriétés de la classe MovieMetaData. Les propriétés du titre, le directeur et DateReleased sont marquées en tant que propriétés requises. La propriété de directeur doit être affectée à une chaîne contenant moins de 5 caractères. Enfin, l’attribut DisplayName est appliquée à la propriété DateReleased pour afficher un message d’erreur tel que « le champ de Date finale est requis. » au lieu de l’erreur « le champ DateReleased est requis. »

> [!NOTE] 
> 
> Notez que les propriétés du proxy dans la classe MovieMetaData n’avez pas besoin représenter les mêmes types que les propriétés correspondantes dans la classe de film. Par exemple, la propriété directeur est une propriété de chaîne dans la classe de films et une propriété d’objet dans la classe MovieMetaData.


Dans la page de **Figure 6** illustre les messages d’erreur retournés lorsque vous entrez des valeurs non valides pour les propriétés de la vidéo.

[![](validation-with-the-data-annotation-validators-vb/_static/image13.png)](validation-with-the-data-annotation-validators-vb/_static/image12.png)

**Figure 6**: à l’aide de validateurs avec Entity Framework ([cliquez pour afficher l’image en taille réelle](validation-with-the-data-annotation-validators-vb/_static/image14.png))

## <a name="summary"></a>Résumé

Dans ce didacticiel, vous avez appris comment tirer parti de classeur de modèles de données d’Annotation pour effectuer la validation au sein d’une application ASP.NET MVC. Vous avez appris à utiliser les différents types d’attributs de validateur comme requis et les attributs de StringLength. Vous avez également appris à utiliser ces attributs lorsque vous travaillez avec Microsoft Entity Framework.

>[!div class="step-by-step"]
[Précédent](validating-with-a-service-layer-vb.md)
