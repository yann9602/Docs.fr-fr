---
title: Utilisation de plusieurs environnements dans ASP.NET Core
author: rick-anderson
description: "Découvrez comment ASP.NET Core fournit la prise en charge pour contrôler le comportement de l’application dans différents environnements."
ms.author: riande
manager: wpickett
ms.date: 12/25/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/environments
ms.openlocfilehash: 60a1543ce11d08490e6df0eb84f980672ecfe672
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="working-with-multiple-environments"></a>Utilisation de plusieurs environnements

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core prend en charge pour la définition du comportement de l’application lors de l’exécution avec les variables d’environnement.

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="environments"></a>Environnements

ASP.NET Core lit la variable d’environnement `ASPNETCORE_ENVIRONMENT` au démarrage de l’application et stocke cette valeur dans [IHostingEnvironment.EnvironmentName](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname?view=aspnetcore-2.0#Microsoft_AspNetCore_Hosting_IHostingEnvironment_EnvironmentName). `ASPNETCORE_ENVIRONMENT`peut être définie sur n’importe quelle valeur, mais [trois valeurs](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.environmentname?view=aspnetcore-2.0) sont pris en charge par l’infrastructure : [développement](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development?view=aspnetcore-2.0), [intermédiaire](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging?view=aspnetcore-2.0), et [Production](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production?view=aspnetcore-2.0). Si `ASPNETCORE_ENVIRONMENT` n’est pas défini, il prend par défaut `Production`.

[!code-csharp[Main](environments/sample/WebApp1/Startup.cs?name=snippet)]

Le code précédent :

* Appels [UseDeveloperExceptionPage](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_DeveloperExceptionPageExtensions_UseDeveloperExceptionPage_Microsoft_AspNetCore_Builder_IApplicationBuilder_) et [UseBrowserLink](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.browserlinkextensions.usebrowserlink?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_BrowserLinkExtensions_UseBrowserLink_Microsoft_AspNetCore_Builder_IApplicationBuilder_) lorsque `ASPNETCORE_ENVIRONMENT` a la valeur `Development`.
* Appels [UseExceptionHandler](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_ExceptionHandlerExtensions_UseExceptionHandler_Microsoft_AspNetCore_Builder_IApplicationBuilder_) lorsque la valeur de `ASPNETCORE_ENVIRONMENT` est définie à une des valeurs suivantes :

    * `Staging`
    * `Production`
    * `Staging_2`

Le [assistance de balise d’environnement ](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) utilise la valeur de `IHostingEnvironment.EnvironmentName` à inclure ou exclure le balisage dans l’élément :

[!code-html[Main](environments/sample/WebApp1/Pages/About.cshtml)]

Remarque : Sur Windows et macOS, valeurs et variables d’environnement ne respecte la casse. Variables d’environnement Linux et les valeurs sont **casse** par défaut.

### <a name="development"></a>Développement

L’environnement de développement peut activer des fonctionnalités qui ne doivent pas être exposées dans la production. Par exemple, les modèles ASP.NET Core activer la [page d’exception developer](xref:fundamentals/error-handling#the-developer-exception-page) dans l’environnement de développement.

L’environnement de développement de l’ordinateur local peut être définie dans le *Properties\launchSettings.json* fichier du projet. Définir des valeurs d’environnement dans *launchSettings.json* remplacent les valeurs définies dans l’environnement du système.

Le code XML suivant montre les trois profils d’un *launchSettings.json* fichier :

[!code-xml[Main](environments/sample/WebApp1/Properties/launchSettings.json?highlight=10,11,18,26)]

Lorsque l’application est lancée avec `dotnet run`, le premier profil avec `"commandName": "Project"` sera utilisé. La valeur de `commandName` Spécifie le serveur web pour lancer. `commandName`peut être :

* IIS Express
* IIS
* Projet (ce qui lance le Kestrel)

Lorsque vous lancez une application avec `dotnet run`:

* *launchSettings.json* est lu si elle est disponible. `environmentVariables`paramètres de *launchSettings.json* remplacer les variables d’environnement.
* L’environnement d’hébergement s’affiche.


La sortie suivante montre une application a démarré avec `dotnet run`:
```bash
PS C:\Webs\WebApp1> dotnet run
Using launch settings from C:\Webs\WebApp1\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Webs\WebApp1
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Visual Studio **déboguer** onglet fournit une interface graphique utilisateur pour modifier la *launchSettings.json* fichier :

![Variables d’environnement de définition de propriétés de projet](environments/_static/project-properties-debug.png)

Modifications apportées aux profils de projet peuvent prendront effet qu’après le redémarrage du serveur web. Kestrel doit être redémarré avant qu’il détecte les modifications apportées à son environnement.

>[!WARNING]
> *launchSettings.json* ne doit pas stocker des clés secrètes. Le [Secret gestionnaire](xref:security/app-secrets) peut être utilisé pour stocker des clés secrètes de développement local.

### <a name="production"></a>Production

L’environnement de production doit être configuré pour optimiser la sécurité, performances et la robustesse de l’application. Certains paramètres courants susceptibles de présenter un environnement de production qui seraient diffèrent de développement incluent :

* La mise en cache.
* Les ressources côté client sont fournies, réduites et potentiellement pris en charge à partir d’un CDN.
* Pages d’erreur de diagnostic désactivés.
* Pages d’erreur convivial activées.
* Journalisation de production et surveillance activé. Par exemple, [Application Insights](https://azure.microsoft.com/documentation/articles/app-insights-asp-net-five/).

## <a name="setting-the-environment"></a>Configuration de l’environnement

Il est souvent utile de définir un environnement spécifique pour le test. Si l’environnement n’est pas défini, il prend par défaut `Production` qui désactive la plupart des fonctionnalités de débogage.

La méthode de configuration de l’environnement dépend du système d’exploitation.

### <a name="azure"></a>Azure

Pour le service d’application Azure :

* Sélectionnez le **paramètres de l’Application** panneau.
* Ajouter la clé et la valeur de **paramètres de l’application**.


### <a name="windows"></a>Windows
Pour définir le `ASPNETCORE_ENVIRONMENT` pour la session active, si l’application est démarrée à l’aide de `dotnet run`, les commandes suivantes sont utilisées.

**Ligne de commande**
```
set ASPNETCORE_ENVIRONMENT=Development
```
**PowerShell**
```
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Ces commandes prennent effet uniquement pour la fenêtre active. Lorsque la fenêtre est fermée, le paramètre ASPNETCORE_ENVIRONMENT rétablit le paramètre par défaut ou la valeur de l’ordinateur. Pour définir la valeur globalement à l’ouverture de Windows le **le panneau de configuration** > **système** > **les paramètres système avancés** et ajouter ou modifier le `ASPNETCORE_ENVIRONMENT` valeur.

![Les propriétés avancées de système](environments/_static/systemsetting_environment.png)

![Variable d’environnement ASPNET Core](environments/_static/windows_aspnetcore_environment.png)


**web.config**

Consultez le *définition des variables d’environnement* section de la [référence de configuration ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) rubrique.

**Par Pool d’applications IIS**

Pour définir les variables d’environnement pour des applications qui s’exécutent dans des Pools d’applications isolées (pris en charge sur IIS 10.0 +), consultez le *commande AppCmd.exe* section de la [Variables d’environnement \< environmentVariables >](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) rubrique.

### <a name="macos"></a>macOS
Définition l’environnement actuel pour macOS peut être effectuée en ligne lors de l’exécution de l’application ;

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```
ou à l’aide de `export` pour la définir avant d’exécuter l’application.

```bash
export ASPNETCORE_ENVIRONMENT=Development
```
Variables d’environnement au niveau machine sont définies le *.bashrc* ou *.bash_profile* fichier. Modifiez le fichier à l’aide de n’importe quel éditeur de texte et ajoutez l’instruction suivante.

```
export ASPNETCORE_ENVIRONMENT=Development
```

### <a name="linux"></a>Linux
Pour les versions de Linux, utilisez le `export` commande à la ligne de commande pour la session en fonction des paramètres de variable et *bash_profile* fichier pour les paramètres d’environnement au niveau ordinateur.

### <a name="configuration-by-environment"></a>Configuration par environnement

Consultez [Configuration par environnement](xref:fundamentals/configuration/index#configuration-by-environment) pour plus d’informations.

<a name="startup-conventions"></a>
## <a name="environment-based-startup-class-and-methods"></a>Environnement en fonction de méthodes et la classe de démarrage.

Quand une application ASP.NET Core démarre, le [classe Startup](xref:fundamentals/startup) amorce l’application. Si une classe `Startup{EnvironmentName}` existe, que la classe sera appelée pour que `EnvironmentName`:

[!code-csharp[Main](environments/sample/WebApp1/StartupDev.cs?name=snippet&highlight=1)]

Remarque : L’appel [WebHostBuilder.UseStartup<TStartup> ](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup?view=aspnetcore-2.0#Microsoft_AspNetCore_Hosting_WebHostBuilderExtensions_UseStartup__1_Microsoft_AspNetCore_Hosting_IWebHostBuilder_) remplace les sections de configuration.

[Configurer](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure?view=aspnetcore-2.0#Microsoft_AspNetCore_Hosting_StartupBase_Configure_Microsoft_AspNetCore_Builder_IApplicationBuilder_) et [ConfigureServices](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices?view=aspnetcore-2.0) prennent en charge des versions spécifiques d’environnement du formulaire `Configure{EnvironmentName}` et `Configure{EnvironmentName}Services`:

[!code-csharp[Main](environments/sample/WebApp1/Startup.cs?name=snippet_all&highlight=15,37)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [Démarrage d’une application](xref:fundamentals/startup)
* [Configuration](xref:fundamentals/configuration/index)
* [IHostingEnvironment.EnvironmentName](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname?view=aspnetcore-2.0#Microsoft_AspNetCore_Hosting_IHostingEnvironment_EnvironmentName)
