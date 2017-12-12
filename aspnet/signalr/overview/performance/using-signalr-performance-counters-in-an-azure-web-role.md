---
uid: signalr/overview/performance/using-signalr-performance-counters-in-an-azure-web-role
title: "À l’aide des compteurs de performance SignalR dans un rôle Web Azure | Documents Microsoft"
author: guardrex
description: "Comment installer et utiliser des compteurs de performance SignalR dans un rôle Web Azure."
keywords: "Compteur ASP.NET,SignalR,performance, rôle web azure"
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/11/2017
ms.topic: article
ms.assetid: 2a127d3b-21ed-4cc9-bec0-cdab4e742a25
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/performance/using-signalr-performance-counters-in-an-azure-web-role
msc.type: authoredcontent
ms.openlocfilehash: 0d2717eb318d282e21e9aa8622a205f556e3a4ee
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="using-signalr-performance-counters-in-an-azure-web-role"></a>À l’aide des compteurs de performance SignalR dans un rôle Web Azure

Par [Luke Latham](https://github.com/guardrex)

Compteurs de performance SignalR permettent de surveiller les performances de votre application dans un rôle Web Azure. Les compteurs sont capturées par les Diagnostics Microsoft Azure. Vous installez des compteurs de performance SignalR sur Azure avec *signalr.exe*, le même outil utilisé pour les applications autonomes ou en local. Étant donné que les rôles Windows Azure sont temporaires, vous configurez une application pour installer et inscrire les compteurs de performance SignalR lors du démarrage.

## <a name="prerequisites"></a>Conditions préalables

* [Visual Studio 2015](https://www.visualstudio.com/vs/visual-studio-express/)
* [Microsoft Azure SDK pour Visual Studio 2015 (VS2015)](https://azure.microsoft.com/downloads/) **Remarque : redémarrer votre ordinateur après avoir installé le Kit de développement.**
* Abonnement Microsoft Azure : pour vous inscrire pour un compte d’essai Azure, consultez [essai gratuit d’Azure](https://azure.microsoft.com/free/).

## <a name="creating-an-azure-web-role-application-that-exposes-signalr-performance-counters"></a>Création d’une application de rôle Web Azure qui expose les compteurs de performance SignalR

1. Ouvrez Visual Studio 2015.

2. Dans Visual Studio 2015, sélectionnez **fichier &gt; nouveau &gt; projet**.

3. Dans le **modèles** volet de la **nouveau projet** fenêtre sous le **Visual C#** nœud, sélectionnez le **Cloud** nœud et sélectionnez le **Azure Cloud Service** modèle. Nom de l’application **SignalRPerfCounters** et sélectionnez **OK**.

   ![Nouvelle Application de Cloud](using-signalr-performance-counters-in-an-azure-web-role/_static/image1.png)
    
4. Dans le **nouveau Microsoft Azure Cloud Service** boîte de dialogue, sélectionnez **rôle Web ASP.NET** et sélectionnez le  **&gt;**  bouton pour ajouter le rôle pour le projet. Sélectionnez **OK**.

   ![Ajouter le rôle Web ASP.NET](using-signalr-performance-counters-in-an-azure-web-role/_static/image2.png)
    
5. Dans le **nouvelle Application Web ASP.NET - WebRole1** boîte de dialogue, sélectionnez le **MVC** modèle, puis sélectionnez **OK**.

   ![Ajouter MVC et l’API Web](using-signalr-performance-counters-in-an-azure-web-role/_static/image3.png)
    
6. Dans **l’Explorateur de solutions**, ouvrez le *diagnostics.wadcfgx* de fichiers sous **WebRole1**.

   ![Diagnostics.wadcfgx de l’Explorateur de solutions](using-signalr-performance-counters-in-an-azure-web-role/_static/image4.png)
    
7. Remplacez le contenu du fichier avec la configuration suivante, puis enregistrez le fichier :

   [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample1.xml)]
    
8. Ouvrez le **Package Manager Console** de **outils &gt; Gestionnaire de Package NuGet**. Entrez les commandes suivantes pour installer la dernière version de SignalR et le package d’utilitaires SignalR :

   [!code-powershell[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample2.ps1)]
    
9. Configurer l’application pour installer les compteurs de performance SignalR dans l’instance de rôle lorsqu’il démarre ou est recyclé. Dans **l’Explorateur de solutions**, avec le bouton droit sur le **WebRole1** de projet et sélectionnez **ajouter &gt; nouveau dossier**. Nommez le nouveau dossier *démarrage*.

   ![Ajouter un dossier de démarrage](using-signalr-performance-counters-in-an-azure-web-role/_static/image5.png)
    
10. Copie le *signalr.exe* fichier (ajoutée avec la **Microsoft.AspNet.SignalR.Utils** package) à partir de  **&lt;dossier du projet&gt;\SignalRPerfCounters\packages\ Microsoft.AspNet.SignalR.Utils. &lt;version&gt;\tools** à la *démarrage* dossier que vous avez créé à l’étape précédente.

11. Dans **l’Explorateur de solutions**, avec le bouton droit le *démarrage* et sélectionnez **ajouter &gt; élément existant**. Dans la boîte de dialogue qui s’affiche, sélectionnez *signalr.exe* et sélectionnez **ajouter**.

    ![Ajouter signalr.exe au projet](using-signalr-performance-counters-in-an-azure-web-role/_static/image6.png)
    
12. Avec le bouton droit sur le *démarrage* dossier que vous avez créé. Sélectionnez **ajouter &gt; un nouvel élément**. Sélectionnez le **général** nœud, sélectionnez **fichier texte**et nommez le nouvel élément *SignalRPerfCounterInstall.cmd*. Ce fichier de commande installe les compteurs de performance SignalR dans le rôle web.

    ![Créer le fichier de commandes d’installation du compteur de performance SignalR](using-signalr-performance-counters-in-an-azure-web-role/_static/image7.png)
     
13. Lorsque Visual Studio crée le *SignalRPerfCounterInstall.cmd* fichier, il s’ouvre automatiquement dans la fenêtre principale. Remplacez le contenu du fichier par le script suivant, puis enregistrez et fermez le fichier. Ce script s’exécute *signalr.exe*, qui ajoute les compteurs de performance SignalR à l’instance de rôle.

    [!code-console[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample3.cmd)]
    
14. Sélectionnez le *signalr.exe* fichier **l’Explorateur de solutions**. Dans du fichier **propriétés**, définissez **copier dans le répertoire de sortie** à **toujours copier**.

    ![Affectez à copier vers le répertoire de sortie pour toujours copier](using-signalr-performance-counters-in-an-azure-web-role/_static/image8.png)
    
15. Répétez l’étape précédente pour le *SignalRPerfCounterInstall.cmd* fichier.

    
16. Avec le bouton droit sur le *SignalRPerfCounterInstall.cmd* fichier et sélectionnez **ouvrir avec**. Dans la boîte de dialogue qui s’affiche, sélectionnez **éditeur binaire** et sélectionnez **OK**.

    ![Ouvrir avec l’éditeur binaire](using-signalr-performance-counters-in-an-azure-web-role/_static/image9.png)
    
17. Dans l’éditeur binaire, sélectionnez tous les octets au début du fichier et les supprimer. Enregistrez et fermez le fichier.

    ![Supprimer des octets au début](using-signalr-performance-counters-in-an-azure-web-role/_static/image10.png)
    
18. Ouvrez *ServiceDefinition.csdef* et ajoutez une tâche de démarrage qui s’exécute le *SignalrPerfCounterInstall.cmd* fichier lorsque le service démarre :

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample4.xml?highlight=4-7)]
    
19. Ouvrez `Views/Shared/_Layout.cshtml` et supprimer le script de lot jQuery à partir de la fin du fichier.

    [!code-cshtml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample5.cshtml)]
    
