---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v4/create-an-odata-v4-client-app
title: "Créer une application de Client OData v4 (c#) | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/26/2014
ms.topic: article
ms.assetid: 47202362-3808-4add-9a69-c9d1f91d5e4e
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v4/create-an-odata-v4-client-app
msc.type: authoredcontent
ms.openlocfilehash: daa39fbbb4ff17d61f71bf2a642a9c2260b353e4
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="create-an-odata-v4-client-app-c"></a>Créer une application de Client OData v4 (c#)
====================
par [Mike Wasson](https://github.com/MikeWasson)

Dans le didacticiel précédent, vous avez créé un service OData de base qui prend en charge les opérations CRUD. Maintenant nous allons créer un client pour le service.

Démarrez une nouvelle instance de Visual Studio et créez un nouveau projet d’application console. Dans le **nouveau projet** boîte de dialogue, sélectionnez **installé** &gt; **modèles** &gt; **Visual C#** &gt; **Windows Desktop**, puis sélectionnez le **Application Console** modèle. Nommez le projet &quot;ProductsApp&quot;.

![](create-an-odata-v4-client-app/_static/image1.png)

> [!NOTE]
> Vous pouvez également ajouter l’application console à la même solution Visual Studio qui contient le service OData.


## <a name="install-the-odata-client-code-generator"></a>Installer le Générateur de Code Client OData

À partir de la **outils** menu, sélectionnez **Extensions et mises à jour**. Sélectionnez **en ligne** &gt; **galerie Visual Studio**. Dans la zone de recherche, recherchez &quot;Générateur de Code Client OData&quot;. Cliquez sur **télécharger** pour installer l’extension VSIX. Vous pouvez être invité à redémarrer Visual Studio.

[![](create-an-odata-v4-client-app/_static/image3.png)](create-an-odata-v4-client-app/_static/image2.png)

## <a name="run-the-odata-service-locally"></a>Exécuter le Service OData localement

Exécutez le projet de ProductService à partir de Visual Studio. Par défaut, Visual Studio lance un navigateur à la racine de l’application. Notez l’URI ; vous en aurez besoin à l’étape suivante. Laissez l'application en cours d'exécution.

![](create-an-odata-v4-client-app/_static/image4.png)

> [!NOTE]
> Si vous placez les deux projets dans la même solution, veillez à exécuter le projet ProductService sans débogage. Dans l’étape suivante, vous devez conserver le service en cours d’exécution lorsque vous modifiez le projet d’application console.


## <a name="generate-the-service-proxy"></a>Générer le Proxy de Service

Le proxy de service est une classe .NET qui définit des méthodes pour accéder au service OData. Le proxy traduit les appels de méthode dans les requêtes HTTP. Vous allez créer la classe proxy en exécutant un [modèle T4](https://msdn.microsoft.com/en-us/library/bb126445.aspx).

Cliquez sur le projet. Sélectionnez **ajouter** &gt; **un nouvel élément**.

![](create-an-odata-v4-client-app/_static/image5.png)

Dans le **ajouter un nouvel élément** boîte de dialogue, sélectionnez **éléments Visual c#** &gt; **Code** &gt; **OData Client**. Nommez le modèle &quot;ProductClient.tt&quot;. Cliquez sur **ajouter** et parcourez l’avertissement de sécurité.

[![](create-an-odata-v4-client-app/_static/image7.png)](create-an-odata-v4-client-app/_static/image6.png)

À ce stade, vous obtenez une erreur, vous pouvez l’ignorer. Visual Studio exécute automatiquement le modèle, mais le modèle doit être certains paramètres de configuration premier.

[![](create-an-odata-v4-client-app/_static/image9.png)](create-an-odata-v4-client-app/_static/image8.png)

Ouvrez le fichier ProductClient.odata.config. Dans la `Parameter` élément, collez l’URI du projet ProductService (étape précédente). Exemple :

[!code-xml[Main](create-an-odata-v4-client-app/samples/sample1.xml)]

[![](create-an-odata-v4-client-app/_static/image11.png)](create-an-odata-v4-client-app/_static/image10.png)

Exécutez à nouveau le modèle. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le fichier ProductClient.tt et sélectionnez **exécuter un outil personnalisé**.

Le modèle crée un fichier de code nommé ProductClient.cs qui définit le proxy. Lorsque vous développez votre application, si vous modifiez le point de terminaison OData, exécutez le modèle pour mettre à jour le serveur proxy.

![](create-an-odata-v4-client-app/_static/image12.png)

## <a name="use-the-service-proxy-to-call-the-odata-service"></a>Utiliser le Proxy de Service pour appeler le Service OData

Ouvrez le fichier Program.cs et remplacez le code par celui-ci.

[!code-csharp[Main](create-an-odata-v4-client-app/samples/sample2.cs)]

Remplacez la valeur de *serviceUri* avec l’URI de service précédemment.

[!code-csharp[Main](create-an-odata-v4-client-app/samples/sample3.cs)]

Lorsque vous exécutez l’application, il doit le résultat suivant :

[!code-console[Main](create-an-odata-v4-client-app/samples/sample4.cmd)]
