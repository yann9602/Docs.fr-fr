---
title: "Bien démarrer avec ASP.NET Core 1.1"
author: rick-anderson
description: "Didacticiel rapide qui crée et exécute une application Hello World simple à l’aide d’ASP.NET Core 1.1."
keywords: "ASP.NET Core,didacticiel,bien démarrer"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.assetid: 73543e9d-d9d5-47d6-9664-17a9beea6cd3
ms.technology: aspnet
ms.prod: asp.net-core
uid: getting-started-1.1
ms.openlocfilehash: e8fd9ef60ebc1cff6ca0e03000ea50eebff0a9f9
ms.sourcegitcommit: 297ee5d2f3b3b24eb8a2c4a25195c9e2973cb91b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2017
---
# <a name="getting-started-with-aspnet-core-11"></a>Bien démarrer avec ASP.NET Core 1.1

> [!NOTE]
> Les instructions suivantes concernent ASP.NET Core 1.1. Si vous recherchez la dernière version, consultez [la version actuelle de ce didacticiel](xref:getting-started).

1. Installez le **programme d’installation du SDK** .NET Core pour le SDK 1.0.4 à partir de la [page des téléchargements du SDK 1.0.4 .NET Core 1.0.5 & 1.1.2](https://github.com/dotnet/core/blob/master/release-notes/download-archives/1.0.5-download.md).

2. Créez un dossier pour un nouveau projet .NET Core.

   Sur macOS et Linux, ouvrez une fenêtre de terminal. Sur Windows, ouvrez une invite de commandes.

   ```terminal
   mkdir aspnetcoreapp
   cd aspnetcoreapp
   ```

2. Si vous avez installé une version ultérieure du SDK sur votre ordinateur, créez un fichier *global.json* pour sélectionner le SDK 1.0.4.

   ```json
   {
     "sdk": { "version": "1.0.4" }
   }
   ```

2. Créez un projet .NET Core.

   ```terminal
   dotnet new web
   ```
   
3.  Restaurez les packages.

    ```terminal
    dotnet restore
    ```

4. Exécutez l’application.

   La commande `dotnet run` commence par générer l’application, si nécessaire.

   ```terminal
   dotnet run
   ```

5. Accédez à `http://localhost:5000`.

<!-- H3 to avoid a single-entry internal TOC -->
### <a name="next-steps"></a>Étapes suivantes

Pour consulter des didacticiels permettant de bien démarrer, consultez [Didacticiels ASP.NET Core](tutorials/index.md).

Pour obtenir une introduction aux concepts et à l’architecture d’ASP.NET Core, consultez [Introduction à ASP.NET Core](index.md) et [Notions de base d’ASP.NET Core](fundamentals/index.md).

Une application ASP.NET Core peut utiliser la bibliothèque de classes de base et le runtime .NET Core ou .NET Framework. Pour plus d’informations, consultez [Choix entre .NET Core et .NET Framework](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server).