20. Ajouter un client JavaScript qui appelle continuellement la `increment` méthode sur le serveur. Ouvrez `Views/Home/Index.cshtml` et remplacez le contenu par le code suivant :

    [!code-cshtml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample6.cshtml)]
    
21. Créer un nouveau dossier dans le **WebRole1** projet nommé *concentrateurs*. Avec le bouton droit le *concentrateurs* dossier dans **l’Explorateur de solutions**, sélectionnez **Web &gt; SignalR**, puis sélectionnez **classe de concentrateur SignalR (v2)**. Nommez le nouveau concentrateur *MyHub.cs* et sélectionnez **ajouter**.

    ![Ajouter une classe concentrateur SignalR pour le dossier de concentrateurs dans la boîte de dialogue Ajouter un nouvel élément](using-signalr-performance-counters-in-an-azure-web-role/_static/image13.png)

22. *MyHub.cs* s’ouvre automatiquement dans la fenêtre principale. Remplacez le contenu par le code suivant, puis enregistrez et fermez le fichier :

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample7.cs)]
    
23. *[Crank.exe](signalr-connection-density-testing-with-crank.md)*  est un outil fourni avec le code base SignalR de test de densité de connexion. Puisque manivelle requiert une connexion permanente, vous ajoutez une à votre site à utiliser lors du test. Ajouter un nouveau dossier pour le **WebRole1** projet appelé *PersistentConnections*. Cliquez sur ce dossier et sélectionnez **ajouter &gt; classe**. Nommez le nouveau fichier de classe *MyPersistentConnections.cs* et sélectionnez **ajouter**.

