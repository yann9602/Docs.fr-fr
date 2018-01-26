---
title: OWIN (Open Web Interface pour .NET)
author: ardalis
description: "Découvrez comment ASP.NET Core prend en charge l’Interface Web ouverte pour .NET (OWIN), ce qui permet aux applications web à être dissocié de serveurs web."
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/owin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42ffa01745b7a492b3b8cb2778805f254863b890
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="introduction-to-open-web-interface-for-net-owin"></a>Introduction à ouvrir l’Interface Web pour .NET (OWIN)

Par [Steve Smith](https://ardalis.com/) et [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core prend en charge l’interface Open Web pour .NET (OWIN). OWIN permet que les applications web soient dissociées des serveurs web. Il définit une manière standard pour un intergiciel (middleware) à utiliser dans un pipeline pour gérer les demandes et réponses associées. Intergiciel (middleware) et les applications ASP.NET Core peuvent interagir avec intergiciel (middleware), les serveurs et les applications basées sur OWIN.

OWIN fournit une couche de découplage qui permet à deux infrastructures avec les modèles d’objets disparates à être utilisés ensemble. Le `Microsoft.AspNetCore.Owin` package fournit deux implémentations d’adaptateur :
- ASP.NET Core pour OWIN 
- OWIN à ASP.NET Core

Cela permet à ASP.NET Core être hébergée sur un serveur/hôte compatible OWIN, ou pour d’autres composants compatibles OWIN à exécuter sur ASP.NET Core.

Remarque : À l’aide de ces adaptateurs assortie d’une baisse des performances. Les applications utilisant uniquement des composants ASP.NET Core ne doivent pas utiliser l’ou les adaptateurs Owin.

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="running-owin-middleware-in-the-aspnet-pipeline"></a>Intergiciel (middleware) OWIN en cours d’exécution dans le pipeline ASP.NET

Prise en charge OWIN de ASP.NET Core est déployé dans le cadre de la `Microsoft.AspNetCore.Owin` package. Vous pouvez importer la prise en charge OWIN dans votre projet en installant ce package.

Intergiciel (middleware) OWIN est conforme à la [spécification de OWIN](http://owin.org/spec/spec/owin-1.0.0.html), ce qui nécessite un `Func<IDictionary<string, object>, Task>` interface et clés spécifiques avec la valeur (tel que `owin.ResponseBody`). L’intergiciel OWIN de simple suivante affiche « Hello World » :

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

La signature de l’exemple retourne un `Task` et accepte un `IDictionary<string, object>` comme exigé par le OWIN.

Le code suivant montre comment ajouter la `OwinHello` intergiciel (middleware) (ci-dessus) pour le pipeline ASP.NET avec la `UseOwin` méthode d’extension.

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

Vous pouvez configurer d’autres actions pour avoir lieu dans le pipeline OWIN.

> [!NOTE]
> En-têtes de réponse doivent uniquement être modifiées avant la première écriture dans le flux de réponse.

> [!NOTE]
> Appels multiples à `UseOwin` est déconseillée pour des raisons de performances. Composants OWIN fonctionnera mieux si regroupés.

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

<a name="hosting-on-owin"></a>

## <a name="using-aspnet-hosting-on-an-owin-based-server"></a>À l’aide d’hébergement ASP.NET sur un serveur OWIN

Les serveurs OWIN peuvent héberger des applications ASP.NET. Un tel serveur est [Nowin](https://github.com/Bobris/Nowin), un serveur web de .NET OWIN. Dans l’exemple de cet article, j’ai inclus un projet qui fait référence à Nowin et l’utilise pour créer un `IServer` capable d’auto-hébergement ASP.NET Core.

[!code-csharp[Main](owin/sample/src/NowinSample/Program.cs?highlight=15)]

`IServer`est une interface qui nécessite une `Features` propriété et un `Start` (méthode).

`Start`est chargé de configurer et démarrer le serveur, qui dans ce cas est effectué via une série d’appels d’API fluent que définir des adresses analysés à partir de la IServerAddressesFeature. Notez que la configuration fluent de la `_builder` variable Spécifie que les demandes sont traitées par le `appFunc` définis précédemment dans la méthode. Cela `Func` est appelée sur chaque demande de traiter les demandes entrantes.

Nous allons également ajouter un `IWebHostBuilder` extension pour faciliter la tâche Ajouter et configurer le serveur Nowin.

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

Tout cela en place, tout ce qui est requis pour exécuter une application ASP.NET à l’aide de ce serveur personnalisé pour appeler l’extension de *Program.cs*:

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

En savoir plus sur ASP.NET [serveurs](servers/index.md).

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a>Exécutez ASP.NET Core sur un serveur OWIN et utiliser sa prise en charge de WebSocket

Un autre exemple de fonctionnalités de serveurs basée sur les OWIN peut être exploité par ASP.NET Core est aux fonctionnalités telles que les WebSockets. Le serveur web .NET OWIN utilisé dans l’exemple précédent a prise en charge de WebSocket intégrées, qui peuvent être exploitées par une application ASP.NET Core. L’exemple ci-dessous montre une application web simple qui prend en charge de WebSocket et renvoie tous les éléments envoyés au serveur via le protocole WebSocket.

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

Cela [exemple](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/owin/sample) est configuré à l’aide de la même `NowinServer` que la précédente - la seule différence réside dans la configuration de l’application dans son `Configure` (méthode). Un test à l’aide de [un client websocket simple](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) montre l’application :

![Client de Test Web Socket](owin/_static/websocket-test.png)

## <a name="owin-environment"></a>Environnement OWIN

Vous pouvez construire un environnement OWIN à l’aide du `HttpContext`.

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a>Clés OWIN

Dépend de OWIN un `IDictionary<string,object>` objet pour communiquer des informations tout au long d’un échange de demande/réponse HTTP. ASP.NET Core implémente les clés répertoriées ci-dessous. Consultez le [principales spécifications, les extensions](http://owin.org/#spec), et [OWIN clé instructions et des clés communes](http://owin.org/spec/spec/CommonKeys.html).

### <a name="request-data-owin-v100"></a>Données de la demande (OWIN v1.0.0)

| Touche               | Valeur (type) | Description |
| ----------------- | ------------ | ----------- |
| owin.RequestScheme | `String` |  |
| owin.RequestMethod  | `String` | |    
| owin.RequestPathBase  | `String` | |    
| owin.RequestPath | `String` | |     
| owin.RequestQueryString  | `String` | |    
| owin.RequestProtocol  | `String` | |    
| owin.RequestHeaders | `IDictionary<string,string[]>`  | |
| owin.RequestBody | `Stream`  | |

### <a name="request-data-owin-v110"></a>Données de la demande (OWIN v1.1.0)

| Touche               | Valeur (type) | Description |
| ----------------- | ------------ | ----------- |
| owin.RequestId | `String` | Facultatif |

### <a name="response-data-owin-v100"></a>Données de réponse (OWIN v1.0.0)

| Touche               | Valeur (type) | Description |
| ----------------- | ------------ | ----------- |
| owin.ResponseStatusCode | `int` | Facultatif |
| owin.ResponseReasonPhrase | `String` | Facultatif |
| owin.ResponseHeaders | `IDictionary<string,string[]>`  | |
| owin.ResponseBody | `Stream`  | |


### <a name="other-data-owin-v100"></a>Autres données (OWIN v1.0.0)

| Touche               | Valeur (type) | Description |
| ----------------- | ------------ | ----------- |
| owin.CallCancelled | `CancellationToken` |  |
| owin.Version  | `String` | |   


### <a name="common-keys"></a>Clés communes

| Touche               | Valeur (type) | Description |
| ----------------- | ------------ | ----------- |
| ssl.ClientCertificate | `X509Certificate` |  |
| ssl.LoadClientCertAsync  | `Func<Task>` | |    
| server.RemoteIpAddress  | `String` | |    
| server.RemotePort | `String` | |     
| server.LocalIpAddress  | `String` | |    
| server.LocalPort  | `String` | |    
| server.IsLocal  | `bool` | |    
| server.OnSendingHeaders  | `Action<Action<object>,object>` | |


### <a name="sendfiles-v030"></a>SendFiles v0.3.0

| Touche               | Valeur (type) | Description |
| ----------------- | ------------ | ----------- |
| sendfile.SendAsync | Consultez [signature du délégué](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) | Par demande |


### <a name="opaque-v030"></a>V0.3.0 opaque

| Touche               | Valeur (type) | Description |
| ----------------- | ------------ | ----------- |
| opaque.Version | `String` |  |
| opaque.Upgrade | `OpaqueUpgrade` | Consultez [signature du délégué](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| opaque.Stream | `Stream` |  |
| opaque.CallCancelled | `CancellationToken` |  |


### <a name="websocket-v030"></a>WebSocket v0.3.0

| Touche               | Valeur (type) | Description |
| ----------------- | ------------ | ----------- |
| websocket.Version | `String` |  |
| websocket.Accept | `WebSocketAccept` | Consultez [signature du délégué](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| websocket.AcceptAlt |  | Non-spec |
| websocket.SubProtocol | `String` | Consultez [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) étape 5.5 |
| websocket.SendAsync | `WebSocketSendAsync` | Consultez [signature du délégué](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket.ReceiveAsync | `WebSocketReceiveAsync` | Consultez [signature du délégué](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket.CloseAsync | `WebSocketCloseAsync` | Consultez [signature du délégué](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket.CallCancelled | `CancellationToken` |  |
| websocket.ClientCloseStatus | `int` | Facultatif |
| websocket.ClientCloseDescription | `String` | Facultatif |


## <a name="additional-resources"></a>Ressources supplémentaires

* [Intergiciel (middleware)](middleware.md)

* [Serveurs](servers/index.md)
