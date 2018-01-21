---
title: "Liaison de modèle personnalisé"
author: ardalis
description: "Personnalisation de la liaison de modèle dans ASP.NET MVC de base."
ms.author: riande
manager: wpickett
ms.date: 04/10/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: d8b94f53954c5ab63ccf3aab4eb7a7a7dbea487b
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="custom-model-binding"></a>Liaison de modèle personnalisé

Par [Steve Smith](https://ardalis.com/)

Liaison de modèle autorise les actions de contrôleur travailler directement avec les types de modèle (transmis en tant qu’arguments de méthode), plutôt que les requêtes HTTP. Mappage entre les modèles de données et votre application demande entrantes est gérée par les classeurs de modèles. Les développeurs peuvent étendre les fonctionnalités de liaison de modèle intégrée en implémentant des classeurs de modèles personnalisés (même si en règle générale, vous n’avez pas besoin d’écrire votre propre fournisseur).

[Afficher ou télécharger l’exemple à partir de GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/)

## <a name="default-model-binder-limitations"></a>Limitations de classeur de modèle par défaut

Les classeurs de modèles par défaut prennent en charge la plupart des types de données .NET Core courants et doivent répondre aux besoins de la plupart des développeurs. Ils s’attendre à lier des données texte à partir de la demande directement à des types de modèle. Vous devrez peut-être transformer l’entrée avant la liaison. Par exemple, si vous avez une clé qui peut être utilisée pour rechercher des données de modèle. Vous pouvez utiliser un classeur de modèles personnalisés pour extraire des données en fonction de la clé.

## <a name="model-binding-review"></a>Vérification de liaison de modèle

Liaison de modèle utilise des définitions spécifiques pour les types qu’il opère sur. A *type simple* est convertie à partir d’une chaîne unique dans l’entrée. A *type complexe* est convertie à partir de plusieurs valeurs d’entrée. L’infrastructure détermine la différence en fonction de l’existence d’un `TypeConverter`. Il est recommandé que vous créez un convertisseur de type, si vous disposez d’un simple `string`  ->  `SomeType` mappage ne nécessitant pas de ressources externes.

Avant de créer votre propre classeur de modèles personnalisés, il est important de consulter des modèles existants classeurs sont implémentées. Prendre en compte la [ByteArrayModelBinder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder) qui peut être utilisé pour convertir des chaînes codées en base64 en tableaux d’octets. Les tableaux d’octets sont souvent stockées en tant que fichiers ou des champs de base de données BLOB.

### <a name="working-with-the-bytearraymodelbinder"></a>Utilisation de la ByteArrayModelBinder

Chaînes encodées en Base64 peuvent être utilisés pour représenter les données binaires. Par exemple, l’image suivante peut être encodé sous forme de chaîne.

![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")

Une petite partie de la chaîne encodée est indiquée dans l’image suivante :

![bot dotnet codé](custom-model-binding/images/encoded-bot.png "bot dotnet codée")

Suivez les instructions de la [fichier Lisezmoi de l’exemple](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) pour convertir la chaîne codée en base 64 dans un fichier.

ASP.NET MVC de base peut prendre une chaînes codées en base64 et utiliser un `ByteArrayModelBinder` pour le convertir en un tableau d’octets. Le [ByteArrayModelBinderProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) qui implémente [IModelBinderProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) mappe `byte[]` arguments à `ByteArrayModelBinder`:

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

Lorsque vous créez votre propre classeur de modèles personnalisés, vous pouvez implémenter votre propre `IModelBinderProvider` tapez, ou utilisez le [ModelBinderAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinderattribute).

L’exemple suivant montre comment utiliser `ByteArrayModelBinder` pour convertir une chaîne codée en base 64 pour un `byte[]` et enregistrer le résultat dans un fichier :

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post1&highlight=3)]

Vous pouvez valider une chaîne codée en base64 à cette méthode d’api à l’aide d’un outil tel que [Postman](https://www.getpostman.com/):

![postman](custom-model-binding/images/postman.png "postman")

Tant que le classeur peut lier des données de demande aux propriétés correctement nommées ou d’arguments, la liaison de modèle réussit. L’exemple suivant montre comment utiliser `ByteArrayModelBinder` avec un modèle d’affichage :

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a>Exemple de classeur de modèle personnalisé

Dans cette section, nous allons implémenter un classeur de modèles personnalisés qui :

- Convertit les données de demandes entrantes en arguments clés fortement typées.
- Utilise Entity Framework Core pour extraire de l’entité associée.
- Passe l’entité associée en tant qu’argument à la méthode d’action.

L’exemple suivant utilise le `ModelBinder` de l’attribut le `Author` modèle :

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Data/Author.cs?highlight=10)]

