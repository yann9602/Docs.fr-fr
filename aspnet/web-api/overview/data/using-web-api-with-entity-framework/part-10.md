---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-10
title: "Publier l’application dans Azure App Service de Azure | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/16/2014
ms.topic: article
ms.assetid: 10fd812b-94d6-4967-be97-a31ce9c45e2c
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-10
msc.type: authoredcontent
ms.openlocfilehash: 08994815cb339800619caacdcb8d717e9986f9d5
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="publish-the-app-to-azure-azure-app-service"></a>Publier l’application dans Azure App Service de Azure
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](https://github.com/MikeWasson/BookService)

La dernière étape, vous allez publier l’application sur Azure. Dans l’Explorateur de solutions, cliquez sur le projet et sélectionnez **publier**.

![](part-10/_static/image1.png)

En cliquant sur **publier** appelle la **publier le site Web** boîte de dialogue. Si vous avez coché **hôte du Cloud** lorsque vous avez créé le projet, puis la connexion et les paramètres sont déjà configurés. Dans ce cas, cliquez simplement sur le **paramètres** et vérifiez &quot;exécuter des Migrations Code First&quot;. (Si vous n’avez pas vérifier **hôte du Cloud** au début, puis suivez les étapes de la [section suivante](#new-website).)

[![](part-10/_static/image3.png)](part-10/_static/image2.png)

Pour déployer l’application, cliquez sur **publier**. Vous pouvez afficher la progression de la publication dans le **de l’activité de publication Web** fenêtre. (À partir de la **vue** menu, sélectionnez **autres fenêtres**, puis sélectionnez **de l’activité de publication Web**.)

![](part-10/_static/image4.png)

Visual Studio après déploiement de l’application, le navigateur par défaut s’ouvre automatiquement à l’URL du site Web déployé, et l’application que vous avez créé est en cours d’exécution dans le cloud. L’URL dans la barre d’adresse de navigateur montre que le site est chargé à partir d’Internet.

[![](part-10/_static/image6.png)](part-10/_static/image5.png)

<a id="new-website"></a>
## <a name="deploying-to-a-new-website"></a>Déploiement vers un nouveau site Web

Si vous n’avez pas coché **hôte du Cloud** lors de la création du projet, vous pouvez désormais configurer une application web. Dans l’Explorateur de solutions, cliquez sur le projet et sélectionnez **publier**. Sélectionnez le **profil** onglet et cliquez sur **Microsoft Azure Websites**. Si vous n’êtes pas actuellement connecté à Azure, vous devrez vous connecter.

[![](part-10/_static/image8.png)](part-10/_static/image7.png)

Dans le **sites Web existants** boîte de dialogue, cliquez sur **nouveau**.

![](part-10/_static/image9.png)

Entrez un nom de site. Sélectionnez votre abonnement Azure et la région. Sous **serveur de base de données**, sélectionnez **créer un nouveau serveur**, ou sélectionnez un serveur existant. Cliquez sur **Créer**.

[![](part-10/_static/image11.png)](part-10/_static/image10.png)

Cliquez sur le **paramètres** et vérifiez &quot;exécuter des Migrations Code First&quot;. Puis cliquez sur **publier**.

>[!div class="step-by-step"]
[Précédent](part-9.md)
