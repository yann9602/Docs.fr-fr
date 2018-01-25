---
uid: webhooks/diagnostics/debugging
title: "WebHooks ASP.NET débogage | Documents Microsoft"
author: rick-anderson
description: "Explique comment déboguer des WebHooks de ASP.NET."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/17/2012
ms.topic: article
ms.assetid: 467da78b-3c35-4c51-8b08-77a32379e4a8
ms.technology: 
ms.prod: .net-framework
ms.openlocfilehash: 524cdf0246eda9ef213414923cd23a92a01f211e
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="aspnet-webhooks-debugging"></a>WebHooks ASP.NET de débogage  

## <a name="debugging-in-azure"></a>Débogage dans Azure

Pour déboguer votre Application Web lors de l’exécution dans Azure, consultez le didacticiel [dépanner une application web dans Azure App Service à l’aide de Visual Studio](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#webserverlogs).

## <a name="debugging-with-source-and-symbols"></a>Débogage avec Source et des symboles

En plus du débogage de votre propre code, il est possible de déboguer directement dans Microsoft ASP.NET WebHooks et en fait partie de .NET. Cela fonctionne indépendamment de si vous déboguez localement ou à distance. Commencez par configurer Visual Studio pour rechercher la source et les symboles en accédant à **déboguer** , puis **Options et paramètres**. Définissez les options comme suit :

![Options et paramètres](_static/SourceSymbols.png)

Ajoutez ensuite un lien vers [symbolsource.org](http://symbolsource.org) pour le téléchargement de la source et les symboles. Accédez à la **symboles** onglet du menu ci-dessus et ajoutez le code suivant comme un emplacement de symboles :

```
http://srv.symbolsource.org/pdb/Public
```

En outre, assurez-vous que le répertoire de cache a un nom court ; dans le cas contraire les noms de fichiers peuvent être trop longs, ce qui entraîne les symboles à ne pas se charger. Un chemin d’accès de l’exemple est la suivante :

```
C:\SymCache
```

Les paramètres doivent ressembler à ceci :

![Exemple d’emplacement de fichier de symboles options](_static/SymSource.png)
