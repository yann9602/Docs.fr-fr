---
title: "La précompilation et compilation de vue razor"
author: rick-anderson
description: "Un document de référence expliquant comment activer la compilation de vue MVC Razor et précompilation dans les applications ASP.NET Core."
ms.author: riande
manager: wpickett
ms.date: 12/13/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/view-compilation
ms.openlocfilehash: 87989455c2fb6b5a922c7fb6133aa3e8cef42c88
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="razor-view-compilation-and-precompilation-in-aspnet-core"></a>Compilation de vue Razor et précompilation dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Vues Razor sont compilées lors de l’exécution lorsque la vue est appelée. ASP.NET Core 1.1.0 et une valeur supérieure peut éventuellement compiler vues Razor et les déployer avec l’application&mdash;processus s’appelé la précompilation. Les modèles de projet ASP.NET Core 2.x activent la précompilation par défaut.

> [!IMPORTANT]
> La précompilation de vue Razor est actuellement pas disponible lorsque vous effectuez un [déploiement autonome (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) dans ASP.NET 2.0 de base. La fonctionnalité sera disponible pour les dimensions à variation lente lorsque 2.1 relâche. Pour plus d’informations, consultez [la compilation de la vue échoue lors de la compilation croisée pour Linux sur Windows](https://github.com/aspnet/MvcPrecompilation/issues/102).

Considérations relatives à la précompilation :

* Résultats de la précompilation des vues dans un plus petit groupe de publié et le démarrage plus rapide.
* Vous ne pouvez pas modifier les fichiers Razor précompiler de vues. Les vues modifiés sera présents dans l’application publiée. 

Pour déployer des vues précompilés :

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Si votre projet cible .NET Framework, inclure une référence de package à [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/):

```xml
<PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.ViewCompilation" Version="2.0.0" PrivateAssets="All" />
```

Si votre projet cible .NET Core, aucune modification n’est nécessaire.

Les modèles de projet ASP.NET Core 2.x implicitement la valeur `MvcRazorCompileOnPublish` à `true` par défaut, ce qui signifie que ce nœud peut être supprimé en toute sécurité le *.csproj* fichier. Si vous préférez être explicite, il n’existe aucun risque dans le paramètre de la `MvcRazorCompileOnPublish` propriété `true`. Les éléments suivants *.csproj* exemple met en surbrillance ce paramètre :

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=5)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Définissez `MvcRazorCompileOnPublish` à `true`et inclure une référence de package à `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`. Les éléments suivants *.csproj* exemple met en évidence ces paramètres :

[!code-xml[Main](view-compilation\sample\MvcRazorCompileOnPublish.csproj?highlight=5,12)]

---

Préparer l’application pour un [dépendant du framework déploiement](/dotnet/core/deploying/#framework-dependent-deployments-fdd) en exécutant une commande telle que la suivante à la racine du projet :

```console
dotnet publish -c Release
```

A *< nom_projet >. PrecompiledViews.dll* fichier, qui contient les vues Razor compilés, est généré lors de la précompilation réussit. Par exemple, la capture d’écran ci-dessous illustre le contenu de *Index.cshtml* à l’intérieur de *WebApplication1.PrecompiledViews.dll*:

![Vues Razor à l’intérieur de DLL](view-compilation/_static/razor-views-in-dll.png)
