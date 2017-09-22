---
title: "Ouvrir l’Interface Web pour .NET (OWIN)"
author: ardalis
description: "Introduction à ouvrir l’Interface Web pour .NET (OWIN)."
keywords: ASP.NET Core, Interface Web ouverte pour .NET, OWIN
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 70c4e6bc-a773-4039-96ec-6fe557c9369d
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/owin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9edacb494c38d7812f9e3826ab9277cd1dffd675
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="introduction-to-open-web-interface-for-net-owin"></a><span data-ttu-id="d7065-104">Introduction à ouvrir l’Interface Web pour .NET (OWIN)</span><span class="sxs-lookup"><span data-stu-id="d7065-104">Introduction to Open Web Interface for .NET (OWIN)</span></span>

<span data-ttu-id="d7065-105">Par [Steve Smith](https://ardalis.com/) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d7065-105">By [Steve Smith](https://ardalis.com/) and  [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d7065-106">ASP.NET Core prend en charge l’Interface Web ouverte pour .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="d7065-106">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="d7065-107">OWIN permet des applications web à être dissocié de serveurs web.</span><span class="sxs-lookup"><span data-stu-id="d7065-107">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="d7065-108">Il définit une manière standard pour un intergiciel (middleware) à utiliser dans un pipeline pour gérer les demandes et réponses associées.</span><span class="sxs-lookup"><span data-stu-id="d7065-108">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="d7065-109">Intergiciel (middleware) et les applications ASP.NET Core peuvent interagir avec intergiciel (middleware), les serveurs et les applications basées sur OWIN.</span><span class="sxs-lookup"><span data-stu-id="d7065-109">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="d7065-110">OWIN fournit une couche de découplage qui permet à deux infrastructures avec les modèles d’objets disparates à être utilisés ensemble.</span><span class="sxs-lookup"><span data-stu-id="d7065-110">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="d7065-111">Le `Microsoft.AspNetCore.Owin` package fournit deux implémentations d’adaptateur :</span><span class="sxs-lookup"><span data-stu-id="d7065-111">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>
- <span data-ttu-id="d7065-112">ASP.NET Core pour OWIN</span><span class="sxs-lookup"><span data-stu-id="d7065-112">ASP.NET Core to OWIN</span></span> 
- <span data-ttu-id="d7065-113">OWIN à ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7065-113">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="d7065-114">Cela permet à ASP.NET Core être hébergée sur un serveur/hôte compatible OWIN, ou pour d’autres composants compatibles OWIN à exécuter sur ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d7065-114">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host, or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

<span data-ttu-id="d7065-115">Remarque : À l’aide de ces adaptateurs assortie d’une baisse des performances.</span><span class="sxs-lookup"><span data-stu-id="d7065-115">Note: Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="d7065-116">Les applications utilisant uniquement des composants ASP.NET Core ne devraient pas utiliser l’ou les adaptateurs Owin.</span><span class="sxs-lookup"><span data-stu-id="d7065-116">Applications using only ASP.NET Core components should not use the Owin package or adapters.</span></span>

[<span data-ttu-id="d7065-117">Afficher ou télécharger l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="d7065-117">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/owin/sample)

## <a name="running-owin-middleware-in-the-aspnet-pipeline"></a><span data-ttu-id="d7065-118">Intergiciel (middleware) OWIN en cours d’exécution dans le pipeline ASP.NET</span><span class="sxs-lookup"><span data-stu-id="d7065-118">Running OWIN middleware in the ASP.NET pipeline</span></span>

<span data-ttu-id="d7065-119">Prise en charge OWIN de ASP.NET Core est déployé dans le cadre de la `Microsoft.AspNetCore.Owin` package.</span><span class="sxs-lookup"><span data-stu-id="d7065-119">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="d7065-120">Vous pouvez importer la prise en charge OWIN dans votre projet en installant ce package.</span><span class="sxs-lookup"><span data-stu-id="d7065-120">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="d7065-121">Intergiciel (middleware) OWIN est conforme à la [spécification de OWIN](http://owin.org/spec/spec/owin-1.0.0.html), ce qui nécessite un `Func<IDictionary<string, object>, Task>` interface et clés spécifiques avec la valeur (tel que `owin.ResponseBody`).</span><span class="sxs-lookup"><span data-stu-id="d7065-121">OWIN middleware conforms to the [OWIN specification](http://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="d7065-122">L’intergiciel OWIN de simple suivante affiche « Hello World » :</span><span class="sxs-lookup"><span data-stu-id="d7065-122">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: http://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}


```

<span data-ttu-id="d7065-123">La signature de l’exemple retourne un `Task` et accepte un `IDictionary<string, object>` comme exigé par le OWIN.</span><span class="sxs-lookup"><span data-stu-id="d7065-123">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="d7065-124">Le code suivant montre comment ajouter la `OwinHello` intergiciel (middleware) (ci-dessus) pour le pipeline ASP.NET avec la `UseOwin` méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="d7065-124">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET pipeline with the `UseOwin` extension method.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {"linenostart": 1}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#", "source": "/Users/shirhatti/src/Docs/aspnet/fundamentals/owin/sample/src/OwinSample/Startup.cs"} -->

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}


```

<span data-ttu-id="d7065-125">Vous pouvez configurer d’autres actions pour avoir lieu dans le pipeline OWIN.</span><span class="sxs-lookup"><span data-stu-id="d7065-125">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="d7065-126">En-têtes de réponse doivent uniquement être modifiées avant la première écriture dans le flux de réponse.</span><span class="sxs-lookup"><span data-stu-id="d7065-126">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="d7065-127">Appels multiples à `UseOwin` est déconseillée pour des raisons de performances.</span><span class="sxs-lookup"><span data-stu-id="d7065-127">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="d7065-128">Composants OWIN fonctionnera mieux si regroupés.</span><span class="sxs-lookup"><span data-stu-id="d7065-128">OWIN components will operate best if grouped together.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        // do something before
        return OwinHello;
        // do something after
    });
});
```

<a name=hosting-on-owin></a>

## <a name="using-aspnet-hosting-on-an-owin-based-server"></a><span data-ttu-id="d7065-129">À l’aide d’hébergement ASP.NET sur un serveur OWIN</span><span class="sxs-lookup"><span data-stu-id="d7065-129">Using ASP.NET Hosting on an OWIN-based server</span></span>

<span data-ttu-id="d7065-130">Les serveurs OWIN peuvent héberger des applications ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="d7065-130">OWIN-based servers can host ASP.NET applications.</span></span> <span data-ttu-id="d7065-131">Un tel serveur est [Nowin](https://github.com/Bobris/Nowin), un serveur web de .NET OWIN.</span><span class="sxs-lookup"><span data-stu-id="d7065-131">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="d7065-132">Dans l’exemple de cet article, j’ai inclus un projet qui fait référence à Nowin et l’utilise pour créer un `IServer` capable d’auto-hébergement ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d7065-132">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[Main](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="d7065-133">`IServer`est une interface qui nécessite une `Features` propriété et un `Start` (méthode).</span><span class="sxs-lookup"><span data-stu-id="d7065-133">`IServer` is an interface that requires an `Features` property and a `Start` method.</span></span>

<span data-ttu-id="d7065-134">`Start`est chargé de configurer et démarrer le serveur, qui dans ce cas est effectué via une série d’appels d’API fluent que définir des adresses analysés à partir de la IServerAddressesFeature.</span><span class="sxs-lookup"><span data-stu-id="d7065-134">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="d7065-135">Notez que la configuration fluent de la `_builder` variable Spécifie que les demandes sont traitées par le `appFunc` définis précédemment dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="d7065-135">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="d7065-136">Cela `Func` est appelée sur chaque demande de traiter les demandes entrantes.</span><span class="sxs-lookup"><span data-stu-id="d7065-136">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="d7065-137">Nous allons également ajouter un `IWebHostBuilder` extension pour faciliter la tâche Ajouter et configurer le serveur Nowin.</span><span class="sxs-lookup"><span data-stu-id="d7065-137">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {"hl_lines": [11], "linenostart": 1}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#", "source": "/Users/shirhatti/src/Docs/aspnet/fundamentals/owin/sample/src/NowinSample/NowinWebHostBuilderExtensions.cs"} -->

```csharp
using System;
using Microsoft.AspNetCore.Hosting.Server;
using Microsoft.Extensions.DependencyInjection;
using Nowin;
using NowinSample;

