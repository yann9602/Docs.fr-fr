---
uid: web-api/overview/advanced/calling-a-web-api-from-a-net-client
title: "Appeler une API Web à partir d’un Client .NET (c#) | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/24/2017
ms.topic: article
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/advanced/calling-a-web-api-from-a-net-client
msc.type: authoredcontent
ms.openlocfilehash: 8fcc5e7c6bc39f961931589128a7a5863482aa4e
ms.sourcegitcommit: b38796ea3806bf39b89806adfa681b2a33762907
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
<a name="call-a-web-api-from-a-net-client-c"></a>Appeler une API Web à partir d’un Client .NET (c#)
====================
par [Mike Wasson](https://github.com/MikeWasson) et [Rick Anderson](https://twitter.com/RickAndMSFT)

[Télécharger le projet terminé](https://github.com/aspnet/Docs/tree/master/aspnet/web-api/overview/advanced/calling-a-web-api-from-a-net-client/sample)

Ce didacticiel montre comment appeler une API web à partir d’une application .NET, à l’aide de [System.Net.Http.HttpClient.](https://msdn.microsoft.com/en-us/library/system.net.http.httpclient(v=vs.110).aspx)

Dans ce didacticiel, une application cliente est écrit qui utilise l’API de web suivant :

| Action | Méthode HTTP | URI relatif |
| --- | --- | --- |
| Obtenir un produit par ID | GET | /API/produits/*id* |
| Créer un nouveau produit | PUBLIER | produits/api / |
| Mettre à jour un produit | PUT | /API/produits/*id* |
| Supprimer un produit | SUPPR | /API/produits/*id* |

Pour savoir comment implémenter cette API avec l’API Web ASP.NET, consultez [création d’une API Web qui prend en charge les opérations CRUD](xref:web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api
).

Par souci de simplicité, l’application cliente dans ce didacticiel est une application console Windows. **HttpClient** est également pris en charge pour les applications du Windows Store et Windows Phone. Pour plus d’informations, consultez [écriture Web API Client de Code pour plusieurs plateformes utilisant les bibliothèques portables](https://blogs.msdn.com/b/webdev/archive/2013/07/19/writing-web-api-client-code-for-multiple-platforms-using-portable-libraries.aspx)

<a id="CreateConsoleApp"></a>
## <a name="create-the-console-application"></a>Créer l’Application Console

Dans Visual Studio, créez une nouvelle application de console Windows nommée **HttpClientSample** et collez le code suivant :

[!code-csharp[Main](calling-a-web-api-from-a-net-client/sample/client/Program.cs?name=snippet_all)]

Le code précédent est l’application cliente complète.

`RunAsync`s’exécute et des blocs jusqu'à son achèvement. La plupart des **HttpClient** méthodes sont asynchrones, car ils effectuent des e/s réseau. Toutes les tâches asynchrones sont effectuées à l’intérieur de `RunAsync`. Normalement une application ne bloque pas le thread principal, mais cette application n’autorise aucune intervention.

[!code-csharp[Main](calling-a-web-api-from-a-net-client/sample/client/Program.cs?name=snippet_run)]

<a id="InstallClientLib"></a>
## <a name="install-the-web-api-client-libraries"></a>Installer les bibliothèques de Client de l’API Web

Utilisez le Gestionnaire de Package NuGet pour installer le package de bibliothèques de Client d’API Web.

Dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet** > **Console du gestionnaire de package**. Dans Package Manager Console (PMC), tapez la commande suivante :

`Install-Package Microsoft.AspNet.WebApi.Client`

La commande précédente ajoute les packages NuGet suivants au projet :

* Microsoft.AspNet.WebApi.Client
* Newtonsoft.Json

Json.NET est une infrastructure JSON de hautes performances populaire pour .NET.

<a id="AddModelClass"></a>
## <a name="add-a-model-class"></a>Ajouter une classe de modèle

Examinez la `Product` classe :

[!code-csharp[Main](calling-a-web-api-from-a-net-client/sample/client/Program.cs?name=snippet_prod)]

Cette classe représente le modèle de données utilisé par l’API web. Une application peut utiliser **HttpClient** pour lire un `Product` instance à partir d’une réponse HTTP. L’application n’a pas d’écrire du code la désérialisation.

<a id="InitClient"></a>
## <a name="create-and-initialize-httpclient"></a>Créez et initialisez HttpClient

Examinez la méthode statique **HttpClient** propriété :

[!code-csharp[Main](calling-a-web-api-from-a-net-client/sample/client/Program.cs?name=snippet_HttpClient)]

**HttpClient** est destinée à être instancié une seule fois et réutilisées dans l’ensemble de la durée de vie d’une application. Les conditions suivantes peuvent entraîner **SocketException** erreurs :

* Création d’un **HttpClient** instance par demande.
* Serveur sous une charge importante.

Création d’un **HttpClient** instance par demande peut épuiser les sockets disponibles.

Le code suivant initialise les **HttpClient** instance :

[!code-csharp[Main](calling-a-web-api-from-a-net-client/sample/client/Program.cs?name=snippet5)]

Le code précédent :

* Définit l’URI de base pour les requêtes HTTP. Modifier le numéro de port pour le port utilisé dans l’application serveur. L’application ne fonctionne pas si le port de l’application serveur est utilisé.
* Définit l’en-tête Accept pour « application/json ». Définition de cet en-tête indique le serveur pour envoyer des données au format JSON.

<a id="GettingResource"></a>
## <a name="send-a-get-request-to-retrieve-a-resource"></a>Envoyer une demande GET pour récupérer une ressource

Le code suivant envoie une demande GET pour un produit :

[!code-csharp[Main](calling-a-web-api-from-a-net-client/sample/client/Program.cs?name=snippet_GetProductAsync)]

Le **GetAsync** méthode envoie la demande HTTP GET. Lorsque la méthode se termine, elle retourne un **HttpResponseMessage** qui contient la réponse HTTP. Si le code d’état dans la réponse est un code de réussite, le corps de réponse contient la représentation JSON d’un produit. Appelez **ReadAsAsync** à désérialiser la charge utile JSON pour un `Product` instance. Le **ReadAsAsync** méthode est asynchrone, car le corps de réponse peut être arbitrairement grand.

**HttpClient** ne lève pas d’exception lors de la réponse HTTP contient un code d’erreur. Au lieu de cela, le **IsSuccessStatusCode** propriété **false** si l’état est un code d’erreur. Si vous souhaitez traiter des codes d’erreur HTTP en tant qu’exceptions, appelez [HttpResponseMessage.EnsureSuccessStatusCode](https://msdn.microsoft.com/en-us/library/system.net.http.httpresponsemessage.ensuresuccessstatuscode(v=vs.110).aspx) sur l’objet de réponse. `EnsureSuccessStatusCode`lève une exception si le code d’état se situe en dehors de la plage 200&ndash;299. Notez que **HttpClient** peut lever des exceptions pour d’autres raisons &mdash; par exemple, si la demande expire.

<a id="MediaTypeFormatters"></a>
### <a name="media-type-formatters-to-deserialize"></a>Formateurs de Type de média à désérialiser

Lorsque **ReadAsAsync** est appelée sans paramètres, il utilise un ensemble par défaut de *formateurs de médias* pour lire le corps de réponse. Les formateurs par défaut prend en charge les données de forme codée en url JSON et XML.

Au lieu d’utiliser les modules de formatage par défaut, vous pouvez fournir une liste de formateurs à la **ReadAsAsync** (méthode).  À l’aide une liste de formateurs est utile si vous avez un formateur de type de média personnalisé :

```csharp
var formatters = new List<MediaTypeFormatter>() {
    new MyCustomFormatter(),
    new JsonMediaTypeFormatter(),
    new XmlMediaTypeFormatter()
};
resp.Content.ReadAsAsync<IEnumerable<Product>>(formatters);
```

Pour plus d’informations, consultez [formateurs de médias dans ASP.NET Web API 2](../formats-and-model-binding/media-formatters.md)

## <a name="sending-a-post-request-to-create-a-resource"></a>Envoi d’une demande POST pour créer une ressource

Le code suivant envoie une demande POST qui contienne un `Product` instance au format JSON :

[!code-csharp[Main](calling-a-web-api-from-a-net-client/sample/client/Program.cs?name=snippet_CreateProductAsync)]

Le **PostAsJsonAsync** méthode :

* Sérialise un objet au format JSON.
* Envoie la charge utile JSON dans une requête POST.

Si la demande réussit :

* Elle doit retourner la réponse 201 (créé).
* La réponse doit inclure l’URL des ressources créées dans l’en-tête Location.

<a id="PuttingResource"></a>
## <a name="sending-a-put-request-to-update-a-resource"></a>Envoi d’une demande PUT pour mettre à jour une ressource

Le code suivant envoie une demande PUT pour mettre à jour un produit :

[!code-csharp[Main](calling-a-web-api-from-a-net-client/sample/client/Program.cs?name=snippet_UpdateProductAsync)]

Le **PutAsJsonAsync** méthode fonctionne comme **PostAsJsonAsync**, sauf qu’il envoie une demande PUT, POST à la place.

<a id="DeletingResource"></a>
## <a name="sending-a-delete-request-to-delete-a-resource"></a>Envoi d’une demande de suppression pour supprimer une ressource

Le code suivant envoie une demande de suppression pour supprimer un produit :

[!code-csharp[Main](calling-a-web-api-from-a-net-client/sample/client/Program.cs?name=snippet_DeleteProductAsync)]

Comme GET, une demande de suppression n’a pas un corps de demande. Vous n’avez pas besoin de spécifier le format JSON ou XML avec DELETE.

## <a name="test-the-sample"></a>Tester l’exemple

Pour tester l’application cliente :

1. [Télécharger](https://github.com/aspnet/Docs/tree/master/aspnet/web-api/overview/advanced/calling-a-web-api-from-a-net-client/samples/server) et exécuter l’application serveur. [Les instructions de téléchargement](https://docs.microsoft.com/en-us/aspnet/core/tutorials/#how-to-download-a-sample). Vérifiez que l’application serveur fonctionne. Pour exaxmple, `http://localhost:64195/api/products` doit retourner une liste de produits.
2. Définir l’URI de base pour les requêtes HTTP. Modifier le numéro de port pour le port utilisé dans l’application serveur.
    [!code-csharp[Main](calling-a-web-api-from-a-net-client/sample/client/Program.cs?name=snippet5&highlight=2)]

3. Exécutez l’application cliente. La sortie suivante est produite :

 ```console
 Created at http://localhost:64195/api/products/4
Name: Gizmo     Price: 100.0    Category: Widgets
Updating price...
Name: Gizmo     Price: 80.0     Category: Widgets
Deleted (HTTP Status = 204)
```
