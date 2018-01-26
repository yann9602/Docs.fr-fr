---
uid: signalr/overview/older-versions/persistent-connection-authorization
title: Authentification et autorisation pour les connexions persistantes SignalR (SignalR 1.x) | Documents Microsoft
author: pfletcher
description: "Cette rubrique décrit comment appliquer l’autorisation sur une connexion persistante. Pour plus d’informations sur l’intégration de sécurité dans une application SignalR,..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/21/2013
ms.topic: article
ms.assetid: c34bc627-41af-4c21-a817-e97a19a7f252
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/older-versions/persistent-connection-authorization
msc.type: authoredcontent
ms.openlocfilehash: 2e97dfd03c61b110325c41a992b4af490fcd17de
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="authentication-and-authorization-for-signalr-persistent-connections-signalr-1x"></a>Authentification et autorisation pour les connexions persistantes SignalR (SignalR 1.x)
====================
par [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

> Cette rubrique décrit comment appliquer l’autorisation sur une connexion persistante. Pour obtenir des informations générales sur l’intégration de sécurité dans une application SignalR, consultez [Introduction à la sécurité](index.md).


## <a name="enforce-authorization"></a>Appliquer l’autorisation

Pour appliquer des règles d’autorisation lorsque vous utilisez un [persistentconnection ne](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.persistentconnection(v=vs.111).aspx) vous devez substituer la `AuthorizeRequest` (méthode). Vous ne pouvez pas utiliser le `Authorize` attribut avec des connexions persistantes. Le `AuthorizeRequest` méthode est appelée par l’infrastructure SignalR avant chaque requête pour vérifier que l’utilisateur est autorisé à effectuer l’action demandée. Le `AuthorizeRequest` méthode n’est pas appelée à partir du client ; au lieu de cela, vous authentifiez l’utilisateur via le mécanisme d’authentification standard de votre application.

L’exemple ci-dessous montre comment limiter les demandes aux utilisateurs authentifiés.

[!code-csharp[Main](persistent-connection-authorization/samples/sample1.cs)]

Vous pouvez ajouter une logique d’autorisation personnalisée de la méthode AuthorizeRequest ; par exemple, la vérification si un utilisateur appartient à un rôle particulier.
