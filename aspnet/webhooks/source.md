---
uid: webhooks/source
title: "Code source de WebHooks d’ASP.NET et les packages NuGet | Documents Microsoft"
author: rick-anderson
description: "Liens vers le code source de WebHooks d’ASP.NET et les packages NuGet"
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/17/2012
ms.topic: article
ms.assetid: 91a62bfa-ea3a-41f9-a2e1-e90d2c8fc8ca
ms.technology: 
ms.prod: .net-framework
ms.openlocfilehash: ab5eaaa32a8f678b2aa4e2a0b30b674dbd6d6e9c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="aspnet-webhooks-source-code-and-nuget-packages"></a>Code source de WebHooks d’ASP.NET et les packages NuGet

Microsoft ASP.NET WebHooks fait partie de la famille Microsoft ASP.NET des modules et est hébergé comme une [ouvrir le projet Source sur GitHub](https://github.com/aspnet/WebHooks). Cela signifie que nous acceptons les contributions, mais que vous regardez le [Contribution instructions](https://github.com/aspnet/Home/blob/master/CONTRIBUTING.md) avant de soumettre une requête de tirage.

Cette documentation en ligne qui vous lisez maintenant est également hébergée en tant que [Open Source sur GitHub](http://docs.asp.net/en/latest/contribute/style-guide.html#style-guide) et accepte également les contributions.

## <a name="nuget-packages"></a>Packages NuGet

Microsoft ASP.NET WebHooks est également disponible comme afficher un aperçu de ce qui signifie que vous devez sélectionner l’indicateur de version préliminaire dans Visual Studio afin des pour voir les packages Nuget.

Le [les packages Nuget](https://nuget.org/packages?q=Microsoft.AspNet.WebHooks) sont devided en trois parties :

* [Common](https://www.nuget.org/packages?q=Microsoft.AspNet.WebHooks.Common): un package commun qui est partagé entre les expéditeurs et destinataires.

* [Expéditeur](https://www.nuget.org/packages?q=Microsoft.AspNet.WebHooks.Custom): un ensemble de packages prenant en charge l’envoi de vos propres WebHooks à d’autres personnes. La fonctionnalité pour l’envoi de WebHooks est décrite plus en détail dans [envoi le WebHooks](sending/index.md).

* [Récepteurs](https://www.nuget.org/packages?q=Microsoft.AspNet.WebHooks.Receivers): un ensemble de packages prenant en charge la réception de WebHooks à partir d’autres. La fonctionnalité de réception des WebHooks est décrite plus en détail dans [recevoir le WebHooks](receiving/index.md).
