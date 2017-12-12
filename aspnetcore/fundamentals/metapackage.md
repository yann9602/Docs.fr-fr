---
title: "Metapackage Microsoft.AspNetCore.All pour ASP.NET Core 2.x et versions ultérieures"
author: Rick-Anderson
description: "Le metapackage Microsoft.AspNetCore.All inclut tous les packages ASP.NET Core et Entity Framework Core, ainsi que leurs dépendances."
keywords: ASP.NET Core,NuGet,package,Microsoft.AspNetCore.All,metapackage
ms.author: riande
manager: wpickett
ms.date: 09/20/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/metapackage
ms.openlocfilehash: ff25d80be907994f7ac3d64a8ffa39ae53278ba6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
#<a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-2x"></a>Metapackage Microsoft.AspNetCore.All pour ASP.NET Core 2.x

Cette fonctionnalité nécessite .NET de ciblage ASP.NET Core 2.x 2.x de base.

Le métapackage [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) pour ASP.NET Core comprend :

* Tous les packages pris en charge par l’équipe ASP.NET Core.
* Tous les packages pris en charge par Entity Framework Core. 
* Les dépendances internes et tierces utilisées par ASP.NET Core et Entity Framework Core. 

Toutes les fonctionnalités ASP.NET Core 2.x et Entity Framework Core 2.x sont inclus dans le `Microsoft.AspNetCore.All` package. Les modèles de projet par défaut utilisent ce package.

Le numéro de version de la `Microsoft.AspNetCore.All` metapackage représente la version de ASP.NET Core et Entity Framework Core (aligné avec la version de .NET Core).

Les applications qui utilisent la `Microsoft.AspNetCore.All` metapackage tirer parti automatiquement de la [magasin du Runtime .NET Core](https://docs.microsoft.com/dotnet/core/deploying/runtime-store). Le magasin de Runtime contient tous les composants runtime nécessaires à l’exécution des applications 2.x ASP.NET Core. Lorsque vous utilisez la `Microsoft.AspNetCore.All` metapackage, **aucun** des ressources depuis les packages ASP.NET Core NuGet référencés sont déployés avec l’application &mdash; le magasin du Runtime .NET Core contient ces ressources. Les éléments multimédias dans le magasin d’exécution sont précompilés pour améliorer les temps de démarrage d’application.

Vous pouvez utiliser le processus de suppression du package à supprimer les packages que vous n’utilisez pas. Packages découpés sont exclus de la sortie de l’application publiée.

Les éléments suivants *.csproj* références de fichiers le `Microsoft.AspNetCore.All` metapackage pour ASP.NET Core :

[!code-xml[Main](..\mvc\views\view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=9)]