Dans le code précédent, le `ModelBinder` attribut spécifie le type de `IModelBinder` qui doit être utilisé pour lier `Author` paramètres d’action. 

Le `AuthorEntityBinder` est utilisée pour lier une `Author` paramètre par extraction de l’entité à partir d’une source de données à l’aide d’Entity Framework Core et une `authorId`:

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

Le code suivant montre comment utiliser le `AuthorEntityBinder` dans une méthode d’action :

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

Le `ModelBinder` attribut peut être utilisé pour appliquer la `AuthorEntityBinder` aux paramètres qui n’utilisent pas les conventions utilisées par défaut :

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

Dans cet exemple, étant donné que le nom de l’argument n’est pas la valeur par défaut `authorId`, il est spécifié sur le paramètre à l’aide `ModelBinder` attribut. Notez que le contrôleur et l’action de méthode sont simplifiées par rapport à la recherche de l’entité dans la méthode d’action. La logique pour extraire l’auteur à l’aide d’Entity Framework Core est déplacée vers le classeur de modèles. Cela peut être considérable simplification lorsque vous disposez de plusieurs méthodes qui lier au modèle auteur et peuvent vous aider à suivre le [principe sec](http://deviq.com/don-t-repeat-yourself/).

Vous pouvez appliquer la `ModelBinder` d’attributs aux propriétés de modèle individuel (tel que sur un viewmodel) ou aux paramètres de méthode d’action pour spécifier un classeur de modèles ou un nom de modèle pour simplement ce type ou l’action.

### <a name="implementing-a-modelbinderprovider"></a>Implémentation d’un ModelBinderProvider

Au lieu d’appliquer un attribut, vous pouvez implémenter `IModelBinderProvider`. Il s’agit d’implémentation des classeurs de la nouvelle infrastructure intégrée. Lorsque vous spécifiez le type de votre classeur opère sur, vous spécifiez le type d’argument, il génère, **pas** accepte une entrée votre classeur. Le fournisseur de classeurs suivant fonctionne avec les `AuthorEntityBinder`. Lorsqu’il est ajouté à la collection de MVC de fournisseurs, vous n’avez pas besoin d’utiliser le `ModelBinder` de l’attribut `Author` ou `Author` des paramètres typés.

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> Remarque : Le code précédent renvoie un `BinderTypeModelBinder`. `BinderTypeModelBinder`sert de fabrique pour les classeurs de modèles et fournit l’injection de dépendance (DI). Le `AuthorEntityBinder` requiert DI accéder aux EF Core. Utilisez `BinderTypeModelBinder` si votre classeur de modèles exige des services de DI.

Pour utiliser un fournisseur de classeurs de modèles personnalisé, ajoutez-le dans `ConfigureServices`:

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

Lors de l’évaluation de classeurs de modèles, la collection de fournisseurs est examinée dans l’ordre. Le premier fournisseur qui retourne un classeur est utilisé.

L’illustration suivante montre la valeur par défaut de classeurs de modèles à partir du débogueur.

![par défaut de classeurs de modèles](custom-model-binding/images/default-model-binders.png "par défaut de classeurs de modèles")

Ajout de votre fournisseur à la fin de la collection peut entraîner un classeur de modèles intégrés appelé avant que votre classeur personnalisé a un risque. Dans cet exemple, le fournisseur personnalisé est ajouté au début de la collection pour vous assurer qu’il est utilisé pour `Author` arguments de l’action.

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

## <a name="recommendations-and-best-practices"></a>Recommandations et meilleures pratiques

Classeurs de modèles personnalisé :
- Déconseillé définir les codes d’état ou de retourner des résultats (par exemple, 404 introuvable). En cas de liaison de modèle, un [filtre d’action](xref:mvc/controllers/filters) ou logique dans la méthode d’action doit gérer l’échec.
- Sont particulièrement utiles pour la suppression du code répétitif et problèmes transversaux à partir des méthodes d’action.
- Ne doit généralement pas être utilisé pour convertir une chaîne en un type personnalisé, un [ `TypeConverter` ](https://docs.microsoft.com//dotnet/api/system.componentmodel.typeconverter) est généralement une meilleure option.
