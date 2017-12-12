---
uid: web-forms/overview/presenting-and-managing-data/model-binding/using-query-string-values-to-retrieve-data
title: "À l’aide des valeurs de chaîne de requête pour filtrer les données avec la liaison de modèle et les web forms | Documents Microsoft"
author: tfitzmac
description: "Cette série de didacticiels montre les aspects de base de l’utilisation de la liaison de modèle avec un projet Web Forms ASP.NET. Liaison de modèle permet une interaction de données plus droites-..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/27/2014
ms.topic: article
ms.assetid: b90978bd-795d-4871-9ade-1671caff5730
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/using-query-string-values-to-retrieve-data
msc.type: authoredcontent
ms.openlocfilehash: 2e5328ccda019462163b984da3661f7322b738df
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-query-string-values-to-filter-data-with-model-binding-and-web-forms"></a>À l’aide des valeurs de chaîne de requête pour filtrer les données avec la liaison de modèle et les web forms
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cette série de didacticiels montre les aspects de base de l’utilisation de la liaison de modèle avec un projet Web Forms ASP.NET. Liaison de modèle permet une interaction de données plus simple que vous traitez des données des objets de source (comme ObjectDataSource ou SqlDataSource). Cette série commence par la partie introductive et déplace vers des concepts plus avancés dans des didacticiels ultérieurs.
> 
> Ce didacticiel montre comment passer une valeur dans la chaîne de requête et utilisez cette valeur pour récupérer des données via la liaison de modèle.
> 
> Ce didacticiel s’appuie sur le projet créé dans le [antérieures](retrieving-data.md) parties de la série.
> 
> Vous pouvez [télécharger](https://go.microsoft.com/fwlink/?LinkId=286116) le projet complet en c# ou VB. Le code téléchargeable fonctionne avec Visual Studio 2012 ou Visual Studio 2013. Elle utilise le modèle Visual Studio 2012, qui est légèrement différent de celle du modèle de Visual Studio 2013 indiqué dans ce didacticiel.


## <a name="what-youll-build"></a>Vous allez générer

Dans ce didacticiel, vous effectuerez les tâches suivantes :

1. Ajouter une nouvelle page pour afficher les cours inscrits pour un étudiant
2. Récupérer les cours inscrits pour l’étudiant sélectionné selon une valeur dans la chaîne de requête
3. Ajouter un lien hypertexte avec une valeur de chaîne de requête à partir de l’affichage de grille vers la nouvelle page

Les étapes décrites dans ce didacticiel sont relativement similaires à ce que vous avez fait dans la plus antérieure [didacticiel](sorting-paging-and-filtering-data.md) pour filtrer les étudiants affichées en fonction de la sélection de l’utilisateur dans une zone de liste déroulante. Dans ce didacticiel, vous avez utilisé le **contrôle** attribut dans la méthode select pour spécifier que la valeur du paramètre provient d’un contrôle. Dans ce didacticiel, vous allez utiliser le **QueryString** attribut dans la méthode select pour spécifier que la valeur du paramètre provient de la chaîne de requête.

## <a name="add-new-page-for-displaying-a-students-courses"></a>Ajouter une nouvelle page pour afficher le cours de l’un étudiant

Ajoutez un nouveau formulaire web qui utilise la page maître Site.master et nommez la page **cours**.

Dans le **Courses.aspx** , ajoutez un affichage de grille pour afficher les cours pour les étudiants sélectionné.

[!code-aspx[Main](using-query-string-values-to-retrieve-data/samples/sample1.aspx)]

## <a name="define-the-select-method"></a>Définir la méthode select

Dans **Courses.aspx.cs**, vous allez ajouter la méthode select avec le nom spécifié dans l’affichage de grille **SelectMethod** propriété. Dans cette méthode, vous allez définir la requête pour récupérer le cours de l’un étudiant et spécifier que le paramètre provient d’une valeur de chaîne de requête avec le même nom que le paramètre.

Vous devez tout d’abord, ajoutez le code suivant **à l’aide de** instructions.

[!code-csharp[Main](using-query-string-values-to-retrieve-data/samples/sample2.cs)]

Ensuite, ajoutez le code suivant à Courses.aspx.cs :

[!code-csharp[Main](using-query-string-values-to-retrieve-data/samples/sample3.cs)]

L’attribut de chaîne de requête signifie qu’une valeur de chaîne de requête nommée StudentID est automatiquement affectée au paramètre de cette méthode.

## <a name="add-hyperlink-with-query-string-value"></a>Ajouter un lien hypertexte avec la valeur de chaîne de requête

Dans l’affichage de grille sur Students.aspx, vous allez ajouter un champ de lien hypertexte qui établit un lien vers votre nouvelle page de cours. Le lien hypertexte inclut une valeur de chaîne de requête avec l’id de l’étudiant.

Dans Students.aspx, ajoutez le champ suivant pour les colonnes de la vue grille juste en dessous du champ de Total des crédits.

[!code-aspx[Main](using-query-string-values-to-retrieve-data/samples/sample4.aspx?highlight=7-8)]

Exécutez l’application et remarquez que l’affichage de grille inclut désormais le lien du cours.

![Ajouter un lien hypertexte](using-query-string-values-to-retrieve-data/_static/image1.png)

Lorsque vous cliquez sur un des liens, vous verrez les cours inscrits que student.

![afficher en cours](using-query-string-values-to-retrieve-data/_static/image2.png)

## <a name="conclusion"></a>Conclusion

Dans ce didacticiel, vous avez ajouté un lien avec une valeur de chaîne de requête. Vous avez utilisé cette valeur de chaîne de requête pour la valeur du paramètre dans la méthode select.

Dans la prochaine [didacticiel](adding-business-logic-layer.md), vous allez déplacer le code à partir des fichiers code-behind dans une couche de logique métier et une couche d’accès aux données.

>[!div class="step-by-step"]
[Précédent](integrating-jquery-ui.md)
[Suivant](adding-business-logic-layer.md)
