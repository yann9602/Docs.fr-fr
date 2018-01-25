---
uid: signalr/overview/performance/scaleout-with-windows-azure-service-bus
title: "Montée en charge SignalR avec Azure Service Bus | Documents Microsoft"
author: MikeWasson
description: "Versions du logiciel utilisé dans cette version de Visual Studio 2013 .NET 4.5 SignalR rubrique 2 Previous versions du serveur de cette rubrique pour le SignalR 1.x de cette rubrique..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/10/2014
ms.topic: article
ms.assetid: ce1305f9-30fd-49e3-bf38-d0a78dfb06c3
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/performance/scaleout-with-windows-azure-service-bus
msc.type: authoredcontent
ms.openlocfilehash: 7cb68d578fee8d6ee036f8fb096ba45e0c8ef3d6
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="signalr-scaleout-with-azure-service-bus"></a>Montée en charge SignalR avec Azure Service Bus
====================
par [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)

Dans ce didacticiel, vous allez déployer une application SignalR pour un rôle Web Windows Azure à l’aide de l’infrastructure d’intégration Service Bus pour distribuer les messages à chaque instance de rôle. (Vous pouvez également utiliser le fond de panier de Service Bus avec [web apps dans Azure App Service](https://docs.microsoft.com/azure/app-service-web/).)

![](scaleout-with-windows-azure-service-bus/_static/image1.png)

Conditions préalables :

- Un compte Windows Azure.
- Le [Windows Azure SDK](https://go.microsoft.com/fwlink/?linkid=254364&amp;clcid=0x409).
- Visual Studio 2012 ou 2013.

Le fond de panier de bus de service est également compatible avec [Service Bus pour Windows Server](https://msdn.microsoft.com/library/windowsazure/dn282144.aspx), version 1.1. Toutefois, il n’est pas compatible avec la version 1.0 du Service Bus pour Windows Server.

## <a name="pricing"></a>Tarification

Le fond de panier de Service Bus utilise des rubriques pour envoyer des messages. Pour les informations de tarification plus récentes, consultez [Service Bus](https://azure.microsoft.com/pricing/details/service-bus/). Au moment de la rédaction, vous pouvez envoyer des messages de 1 000 000 par mois pour moins de 1 $. Le fond de panier envoie un message de bus de service pour chaque appel d’une méthode de concentrateur SignalR. Il existe également des messages de contrôle pour les connexions, déconnexions, jointure ou laisser les groupes et ainsi de suite. Dans la plupart des applications, la majorité du trafic message seront les appels de méthode de concentrateur.

## <a name="overview"></a>Vue d'ensemble

Avant du didacticiel détaillé, voici une vue d’ensemble rapide des opérations à effectuer.

1. Utilisez le portail Windows Azure pour créer un nouvel espace de noms Service Bus.
2. Ajoutez ces packages NuGet pour votre application : 

    - [Microsoft.AspNet.SignalR](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [Microsoft.AspNet.SignalR.ServiceBus](http://www.nuget.org/packages/SignalR.WindowsAzureServiceBus)
3. Créez une application SignalR.
4. Ajoutez le code suivant à Startup.cs pour configurer l’infrastructure d’intégration : 

    [!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample1.cs)]

Ce code configure le fond de panier avec les valeurs par défaut [TopicCount](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.servicebusscaleoutconfiguration.topiccount(v=vs.118).aspx) et [MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx). Pour plus d’informations sur la modification de ces valeurs, consultez [SignalR performances : montée en puissance parallèle métriques](signalr-performance.md#scaleout_metrics).

Pour chaque application, choisissez une autre valeur pour « Nomapp ». N’utilisez pas la même valeur entre plusieurs applications.

## <a name="create-the-azure-services"></a>Créer des Services Azure

Créer un Service Cloud, comme décrit dans [comment créer et déployer un Service Cloud](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy). Suivez les étapes décrites dans la section « Comment : créer un service cloud à l’aide de la création rapide ». Pour ce didacticiel, vous n’avez pas besoin de télécharger un certificat.

![](scaleout-with-windows-azure-service-bus/_static/image2.png)

Créer un nouvel espace de noms Service Bus, comme décrit dans [comment faire pour utiliser rubriques/abonnements Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions). Suivez les étapes décrites dans la section « Créer un Namespace de Service ».

![](scaleout-with-windows-azure-service-bus/_static/image3.png)

> [!NOTE]
> Veillez à sélectionner la même région pour le service cloud et l’espace de noms Service Bus.


## <a name="create-the-visual-studio-project"></a>Créer le projet Visual Studio

Démarrez Visual Studio. À partir de la **fichier** menu, cliquez sur **nouveau projet**.

Dans le **nouveau projet** boîte de dialogue, développez **Visual C#**. Sous **modèles installés**, sélectionnez **Cloud** , puis sélectionnez **Windows Azure Cloud Service**. Conservez la valeur par défaut .NET Framework 4.5. Nom de l’application ChatService et cliquez sur **OK**.

![](scaleout-with-windows-azure-service-bus/_static/image4.png)

Dans le **nouveau Windows Azure Cloud Service** boîte de dialogue Sélectionner un rôle Web ASP.NET. Cliquez sur le bouton de flèche droite (**&gt;**) pour ajouter le rôle à votre solution.

Pointez la souris sur le nouveau rôle, par conséquent, l’icône de crayon visible. Cliquez sur cette icône pour renommer le rôle. Nom du rôle « SignalRChat » et cliquez sur **OK**.

![](scaleout-with-windows-azure-service-bus/_static/image5.png)

Dans le **nouveau projet ASP.NET** boîte de dialogue, sélectionnez **MVC**, puis cliquez sur OK.

![](scaleout-with-windows-azure-service-bus/_static/image6.png)

L’Assistant projet crée deux projets :

- ChatService : Ce projet est l’application Windows Azure. Il définit les rôles Azure et autres options de configuration.
- SignalRChat : Ce projet est votre projet ASP.NET MVC 5.

## <a name="create-the-signalr-chat-application"></a>Créer l’Application de la conversation de SignalR

Pour créer l’application de conversation, suivez les étapes du didacticiel [prise en main SignalR et MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md).

Utilisez NuGet pour installer les bibliothèques requises. À partir de la **outils** menu, sélectionnez **Gestionnaire de Package de bibliothèque**, puis sélectionnez **Package Manager Console**. Dans le **Package Manager Console** fenêtre, entrez les commandes suivantes :

[!code-powershell[Main](scaleout-with-windows-azure-service-bus/samples/sample2.ps1)]

Utilisez la `-ProjectName` option pour installer les packages dans le projet ASP.NET MVC, plutôt que le projet Windows Azure.

## <a name="configure-the-backplane"></a>Configurer l’infrastructure d’intégration

Dans le fichier de votre application Startup.cs, ajoutez le code suivant :

[!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample3.cs)]

Vous devez maintenant obtenir votre chaîne de connexion de service bus. Dans le portail Azure, sélectionnez l’espace de noms service bus que vous avez créé et cliquez sur l’icône de clé d’accès.

![](scaleout-with-windows-azure-service-bus/_static/image7.png)

Copier la chaîne de connexion dans le Presse-papiers, puis collez-la dans la *connectionString* variable.

![](scaleout-with-windows-azure-service-bus/_static/image8.png)

[!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample4.cs)]

## <a name="deploy-to-azure"></a>Déployer vers Azure

Dans l’Explorateur de solutions, développez le **rôles** dossier situé dans le projet ChatService.

![](scaleout-with-windows-azure-service-bus/_static/image9.png)

Cliquez sur le rôle SignalRChat et sélectionnez **propriétés**. Sélectionnez le **Configuration** onglet. Sous **Instances** sélectionnez 2. Vous pouvez également définir la taille de machine virtuelle **très petite**.

![](scaleout-with-windows-azure-service-bus/_static/image10.png)

Enregistrez les modifications.

Dans l’Explorateur de solutions, cliquez sur le projet ChatService. Sélectionnez **Publier**.

![](scaleout-with-windows-azure-service-bus/_static/image11.png)

S’il s’agit de votre première publication de temps sur Windows Azure, vous devez télécharger vos informations d’identification. Dans le **publier** Assistant, cliquez sur « Se connecter télécharger les informations d’identification ». Vous serez invité à vous connecter au portail Windows Azure et télécharger un fichier de paramètres de publication.

![](scaleout-with-windows-azure-service-bus/_static/image12.png)

Cliquez sur **importation** et sélectionnez le fichier de paramètres de publication que vous avez téléchargé.

Cliquez sur **Suivant**. Dans le **paramètres de publication** boîte de dialogue, sous **Service Cloud**, sélectionnez le service cloud que vous avez créé précédemment.

![](scaleout-with-windows-azure-service-bus/_static/image13.png)

Cliquez sur **Publier**. Il peut prendre quelques minutes pour déployer l’application et démarrer les ordinateurs virtuels.

Maintenant lorsque vous exécutez l’application de conversation, les instances de rôle communiquent via Azure Service Bus, à l’aide d’une rubrique Service Bus. Une rubrique est une file d’attente qui permet à plusieurs abonnés.

Le fond de panier crée automatiquement la rubrique et les abonnements. Pour afficher les abonnements et l’activité des messages, ouvrir le portail Azure, sélectionnez l’espace de noms Service Bus et cliquez sur « Rubriques ».

![](scaleout-with-windows-azure-service-bus/_static/image14.png)

Il peut prendre quelques minutes pour l’activité d’un message s’affiche dans le tableau de bord.

![](scaleout-with-windows-azure-service-bus/_static/image15.png)

SignalR gère la durée de vie de rubrique. Tant que votre application est déployée, n’essayez pas de supprimer les rubriques manuellement ou de modifier les paramètres de la rubrique.

## <a name="troubleshooting"></a>Résolution des problèmes

**System.InvalidOperationException « La seule IsolationLevel pris en charge est 'IsolationLevel.Serializable' ».**

Cette erreur peut se produire si le niveau de la transaction pour une opération est défini sur une valeur autre que `Serializable`. Vérifiez qu’aucune opération n’est effectuées avec les autres niveaux de transaction.
