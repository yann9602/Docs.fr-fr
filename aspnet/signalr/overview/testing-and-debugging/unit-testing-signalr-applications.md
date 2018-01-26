---
uid: signalr/overview/testing-and-debugging/unit-testing-signalr-applications
title: "Tests unitaires d’Applications de SignalR | Documents Microsoft"
author: pfletcher
description: "Cet article décrit comment utiliser les fonctionnalités de test unitaire de SignalR 2.0."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/10/2014
ms.topic: article
ms.assetid: d1983524-e0d5-4ee6-9d87-1f552f7cb964
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/testing-and-debugging/unit-testing-signalr-applications
msc.type: authoredcontent
ms.openlocfilehash: d767e1a9d27670387133e5a48a8f92f5bdd39d9e
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="unit-testing-signalr-applications"></a>Unité de test des Applications SignalR
====================
par [Patrick Fletcher](https://github.com/pfletcher)

> Cet article décrit les fonctionnalités des tests unitaires de SignalR 2. 
> 
> ## <a name="software-versions-used-in-this-topic"></a>Versions du logiciel utilisées dans cette rubrique
> 
> 
> - [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - .NET 4.5
> - SignalR version 2
>   
> 
> 
> ## <a name="questions-and-comments"></a>Questions et des commentaires
> 
> Veuillez laisser des commentaires sur la façon dont vous avez aimé ce didacticiel et nous pouvons améliorer dans les commentaires en bas de la page. Si vous avez des questions qui ne sont pas directement liées à ce didacticiel, vous pouvez les valider pour le [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


<a id="unit"></a>
## <a name="unit-testing-signalr-applications"></a>Les applications SignalR de tests unitaires

Vous pouvez utiliser les fonctionnalités de test unitaire dans SignalR 2 pour créer des tests unitaires pour votre application SignalR. SignalR 2 inclut les [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) interface, ce qui peut être utilisé pour créer un objet factice pour simuler vos méthodes de concentrateur de test.

Dans cette section, vous allez ajouter des tests unitaires pour l’application créée dans le [didacticiel de mise en route](../getting-started/tutorial-getting-started-with-signalr.md) à l’aide de [XUnit.net](https://github.com/xunit/xunit) et [Moq](https://github.com/Moq/moq4).

XUnit.net permet de contrôler le test ; Moq permet de créer un [mocker](http://en.wikipedia.org/wiki/Mock_object) objet pour le test. Autres infrastructures factices peuvent être utilisés si vous le souhaitez ; [NSubstitute](http://nsubstitute.github.io/) est également un bon choix. Ce didacticiel montre comment définir l’objet factice de deux manières : en premier lieu, à l’aide un `dynamic` objet (introduite dans .NET Framework 4) et, ensuite, à l’aide d’une interface.

### <a name="contents"></a>Sommaire

Ce didacticiel contient les sections suivantes.

- [Tests unitaires avec dynamique](#dynamic)
- [Tests unitaires en type](#type)

<a id="dynamic"></a>
### <a name="unit-testing-with-dynamic"></a>Tests unitaires avec dynamique

Dans cette section, vous allez ajouter un test unitaire pour l’application créée dans le [didacticiel de mise en route](../getting-started/tutorial-getting-started-with-signalr.md) à l’aide d’un objet dynamique.

1. Installer le [extension de XUnit Runner](https://visualstudiogallery.msdn.microsoft.com/463c5987-f82b-46c8-a97e-b1cde42b9099) pour Visual Studio 2013.
2. Terminer la [didacticiel de mise en route](../getting-started/tutorial-getting-started-with-signalr.md), ou téléchargez l’application terminée à partir de [MSDN Code Gallery](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).
3. Si vous utilisez la version de téléchargement de l’application de mise en route, ouvrez **Package Manager Console** et cliquez sur **restaurer** pour ajouter le package SignalR au projet.

    ![Restaurer les Packages](unit-testing-signalr-applications/_static/image1.png)
4. Ajoutez un projet à la solution pour le test unitaire. Avec le bouton droit de votre solution dans **l’Explorateur de solutions** et sélectionnez **ajouter**, **nouveau projet...** . Sous le **c#** nœud, sélectionnez le **Windows** nœud. Sélectionnez **bibliothèque de classes**. Nommez le nouveau projet **TestLibrary** et cliquez sur **OK**.

    ![Créer la bibliothèque de tests](unit-testing-signalr-applications/_static/image2.png)
5. Ajoutez une référence dans le projet de bibliothèque de test au projet SignalRChat. Avec le bouton droit le **TestLibrary** de projet et sélectionnez **ajouter**, **référence...** . Sélectionnez le **projets** nœud sous la **Solution** nœud et cocher **SignalRChat**. Cliquez sur **OK**.

    ![Ajouter une référence de projet](unit-testing-signalr-applications/_static/image3.png)
6. Ajouter les packages SignalR, Moq et XUnit le **TestLibrary** projet. Dans le **Package Manager Console**, définissez le **projet par défaut** menu déroulant pour **TestLibrary**. Exécutez les commandes suivantes dans la fenêtre de console :

    - `Install-Package Microsoft.AspNet.SignalR`
    - `Install-Package Moq`
    - `Install-Package XUnit`

    ![Installer des Packages](unit-testing-signalr-applications/_static/image4.png)
7. Créer le fichier de test. Cliquez sur le **TestLibrary** projet puis cliquez sur **ajouter...** , **Classe**. Nommez la nouvelle classe **Tests.cs**.
8. Remplacez le contenu de Tests.cs par le code suivant.

    [!code-csharp[Main](unit-testing-signalr-applications/samples/sample1.cs)]

    Dans le code ci-dessus, un client de test est créé à l’aide de la `Mock` à partir de l’objet le [Moq](https://github.com/Moq/moq4) bibliothèque, de type [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (SignalR 2.1, affecter `dynamic` pour le type paramètre.) Le `IHubCallerConnectionContext` interface est l’objet proxy avec lequel vous appelez des méthodes sur le client. Le `broadcastMessage` fonction est alors définie pour le client fictif afin qu’il peut être appelé le `ChatHub` classe. Le moteur de test appelle ensuite la `Send` méthode de la `ChatHub` (classe), qui à son tour appelle la factices `broadcastMessage` (fonction).
9. Générez la solution en appuyant sur **F6**.
10. Exécutez le test unitaire. Dans Visual Studio, sélectionnez **Test**, **Windows**, **l’Explorateur de tests**. Dans la fenêtre de l’Explorateur de tests, cliquez sur **HubsAreMockableViaDynamic** et sélectionnez **exécuter les Tests sélectionnés**.

    ![Explorateur de tests](unit-testing-signalr-applications/_static/image5.png)
11. Vérifiez que le test a réussi en vérifiant le volet inférieur de la fenêtre de l’Explorateur de tests. La fenêtre indique que le test a réussi.

    ![Test réussi](unit-testing-signalr-applications/_static/image6.png)

<a id="type"></a>
### <a name="unit-testing-by-type"></a>Tests unitaires en type

Dans cette section, vous allez ajouter un test pour l’application créée dans le [didacticiel de mise en route](../getting-started/tutorial-getting-started-with-signalr.md) à l’aide d’une interface qui contient la méthode à tester.

1. Effectuez les étapes 1 à 7 dans le [tests unitaires avec dynamique](#dynamic) didacticiel ci-dessus.
2. Remplacez le contenu de Tests.cs par le code suivant.

    [!code-csharp[Main](unit-testing-signalr-applications/samples/sample2.cs)]

    Dans le code ci-dessus, une interface est créée en définissant la signature de la `broadcastMessage` méthode pour laquelle le moteur de test créera un client fictif. Un client fictif est ensuite créé à l’aide de la `Mock` objet de type [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (SignalR 2.1, affecter `dynamic` pour le paramètre de type.) Le `IHubCallerConnectionContext` interface est l’objet proxy avec lequel vous appelez des méthodes sur le client.

    Le test crée ensuite une instance de `ChatHub`, puis crée une version fictive de la `broadcastMessage` (méthode), qui à son tour, est appelé en appelant le `Send` méthode du concentrateur.
3. Générez la solution en appuyant sur **F6**.
4. Exécutez le test unitaire. Dans Visual Studio, sélectionnez **Test**, **Windows**, **l’Explorateur de tests**. Dans la fenêtre de l’Explorateur de tests, cliquez sur **HubsAreMockableViaDynamic** et sélectionnez **exécuter les Tests sélectionnés**.

    ![Explorateur de tests](unit-testing-signalr-applications/_static/image7.png)
5. Vérifiez que le test a réussi en vérifiant le volet inférieur de la fenêtre de l’Explorateur de tests. La fenêtre indique que le test a réussi.

    ![Test réussi](unit-testing-signalr-applications/_static/image8.png)
