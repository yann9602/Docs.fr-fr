---
uid: web-api/overview/testing-and-debugging/unit-testing-controllers-in-web-api
title: "Test unitaire contrôleurs dans ASP.NET Web API 2 | Documents Microsoft"
author: MikeWasson
description: "Cette rubrique décrit certaines techniques spécifiques pour l’unité de contrôleurs de test dans l’API Web 2. Avant de lire cette rubrique, vous pouvez souhaiter lire le didacticiel unité..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/11/2014
ms.topic: article
ms.assetid: 43a6cce7-a3ef-42aa-ad06-90d36d49f098
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/testing-and-debugging/unit-testing-controllers-in-web-api
msc.type: authoredcontent
ms.openlocfilehash: 167cd24d27977c3652f6a8903054654f5edf7756
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="unit-testing-controllers-in-aspnet-web-api-2"></a>Test unitaire contrôleurs dans ASP.NET Web API 2
====================
par [Mike Wasson](https://github.com/MikeWasson)

> Cette rubrique décrit certaines techniques spécifiques pour l’unité de contrôleurs de test dans l’API Web 2. Avant de lire cette rubrique, vous pouvez souhaiter lire le didacticiel [unité test ASP.NET Web API 2](unit-testing-with-aspnet-web-api.md), qui montre comment ajouter un projet de test unitaire à votre solution.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> - [Visual Studio 2017](https://www.visualstudio.com/vs/)
> - API Web 2
> - [Moq](https://github.com/Moq) 4.5.30

> [!NOTE]
> J’ai utilisé Moq, mais le même principe s’applique à n’importe quelle infrastructure factices. Moq 4.5.30 (et versions ultérieures) prend en charge de Visual Studio 2017, Roslyn et .NET 4.5 et versions ultérieures.

Il est courant dans les tests unitaires &quot;réorganiser act-assert&quot;:

- Réorganiser : Configurer les composants requis pour le test à exécuter.
- ACT : Effectuer le test.
- Assertion : Vérifiez que le test a réussi.

Dans l’étape d’organisation, vous souvent utiliser fictifs ou objets du stub. Qui réduit le nombre de dépendances, donc le test se concentre sur un point de test.

Voici quelques éléments que vous devez le test unitaire sur vos contrôleurs de l’API Web :

- L’action retourne le type de réponse approprié.
- Paramètres non valides renvoient la réponse d’erreur approprié.
- L’action appelle la méthode correcte sur la couche du référentiel ou un service.
- Si la réponse inclut un modèle de domaine, vérifiez le type de modèle.

Voici quelques-unes des choses générales pour tester, mais les spécificités dépendent de votre implémentation de contrôleur. En particulier, il fait une grande différence si vos actions de contrôleur retournent **HttpResponseMessage** ou **IHttpActionResult**. Pour plus d’informations sur ces types de résultats, consultez [résultats d’Action dans l’Api Web 2](../getting-started-with-aspnet-web-api/action-results.md).

## <a name="testing-actions-that-return-httpresponsemessage"></a>Test des Actions qui retournent des objets HttpResponseMessage

Voici un exemple d’un contrôleur dont retour actions **HttpResponseMessage**.

[!code-csharp[Main](unit-testing-controllers-in-web-api/samples/sample1.cs)]

Notez le contrôleur utilise l’injection de dépendances pour injecter un `IProductRepository`. Ainsi, le contrôleur des tests, car vous pouvez injecter un référentiel fictif. Le test unitaire suivant vérifie que la `Get` méthode écrit un `Product` dans le corps de réponse. Supposons que `repository` est un fictifs `IProductRepository`.

[!code-csharp[Main](unit-testing-controllers-in-web-api/samples/sample2.cs)]

Il est important de définir **demande** et **Configuration** sur le contrôleur. Dans le cas contraire, le test échoue avec une **ArgumentNullException** ou **InvalidOperationException**.

## <a name="testing-link-generation"></a>Génération du lien de test

Le `Post` les appels de méthode **UrlHelper.Link** pour créer des liens dans la réponse. Cela nécessite un peu plus de programme d’installation dans le test unitaire :

[!code-csharp[Main](unit-testing-controllers-in-web-api/samples/sample3.cs)]

Le **UrlHelper** classe a besoin des requête URL et données d’itinéraire, par conséquent, le test a définir des valeurs pour ces. Une autre option consiste fictifs ou stub **UrlHelper**. Avec cette approche, vous remplacez la valeur par défaut [ApiController.Url](https://msdn.microsoft.com/en-us/library/system.web.http.apicontroller.url.aspx) avec une version fictifs ou stub qui retourne une valeur fixe.

Nous allons réécrire le test à l’aide de la [Moq](https://github.com/Moq) framework. Installer le `Moq` package NuGet dans le projet de test.

[!code-csharp[Main](unit-testing-controllers-in-web-api/samples/sample4.cs)]

Dans cette version, vous n’avez pas besoin de configurer les données d’itinéraire, car la fictifs **UrlHelper** retourne une chaîne constante.


## <a name="testing-actions-that-return-ihttpactionresult"></a>Test des Actions qui retournent IHttpActionResult

Dans l’API Web 2, une action de contrôleur peut retourner **IHttpActionResult**, qui est analogue à **ActionResult** dans ASP.NET MVC. Le **IHttpActionResult** interface définit un modèle de commande pour créer des réponses HTTP. Au lieu de créer directement à la réponse, le contrôleur renvoie un **IHttpActionResult**. Une version ultérieure, le pipeline appelle la **IHttpActionResult** pour créer la réponse. Cette approche facilite écrire des tests unitaires, car vous pouvez ignorer le programme d’installation n’est nécessaire pour de nombreuses **HttpResponseMessage**.

Voici un exemple controller dont retour actions **IHttpActionResult**.

[!code-csharp[Main](unit-testing-controllers-in-web-api/samples/sample5.cs)]

Cet exemple illustre des modèles communs à l’aide de **IHttpActionResult**. Voyons comment à unité les tester.

### <a name="action-returns-200-ok-with-a-response-body"></a>Action renvoie 200 (OK) avec un corps de réponse

Le `Get` les appels de méthode `Ok(product)` si le produit est trouvé. Dans le test unitaire, assurez-vous que le type de retour est **OkNegotiatedContentResult** et le produit retourné a l’ID de droite.

[!code-csharp[Main](unit-testing-controllers-in-web-api/samples/sample6.cs)]

Notez que le test unitaire n’exécute pas le résultat d’action. Vous pouvez supposer que le résultat d’action crée la réponse HTTP correctement. (C’est pourquoi l’infrastructure API Web a son propre tests unitaires !)

### <a name="action-returns-404-not-found"></a>Action retourne 404 (introuvable)

Le `Get` les appels de méthode `NotFound()` si le produit est introuvable. Dans ce cas, le test unitaire vérifie uniquement si le type de retour est **NotFoundResult**.

[!code-csharp[Main](unit-testing-controllers-in-web-api/samples/sample7.cs)]

### <a name="action-returns-200-ok-with-no-response-body"></a>Action renvoie 200 (OK) avec aucun corps de réponse

Le `Delete` les appels de méthode `Ok()` pour renvoyer une réponse HTTP 200 vide. Comme l’exemple précédent, le test unitaire vérifie le type de retour, dans ce cas **OkResult**.

[!code-csharp[Main](unit-testing-controllers-in-web-api/samples/sample8.cs)]

### <a name="action-returns-201-created-with-a-location-header"></a>Action renvoie 201 (créé) avec un en-tête d’emplacement

Le `Post` les appels de méthode `CreatedAtRoute` pour renvoyer une réponse HTTP 201 avec un URI dans l’en-tête Location. Dans le test unitaire, vérifiez que l’action définit les valeurs de routage corrects.

[!code-csharp[Main](unit-testing-controllers-in-web-api/samples/sample9.cs)]

### <a name="action-returns-another-2xx-with-a-response-body"></a>Action retourne un autre 2xx avec un corps de réponse

Le `Put` les appels de méthode `Content` pour renvoyer une réponse HTTP 202 (accepté) avec un corps de réponse. Ce cas est semblable au retour de 200 (OK), mais le test unitaire doit également vérifier le code d’état.

[!code-csharp[Main](unit-testing-controllers-in-web-api/samples/sample10.cs)]

## <a name="additional-resources"></a>Ressources supplémentaires

- [Simulation d’Entity Framework lorsque l’API Web ASP.NET 2 de tests unitaires](mocking-entity-framework-when-unit-testing-aspnet-web-api-2.md)
- [Écrivez des tests pour un service ASP.NET Web API](https://blogs.msdn.com/b/youssefm/archive/2013/01/28/writing-tests-for-an-asp-net-webapi-service.aspx) (billet de blog de Youssef Moussaoui).
- [Débogage API Web ASP.NET avec le débogueur d’itinéraires](https://blogs.msdn.com/b/webdev/archive/2013/04/04/debugging-asp-net-web-api-with-route-debugger.aspx)
