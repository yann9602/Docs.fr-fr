---
title: "Metapackage Microsoft.AspNetCore.All pour ASP.NET Core 2.x et versions ultérieures"
author: Rick-Anderson
description: Microsoft.AspNetCore.All metapackage inclut tous les packages.
keywords: "ASP.NET Core, NuGet, créez un package, Microsoft.AspNetCore.All, metapackage"
ms.author: riande
manager: wpickett
ms.date: 07/16/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/metapackage
ms.openlocfilehash: 255438a4ce36ce4978f8c8ee298388a25ac00d17
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
#<a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-2x"></a>Metapackage Microsoft.AspNetCore.All pour ASP.NET Core 2.x

Cette fonctionnalité nécessite ASP.NET Core 2.x.

Le [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) metapackage pour ASP.NET Core inclut :

* Tous les packages pris en charge par l’équipe ASP.NET Core.
* Tous les packages par l’Entity Framework Core pris en charge. 
* Dépendances internes et 3 rd-party utilisés par ASP.NET Core et Entity Framework Core. 

Toutes les fonctionnalités ASP.NET Core 2.x et Entity Framework Core 2.x sont inclus dans le `Microsoft.AspNetCore.All` package. Les modèles de projet par défaut utilisent ce package.

Le numéro de version de la `Microsoft.AspNetCore.All` metapackage représente la version de ASP.NET Core et Entity Framework Core (aligné avec la version de .NET Core).

Les applications qui utilisent le `Microsoft.AspNetCore.All` metapackage tirer parti automatiquement de la banque de Runtime .NET Core. Le magasin de Runtime contient tous les composants runtime nécessaires à l’exécution des applications 2.x ASP.NET Core. Lorsque vous utilisez la `Microsoft.AspNetCore.All` metapackage, **aucun** des ressources depuis les packages ASP.NET Core NuGet référencés sont déployés avec l’application &mdash; le magasin du Runtime .NET Core contient ces ressources. <!-- todo add link to Runtime store -->Les éléments multimédias dans le magasin d’exécution sont précompilés pour améliorer les temps de démarrage d’application.

Vous pouvez utiliser le processus de suppression du package à supprimer les packages que vous n’utilisez pas. Packages découpés sont exclus de la sortie de l’application publiée.

Les éléments suivants *.csproj* références de fichiers le `Microsoft.AspNetCore.All` metapackage pour ASP.NET Core :

[!code-xml[Main](..\mvc\views\view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=9)]
