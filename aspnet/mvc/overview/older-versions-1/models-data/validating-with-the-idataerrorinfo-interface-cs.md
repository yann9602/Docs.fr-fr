---
uid: mvc/overview/older-versions-1/models-data/validating-with-the-idataerrorinfo-interface-cs
title: "Validation avec l’Interface IDataErrorInfo (c#) | Documents Microsoft"
author: StephenWalther
description: "Stephen Walther vous montre comment afficher des messages d’erreur de validation personnalisées en implémentant l’interface IDataErrorInfo dans une classe de modèle."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/02/2009
ms.topic: article
ms.assetid: 4733b9f1-9999-48fb-8b73-6038fbcc5ecb
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/models-data/validating-with-the-idataerrorinfo-interface-cs
msc.type: authoredcontent
ms.openlocfilehash: c04088c576481e4a91676d7e6962c03b56e7a8a4
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="validating-with-the-idataerrorinfo-interface-c"></a>Validation avec l’Interface IDataErrorInfo (c#)
====================
par [Stephen Walther](https://github.com/StephenWalther)

> Stephen Walther vous montre comment afficher des messages d’erreur de validation personnalisées en implémentant l’interface IDataErrorInfo dans une classe de modèle.


L’objectif de ce didacticiel est d’expliquer une méthode pour effectuer la validation dans une application ASP.NET MVC. Vous apprenez à empêcher une personne de l’envoi d’un formulaire HTML sans fournir de valeurs pour les champs obligatoires. Dans ce didacticiel, vous allez apprendre à effectuer la validation à l’aide de l’interface IErrorDataInfo.

## <a name="assumptions"></a>Assumptions (Hypothèses)

Dans ce didacticiel, je vais utiliser la base de données MoviesDB et la table de base de données de films. Cette table comporte les colonnes suivantes :

<a id="0.5_table01"></a>


| **Nom de la colonne** | **Type de données** | **Autoriser les valeurs null** |
| --- | --- | --- |
| Id | Int | False |
| Titre | Nvarchar (100) | False |
| Directeur | Nvarchar (100) | False |
| DateReleased | DateTime | False |


Dans ce didacticiel, utiliser Microsoft Entity Framework pour générer des classes de mon modèle de base de données. La classe de vidéo générée par Entity Framework est affichée dans la Figure 1.


[![L’entité de film](validating-with-the-idataerrorinfo-interface-cs/_static/image1.jpg)](validating-with-the-idataerrorinfo-interface-cs/_static/image1.png)

**Figure 01**: entité de la séquence ([cliquez pour afficher l’image en taille réelle](validating-with-the-idataerrorinfo-interface-cs/_static/image2.png))


> [!NOTE] 
> 
> Pour en savoir plus sur l’utilisation d’Entity Framework pour générer vos classes de modèle de base de données, consultez que mon didacticiel le droit de créer des Classes de modèle avec Entity Framework.


## <a name="the-controller-class"></a>La classe de contrôleur

Nous utilisent le contrôleur Home cinéma de liste et créer de nouveaux films. Le code de cette classe est contenu dans la liste 1.

**La liste 1 - Controllers\HomeController.cs**

[!code-csharp[Main](validating-with-the-idataerrorinfo-interface-cs/samples/sample1.cs)]

La classe de contrôleur d’accueil dans la liste 1 contient deux actions Create(). La première action affiche le formulaire HTML pour la création d’un film de nouveau. La seconde action Create() effectue l’insertion de la nouvelle séquence dans la base de données. La seconde action Create() est appelée lorsque le formulaire affiché par la première action Create() est envoyé au serveur.

Notez que la seconde action Create() contient les lignes de code suivantes :

[!code-csharp[Main](validating-with-the-idataerrorinfo-interface-cs/samples/sample2.cs)]

La propriété IsValid retourne la valeur false lorsqu’il existe une erreur de validation. Dans ce cas, la créer une vue qui contient le formulaire HTML pour la création d’un film s’affiche de nouveau.

## <a name="creating-a-partial-class"></a>Création d’une classe partielle

La classe de film est générée par Entity Framework. Vous pouvez voir le code de la classe de film si vous développez le fichier MoviesDBModel.edmx dans la fenêtre de l’Explorateur de solutions et ouvrez le fichier MoviesDBModel.Designer.cs dans l’éditeur de Code (voir Figure 2).


[![Le code de l’entité de film](validating-with-the-idataerrorinfo-interface-cs/_static/image2.jpg)](validating-with-the-idataerrorinfo-interface-cs/_static/image3.png)

**Figure 02**: le code de l’entité de film ([cliquez pour afficher l’image en taille réelle](validating-with-the-idataerrorinfo-interface-cs/_static/image4.png))


La classe de film est une classe partielle. Cela signifie que nous pouvons ajouter une autre classe partielle portant le même nom pour étendre les fonctionnalités de la classe de film. Nous allons ajouter notre logique de validation à la nouvelle classe partielle.

Ajoutez la classe dans la liste 2 dans le dossier de modèles.

**Listing 2 - Models\Movie.cs**

[!code-csharp[Main](validating-with-the-idataerrorinfo-interface-cs/samples/sample3.cs)]

Notez que la classe dans la liste 2 inclut les *partielle* modificateur. Les méthodes ou les propriétés que vous ajoutez à cette classe deviennent partie de la classe de vidéo générée par Entity Framework.

## <a name="adding-onchanging-and-onchanged-partial-methods"></a>Ajout de OnChanging et méthodes de OnChanged partielle

Lorsque l’Entity Framework génère une classe d’entité, Entity Framework ajoute automatiquement les méthodes partielles à la classe. Entity Framework génère des méthodes partielles OnChanging et OnChanged qui correspondent à chaque propriété de la classe.

Dans le cas de la classe de film, Entity Framework crée des méthodes suivantes :

- OnIdChanging
- OnIdChanged
- OnTitleChanging
- OnTitleChanged
- OnDirectorChanging
- OnDirectorChanged
- OnDateReleasedChanging
- OnDateReleasedChanged

La méthode OnChanging est appelée droite avant la modification de la propriété correspondante. La méthode OnChanged est appelée droit une fois que la propriété est modifiée.

Vous pouvez tirer parti de ces méthodes partielles pour ajouter la logique de validation à la classe de film. La mise à jour de classe de film dans le Listing 3 vérifie que les propriétés Title et directeur sont affectées des valeurs non vides.

> [!NOTE] 
> 
> Une méthode partielle est une méthode définie dans une classe que vous n’êtes pas obligé d’implémenter. Si vous n’implémentez pas une méthode partielle ensuite le compilateur supprime la signature de méthode et tous les appels à la méthode, par conséquent, il n’existe aucun coût d’exécution associé à la méthode partielle. Dans l’éditeur de Code Visual Studio, vous pouvez ajouter une méthode partielle en tapant le mot clé *partielle* suivi d’un espace pour afficher une liste d’aucun à implémenter.


**La liste 3 - Models\Movie.cs**

[!code-csharp[Main](validating-with-the-idataerrorinfo-interface-cs/samples/sample4.cs)]

Par exemple, si vous essayez d’affecter une chaîne vide à la propriété de titre, un message d’erreur est attribué à un dictionnaire nommé \_erreurs.

À ce stade, rien ne réellement se produit lorsque vous assignez une chaîne vide à la propriété de titre et une erreur est ajoutée à privé \_champ d’erreurs. Nous devons implémenter l’interface IDataErrorInfo pour exposer ces erreurs de validation de l’infrastructure ASP.NET MVC.

## <a name="implementing-the-idataerrorinfo-interface"></a>Implémentation de l’Interface IDataErrorInfo

L’interface IDataErrorInfo a fait partie du .NET framework depuis la première version. Cette interface est une interface très simple :

[!code-csharp[Main](validating-with-the-idataerrorinfo-interface-cs/samples/sample5.cs)]

Si une classe implémente l’interface IDataErrorInfo, l’infrastructure ASP.NET MVC utilise cette interface lors de la création d’une instance de la classe. Par exemple, le contrôleur Home Create() action accepte une instance de la classe de film :

[!code-csharp[Main](validating-with-the-idataerrorinfo-interface-cs/samples/sample6.cs)]

L’infrastructure ASP.NET MVC crée l’instance de la séquence passée à l’action Create() à l’aide d’un classeur de modèles (la DefaultModelBinder). Le classeur de modèles est responsable de la création d’une instance de l’objet séquence par les champs de formulaire HTML de la liaison à une instance de l’objet.

Le DefaultModelBinder de détecter si une classe implémente l’interface IDataErrorInfo. Si une classe implémente cette interface le classeur de modèles appelle l’indexeur IDataErrorInfo.this pour chaque propriété de la classe. Si l’indexeur retourne un message d’erreur le classeur de modèles ajoute ce message d’erreur à l’état de modèle automatiquement.

Le DefaultModelBinder vérifie également la propriété IDataErrorInfo.Error. Cette propriété est conçue pour représenter les erreurs de validation spécifique de propriétés de non associés à la classe. Par exemple, vous pouvez souhaiter appliquer une règle de validation qui dépend des valeurs de plusieurs propriétés de la classe de film. Dans ce cas, vous retournerait une erreur de validation de la propriété de l’erreur.

La classe de film mis à jour dans la liste 4 implémente l’interface IDataErrorInfo.

**La liste 4 - Models\Movie.cs (implémente IDataErrorInfo)**

[!code-csharp[Main](validating-with-the-idataerrorinfo-interface-cs/samples/sample7.cs)]

Dans la liste 4, la propriété d’indexeur vérifie le \_collection d’erreurs pour voir s’il contient une clé qui correspond au nom de propriété passé à l’indexeur. Si aucune erreur de validation associé à la propriété n’est une chaîne vide est retournée.

Vous n’avez pas besoin de modifier le contrôleur Home de quelque manière d’utiliser la classe de film modifiée. La page affichée dans la Figure 3 illustre que se passe-t-il quand aucune valeur n’est entrée pour les champs de formulaire titre ou directeur.


[![Création automatique de méthodes d’action](validating-with-the-idataerrorinfo-interface-cs/_static/image3.jpg)](validating-with-the-idataerrorinfo-interface-cs/_static/image5.png)

**Figure 03**: un formulaire avec des valeurs manquantes ([cliquez pour afficher l’image en taille réelle](validating-with-the-idataerrorinfo-interface-cs/_static/image6.png))


Notez que la valeur DateReleased est automatiquement validée. Étant donné que la propriété DateReleased n’accepte pas les valeurs NULL, la DefaultModelBinder génère automatiquement une erreur de validation pour cette propriété lorsqu’il n’a pas de valeur. Si vous souhaitez modifier le message d’erreur pour la propriété DateReleased, vous devez créer un classeur de modèles personnalisés.

## <a name="summary"></a>Résumé

Dans ce didacticiel, vous avez appris à utiliser l’interface IDataErrorInfo pour générer des messages d’erreur de validation. Tout d’abord, nous avons créé une classe partielle de film qui étend les fonctionnalités de la classe partielle de vidéo générée par Entity Framework. Ensuite, nous avons ajouté la logique de validation pour les films classe OnTitleChanging() et OnDirectorChanging() méthodes partielles. Enfin, nous avons implémenté l’interface IDataErrorInfo afin d’exposer ces messages de validation de l’infrastructure ASP.NET MVC.

>[!div class="step-by-step"]
[Précédent](performing-simple-validation-cs.md)
[Suivant](validating-with-a-service-layer-cs.md)
