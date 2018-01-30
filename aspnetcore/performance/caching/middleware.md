---
title: "Réponse mise en cache d’intergiciel (middleware) dans ASP.NET Core"
author: guardrex
description: "Découvrez comment configurer et utiliser un intergiciel (middleware) de réponse mise en cache dans ASP.NET Core."
ms.author: riande
manager: wpickett
ms.custom: mvc
ms.date: 01/26/2017
ms.topic: article
ms.prod: asp.net-core
uid: performance/caching/middleware
ms.openlocfilehash: a355dda0fdffe1e936c119ffb477c2817f28bd9c
ms.sourcegitcommit: 18ff1fdaa3e1ae204ed6a2ba9351ce8cf1371c85
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="response-caching-middleware-in-aspnet-core"></a>Réponse mise en cache d’intergiciel (middleware) dans ASP.NET Core

Par [Luke Latham](https://github.com/guardrex) et [John Luo](https://github.com/JunTaoLuo)

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/middleware/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

Cet article explique comment configurer un intergiciel (middleware) de réponse mise en cache dans une application ASP.NET Core. L’intergiciel (middleware) détermine quand les réponses sont mis en cache, les réponses de magasins et sert des réponses à partir du cache. Pour obtenir une présentation de la mise en cache HTTP et le `ResponseCache` d’attribut, consultez [réponse mise en cache](xref:performance/caching/response).

## <a name="package"></a>Package

Pour inclure l’intergiciel (middleware) dans un projet, ajoutez une référence à la [ `Microsoft.AspNetCore.ResponseCaching` ](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) du package ou utilisez le [ `Microsoft.AspNetCore.All` ](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) package (noyaux d’ASP.NET 2.0 ou version ultérieure, lorsque vous ciblez .NET Core).

## <a name="configuration"></a>Configuration

Dans `ConfigureServices`, ajoutez l’intergiciel (middleware) à la collection de service.

[!code-csharp[Main](middleware/sample/Startup.cs?name=snippet1&highlight=3)]

Configurer l’application pour utiliser l’intergiciel (middleware) avec la `UseResponseCaching` méthode d’extension, qui ajoute l’intergiciel (middleware) au pipeline de traitement de la demande. L’exemple d’application ajoute un [ `Cache-Control` ](https://tools.ietf.org/html/rfc7234#section-5.2) en-tête dans la réponse qui met en cache les réponses de mise en cache pendant 10 secondes. L’exemple envoie une [ `Vary` ](https://tools.ietf.org/html/rfc7231#section-7.1.4) en-tête pour configurer l’intergiciel (middleware) pour servir une réponse mise en cache d’uniquement si le [ `Accept-Encoding` ](https://tools.ietf.org/html/rfc7231#section-5.3.4) en-tête des demandes ultérieures correspond à celle de la demande d’origine.

[!code-csharp[Main](middleware/sample/Startup.cs?name=snippet2&highlight=3,7-12)]

Intergiciel (middleware) de réponse mise en cache met uniquement en cache les réponses du serveur qui génèrent un code d’état 200 (OK). Autres réponses, y compris [les pages d’erreur](xref:fundamentals/error-handling), sont ignorées par l’intergiciel (middleware).

> [!WARNING]
> Qui contient le contenu pour les clients authentifiés de réponses doivent être marquées comme ne pouvant pas être afin d’éviter l’intergiciel (middleware) de stockage et traitement des ces réponses. Consultez [Conditions pour la mise en cache](#conditions-for-caching) pour plus d’informations sur la façon dont l’intergiciel (middleware) détermine si une réponse est mis en cache.

## <a name="options"></a>Options

L’intergiciel (middleware) offre trois options pour contrôler la réponse mise en cache.

| Option                | Valeur par défaut |
| --------------------- | ------------- |
| UseCaseSensitivePaths | Détermine si les réponses sont mises en cache sur les chemins d’accès qui respecte la casse.</p><p>La valeur par défaut est `false`. |
| MaximumBodySize       | La mise en cache plus grande taille pour le corps de réponse en octets.</p>La valeur par défaut est `64 * 1024 * 1024` (64 Mo). |
| SizeLimit             | La limite de taille pour l’intergiciel (middleware) du cache de la réponse en octets. La valeur par défaut est `100 * 1024 * 1024` (100 Mo). |

L’exemple suivant configure l’intergiciel (middleware) pour :

* Mettre en cache les réponses inférieures ou égales à 1 024 octets.
* Stocker les réponses par des chemins d’accès qui respecte la casse (par exemple, `/page1` et `/Page1` sont stockés séparément).

```csharp
services.AddResponseCaching(options =>
{
    options.UseCaseSensitivePaths = true;
    options.MaximumBodySize = 1024;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Lorsque vous utilisez les contrôleurs MVC/Web API ou des modèles de page de Pages Razor, le `ResponseCache` attribut spécifie les paramètres nécessaires pour définir les en-têtes appropriés pour la réponse mise en cache. Le seul paramètre de la `ResponseCache` attribut nécessitant strictement l’intergiciel (middleware) est `VaryByQueryKeys`, qui ne correspond pas à un en-tête HTTP réels. Pour plus d’informations, consultez [ResponseCache attribut](xref:performance/caching/response#responsecache-attribute).

Lorsque vous N'utilisez pas le `ResponseCache` attribut, la mise en cache de réponse peut varier avec le `VaryByQueryKeys` fonctionnalité. Utilisez le `ResponseCachingFeature` directement à partir de la `IFeatureCollection` de la `HttpContext`:

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();
if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

## <a name="http-headers-used-by-response-caching-middleware"></a>En-têtes HTTP utilisés par un intergiciel (middleware) de réponse mise en cache

Réponse mise en cache par l’intergiciel (middleware) est configuré à l’aide d’en-têtes HTTP.

| Header | Détails |
| ------ | ------- |
| Autorisation | La réponse n’est pas mis en cache si l’en-tête existe. |
| Cache-Control | L’intergiciel (middleware) considère uniquement la mise en cache des réponses marqués avec la `public` directive de cache. Contrôler la mise en cache avec les paramètres suivants :<ul><li>max-age</li><li>max-stale&#8224;</li><li>frais de min</li><li>doit-revalidate.</li><li>no-cache</li><li>Aucun magasin</li><li>seul if-mise en cache</li><li>private</li><li>public</li><li>s-maxage</li><li>proxy-revalidate&#8225;</li></ul>&#8224; si aucune limite n’est spécifiée pour `max-stale`, l’intergiciel (middleware) n’effectue aucune action.<br>&#8225; `proxy-revalidate` a le même effet que `must-revalidate`.<br><br>Pour plus d’informations, consultez [7231 relative aux RFC : demander des Directives de Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| Pragma | A `Pragma: no-cache` en-tête dans la requête produit le même effet que `Cache-Control: no-cache`. Cet en-tête est remplacé par les directives pertinentes dans le `Cache-Control` en-tête, le cas échéant. Pris en compte pour la compatibilité descendante avec HTTP/1.0. |
| Set-Cookie | La réponse n’est pas mis en cache si l’en-tête existe. |
| Varier | Le `Vary` en-tête est utilisé pour faire varier la réponse mise en cache par un autre en-tête. Par exemple, mettre en cache les réponses par l’encodage en incluant le `Vary: Accept-Encoding` en-tête, qui met en cache les réponses pour les demandes avec des en-têtes `Accept-Encoding: gzip` et `Accept-Encoding: text/plain` séparément. Une réponse avec une valeur d’en-tête de `*` n’est jamais stocké. |
| Expires | Une réponse jugée obsolète par cet en-tête n’est pas enregistrer ou à récupérer, sauf si remplacées par d’autres `Cache-Control` en-têtes. |
| If-None-Match | La réponse complète est pris en charge à partir du cache si la valeur n’est pas `*` et `ETag` de la réponse ne correspond à aucune des valeurs fournies. Sinon, une réponse 304 (non modifié) est pris en charge. |
| If-Modified-Since | Si le `If-None-Match` en-tête n’est pas présent, une réponse complet est pris en charge à partir du cache si la date de la réponse mise en cache est plus récente que la valeur fournie. Sinon, une réponse 304 (non modifié) est pris en charge. |
| Date | Lorsque le traitement à partir du cache, le `Date` en-tête est défini par l’intergiciel (middleware) s’il n’a pas été fourni sur la réponse d’origine. |
| Content-Length | Lorsque le traitement à partir du cache, le `Content-Length` en-tête est défini par l’intergiciel (middleware) s’il n’a pas été fourni sur la réponse d’origine. |
| Durée de vie | Le `Age` en-tête envoyé dans la réponse d’origine est ignoré. L’intergiciel (middleware) calcule une nouvelle valeur avec une réponse mise en cache. |

## <a name="caching-respects-request-cache-control-directives"></a>La mise en cache respecte les directives de contrôle du Cache de demande

L’intergiciel (middleware) respecte les règles de la [spécification de la mise en cache à HTTP 1.1](https://tools.ietf.org/html/rfc7234#section-5.2). Les règles nécessitent un cache d’honorer valide `Cache-Control` en-tête envoyé par le client. Dans la spécification d’un client peut envoyer des demandes avec un `no-cache` valeur d’en-tête et de forcer le serveur génère une nouvelle réponse pour chaque demande. Actuellement, il n’existe aucun contrôle du développeur sur ce comportement de mise en cache lors de l’utilisation de l’intergiciel (middleware), car l’intergiciel (middleware) est conforme à la spécification officielle de mise en cache.

Pour contrôler le comportement de mise en cache plus, Explorer d’autres fonctionnalités de mise en cache de ASP.NET Core. Consultez les rubriques suivantes :

* [Mise en cache en mémoire](xref:performance/caching/memory)
* [Utilisation d’un cache distribué](xref:performance/caching/distributed)
* [Mettre en cache d’assistance de balise dans le cœur d’ASP.NET MVC](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)
* [Tag Helper de cache distribué](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)

## <a name="troubleshooting"></a>Résolution des problèmes

Si le comportement de mise en cache n’est pas comme prévu, vérifiez que les réponses sont mis en cache et capable de servies à partir du cache. Examinez les en-têtes entrants de la demande et la réponse sortante. Activer [journalisation](xref:fundamentals/logging/index) pour aider au débogage.

Lorsque les tests et le dépannage du comportement de mise en cache, un navigateur peut définir les en-têtes de demande qui affectent la mise en cache comme indésirables. Par exemple, un navigateur peut-être définir le `Cache-Control` en-tête `no-cache` ou `max-age=0` lors de l’actualisation d’une page. Les outils suivants peuvent définir explicitement les en-têtes de demande et sont recommandés pour le test de la mise en cache :

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Conditions pour la mise en cache

* La demande doit entraîner une réponse du serveur avec un code d’état 200 (OK).
* La méthode de demande doit être GET ou HEAD.
* Intergiciel Terminal, tel que [intergiciel (middleware) du fichier statique](xref:fundamentals/static-files), ne doit pas traiter la réponse avant de l’intergiciel (middleware) réponse mise en cache.
* Le `Authorization` en-tête ne doit pas être présent.
* `Cache-Control`paramètres de l’en-tête doivent être valides, et la réponse doit être marquée `public` et pas marquée `private`.
* Le `Pragma: no-cache` en-tête ne doit pas être présent si la `Cache-Control` en-tête n’est pas présent, comme le `Cache-Control` en-tête remplace le `Pragma` en-tête lorsqu’il est présent.
* Le `Set-Cookie` en-tête ne doit pas être présent.
* `Vary`paramètres de l’en-tête doivent être valide et non égal à `*`.
* Le `Content-Length` valeur d’en-tête (si défini) doit correspondre à la taille du corps de réponse.
* Le [IHttpSendFileFeature](/aspnet/core/api/microsoft.aspnetcore.http.features.ihttpsendfilefeature) n’est pas utilisé.
* La réponse ne doit pas être périmée tel que spécifié par le `Expires` en-tête et le `max-age` et `s-maxage` directives de cache.
* Mise en mémoire tampon de réponse doit être réussie et la taille de la réponse doit être inférieure à la configuration ou par défaut `SizeLimit`.
* La réponse doit être mise en cache en fonction de la [RFC 7234](https://tools.ietf.org/html/rfc7234) spécifications. Par exemple, le `no-store` directive ne doit pas exister dans les champs d’en-tête de demande ou de réponse. Consultez *Section 3 : le stockage des réponses dans les Caches* de [RFC 7234](https://tools.ietf.org/html/rfc7234) pour plus d’informations.

> [!NOTE]
> Définit des attaques par le système de côté pour la génération de jetons sécurisés pour empêcher la falsification de demande d’intersites (CSRF) le `Cache-Control` et `Pragma` en-têtes `no-cache` afin que les réponses ne sont pas mises en cache.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Démarrage d’une application](xref:fundamentals/startup)
* [Intergiciel (middleware)](xref:fundamentals/middleware)
* [Mise en cache en mémoire](xref:performance/caching/memory)
* [Utilisation d’un cache distribué](xref:performance/caching/distributed)
* [Détecter les modifications à l’aide de jetons de modification](xref:fundamentals/primitives/change-tokens)
* [Mise en cache des réponses](xref:performance/caching/response)
* [Tag Helper de cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)
* [Tag Helper de cache distribué](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)
