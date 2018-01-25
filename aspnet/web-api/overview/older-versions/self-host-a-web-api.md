---
uid: web-api/overview/older-versions/self-host-a-web-api
title: "Auto-hébergement ASP.NET Web API 1 (c#) | Documents Microsoft"
author: MikeWasson
description: "API Web ASP.NET ne requiert pas d’IIS. Vous pouvez auto-hébergement une API web dans votre propre processus d’hébergement. Ce didacticiel montre comment héberger une API web à l’intérieur d’une console applic..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/26/2012
ms.topic: article
ms.assetid: be5ab1e2-4140-4275-ac59-ca82a1bac0c1
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/older-versions/self-host-a-web-api
msc.type: authoredcontent
ms.openlocfilehash: 564f859e73a88ac9c5f27e9b8f7409ec126642f8
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="self-host-aspnet-web-api-1-c"></a>Auto-hébergement ASP.NET Web API 1 (c#)
====================
par [Mike Wasson](https://github.com/MikeWasson)

> API Web ASP.NET ne requiert pas d’IIS. Vous pouvez auto-hébergement une API web dans votre propre processus d’hébergement. Ce didacticiel montre comment héberger une API web à l’intérieur d’une application console.
> 
> **Nouvelles applications doivent utiliser OWIN pour l’auto-hébergement API Web.** Consultez [OWIN permet d’auto-hébergement ASP.NET Web API 2](../hosting-aspnet-web-api/use-owin-to-self-host-web-api.md).
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - API Web 1
> - Visual Studio 2012


## <a name="create-the-console-application-project"></a>Créer le projet d’Application Console

Démarrez Visual Studio et sélectionnez **nouveau projet** à partir de la **Démarrer** page. Ou, à partir de la **fichier** menu, sélectionnez **nouveau** , puis **projet**.

Dans le **modèles** volet, sélectionnez **modèles installés** et développez le **Visual C#** nœud. Sous **Visual C#**, sélectionnez **Windows**. Dans la liste des modèles de projet, sélectionnez **Application Console**. Nommez le projet &quot;SelfHost&quot; et cliquez sur **OK**.

![](self-host-a-web-api/_static/image1.png)

## <a name="set-the-target-framework-visual-studio-2010"></a>Définir le Framework cible (Visual Studio 2010)

Si vous utilisez Visual Studio 2010, modifier le framework cible pour le .NET Framework 4.0. (Par défaut, le modèle de projet cible le [du profil .net Framework Client](https://msdn.microsoft.com/library/cc656912.aspx#features_not_included_in_the_net_framework_client_profile).)

Dans l’Explorateur de solutions, cliquez sur le projet et sélectionnez **propriétés**. Dans le **framework cible** déroulante liste, de modifier le framework cible pour le .NET Framework 4.0. Lorsque vous êtes invité à appliquer la modification, cliquez sur **Oui**.

![](self-host-a-web-api/_static/image2.png)

## <a name="install-nuget-package-manager"></a>Installer le Gestionnaire de Package NuGet

Le Gestionnaire de Package NuGet est le moyen le plus simple pour ajouter les assemblys de l’API Web à un projet non-ASP.NET.

Pour vérifier si le Gestionnaire de Package NuGet est installé, cliquez sur le **outils** menu dans Visual Studio. Si vous voyez un élément de menu appelé **Gestionnaire de Package de bibliothèque**, vous devez le Gestionnaire de Package NuGet.

Pour installer le Gestionnaire de Package NuGet :

1. Démarrez Visual Studio.
2. À partir de la **outils** menu, sélectionnez **Extensions et mises à jour**.
3. Dans le **Extensions et mises à jour** boîte de dialogue, sélectionnez **Online**.
4. Si vous ne voyez pas « Gestionnaire de Package NuGet », tapez « Gestionnaire de package nuget » dans la zone de recherche.
5. Sélectionnez le Gestionnaire de Package NuGet et cliquez sur **télécharger**.
6. Une fois le téléchargement terminé, vous devrez installer.
7. Une fois l’installation terminée, vous pouvez être invité à redémarrer Visual Studio.

![](self-host-a-web-api/_static/image3.png)

## <a name="add-the-web-api-nuget-package"></a>Ajoutez le Package NuGet de l’API Web

Une fois que le Gestionnaire de Package NuGet est installé, ajoutez le package de Self-Host d’API Web à votre projet.

1. À partir de la **outils** menu, sélectionnez **Gestionnaire de Package de bibliothèque**. *Remarque*: Si ne vous voyez pas ce menu d’élément, assurez-vous que ce gestionnaire de Package NuGet installé correctement.
2. Sélectionnez **gérer les Packages NuGet pour la Solution...**
3. Dans le **gérer les Packages pépite** boîte de dialogue, sélectionnez **Online**.
4. Dans la zone de recherche, tapez &quot;Microsoft.AspNet.WebApi.SelfHost&quot;.
5. Sélectionnez le package ASP.NET Web API Self Host et cliquez sur **installer**.
6. Une fois le package installé, cliquez sur **fermer** pour fermer la boîte de dialogue.

> [!NOTE]
> Veillez à installer le package nommé Microsoft.AspNet.WebApi.SelfHost, AspNetWebApi.SelfHost pas.


![](self-host-a-web-api/_static/image4.png)

## <a name="create-the-model-and-controller"></a>Créer le modèle et le contrôleur

Ce didacticiel utilise les mêmes classes de modèle et du contrôleur en tant que le [mise en route](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md) didacticiel.

Ajoutez une classe publique nommée `Product`.

[!code-csharp[Main](self-host-a-web-api/samples/sample1.cs)]

Ajoutez une classe publique nommée `ProductsController`. Dériver de cette classe à partir de **System.Web.Http.ApiController**.

[!code-csharp[Main](self-host-a-web-api/samples/sample2.cs)]

Pour plus d’informations sur le code de ce contrôleur, consultez le [mise en route](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md) didacticiel. Ce contrôleur définit trois actions GET :

| URI | Description |
| --- | --- |
| produits/api / | Obtenir une liste de tous les produits. |
| /api/products/*id* | Obtenir un produit par ID. |
| /api/products/?category=*category* | Obtenir une liste de produits par catégorie. |

## <a name="host-the-web-api"></a>Hôte de l’API Web

Ouvrez le fichier Program.cs et ajoutez le code suivant à l’aide des instructions :

[!code-csharp[Main](self-host-a-web-api/samples/sample3.cs)]

Ajoutez le code suivant à la **programme** classe.

[!code-csharp[Main](self-host-a-web-api/samples/sample4.cs)]

## <a name="optional-add-an-http-url-namespace-reservation"></a>(Facultatif) Ajouter une réservation Namespace d’URL HTTP

Cette application écoute `http://localhost:8080/`. Par défaut, à l’écoute sur une adresse HTTP particulière nécessite des privilèges d’administrateur. Lorsque vous exécutez le didacticiel, par conséquent, vous obtiendrez cette erreur : « HTTP n’a pas inscrire URL http://+:8080/ » il existe deux façons d’éviter cette erreur :

- Exécuter Visual Studio avec des autorisations d’administrateur élevés, ou
- Utilisation de Netsh.exe pour accorder des autorisations de votre compte pour réserver l’URL.

Pour utiliser Netsh.exe, ouvrez une invite de commandes avec des privilèges d’administrateur et entrez la commande suivante de la commande : suivantes :

[!code-console[Main](self-host-a-web-api/samples/sample5.cmd)]

où *Machine\nom d’utilisateur* est votre compte d’utilisateur.

Lorsque vous avez terminé l’auto-hébergement, veillez à supprimer la réservation :

[!code-console[Main](self-host-a-web-api/samples/sample6.cmd)]

## <a name="call-the-web-api-from-a-client-application-c"></a>Appeler l’API Web à partir d’une Application cliente (c#)

Nous allons écrire une application console simple qui appelle l’API web.

Ajouter un nouveau projet d’application console à la solution :

- Dans l’Explorateur de solutions, cliquez sur la solution et sélectionnez **ajouter un nouveau projet**.
- Créez une application console nommée &quot;ClientApp&quot;.

![](self-host-a-web-api/_static/image5.png)

Utilisez le Gestionnaire de Package NuGet pour ajouter le package ASP.NET Web API Core Libraries :

- Dans le menu Outils, sélectionnez **Gestionnaire de Package de bibliothèque**.
- Sélectionnez **gérer les Packages NuGet pour la Solution...**
- Dans le **gérer les Packages NuGet** boîte de dialogue, sélectionnez **Online**.
- Dans la zone de recherche, tapez &quot;Microsoft.AspNet.WebApi.Client&quot;.
- Sélectionnez le package Microsoft ASP.NET Web API Client Libraries et cliquez sur **installer**.

Ajoutez une référence dans ClientApp au projet SelfHost :

- Dans l’Explorateur de solutions, cliquez sur le projet ClientApp.
- Sélectionnez **ajouter une référence**.
- Dans le **Gestionnaire de références** boîte de dialogue, sous **Solution**, sélectionnez **projets**.
- Sélectionnez le projet SelfHost.
- Cliquez sur **OK**.

![](self-host-a-web-api/_static/image6.png)

Ouvrez le fichier Client/Program.cs. Ajoutez le code suivant **à l’aide de** instruction :

[!code-csharp[Main](self-host-a-web-api/samples/sample7.cs)]

Ajouter un mappage statique **HttpClient** instance :

[!code-csharp[Main](self-host-a-web-api/samples/sample8.cs)]

Ajoutez les méthodes suivantes pour répertorier tous les produits, un produit par l’ID de liste et répertorient les produits par catégorie.

[!code-csharp[Main](self-host-a-web-api/samples/sample9.cs)]

Chacune de ces méthodes suit le même modèle :

1. Appelez **HttpClient.GetAsync** pour envoyer une requête GET à l’URI approprié.
2. Appelez **HttpResponseMessage.EnsureSuccessStatusCode**. Cette méthode lève une exception si l’état de réponse HTTP est un code d’erreur.
3. Appelez **ReadAsAsync&lt;T&gt;**  pour désérialiser un type CLR de la réponse HTTP. Cette méthode est une méthode d’extension, définie dans **System.Net.Http.HttpContentExtensions**.

Le **GetAsync** et **ReadAsAsync** méthodes sont asynchrones. Elles retournent **tâche** objets qui représentent l’opération asynchrone. Obtention de la **résultat** propriété bloque le thread jusqu'à ce que l’opération se termine.

Pour plus d’informations sur l’utilisation du client HTTP, y compris comment effectuer des appels non bloquant, consultez [appeler une Web API d’un Client .NET](../advanced/calling-a-web-api-from-a-net-client.md).

Avant d’appeler ces méthodes, définissez la propriété BaseAddress sur l’instance de client HTTP pour «`http://localhost:8080`». Exemple :

[!code-csharp[Main](self-host-a-web-api/samples/sample10.cs)]

Il doit le résultat suivant. (N’oubliez pas à exécuter l’application SelfHost en premier).

[!code-console[Main](self-host-a-web-api/samples/sample11.cmd)]

![](self-host-a-web-api/_static/image7.png)
