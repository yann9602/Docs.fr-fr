---
uid: web-forms/overview/presenting-and-managing-data/model-binding/sorting-paging-and-filtering-data
title: "Le tri, la pagination et le filtrage des données avec la liaison de modèle et les web forms | Documents Microsoft"
author: tfitzmac
description: "Cette série de didacticiels montre les aspects de base de l’utilisation de la liaison de modèle avec un projet Web Forms ASP.NET. Liaison de modèle permet une interaction de données plus droites-..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/27/2014
ms.topic: article
ms.assetid: 266e7866-e327-4687-b29d-627a0925e87d
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/sorting-paging-and-filtering-data
msc.type: authoredcontent
ms.openlocfilehash: 94fc84533be5fcbcf0612fcdcabea7dee738d89b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="sorting-paging-and-filtering-data-with-model-binding-and-web-forms"></a>Le tri, la pagination et le filtrage des données avec la liaison de modèle et les web forms
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cette série de didacticiels montre les aspects de base de l’utilisation de la liaison de modèle avec un projet Web Forms ASP.NET. Liaison de modèle permet une interaction de données plus simple que vous traitez des données des objets de source (comme ObjectDataSource ou SqlDataSource). Cette série commence par la partie introductive et déplace vers des concepts plus avancés dans des didacticiels ultérieurs.
> 
> Ce didacticiel montre comment ajouter le tri, la pagination et le filtrage des données via la liaison de modèle.
> 
> Ce didacticiel s’appuie sur le projet créé dans la première [partie](retrieving-data.md) de la série.
> 
> Vous pouvez [télécharger](https://go.microsoft.com/fwlink/?LinkId=286116) le projet complet en c# ou VB. Le code téléchargeable fonctionne avec Visual Studio 2012 ou Visual Studio 2013. Elle utilise le modèle Visual Studio 2012, qui est légèrement différent de celle du modèle de Visual Studio 2013 indiqué dans ce didacticiel.


## <a name="what-youll-build"></a>Vous allez générer

Dans ce didacticiel, vous effectuerez les tâches suivantes :

1. Activer le tri et la pagination des données
2. Activer le filtrage des données selon une sélection par l’utilisateur

## <a name="add-sorting"></a>Ajouter un tri

Il est très facile d’activation du tri dans le GridView. Dans le fichier Student.aspx, définissez simplement **AllowSorting** à **true** dans le GridView. Vous n’avez pas besoin de définir un **SortExpression** valeur pour chaque colonne, comme le DataField est automatiquement utilisé. Le contrôle GridView modifie la requête pour inclure le tri des données par la valeur sélectionnée. Le code en surbrillance ci-dessous illustre l’ajout de que vous devez faire activer le tri.

[!code-aspx[Main](sorting-paging-and-filtering-data/samples/sample1.aspx?highlight=5)]

Exécuter l’application web et de tester des étudiants tri en fonction des valeurs dans des colonnes différentes.

![étudiants de tri](sorting-paging-and-filtering-data/_static/image2.png)

## <a name="add-paging"></a>Ajouter la pagination

Il est également très facile de l’activation de la pagination. Dans le GridView, définissez la **AllowPaging** propriété **true** et définir le **PageSize** propriété pour le nombre d’enregistrements que vous souhaitez afficher sur chaque page. Dans ce didacticiel, vous pouvez la définir à 4.

[!code-aspx[Main](sorting-paging-and-filtering-data/samples/sample2.aspx?highlight=5)]

Exécuter l’application web et notez que maintenant les enregistrements sont réparties sur plusieurs pages avec pas plus de 4 enregistrements affichés sur une seule page.

![ajouter la pagination](sorting-paging-and-filtering-data/_static/image4.png)

Exécution de requête différée améliore l’efficacité de l’application. Au lieu de récupérer l’ensemble de données, le contrôle GridView modifie la requête pour récupérer uniquement les enregistrements de la page actuelle.

## <a name="filter-records-by-user-selection"></a>Filtrer les enregistrements par sélection de l’utilisateur

Liaison de modèle ajoute plusieurs attributs qui vous permettent de désigner la définition de la valeur d’un paramètre dans une méthode de liaison de modèle. Ces attributs sont dans le **System.Web.ModelBinding** espace de noms. Elles comprennent :

- Contrôle
- Cookie
- Formulaire
- Profil
- QueryString
- RouteData
- Session
- Profil utilisateur
- État d’affichage

Dans ce didacticiel, vous utiliserez une valeur d’un contrôle pour filtrer les enregistrements sont affichés dans le GridView. Vous allez ajouter le **contrôle** d’attribut à la méthode de requête que vous avez créé précédemment. Dans un [ultérieurement](using-query-string-values-to-retrieve-data.md) didacticiel, vous allez appliquer la **QueryString** d’attribut à un paramètre pour spécifier que la valeur du paramètre provient d’une valeur de chaîne de requête.

Au-dessus du contrôle ValidationSummary, ajoutez d’abord une liste déroulante de filtrage les étudiants sont affichés.

[!code-aspx[Main](sorting-paging-and-filtering-data/samples/sample3.aspx?highlight=3-11)]

Dans le fichier code-behind, modifier la méthode select pour recevoir une valeur à partir du contrôle et le nom du contrôle qui fournit la valeur de la valeur du nom du paramètre.

Vous devez ajouter un **à l’aide de** instruction pour le **System.Web.ModelBinding** résoudre l’attribut de contrôle de l’espace de noms.

[!code-csharp[Main](sorting-paging-and-filtering-data/samples/sample4.cs)]

Le code suivant montre la méthode select nouveau travaillée pour filtrer les données retournées en fonction de la valeur de la zone de liste déroulante. Ajout d’un attribut de contrôle avant un paramètre spécifie que la valeur de ce paramètre est fourni à partir d’un contrôle portant le même nom.

[!code-csharp[Main](sorting-paging-and-filtering-data/samples/sample5.cs)]

Exécuter l’application web et sélectionner des valeurs différentes à partir de la liste déroulante pour filtrer la liste d’étudiants.

![étudiants de filtre](sorting-paging-and-filtering-data/_static/image6.png)

## <a name="conclusion"></a>Conclusion

Dans ce didacticiel, vous activé le tri et la pagination des données. Vous est également activé le filtrage des données par la valeur d’un contrôle.

Dans la prochaine [didacticiel](integrating-jquery-ui.md) vous allez améliorer l’interface utilisateur en intégrant un widget de JQuery UI dans le modèle de données dynamiques.

>[!div class="step-by-step"]
[Précédent](updating-deleting-and-creating-data.md)
[Suivant](integrating-jquery-ui.md)
