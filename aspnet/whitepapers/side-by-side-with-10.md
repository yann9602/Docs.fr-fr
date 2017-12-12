---
uid: whitepapers/side-by-side-with-10
title: "L’exécution d’ASP.NET côte à côte du .NET Framework 1.0 et 1.1 | Documents Microsoft"
author: rick-anderson
description: "Ce livre blanc décrit comment installer .NET 1.0 et 1.1 de .NET sur votre ordinateur, en permettant à une application Web ASP.NET pour s’exécuter sur des versions de l’encadrer..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/10/2010
ms.topic: article
ms.assetid: bdea2003-e964-4db5-9092-d56cc7560616
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /whitepapers/side-by-side-with-10
msc.type: content
ms.openlocfilehash: 939b04d440955b184bf5f4c40a2ef8175641edfa
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-side-by-side-execution-of-net-framework-10-and-11"></a>Exécution d’ASP.NET côte à côte du .NET Framework 1.0 et 1.1
====================
> Ce livre blanc décrit comment installer .NET 1.0 et 1.1 de .NET sur votre ordinateur, ce qui permet une application Web ASP.NET pour s’exécuter sur des versions du framework.
> 
> S’applique à ASP.NET 1.0 et 1.1 d’ASP.NET.


Dans ASP.NET, on dit des applications à s’exécuter côte à côte lorsqu’elles sont installées sur le même ordinateur, mais utilisent des versions différentes du .NET Framework. La rubrique suivante décrit comment configurer des applications ASP.NET pour une exécution côte à côte et fournit les étapes détaillées pour :

