---
title: "Référence d’erreurs commune de Service d’applications Azure et d’IIS avec ASP.NET Core"
author: guardrex
description: "Distinguer les erreurs courantes lors de l’hébergement d’applications ASP.NET Core sur IIS et le Service d’applications Azure."
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 03/13/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 53b59045751153cd858e13769b5b42d5700e26d4
ms.sourcegitcommit: 7a87d66cf1d01febe6635c7306f2f679434901d1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a>Référence d’erreurs commune de Service d’applications Azure et d’IIS avec ASP.NET Core

Par [Luke Latham](https://github.com/guardrex)

Cette liste d’erreurs n’est pas exhaustive. Si vous rencontrez une erreur non répertoriée ici, [ouvrir un nouveau problème](https://github.com/aspnet/Docs/issues/new) avec des instructions détaillées pour reproduire l’erreur.

Collectez les informations suivantes :

* Comportement du navigateur
* Entrées de journal des événements d’application
* Entrées de journal stdout ASP.NET Module Core

Comparer les informations pour les erreurs courantes. Si une correspondance est trouvée, suivez les conseils de dépannage.

## <a name="installer-unable-to-obtain-vc-redistributable"></a>Le programme d’installation ne parvient pas à obtenir VC++ Redistributable

* **Exception de programme d’installation :** 0x80072efd ou 0x80072f76 - Erreur non spécifiée

* **Exception du journal d’installation&#8224;:** Erreur 0x80072efd ou 0x80072f76 : Impossible d’exécuter le package EXE

  &#8224;Le journal se trouve dans C:\Users\\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{timestamp}.log.

Résolution des problèmes :

* Si le système n’a pas accès à Internet lors de l’installation du bundle d’hébergement du serveur, cette exception se produit quand le programme d’installation ne peut pas obtenir *Microsoft Visual C++ 2015 Redistributable*. Obtenir un programme d’installation à partir de la [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840). Si le programme d’installation échoue, le serveur ne peut pas recevoir le runtime .NET Core requis pour héberger un déploiement dépendant du framework (lecteur de disquette). Si vous hébergez un lecteur de disquette, vérifiez que le runtime est installé dans les programmes &amp; fonctionnalités. Si nécessaire, obtenir un programme d’installation du runtime à partir de [téléchargements .NET](https://www.microsoft.com/net/download/core). Après avoir installé le runtime, redémarrez le système ou IIS en exécutant **net stop was /y** suivi de **net start w3svc** à partir d’une invite de commandes.

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>La mise à niveau du système d’exploitation a supprimé le Module ASP.NET Core 32 bits

* **Journal des applications :** Le fichier DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** du module n’a pas pu se charger. Les données sont erronées.

Résolution des problèmes :

* Les fichiers autres que les fichiers de système d’exploitation dans le répertoire **C:\Windows\SysWOW64\inetsrv** ne sont pas conservés pendant la mise à niveau du système d’exploitation. Si le Module de base ASP.NET est installé avant une mise à niveau du système d’exploitation et puis n’importe quel pool d’applications s’exécute en mode 32 bits après une mise à niveau du système d’exploitation, vous rencontrerez ce problème. Après une mise à niveau du système d’exploitation, réparez le Module ASP.NET Core. Consultez [Installer le bundle d’hébergement .NET Core Windows Server](xref:host-and-deploy/iis/index#install-the-net-core-windows-server-hosting-bundle). Sélectionnez **réparation** lorsque le programme d’installation est exécuté.

## <a name="platform-conflicts-with-rid"></a>Conflits de plateforme avec RID

* **Navigateur :** Erreur HTTP 502.5 - Échec du processus

* **Journal des applications :** Application « MACHINE/WEBROOT/APPHOST / {ASSEMBLY}' avec racine physique ' C:\{chemin d’accès}\' n’a pas pu démarrer le processus avec la ligne de commande ' « C:\\{chemin d’accès} {assembly}. {} exe | dll} » ', code d’erreur = ' 0 x 80004005 : ff.

* **Journal du Module ASP.NET Core :** Exception non gérée : System.BadImageFormatException : Impossible de charger fichier ou assembly '.dll {assembly}'. Tentative de chargement d’un programme au format incorrect.

Résolution des problèmes :

* Vérifiez que l’application s’exécute localement sur Kestrel. Un échec de processus peut être dû à un problème au niveau de l’application. Pour plus d’informations, consultez [dépannage](xref:host-and-deploy/iis/troubleshoot).

* Vérifiez que le `<PlatformTarget>` dans les *.csproj* ne sont en conflit avec le numéro du RID. Par exemple, ne spécifiez pas un `<PlatformTarget>` de `x86` et publier avec un RID de `win10-x64`, à l’aide de *dotnet publier - c - r de version Windows 10-x64* ou en définissant le `<RuntimeIdentifiers>` dans le *.csproj*  à `win10-x64`. Le projet est publié sans avertissement ni erreur, mais il échoue avec les exceptions journalisées ci-dessus sur le système.

* Si cette exception se produit pour un déploiement d’applications Azure lors de la mise à niveau d’une application et de déploiement de nouveaux assemblys, supprimez manuellement tous les fichiers du déploiement précédent. Le fait de laisser des assemblys incompatibles peut provoquer une exception `System.BadImageFormatException` lors du déploiement d’une application mise à niveau.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>Point de terminaison d’URI incorrect ou site web arrêté

* **Navigateur :** ERR_CONNECTION_REFUSED

* **Journal des applications :** Aucune entrée

* **Journal du Module ASP.NET Core :** Fichier journal non créé

Résolution des problèmes :

* Vérifiez que le point de terminaison de l’URI correct pour l’application est utilisée. Vérifiez les liaisons.

* Vérifiez que le site Web IIS n’est pas dans le *arrêté* état.

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>Fonctionnalités de serveur W3SVC ou CoreWebEngine désactivées

* **Exception de système d’exploitation :** Les fonctionnalités CoreWebEngine et W3SVC d’IIS 7.0 doivent être installées pour utiliser le Module ASP.NET Core.

Résolution des problèmes :

* Vérifiez que le rôle approprié et les fonctionnalités sont activées. Consultez [Configuration d’IIS](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Chemin d’accès physique du site Web incorrecte ou manquante de l’application

* **Navigateur :** 403 - Interdit : accès refusé **--ou--** 403.14 - Interdit : Le serveur Web est configuré pour ne pas afficher le contenu de ce répertoire.

* **Journal des applications :** Aucune entrée

* **Journal du Module ASP.NET Core :** Fichier journal non créé

Résolution des problèmes :

* Consultez le site Web IIS **les paramètres de base** et le dossier application physique. Vérifiez que l’application est dans le dossier sur le site Web IIS **chemin d’accès physique**.

## <a name="incorrect-role-module-not-installed-or-incorrect-permissions"></a>Rôle incorrect, module non installé ou autorisations incorrectes

* **Navigateur :** 500.19 Erreur interne du serveur : Impossible d’accéder à la page que vous avez demandée, car les données de configuration connexes relatives à la page ne sont pas valides.

* **Journal des applications :** Aucune entrée

* **Journal du Module ASP.NET Core :** Fichier journal non créé

Résolution des problèmes :

* Vérifiez que le rôle approprié est activé. Consultez [Configuration d’IIS](xref:host-and-deploy/iis/index#iis-configuration).

* Accédez à **Programmes &amp; Fonctionnalités** et vérifiez que le **Module Microsoft ASP.NET Core** a été installé. Si le **Module Microsoft ASP.NET Core** n’est pas présent dans la liste des programmes installés, installez-le. Consultez [Installer le bundle d’hébergement .NET Core Windows Server](xref:host-and-deploy/iis/index#install-the-net-core-windows-server-hosting-bundle).

* Assurez-vous que le **Pool d’applications** > **modèle de processus** > **identité** a la valeur **ApplicationPoolIdentity** ou l’identité personnalisée dispose des autorisations appropriées pour accéder au dossier de déploiement de l’application.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>processPath incorrect, variable de chemin manquante, bundle d’hébergement non installé, système/IIS non redémarré, VC++ Redistributable non installé ou violation d’accès dotnet.exe

* **Navigateur :** Erreur HTTP 502.5 - Échec du processus

* **Journal des applications :** Application « MACHINE/WEBROOT/APPHOST / {ASSEMBLY}' avec racine physique ' C:\\{chemin d’accès}\' n’a pas pu démarrer le processus avec la ligne de commande ' ».\{ assembly} .exe » ', code d’erreur = ' 0 x 80070002 : 0.

* **Journal du Module ASP.NET Core :** Fichier journal créé mais vide

Résolution des problèmes :

* Vérifiez que l’application s’exécute localement sur Kestrel. Un échec de processus peut être dû à un problème au niveau de l’application. Pour plus d’informations, consultez [dépannage](xref:host-and-deploy/iis/troubleshoot).

* Vérifiez le *processPath* de l’attribut le `<aspNetCore>` élément *web.config* pour confirmer qu’il s’agit *dotnet* pour un déploiement de framework dépendant (lecteur de disquette) ou un *. \{assembly} .exe* pour un déploiement autonome (SCD).

* Pour un déploiement dépendant du framework, *dotnet.exe* peut ne pas être accessible via les paramètres PATH. Vérifiez que *C:\Program Files\dotnet\* existe dans les paramètres PATH du système.

* Pour un déploiement dépendant du framework, *dotnet.exe* peut ne pas être accessible pour l’identité d’utilisateur du pool d’applications. Vérifiez que l’identité utilisateur du pool d’applications a accès au répertoire *C:\Program Files\dotnet*. Confirmer qu’il n’y a aucune règle de refus configuré pour l’identité de l’utilisateur du pool d’applications sur le *C:\Program Files\dotnet* et les répertoires de l’application.

* Un lecteur de disquette ont été déployé et .NET Core est installé sans redémarrer IIS. Redémarrez le serveur ou IIS en exécutant **net stop was /y** suivi de **net start w3svc** à partir d’une invite de commandes.

* Un lecteur de disquette peut avoir été déployé sans installer le runtime .NET Core sur le système hôte. Si le runtime .NET Core n’a pas été installé, exécutez le **.NET Core Windows serveur qui héberge les programme d’installation de l’offre groupée** sur le système. Consultez [Installer le bundle d’hébergement .NET Core Windows Server](xref:host-and-deploy/iis/index#install-the-net-core-windows-server-hosting-bundle). Si vous tentez d’installer le runtime .NET Core sur un système sans connexion Internet, obtenir l’exécution à partir de [téléchargements .NET](https://www.microsoft.com/net/download/core) et exécutez le programme d’installation de groupe hôte pour installer le Module de base ASP.NET. Terminez l’installation en redémarrant le système ou IIS en exécutant **net stop was /y** suivi de **net start w3svc** à partir d’une invite de commandes.

* Un lecteur de disquette ont été déployée et le *redistribuable Microsoft Visual C++ 2015 (x64)* n’est pas installé sur le système. Obtenir un programme d’installation à partir de la [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Arguments incorrects de l’élément \<aspNetCore\>

* **Navigateur :** Erreur HTTP 502.5 - Échec du processus

* **Journal des applications :** Application « MACHINE/WEBROOT/APPHOST / {ASSEMBLY}' avec racine physique ' C:\\{chemin d’accès}\' n’a pas pu démarrer le processus avec la ligne de commande ' « dotnet ».\{ assembly} .dll ', code d’erreur = ' 0 x 80004005 : 80008081.

* **Journal du Module ASP.NET Core :** l’application à exécuter n’existe pas : ' chemin d’accès\{assembly} .dll '

Résolution des problèmes :

* Vérifiez que l’application s’exécute localement sur Kestrel. Un échec de processus peut être dû à un problème au niveau de l’application. Pour plus d’informations, consultez [dépannage](xref:host-and-deploy/iis/troubleshoot).

* Examiner la *arguments* de l’attribut le `<aspNetCore>` élément *web.config* pour confirmer qu’il s’agit (a) *.\{ assembly} .dll* pour un déploiement dépendant du framework (lecteur de disquette) ; ou (b) est absent, une chaîne vide (*arguments = » «*), ou une liste d’arguments de l’application (*arguments = « arg1, arg2,... »*) pour un déploiement autonome (SCD).

## <a name="missing-net-framework-version"></a>Version du .NET Framework manquante

* **Navigateur :** 502.3 Passerelle incorrecte : Une erreur de connexion s’est produite lors de la tentative de routage de la requête.

* **Journal des applications :** ErrorCode = Application « MACHINE/WEBROOT/APPHOST / {ASSEMBLY}' avec racine physique ' C:\\{chemin d’accès}\' n’a pas pu démarrer le processus avec la ligne de commande ' « dotnet ».\{ assembly} .dll ', code d’erreur = ' 0 x 80004005 : 80008081.

* **Journal du Module ASP.NET Core :** Exception d’assembly, de méthode ou de fichier manquante. La méthode, le fichier ou l’assembly spécifié dans l’exception est une méthode, un fichier ou un assembly .NET Framework.

Résolution des problèmes :

* Installez la version de .NET Framework manquante sur le système.

* Pour un déploiement dépendant du framework (lecteur de disquette), vérifiez que le runtime approprié installé sur le système. Si le projet est mis à niveau à partir de 1.1 à 2.0, déployé sur le système hôte, et cette exception entraîne, vérifiez que le framework 2.0 est sur le système hôte.

## <a name="stopped-application-pool"></a>Pool d’applications arrêté

* **Navigateur :** 503 Service non disponible

* **Journal des applications :** Aucune entrée

* **Journal du Module ASP.NET Core :** Fichier journal non créé

Résolution des problèmes

* Vérifiez que le Pool d’applications n’est pas dans le *arrêté* état.

## <a name="iis-integration-middleware-not-implemented"></a>L’intergiciel d’intégration IIS n’est pas implémenté

* **Navigateur :** Erreur HTTP 502.5 - Échec du processus

* **Journal des applications :** Application « MACHINE/WEBROOT/APPHOST / {ASSEMBLY}' avec racine physique ' C:\\{chemin d’accès}\' créé des processus avec la ligne de commande ' » C:\\{chemin d’accès}\{assembly}. {} exe | dll} » ' mais est tombé en panne ou n’a pas de réponse ou ne pas écouter sur le port spécifié '{PORT}', code d’erreur = '0x800705b4'

* **Journal du Module ASP.NET Core :** Fichier journal créé et indiquant un fonctionnement normal.

Résolution des problèmes

* Vérifiez que l’application s’exécute localement sur Kestrel. Un échec de processus peut être dû à un problème au niveau de l’application. Pour plus d’informations, consultez [dépannage](xref:host-and-deploy/iis/troubleshoot).

* Vérifiez que soit :
  * L’intergiciel (middleware) intégration des services Internet est referencedby appelant le `UseIISIntegration` méthode sur l’application `WebHostBuilder` (ASP.NET Core 1.x)
  * Les applications utilise le `CreateDefaultBuilder` (méthode) (ASP.NET Core 2.x).
  
  Consultez [Hébergement dans ASP.NET Core](xref:fundamentals/hosting) pour plus d’informations.

## <a name="sub-application-includes-a-handlers-section"></a>La sous-application inclut une section \<handlers\>

* **Navigateur :** Erreur HTTP 500.19 : Erreur interne du serveur

* **Journal des applications :** Aucune entrée

* **Journal du Module ASP.NET Core :** fichier journal créé et indique un fonctionnement normal de l’application racine. Fichier journal ne créé pas pour l’application de sub.

Résolution des problèmes

* Vérifiez que le fichier *web.config* de la sous-application n’inclut pas de section `<handlers>`.

## <a name="application-configuration-general-issue"></a>Problème général lié à la configuration d’application

* **Navigateur :** Erreur HTTP 502.5 - Échec du processus

* **Journal des applications :** Application « MACHINE/WEBROOT/APPHOST / {ASSEMBLY}' avec racine physique ' C:\\{chemin d’accès}\' créé des processus avec la ligne de commande ' » C:\\{chemin d’accès}\{assembly}. {} exe | dll} » ' mais est tombé en panne ou n’a pas de réponse ou ne pas écouter sur le port spécifié '{PORT}', code d’erreur = '0x800705b4'

* **Journal du Module ASP.NET Core :** Fichier journal créé mais vide

Résolution des problèmes

* Cette exception générale indique que le processus a échoué Démarrer, probablement en raison d’un problème de configuration d’application. Référence à [Structure de répertoires](xref:host-and-deploy/directory-structure), vérifiez que l’application déployée des fichiers et dossiers sont appropriées et que les fichiers de configuration de l’application sont présents et contiennent les paramètres corrects de l’application et l’environnement. Pour plus d’informations, consultez [dépannage](xref:host-and-deploy/iis/troubleshoot).
