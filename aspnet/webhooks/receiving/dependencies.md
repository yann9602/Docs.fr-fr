---
uid: webhooks/receiving/dependencies
title: "Dépendances de récepteur ASP.NET WebHooks | Documents Microsoft"
author: rick-anderson
description: "Dépendances du récepteur et injection de dépendances dans ASP.NET WebHooks."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/17/2012
ms.topic: article
ms.assetid: 5125e483-c2bb-435b-8cd1-21d3499bfaaf
ms.technology: 
ms.prod: .net-framework
ms.openlocfilehash: f9726c746c8934594e26f2871f9b867c192374bb
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="aspnet-webhooks-receiver-dependencies"></a>Dépendances de récepteur ASP.NET WebHooks

Microsoft ASP.NET WebHooks est conçue avec l’injection de dépendances à l’esprit. La plupart des dépendances dans le système peut être remplacé par les implémentations alternatives à l’aide d’un moteur d’injection de dépendance.

Consultez [DependencyScopeExtensions](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/Extensions/DependencyScopeExtensions.cs) pour obtenir la liste de dépendances de récepteur. Si aucune dépendance n’a été inscrit, une implémentation par défaut est utilisée. Consultez [ReceiverServices](https://github.com/aspnet/WebHooks/blob/master/src/Microsoft.AspNet.WebHooks.Receivers/Services/ReceiverServices.cs) pour obtenir la liste des implémentations par défaut.
