---
uid: web-forms/overview/presenting-and-managing-data/model-binding/updating-deleting-and-creating-data
title: "Mise à jour, suppression et la création des données avec la liaison de modèle et les web forms | Documents Microsoft"
author: tfitzmac
description: "Cette série de didacticiels montre les aspects de base de l’utilisation de la liaison de modèle avec un projet Web Forms ASP.NET. Liaison de modèle permet une interaction de données plus droites-..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/27/2014
ms.topic: article
ms.assetid: 602baa94-5a4f-46eb-a717-7a9e539c1db4
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/updating-deleting-and-creating-data
msc.type: authoredcontent
ms.openlocfilehash: 18c065b44524e7738c048b5908fa50c592188064
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="updating-deleting-and-creating-data-with-model-binding-and-web-forms"></a>Mise à jour, suppression et la création des données avec la liaison de modèle et les web forms
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cette série de didacticiels montre les aspects de base de l’utilisation de la liaison de modèle avec un projet Web Forms ASP.NET. Liaison de modèle permet une interaction de données plus simple que vous traitez des données des objets de source (comme ObjectDataSource ou SqlDataSource). Cette série commence par la partie introductive et déplace vers des concepts plus avancés dans des didacticiels ultérieurs.
> 
> Ce didacticiel montre comment créer, mettre à jour et supprimer des données avec la liaison de modèle. Vous allez définir les propriétés suivantes :
> 
> - DeleteMethod
> - InsertMethod
> - UpdateMethod
> 
> Ces propriétés reçoivent le nom de la méthode qui gère l’opération correspondante. Dans cette méthode, vous fournissez la logique permettant d’interagir avec les données.
> 
> Ce didacticiel s’appuie sur le projet créé dans la première [partie](retrieving-data.md) de la série.
> 
> Vous pouvez [télécharger](https://go.microsoft.com/fwlink/?LinkId=286116) le projet complet en c# ou VB. Le code téléchargeable fonctionne avec Visual Studio 2012 ou Visual Studio 2013. Elle utilise le modèle Visual Studio 2012, qui est légèrement différent de celle du modèle de Visual Studio 2013 indiqué dans ce didacticiel.


## <a name="what-youll-build"></a>Vous allez générer

Dans ce didacticiel, vous effectuerez les tâches suivantes :

1. Ajouter des modèles de données dynamiques
2. Activer la mise à jour et suppression des données par le biais des méthodes de liaison de modèle
3. Appliquer des règles de validation de données - activer la création d’un nouvel enregistrement dans la base de données

## <a name="add-dynamic-data-templates"></a>Ajouter des modèles de données dynamiques

Pour offrir la meilleure expérience utilisateur et de réduire la redondance du code, vous allez utiliser les modèles de données dynamiques. Vous pouvez facilement intégrer des modèles prédéfinis dynamic data à votre site existant en installant un package NuGet.

À partir de la **gérer les Packages NuGet**, installez le **DynamicDataTemplatesCS**.

![modèles de données dynamiques](updating-deleting-and-creating-data/_static/image1.png)

Notez que votre projet inclut maintenant un dossier nommé **DynamicData**. Dans ce dossier, vous trouverez des modèles qui sont automatiquement appliqués aux contrôles dynamiques persistants dans vos formulaires web.

![dossier de données dynamiques](updating-deleting-and-creating-data/_static/image2.png)

## <a name="enable-updating-and-deleting"></a>Activer mise à jour et suppression

Permettre aux utilisateurs de mettre à jour et supprimer des enregistrements dans la base de données est très similaire au processus de récupération des données. Dans le **UpdateMethod** et **DeleteMethod** propriétés, vous spécifiez les noms des méthodes qui effectuent ces opérations. Avec un contrôle GridView, vous pouvez également spécifier la génération automatique de modifier et supprimer des boutons. Le code en surbrillance suivant montre les ajouts au code GridView.

[!code-aspx[Main](updating-deleting-and-creating-data/samples/sample1.aspx?highlight=4-5)]

Dans le fichier code-behind, ajoutez une à l’aide de l’instruction pour **System.Data.Entity.Infrastructure**.

[!code-csharp[Main](updating-deleting-and-creating-data/samples/sample2.cs)]

Ensuite, ajouter la mise à jour suivant et supprimer les méthodes.

[!code-csharp[Main](updating-deleting-and-creating-data/samples/sample3.cs)]

Le **TryUpdateModel** méthode s’applique les valeurs liées aux données correspondantes à partir du formulaire web à l’élément de données. L’élément de données est récupéré en fonction de la valeur du paramètre id.

## <a name="enforce-validation-requirements"></a>Appliquer des critères de validation

Les attributs de validation que vous avez appliqués aux propriétés dans la classe Student FirstName, LastName et année sont appliquées automatiquement lors de la mise à jour les données. Les contrôles DynamicField ajoutent des programmes de validation client et serveur basés sur les attributs de validation. Les propriétés FirstName et LastName sont tous deux requis. Prénom ne peut pas dépasser 20 caractères et LastName ne peut pas dépasser 40 caractères. Année doit être une valeur valide pour l’énumération AcademicYear.

Si l’utilisateur ne respecte pas une des conditions de validation, la mise à jour n’est pas effectuée. Pour voir le message d’erreur, ajoutez un contrôle ValidationSummary ci-dessus le GridView. Pour afficher les erreurs de validation à partir de la liaison de modèle, définissez la **ShowModelStateErrors** propriété **true**. 

[!code-aspx[Main](updating-deleting-and-creating-data/samples/sample4.aspx)]

Exécuter l’application web et mettre à jour et supprimer les enregistrements.

![Mettre à jour des données](updating-deleting-and-creating-data/_static/image3.png)

Notez que lorsque vous en mode d’édition, la valeur de la propriété de l’année est restituée automatiquement comme une zone de liste déroulante. La propriété de l’année est une valeur d’énumération, et le modèle de données dynamiques pour une valeur d’énumération spécifie une liste déroulante de modification. Vous pouvez trouver ce modèle en ouvrant le **énumération\_Data Edit.ascx** de fichiers dans le **DynamicData**/**FieldTemplates** dossier.

Si vous fournissez des valeurs valides, la mise à jour se termine correctement. Si vous ne respectez pas une des conditions de validation, la mise à jour s’arrête et un message d’erreur s’affiche au-dessus de la grille.

![Message d’erreur](updating-deleting-and-creating-data/_static/image4.png)

## <a name="add-new-records"></a>Ajout de nouveaux enregistrements

Le contrôle GridView n’inclut pas le **InsertMethod** propriété et ne peut donc pas être utilisé pour ajouter un nouvel enregistrement avec la liaison de modèle. Vous pouvez trouver la propriété InsertMethod dans le **FormView**, **DetailsView**, ou **ListView** contrôles. Dans ce didacticiel, vous utiliserez un contrôle FormView pour ajouter un nouvel enregistrement.

Tout d’abord, ajoutez un lien vers la nouvelle page, que vous allez créer pour ajouter un nouvel enregistrement. Au-dessus du contrôle ValidationSummary, ajoutez :

[!code-aspx[Main](updating-deleting-and-creating-data/samples/sample5.aspx)]

Le nouveau lien s’affiche en haut du contenu de la page d’étudiants.

![nouveau lien](updating-deleting-and-creating-data/_static/image5.png)

Ensuite, ajoutez un nouveau formulaire web à l’aide d’une page maître et nommez-le **AddStudent**. Sélectionnez la page maître Site.Master.

Vous rendrez les champs pour l’ajout d’un nouvel étudiant en utilisant un **DynamicEntity** contrôle. Le contrôle DynamicEntity restitue que propriétés modifiables de la classe spécifiée dans la propriété ItemType. La propriété StudentID a été marquée avec la **[ScaffoldColumn(false)]** d’attribut afin qu’il n’est pas rendu. Dans l’espace réservé MainContent de la page AddStudent, ajoutez le code suivant.

[!code-aspx[Main](updating-deleting-and-creating-data/samples/sample6.aspx)]

Dans le fichier code-behind (AddStudent.aspx.cs), ajoutez un **à l’aide de** instruction pour le **ContosoUniversityModelBinding.Models** espace de noms.

[!code-csharp[Main](updating-deleting-and-creating-data/samples/sample7.cs)]

Ensuite, ajoutez les méthodes suivantes pour spécifier comment insérer un nouvel enregistrement et un gestionnaire d’événements pour le bouton Annuler.

[!code-csharp[Main](updating-deleting-and-creating-data/samples/sample8.cs)]

Enregistrez toutes les modifications.

Exécuter l’application web et créer un nouvel étudiant.

![Ajouter Nouvel étudiant](updating-deleting-and-creating-data/_static/image6.png)

Cliquez sur **insérer** et notez le nouvel étudiant a été créé.

![afficher le nouvel étudiant](updating-deleting-and-creating-data/_static/image7.png)

## <a name="conclusion"></a>Conclusion

Dans ce didacticiel, vous activé la mise à jour, la suppression et la création de données. Vous assurer que les règles de validation sont appliquées lors de l’interaction avec les données.

Dans la prochaine [didacticiel](sorting-paging-and-filtering-data.md) dans cette série, vous allez activer le tri, la pagination et le filtrage des données.

>[!div class="step-by-step"]
[Précédent](retrieving-data.md)
[Suivant](sorting-paging-and-filtering-data.md)
