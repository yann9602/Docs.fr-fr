---
title: Intergiciel (middleware) ASP.NET Core
author: rick-anderson
description: "En savoir plus sur ASP.NET Core intergiciel (middleware) et le pipeline de requête."
keywords: "ASP.NET Core, intergiciel (middleware), pipeline, délégué"
ms.author: riande
manager: wpickett
ms.date: 10/14/2017
ms.topic: article
ms.assetid: db9a86ab-46c2-40e0-baed-86e38c16af1f
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/middleware
ms.openlocfilehash: 730b4c281a766059b16ca1c36bbeb9611b979b72
ms.sourcegitcommit: 0f23400cae837e90927043aa0dfd6c31108a4e2c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2017
---
# <a name="aspnet-core-middleware-fundamentals"></a>Notions de base ASP.NET Core intergiciel (middleware)

<a name=fundamentals-middleware></a>

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://ardalis.com/)

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="what-is-middleware"></a>Nouveautés d’intergiciel (middleware)

Intergiciel (middleware) est un logiciel qui est intégré à un pipeline d’application pour gérer les demandes et réponses. Chaque composant :

* Peut décider de transmettre la demande au composant suivant dans le pipeline.
* Peut effectuer un travail avant et après que le composant suivant dans le pipeline est appelé. 

Les délégués de demande sont utilisés pour construire le pipeline de requête. Les délégués de la demande gérer chaque requête HTTP.

Demander les délégués sont configurés à l’aide de [exécuter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions), [carte](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions), et [utilisation](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions) les méthodes d’extension. Un délégué de demande individuelle peut être spécifié en ligne en tant qu’une méthode anonyme (appelée intergiciel (middleware) en ligne), ou elle peut être définie dans une classe réutilisable. Ces classes réutilisables et des méthodes anonymes d’en ligne sont *intergiciel (middleware)*, ou *composants d’intergiciel (middleware)*. Chaque composant d’intergiciel (middleware) dans le pipeline de demande est responsable de l’appel de composant suivant dans le pipeline ou la chaîne de court-circuit si nécessaire.

[Migration de Modules HTTP à un intergiciel (middleware)](../migration/http-modules.md) explique la différence entre la demande des pipelines dans ASP.NET Core et les versions précédentes et fournit d’autres exemples d’intergiciel (middleware).

## <a name="creating-a-middleware-pipeline-with-iapplicationbuilder"></a>Création d’un pipeline de l’intergiciel (middleware) avec IApplicationBuilder

Le pipeline de demande ASP.NET Core se compose d’une séquence de délégués de la demande, appelé une après l’autre, comme le montre ce diagramme (le thread d’exécution suit les flèches noires) :

![Modèle de traitement de requête montrant une demande qui arrivent, via trois middlewares et la réponse en laissant l’application de traitement. Chaque intergiciel (middleware) s’exécute sa logique et transmet la demande de l’intergiciel (middleware) suivant à l’instruction next(). Une fois que l’intergiciel (middleware) tiers traite la demande, il est main par le biais des précédentes deux middlewares pour le traitement supplémentaire après les instructions de next() à son tour, avant de quitter l’application en réponse au client.](middleware/_static/request-delegate-pipeline.png)

Chaque délégué peut effectuer des opérations avant et après le délégué suivant. Un délégué peut également décider de ne pas transmettre une demande pour le délégué suivant, qui est appelé le pipeline de demande de court-circuit. Court-circuit est souvent souhaitable car elle évite le travail inutile. Par exemple, l’intergiciel (middleware) de fichiers statiques peut retourner une demande pour un fichier statique et le reste du pipeline de court-circuit. Gestion des exceptions délégués doivent être appelés tôt dans le pipeline, afin qu’ils peuvent intercepter les exceptions qui se produisent dans les phases ultérieures du pipeline.

L’application de ASP.NET Core la plus simple possible définit un délégué de requête unique qui gère toutes les demandes. Ce cas n’inclut pas un pipeline de demande réelle. Au lieu de cela, une fonction anonyme unique est appelée en réponse à chaque requête HTTP.

[!code-csharp[Main](middleware/sample/Middleware/Startup.cs)]

Le premier [application. Exécutez](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions) délégué termine le pipeline.

Vous pouvez chaîner plusieurs délégués demande avec [application. Utilisez](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions). Le `next` paramètre représente le délégué suivant dans le pipeline. (Souvenez-vous que vous pouvez court-circuit le pipeline par *pas* appelant le *suivant* paramètre.) Vous pouvez généralement effectuer actions avant et après le délégué suivant, comme illustré dans cet exemple :

[!code-csharp[Main](middleware/sample/Chain/Startup.cs?name=snippet1)]

