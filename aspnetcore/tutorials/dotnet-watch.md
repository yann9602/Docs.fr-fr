---
title: "Développement d’applications ASP.NET Core à l’aide de dotnet watch"
author: rick-anderson
description: Montre comment utiliser dotnet watch.
keywords: "ASP.NET Core, à l’aide de dotnet watch"
ms.author: riande
manager: wpickett
ms.date: 03/09/2017
ms.topic: article
ms.assetid: 563ffb3f-d369-4aa5-bf0a-7300b4e7832c
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/dotnet-watch
ms.openlocfilehash: 6a8943619e6174dbcd3d901b7bb736ba5d3af95d
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="developing-aspnet-core-apps-using-dotnet-watch"></a>Développement d’applications ASP.NET Core à l’aide de dotnet watch


Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

`dotnet watch` est un outil qui exécute une commande `dotnet` quand des fichiers sources changent. Par exemple, une modification de fichier peut déclencher la compilation, tests ou le déploiement.

Dans ce didacticiel, nous utilisons une application API web existante avec deux points de terminaison : un qui retourne une somme et un qui retourne un produit. La méthode product contient un bogue que nous allons résoudre dans le cadre de ce didacticiel.

Téléchargez l’[application exemple](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample). Il contient deux projets, `WebApp` (une application web) et `WebAppTests` (des tests unitaires pour l’application web).

Dans une console, accédez au dossier WebApp et exécutez les commandes suivantes :

- `dotnet restore`
- `dotnet run`

La sortie de la console affiche des messages semblables à ce qui suit (indiquant que l’application est en cours d’exécution et en attente de requêtes) :

```console
$ dotnet run
Hosting environment: Production
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Dans un navigateur web, accédez à `http://localhost:5000/api/math/sum?a=4&b=5`. Le résultat `9` doit s’afficher.

Accédez à l’API du produit (`http://localhost:5000/api/math/product?a=4&b=5`). Elle retourne `9`, et non pas `20` comme prévu. Nous allons corriger cela ultérieurement dans le didacticiel.

## <a name="add-dotnet-watch-to-a-project"></a>Ajouter `dotnet watch` à un projet

- Ajoutez `Microsoft.DotNet.Watcher.Tools` au fichier *.csproj* :
 ```xml
 <ItemGroup>
   <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
 </ItemGroup> 
 ```

- Exécutez `dotnet restore`.

## <a name="running-dotnet-commands-using-dotnet-watch"></a>Exécution de commandes `dotnet` à l’aide de `dotnet watch`

Il est possible d’exécuter n’importe quelle commande `dotnet` avec `dotnet watch` ; par exemple :

| Commande | Commande avec watch |
| ---- | ----- |
| dotnet run | dotnet watch run |
| dotnet run -f net451 | dotnet watch run -f net451 |
| dotnet run -f net451 -- --arg1 | dotnet watch run -f net451 -- --arg1 |
| dotnet test | dotnet watch test |

Exécutez `dotnet watch run` dans le dossier `WebApp`. La sortie de la console indique alors que `watch` a démarré.

## <a name="making-changes-with-dotnet-watch"></a>Apport de modifications avec `dotnet watch`

Vérifiez que `dotnet watch` est en cours d’exécution.

Corrigez le bogue présent dans la méthode `Product` de `MathController` afin qu’elle retourne le produit et non la somme.

```csharp
public static int Product(int a, int b)
{
  return a * b;
} 
```

Enregistrez le fichier. La sortie de la console affiche des messages indiquant que `dotnet watch` a détecté un changement de fichier et redémarré de l’application.

Vérifiez que `http://localhost:5000/api/math/product?a=4&b=5` retourne le résultat correct.

## <a name="running-tests-using-dotnet-watch"></a>Exécution de tests à l’aide de `dotnet watch`

- Modifiez la méthode `Product` de `MathController` pour qu’elle retourne à nouveau la somme, puis enregistrez le fichier.
- Dans une fenêtre Commande, accédez au dossier `WebAppTests`.
- Exécutez `dotnet restore`.
- Exécutez `dotnet watch test`. Une sortie s’affiche, indiquant qu’un test a échoué et que l’observateur est en attente de modifications de fichier :

 ```console
 Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
 Test Run Failed.
  ```
- Corrigez le code de la méthode `Product` afin qu’elle retourne le produit. Enregistrez le fichier.

`dotnet watch` détecte le changement de fichier et réexécute les tests. La sortie de la console affiche alors les tests réussis.

## <a name="dotnet-watch-in-github"></a>dotnet-watch dans GitHub

dotnet-watch fait partie du [dépôt DotNetTools](https://github.com/aspnet/DotNetTools/tree/dev/src/Microsoft.DotNet.Watcher.Tools) GitHub.

La [section MSBuild](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md#msbuild) du fichier [Lisez-moi dotnet-watch](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md) explique comment configurer dotnet-watch à partir du fichier projet MSBuild surveillé. Le fichier [Lisez-moi dotnet-watch](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md) contient des informations sur dotnet-watch non traitées dans ce didacticiel.
