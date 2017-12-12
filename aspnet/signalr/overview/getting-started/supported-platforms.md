---
uid: signalr/overview/getting-started/supported-platforms
title: Plateformes prises en charge | Documents Microsoft
author: pfletcher
description: "Cet article décrit les clients et les serveurs sont pris en charge par SignalR."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/10/2014
ms.topic: article
ms.assetid: eac31beb-0f46-4afa-9def-e80904dea4f0
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/getting-started/supported-platforms
msc.type: authoredcontent
ms.openlocfilehash: 7f41017a2a8c058c01fe6f89a2503eb5fa77048e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="supported-platforms"></a>Plateformes prises en charge
====================
par [Patrick Fletcher](https://github.com/pfletcher)

> Cet article décrit les clients et les serveurs sont pris en charge par SignalR. 
> 
> ## <a name="questions-and-comments"></a>Questions et des commentaires
> 
> Veuillez laisser des commentaires sur la façon dont vous avez aimé ce didacticiel et nous pouvons améliorer dans les commentaires en bas de la page. Si vous avez des questions qui ne sont pas directement liées à ce didacticiel, vous pouvez les valider pour le [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


SignalR est pris en charge dans diverses configurations de serveur et client. De plus, chaque option de transport possède un ensemble d’exigences de son propre ; Si la configuration système requise pour un transport n’est pas disponible, SignalR bascule en douceur vers les autres transports. Pour plus d’informations sur les transports prenant en charge SignalR, consultez [du transport et secours](introduction-to-signalr.md#transports).

## <a name="server-system-requirements"></a>Configuration système requise du serveur

Le composant de serveur SignalR peut être hébergé sur une variété de configurations de serveur. Cette section décrit les versions prises en charge de systèmes d’exploitation, .NET framework, Internet Information Server et autres composants.

### <a name="supported-server-operating-systems"></a>Systèmes d'exploitation serveurs pris en charge

Le composant de serveur SignalR peut être hébergé dans les systèmes d’exploitation client ou serveur suivants. Notez que pour SignalR utiliser des WebSockets, Windows Server 2012 ou Windows 8 (WebSocket peut être utilisé sur les Sites Web Windows Azure, tant que la version .NET framework du site est définie sur 4.5 et WebSocket est activée dans la page de Configuration du site).

- Windows Server 2012
- Windows Server 2008 r2
- Windows 8
- Windows 7
- Windows Azure

### <a name="supported-server-net-framework-version"></a>Version de serveur pris en charge du .NET Framework

SignalR 2 est uniquement pris en charge sur le .NET Framework 4.5. Consultez le [mises à jour recommandées](#updates) section mises à jour qui améliorent la fiabilité, de compatibilité, de stabilité et de performances.

### <a name="supported-server-iis-versions"></a>Versions d’IIS serveur pris en charge

Lorsque SignalR est hébergé dans IIS, les versions suivantes sont prises en charge. Notez que si un système d’exploitation de client est utilisé, comme pour le développement (Windows 8 ou Windows 7), version complète d’IIS ou Cassini ne doit pas être utilisée, car il y a une limite de 10 connexions simultanées imposée, qui sera atteinte très rapidement car les connexions sont transitoires, souvent rétablie et sont pas supprimés immédiatement fonction n’est plus utilisé. IIS Express doit être utilisé sur les systèmes d’exploitation clients.

Notez également que pour SignalR à utiliser WebSocket, IIS 8 ou IIS Express de 8 doit être utilisé, le serveur doit être à l’aide de Windows 8, Windows Server 2012 ou version ultérieure, et WebSocket doit être activée dans IIS. Pour plus d’informations sur l’activation de WebSocket dans IIS, consultez [prise en charge du protocole WebSocket IIS 8.0](https://www.iis.net/learn/get-started/whats-new-in-iis-8/iis-80-websocket-protocol-support).

- IIS 8 ou IIS 8 Express.
- IIS 7 et 7.5. Prise en charge de [URL sans extension](https://support.microsoft.com/kb/980368) est requis.
- IIS doit être en cours d’exécution en mode intégré ; en mode classique n’est pas pris en charge. Retards de message de 30 secondes peut-être être rencontrés si IIS est exécuté en mode classique, l’utilisation du transport Server-Sent événements.
- L’application d’hébergement doit être en cours d’exécution en mode confiance totale.

## <a name="client-system-requirements"></a>Configuration système requise du client

SignalR utilisable dans un éventail de plateformes de client. Cette section décrit la configuration requise pour l’utilisation de SignalR dans les navigateurs web, les applications de bureau Windows, les applications Silverlight et les appareils mobiles.

### <a name="web-browsers"></a>Navigateurs Web

SignalR peut être utilisé dans une variété de navigateurs web, mais en règle générale, uniquement les deux dernières versions sont prises en charge.

Les applications qui utilisent SignalR dans les navigateurs doivent utiliser jQuery 1.6.4 ou une version ultérieure principale (par exemple, 1.7.2, 1.8.2 ou 1.9.1).

SignalR peut être utilisé dans les navigateurs suivants :

- Versions de Microsoft Internet Explorer 8, 9, 10 et 11. Moderne, bureau et mobiles versions sont prises en charge.
- Mozilla Firefox : version actuelle - 1, les versions Windows et Mac.
- Google Chrome : version actuelle - 1, les versions Windows et Mac.
- Safari : version actuelle - 1, versions de Mac et iOS.
- Opera : version actuelle - 1, Windows uniquement.
- Navigateur Android

En plus des certains navigateurs, les divers transports SignalR utilise ont des exigences de leurs propres. Les transports suivants sont pris en charge les configurations suivantes :

<a id="browser"></a>

**Configuration de Transport du navigateur Web**

| Transport | Internet Explorer | Chrome (Windows ou iOS) | Firefox | Safari (OS x ou iOS) | Android |
| --- | --- | --- | --- | --- | --- |
| WebSockets | 10+ | en cours - 1 | en cours - 1 | en cours - 1 | N/A |
| Événements de serveur a été envoyé | N/A | en cours - 1 | en cours - 1 | en cours - 1 | N/A |
| ForeverFrame | 8+ | N/A | N/A | N/A | 4.1 |
| D’interrogation longue | 8+ | en cours - 1 | en cours - 1 | en cours - 1 | 4.1 |

\*: 6 + nécessaire pour les fonctionnalités complètes.

#### <a name="unsupported-browsers"></a>Navigateurs non pris en charge

Alors que SignalR *peut* s’exécuter sans problèmes majeurs pour les anciennes versions de navigateur, nous ne testez pas activement SignalR dans les et généralement ne pas corriger les bogues qui peuvent apparaître dans les.

### <a name="windows-desktop-and-silverlight-applications"></a>Bureau Windows et les Applications Silverlight

En plus en cours d’exécution dans un navigateur web, SignalR peut être hébergé dans le client autonome Windows ou des applications Silverlight. Les applications de bureau Windows et Silverlight SignalR ont les exigences système suivantes.

- Les applications utilisant le .NET 4 sont pris en charge sur Windows XP SP3 ou version ultérieure.
- Applications à l’aide de .NET Framework 4.5 sont pris en charge sur Windows Vista ou version ultérieure.

Outre le système d’exploitation et la configuration requise du .NET framework, les transports disponibles pour SignalR ont des exigences de leurs propres. Les transports suivants sont pris en charge les configurations suivantes :

**Bureau Windows en matière de Transport de Silverlight**

| Transport | Application .NET | Silverlight |
| --- | --- | --- |
| WebSockets | Windows 8 + et .NET 4.5 + | N/A |
| Forever Frame | N/A | N/A |
| Événements de serveur a été envoyé | .NET 4 + | 5+ |
| D’interrogation longue | .NET 4 + | 5+ |

<a id="android"></a>

### <a name="windows-store-and-windows-phone-applications"></a>Windows Store et les Applications Windows Phone

SignalR peut être utilisé dans les applications du Windows Store et les applications Windows Phone 8. Les transports suivants sont pris en charge les configurations suivantes :

**Windows Store et les exigences de Transport de Windows Phone**

| Transport | Windows Store / .NET | Windows Store / JavaScript | Windows Phone / IE | Windows Phone / .NET |
| --- | --- | --- | --- | --- |
| WebSockets | N/A | Win8 + | 8+ | N/A |
| Forever Frame | N/A | Win8 + | 7.5+ | N/A |
| Événements de serveur a été envoyé | Win8 + | N/A | N/A | 8+ |
| D’interrogation longue | Win8 + | Win8 + | 7.5+ | 8+ |

<a id="updates"></a>

## <a name="recommended-updates"></a>Mises à jour recommandées

Les mises à jour suivantes sont recommandées pour les serveurs de SignalR :

- Une mise à jour pour .NET Framework 4.5 est disponible [ici](https://support.microsoft.com/kb/2750149).
- Microsoft publie régulièrement QFE pour ASP.NET. Il doivent être appliqués comme étant disponibles.
