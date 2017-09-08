---
title: Utilisation de plusieurs environnements
author: ardalis
description: 
keywords: "ASP.NET Core, les paramètres d’environnement, ASPNETCORE_ENVIRONMENT"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: b5bba985-be12-4464-9a01-df3599b2a6f1
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/environments
ms.openlocfilehash: 5e41920c9745f903d33fa25922727e920c1efc26
ms.sourcegitcommit: 8f5277871eff86134ebf68d3737196cfd4a62c2c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2017
---
# <a name="working-with-multiple-environments"></a>Utilisation de plusieurs environnements

Par [Steve Smith](http://ardalis.com)

ASP.NET Core prend en charge pour contrôler le comportement de l’application dans différents environnements, tels que le développement, intermédiaire et de production. Variables d’environnement sont utilisées pour indiquer l’environnement d’exécution, ce qui permet de l’application doit être configuré pour cet environnement.

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample)

## <a name="development-staging-production"></a>Développement, intermédiaires, Production

ASP.NET Core fait référence à un particulier [variable d’environnement](https://github.com/aspnet/Home/wiki/Environment-Variables), `ASPNETCORE_ENVIRONMENT` pour décrire l’environnement de l’application est en cours d’exécution dans. Cette variable peut être définie à n’importe quelle valeur vous le souhaitez, mais trois valeurs sont utilisées par convention : `Development`, `Staging`, et `Production`. Vous trouverez ces valeurs utilisées dans les exemples et les modèles fournis avec ASP.NET Core.

Le paramètre actuel de l’environnement peut être détecté par programme à partir de dans votre application. En outre, vous pouvez utiliser l’environnement [d’assistance de balise](../mvc/views/tag-helpers/index.md) à inclure certaines sections de votre [affichage](../mvc/views/index.md) en fonction de l’environnement d’application actuel.

Remarque : Sur Windows et Mac OS, le nom de l’environnement spécifié respecte la casse. Si vous définissez la variable `Development` ou `development` ou `DEVELOPMENT` les résultats seront identiques. Toutefois, Linux est un **casse** système d’exploitation par défaut. Variables d’environnement, les noms de fichiers et paramètres requièrent le respect de la casse.

### <a name="development"></a>Développement

Il s’agit de l’environnement utilisé lors du développement d’une application. Il est généralement utilisé pour activer les fonctionnalités que vous ne souhaitez pas être disponibles lors de l’exécution de l’application en production, telles que la [page d’exception developer](xref:fundamentals/error-handling#the-developer-exception-page).

Si vous utilisez Visual Studio, l’environnement peut être configuré dans le profil de débogage de votre projet. Déboguer des profils de spécifier le [server](xref:fundamentals/servers/index) à utiliser lors du lancement de l’application et les variables d’environnement à définir. Votre projet peut avoir plusieurs profils de débogage qui définissent des variables d’environnement différemment. Gestion de ces profils à l’aide de la **déboguer** onglet de de votre projet application web **propriétés** menu. Les valeurs que vous définissez dans les propriétés du projet sont conservées dans le *launchSettings.json* fichier et vous pouvez également configurer des profils en modifiant ce fichier directement.

Le profil pour IIS Express est illustré ici :

![Variables d’environnement de définition de propriétés de projet](environments/_static/project-properties-debug.png)

Voici un `launchSettings.json` fichier qui inclut les profils pour `Development` et `Staging`:

[!code-json[Main](../fundamentals/environments/sample/src/Environments/Properties/launchSettings.json?highlight=15,22)]

Modifications apportées aux profils de projet ne peuvent pas prennent effet qu’après le redémarrage du serveur web utilisé (en particulier, Kestrel doit être redémarré avant qu’il détecte les modifications apportées à son environnement).

>[!WARNING]
> Variables d’environnement stockée dans *launchSettings.json* ne sont pas sécurisés de quelque manière et fera partie du référentiel de code source pour votre projet, si vous utilisez un. **Ne stockez jamais les informations d’identification ou d’autres données secrètes dans ce fichier.** Si vous avez besoin d’un emplacement pour stocker ces données, utilisez la *Secret Manager* outil décrit dans [stockage sécurisé des secrets d’application pendant le développement](../security/app-secrets.md#security-app-secrets).

### <a name="staging"></a>Mise en lots

Par convention, un `Staging` environnement est un environnement de préproduction utilisé pour le test final avant le déploiement en production. Dans l’idéal, les caractéristiques physiques doivent refléter celui de production, afin que tous les problèmes qui peuvent survenir en production figurer en premier dans l’environnement intermédiaire, où ils peuvent être traités sans impact sur les utilisateurs.

### <a name="production"></a>Production

Le `Production` environnement est l’environnement dans lequel s’exécute l’application quand elle est active et utilisée par les utilisateurs finaux. Cet environnement doit être configuré pour optimiser la sécurité, performances et la robustesse de l’application. Certains paramètres courants susceptibles de présenter un environnement de production qui seraient diffèrent de développement incluent :

* Activer la mise en cache

* Assurez-vous que toutes les ressources côté client sont fournies, réduire, potentiellement pris en charge à partir d’un CDN

* Désactiver le diagnostic ErrorPages

* Activer les pages d’erreur convivial

* Activer la journalisation et de surveillance de production (par exemple, [Application Insights](https://azure.microsoft.com/documentation/articles/app-insights-asp-net-five/))

Il n’est pas destiné à être une liste complète. Il est préférable d’éviter la diffusion des vérifications de l’environnement dans de nombreuses parties de votre application. Au lieu de cela, l’approche recommandée consiste à effectuer ces vérifications au sein de l’application `Startup` classes chaque fois que possible

## <a name="setting-the-environment"></a>Configuration de l’environnement

La méthode de configuration de l’environnement dépend du système d’exploitation.

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

**Web.config**

Consultez le *définition des variables d’environnement* section de la [référence de configuration ASP.NET Core Module](xref:hosting/aspnet-core-module#setting-environment-variables) rubrique.

**Par Pool d’applications IIS**

Si vous devez définir les variables d’environnement pour des applications qui s’exécutent dans des Pools d’applications isolées (pris en charge sur IIS 10.0 +), consultez le *commande AppCmd.exe* section de la [Variables d’environnement \< environmentVariables >](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) documentation de référence de rubrique dans IIS.

### <a name="macos"></a>MacOS
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

## <a name="determining-the-environment-at-runtime"></a>Détermination de l’environnement lors de l’exécution

Le `IHostingEnvironment` service fournit l’abstraction de base pour l’utilisation des environnements. Ce service est fourni par ASP.NET héberge de couche et peuvent être injectées dans votre logique de démarrage via [Injection de dépendance](dependency-injection.md). Le modèle de site web ASP.NET Core dans Visual Studio utilise cette approche pour charger des fichiers de configuration spécifiques à l’environnement (le cas échéant) et pour personnaliser la gestion des paramètres des erreurs de l’application. Dans les deux cas, ce comportement est obtenu en faisant référence à l’environnement spécifié actuellement en appelant `EnvironmentName` ou `IsEnvironment` sur l’instance de `IHostingEnvironment` passé dans la méthode appropriée.

> [!NOTE]
> Si vous avez besoin vérifier si l’application s’exécute dans un environnement spécifique, utilisez `env.IsEnvironment("environmentname")` , car il ignore la casse correctement (au lieu de vérifier si `env.EnvironmentName == "Development"` par exemple).

Par exemple, vous pouvez utiliser le code suivant dans votre méthode de configuration pour configurer la gestion d’erreur spécifique environnement :

[!code-csharp[Main](environments/sample/src/Environments/Startup.cs?range=19-30)]

Si l’application s’exécute dans un `Development` environnement, puis il permet la prise en charge du runtime nécessaire pour utiliser la fonctionnalité « BrowserLink » dans Visual Studio, les pages d’erreur spécifique au développement (qui doivent exécute généralement pas être en production) et erreur de base de données spéciale pages (qui fournissent un moyen d’appliquer des migrations et doivent donc uniquement être utilisées dans le développement). Sinon, si l’application n’est pas en cours d’exécution dans un environnement de développement, une page de gestion d’erreur standard est configurée pour être affichées en réponse à toutes les exceptions non gérées.

Vous devrez peut-être déterminer le contenu à envoyer au client lors de l’exécution, en fonction de l’environnement actuel. Par exemple, dans un environnement de développement vous généralement servez non réduites de scripts et les feuilles de style, ce qui rend le débogage. Les environnements de production et de test doivent traiter les versions réduites et généralement depuis un CDN. Ce faire, vous pouvez utiliser l’environnement [d’assistance de balise](../mvc/views/tag-helpers/intro.md). L’assistance de balise d’environnement qui restitue le contenu si l’environnement actuel correspond à l’un environnements spécifiés à l’aide de la `names` attribut.

[!code-html[Main](environments/sample/src/Environments/Views/Shared/_Layout.cshtml?range=13-22)]

Mise en route à l’aide de programmes d’assistance de balise dans votre application, consultez [Introduction aux programmes d’assistance de balise](../mvc/views/tag-helpers/intro.md).

## <a name="startup-conventions"></a>Conventions de démarrage

ASP.NET Core prend en charge une approche basée sur une convention à la configuration de démarrage d’une application basé sur l’environnement actuel. Vous pouvez également programmer contrôler comment votre application comporte conformément à l’environnement dans lequel il se trouve, ce qui vous permet de créer et gérer vos propres conventions.

Démarrage d’une application ASP.NET Core, la `Startup` classe est utilisée pour démarrer l’application, ses paramètres de configuration, un etc. de charge ([en savoir plus sur ASP.NET démarrage](startup.md)). Toutefois, s’il existe une classe nommée `Startup{EnvironmentName}` (par exemple `StartupDevelopment`) et le `ASPNETCORE_ENVIRONMENT` variable d’environnement correspondant à ce nom, puis qui `Startup` classe est utilisée à la place. Par conséquent, vous pouvez configurer `Startup` pour le développement, mais ils présentent un distinct `StartupProduction` qui seraient utilisés lors de l’application est exécutée en production. Ou vice versa.

> [!NOTE]
> Appel de `WebHostBuilder.UseStartup<TStartup>()` remplace les sections de configuration.

Outre l’utilisation totalement distincts `Startup` classe basée sur l’environnement actuel, vous pouvez également effectuer des ajustements à la configuration de l’application dans un `Startup` classe. Le `Configure()` et `ConfigureServices()` méthodes prennent en charge des versions spécifiques à l’environnement similaire à la `Startup` classe proprement dite, sous la forme `Configure{EnvironmentName}()` et `Configure{EnvironmentName}Services()`. Si vous définissez une méthode `ConfigureDevelopment()` elle sera appelée à la place de `Configure()` lorsque l’environnement est configuré pour le développement. De même, `ConfigureDevelopmentServices()` est appelée à la place de `ConfigureServices()` dans le même environnement.

## <a name="summary"></a>Résumé

ASP.NET Core fournit un certain nombre de conventions qui permettent aux développeurs de facilement contrôler le comportement de leurs applications dans différents environnements. Lorsque vous publiez une application à partir de développement dans l’environnement intermédiaire en production, variables d’environnement définies correctement pour l’environnement autoriser pour l’optimisation de l’application pour utiliser le débogage, de test ou de production, comme il convient.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Configuration](configuration.md)

* [Introduction aux applications d’assistance de balise](../mvc/views/tag-helpers/intro.md)