namespace Microsoft.AspNetCore.Hosting
{
    public static class NowinWebHostBuilderExtensions
    {
        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder)
        {
            return builder.ConfigureServices(services =>
            {
                services.AddSingleton<IServer, NowinServer>();
            });
        }

        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder, Action<ServerBuilder> configure)
        {
            builder.ConfigureServices(services =>
            {
                services.Configure(configure);
            });
            return builder.UseNowin();
        }
    }
}
```

<span data-ttu-id="d7065-138">Tout cela en place, tout ce qui est requis pour exécuter une application ASP.NET à l’aide de ce serveur personnalisé pour appeler l’extension de *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d7065-138">With this in place, all that's required to run an ASP.NET application using this custom server to call the extension in *Program.cs*:</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {"hl_lines": [15], "linenostart": 1}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#", "source": "/Users/shirhatti/src/Docs/aspnet/fundamentals/owin/sample/src/NowinSample/Program.cs"} -->

```csharp

using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace NowinSample
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseNowin()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .Build();

            host.Run();
        }
    }
}

```

<span data-ttu-id="d7065-139">En savoir plus sur ASP.NET [serveurs](servers/index.md).</span><span class="sxs-lookup"><span data-stu-id="d7065-139">Learn more about ASP.NET [Servers](servers/index.md).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="d7065-140">Exécutez ASP.NET Core sur un serveur OWIN et utiliser sa prise en charge de WebSocket</span><span class="sxs-lookup"><span data-stu-id="d7065-140">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="d7065-141">Un autre exemple de fonctionnalités de serveurs basée sur les OWIN peut être exploité par ASP.NET Core est aux fonctionnalités telles que les WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d7065-141">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="d7065-142">Le serveur web .NET OWIN utilisé dans l’exemple précédent a prise en charge de WebSocket intégrées, qui peuvent être exploitées par une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d7065-142">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="d7065-143">L’exemple ci-dessous montre une application web simple qui prend en charge de WebSocket et renvoie tous les éléments envoyés au serveur via le protocole WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d7065-143">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {"hl_lines": [7, 9, 10], "linenostart": 1}, "backrefs": [], "dupnames": [], "linenos": true, "classes": [], "xml:space": "preserve", "language": "c#", "source": "/Users/shirhatti/src/Docs/aspnet/fundamentals/owin/sample/src/NowinWebSockets/Startup.cs"} -->

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

<span data-ttu-id="d7065-144">Cela [exemple](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/owin/sample) est configuré à l’aide de la même `NowinServer` que la précédente - la seule différence réside dans la configuration de l’application dans son `Configure` (méthode).</span><span class="sxs-lookup"><span data-stu-id="d7065-144">This [sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="d7065-145">Un test à l’aide de [un client websocket simple](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) montre l’application :</span><span class="sxs-lookup"><span data-stu-id="d7065-145">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![Client de Test Web Socket](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="d7065-147">Environnement OWIN</span><span class="sxs-lookup"><span data-stu-id="d7065-147">OWIN environment</span></span>

<span data-ttu-id="d7065-148">Vous pouvez construire un environnement OWIN à l’aide du `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="d7065-148">You can construct a OWIN environment using the `HttpContext`.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="d7065-149">Clés OWIN</span><span class="sxs-lookup"><span data-stu-id="d7065-149">OWIN keys</span></span>

