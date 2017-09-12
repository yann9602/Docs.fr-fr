---
title: Prise en charge de WebSocket dans ASP.NET Core
author: tdykstra
description: "Quel est le protocole WebSocket prennent en charge ASP.NET Core et comment l’utiliser."
keywords: ASP.NET Core, WebSockets
ms.author: tdykstra
manager: wpickett
ms.date: 03/25/2017
ms.topic: article
ms.assetid: 0e0fedcd-a7b4-4479-8ae0-36eab0229d7e
ms.technology: aspnet
ms.prod: aspnet-core
uid: fundamentals/websockets
ms.openlocfilehash: a5914ad0d1056db993fcbfa1f00dd3422fcc4b6e
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="introduction-to-websockets-in-aspnet-core"></a><span data-ttu-id="d8e44-104">Introduction à WebSockets dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8e44-104">Introduction to WebSockets in ASP.NET Core</span></span>

<span data-ttu-id="d8e44-105">Par [Tom Dykstra](https://github.com/tdykstra) et [Andrew Stanton-infirmière](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="d8e44-105">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="d8e44-106">Cet article explique comment démarrer avec le protocole WebSocket dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8e44-106">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="d8e44-107">[WebSocket](https://wikipedia.org/wiki/WebSocket) est un protocole qui permet des canaux de communication persistant bidirectionnelle sur les connexions TCP.</span><span class="sxs-lookup"><span data-stu-id="d8e44-107">[WebSocket](https://wikipedia.org/wiki/WebSocket) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="d8e44-108">Il est utilisé pour les applications telles que la conversation, téléscripteurs, jeux, n’importe où vous souhaitez les fonctionnalités en temps réel dans une application web.</span><span class="sxs-lookup"><span data-stu-id="d8e44-108">It is used for applications such as chat, stock tickers, games, anywhere you want real-time functionality in a web application.</span></span>

<span data-ttu-id="d8e44-109">[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/sample).</span><span class="sxs-lookup"><span data-stu-id="d8e44-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/sample).</span></span> <span data-ttu-id="d8e44-110">Consultez le [étapes](#next-steps) section pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="d8e44-110">See the [Next Steps](#next-steps) section for more information.</span></span>


## <a name="prerequisites"></a><span data-ttu-id="d8e44-111">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="d8e44-111">Prerequisites</span></span>

* <span data-ttu-id="d8e44-112">ASP.NET Core 1.1 (ne s’exécute pas sur la version 1.0)</span><span class="sxs-lookup"><span data-stu-id="d8e44-112">ASP.NET Core 1.1 (does not run on 1.0)</span></span>
* <span data-ttu-id="d8e44-113">Un système d’exploitation ASP.NET Core s’exécute sur :</span><span class="sxs-lookup"><span data-stu-id="d8e44-113">Any OS that ASP.NET Core runs on:</span></span>
  
  * <span data-ttu-id="d8e44-114">Windows 7 / Windows Server 2008 et versions ultérieur</span><span class="sxs-lookup"><span data-stu-id="d8e44-114">Windows 7 / Windows Server 2008 and later</span></span>
  * <span data-ttu-id="d8e44-115">Linux</span><span class="sxs-lookup"><span data-stu-id="d8e44-115">Linux</span></span>
  * <span data-ttu-id="d8e44-116">MacOS</span><span class="sxs-lookup"><span data-stu-id="d8e44-116">macOS</span></span>

* <span data-ttu-id="d8e44-117">**Exception**: Si votre application s’exécute sur Windows avec IIS, ou avec WebListener, vous devez utiliser :</span><span class="sxs-lookup"><span data-stu-id="d8e44-117">**Exception**: If your app runs on Windows with IIS, or with WebListener, you must use:</span></span>

  * <span data-ttu-id="d8e44-118">Windows 8 / Windows Server 2012 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="d8e44-118">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="d8e44-119">IIS 8 / IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="d8e44-119">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="d8e44-120">WebSocket doit être activé dans IIS</span><span class="sxs-lookup"><span data-stu-id="d8e44-120">WebSocket must be enabled in IIS</span></span>

* <span data-ttu-id="d8e44-121">Pour les navigateurs pris en charge, consultez http://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="d8e44-121">For supported browsers, see http://caniuse.com/#feat=websockets.</span></span>

## <a name="when-to-use-it"></a><span data-ttu-id="d8e44-122">Quand l'utiliser</span><span class="sxs-lookup"><span data-stu-id="d8e44-122">When to use it</span></span>

<span data-ttu-id="d8e44-123">Utiliser des WebSockets lorsque vous avez besoin travailler directement avec une connexion de socket.</span><span class="sxs-lookup"><span data-stu-id="d8e44-123">Use WebSockets when you need to work directly with a socket connection.</span></span> <span data-ttu-id="d8e44-124">Par exemple, vous devrez peut-être les meilleures performances possibles pour un jeu en temps réel.</span><span class="sxs-lookup"><span data-stu-id="d8e44-124">For example, you might need the best possible performance for a real-time game.</span></span>

<span data-ttu-id="d8e44-125">[ASP.NET SignalR](https://docs.microsoft.com/aspnet/signalr/overview/getting-started/introduction-to-signalr) fournit un riche modèle d’application de la fonctionnalité en temps réel, mais il s’exécute uniquement sur ASP.NET, pas ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8e44-125">[ASP.NET SignalR](https://docs.microsoft.com/aspnet/signalr/overview/getting-started/introduction-to-signalr) provides a richer application model for real-time functionality, but it runs only on ASP.NET, not ASP.NET Core.</span></span> <span data-ttu-id="d8e44-126">Une version de base de SignalR est en cours de développement ; pour suivre sa progression, consultez le [référentiel GitHub pour SignalR Core](https://github.com/aspnet/SignalR).</span><span class="sxs-lookup"><span data-stu-id="d8e44-126">A Core version of SignalR is under development; to follow its progress, see the [GitHub repository for SignalR Core](https://github.com/aspnet/SignalR).</span></span>

<span data-ttu-id="d8e44-127">Si vous ne souhaitez pas attendre que SignalR Core, vous pouvez utiliser des WebSockets directement maintenant.</span><span class="sxs-lookup"><span data-stu-id="d8e44-127">If you don't want to wait for SignalR Core, you can use WebSockets directly now.</span></span> <span data-ttu-id="d8e44-128">Toutefois, vous devrez peut-être développer les fonctionnalités SignalR fournirait, telles que :</span><span class="sxs-lookup"><span data-stu-id="d8e44-128">But you might have to develop features that SignalR would provide, such as:</span></span>

* <span data-ttu-id="d8e44-129">Prise en charge pour un plus grand nombre de versions de navigateur à l’aide de secours automatique de méthodes de transport secondaire.</span><span class="sxs-lookup"><span data-stu-id="d8e44-129">Support for a broader range of browser versions by using automatic fallback to alternative transport methods.</span></span>
* <span data-ttu-id="d8e44-130">Reconnexion automatique lors de la connexion.</span><span class="sxs-lookup"><span data-stu-id="d8e44-130">Automatic reconnection when a connection drops.</span></span>
* <span data-ttu-id="d8e44-131">Prise en charge pour les clients de l’appel de méthodes sur le serveur, ou vice versa.</span><span class="sxs-lookup"><span data-stu-id="d8e44-131">Support for clients calling methods on the server or vice versa.</span></span>
* <span data-ttu-id="d8e44-132">Prise en charge pour la mise à l’échelle sur plusieurs serveurs.</span><span class="sxs-lookup"><span data-stu-id="d8e44-132">Support for scaling to multiple servers.</span></span>

## <a name="how-to-use-it"></a><span data-ttu-id="d8e44-133">Comment l’utiliser</span><span class="sxs-lookup"><span data-stu-id="d8e44-133">How to use it</span></span>

* <span data-ttu-id="d8e44-134">Installer le [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span><span class="sxs-lookup"><span data-stu-id="d8e44-134">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>
* <span data-ttu-id="d8e44-135">Configurer l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="d8e44-135">Configure the middleware.</span></span>
* <span data-ttu-id="d8e44-136">Accepter les demandes WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d8e44-136">Accept WebSocket requests.</span></span>
* <span data-ttu-id="d8e44-137">Envoyer et recevoir des messages.</span><span class="sxs-lookup"><span data-stu-id="d8e44-137">Send and receive messages.</span></span>

### <a name="configure-the-middleware"></a><span data-ttu-id="d8e44-138">Configurer l’intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="d8e44-138">Configure the middleware</span></span>

<span data-ttu-id="d8e44-139">Ajouter l’intergiciel (middleware) WebSockets dans le `Configure` méthode de la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="d8e44-139">Add the WebSockets middleware in the `Configure` method of the `Startup` class.</span></span>

[!code-csharp[](websockets/sample/Startup.cs?name=UseWebSockets)]

<span data-ttu-id="d8e44-140">Les paramètres suivants peuvent être configurés :</span><span class="sxs-lookup"><span data-stu-id="d8e44-140">The following settings can be configured:</span></span>

* <span data-ttu-id="d8e44-141">`KeepAliveInterval`-La fréquence à laquelle envoyer des trames de « ping » pour le client, pour vous assurer de proxys maintiennent ouverte la connexion.</span><span class="sxs-lookup"><span data-stu-id="d8e44-141">`KeepAliveInterval` - How frequently to send "ping" frames to the client, to ensure proxies keep the connection open.</span></span>
* <span data-ttu-id="d8e44-142">`ReceiveBufferSize`-La taille de la mémoire tampon utilisée pour recevoir des données.</span><span class="sxs-lookup"><span data-stu-id="d8e44-142">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="d8e44-143">Seuls les utilisateurs expérimentés devrez modifier ce paramètre, pour le réglage des performances basée sur la taille de leurs données.</span><span class="sxs-lookup"><span data-stu-id="d8e44-143">Only advanced users would need to change this, for performance tuning based on the size of their data.</span></span>

[!code-csharp[](websockets/sample/Startup.cs?name=UseWebSocketsOptions)]

### <a name="accept-websocket-requests"></a><span data-ttu-id="d8e44-144">Accepter les demandes de WebSocket</span><span class="sxs-lookup"><span data-stu-id="d8e44-144">Accept WebSocket requests</span></span>

<span data-ttu-id="d8e44-145">Plus tard dans le cycle de vie de demande (plus loin dans le `Configure` méthode ou dans une action MVC, par exemple) vérifier s’il est une demande WebSocket et accepter la demande WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d8e44-145">Somewhere later in the request life cycle (later in the `Configure` method or in an MVC action, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="d8e44-146">Cet exemple provient plus tard dans le `Configure` (méthode).</span><span class="sxs-lookup"><span data-stu-id="d8e44-146">This example is from later in the `Configure` method.</span></span>

[!code-csharp[](websockets/sample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="d8e44-147">Une demande WebSocket peut provenir sur n’importe quelle URL, mais cet exemple de code n’accepte que les demandes de `/ws`.</span><span class="sxs-lookup"><span data-stu-id="d8e44-147">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

### <a name="send-and-receive-messages"></a><span data-ttu-id="d8e44-148">Envoyer et recevoir des messages</span><span class="sxs-lookup"><span data-stu-id="d8e44-148">Send and receive messages</span></span>

<span data-ttu-id="d8e44-149">Le `AcceptWebSocketAsync` méthode met à niveau de la connexion TCP pour une connexion WebSocket et vous donne un [WebSocket](https://docs.microsoft.com/dotnet/core/api/system.net.websockets.websocket) objet.</span><span class="sxs-lookup"><span data-stu-id="d8e44-149">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and gives you a [WebSocket](https://docs.microsoft.com/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="d8e44-150">Utilisez l’objet WebSocket pour envoyer et recevoir des messages.</span><span class="sxs-lookup"><span data-stu-id="d8e44-150">Use the WebSocket object to send and receive messages.</span></span>

<span data-ttu-id="d8e44-151">Le code illustré précédemment et qui accepte la demande WebSocket passe le `WebSocket` de l’objet à un `Echo` méthode ; c' est ici le `Echo` (méthode).</span><span class="sxs-lookup"><span data-stu-id="d8e44-151">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method; here's the `Echo` method.</span></span> <span data-ttu-id="d8e44-152">Le code reçoit un message et envoie immédiatement le même message.</span><span class="sxs-lookup"><span data-stu-id="d8e44-152">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="d8e44-153">Il reste dans une boucle cela jusqu'à ce que le client ferme la connexion.</span><span class="sxs-lookup"><span data-stu-id="d8e44-153">It stays in a loop doing that until the client closes the connection.</span></span> 

[!code-csharp[](websockets/sample/Startup.cs?name=Echo)]

<span data-ttu-id="d8e44-154">Lorsque vous acceptez le WebSocket avant de commencer cette boucle, le pipeline de l’intergiciel (middleware) se termine.</span><span class="sxs-lookup"><span data-stu-id="d8e44-154">When you accept the WebSocket before beginning this loop, the middleware pipeline ends.</span></span>  <span data-ttu-id="d8e44-155">La fermeture du socket, le pipeline se déroule.</span><span class="sxs-lookup"><span data-stu-id="d8e44-155">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="d8e44-156">Autrement dit, l’arrêt de demande de déplacement vers l’avant dans le pipeline lorsque vous acceptez un WebSocket, tel qu’il serait lorsque vous atteignez une action MVC, par exemple.</span><span class="sxs-lookup"><span data-stu-id="d8e44-156">That is, the request stops moving forward in the pipeline when you accept a WebSocket, just as it would when you hit an MVC action, for example.</span></span>  <span data-ttu-id="d8e44-157">Mais lorsque vous terminez cette boucle et que vous fermez le socket, la demande se poursuit sauvegarder le pipeline.</span><span class="sxs-lookup"><span data-stu-id="d8e44-157">But when you finish this loop and close the socket, the request proceeds back up the pipeline.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d8e44-158">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="d8e44-158">Next steps</span></span>

<span data-ttu-id="d8e44-159">Le [exemple d’application](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/sample) qui accompagne cette article est une application simple écho.</span><span class="sxs-lookup"><span data-stu-id="d8e44-159">The [sample application](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/sample) that accompanies this article is a simple echo application.</span></span> <span data-ttu-id="d8e44-160">Il a une page web qui établit des connexions de WebSocket, et le serveur renvoie simplement au client les messages qu’il reçoit.</span><span class="sxs-lookup"><span data-stu-id="d8e44-160">It has a web page that makes WebSocket connections, and the server just resends back to the client any messages it receives.</span></span> <span data-ttu-id="d8e44-161">Exécuter à partir d’une invite de commandes (il n'a pas configuré pour exécuter à partir de Visual Studio avec IIS Express) et accédez à http://localhost : 5000.</span><span class="sxs-lookup"><span data-stu-id="d8e44-161">Run it from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="d8e44-162">La page web affiche l’état de la connexion en haut à gauche :</span><span class="sxs-lookup"><span data-stu-id="d8e44-162">The web page shows connection status at the upper left:</span></span>

![État initial de la page web](websockets/_static/start.png)

<span data-ttu-id="d8e44-164">Sélectionnez **connexion** pour envoyer une demande WebSocket à l’URL affichée.</span><span class="sxs-lookup"><span data-stu-id="d8e44-164">Select **Connect** to send a WebSocket request to the URL shown.</span></span>  <span data-ttu-id="d8e44-165">Entrez un message de test et sélectionnez **envoyer**.</span><span class="sxs-lookup"><span data-stu-id="d8e44-165">Enter a test message and select **Send**.</span></span> <span data-ttu-id="d8e44-166">Lorsque vous avez terminé, sélectionnez **Socket fermer**.</span><span class="sxs-lookup"><span data-stu-id="d8e44-166">When done, select **Close Socket**.</span></span> <span data-ttu-id="d8e44-167">Le **Communication journal** section signale chaque ouverture, d’envoi et l’action Fermer tel qu’il se produit.</span><span class="sxs-lookup"><span data-stu-id="d8e44-167">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![État initial de la page web](websockets/_static/end.png)