>[!WARNING]
> N’appelez pas `next.Invoke` une fois que la réponse a été envoyée au client. Modifications apportées à `HttpResponse` après le démarrage de la réponse lève une exception. Par exemple, les modifications telles que la définition des en-têtes, code d’état, etc., lève une exception. Écrire dans le corps de réponse après avoir appelé `next`:
> - Peut entraîner une violation du protocole. Par exemple, l’écriture de plus de la manière indiquée `content-length`.
> - Peut altérer le format du corps. Par exemple, l’écriture d’un pied de page HTML dans un fichier CSS.
>
> [HttpResponse.HasStarted](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.httpresponsefeature#Microsoft_AspNetCore_Http_Features_HttpResponseFeature_HasStarted) est un indicateur utile pour indiquer si les en-têtes ont été envoyés et/ou le corps a été écrit.

## <a name="ordering"></a>Classement

Composants d’intergiciel (middleware) sont ajoutés dans l’ordre de la `Configure` méthode définit l’ordre dans lequel ils sont appelés sur les demandes et l’ordre inverse pour la réponse. Ce classement est critique de sécurité, performances et fonctionnalités.

La méthode de configuration (voir ci-dessous) ajoute les composants d’intergiciel (middleware) suivant :

1. Gestion d’erreur d’exception
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

Dans le code ci-dessus, `UseExceptionHandler` est le premier composant d’intergiciel (middleware) ajouté au pipeline, par conséquent, elle intercepte toutes les exceptions qui se produisent dans les appels ultérieurs.

L’intergiciel (middleware) fichier statique est appelée très tôt dans le pipeline afin de gérer les demandes et de court-circuit sans passer par les autres composants. Fournit de l’intergiciel (middleware) de fichiers statiques **aucune** vérifications d’autorisation. Tous les fichiers pris en charge par, notamment ceux de *wwwroot*, sont accessibles. Consultez [utilisation des fichiers statiques](xref:fundamentals/static-files) pour une approche de sécuriser les fichiers statiques.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)


Si la demande n’est pas gérée par l’intergiciel (middleware) fichier statique, il est transmis à l’intergiciel (middleware) identité (`app.UseAuthentication`), qui effectue l’authentification. Identité n’effectue pas de court-circuit les demandes non authentifiées. Bien que l’identité authentifie les requêtes, d’autorisation (et rejet) se produit uniquement après que MVC sélectionne une Page Razor ou un contrôleur et une action spécifique.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Si la demande n’est pas gérée par l’intergiciel (middleware) fichier statique, il est transmis à l’intergiciel (middleware) identité (`app.UseIdentity`), qui effectue l’authentification. Identité n’effectue pas de court-circuit les demandes non authentifiées. Bien que l’identité authentifie les requêtes, d’autorisation (et rejet) se produit uniquement après que MVC sélectionne un contrôleur spécifique et une action.

-----------

L’exemple suivant montre un intergiciel (middleware) classement où les demandes de fichiers statiques sont gérées par l’intergiciel (middleware) de fichiers statiques avant l’intergiciel de compression de la réponse. Fichiers statiques ne sont pas compressés avec ce classement de l’intergiciel (middleware). Les réponses MVC de [UseMvcWithDefaultRoute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvcWithDefaultRoute_Microsoft_AspNetCore_Builder_IApplicationBuilder_) peuvent être compressées.

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseStaticFiles();         // Static files not compressed
                                  // by middleware.
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

<a name=middleware-run-map-use></a>

### <a name="use-run-and-map"></a>Utilisez, exécuter et carte

Vous configurez le pipeline HTTP à l’aide `Use`, `Run`, et `Map`. Le `Use` méthode pouvez court-circuit le pipeline (autrement dit, si elle n’appelle pas une `next` délégué de la demande). `Run`est une convention, et certains composants d’intergiciel (middleware) peuvent exposer `Run[Middleware]` méthodes qui s’exécutent à la fin du pipeline.

`Map*`les extensions sont utilisées comme une convention pour créer une branche le pipeline. [Carte](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions) branches le pipeline de requête en fonction des correspondances de chemin de la demande donnée. Si le chemin d’accès de requête commence par le chemin d’accès donné, la branche est exécutée.

[!code-csharp[Main](middleware/sample/Chain/StartupMap.cs?name=snippet1)]

Le tableau suivant présente les demandes et réponses de `http://localhost:1234` en utilisant le code précédent :

| Requête | Réponse |
| --- | --- |
| localhost:1234 | Bonjour à partir de la table non délégué.  |
| localhost:1234 / map1 | Test de mappage 1 |
| localhost:1234 / map2 | Test de mappage 2 |
| localhost:1234 / map3 | Bonjour à partir de la table non délégué.  |

Lorsque `Map` est utilisé, les segments de chemin d’accès de mise en correspondance sont supprimés de `HttpRequest.Path` et ajouté à `HttpRequest.PathBase` pour chaque demande.

[MapWhen](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapwhenextensions) branches le pipeline de requête en fonction du résultat du prédicat donné. Un prédicat de type `Func<HttpContext, bool>` peut être utilisé pour mapper les requêtes avec une nouvelle branche du pipeline. Dans l’exemple suivant, un prédicat est utilisé pour détecter la présence d’une variable de chaîne de requête `branch`:

