---
uid: webhooks/diagnostics/logging
title: WebHooks ASP.NET journalisation | Documents Microsoft
author: rick-anderson
description: "Procédure à suivre la journalisation dans ASP.NET WebHooks."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/17/2012
ms.topic: article
ms.assetid: f71bc442-5f80-481b-a32c-a0ec18dee9d6
ms.technology: 
ms.prod: .net-framework
ms.openlocfilehash: 042d20e38a9bc4f1e9792f6e3ff5be11a1eaa882
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="aspnet-webhooks-logging"></a>WebHooks ASP.NET journalisation

Microsoft ASP.NET WebHooks utilise la journalisation comme un moyen de problèmes et des problèmes de reporting. Par défaut, les journaux sont écrits à l’aide de [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) où il peut être à l’aide de [écouteurs de traçage](https://msdn.microsoft.com/library/system.diagnostics.tracelistener.aspx) comme tout autre flux de journal.

Lorsque vous déployez votre Application Web comme une application Web Azure, les journaux sont récupérées automatiquement et peuvent être gérés avec n’importe quel autre [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) journalisation. Pour plus d’informations, consultez [activer la journalisation des diagnostics pour les applications web dans Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/)

En outre, les journaux peuvent être obtenus directement à partir de Visual Studio, comme décrit dans [dépanner une application web dans Azure App Service à l’aide de Visual Studio](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#webserverlogs).

## <a name="redirecting-logs"></a>Redirection des journaux

Au lieu d’écrire des journaux de [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace), il est possible de fournir une implémentation d’un autre enregistrement qui peut se connecter directement à un gestionnaire de journal comme [Log4Net](http://logging.apache.org/log4net/) et [NLog ](http://nlog-project.org/). Il suffit de fournir une implémentation de [ILogger](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Common/Diagnostics/ILogger.cs) il est collectée par Microsoft ASP.NET WebHooks et inscrivez-le avec un moteur d’injection de dépendance de votre choix. Consultez [Injection de dépendances dans ASP.NET Web API 2](https://www.asp.net/web-api/overview/advanced/dependency-injection) pour plus d’informations.
