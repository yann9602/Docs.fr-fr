---
title: "Développement d’applications ASP.NET Core à l’aide de dotnet watch"
author: rick-anderson
description: "Ce didacticiel montre comment installer et utiliser l’outil Observateur de fichiers (dotnet watch) de l’interface de ligne de commande .NET Core dans une application ASP.NET Core."
keywords: "ASP.NET Core, à l’aide de dotnet watch"
ms.author: riande
manager: wpickett
ms.date: 10/05/2017
ms.topic: article
ms.assetid: 563ffb3f-d369-4aa5-bf0a-7300b4e7832c
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/dotnet-watch
ms.openlocfilehash: 1b26beaa41f4b38e0cfd2c8300cb521a3dcce47d
ms.sourcegitcommit: 281f0c614543a6c3db565ea4655b70fe49b61d84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="developing-aspnet-core-apps-using-dotnet-watch"></a>Développement d’applications ASP.NET Core à l’aide de dotnet watch

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

`dotnet watch` est un outil qui exécute une commande [.NET Core CLI](/dotnet/core/tools) quand des fichiers sources changent. Par exemple, un changement de fichier peut déclencher la compilation, l’exécution de tests ou le déploiement.

Dans ce didacticiel, nous utilisons une application API web existante avec deux points de terminaison : un qui retourne une somme et un qui retourne un produit. La méthode product contient un bogue que nous allons résoudre dans le cadre de ce didacticiel.

Téléchargez l’[application exemple](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample). Elle contient deux projets : *WebApp* (API web ASP.NET Core) et *WebAppTests* (API de tests unitaires pour le web).

Dans une interface de commande, accédez au dossier *WebApp* et exécutez la commande suivante :

```console
dotnet run
```

La sortie de la console affiche des messages semblables à ce qui suit (indiquant que l’application est en cours d’exécution et en attente de demandes) :

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Dans un navigateur web, accédez à `http://localhost:<port number>/api/math/sum?a=4&b=5`. Vous devez voir le résultat `9`.

Accédez à l’API du produit (`http://localhost:<port number>/api/math/product?a=4&b=5`). Elle retourne `9`, et non pas `20` comme prévu. Nous allons corriger cela ultérieurement dans le didacticiel.

## <a name="add-dotnet-watch-to-a-project"></a>Ajouter `dotnet watch` à un projet

1. Ajoutez une référence de package `Microsoft.DotNet.Watcher.Tools` dans le fichier *.csproj* :

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup> 
    ```

1. Installez le package `Microsoft.DotNet.Watcher.Tools` en exécutant la commande suivante :
    
    ```console
    dotnet restore
    ```

## <a name="running-net-core-cli-commands-using-dotnet-watch"></a>Exécution des commandes de l’interface CLI de .NET Core avec `dotnet watch`

Toutes les [commandes de l’interface CLI de .NET Core](/dotnet/core/tools#cli-commands) peuvent être exécutées avec `dotnet watch`. Exemple :

| Commande | Commande avec watch |
| ---- | ----- |
| dotnet run | dotnet watch run |
| dotnet run -f netcoreapp2.0 | dotnet watch run -f netcoreapp2.0 |
| dotnet run -f netcoreapp2.0 -- --arg1 | dotnet watch run -f netcoreapp2.0 -- --arg1 |
| dotnet test | dotnet watch test |

Exécutez `dotnet watch run` dans le dossier *WebApp*. La sortie de la console indique que `watch` a démarré.

## <a name="making-changes-with-dotnet-watch"></a>Apport de modifications avec `dotnet watch`

Vérifiez que `dotnet watch` est en cours d’exécution.

Corrigez le bogue présent dans la méthode `Product` de *MathController.cs* afin qu’elle retourne le produit et non la somme :

```csharp
public static int Product(int a, int b)
{
  return a * b;
} 
```

Enregistrez le fichier. La sortie de la console indique que `dotnet watch` a détecté un changement de fichier et a redémarré l’application.

Vérifiez que `http://localhost:<port number>/api/math/product?a=4&b=5` retourne le résultat correct.

## <a name="running-tests-using-dotnet-watch"></a>Exécution de tests à l’aide de `dotnet watch`

1. Modifiez la méthode `Product` de *MathController.cs* pour qu’elle retourne à nouveau la somme, puis enregistrez le fichier.
1. Dans une interface de commande, accédez au dossier *WebAppTests*.
1. Exécutez `dotnet restore`.
1. Exécutez `dotnet watch test`. Sa sortie indique qu’un test a échoué et que l’observateur est en attente de changement de fichier :

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. Corrigez le code de la méthode `Product` afin qu’elle retourne le produit. Enregistrez le fichier.

`dotnet watch` détecte le changement de fichier et réexécute les tests. La sortie de la console indique que les tests ont réussi.

## <a name="dotnet-watch-in-github"></a>dotnet-watch dans GitHub

dotnet-watch fait partie du [dépôt DotNetTools](https://github.com/aspnet/DotNetTools/tree/dev/src/dotnet-watch) GitHub.

La [section MSBuild](https://github.com/aspnet/DotNetTools/tree/dev/src/dotnet-watch#msbuild) du fichier [Lisez-moi dotnet-watch](https://github.com/aspnet/DotNetTools/blob/dev/src/dotnet-watch/README.md) explique comment configurer dotnet-watch à partir du fichier projet MSBuild surveillé. Le fichier [Lisez-moi dotnet-watch](https://github.com/aspnet/DotNetTools/blob/dev/src/dotnet-watch/README.md) contient des informations sur dotnet-watch non traitées dans ce didacticiel.