24. Visual Studio ouvre le *MyPersistentConnections.cs* fichier dans la fenêtre principale. Remplacez le contenu par le code suivant, puis enregistrez et fermez le fichier :

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample8.cs)]
    
25. À l’aide de la `Startup` (classe), les objets SignalR démarrer au démarrage de OWIN. Ouvrez ou créez *Startup.cs* et remplacez le contenu par le code suivant :

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample9.cs)]
    
    Dans le code ci-dessus, le `OwinStartup` attribut marque cette classe pour OWIN. Le `Configuration` méthode commence à SignalR.
    
26. Testez votre application dans l’émulateur Microsoft Azure en appuyant sur **F5**.

    > [!NOTE]
    > Si vous rencontrez un **FileLoadException** à **MapSignalR**, modifiez les redirections de liaison dans *web.config* à ce qui suit :

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample12.xml?highlight=3,7)]
    
27. Attendez environ une minute. Ouvrez la fenêtre d’outil Cloud Explorer dans Visual Studio (**vue &gt; Cloud Explorer**) et développez le chemin d’accès `(Local)\Storage Accounts\(Development)\Tables`. Double-cliquez sur **WADPerformanceCountersTable**. Vous devez voir les compteurs SignalR dans les données de table. Si vous ne voyez pas la table, vous devrez peut-être entrer à nouveau vos informations d’identification de stockage Azure. Vous devrez peut-être sélectionner la **Actualiser** bouton pour afficher la table de **Cloud Explorer** ou sélectionnez le **Actualiser** dans la fenêtre Ouvrir une table pour afficher les données de la table.

    ![Sélection de la Table de compteurs de Performance de Diagnostics Windows AZURE dans Visual Studio Cloud Explorer](using-signalr-performance-counters-in-an-azure-web-role/_static/image11.png)

    ![Affichant les compteurs collectés dans la Table de compteurs de Performance de Diagnostics Windows AZURE](using-signalr-performance-counters-in-an-azure-web-role/_static/image12.png)
    
28. Pour tester votre application dans le cloud, mettre à jour le **ServiceConfiguration.Cloud.cscfg** de fichiers et de définir le `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` à une chaîne de connexion de compte Azure Storage valide.

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample10.xml)]

29. Déployez l’application à votre abonnement Azure. Pour plus d’informations sur la façon de déployer une application dans Azure, consultez [comment créer et déployer un Service Cloud](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy).

30. Attendez quelques minutes. Dans **Cloud Explorer**, recherchez le compte de stockage que vous avez configuré ci-dessus et recherchez le `WADPerformanceCountersTable` table qu’elle contient. Vous devez voir les compteurs SignalR dans les données de table. Si vous ne voyez pas la table, vous devrez peut-être entrer à nouveau vos informations d’identification de stockage Azure. Vous devrez peut-être sélectionner la **Actualiser** bouton pour afficher la table de **Cloud Explorer** ou sélectionnez le **Actualiser** dans la fenêtre Ouvrir une table pour afficher les données de la table.

Remerciements [Martin Richard](https://social.msdn.microsoft.com/profile/Martin+Richard) pour le contenu d’origine utilisé dans ce didacticiel.