[!code-csharp[Main](middleware/sample/Chain/StartupMapWhen.cs?name=snippet1)]

Le tableau suivant présente les demandes et réponses de `http://localhost:1234` en utilisant le code précédent :

| Requête | Réponse |
| --- | --- |
| localhost:1234 | Bonjour à partir de la table non délégué.  |
| localhost:1234 / ? branche = principale | Branche utilisé = principale|

`Map`prend en charge l’imbrication, par exemple :

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

`Map`peut également correspondre à plusieurs segments à la fois, par exemple :

 ```csharp
app.Map("/level1/level2", HandleMultiSeg);
```

## <a name="built-in-middleware"></a>Intergiciel (middleware) intégré

ASP.NET Core est fourni avec les composants d’intergiciel (middleware) suivant :

| Intergiciel (middleware) | Description |
| ----- | ------- |
| [Authentification](xref:security/authentication/identity) | Fournit la prise en charge de l’authentification. |
| [CORS](xref:security/cors) | Configure le partage de ressources Cross-Origin. |
| [Mise en cache des réponses](xref:performance/caching/middleware) | Prend en charge la mise en cache des réponses. |
| [Compression de la réponse](xref:performance/response-compression) | Prend en charge la compression des réponses. |
| [Routage](xref:fundamentals/routing) | Définit et contraint les itinéraires de la demande. |
| [Session](xref:fundamentals/app-state) | Prend en charge la gestion des sessions utilisateur. |
| [Fichiers statiques](xref:fundamentals/static-files) | Fournit la prise en charge pour traiter les fichiers statiques et l’exploration des répertoires. |
| [Intergiciel de réécriture d’URL](xref:fundamentals/url-rewriting) | Prend en charge la réécriture d’URL et la redirection des demandes. |

<a name=middleware-writing-middleware></a>

## <a name="writing-middleware"></a>Intergiciel (middleware) de l’écriture

Intergiciel (middleware) est généralement encapsulé dans une classe et exposée avec une méthode d’extension. Prenez en compte l’intergiciel (middleware) suivant, qui définit la culture pour la requête actuelle à partir de la chaîne de requête :

[!code-csharp[Main](middleware/sample/Culture/StartupCulture.cs?name=snippet1)]

Remarque : L’exemple de code ci-dessus est utilisé pour illustrer la création d’un composant d’intergiciel (middleware). Consultez [ globalisation et localisation](xref:fundamentals/localization) pour la prise en charge de la localisation intégré d’ASP.NET Core.

Vous pouvez tester l’intergiciel (middleware) en passant dans la culture, par exemple `http://localhost:7997/?culture=no`.

Le code suivant déplace le délégué de l’intergiciel (middleware) à une classe :

[!code-csharp[Main](middleware/sample/Culture/RequestCultureMiddleware.cs)]

La méthode d’extension suivant expose l’intergiciel (middleware) via [IApplicationBuilder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder):

[!code-csharp[Main](middleware/sample/Culture/RequestCultureMiddlewareExtensions.cs)]

Le code suivant appelle l’intergiciel (middleware) à partir de `Configure`:

[!code-csharp[Main](middleware/sample/Culture/Startup.cs?name=snippet1&highlight=5)]

Intergiciel (middleware) doit suivre le [principe de dépendances explicites](http://deviq.com/explicit-dependencies-principle/) en exposant ses dépendances dans son constructeur. Intergiciel (middleware) est construit en une seule fois par *durée de vie application*. Consultez *dépendances par demande* ci-dessous si vous avez besoin de partager des services avec un intergiciel (middleware) au sein d’une requête.

Composants d’intergiciel (middleware) peuvent résoudre leurs dépendances à partir de l’injection de dépendances via les paramètres du constructeur. [`UseMiddleware<T>`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.usemiddlewareextensions#methods_summary)peut également accepter des paramètres supplémentaires directement.

### <a name="per-request-dependencies"></a>Dépendances par demande

Étant donné que l’intergiciel (middleware) est créée au démarrage de l’application, pas à la demande, *étendue* les services de durée de vie utilisés par les constructeurs de l’intergiciel (middleware) ne sont pas partagés avec d’autres types d’injection de dépendance lors de chaque demande. Si vous devez partager une *étendue* entre votre intergiciel (middleware) et d’autres types de service, ajoutez ces services pour le `Invoke` signature de la méthode. Le `Invoke` méthode peut accepter des paramètres supplémentaires sont remplies par injection de dépendances. Exemple :

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

## <a name="resources"></a>Ressources

* [Exemple de code utilisé dans ce document.](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample)
* [Migration des modules HTTP vers les intergiciels (middleware)](../migration/http-modules.md)
* [Démarrage d’une application](startup.md)
* [Fonctionnalités de requête](request-features.md)
