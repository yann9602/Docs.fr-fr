---
uid: web-api/overview/getting-started-with-aspnet-web-api/action-results
title: "Entraîne une API Web 2 | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/03/2014
ms.topic: article
ms.assetid: 2fc4797c-38ef-4cc7-926c-ca431c4739e8
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/getting-started-with-aspnet-web-api/action-results
msc.type: authoredcontent
ms.openlocfilehash: d0db5c6d45020861d7295ab1db989caee525fff9
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="action-results-in-web-api-2"></a>Résultats d’action dans l’API Web 2
====================
par [Mike Wasson](https://github.com/MikeWasson)

Cette rubrique décrit comment les API Web ASP.NET convertit la valeur de retour d’une action de contrôleur dans un message de réponse HTTP.

Une action de contrôleur d’API Web peut renvoyer la valeur d’une des opérations suivantes :

1. void
2. **HttpResponseMessage**
3. **IHttpActionResult**
4. Un autre type

En fonction de ces est retournée, API Web utilise un mécanisme différent pour créer la réponse HTTP.

| Type de retour | Comment les API Web crée la réponse |
| --- | --- |
| void | Retour 204 vide (aucun contenu) |
| **HttpResponseMessage** | Convertir directement à un message de réponse HTTP. |
| **IHttpActionResult** | Appelez **ExecuteAsync** pour créer un **HttpResponseMessage**, puis la convertir en un message de réponse HTTP. |
| Autre type | Écrire la valeur de retournée sérialisée dans le corps de réponse ; retourner 200 (OK). |

Le reste de cette rubrique décrit chaque option plus en détail.

## <a name="void"></a>void

Si le type de retour est `void`, API Web retourne simplement une réponse HTTP vide avec le code d’état 204 (aucun contenu).

Contrôleur d’exemple :

[!code-csharp[Main](action-results/samples/sample1.cs)]

Réponse HTTP :

[!code-console[Main](action-results/samples/sample2.cmd)]

## <a name="httpresponsemessage"></a>HttpResponseMessage

Si l’action retourne un [HttpResponseMessage](https://msdn.microsoft.com/library/system.net.http.httpresponsemessage.aspx), API Web convertit la valeur de retour directement dans un message de réponse HTTP, en utilisant les propriétés de la **HttpResponseMessage** objet à remplir la réponse.

Cette option vous donne un contrôle important sur le message de réponse. Par exemple, l’action du contrôleur suivant définit l’en-tête Cache-Control.

[!code-csharp[Main](action-results/samples/sample3.cs)]

Réponse :

[!code-console[Main](action-results/samples/sample4.cmd?highlight=2)]

Si vous passez d’un modèle de domaine pour le **CreateResponse** (méthode), l’API Web utilise un [formateur de média](../formats-and-model-binding/media-formatters.md) pour écrire le modèle sérialisé dans le corps de réponse.

[!code-csharp[Main](action-results/samples/sample5.cs)]

API Web utilise l’en-tête Accept dans la requête pour choisir le formateur. Pour plus d’informations, consultez [négociation de contenu](../formats-and-model-binding/content-negotiation.md).

## <a name="ihttpactionresult"></a>IHttpActionResult

Le **IHttpActionResult** interface a été introduite dans l’API Web 2. Essentiellement, elle définit un **HttpResponseMessage** en usine. Voici quelques avantages de l’utilisation de la **IHttpActionResult** interface :

- Simplifie la [test unitaire](../testing-and-debugging/unit-testing-controllers-in-web-api.md) vos contrôleurs.
- Déplace une logique commune pour la création de réponses HTTP dans des classes séparées.
- Rend l’objectif de l’action de contrôleur plus claire, en masquant les détails de bas niveau de la construction de la réponse.

**IHttpActionResult** contient une méthode unique, **ExecuteAsync**, ce qui crée de façon asynchrone un **HttpResponseMessage** instance.

[!code-csharp[Main](action-results/samples/sample6.cs)]

Si une action du contrôleur retourne une **IHttpActionResult**, API Web appelle la **ExecuteAsync** méthode pour créer un **HttpResponseMessage**. Il convertit le **HttpResponseMessage** dans un message de réponse HTTP.

Voici une simple implémentation n’a de **IHttpActionResult** qui crée une réponse en texte brut :

[!code-csharp[Main](action-results/samples/sample7.cs)]

Action de contrôleur d’exemple :

[!code-csharp[Main](action-results/samples/sample8.cs)]

Réponse :

[!code-console[Main](action-results/samples/sample9.cmd)]

Plus souvent, vous utiliserez le **IHttpActionResult** implémentations définies dans le  **[System.Web.Http.Results](https://msdn.microsoft.com/library/system.web.http.results.aspx)**  espace de noms. Le **ApiController** classe définit des méthodes d’assistance qui retournent les résultats d’action intégrés.

Dans l’exemple suivant, si la demande ne correspond pas à un ID de produit existant, le contrôleur appelle [ApiController.NotFound](https://msdn.microsoft.com/library/system.web.http.apicontroller.notfound.aspx) pour créer une réponse 404 (introuvable). Sinon, le contrôleur appelle [ApiController.OK](https://msdn.microsoft.com/library/dn314591.aspx), ce qui crée une réponse de 200 (OK) qui contient le produit.

[!code-csharp[Main](action-results/samples/sample10.cs)]

## <a name="other-return-types"></a>Autres Types de retour

Pour tous les autres types de retour, les API Web utilise un [formateur de média](../formats-and-model-binding/media-formatters.md) pour sérialiser la valeur de retour. API Web écrit la valeur sérialisée dans le corps de réponse. Le code d’état de réponse est 200 (OK).

[!code-csharp[Main](action-results/samples/sample11.cs)]

L’inconvénient de cette approche est que vous ne pouvez pas retourner directement un code d’erreur, tels que 404. Toutefois, vous pouvez lever une **HttpResponseException** pour les codes d’erreur. Pour plus d’informations, consultez [Exception Handling in ASP.NET Web API](../error-handling/exception-handling.md).

API Web utilise l’en-tête Accept dans la requête pour choisir le formateur. Pour plus d’informations, consultez [négociation de contenu](../formats-and-model-binding/content-negotiation.md).

Exemple de demande

[!code-console[Main](action-results/samples/sample12.cmd)]

Exemple de réponse :

[!code-console[Main](action-results/samples/sample13.cmd)]
