---
title: Intergiciel (middleware) ASP.NET Core
author: rick-anderson
description: "Découvrez le middleware ASP.NET Core et le pipeline de requête."
manager: wpickett
ms.author: riande
ms.date: 01/22/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: fundamentals/middleware/index
ms.openlocfilehash: 887ba1a4742821226a7ebecfd238c97627d6c5f7
ms.sourcegitcommit: f2a11a89037471a77ad68a67533754b7bb8303e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="aspnet-core-middleware"></a>Intergiciel (middleware) ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://ardalis.com/)

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/index/sample) ([mode de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="what-is-middleware"></a>Qu’est-ce qu’un intergiciel (middleware) ?

Un intergiciel (middleware) est un logiciel qui est assemblé dans un pipeline d’application pour gérer les requêtes et les réponses. Chaque composant :

* Choisit de passer la requête au composant suivant dans le pipeline.
* Peut travailler avant et après l’appel du composant suivant dans le pipeline. 

Les délégués de requête sont utilisés pour créer le pipeline de requête. Les délégués de requête gèrent chaque requête HTTP.

Les délégués de requête sont configurés à l’aide des méthodes d’extension [Run](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions), [Map](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions) et [Use](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions). Chaque délégué de requête peut être spécifié in-line comme méthode anonyme (appelée middleware in-line) ou peut être défini dans une classe réutilisable. Ces classes réutilisables et les méthodes anonymes in-line sont des *intergiciels* ou des *composants d’intergiciel*. Chaque composant d’intergiciel du pipeline de requête est responsable de l’appel du composant suivant dans le pipeline ou du court-circuit de la chaîne, si nécessaire.

La rubrique [Migration des modules HTTP vers les intergiciels (middleware)](xref:migration/http-modules) explique la différence entre les pipelines de requête dans ASP.NET Core et ASP.NET 4.x, puis fournit d’autres exemples d’intergiciel.

## <a name="creating-a-middleware-pipeline-with-iapplicationbuilder"></a>Création d’un pipeline d’intergiciel avec IApplicationBuilder

Le pipeline de requête ASP.NET Core se compose d’une séquence de délégués de requête, appelés l’un après l’autre, comme le montre ce diagramme (le thread d’exécution suit les flèches noires) :

![Modèle de traitement des requêtes montrant une requête qui arrive et qui est traitée par trois intergiciels, puis la réponse qui quitte l’application. Chaque intergiciel exécute sa logique et transmet la requête à l’intergiciel suivant à l’instruction next(). Une fois que le troisième intergiciel a traité la requête, celle-ci repasse par les deux intergiciels précédents dans l’ordre inverse pour être à nouveau traitée après ses instructions next(), avant de quitter l’application sous forme de réponse au client.](index/_static/request-delegate-pipeline.png)

Chaque délégué peut effectuer des opérations avant et après le délégué suivant. Un délégué peut également décider de ne pas passer une requête au délégué suivant. On parle alors de court-circuit du pipeline de requête. Un court-circuit est souvent souhaitable car il évite le travail inutile. Par exemple, l’intergiciel de fichiers statiques peut retourner une requête pour un fichier statique et court-circuiter le reste du pipeline. Les délégués gérant les exceptions doivent être appelés tôt dans le pipeline pour qu’ils puissent intercepter les exceptions qui se produisent dans les phases ultérieures du pipeline.

L’application ASP.NET Core la plus simple possible définit un seul délégué de requête qui gère toutes les requêtes. Ce cas ne fait pas appel à un pipeline de requête réel. Une seule fonction anonyme est plutôt appelée en réponse à chaque requête HTTP.

[!code-csharp[Main](index/sample/Middleware/Startup.cs)]

Le premier délégué [app.Run](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions) termine le pipeline.

Vous pouvez chaîner plusieurs délégués de requête avec [app.Use](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions). Le paramètre `next` représente le délégué suivant dans le pipeline. (N’oubliez pas que vous pouvez court-circuiter le pipeline en n’appelant *pas* le paramètre *suivant*.) Vous pouvez généralement effectuer des actions avant et après le délégué suivant, comme illustré dans cet exemple :

[!code-csharp[Main](index/sample/Chain/Startup.cs?name=snippet1)]

>[!WARNING]
> N’appelez pas `next.Invoke` une fois que la réponse a été envoyée au client. Les changements apportés à `HttpResponse` après le démarrage de la réponse lèvent une exception. Par exemple, les changements comme la définition des en-têtes, le code d’état, etc., lèvent une exception. Écrire dans le corps de la réponse après avoir appelé `next` :
> - Peut entraîner une violation de protocole. Par exemple, écrire plus que le `content-length` indiqué
> - peut altérer le format du corps. Par exemple, l’écriture d’un pied de page HTML dans un fichier CSS.
>
> [HttpResponse.HasStarted](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.httpresponsefeature#Microsoft_AspNetCore_Http_Features_HttpResponseFeature_HasStarted) est un indice utile pour indiquer si les en-têtes ont été envoyés et/ou si le corps a fait l’objet d’écritures.

## <a name="ordering"></a>Ordre

L’ordre dans lequel les composants d’intergiciel sont ajoutés dans la méthode `Configure` définit l’ordre dans lequel ils sont appelés sur les requêtes, et l’ordre inverse pour la réponse. Cet ordre est critique pour la sécurité, les performances et le fonctionnement.

La méthode Configure (illustrée ci-dessous) ajoute les composants d’intergiciel suivants :

1. Gestion des erreurs/exceptions
2. Serveur de fichiers statiques
3. Authentification
4. MVC

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)


```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseExceptionHandler("/Home/Error"); // Call first to catch exceptions
                                            // thrown in the following middleware.

    app.UseStaticFiles();                   // Return static files and end pipeline.

    app.UseAuthentication();               // Authenticate before you access
                                           // secure resources.

    app.UseMvcWithDefaultRoute();          // Add MVC to the request pipeline.
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseExceptionHandler("/Home/Error"); // Call first to catch exceptions
                                            // thrown in the following middleware.

    app.UseStaticFiles();                   // Return static files and end pipeline.

    app.UseIdentity();                     // Authenticate before you access
                                           // secure resources.

    app.UseMvcWithDefaultRoute();          // Add MVC to the request pipeline.
}
```

-----------

Dans le code ci-dessus, `UseExceptionHandler` est le premier composant d’intergiciel ajouté au pipeline, donc il intercepte toutes les exceptions qui se produisent dans les appels ultérieurs.

L’intergiciel de fichiers statiques est appelé tôt dans le pipeline pour qu’il gère les requêtes et procède au court-circuit sans passer par les composants restants. L’intergiciel de fichiers statiques ne fournit **aucune** vérification d’autorisation. Tous les fichiers qu’il sert, notamment ceux sous *wwwroot* sont accessibles à tous. Consultez [Utilisation des fichiers statiques](xref:fundamentals/static-files) pour une approche permettant de sécuriser les fichiers statiques.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)


Si la requête n’est pas gérée par l’intergiciel de fichiers statiques, elle est transmise à l’intergiciel Identité (`app.UseAuthentication`), qui effectue l’authentification. L’Identité ne court-circuite pas les requêtes non authentifiées. Même si elle authentifie les requêtes, l’autorisation (et le refus) intervient uniquement après que MVC sélectionne une Page Razor/un contrôleur et une action spécifiques.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Si la requête n’est pas gérée par l’intergiciel de fichiers statiques, elle est transmise à l’intergiciel Identité (`app.UseIdentity`), qui effectue l’authentification. L’Identité ne court-circuite pas les requêtes non authentifiées. Même si elle authentifie les requêtes, l’autorisation (et le refus) intervient uniquement après que MVC sélectionne un contrôleur et une action spécifiques.

-----------

L’exemple suivant montre un ordre d’intergiciels où les requêtes pour les fichiers statiques sont gérées par l’intergiciel de fichiers statiques avant l’intergiciel de compression de la réponse. Les fichiers statiques ne sont pas compressés avec cet ordre d’intergiciels. Les réponses MVC de [UseMvcWithDefaultRoute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvcWithDefaultRoute_Microsoft_AspNetCore_Builder_IApplicationBuilder_) peuvent être compressées.

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseStaticFiles();         // Static files not compressed
                                  // by middleware.
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

<a name="middleware-run-map-use"></a>

### <a name="use-run-and-map"></a>Use, Run et Map

Vous configurez le pipeline HTTP avec `Use`, `Run` et `Map`. La méthode `Use` peut court-circuiter le pipeline (si elle n’appelle pas un délégué de requête `next`). `Run` est une convention, et certains composants d’intergiciel peuvent exposer des méthodes `Run[Middleware]` qui s’exécutent à la fin du pipeline.

Les extensions `Map*` sont utilisées comme convention pour créer une branche dans le pipeline. [Map](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions) crée une branche dans le pipeline de requête en fonction des correspondances du chemin de requête donné. Si le chemin de requête commence par le chemin donné, la branche est exécutée.

[!code-csharp[Main](index/sample/Chain/StartupMap.cs?name=snippet1)]

Le tableau suivant présente les requêtes et les réponses de `http://localhost:1234` avec le code précédent :

| Requête | Réponse |
| --- | --- |
| localhost:1234 | Hello from non-Map delegate.  |
| localhost:1234/map1 | Map Test 1 |
| localhost:1234/map2 | Map Test 2 |
| localhost:1234/map3 | Hello from non-Map delegate.  |

Quand `Map` est utilisé, le ou les segments de chemin correspondants sont supprimés de `HttpRequest.Path` et ajoutés à `HttpRequest.PathBase` pour chaque requête.

[MapWhen](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapwhenextensions) crée une branche dans le pipeline de requête en fonction du résultat du prédicat donné. Un prédicat de type `Func<HttpContext, bool>` peut être utilisé pour mapper les requêtes à une nouvelle branche du pipeline. Dans l’exemple suivant, un prédicat est utilisé pour détecter la présence d’une variable de chaîne de requête `branch` :

[!code-csharp[Main](index/sample/Chain/StartupMapWhen.cs?name=snippet1)]

Le tableau suivant présente les requêtes et les réponses de `http://localhost:1234` avec le code précédent :

| Requête | Réponse |
| --- | --- |
| localhost:1234 | Hello from non-Map delegate.  |
| localhost:1234/?branch=master | Branch used = master|

`Map` prend en charge l’imbrication, par exemple :

```csharp
app.Map("/level1", level1App => {
       level1App.Map("/level2a", level2AApp => {
           // "/level1/level2a"
           //...
       });
       level1App.Map("/level2b", level2BApp => {
           // "/level1/level2b"
           //...
       });
   });
   ```

`Map` peut également faire correspondre plusieurs segments à la fois, par exemple :

 ```csharp
app.Map("/level1/level2", HandleMultiSeg);
```

## <a name="built-in-middleware"></a>Intergiciels intégrés

ASP.NET Core est fourni avec les composants d’intergiciel suivants et une description de l’ordre dans lequel ils doivent être ajoutés :

| Intergiciel | Description | Ordre |
| ---------- | ----------- | ----- |
| [Authentification](xref:security/authentication/identity) | Prend en charge l’authentification. | Avant que `HttpContext.User` ne soit nécessaire. Terminal pour les rappels OAuth. |
| [CORS](xref:security/cors) | Configure le partage des ressources cross-origin (CORS). | Avant les composants qui utilisent CORS. |
| [Diagnostics](xref:fundamentals/error-handling) | Configure les diagnostics. | Avant les composants qui génèrent des erreurs. |
| [ForwardedHeaders/HttpOverrides](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions) | Transfère les en-têtes en proxy vers la requête actuelle. | Avant les composants qui consomment les champs mis à jour (exemples : schéma, hôte, IP client, méthode). |
| [Mise en cache des réponses](xref:performance/caching/middleware) | Prend en charge la mise en cache des réponses. | Avant les composants qui nécessitent la mise en cache. |
| [Compression des réponses](xref:performance/response-compression) | Prend en charge la compression des réponses. | Avant les composants qui nécessitent la compression. |
| [RequestLocalization](xref:fundamentals/localization) | Prend en charge la traduction (localisation). | Avant les composants susceptibles d’être traduits. |
| [Routage](xref:fundamentals/routing) | Définit et contraint les itinéraires de requête. | Terminal pour les itinéraires correspondants. |
| [Session](xref:fundamentals/app-state) | Prend en charge la gestion des sessions utilisateur. | Avant les composants qui nécessitent la session. |
| [Fichiers statiques](xref:fundamentals/static-files) | Prend en charge le traitement des fichiers statiques et l’exploration des répertoires. | Terminal si une requête correspond à des fichiers. |
| [Réécriture d’URL](xref:fundamentals/url-rewriting) | Prend en charge la réécriture d’URL et la redirection des requêtes. | Avant les composants qui consomment l’URL. |
| [WebSockets](xref:fundamentals/websockets) | Autorise le protocole WebSockets. | Avant les composants qui sont nécessaires pour accepter les requêtes WebSocket. |

<a name="middleware-writing-middleware"></a>

## <a name="writing-middleware"></a>Intergiciel d’écriture

Les intergiciels sont généralement encapsulés dans une classe et exposés avec une méthode d’extension. Prenez en compte l’intergiciel suivant, qui définit la culture de la requête actuelle à partir de la chaîne de requête :

[!code-csharp[Main](index/sample/Culture/StartupCulture.cs?name=snippet1)]

Remarque : L’exemple de code ci-dessus est utilisé pour illustrer la création d’un composant d’intergiciel. Consultez [Internationalisation et traduction](xref:fundamentals/localization) pour la prise en charge de la traduction intégrée d’ASP.NET Core.

Vous pouvez tester l’intergiciel en passant dans la culture, par exemple `http://localhost:7997/?culture=no`.

Le code suivant déplace le délégué de l’intergiciel dans une classe :

[!code-csharp[Main](index/sample/Culture/RequestCultureMiddleware.cs)]

La méthode d’extension suivante expose l’intergiciel via [IApplicationBuilder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder) :

[!code-csharp[Main](index/sample/Culture/RequestCultureMiddlewareExtensions.cs)]

Le code suivant appelle l’intergiciel à partir de `Configure` :

[!code-csharp[Main](index/sample/Culture/Startup.cs?name=snippet1&highlight=5)]

L’intergiciel doit suivre le [principe de dépendances explicites](http://deviq.com/explicit-dependencies-principle/) en exposant ses dépendances dans son constructeur. L’intergiciel est construit une fois par *durée de vie d’application*. Consultez *Dépendances par requête* ci-dessous si vous avez besoin de partager des services avec un intergiciel au sein d’une requête.

Les composants d’intergiciel peuvent résoudre leurs dépendances à partir de l’injection de dépendances via les paramètres du constructeur. [`UseMiddleware<T>`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.usemiddlewareextensions#methods_summary) peut également accepter des paramètres supplémentaires directement.

### <a name="per-request-dependencies"></a>Dépendances par requête

Étant donné que l’intergiciel est construit au démarrage de l’application, et non par requête, les services de durée de vie *délimités* utilisés par les constructeurs de l’intergiciel ne sont pas partagés avec d’autres types d’injection de dépendance lors de chaque requête. Si vous devez partager un service *délimité* entre votre intergiciel et d’autres types, ajoutez-le à la signature de la méthode `Invoke`. La méthode `Invoke` peut accepter des paramètres supplémentaires qui sont renseignés par injection de dépendances. Exemple :

```c#
public class MyMiddleware
{
    private readonly RequestDelegate _next;

    public MyMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Migration des modules HTTP vers les intergiciels (middleware)](xref:migration/http-modules)
* [Démarrage d’une application](xref:fundamentals/startup)
* [Fonctionnalités de requête](xref:fundamentals/request-features)
* [Activation d’intergiciel (middleware) basée sur une fabrique](xref:fundamentals/middleware/extensibility)
