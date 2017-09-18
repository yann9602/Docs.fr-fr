---
title: "Bien démarrer avec ASP.NET Core 2.0"
author: rick-anderson
description: "Didacticiel rapide qui crée et exécute une application Hello World simple à l’aide d’ASP.NET Core."
keywords: "ASP.NET Core,didacticiel,bien démarrer"
ms.author: riande
manager: wpickett
ms.date: 08/30/2017
ms.topic: get-started-article
ms.assetid: 73543e9d-d9d5-47d6-9664-17a9beea6cd3
ms.technology: aspnet
ms.prod: asp.net-core
uid: getting-started
ms.openlocfilehash: c81e1328fda6d1652ab937bd580be2342924d241
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="getting-started-with-aspnet-core"></a>Bien démarrer avec ASP.NET Core

> [!NOTE]
> Les instructions suivantes concernent la dernière version d’ASP.NET Core. Si vous souhaitez bien démarrer avec une version précédente, consultez [la version 1.1 de ce didacticiel](xref:getting-started-1.1).

1. Installez [.NET Core](https://www.microsoft.com/net/core/).

2. Créez un projet .NET Core.

   Sur macOS et Linux, ouvrez une fenêtre de terminal. Sur Windows, ouvrez une invite de commandes.

    ```terminal
    dotnet new razor -o aspnetcoreapp
    ```
    
4. Exécutez l’application.

    Exécutez les commandes suivantes pour exécuter l’application :

    ```terminal
    cd aspnetcoreapp
    dotnet run
    ```

5. Accédez à [http://localhost:5000](http://localhost:5000)

6. Ouvrez *Pages/About.cshtml*, puis modifiez la page de façon à afficher le message « Hello, world! The time on the server is @DateTime.Now » :

    [!code-html[Main](getting-started/sample/getting-started/about.cshtml?highlight=9&range=1-9)]

7. Accédez à [http://localhost:5000/About](http://localhost:5000/About), puis vérifiez les modifications.

### <a name="next-steps"></a>Étapes suivantes

Pour consulter des didacticiels permettant de bien démarrer, consultez [Didacticiels ASP.NET Core](tutorials/index.md).

Pour obtenir une introduction aux concepts et à l’architecture d’ASP.NET Core, consultez [Introduction à ASP.NET Core](index.md) et [Notions de base d’ASP.NET Core](fundamentals/index.md).

Une application ASP.NET Core peut utiliser la bibliothèque de classes de base et le runtime .NET Core ou .NET Framework. Pour plus d’informations, consultez [Choix entre .NET Core et .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).
