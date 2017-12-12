---
uid: web-forms/overview/presenting-and-managing-data/model-binding/adding-business-logic-layer
title: "Couche de logique métier ajout à un projet qui utilise la liaison de modèle et les web forms | Documents Microsoft"
author: tfitzmac
description: "Cette série de didacticiels montre les aspects de base de l’utilisation de la liaison de modèle avec un projet Web Forms ASP.NET. Liaison de modèle permet une interaction de données plus droites-..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/27/2014
ms.topic: article
ms.assetid: 7ef664b3-1cc8-4cbf-bb18-9f0f3a3ada2b
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/adding-business-logic-layer
msc.type: authoredcontent
ms.openlocfilehash: ca50690052cca73a718342a9725c8096a72f1187
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="adding-business-logic-layer-to-a-project-that-uses-model-binding-and-web-forms"></a>Couche de logique métier ajout à un projet qui utilise la liaison de modèle et les web forms
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cette série de didacticiels montre les aspects de base de l’utilisation de la liaison de modèle avec un projet Web Forms ASP.NET. Liaison de modèle permet une interaction de données plus simple que vous traitez des données des objets de source (comme ObjectDataSource ou SqlDataSource). Cette série commence par la partie introductive et déplace vers des concepts plus avancés dans des didacticiels ultérieurs.
> 
> Ce didacticiel montre comment utiliser la liaison de modèle avec une couche de logique métier. Vous allez définir le membre OnCallingDataMethods pour spécifier qu’un objet autre que la page actuelle est utilisé pour appeler les méthodes de données.
> 
> Ce didacticiel s’appuie sur le projet créé dans le [antérieures](retrieving-data.md) parties de la série.
> 
> Vous pouvez [télécharger](https://go.microsoft.com/fwlink/?LinkId=286116) le projet complet en c# ou VB. Le code téléchargeable fonctionne avec Visual Studio 2012 ou Visual Studio 2013. Elle utilise le modèle Visual Studio 2012, qui est légèrement différent de celle du modèle de Visual Studio 2013 indiqué dans ce didacticiel.


## <a name="what-youll-build"></a>Vous allez générer

Liaison de modèle vous permet de placer votre code d’interaction de données dans le fichier code-behind d’une page web ou dans une classe de logique métier distincts. Les didacticiels précédents ont montré comment utiliser les fichiers code-behind pour le code d’interaction de données. Cette approche fonctionne pour les sites de petite taille, mais il peut se produire du code répétition et difficulté supérieure lors de la maintenance d’un site de grande taille. Il peut également être très difficile à tester par programmation du code qui se trouve dans les fichiers code-behind, car il n’existe aucune couche d’abstraction.

Pour centraliser le code d’interaction de données, vous pouvez créer une couche de logique métier qui contient l’ensemble de la logique permettant d’interagir avec des données. Appelez ensuite la couche de logique métier à partir de vos pages web. Ce didacticiel montre comment déplacer tout le code que vous avez écrit dans les didacticiels précédents dans une couche de logique métier et ensuite utiliser ce code à partir des pages.

Dans ce didacticiel, vous effectuerez les tâches suivantes :

1. Déplacer le code à partir des fichiers de code-behind pour une couche de logique métier
2. Modifier vos contrôles liés aux données pour appeler les méthodes dans la couche de logique métier

## <a name="create-business-logic-layer"></a>Créer une couche de logique métier

Maintenant, vous allez créer la classe qui est appelée à partir des pages web. Les méthodes de cette classe similaire aux méthodes que vous avez utilisée dans les didacticiels précédents et incluent les attributs de fournisseur de valeur.

Tout d’abord, ajoutez un nouveau dossier appelé **BLL**.

![Ajouter un dossier](adding-business-logic-layer/_static/image1.png)

Dans le dossier de la couche BLL, créez une nouvelle classe nommée **SchoolBL.cs**. Il contient toutes les opérations de données permettant d’accéder dans les fichiers code-behind. Les méthodes sont presque les mêmes que les méthodes dans le fichier code-behind, mais incluent des modifications.

Le changement le plus important de noter est que vous exécutez n’est plus le code à partir d’une instance de **Page** classe. La classe de Page contient le **TryUpdateModel** (méthode) et le **ModelState** propriété. Lorsque ce code est déplacé vers une couche de logique métier, vous n’avez plus une instance de la classe de Page pour appeler ces membres. Pour contourner ce problème, vous devez ajouter un **ModelMethodContext** paramètre à une méthode qui accède à TryUpdateModel ou ModelState. Ce paramètre ModelMethodContext vous permet d’appeler TryUpdateModel ou récupérer ModelState. Vous n’avez pas besoin modifier n’importe où dans la page web pour prendre en compte ce nouveau paramètre.

Remplacez le code dans SchoolBL.cs par le code suivant.

[!code-csharp[Main](adding-business-logic-layer/samples/sample1.cs)]

## <a name="revise-existing-pages-to-retrieve-data-from-business-logic-layer"></a>Passez en revue les pages existantes pour récupérer des données à partir de la couche de logique métier

Enfin, vous allez convertir les pages Students.aspx, AddStudent.aspx et Courses.aspx à partir de l’utilisation des requêtes dans le fichier code-behind à l’aide de la couche de logique métier.

Dans les fichiers code-behind pour les étudiants, AddStudent et Courses, supprimez ou commentez les méthodes de requête suivantes :

- studentsGrid\_GetData
- studentsGrid\_UpdateItem
- studentsGrid\_DeleteItem
- addStudentForm\_InsertItem
- coursesGrid\_GetData

Vous ne devez maintenant disposer d’aucun code dans le fichier code-behind relative aux opérations de données.

Le **OnCallingDataMethods** Gestionnaire d’événements vous permet de spécifier un objet à utiliser pour les méthodes de données. Dans Students.aspx, ajoutez une valeur pour ce gestionnaire d’événements et modifier les noms des méthodes de données pour les noms des méthodes dans la classe de logique métier.

[!code-aspx[Main](adding-business-logic-layer/samples/sample2.aspx?highlight=3-4,8)]

Dans le fichier code-behind pour Students.aspx, définissez le Gestionnaire d’événements pour l’événement CallingDataMethods. Dans ce gestionnaire d’événements, vous spécifiez la classe de logique métier pour les opérations de données.

[!code-csharp[Main](adding-business-logic-layer/samples/sample3.cs)]

Dans AddStudent.aspx, apporter des modifications similaires.

[!code-aspx[Main](adding-business-logic-layer/samples/sample4.aspx?highlight=3-4)]

[!code-csharp[Main](adding-business-logic-layer/samples/sample5.cs)]

Dans Courses.aspx, apporter des modifications similaires.

[!code-aspx[Main](adding-business-logic-layer/samples/sample6.aspx?highlight=3-4)]

[!code-csharp[Main](adding-business-logic-layer/samples/sample7.cs)]

Exécutez l’application et remarquez que toutes les pages de fonction comme c’était précédemment. Également, la logique de validation fonctionne correctement.

## <a name="conclusion"></a>Conclusion

Dans ce didacticiel, vous ré-structurée de votre application d’utiliser une couche d’accès aux données et la couche de logique métier. Vous avez spécifié que les contrôles de données utilisent un objet qui n’est pas la page actuelle pour les opérations de données.

>[!div class="step-by-step"]
[Précédent](using-query-string-values-to-retrieve-data.md)