- [Mettre à jour le mappage de votre application Web pour la version 1.0 du .NET Framework lors de l’installation](#1)
- [Mapper une application Web vers une version spécifique du .NET Framework](#2)
- [Rechercher la version du .NET Framework qui utilise un site Web](#3)

En règle générale, lorsqu’un composant ou une application est mise à jour sur un ordinateur, l’ancienne version est supprimée et remplacée par la version plus récente. Si la nouvelle version n’est pas compatible avec la version précédente, cela arrête généralement d’autres applications qui utilisent le composant ou l’application. Le .NET Framework prend en charge l’exécution côte à côte, ce qui permet à plusieurs versions d’un assembly ou d’une application soit installée sur le même ordinateur en même temps. Étant donné que plusieurs versions peuvent être installées simultanément, les applications gérées peuvent sélectionner la version à utiliser sans affecter les applications qui utilisent une version différente.

Par défaut, lors de l’installation du .NET Framework version 1.1, toutes les applications ASP.NET existantes sont automatiquement reconfigurées pour utiliser la dernière version du .NET Framework. Si vous ne souhaitez pas que vos applications ASP.NET par défaut pour le .NET Framework 1.1, cliquez sur [ici](#1) pour savoir comment éviter ce problème lors de l’installation.

Si vous mettez à jour votre serveur Web vers la version 1.1 de .NET Framework et que vous souhaitez une ou plusieurs applications Web pour exécuter le .NET Framework 1.0, vous devez mettre à jour le mappage de Script de Internet Information Services (IIS). Le mappage du script est le mécanisme pour mapper l’extension de fichier .aspx pour une application Web spécifique à une version du .NET Framework. Cliquez sur [ici](#2) pour savoir comment mapper une application Web vers une version spécifique du .NET Framework.

Vous pouvez utiliser le Gestionnaire d’informations Internet ou de l’outil ASP.NET IIS Registration (Aspnet\_regiis.exe) pour rechercher la version de .NET Framework s’exécute une application Web particulière. Cliquez sur [ici](#3) pour savoir comment rechercher la version du .NET Framework qui utilise un site Web.

Une importation pas prises en compte lors de la migration vers la version 1.1 de .NET Framework est que chaque version du .NET Framework utilise son propre fichier Machine.config. Par conséquent, si un administrateur Web a apporté des modifications au fichier Machine.config, ces modifications doivent être migrées dans le fichier Machine.config de .NET Framework 1.1.

<a id="1"></a>

## <a name="maintaining-your-web-applications-mapping-to-net-framework-10-during-installation"></a>Gestion de mappage de votre application Web pour .NET Framework 1.0 pendant l’installation

Par défaut, toutes les applications ASP.NET existantes sont automatiquement reconfigurées pendant l’installation pour utiliser la nouvelle version du .NET Framework. À l’aide de la version la plus récente du .NET Framework, les applications peuvent tirer pleinement parti des améliorations et nouvelles fonctionnalités incluses dans la nouvelle version. En même temps, l’administrateur Web, qui souhaitent un contrôle granulaire sur les applications qui sont mis à jour, peut empêcher le remappage automatique de toutes les applications ASP.NET existantes lors de l’installation du .NET Framework.

Pour éviter le remappage automatique de l’application ASP.NET à la version la plus récente du .NET Framework, l’administrateur Web peut utiliser l’option de ligne de commande /noaspupgrade avec le programme d’installation de Dotnetfx.exe.

**Pour empêcher le remappage total d’application ASP.NET pour une version plus récente**

1. Accédez à **Démarrer**.
2. Cliquez sur **exécuter**.
3. Tapez **cmd**.
4. Cliquez sur **OK**.  
  
    ![](side-by-side-with-10/_static/image1.gif)
5. À partir de l’invite de commandes, tapez la ligne suivante pour démarrer l’installation du .NET Framework : **/c: de Dotnetfx.exe « installer /noaspupgrade ?**.  
  
    ![](side-by-side-with-10/_static/image2.gif)
6. Cliquez sur **Oui** dans le programme d’installation de Microsoft .NET Framework 1.1. Démarre le processus d’installation du .NET Framework 1.1.  
  
    ![](side-by-side-with-10/_static/image3.gif)

<a id="2"></a>

## <a name="map-a-web-application-to-a-specific-version-of-the-net-framework"></a>Mapper une application Web vers une version spécifique du .NET Framework

Chaque version du .NET Framework inclut une version de l’outil ASP.NET IIS Registration (Aspnet\_regiis.exe). Cet outil permet aux administrateurs de spécifier qu’une application Web être exécuté sous une version particulière du .NET Framework. Cela est appelé mappage d’une application Web vers une version du .NET Framework. Les administrateurs doivent sélectionner le compte Aspnet\_regiis.exe qui correspond à la version du .NET Framework qui sera associé à l’application Web. Par exemple, un administrateur souhaite pour spécifier qu’un site Web, utilisez .NET Framework 1.1 doive utiliser le compte Aspnet\_regiis.exe qui est fourni avec .NET Framework 1.1.

Le compte Aspnet\_regiis.exe pour la version 1.0 est situé :

- C:\Windows\Microsoft.NET\Framework\**v1.0.3705** \aspnet\_regiis

Le compte Aspnet\_regiis.exe pour la version 1,1 se trouve dans :

- C:\Windows\Microsoft.NET\Framework\**v1.1.4322** \aspnet\_regiis

Le compte Aspnet\_regiis.exe fournit deux options pour une application Web de mappage de script :

- **-s** définit le mappage de script dans le chemin d’accès et de ses enfants répertoires.
- **-sn** définit le mappage de script dans le chemin d’accès uniquement.

Le chemin d’accès définit le chemin de métadonnées IIS Web application, qui est défini dans l’écran de W3SVC/ROOT / {WebSiteNumber} / {Application\_nom}. Par exemple, pour une application Web appelée portail situé sous le site Web par défaut, le chemin d’accès de métabase est W3SVC/1/racine/portail.

![](side-by-side-with-10/_static/image4.gif)

Notez que vous pouvez également utiliser un outil appelé éditeur de la métabase pour obtenir le chemin d’accès de métabase. Vous pouvez télécharger cet outil à partir du site de Support technique de Microsoft à [https://support.microsoft.com/default.aspx?scid=kb;en-us;232068.](https://support.microsoft.com/default.aspx?scid=kb;en-us;232068)

- Exécutez Aspnet\_regiis.exe -s W3SVC/1/racine/portail pour mettre à jour le portail IIS de script de carte et de ses subapplication.  
  
    ![](side-by-side-with-10/_static/image5.gif)

- Exécutez Aspnet\_regiis.exe -sn W3SVC/1/racine/portail pour mettre à jour le script d’IIS portail mapper, sans affecter les applications dans le portail ? dans les sous-répertoires s.  
  
    ![](side-by-side-with-10/_static/image6.gif)

<a id="3"></a>

## <a name="find-the-net-framework-version-that-a-web-application-is-using"></a>Rechercher la version de .NET Framework qui utilise une application Web

Un administrateur peut utiliser le Gestionnaire des services Internet pour rechercher la version du .NET Framework s’exécute à un site Web. Versions du système d’exploitation différent lancement le Gestionnaire des services Internet différemment. Pour démarrer le Gestionnaire des services, suivez les étapes répertoriées ci-dessous.

**Pour démarrer le Gestionnaire des services Internet**

1. Accédez à **Démarrer**.
2. Cliquez sur **exécuter**.
3. Type **inetmgr**.  
  
    ![](side-by-side-with-10/_static/image7.gif)
4. À partir du Gestionnaire des services Internet, sélectionnez l’application Web dont vous souhaitez connaître la version du .NET Framework.  
  
    ![](side-by-side-with-10/_static/image8.gif)
5. Avec le bouton droit sur l’application Web, puis cliquez sur **propriétés.**  
  
    ![](side-by-side-with-10/_static/image9.gif)
6. Dans la fenêtre Propriétés, sélectionnez **Configuration.**  
  
    ![](side-by-side-with-10/_static/image10.gif)
7. Dans la table de mappage d’application, sélectionnez **.aspx**, puis cliquez sur **modifier**.  
  
    ![](side-by-side-with-10/_static/image11.gif)
8. À partir de la **exécutable** zone de texte, examinez le répertoire de version faisant défiler l’écran. Si le répertoire de version est v.1.1.4322, l’application est mappée vers la version 1.1 de .NET Framework. À l’inverse, si le répertoire de version est v1.0.3705, l’application est mappée à .NET Framework 1.0.  
  
    ![](side-by-side-with-10/_static/image12.gif)
