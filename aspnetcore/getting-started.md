---
title: "Bien démarrer avec ASP.NET Core 2.0"
author: rick-anderson
description: "Didacticiel rapide qui crée et exécute une application Hello World simple à l’aide d’ASP.NET Core."
manager: wpickett
ms.author: riande
ms.date: 10/18/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: getting-started
ms.openlocfilehash: eb1fd748029743ca6991927cc95b612ed1975338
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="get-started-with-aspnet-core"></a>Bien démarrer avec ASP.NET Core

> [!NOTE]
> Les instructions suivantes concernent la dernière version d’ASP.NET Core. Si vous souhaitez bien démarrer avec une version précédente, consultez [la version 1.1 de ce didacticiel](xref:getting-started-1.1).

1. Installez [.NET Core](https://www.microsoft.com/net/core/).

2. Créez un projet .NET Core.

   Sur macOS et Linux, ouvrez une fenêtre de terminal. Sur Windows, ouvrez une invite de commandes. Entrez la commande suivante :

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
