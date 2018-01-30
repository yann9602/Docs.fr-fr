---
title: "Utilisation avec un cache distribué dans ASP.NET Core"
author: ardalis
description: "Découvrez comment utiliser la mise en cache distribuée afin d’améliorer les performances et l’évolutivité des applications ASP.NET Core, en particulier lorsque hébergée dans un environnement de batterie de serveurs cloud ou de serveur."
manager: wpickett
ms.author: riande
ms.date: 02/14/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: performance/caching/distributed
ms.openlocfilehash: 86fd40863f6eeef3c129335141d704769d36b4c1
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="working-with-a-distributed-cache-in-aspnet-core"></a>Utilisation avec un cache distribué dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/)

Les caches distribués peuvent améliorer les performances et l’évolutivité des applications ASP.NET Core, en particulier lorsque hébergée dans un environnement de batterie de serveurs cloud ou de serveur. Cet article explique comment travailler avec les implémentations et les abstractions de cache distribué intégrée d’ASP.NET Core.

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/distributed/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="what-is-a-distributed-cache"></a>Qu’est un cache distribué

Un cache distribué est partagé par plusieurs serveurs d’application (consultez [principes fondamentaux de la mise en cache](memory.md#caching-basics)). Les informations contenues dans le cache ne sont pas stockées dans la mémoire des serveurs de site web individuel, et les données mises en cache sont disponibles pour tous les serveurs de l’application. Cela présente plusieurs avantages :

1. Données mises en cache sont cohérentes sur tous les serveurs web. Les utilisateurs ne voient des résultats différents selon le web server gère leur demande

2. Données mises en cache survivent redémarrage du serveur web et les déploiements. Serveurs web individuels peuvent être supprimés ou ajoutés sans impact sur le cache.

3. Le magasin de données source a moins de demandes effectuées (de plusieurs caches en mémoire ou de non mise en cache du tout).

> [!NOTE]
> Si vous utilisez un Cache distribué de SQL Server, certains de ces avantages ne sont trues si une instance distincte de la base de données est utilisée pour le cache que pour les données de source de l’application.

Comme n’importe quel type de cache, un cache distribué peut considérablement améliorer la réactivité d’une application, car en général, les données peuvent être récupérées à partir du cache beaucoup plus rapide qu’à partir d’une base de données relationnelle (ou un service web).

Configuration du cache est spécifique à l’implémentation. Cet article décrit comment configurer les deux Redis et distribuées SQL Server met en cache. Indépendamment de l’implémentation est sélectionnée, l’application interagit avec le cache à l’aide d’un commun `IDistributedCache` interface.

## <a name="the-idistributedcache-interface"></a>L’Interface IDistributedCache

Le `IDistributedCache` interface inclut des méthodes synchrones et asynchrones. L’interface autorise des éléments ajoutés, la récupération et la supprimer de l’implémentation de cache distribué. Le `IDistributedCache` interface comprend les méthodes suivantes :

**Get, GetAsync**

Prend une clé de chaîne et récupère un élément mis en cache comme un `byte[]` si trouvée dans le cache.

**Set, SetAsync**

Ajoute un élément (comme `byte[]`) dans le cache à l’aide d’une clé de chaîne.

**Refresh, RefreshAsync**

Actualise un élément dans le cache en fonction de sa clé, la réinitialisation de son délai d’expiration décalée (le cas échéant).

**Remove, RemoveAsync**

Supprime une entrée de cache en fonction de sa clé.

Pour utiliser le `IDistributedCache` interface :

   1. Ajouter les packages NuGet nécessaires à votre fichier projet.

   2. Configurer l’implémentation spécifique de `IDistributedCache` dans votre `Startup` la classe `ConfigureServices` (méthode) et l’ajouter au conteneur il.

   3. À partir de l’application [intergiciel (middleware)](../../fundamentals/middleware.md) ou classes de contrôleur MVC, demandez à une instance de `IDistributedCache` à partir du constructeur. L’instance est assurée par le [Injection de dépendance](../../fundamentals/dependency-injection.md) (DI).

> [!NOTE]
> Il est inutile d’utiliser une durée de vie Singleton ou inclus dans l’étendue pour `IDistributedCache` instances (au moins pour les implémentations intégrées). Vous pouvez également créer une instance de chaque fois que vous devrez peut-être une (au lieu d’utiliser [Injection de dépendance](../../fundamentals/dependency-injection.md)), mais cela peut rendre votre code plus difficile à tester et ne respecte pas la [principe de dépendances explicites](http://deviq.com/explicit-dependencies-principle/).

L’exemple suivant montre comment utiliser une instance de `IDistributedCache` dans un composant d’intergiciel (middleware) simple :

[!code-csharp[Main](./distributed/sample/src/DistCacheSample/StartTimeHeader.cs?highlight=15,18,21,27,28,29,30,31)]

Dans le code ci-dessus, la valeur mise en cache est lu, mais jamais écrite. Dans cet exemple, la valeur est définie uniquement lorsqu’un serveur démarre et ne change pas. Dans un scénario multiserveur, le serveur le plus récent pour démarrer remplace toutes les valeurs précédentes qui ont été définies par d’autres serveurs. Le `Get` et `Set` méthodes utilisent le `byte[]` type. Par conséquent, la valeur de chaîne doive être convertie à l’aide de `Encoding.UTF8.GetString` (pour `Get`) et `Encoding.UTF8.GetBytes` (pour `Set`).

Le code suivant à partir de *Startup.cs* affiche la valeur :

[!code-csharp[Main](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=2,4,5,6&range=58-66)]

> [!NOTE]
> Étant donné que `IDistributedCache` est configuré dans le `ConfigureServices` méthode, il est disponible pour le `Configure` méthode en tant que paramètre. Pour permettre à l’instance configurée être fourni via DI, ajoutez-le en tant que paramètre.

## <a name="using-a-redis-distributed-cache"></a>À l’aide d’un cache Redis distribué

[Redis](https://redis.io/) est un magasin de données en mémoire open source, qui est souvent utilisé comme un cache distribué. Vous pouvez l’utiliser localement, et vous pouvez configurer un [Cache Redis Azure](https://azure.microsoft.com/services/cache/) pour vos applications hébergés par Azure ASP.NET Core. Votre application ASP.NET Core configure l’implémentation de cache à l’aide un `RedisDistributedCache` instance.

Vous configurez l’implémentation de Redis dans `ConfigureServices` et y accéder dans le code de votre application en demandant une instance de `IDistributedCache` (voir le code ci-dessus).

Dans l’exemple de code, un `RedisCache` implémentation est utilisée lorsque le serveur est configuré pour un `Staging` environnement. Par conséquent, le `ConfigureStagingServices` méthode configure le `RedisCache`:

[!code-csharp[Main](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=8,9,10,11,12,13&range=27-40)]

> [!NOTE]
> Pour installer Redis sur votre ordinateur local, installez le package chocolatey [https://chocolatey.org/packages/redis-64/](https://chocolatey.org/packages/redis-64/) et exécutez `redis-server` à partir d’une invite de commandes.

## <a name="using-a-sql-server-distributed-cache"></a>À l’aide d’un serveur SQL Server de cache distribué

L’implémentation de SqlServerCache autorise le cache distribué à utiliser une base de données SQL Server comme magasin de sauvegarde. Pour créer de SQL Server, table, vous pouvez utiliser d’outil de cache sql, l’outil crée une table avec le nom et le schéma que vous spécifiez.

Pour utiliser l’outil de cache sql, ajoutez `SqlConfig.Tools` à la `<ItemGroup>` élément de la *.csproj* et exécutez dotnet restauration.

[!code-xml[Main](./distributed/sample/src/DistCacheSample/DistCacheSample.csproj?range=23-25)]

Test SqlConfig.Tools en exécutant la commande suivante

```none
C:\DistCacheSample\src\DistCacheSample>dotnet sql-cache create --help
   ```

outil SQL-cache affichent l’aide de l’utilisation, les options et les commandes, vous pouvez désormais créer les tables dans sql server, « créer un cache de sql » une commande en cours d’exécution :

```none
C:\DistCacheSample\src\DistCacheSample>dotnet sql-cache create "Data Source=(localdb)\v11.0;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
   info: Microsoft.Extensions.Caching.SqlConfig.Tools.Program[0]
       Table and index were created successfully.
   ```

Le tableau présente le schéma suivant :

![Table de Cache de SQL Server](distributed/_static/SqlServerCacheTable.png)

Comme toutes les implémentations de cache, votre application doit obtenir et définir des valeurs de cache à l’aide d’une instance de `IDistributedCache`, et non un `SqlServerCache`. L’exemple implémente `SqlServerCache` dans les `Production` environnement (par conséquent, il est configuré dans `ConfigureProductionServices`).

[!code-csharp[Main](./distributed/sample/src/DistCacheSample/Startup.cs?highlight=7,8,9,10,11,12&range=42-56)]

> [!NOTE]
> Le `ConnectionString` (et, éventuellement, `SchemaName` et `TableName`) doivent généralement être stockées en dehors du contrôle de code source (par exemple, usersecrets.), car ils peuvent contenir des informations d’identification.

## <a name="recommendations"></a>Recommandations

Lorsque vous décidez quelle implémentation de `IDistributedCache` est adaptée à votre application, choisissez entre Redis et SQL Server en fonction de votre infrastructure existante et environnement, vos exigences de performances et expérience de votre équipe. Si votre équipe n’est plus familiarisent avec Redis, il constitue un excellent choix. Si votre équipe s’il préfère que SQL Server, vous pouvez être certain qu’également l’implémentation. Notez qu’une solution de mise en cache traditionnelle stocke les données en mémoire qui permet la récupération rapide des données. Vous devez stocker les données couramment utilisées dans un cache et stocker la totalité des données dans un magasin persistant de back-end telles que SQL Server ou le stockage Azure. Cache redis est une solution de mise en cache qui vous donne un débit élevé et une faible latence par rapport au Cache SQL.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Cache dans Azure redis](https://azure.microsoft.com/documentation/services/redis-cache/)
* [Base de données SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/)
* [Mise en cache en mémoire](xref:performance/caching/memory)
* [Détecter les modifications à l’aide de jetons de modification](xref:fundamentals/primitives/change-tokens)
* [Mise en cache des réponses](xref:performance/caching/response)
* [Intergiciel de mise en cache des réponses](xref:performance/caching/middleware)
* [Tag Helper de cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)
* [Tag Helper de cache distribué](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)
