---
title: Module ASP.NET Core
author: tdykstra
description: "Introduit le Module de base ASP.NET (ANCM), un module d’IIS qui permet au serveur web Kestrel utiliser IIS ou IIS Express comme serveur proxy inverse."
keywords: Module de base Express,ASP.NET ASP.NET Core, IIS, IIS, UseIISIntegration
ms.author: tdykstra
manager: wpickett
ms.date: 08/03/2017
ms.topic: article
ms.assetid: 4661af33-34c5-4d71-93a0-8c7632f43580
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/aspnet-core-module
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5eef9405c0c3d219755d7cffa5d45c3df45ddb5c
ms.sourcegitcommit: 12e5194936b7e820efc5505a2d5d4f84e88eb5ef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="introduction-to-aspnet-core-module"></a>Introduction au Module ASP.NET Core

Par [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), et [Chris Ross](https://github.com/Tratcher) 

Module de base ASP.NET (ANCM) vous permet d’exécuter des applications derrière IIS, ASP.NET Core à l’aide d’IIS pour qu’il est bon pour (sécurité, la facilité de gestion et beaucoup plus) et à l’aide de [Kestrel](kestrel.md) pour qu’il est bon pour (qui est très rapide) et l’obtention de la avantages de ces deux technologies en même temps. **ANCM fonctionne uniquement avec Kestrel ; Il n’est pas compatible avec WebListener (dans ASP.NET Core 1.x) ou HTTP.sys (dans 2.x).** 

Versions de Windows prises en charge :

* Windows 7 et Windows Server 2008 R2 et versions ultérieures

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/aspnet-core-module/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="what-aspnet-core-module-does"></a>Ce que fait le Module de base ASP.NET

ANCM est un module IIS natif qui intercepte dans le pipeline d’IIS et redirige le trafic vers le serveur principal application ASP.NET Core. La plupart des autres modules, telles que l’authentification windows, obtenir une chance de s’exécuter. ANCM prend uniquement le contrôle lorsqu’un gestionnaire est sélectionné pour la demande et de mappage de gestionnaire est défini dans l’application *web.config* fichier.

Étant donné que les applications ASP.NET Core s’exécutées dans un processus distincts du processus de travail IIS, ANCM traite également gestion. ANCM démarre le processus de l’application ASP.NET Core lors de la première demande arrive et qu’il redémarre lorsque qu’elle s’arrête. Il s’agit essentiellement le même comportement que les applications ASP.NET classiques qui s’exécutent in-process dans IIS et sont gérés par le service WAS (Windows Activation Service).

Voici un diagramme qui illustre les relations entre les applications IIS et ASP.NET Core ANCM.

![Module ASP.NET Core](aspnet-core-module/_static/ancm.png)

Les demandes provenant du Web et appuyez sur le pilote de Http.Sys en mode noyau qui achemine les dans IIS sur le port principal (80) ou le port SSL (443). ANCM transfère les demandes à l’application ASP.NET Core sur le port HTTP configuré pour l’application, qui est le port 80/443.

Kestrel écoute le trafic en provenance de ANCM.  ANCM Spécifie le port via une variable d’environnement au démarrage et la [UseIISIntegration](#call-useiisintegration) méthode configure le serveur pour écouter sur `http://localhost:{port}`. Il existe des vérifications supplémentaires pour rejeter les demandes non à partir de ANCM. (ANCM ne prend pas en charge HTTPS de transfert, même si IIS reçoit via le protocole HTTPS, les demandes sont transférées via HTTP.)

Kestrel récupère les demandes dans ANCM et les transmet au pipeline d’intergiciel (middleware) ASP.NET Core, qui les traite, puis les transfère en tant que `HttpContext` instances à la logique d’application. Réponses de l’application sont ensuite passées à IIS, qui pousse les arrière au client qui a lancé les demandes HTTP.

ANCM a d’autres fonctions :

* Définit les variables d’environnement.
* Journaux `stdout` au stockage de fichiers de sortie.
* Transfère les jetons d’authentification Windows.

## <a name="how-to-use-ancm-in-aspnet-core-apps"></a>Comment utiliser ANCM dans les applications ASP.NET Core

Cette section fournit une vue d’ensemble du processus de configuration d’un serveur IIS et d’application ASP.NET Core. Pour obtenir des instructions détaillées, consultez [hôte sous Windows avec IIS](xref:host-and-deploy/iis/index).

### <a name="install-ancm"></a>Installer ANCM


Le Module de base ASP.NET doit être installé dans IIS sur vos serveurs et dans IIS Express sur vos ordinateurs de développement. Pour les serveurs, ANCM est inclus dans le [.NET Core Windows serveur qui héberge le groupe](https://aka.ms/dotnetcore-2-windowshosting). Pour les ordinateurs de développement, Visual Studio installe automatiquement ANCM dans IIS Express et IIS s’il est déjà installé sur l’ordinateur.

### <a name="install-the-iisintegration-nuget-package"></a>Installez le package NuGet de IISIntegration

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Le [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) package est inclus dans les metapackages ASP.NET Core ([Microsoft.AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore/) et [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) ). Si vous n’utilisez pas un de le metapackages, installez `Microsoft.AspNetCore.Server.IISIntegration` séparément. Le `IISIntegration` package est un Pack d’interopérabilité qui lit les variables d’environnement ANCM pour configurer votre application de diffusion. Les variables d’environnement fournissent des informations de configuration, tels que le port d’écoute. 

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Dans votre application, vous devez installer [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/). Le `IISIntegration` package est un Pack d’interopérabilité qui lit les variables d’environnement ANCM pour configurer votre application de diffusion. Les variables d’environnement fournissent des informations de configuration, tels que le port d’écoute. 

---

### <a name="call-useiisintegration"></a>Appel UseIISIntegration

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Le `UseIISIntegration` méthode d’extension sur [ `WebHostBuilder` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder) est appelé automatiquement lorsque vous exécutez avec IIS.

Si vous n’utilisez pas un de le metapackages ASP.NET Core et que vous n’avez pas installé le `Microsoft.AspNetCore.Server.IISIntegration` package, vous obtenez une erreur d’exécution. Si vous appelez `UseIISIntegration` explicitement, vous obtenez une erreur de compilation si le package n’est pas installé.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Dans votre application `Main` méthode, appelez le `UseIISIntegration` méthode d’extension sur [ `WebHostBuilder` ](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder). 

[!code-csharp[](aspnet-core-module/sample/Program.cs?name=snippet_Main&highlight=12)]

---

Le `UseIISIntegration` méthode recherche de variables d’environnement qui définit des ANCM et non-ops si elles sont introuvables. Ce comportement facilite les scénarios de développement et de test sur macOS ou Linux et de déploiement sur un serveur qui exécute IIS. Lors de l’exécution sur macOS ou Linux, Kestrel joue le rôle serveur web ; mais lorsque l’application est déployée sur l’environnement d’IIS, il utilise automatiquement ANCM et IIS.

### <a name="ancm-port-binding-overrides-other-port-bindings"></a>Liaison de port ANCM remplace d’autres liaisons de port

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

ANCM génère un port dynamique pour affecter au processus principal. Le `UseIISIntegration` méthode récupère ce port dynamique et le configure Kestrel pour écouter sur `http://locahost:{dynamicPort}/`. Il substitue à d’autres configurations d’URL, tels que les appels à `UseUrls` ou [API d’écoute de Kestrel](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration). Par conséquent, vous n’avez pas besoin d’appeler `UseUrls` ou de Kestrel `Listen` API lorsque vous utilisez ANCM. Si vous n’appelez pas `UseUrls` ou `Listen`, Kestrel est à l’écoute sur le port que vous spécifiez lorsque vous exécutez l’application sans IIS.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

ANCM génère un port dynamique pour affecter au processus principal. Le `UseIISIntegration` méthode récupère ce port dynamique et le configure Kestrel pour écouter sur `http://locahost:{dynamicPort}/`. Il substitue à d’autres configurations d’URL, tels que les appels à `UseUrls`. Par conséquent, vous n’avez pas besoin d’appeler `UseUrls` lorsque vous utilisez ANCM. Si vous n’appelez pas `UseUrls`, Kestrel est à l’écoute sur le port que vous spécifiez lorsque vous exécutez l’application sans IIS.

Dans ASP.NET Core 1.0, si vous appelez `UseUrls`, appelez-le **avant** vous appelez `UseIISIntegration` afin que le port configuré ANCM n’est pas remplacé. Cet ordre d’appel n’est pas requis dans ASP.NET version 1.1 de noyaux, car le paramètre ANCM remplace `UseUrls`.

---

### <a name="configure-ancm-options-in-webconfig"></a>Configurer les options de ANCM dans le fichier Web.config

Configuration pour le Module de base ASP.NET est stockée dans le *web.config* fichier qui se trouve dans le dossier racine de l’application. Point de paramètres de ce fichier à la commande de démarrage et les arguments que vous démarrez votre application ASP.NET Core. Pour exemple *web.config* code et obtenir des conseils sur les options de configuration, consultez [référence de Configuration du Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

### <a name="run-with-iis-express-in-development"></a>Exécuter avec IIS Express dans le développement

IIS Express peut être lancé par Visual Studio en utilisant le profil par défaut défini par les modèles ASP.NET Core.

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Configuration du serveur proxy utilise le protocole HTTP et un jeton d’appariement

Le proxy créé entre le ANCM et Kestrel utilise le protocole HTTP. À l’aide de HTTP est une optimisation des performances d’où le trafic entre le ANCM et Kestrel a lieu sur une adresse de bouclage hors de l’interface réseau. Il n’existe aucun risque d’écoute clandestine le trafic entre le ANCM et Kestrel à partir d’un emplacement sur le serveur.

Un jeton d’appariement est utilisé pour garantir que les demandes reçues par Kestrel ont été traitées par IIS et n’avez pas provenir d’une autre source. Le jeton d’appariement est créé et défini dans une variable d’environnement (`ASPNETCORE_TOKEN`) par le ANCM. Le jeton d’appariement est également défini dans un en-tête (`MSAspNetCoreToken`) sur toutes les demandes traitées. Intergiciel (middleware) IIS vérifie chaque demande qu’il reçoit pour confirmer que la valeur d’en-tête de jeton appariement correspond à la valeur de variable d’environnement. Si les valeurs de jeton ne correspondent pas, la demande est connectée et rejetée. La variable d’environnement jeton appariement et le trafic entre le ANCM et Kestrel ne sont pas accessibles à partir d’un emplacement sur le serveur. Sans connaître la valeur du jeton appariement, une personne malveillante ne peuvent pas soumettre des demandes de contournent la vérification de l’intergiciel (middleware) IIS.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, reportez-vous aux ressources suivantes :

* [Exemple d’application pour cet article](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/aspnet-core-module/sample)
* [Code source de Module de base ASP.NET](https://github.com/aspnet/AspNetCoreModule)
* [Informations de référence sur la configuration du module ASP.NET Core](xref:host-and-deploy/aspnet-core-module)
* [Héberger sur Windows avec IIS](xref:host-and-deploy/iis/index)
