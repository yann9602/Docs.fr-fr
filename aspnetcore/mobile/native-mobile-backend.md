---
title: "Création de Services principaux pour les Applications mobiles natives"
author: ardalis
description: 
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: mobile/native-mobile-backend
ms.openlocfilehash: ff09f331cff5cca7b42fa89bff55c0ed5c7d82f4
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="creating-backend-services-for-native-mobile-applications"></a>Création de Services principaux pour les Applications mobiles natives

Par [Steve Smith](https://ardalis.com/)

Les applications mobiles peuvent communiquer facilement avec les services principaux de ASP.NET Core.

[Afficher ou télécharger l’exemple de code de services principaux](https://github.com/aspnet/Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a>L’exemple d’application Mobile natif

Ce didacticiel montre comment créer des services principaux à l’aide d’ASP.NET MVC de base pour prendre en charge des applications mobiles natives. Elle utilise le [Xamarin Forms ToDoRest application](https://developer.xamarin.com/guides/xamarin-forms/web-services/consuming/rest/) en tant que son client natif, qui inclut des clients natifs distincts pour les appareils Android, iOS, Windows universel et Windows Phone. Vous pouvez suivre le didacticiel lié pour créer l’application native (et installer les outils Xamarin libres nécessaires), ainsi que télécharger l’exemple de solution Xamarin. L’exemple de Xamarin inclut un projet de services ASP.NET Web API 2, qui remplace les applications ASP.NET Core de cet article (sans aucune modification n’est requise par le client).

![Pour l’application de faire le reste en cours d’exécution sur un smartphone Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>Fonctionnalités

L’application ToDoRest prend en charge l’affichage, ajout, la suppression et la mise à jour des éléments de tâche. Chaque élément possède un ID, un nom, Notes et une propriété qui indique si elle est été encore terminé.

La vue principale des éléments, comme indiqué ci-dessus, répertorie le nom de chaque élément et indique si elle est effectuée avec une coche.

Si vous appuyez sur la `+` icône ouvre une boîte de dialogue Ajouter élément :

![Élément de boîte de dialogue Ajouter](native-mobile-backend/_static/todo-android-new-item.png)

Un élément sur l’écran de liste principale ouvre une boîte de dialogue Modifier où nom de l’élément, les notes de publication et les paramètres terminés peuvent être modifiés ou que l’élément peut être supprimé :

![Élément de boîte de dialogue Modifier](native-mobile-backend/_static/todo-android-edit-item.png)

Cet exemple est configuré par défaut pour utiliser les services principaux hébergés sur developer.xamarin.com, qui autorisent des opérations en lecture seule. Pour tester vous-même par rapport à l’application ASP.NET Core créée dans la section suivante, en cours d’exécution sur votre ordinateur, vous devez mettre à jour de l’application `RestUrl` constante. Accédez à la `ToDoREST` de projet et ouvrez le *Constants.cs* fichier. Remplacez le `RestUrl` avec une URL qui inclut IP votre ordinateur adresse (pas localhost ou 127.0.0.1, étant donné que cette adresse est utilisée à partir de l’émulateur d’appareil, pas à partir de votre ordinateur). Inclut également le numéro de port (5000). Afin de vérifier que vos services fonctionnent avec un périphérique, assurez-vous de que vous n’avez pas un pare-feu actif bloque l’accès à ce port.

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a>Création du projet ASP.NET Core

Créer une Application Web ASP.NET Core dans Visual Studio. Choisissez le modèle de l’API Web et aucune authentification. Nommez le projet *ToDoApi*.

![Boîte de dialogue nouvelle Application Web ASP.NET avec le modèle de projet d’API Web sélectionné](native-mobile-backend/_static/web-api-template.png)

L’application doit répondre à toutes les demandes adressées au port 5000. Mise à jour *Program.cs* à inclure `.UseUrls("http://*:5000")` d’effectuer cette opération :

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> Assurez-vous que vous exécutez l’application directement, plutôt que derrière IIS Express, qui ignore les demandes non locaux par défaut. Exécutez `dotnet run` à partir d’une invite de commandes, ou choisissez le profil de nom d’application dans la liste déroulante de cible de débogage dans la barre d’outils de Visual Studio.

Ajouter une classe de modèle pour représenter des éléments de tâche. Marque requis des champs à l’aide de la `[Required]` attribut :

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

Les méthodes d’API requièrent un moyen d’utiliser des données. Utiliser le même `IToDoRepository` l’exemple de Xamarin d’origine utilise l’interface :

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

Pour cet exemple, l’implémentation utilise seulement une collection privée d’éléments :

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

Configurer la mise en oeuvre dans *Startup.cs*:

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

À ce stade, vous êtes prêt à créer le *ToDoItemsController*.

> [!TIP]
> En savoir plus sur la création web API dans [construction votre API Web premier avec ASP.NET MVC de base et de Visual Studio](../tutorials/first-web-api.md).

## <a name="creating-the-controller"></a>Création du contrôleur

Ajoutez un nouveau contrôleur pour le projet, *ToDoItemsController*. Il doit hériter de Microsoft.AspNetCore.Mvc.Controller. Ajouter un `Route` attribut pour indiquer que le contrôleur gère les demandes effectuées aux chemins d’accès commençant par `api/todoitems`. Le `[controller]` jeton dans l’itinéraire est remplacé par le nom du contrôleur (l’omission de la `Controller` suffixe) et s’avère particulièrement utile pour les itinéraires globales. En savoir plus sur [routage](../fundamentals/routing.md).

Le contrôleur a besoin d’un `IToDoRepository` à fonction ; demander une instance de ce type via le constructeur du contrôleur. Lors de l’exécution, cette instance est fournie à l’aide de la prise en charge de l’infrastructure de [injection de dépendance](../fundamentals/dependency-injection.md).

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

Cette API prend en charge quatre verbes HTTP différents pour effectuer des opérations CRUD (création, lecture, mise à jour, suppression) sur la source de données. La plus simple d'entre eux est l’opération de lecture, qui correspond à une requête HTTP GET.

### <a name="reading-items"></a>Lecture des éléments

Demande d’une liste d’éléments est effectuée avec une demande GET pour le `List` (méthode). Le `[HttpGet]` de l’attribut le `List` méthode indique que cette action doit gérer uniquement les demandes GET. L’itinéraire pour cette action est l’itinéraire spécifié sur le contrôleur. Vous n’avez pas nécessairement besoin d’utiliser le nom d’action dans le cadre de l’itinéraire. Il vous suffit de vous assurer de que chaque action comporte un itinéraire unique et non équivoque. Attributs de routage peuvent être appliqués au contrôleur et à des niveaux de méthode pour créer des itinéraires spécifiques.

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

Le `List` méthode retourne un code de réponse OK 200 et tous les éléments de tâche, sérialisées au format JSON.

Vous pouvez tester votre nouvelle méthode d’API à l’aide de divers outils, tels que [Postman](https://www.getpostman.com/docs/), illustrée ici :

![Affichage d’une demande GET todoitems et le corps de la réponse de JSON pour les trois éléments renvoyés dans la console postman](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>Création d’éléments

Par convention, la création de nouveaux éléments de données est mappé pour le verbe HTTP POST. Le `Create` méthode a une `[HttpPost]` attribut appliqué et accepte un `ToDoItem` instance. Étant donné que la `item` argument sera passé dans le corps de la publication, ce paramètre est décoré avec le `[FromBody]` attribut.

À l’intérieur de la méthode, l’élément est activé pour la validité et l’existence préalable dans le magasin de données, et si aucun problème se produit, il est ajouté à l’aide de l’espace de stockage. La vérification de la `ModelState.IsValid` effectue [validation des modèles](../mvc/models/validation.md)et doit être effectuée dans chaque méthode d’API qui accepte une entrée d’utilisateur.

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

L’exemple utilise une énumération qui contient les codes d’erreur qui sont transmis sur le client mobile :

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

Ajout de nouveaux éléments à l’aide de Postman en choisissant le verbe POST en fournissant le nouvel objet au format JSON dans le corps de la demande de test. Vous devez également ajouter un en-tête de demande spécifiant un `Content-Type` de `application/json`.

![Affichage d’une publication et une réponse dans la console postman](native-mobile-backend/_static/postman-post.png)

La méthode retourne l’élément qui vient d’être créé dans la réponse.

### <a name="updating-items"></a>Mise à jour des éléments

Modification des enregistrements s’effectue à l’aide de requêtes HTTP PUT. Outre cette modification, le `Edit` méthode est presque identique à `Create`. Notez que si l’enregistrement n’est trouvé, le `Edit` action retournera un `NotFound` réponse (404).

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

Pour tester avec Postman, remplacez le verbe PUT. Spécifiez les données de l’objet mis à jour dans le corps de la demande.

![Affichage d’un PUT et réponse de la console postman](native-mobile-backend/_static/postman-put.png)

Cette méthode retourne un `NoContent` réponse (204) lors de la réussite, par souci de cohérence avec l’API préexistant.

### <a name="deleting-items"></a>Suppression d’éléments

La suppression d’enregistrements est effectuée par des demandes de suppression du service et en passant l’ID de l’élément à supprimer. Mise à jour, recevoir des demandes pour les éléments qui n’existent pas dans `NotFound` réponses. Sinon, une demande réussie obtiennent un `NoContent` réponse (204).

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

Notez que lorsque vous testez les fonctionnalités de suppression, rien n’est requis dans le corps de la demande.

![Affichage d’une suppression et une réponse de la console postman](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a>Conventions d’API Web courantes

Lorsque vous développez les services principaux pour votre application, vous devez élaborer un jeu cohérent de conventions ou les stratégies de gestion des problèmes transversaux. Par exemple, dans le service illustré ci-dessus, les demandes des enregistrements spécifiques qui n’ont pas été trouvés a reçu un `NotFound` réponse, plutôt qu’un `BadRequest` réponse. De même, les commandes envoyées à ce service passé dans les types de modèle lié toujours vérifiées `ModelState.IsValid` et a retourné un `BadRequest` pour les types de modèle non valide.

Une fois que vous avez identifié une stratégie commune pour votre API, vous pouvez encapsuler généralement dans un [filtre](../mvc/controllers/filters.md). En savoir plus sur [comment encapsuler des politiques d’API communes dans les applications ASP.NET MVC de base](https://msdn.microsoft.com/magazine/mt767699.aspx).
