---
title: "Utilisation avec un Cache distribué"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: 870f082d-6d43-453d-b311-45f3aeb4d2c5
ms.technology: aspnet
ms.prod: asp.net-core
uid: performance/caching/distributed
ms.openlocfilehash: abf680fef9de175082c1e4f4cebc2b9648f18a28
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="working-with-a-distributed-cache"></a><span data-ttu-id="73f51-103">Utilisation avec un cache distribué</span><span class="sxs-lookup"><span data-stu-id="73f51-103">Working with a distributed cache</span></span>

<span data-ttu-id="73f51-104">Par [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="73f51-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="73f51-105">Les caches distribués peuvent améliorer les performances et l’évolutivité des applications ASP.NET Core, en particulier lorsque hébergée dans un environnement de batterie de serveurs cloud ou de serveur.</span><span class="sxs-lookup"><span data-stu-id="73f51-105">Distributed caches can improve the performance and scalability of ASP.NET Core apps, especially when hosted in a cloud or server farm environment.</span></span> <span data-ttu-id="73f51-106">Cet article explique comment travailler avec les implémentations et les abstractions de cache distribué intégrée d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="73f51-106">This article explains how to work with ASP.NET Core's built-in distributed cache abstractions and implementations.</span></span>

[<span data-ttu-id="73f51-107">Afficher ou télécharger l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="73f51-107">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/distributed/sample)

## <a name="what-is-a-distributed-cache"></a><span data-ttu-id="73f51-108">Qu’est un Cache distribué</span><span class="sxs-lookup"><span data-stu-id="73f51-108">What is a Distributed Cache</span></span>

<span data-ttu-id="73f51-109">Un cache distribué est partagé par plusieurs serveurs d’application (consultez [principes fondamentaux de la mise en cache](memory.md#caching-basics)).</span><span class="sxs-lookup"><span data-stu-id="73f51-109">A distributed cache is shared by multiple app servers (see [Caching Basics](memory.md#caching-basics)).</span></span> <span data-ttu-id="73f51-110">Les informations contenues dans le cache ne sont pas stockés dans la mémoire des serveurs de site web individuel, et les données mises en cache sont disponibles pour tous les serveurs de l’application.</span><span class="sxs-lookup"><span data-stu-id="73f51-110">The information in the cache is not stored in the memory of individual web servers, and the cached data is available to all of the app's servers.</span></span> <span data-ttu-id="73f51-111">Cela présente plusieurs avantages :</span><span class="sxs-lookup"><span data-stu-id="73f51-111">This provides several advantages:</span></span>

1. <span data-ttu-id="73f51-112">Données mises en cache sont cohérentes sur tous les serveurs web.</span><span class="sxs-lookup"><span data-stu-id="73f51-112">Cached data is coherent on all web servers.</span></span> <span data-ttu-id="73f51-113">Les utilisateurs ne voient des résultats différents selon le web server gère leur demande</span><span class="sxs-lookup"><span data-stu-id="73f51-113">Users don't see different results depending on which web server handles their request</span></span>

2. <span data-ttu-id="73f51-114">Données mises en cache survivent redémarrage du serveur web et les déploiements.</span><span class="sxs-lookup"><span data-stu-id="73f51-114">Cached data survives web server restarts and deployments.</span></span> <span data-ttu-id="73f51-115">Serveurs web individuels peuvent être supprimés ou ajoutés sans impact sur le cache.</span><span class="sxs-lookup"><span data-stu-id="73f51-115">Individual web servers can be removed or added without impacting the cache.</span></span>

3. <span data-ttu-id="73f51-116">Le magasin de données source a moins de demandes effectuées (de plusieurs caches en mémoire ou de non mise en cache du tout).</span><span class="sxs-lookup"><span data-stu-id="73f51-116">The source data store has fewer requests made to it (than with multiple in-memory caches or no cache at all).</span></span>

> [!NOTE]
> <span data-ttu-id="73f51-117">Si vous utilisez un Cache distribué de SQL Server, certains de ces avantages ne sont trues si une instance distincte de la base de données est utilisée pour le cache que pour les données de source de l’application.</span><span class="sxs-lookup"><span data-stu-id="73f51-117">If using a SQL Server Distributed Cache, some of these advantages are only true if a separate database instance is used for the cache than for the app's source data.</span></span>

<span data-ttu-id="73f51-118">Comme n’importe quel type de cache, un cache distribué peut considérablement améliorer la réactivité d’une application, car en général, les données peuvent être récupérées à partir du cache beaucoup plus rapide qu’à partir d’une base de données relationnelle (ou un service web).</span><span class="sxs-lookup"><span data-stu-id="73f51-118">Like any cache, a distributed cache can dramatically improve an app's responsiveness, since typically data can be retrieved from the cache much faster than from a relational database (or web service).</span></span>

<span data-ttu-id="73f51-119">Configuration du cache est spécifique à l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="73f51-119">Cache configuration is implementation specific.</span></span> <span data-ttu-id="73f51-120">Cet article décrit comment configurer les deux Redis et distribuées SQL Server met en cache.</span><span class="sxs-lookup"><span data-stu-id="73f51-120">This article describes how to configure both Redis and SQL Server distributed caches.</span></span> <span data-ttu-id="73f51-121">Indépendamment de l’implémentation est sélectionnée, l’application interagit avec le cache à l’aide d’un commun `IDistributedCache` interface.</span><span class="sxs-lookup"><span data-stu-id="73f51-121">Regardless of which implementation is selected, the app interacts with the cache using a common `IDistributedCache` interface.</span></span>

## <a name="the-idistributedcache-interface"></a><span data-ttu-id="73f51-122">L’Interface IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="73f51-122">The IDistributedCache Interface</span></span>

<span data-ttu-id="73f51-123">Le `IDistributedCache` interface inclut des méthodes synchrones et asynchrones.</span><span class="sxs-lookup"><span data-stu-id="73f51-123">The `IDistributedCache` interface includes synchronous and asynchronous methods.</span></span> <span data-ttu-id="73f51-124">L’interface autorise des éléments ajoutés, la récupération et la supprimer de l’implémentation de cache distribué.</span><span class="sxs-lookup"><span data-stu-id="73f51-124">The interface allows items to be added, retrieved, and removed from the distributed cache implementation.</span></span> <span data-ttu-id="73f51-125">Le `IDistributedCache` interface comprend les méthodes suivantes :</span><span class="sxs-lookup"><span data-stu-id="73f51-125">The `IDistributedCache` interface includes the following methods:</span></span>

<span data-ttu-id="73f51-126">**Get, GetAsync**</span><span class="sxs-lookup"><span data-stu-id="73f51-126">**Get, GetAsync**</span></span>

<span data-ttu-id="73f51-127">Prend une clé de chaîne et récupère un élément mis en cache comme un `byte[]` si trouvée dans le cache.</span><span class="sxs-lookup"><span data-stu-id="73f51-127">Takes a string key and retrieves a cached item as a `byte[]` if found in the cache.</span></span>

<span data-ttu-id="73f51-128">**Ensemble, SetAsync**</span><span class="sxs-lookup"><span data-stu-id="73f51-128">**Set, SetAsync**</span></span>

<span data-ttu-id="73f51-129">Ajoute un élément (comme `byte[]`) dans le cache à l’aide d’une clé de chaîne.</span><span class="sxs-lookup"><span data-stu-id="73f51-129">Adds an item (as `byte[]`) to the cache using a string key.</span></span>

<span data-ttu-id="73f51-130">**Actualisation, RefreshAsync**</span><span class="sxs-lookup"><span data-stu-id="73f51-130">**Refresh, RefreshAsync**</span></span>

<span data-ttu-id="73f51-131">Actualise un élément dans le cache en fonction de sa clé, la réinitialisation de son délai d’expiration décalée (le cas échéant).</span><span class="sxs-lookup"><span data-stu-id="73f51-131">Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>

<span data-ttu-id="73f51-132">**Supprimer, RemoveAsync**</span><span class="sxs-lookup"><span data-stu-id="73f51-132">**Remove, RemoveAsync**</span></span>

<span data-ttu-id="73f51-133">Supprime une entrée de cache en fonction de sa clé.</span><span class="sxs-lookup"><span data-stu-id="73f51-133">Removes a cache entry based on its key.</span></span>

<span data-ttu-id="73f51-134">Pour utiliser le `IDistributedCache` interface :</span><span class="sxs-lookup"><span data-stu-id="73f51-134">To use the `IDistributedCache` interface:</span></span>

   1. <span data-ttu-id="73f51-135">Ajouter les packages NuGet nécessaires à votre fichier projet.</span><span class="sxs-lookup"><span data-stu-id="73f51-135">Add the required NuGet packages to your project file.</span></span>

   2. <span data-ttu-id="73f51-136">Configurer l’implémentation spécifique de `IDistributedCache` dans votre `Startup` la classe `ConfigureServices` (méthode) et l’ajouter au conteneur il.</span><span class="sxs-lookup"><span data-stu-id="73f51-136">Configure the specific implementation of `IDistributedCache` in your `Startup` class's `ConfigureServices` method, and add it to the container there.</span></span>

   3. <span data-ttu-id="73f51-137">À partir de l’application [`Middleware](../../fundamentals/middleware.md) or MVC controller classes, request an instance of `IDistributedCache' à partir du constructeur.</span><span class="sxs-lookup"><span data-stu-id="73f51-137">From the app's [`Middleware](../../fundamentals/middleware.md) or MVC controller classes, request an instance of `IDistributedCache\` from the constructor.</span></span> <span data-ttu-id="73f51-138">L’instance est assurée par le [Injection de dépendance](../../fundamentals/dependency-injection.md) (DI).</span><span class="sxs-lookup"><span data-stu-id="73f51-138">The instance will be provided by [Dependency Injection](../../fundamentals/dependency-injection.md) (DI).</span></span>

> [!NOTE]
> <span data-ttu-id="73f51-139">Il est inutile d’utiliser une durée de vie Singleton ou inclus dans l’étendue pour `IDistributedCache` instances (au moins pour les implémentations intégrées).</span><span class="sxs-lookup"><span data-stu-id="73f51-139">There is no need to use a Singleton or Scoped lifetime for `IDistributedCache` instances (at least for the built-in implementations).</span></span> <span data-ttu-id="73f51-140">Vous pouvez également créer une instance de chaque fois que vous devrez peut-être une (au lieu d’utiliser [Injection de dépendance](../../fundamentals/dependency-injection.md)), mais cela peut rendre votre code plus difficile à tester et ne respecte pas la [principe de dépendances explicites](http://deviq.com/explicit-dependencies-principle/).</span><span class="sxs-lookup"><span data-stu-id="73f51-140">You can also create an instance wherever you might need one (instead of using [Dependency Injection](../../fundamentals/dependency-injection.md)), but this can make your code harder to test, and violates the [Explicit Dependencies Principle](http://deviq.com/explicit-dependencies-principle/).</span></span>

<span data-ttu-id="73f51-141">L’exemple suivant montre comment utiliser une instance de `IDistributedCache` dans un composant d’intergiciel (middleware) simple :</span><span class="sxs-lookup"><span data-stu-id="73f51-141">The following example shows how to use an instance of `IDistributedCache` in a simple middleware component:</span></span>

<span data-ttu-id="73f51-142">[!code-csharp[Main](./distributed/sample/src/DistCacheSample/StartTimeHeader.cs?highlight=15,18,21,27,28,29,30,31)]</span><span class="sxs-lookup"><span data-stu-id="73f51-142">[!code-csharp[Main](./distributed/sample/src/DistCacheSample/StartTimeHeader.cs?highlight=15,18,21,27,28,29,30,31)]</span></span>

<span data-ttu-id="73f51-143">Dans le code ci-dessus, la valeur mise en cache est lu, mais jamais écrite.</span><span class="sxs-lookup"><span data-stu-id="73f51-143">In the code above, the cached value is read, but never written.</span></span> <span data-ttu-id="73f51-144">Dans cet exemple, la valeur est définie uniquement lorsqu’un serveur démarre et ne change pas.</span><span class="sxs-lookup"><span data-stu-id="73f51-144">In this sample, the value is only set when a server starts up, and doesn't change.</span></span> <span data-ttu-id="73f51-145">Dans un scénario multiserveur, le serveur le plus récent pour démarrer remplace toutes les valeurs précédentes qui ont été définies par d’autres serveurs.</span><span class="sxs-lookup"><span data-stu-id="73f51-145">In a multi-server scenario, the most recent server to start will overwrite any previous values that were set by other servers.</span></span> <span data-ttu-id="73f51-146">Le `Get` et `Set` méthodes utilisent le `byte[]` type.</span><span class="sxs-lookup"><span data-stu-id="73f51-146">The `Get` and `Set` methods use the `byte[]` type.</span></span> <span data-ttu-id="73f51-147">Par conséquent, la valeur de chaîne doive être convertie à l’aide de `Encoding.UTF8.GetString` (pour `Get`) et `Encoding.UTF8.GetBytes` (pour `Set`).</span><span class="sxs-lookup"><span data-stu-id="73f51-147">Therefore, the string value must be converted using `Encoding.UTF8.GetString` (for `Get`) and `Encoding.UTF8.GetBytes` (for `Set`).</span></span>

<span data-ttu-id="73f51-148">Le code suivant à partir de *Startup.cs* affiche la valeur :</span><span class="sxs-lookup"><span data-stu-id="73f51-148">The following code from *Startup.cs* shows the value being set:</span></span>

<span data-ttu-id="73f51-149">[!code-csharp[Main](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=2,4,5,6&range=58-66)]</span><span class="sxs-lookup"><span data-stu-id="73f51-149">[!code-csharp[Main](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=2,4,5,6&range=58-66)]</span></span>

> [!NOTE]
> <span data-ttu-id="73f51-150">Étant donné que `IDistributedCache` est configuré dans le `ConfigureServices` méthode, il est disponible pour le `Configure` méthode en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="73f51-150">Since `IDistributedCache` is configured in the `ConfigureServices` method, it is available to the `Configure` method as a parameter.</span></span> <span data-ttu-id="73f51-151">Pour permettre à l’instance configurée être fourni via DI, ajoutez-le en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="73f51-151">Adding it as a parameter will allow the configured instance to be provided through DI.</span></span>

## <a name="using-a-redis-distributed-cache"></a><span data-ttu-id="73f51-152">À l’aide d’un Redis Cache distribué</span><span class="sxs-lookup"><span data-stu-id="73f51-152">Using a Redis Distributed Cache</span></span>

<span data-ttu-id="73f51-153">[Redis](https://redis.io/) est un magasin de données en mémoire open source, qui est souvent utilisé comme un cache distribué.</span><span class="sxs-lookup"><span data-stu-id="73f51-153">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="73f51-154">Vous pouvez l’utiliser localement, et vous pouvez configurer un [Cache Redis Azure](https://azure.microsoft.com/services/cache/) pour vos applications hébergés par Azure ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="73f51-154">You can use it locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for your Azure-hosted ASP.NET Core apps.</span></span> <span data-ttu-id="73f51-155">Votre application ASP.NET Core configure l’implémentation de cache à l’aide un `RedisDistributedCache` instance.</span><span class="sxs-lookup"><span data-stu-id="73f51-155">Your ASP.NET Core app configures the cache implementation using a `RedisDistributedCache` instance.</span></span>

<span data-ttu-id="73f51-156">Vous configurez l’implémentation de Redis dans `ConfigureServices` et y accéder dans le code de votre application en demandant une instance de `IDistributedCache` (voir le code ci-dessus).</span><span class="sxs-lookup"><span data-stu-id="73f51-156">You configure the Redis implementation in `ConfigureServices` and access it in your app code by requesting an instance of `IDistributedCache` (see the code above).</span></span>

<span data-ttu-id="73f51-157">Dans l’exemple de code, un `RedisCache` implémentation est utilisée lorsque le serveur est configuré pour un `Staging` environnement.</span><span class="sxs-lookup"><span data-stu-id="73f51-157">In the sample code, a `RedisCache` implementation is used when the server is configured for a `Staging` environment.</span></span> <span data-ttu-id="73f51-158">Par conséquent, le `ConfigureStagingServices` méthode configure le `RedisCache`:</span><span class="sxs-lookup"><span data-stu-id="73f51-158">Thus the `ConfigureStagingServices` method configures the `RedisCache`:</span></span>

<span data-ttu-id="73f51-159">[!code-csharp[Main](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=8,9,10,11,12,13&range=27-40)]</span><span class="sxs-lookup"><span data-stu-id="73f51-159">[!code-csharp[Main](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=8,9,10,11,12,13&range=27-40)]</span></span>

> [!NOTE]
> <span data-ttu-id="73f51-160">Pour installer Redis sur votre ordinateur local, installez le package chocolatey [https://chocolatey.org/packages/redis-64/](https://chocolatey.org/packages/redis-64/) et exécutez `redis-server` à partir d’une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="73f51-160">To install Redis on your local machine, install the chocolatey package [https://chocolatey.org/packages/redis-64/](https://chocolatey.org/packages/redis-64/) and run `redis-server` from a command prompt.</span></span>

## <a name="using-a-sql-server-distributed-cache"></a><span data-ttu-id="73f51-161">À l’aide d’un serveur SQL Server de Cache distribué</span><span class="sxs-lookup"><span data-stu-id="73f51-161">Using a SQL Server Distributed Cache</span></span>

<span data-ttu-id="73f51-162">L’implémentation de SqlServerCache autorise le cache distribué à utiliser une base de données SQL Server comme magasin de sauvegarde.</span><span class="sxs-lookup"><span data-stu-id="73f51-162">The SqlServerCache implementation allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="73f51-163">Pour créer de SQL Server, table, vous pouvez utiliser d’outil de cache sql, l’outil crée une table avec le nom et le schéma que vous spécifiez.</span><span class="sxs-lookup"><span data-stu-id="73f51-163">To create SQL Server table you can use sql-cache tool, the tool creates a table with the name and schema you specify.</span></span>

<span data-ttu-id="73f51-164">Pour utiliser l’outil de cache sql, ajoutez `SqlConfig.Tools` à la `<ItemGroup>` élément de la *.csproj* et exécutez dotnet restauration.</span><span class="sxs-lookup"><span data-stu-id="73f51-164">To use the sql-cache tool, add `SqlConfig.Tools` to the `<ItemGroup>` element of the *.csproj* file and run dotnet restore.</span></span>

<span data-ttu-id="73f51-165">[!code-csharp[Main](./distributed/sample/src/DistCacheSample/DistCacheSample.csproj?range=23-25)]</span><span class="sxs-lookup"><span data-stu-id="73f51-165">[!code-csharp[Main](./distributed/sample/src/DistCacheSample/DistCacheSample.csproj?range=23-25)]</span></span>

<span data-ttu-id="73f51-166">Test SqlConfig.Tools en exécutant la commande suivante</span><span class="sxs-lookup"><span data-stu-id="73f51-166">Test SqlConfig.Tools by running the following command</span></span>

```none
C:\DistCacheSample\src\DistCacheSample>dotnet sql-cache create --help
   ```

<span data-ttu-id="73f51-167">outil SQL-cache affichent l’aide de l’utilisation, les options et les commandes, vous pouvez désormais créer les tables dans sql server, « créer un cache de sql » une commande en cours d’exécution :</span><span class="sxs-lookup"><span data-stu-id="73f51-167">sql-cache tool  will display usage, options and command help, now you can create tables into sql server, running "sql-cache create" command :</span></span>

```none
C:\DistCacheSample\src\DistCacheSample>dotnet sql-cache create "Data Source=(localdb)\v11.0;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
   info: Microsoft.Extensions.Caching.SqlConfig.Tools.Program[0]
       Table and index were created successfully.
   ```

<span data-ttu-id="73f51-168">Le tableau présente le schéma suivant :</span><span class="sxs-lookup"><span data-stu-id="73f51-168">The created table has the following schema:</span></span>

![Table de Cache de SQL Server](distributed/_static/SqlServerCacheTable.png)

<span data-ttu-id="73f51-170">Comme toutes les implémentations de cache, votre application doit obtenir et définir des valeurs de cache à l’aide d’une instance de `IDistributedCache`, et non un `SqlServerCache`.</span><span class="sxs-lookup"><span data-stu-id="73f51-170">Like all cache implementations, your app should get and set cache values using an instance of `IDistributedCache`, not a `SqlServerCache`.</span></span> <span data-ttu-id="73f51-171">L’exemple implémente `SqlServerCache` dans les `Production` environnement (par conséquent, il est configuré dans `ConfigureProductionServices`).</span><span class="sxs-lookup"><span data-stu-id="73f51-171">The sample implements `SqlServerCache` in the `Production` environment (so it is configured in `ConfigureProductionServices`).</span></span>

<span data-ttu-id="73f51-172">[!code-csharp[Main](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=7,8,9,10,11,12&range=42-56)]</span><span class="sxs-lookup"><span data-stu-id="73f51-172">[!code-csharp[Main](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=7,8,9,10,11,12&range=42-56)]</span></span>

> [!NOTE]
> <span data-ttu-id="73f51-173">Le `ConnectionString` (et, éventuellement, `SchemaName` et `TableName`) doivent généralement être stockées en dehors du contrôle de code source (par exemple, usersecrets.), car ils peuvent contenir des informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="73f51-173">The `ConnectionString` (and optionally, `SchemaName` and `TableName`) should typically be stored outside of source control (such as UserSecrets), as they may contain credentials.</span></span>

## <a name="recommendations"></a><span data-ttu-id="73f51-174">Recommandations</span><span class="sxs-lookup"><span data-stu-id="73f51-174">Recommendations</span></span>

<span data-ttu-id="73f51-175">Lorsque vous décidez quelle implémentation de `IDistributedCache` est adaptée à votre application, choisissez entre Redis et SQL Server en fonction de votre infrastructure existante et environnement, vos exigences de performances et expérience de votre équipe.</span><span class="sxs-lookup"><span data-stu-id="73f51-175">When deciding which implementation of `IDistributedCache` is right for your app, choose between Redis and SQL Server based on your existing infrastructure and environment, your performance requirements, and your team's experience.</span></span> <span data-ttu-id="73f51-176">Si votre équipe n’est plus familiarisent avec Redis, il constitue un excellent choix.</span><span class="sxs-lookup"><span data-stu-id="73f51-176">If your team is more comfortable working with Redis, it's an excellent choice.</span></span> <span data-ttu-id="73f51-177">Si votre équipe s’il préfère que SQL Server, vous pouvez être certain qu’également l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="73f51-177">If your team prefers SQL Server, you can be confident in that implementation as well.</span></span> <span data-ttu-id="73f51-178">Notez qu’une solution de mise en cache traditionnelle stocke les données en mémoire qui permet la récupération rapide des données.</span><span class="sxs-lookup"><span data-stu-id="73f51-178">Note that A traditional caching solution stores data in-memory which allows for fast retrieval of data.</span></span> <span data-ttu-id="73f51-179">Vous devez stocker les données couramment utilisées dans un cache et stocker la totalité des données dans un magasin persistant de back-end telles que SQL Server ou le stockage Azure.</span><span class="sxs-lookup"><span data-stu-id="73f51-179">You should store commonly used data in a cache and store the entire data in a backend persistent store such as SQL Server or Azure Storage.</span></span> <span data-ttu-id="73f51-180">Cache redis est une solution de mise en cache qui vous donne un débit élevé et une faible latence par rapport au Cache SQL.</span><span class="sxs-lookup"><span data-stu-id="73f51-180">Redis Cache is a caching solution which gives you high throughput and low latency as compared to SQL Cache.</span></span>

<span data-ttu-id="73f51-181">Ressources supplémentaires :</span><span class="sxs-lookup"><span data-stu-id="73f51-181">Additional resources:</span></span>

* [<span data-ttu-id="73f51-182">La mise en mémoire cache</span><span class="sxs-lookup"><span data-stu-id="73f51-182">In memory caching</span></span>](memory.md)
* [<span data-ttu-id="73f51-183">Cache dans Azure redis</span><span class="sxs-lookup"><span data-stu-id="73f51-183">Redis Cache on Azure</span></span>](https://azure.microsoft.com/documentation/services/redis-cache/)
* [<span data-ttu-id="73f51-184">Base de données SQL Azure</span><span class="sxs-lookup"><span data-stu-id="73f51-184">SQL Database on Azure</span></span>](https://azure.microsoft.com/documentation/services/sql-database/)