<span data-ttu-id="d7065-150">Dépend de OWIN un `IDictionary<string,object>` objet pour communiquer des informations tout au long d’un échange de demande/réponse HTTP.</span><span class="sxs-lookup"><span data-stu-id="d7065-150">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="d7065-151">ASP.NET Core implémente les clés répertoriées ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="d7065-151">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="d7065-152">Consultez le [principales spécifications, les extensions](http://owin.org/#spec), et [OWIN clé instructions et des clés communes](http://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="d7065-152">See the [primary specification, extensions](http://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](http://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="d7065-153">Données de la demande (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="d7065-153">Request Data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="d7065-154">Touche</span><span class="sxs-lookup"><span data-stu-id="d7065-154">Key</span></span>               | <span data-ttu-id="d7065-155">Valeur (type)</span><span class="sxs-lookup"><span data-stu-id="d7065-155">Value (type)</span></span> | <span data-ttu-id="d7065-156">Description</span><span class="sxs-lookup"><span data-stu-id="d7065-156">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d7065-157">owin. RequestScheme</span><span class="sxs-lookup"><span data-stu-id="d7065-157">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="d7065-158">owin. RequestMethod</span><span class="sxs-lookup"><span data-stu-id="d7065-158">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="d7065-159">owin. RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="d7065-159">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="d7065-160">owin. RequestPath</span><span class="sxs-lookup"><span data-stu-id="d7065-160">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="d7065-161">owin. RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="d7065-161">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="d7065-162">owin. RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="d7065-162">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="d7065-163">owin. RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="d7065-163">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="d7065-164">owin. RequestBody</span><span class="sxs-lookup"><span data-stu-id="d7065-164">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="d7065-165">Données de la demande (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="d7065-165">Request Data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="d7065-166">Touche</span><span class="sxs-lookup"><span data-stu-id="d7065-166">Key</span></span>               | <span data-ttu-id="d7065-167">Valeur (type)</span><span class="sxs-lookup"><span data-stu-id="d7065-167">Value (type)</span></span> | <span data-ttu-id="d7065-168">Description</span><span class="sxs-lookup"><span data-stu-id="d7065-168">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d7065-169">owin. ID de requête</span><span class="sxs-lookup"><span data-stu-id="d7065-169">owin.RequestId</span></span> | `String` | <span data-ttu-id="d7065-170">Facultatif</span><span class="sxs-lookup"><span data-stu-id="d7065-170">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="d7065-171">Données de réponse (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="d7065-171">Response Data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="d7065-172">Touche</span><span class="sxs-lookup"><span data-stu-id="d7065-172">Key</span></span>               | <span data-ttu-id="d7065-173">Valeur (type)</span><span class="sxs-lookup"><span data-stu-id="d7065-173">Value (type)</span></span> | <span data-ttu-id="d7065-174">Description</span><span class="sxs-lookup"><span data-stu-id="d7065-174">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d7065-175">owin. ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="d7065-175">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="d7065-176">Facultatif</span><span class="sxs-lookup"><span data-stu-id="d7065-176">Optional</span></span> |
| <span data-ttu-id="d7065-177">owin. ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="d7065-177">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="d7065-178">Facultatif</span><span class="sxs-lookup"><span data-stu-id="d7065-178">Optional</span></span> |
| <span data-ttu-id="d7065-179">owin. ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="d7065-179">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="d7065-180">owin. ResponseBody</span><span class="sxs-lookup"><span data-stu-id="d7065-180">owin.ResponseBody</span></span> | `Stream`  | |


### <a name="other-data-owin-v100"></a><span data-ttu-id="d7065-181">Autres données (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="d7065-181">Other Data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="d7065-182">Touche</span><span class="sxs-lookup"><span data-stu-id="d7065-182">Key</span></span>               | <span data-ttu-id="d7065-183">Valeur (type)</span><span class="sxs-lookup"><span data-stu-id="d7065-183">Value (type)</span></span> | <span data-ttu-id="d7065-184">Description</span><span class="sxs-lookup"><span data-stu-id="d7065-184">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d7065-185">owin. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="d7065-185">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="d7065-186">owin. Version</span><span class="sxs-lookup"><span data-stu-id="d7065-186">owin.Version</span></span>  | `String` | |   


### <a name="common-keys"></a><span data-ttu-id="d7065-187">Clés communes</span><span class="sxs-lookup"><span data-stu-id="d7065-187">Common Keys</span></span>

| <span data-ttu-id="d7065-188">Touche</span><span class="sxs-lookup"><span data-stu-id="d7065-188">Key</span></span>               | <span data-ttu-id="d7065-189">Valeur (type)</span><span class="sxs-lookup"><span data-stu-id="d7065-189">Value (type)</span></span> | <span data-ttu-id="d7065-190">Description</span><span class="sxs-lookup"><span data-stu-id="d7065-190">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d7065-191">SSL. ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="d7065-191">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="d7065-192">SSL. LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="d7065-192">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="d7065-193">serveur. RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="d7065-193">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="d7065-194">serveur. Port_distant</span><span class="sxs-lookup"><span data-stu-id="d7065-194">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="d7065-195">serveur. LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="d7065-195">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="d7065-196">serveur. LocalPort</span><span class="sxs-lookup"><span data-stu-id="d7065-196">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="d7065-197">serveur. IsLocal</span><span class="sxs-lookup"><span data-stu-id="d7065-197">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="d7065-198">serveur. OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="d7065-198">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |


### <a name="sendfiles-v030"></a><span data-ttu-id="d7065-199">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="d7065-199">SendFiles v0.3.0</span></span>

| <span data-ttu-id="d7065-200">Touche</span><span class="sxs-lookup"><span data-stu-id="d7065-200">Key</span></span>               | <span data-ttu-id="d7065-201">Valeur (type)</span><span class="sxs-lookup"><span data-stu-id="d7065-201">Value (type)</span></span> | <span data-ttu-id="d7065-202">Description</span><span class="sxs-lookup"><span data-stu-id="d7065-202">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d7065-203">SendFile. SendAsync</span><span class="sxs-lookup"><span data-stu-id="d7065-203">sendfile.SendAsync</span></span> | <span data-ttu-id="d7065-204">Consultez [signature du délégué](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d7065-204">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="d7065-205">Par demande</span><span class="sxs-lookup"><span data-stu-id="d7065-205">Per Request</span></span> |


### <a name="opaque-v030"></a><span data-ttu-id="d7065-206">V0.3.0 opaque</span><span class="sxs-lookup"><span data-stu-id="d7065-206">Opaque v0.3.0</span></span>

| <span data-ttu-id="d7065-207">Touche</span><span class="sxs-lookup"><span data-stu-id="d7065-207">Key</span></span>               | <span data-ttu-id="d7065-208">Valeur (type)</span><span class="sxs-lookup"><span data-stu-id="d7065-208">Value (type)</span></span> | <span data-ttu-id="d7065-209">Description</span><span class="sxs-lookup"><span data-stu-id="d7065-209">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d7065-210">opaque. Version</span><span class="sxs-lookup"><span data-stu-id="d7065-210">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="d7065-211">opaque. Mise à niveau</span><span class="sxs-lookup"><span data-stu-id="d7065-211">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="d7065-212">Consultez [signature du délégué](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d7065-212">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="d7065-213">opaque. Flux de données</span><span class="sxs-lookup"><span data-stu-id="d7065-213">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="d7065-214">opaque. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="d7065-214">opaque.CallCancelled</span></span> | `CancellationToken` |  |


### <a name="websocket-v030"></a><span data-ttu-id="d7065-215">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="d7065-215">WebSocket v0.3.0</span></span>

| <span data-ttu-id="d7065-216">Touche</span><span class="sxs-lookup"><span data-stu-id="d7065-216">Key</span></span>               | <span data-ttu-id="d7065-217">Valeur (type)</span><span class="sxs-lookup"><span data-stu-id="d7065-217">Value (type)</span></span> | <span data-ttu-id="d7065-218">Description</span><span class="sxs-lookup"><span data-stu-id="d7065-218">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d7065-219">WebSocket. Version</span><span class="sxs-lookup"><span data-stu-id="d7065-219">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="d7065-220">WebSocket. Accepter</span><span class="sxs-lookup"><span data-stu-id="d7065-220">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="d7065-221">Consultez [signature du délégué](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d7065-221">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="d7065-222">WebSocket. AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="d7065-222">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="d7065-223">Non-spec</span><span class="sxs-lookup"><span data-stu-id="d7065-223">Non-spec</span></span> |
| <span data-ttu-id="d7065-224">WebSocket. Sous-protocole</span><span class="sxs-lookup"><span data-stu-id="d7065-224">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="d7065-225">Consultez [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) étape 5.5</span><span class="sxs-lookup"><span data-stu-id="d7065-225">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="d7065-226">WebSocket. SendAsync</span><span class="sxs-lookup"><span data-stu-id="d7065-226">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="d7065-227">Consultez [signature du délégué](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d7065-227">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="d7065-228">WebSocket. ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="d7065-228">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="d7065-229">Consultez [signature du délégué](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d7065-229">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="d7065-230">WebSocket. CloseAsync</span><span class="sxs-lookup"><span data-stu-id="d7065-230">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="d7065-231">Consultez [signature du délégué](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d7065-231">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="d7065-232">WebSocket. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="d7065-232">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="d7065-233">WebSocket. ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="d7065-233">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="d7065-234">Facultatif</span><span class="sxs-lookup"><span data-stu-id="d7065-234">Optional</span></span> |
| <span data-ttu-id="d7065-235">WebSocket. ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="d7065-235">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="d7065-236">Facultatif</span><span class="sxs-lookup"><span data-stu-id="d7065-236">Optional</span></span> |


## <a name="additional-resources"></a><span data-ttu-id="d7065-237">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d7065-237">Additional Resources</span></span>

* [<span data-ttu-id="d7065-238">Intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="d7065-238">Middleware</span></span>](middleware.md)

* [<span data-ttu-id="d7065-239">Serveurs</span><span class="sxs-lookup"><span data-stu-id="d7065-239">Servers</span></span>](servers/index.md)
