---
uid: signalr/overview/getting-started/tutorial-server-broadcast-with-signalr
title: "Didacticiel : Serveur de diffusion avec SignalR 2 | Documents Microsoft"
author: tdykstra
description: "Ce didacticiel montre comment créer une application web qui utilise ASP.NET SignalR 2 pour fournir des fonctionnalités de diffusion de serveur. Diffusion de serveur signifie que commun..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/13/2014
ms.topic: article
ms.assetid: 1568247f-60b5-4eca-96e0-e661fbb2b273
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/getting-started/tutorial-server-broadcast-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: 98a7ce4991d58181177cf56976888e9fd1526987
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="tutorial-server-broadcast-with-signalr-2"></a>Didacticiel : Serveur de diffusion avec SignalR 2
====================
par [Tom Dykstra](https://github.com/tdykstra), [Tom FitzMacken](https://github.com/tfitzmac)

> Ce didacticiel montre comment créer une application web qui utilise ASP.NET SignalR 2 pour fournir des fonctionnalités de diffusion de serveur. Diffusion de serveur signifie que les communications envoyées aux clients sont lancées par le serveur. Ce scénario nécessite une approche de programmation différents scénarios d’égal à égal telles que les applications de conversation, dans lequel les communications envoyées aux clients sont lancées par un ou plusieurs clients.
> 
> L’application que vous allez créer dans ce didacticiel simule un téléscripteur, un scénario classique pour les fonctionnalités de diffusion de serveur.
> 
> Cette rubrique a été écrit à l’origine par Patrick Fletcher.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - .NET 4.5
> - SignalR version 2
>   
> 
> 
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a>À l’aide de Visual Studio 2012 avec ce didacticiel
> 
> 
> Pour utiliser Visual Studio 2012 avec ce didacticiel, procédez comme suit :
> 
> - Mise à jour votre [Package Manager](http://docs.nuget.org/docs/start-here/installing-nuget) vers la dernière version.
> - Installer le [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).
> - Dans Web Platform Installer, recherchez et installez **ASP.NET et 2013.1 des outils Web pour Visual Studio 2012**. Il installe les modèles Visual Studio pour les classes de SignalR comme **Hub**.
> - Certains modèles (tel que **classe de démarrage OWIN**) ne sera pas disponible ; dans ce cas, utilisez un fichier de classe à la place.
> 
> 
> ## <a name="tutorial-versions"></a>Versions du didacticiels
> 
> Pour plus d’informations sur les versions antérieures de SignalR, consultez [SignalR des Versions antérieures](../older-versions/index.md).
> 
> ## <a name="questions-and-comments"></a>Questions et des commentaires
> 
> Veuillez laisser des commentaires sur la façon dont vous avez aimé ce didacticiel et nous pouvons améliorer dans les commentaires en bas de la page. Si vous avez des questions qui ne sont pas directement liées à ce didacticiel, vous pouvez les valider pour le [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


## <a name="overview"></a>Vue d'ensemble

Dans ce didacticiel, vous allez créer une application de cotations boursières est représentatif des applications en temps réel dans lequel vous souhaitez effectuer régulièrement un « push », ou de diffusion, des notifications à partir du serveur à tous les clients connectés. Dans la première partie de ce didacticiel, vous allez créer une version simplifiée de l’application à partir de zéro. Dans le reste de ce didacticiel, vous allez installer un package NuGet qui contient des fonctionnalités supplémentaires et examinez le code de ces fonctionnalités.

L’application que vous allez créer dans la première partie de ce didacticiel contient une grille comportant des données boursières.

![Version initiale de StockTicker](tutorial-server-broadcast-with-signalr/_static/image1.png)

Régulièrement le serveur de façon aléatoire des actions des mises à jour et transmet les mises à jour pour tous les clients connectés. Dans le navigateur les nombres et les symboles dans le **modifier** et  **%**  colonnes changent dynamiquement en réponse à des notifications à partir du serveur. Si vous ouvrez des navigateurs supplémentaires à la même URL, elles affichent toutes les mêmes données et les mêmes modifications aux données simultanément.

Ce didacticiel contient les sections suivantes :

- [Composants requis](#prerequisites)
- [Créer le projet](#createproject)
- [Définissez le code serveur](#server)
- [Définissez le code client](#client)
- [Tester l’application](#test)
- [Activer la journalisation](#enablelogging)
- [Installer et examinez l’exemple StockTicker complète](#fullsample)
- [Étapes suivantes](#nextsteps)

Le [Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) package NuGet installe une application de cotations boursières exemple simulé dans un projet Visual Studio.

> [!NOTE]
> Si vous ne souhaitez pas utiliser les étapes de génération de l’application, vous pouvez installer le package SignalR.Sample dans un nouveau projet d’Application Web ASP.NET vide. Si vous installez le package NuGet sans exécuter les étapes de ce didacticiel, **vous devez suivre les instructions dans le fichier readme.txt**. Pour exécuter le package, vous devez ajouter une classe de démarrage OWIN qui appelle la méthode ConfigureSignalR dans le package installé. Vous recevrez une erreur si vous n’ajoutez pas de la classe de démarrage OWIN.


<a id="prerequisites"></a>

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que vous disposez de Visual Studio 2013 est installé sur votre ordinateur. Si vous n’avez pas Visual Studio, consultez [téléchargements ASP.NET](https://www.asp.net/downloads) pour obtenir la version gratuite Visual Studio 2013 Express.

<a id="createproject"></a>

## <a name="create-the-project"></a>Créer le projet

1. À partir de la **fichier** menu, cliquez sur **nouveau projet**.
2. Dans le **nouveau projet** boîte de dialogue, développez **c#** sous **modèles** et sélectionnez **Web**.
3. Sélectionnez le **Application Web ASP.NET vide** modèle, nommez le projet *SignalR.StockTicker*, puis cliquez sur **OK**.

    ![Boîte de dialogue Nouveau projet de test](tutorial-server-broadcast-with-signalr/_static/image2.png)
4. Dans le **nouveau ASP.NET** fenêtre du projet, laissez le champ **vide** sélectionné, cliquez sur **créer un projet de**.

    ![Boîte de dialogue Nouveau projet ASP](tutorial-server-broadcast-with-signalr/_static/image3.png)

<a id="server"></a>

## <a name="set-up-the-server-code"></a>Définissez le code serveur

Dans cette section, vous définissez le code qui s’exécute sur le serveur.

### <a name="create-the-stock-class"></a>Créer la classe de Stock

Vous commencez par créer la classe de modèle de Stock que vous allez utiliser pour stocker et transmettre des informations sur une action.

1. Créer un nouveau fichier de classe dans le dossier du projet, nommez-le *Stock.cs*, puis remplacez le code du modèle par le code suivant :

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample1.cs)]

    Les deux propriétés que vous allez définir lorsque vous créez des stocks sont le symbole (par exemple, MSFT pour Microsoft) et le prix. Les autres propriétés dépendent comment et quand vous définissez les prix. La première fois que vous définissez les prix, la valeur obtient propagée aux DayOpen. Une fois suivantes lorsque vous définissez les prix, la modification et les valeurs de propriété ChangementPourcentage sont calculées selon la différence entre le prix et DayOpen.

### <a name="create-the-stockticker-and-stocktickerhub-classes"></a>Créer les classes StockTicker et StockTickerHub

L’API de concentrateur SignalR vous permet de gérer l’interaction avec le serveur au client. Une classe StockTickerHub qui dérive de la classe de concentrateur SignalR gérera reçoit des connexions et des appels de méthode à partir de clients. Vous devez également mettre à jour les données de cotation boursière et exécuter un objet de minuterie pour déclencher régulièrement des mises à jour des prix, indépendamment des connexions client. Vous ne pouvez pas mettre ces fonctions dans une classe de concentrateur, étant donné que les instances de concentrateur sont temporaires. Une instance de classe de concentrateur est créée pour chaque opération sur le concentrateur, tels que les connexions et les appels du client au serveur. Par conséquent, le mécanisme qui conserve les données de cotation boursière, met à jour des prix et diffuse les mises à jour des prix doit s’exécuter dans une classe distincte, que vous devez nommer StockTicker.

![Diffusion de StockTicker](tutorial-server-broadcast-with-signalr/_static/image5.png)

Vous souhaitez seulement une instance de la classe StockTicker exécutés sur le serveur, vous devez définir une référence à partir de chaque instance StockTickerHub à l’instance de StockTicker singleton. La classe StockTicker doit être en mesure de diffusion pour les clients, car il comporte les données de stock et déclenche des mises à jour, mais StockTicker n’est pas une classe de concentrateur. Par conséquent, la classe StockTicker doit obtenir une référence à l’objet de contexte de connexion de concentrateur SignalR. Il peut ensuite utiliser l’objet de contexte de connexion SignalR pour diffuser aux clients.

1. Dans **l’Explorateur de solutions**, cliquez sur le projet, cliquez sur **ajouter | Classe de concentrateur SignalR (v2)**.
2. Nommez le nouveau concentrateur *StockTickerHub.cs*, puis cliquez sur **ajouter**. Les packages NuGet de SignalR seront ajoutés à votre projet.
3. Remplacez le code du modèle par le code suivant :

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample2.cs)]

    Le [Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx) classe est utilisée pour définir des méthodes, les clients peuvent appeler sur le serveur. Vous définissez une méthode : `GetAllStocks()`. Lorsqu’un client se connecte initialement au serveur, il appelle cette méthode pour obtenir une liste de tous les stocks avec leurs prix actuel. La méthode peut s’exécuter de façon synchrone et renvoyer `IEnumerable<Stock>` , car il retourne les données de la mémoire. Si la méthode avait obtenir les données en effectuant ce qui nécessiterait en attente, telles que la recherche d’une base de données ou un appel de service web, vous devez spécifier `Task<IEnumerable<Stock>>` comme valeur de retour pour permettre le traitement asynchrone. Pour plus d’informations, consultez [ASP.NET SignalR concentrateurs API Guide - Server - quand exécuter de façon asynchrone](../guide-to-the-api/hubs-api-guide-server.md#asyncmethods).

    L’attribut HubName spécifie comment le concentrateur sera référencé dans le code JavaScript sur le client. Le nom par défaut sur le client si vous n’utilisez pas cet attribut est une version de casse mixte, le nom de classe, qui dans ce cas serait stockTickerHub.

    Comme vous le verrez plus tard lorsque vous créez la classe StockTicker, une instance de singleton de cette classe est créée dans sa propriété d’Instance statique. Instance singleton de StockTicker reste en mémoire, quel que soit le nombre de clients se connecte ou déconnecter que cette instance est utilisé par la méthode GetAllStocks pour retourner des informations de stock en cours.
4. Créer un nouveau fichier de classe dans le dossier du projet, nommez-le *StockTicker.cs*, puis remplacez le code du modèle par le code suivant :

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample3.cs)]

    Étant donné que plusieurs threads exécutera la même instance de StockTicker code, la classe StockTicker doit être thread-safe.

    ### <a name="storing-the-singleton-instance-in-a-static-field"></a>Le stockage de l’instance de singleton dans un champ statique

    Le code initialise statiques \_champ d’instance qui stocke la propriété d’Instance avec une instance de la classe et cela est la seule instance de la classe qui peut être créée, car le constructeur est marqué comme privé. [L’initialisation tardive](https://msdn.microsoft.com/library/dd997286.aspx) est utilisé pour le \_champ d’instance, pas pour des raisons de performances, mais sur, vérifiez que la création de l’instance est thread-safe.

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample4.cs)]

    Chaque fois qu'un client se connecte au serveur, une nouvelle instance de la classe StockTickerHub en cours d’exécution dans un thread distinct Obtient l’instance de singleton StockTicker à partir de la propriété statique StockTicker.Instance, que vous avez vu plus haut dans la classe StockTickerHub.

    ### <a name="storing-stock-data-in-a-concurrentdictionary"></a>Le stockage des données de cotation boursière dans un ConcurrentDictionary

    Le constructeur initialise la \_collection stocks avec quelques exemples de données stock et GetAllStocks renvoie les stocks. Comme vous l’avez vu précédemment, cette collection d’actions est à son tour retournée par StockTickerHub.GetAllStocks qui est une méthode de serveur dans la classe de concentrateur, les clients peuvent appeler.

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample5.cs)]

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample6.cs)]

    La collection d’actions est définie comme un [ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx) type pour la sécurité des threads. En guise d’alternative, vous pouvez utiliser un [dictionnaire](https://msdn.microsoft.com/library/xfhwa508.aspx) de l’objet et les verrous explicitement le dictionnaire lorsque vous apportez des modifications à ce dernier.

    Pour cet exemple d’application, il est OK pour stocker les données d’application en mémoire et perdre de données lors de la suppression de l’instance de StockTicker. Dans une application réelle vous le feriez avec un magasin de données back-end, par exemple une base de données.

    ### <a name="periodically-updating-stock-prices"></a>Mise à jour périodique des actions

    Le constructeur démarre un objet de minuterie qui appelle régulièrement des méthodes qui mettent à jour des actions de façon aléatoire.

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample7.cs)]

    UpdateStockPrices est appelée par la minuterie, dont la valeur NULL dans le paramètre state. Avant la mise à jour des prix, un verrou le \_updateStockPricesLock objet. Le code vérifie si un autre thread est déjà mise à jour des prix, et il appelle ensuite TryUpdateStockPrice sur chaque action dans la liste. La méthode TryUpdateStockPrice décide s’il faut modifier le prix de stock et la quantité pour le modifier. Si le prix de stock est modifié, BroadcastStockPrice est appelée pour diffuser la modification de cotation à tous les clients connectés.

    Le \_updatingStockPrices indicateur est marqué comme [volatile](https://msdn.microsoft.com/library/x13ttww7.aspx) pour vous assurer que l’accès lui est thread-safe.

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample8.cs)]

    Dans une application réelle, la méthode TryUpdateStockPrice appelez un service web pour rechercher le prix ; Dans ce code, il utilise un générateur de nombres aléatoires pour apporter des modifications de façon aléatoire.

    ### <a name="getting-the-signalr-context-so-that-the-stockticker-class-can-broadcast-to-clients"></a>Obtenir le contexte de SignalR afin que la classe StockTicker peut diffuser vers les clients

    Étant donné que les modifications des prix proviennent ici de l’objet StockTicker, il s’agit de l’objet qui doit appeler une méthode updateStockPrice sur tous les clients connectés. Dans une classe de concentrateur, vous avez une API pour appeler les méthodes du client, mais StockTicker ne dérive pas de la classe de concentrateur et n’a pas une référence à n’importe quel objet de concentrateur. Par conséquent, pour pouvoir diffuser aux clients connectés, la classe StockTicker doit obtenir l’instance de contexte SignalR pour la classe StockTickerHub et l’utiliser pour appeler des méthodes sur les clients.

    Le code obtient une référence au contexte SignalR lorsqu’il crée l’instance de la classe singleton passes qui font référence au constructeur, et le constructeur place dans la propriété de Clients.

    Il existe deux raisons pour lesquelles vous souhaitez obtenir le contexte d’une seule fois : obtenir le contexte est une opération coûteuse et mise en route une fois garantit que l’ordre prévu de messages envoyés aux clients est conservé.

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample9.cs)]

    Obtention de la propriété de Clients du contexte et leur placement dans la propriété StockTickerClient vous permettent d’écrire du code pour appeler des méthodes de client qui a le même aspect comme il le ferait dans une classe de concentrateur. Par exemple, pour la diffusion à tous les clients, vous pouvez écrire Clients.All.updateStockPrice(stock).

    La méthode updateStockPrice que vous appelez dans BroadcastStockPrice n’existe pas encore ; vous l’ajouterez ultérieurement lorsque vous écrivez du code qui s’exécute sur le client. Vous pouvez faire référence à updateStockPrice ici Clients.All étant dynamique, ce qui signifie que l’expression est évaluée au moment de l’exécution. Lorsque cet appel de méthode s’exécute, SignalR envoie le nom de méthode et la valeur du paramètre au client et si le client possède une méthode nommée updateStockPrice, cette méthode est appelée et la valeur du paramètre est passée à ce dernier.

    Clients.All signifie envoyer à tous les clients. SignalR vous donne des autres options pour spécifier les clients ou les groupes de clients pour envoyer à. Pour plus d’informations, consultez [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx).

### <a name="register-the-signalr-route"></a>Inscrire l’itinéraire SignalR

Le serveur doit connaître l’URL à intercepter et diriger vers SignalR. Pour ce faire, vous allez ajouter et classe de démarrage OWIN.

1. Dans **l’Explorateur de solutions**, cliquez sur le projet, puis cliquez sur **ajouter | Classe de démarrage OWIN**. Nom de la classe **Startup.cs**.
2. Remplacez le code dans **Startup.cs** par le code suivant.

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample10.cs)]

Vous avez maintenant terminé la configuration du code serveur. Dans la section suivante, vous allez configurer le client.

<a id="client"></a>

## <a name="set-up-the-client-code"></a>Définissez le code client

1. Créer un nouveau fichier HTML dans le dossier du projet et nommez-le *StockTicker.html*.
2. Remplacez le code du modèle par le code suivant.

    [!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample11.html)]

    Le code HTML crée une table avec 5 colonnes, une ligne d’en-tête et une ligne de données avec une seule cellule qui s’étend sur toutes les 5 colonnes. La ligne de données affiche « chargement... » et ne sera affichée que momentanément lorsque l’application démarre. Code JavaScript sera supprimer cette ligne et ajoutez dans ses lignes sur place avec des données boursières récupérées à partir du serveur.

    Les balises de script spécifient le fichier de script jQuery, le fichier de script SignalR core, le fichier de script de proxy SignalR et un fichier de script StockTicker que vous allez créer ultérieurement. Le fichier de script de proxy SignalR, qui spécifie l’URL « concentrateurs signalr / / », est généré dynamiquement et définit les méthodes de proxy pour les méthodes sur la classe de concentrateur, dans ce cas pour StockTickerHub.GetAllStocks. Si vous préférez, vous pouvez générer manuellement ce fichier JavaScript en utilisant [SignalR utilitaires](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) et désactiver la création de fichier dynamique dans l’appel de méthode MapHubs.
3. > [!IMPORTANT]
 > Assurez-vous que le fichier JavaScript fait référence dans *StockTicker.html* sont corrects. Autrement dit, vous assurer que la version de jQuery dans votre balise script (1.10.2 dans l’exemple) est identique à la version de votre projet jQuery *Scripts* dossier et vous assurer que la version SignalR dans votre balise script est le même que SignalR version de votre projet *Scripts* dossier. Modifier les noms de fichiers dans les balises de script, si nécessaire.
4. Dans **l’Explorateur de solutions**, avec le bouton droit *StockTicker.html*, puis cliquez sur **définir comme Page de démarrage**.
5. Créez un nouveau fichier JavaScript dans le dossier du projet et nommez-le *StockTicker.js*...
6. Remplacez le code du modèle par le code suivant :

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample12.js)]

    $.connection fait référence aux proxys SignalR. Le code obtient une référence au proxy pour la classe StockTickerHub et le place dans la variable d’action. Le nom du proxy est celui qui a été défini par l’attribut [HubName] :

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample13.js)]

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample14.cs)]

    Une fois que toutes les fonctions et variables sont définies, la dernière ligne de code dans le fichier initialise la connexion de SignalR en appelant la fonction de démarrage SignalR. La fonction de démarrage s’exécute de façon asynchrone et retourne un [jQuery différé objet](http://api.jquery.com/category/deferred-object/), ce qui signifie que vous pouvez appeler la fonction terminée pour spécifier la fonction à appeler lorsque l’opération asynchrone est terminée...

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample15.js)]

    La fonction init appelle la fonction getAllStocks sur le serveur et utilise les informations renvoyées par le serveur pour mettre à jour la table de stock. Notez que par défaut, vous devez utiliser une casse mixte sur le client même si le nom de la méthode est sur le serveur de la casse pascal. La règle de casse mixte s’applique uniquement aux méthodes, pas les objets. Par exemple, vous reporter à stock. Symbole et stock. Prix ni stock.symbol ni stock.price.

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample16.js)]

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample17.cs)]

    Si vous souhaitez utiliser la casse pascal sur le client, ou si vous souhaitez utiliser un nom de méthode complètement différent, vous pourriez décorer avec l’attribut HubMethodName, la méthode de concentrateur la même façon, vous décorée la classe du concentrateur avec l’attribut HubName.

    Dans la méthode init, HTML pour une ligne de la table est créée pour chaque objet de stock reçu à partir du serveur en appelant formatStock aux propriétés de format de l’objet de stock, puis par un appel à supplanter (qui est défini dans la partie supérieure de *StockTicker.js*) pour remplacer les espaces réservés dans la variable rowTemplate avec les valeurs de propriété d’objet stock. Le code HTML résultant est alors ajouté à la table de stock.

    Vous appelez init en la passant comme une fonction de rappel qui s’exécute après que la fonction start asynchrone se termine. Si vous avez appelé init comme une instruction JavaScript séparée après l’appel de début, la fonction échoue, car elle s’exécute immédiatement sans attendre que la fonction de démarrage à la fin de l’établissement de la connexion. Dans ce cas, la fonction init tentez d’appeler la fonction getAllStocks avant l’établie de la connexion au serveur.

    Lorsque le serveur change les prix d’une action, il appelle l’updateStockPrice sur les clients connectés. La fonction est ajoutée à la propriété de client du proxy stockTicker pour rendre disponibles aux appels à partir du serveur.

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample18.js)]

    La fonction updateStockPrice met en forme un objet stock reçu du serveur dans une ligne de table, la même façon que dans la fonction d’initialisation. Toutefois, au lieu de l’ajout de la ligne à la table, il recherche la ligne actuelle de l’action de la table et remplace cette ligne par la nouvelle.

<a id="test"></a>

## <a name="test-the-application"></a>Tester l’application

1. Appuyez sur F5 pour exécuter l’application en mode débogage.

    La table stock affiche initialement les « chargement... » ligne, puis après un court délai que le stock de données initial s’affiche, et lancez la bourse à modifier.

    ![Chargement en cours](tutorial-server-broadcast-with-signalr/_static/image6.png)

    ![Tableau d’actions initial](tutorial-server-broadcast-with-signalr/_static/image7.png)

    ![Tableau d’actions recevoir des modifications à partir du serveur](tutorial-server-broadcast-with-signalr/_static/image8.png)
2. Copiez l’URL de la barre d’adresse de navigateur et collez-le dans un ou plusieurs nouvelles fenêtres de navigateur.

    L’affichage initial de stock est le même que le navigateur en premier et les modifications se produisent simultanément.
3. Fermez tous les navigateurs et ouvrir une nouvelle fenêtre de navigateur, puis accédez à la même URL.

    L’objet singleton StockTicker a continué à s’exécuter sur le serveur, afin que l’affichage de la table stock n’affiche que les stocks ont continué à modifier. (Vous ne voyez la table initiale avec zéro modifier les chiffres.)
4. Fermez le navigateur.

<a id="enablelogging"></a>

## <a name="enable-logging"></a>Activer la journalisation

SignalR possède une fonction de journalisation intégrés que vous pouvez activer sur le client pour faciliter le dépannage. Dans cette section, vous activez la journalisation et voir des exemples qui montrent comment journaux vous indiquer parmi les méthodes de transport suivants à l’aide de SignalR :

- [WebSockets](http://en.wikipedia.org/wiki/WebSocket), pris en charge par IIS 8 et les navigateurs actuels.
- [Événements de serveur a été envoyé](http://en.wikipedia.org/wiki/Server-sent_events), pris en charge par les navigateurs Internet Explorer.
- [Forever frame](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe), pris en charge par Internet Explorer.
- [AJAX longue d’interrogation](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling), pris en charge par tous les navigateurs.

Pour une connexion donnée, SignalR choisit la meilleure méthode de transport qui prennent en charge par le serveur et le client.

1. Ouvrez *StockTicker.js* et ajouter une ligne de code pour activer la journalisation immédiatement avant le code qui initialise la connexion à la fin du fichier :

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample19.js)]
2. Appuyez sur F5 pour exécuter le projet.
3. Ouvrez la fenêtre Outils de développement de votre navigateur et sélectionnez la Console pour afficher les journaux. Vous devrez peut-être actualiser la page pour afficher les journaux de la négociation de la méthode de transport pour une nouvelle connexion de Signalr.

    Si vous exécutez Internet Explorer 10 sur Windows 8 (IIS 8), le mode de transport est WebSocket.

    ![Internet Explorer 10 IIS 8 de la Console](tutorial-server-broadcast-with-signalr/_static/image9.png)

    Si vous exécutez Internet Explorer 10 sous Windows 7 (IIS 7.5), la méthode de transport est iframe.

    ![Internet Explorer 10 Console, IIS 7.5](tutorial-server-broadcast-with-signalr/_static/image10.png)

    Dans Firefox, installez le complément Firebug pour obtenir une fenêtre de Console. Si vous utilisez Firefox 19 sur Windows 8 (IIS 8), le mode de transport est WebSocket.

    ![Firefox 19 IIS 8 Websockets](tutorial-server-broadcast-with-signalr/_static/image11.png)

    Si vous utilisez Firefox 19 sur Windows 7 (IIS 7.5), la méthode de transport est envoyées par le serveur des événements.

    ![Firefox 19 de la Console IIS 7.5](tutorial-server-broadcast-with-signalr/_static/image12.png)

<a id="fullsample"></a>

## <a name="install-and-review-the-full-stockticker-sample"></a>Installer et examinez l’exemple StockTicker complète

L’application StockTicker qui est installée par le [Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) package NuGet inclut plus de fonctionnalités que la version simplifiée que vous venez de créer à partir de zéro. Dans cette section du didacticiel, vous installez le package NuGet et passez en revue les nouvelles fonctionnalités et le code qui implémente les. Si vous installez le package sans effectuer les étapes précédentes de ce didacticiel, vous devez ajouter une classe de démarrage OWIN à votre projet. Cette étape est expliquée dans le fichier readme.txt pour le package NuGet.

### <a name="install-the-signalrsample-nuget-package"></a>Installez le package NuGet de SignalR.Sample

1. Dans **l’Explorateur de solutions**, cliquez sur le projet, cliquez sur **gérer les Packages NuGet**.
2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **Online**, entrez *SignalR.Sample* dans les **recherche en ligne** zone, puis cliquez sur  **Installer** dans les **SignalR.Sample** package.

    ![Installer le package de SignalR.Sample](tutorial-server-broadcast-with-signalr/_static/image13.png)
3. Dans **l’Explorateur de solutions**, développez le *SignalR.Sample* dossier qui a été créé par l’installation du package SignalR.Sample.
4. Dans le *SignalR.Sample* dossier, avec le bouton droit *StockTicker.html*, puis cliquez sur **définir comme Page de démarrage**.

    > [!NOTE]
    > Installation de NuGet de SignalR.Sample le package peut modifier la version de jQuery que vous avez dans votre *Scripts* dossier. La nouvelle *StockTicker.html* fichier que le package installe dans le *SignalR.Sample* dossier sera synchronisée avec la version de jQuery que le package installe, mais si vous souhaitez exécuter votre d’origine*StockTicker.html* de fichiers à nouveau, vous devrez peut-être mettre à jour la référence de jQuery dans la balise script premier.

### <a name="run-the-application"></a>Exécuter l'application

1. Appuyez sur F5 pour exécuter l'application.

    En plus de la grille que vous avez vu précédemment, l’application de cotations boursières complète affiche une fenêtre de défilement horizontal qui affiche les mêmes données de stock. Lorsque vous exécutez l’application pour la première fois, le « marché » est « fermé » et vous voyez une grille statique et une fenêtre de code qui n’est pas le défilement.

    ![Début d’écran StockTicker](tutorial-server-broadcast-with-signalr/_static/image14.png)

    Lorsque vous cliquez sur **Open Market**, le **téléscripteur de Live** zone commence à faire défiler horizontalement et que le serveur commence à diffusion régulièrement les modifications des prix de stock de façon aléatoire. Chaque fois qu’une cotation change, les deux le **Table de Stock Live** grille et la **téléscripteur de Live** zone sont mis à jour. Lors de la modification de prix d’une action est positive, le stock est affiché avec un arrière-plan vert, et lorsque la modification est négative, le stock est affiché avec un arrière-plan rouge.

    ![Ouvrir le marché StockTicker application,](tutorial-server-broadcast-with-signalr/_static/image15.png)

    Le **marché fermer** bouton arrête les modifications et le défilement du titre et le **réinitialiser** bouton réinitialise toutes les données de stock à l’état initial avant le début des modifications des prix. Si vous ouvrez plusieurs fenêtres du navigateur et accédez à la même URL, vous consultez les mêmes données mis à jour dynamiquement en même temps dans chaque navigateur. Lorsque vous cliquez sur un des boutons, tous les navigateurs répondent de la même manière en même temps.

### <a name="live-stock-ticker-display"></a>Affichage de téléscripteur dynamique

Le **téléscripteur de Live** affichage est une liste non triée dans un élément div mis en forme en une seule ligne par les styles CSS. Le titre est initialisé et mise à jour de la même façon que la table : en remplaçant les espaces réservés dans une &lt;li&gt; chaîne de modèle et ajouter dynamiquement le &lt;li&gt; éléments à la &lt;ul&gt; élément. Le défilement est effectué à l’aide de la fonction animer jQuery pour faire varier la marge gauche de la liste non triée au sein de la balise div.

Les cotations boursières HTML :

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample20.html)]

Les cotations boursières CSS :

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample21.html)]

Le code jQuery qui permet de faire défiler :

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample22.js)]

### <a name="additional-methods-on-the-server-that-the-client-can-call"></a>Sur le serveur que le client peut appeler des méthodes supplémentaires

La classe StockTickerHub définit quatre méthodes supplémentaires que le client peut appeler :

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample23.cs)]

OpenMarket, CloseMarket et réinitialisation sont appelées en réponse aux boutons en haut de la page. Elles illustrent le modèle d’un client de déclenchement d’un changement d’état est immédiatement propagée à tous les clients. Chacune de ces méthodes appelle une méthode dans la classe StockTicker qui affecte l’état de marché modifier et diffuse ensuite le nouvel état.

Dans la classe StockTicker, l’état du marché est géré par une propriété MarketState qui retourne une valeur d’énumération MarketState :

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample24.cs)]

Chacune des méthodes qui modifient l’état de marché le faire à l’intérieur d’un bloc de verrous, car la classe StockTicker doit être thread-safe :

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample25.cs)]

Pour vous assurer que ce code est thread-safe, les \_marketState champ qui stocke la propriété MarketState est marquée comme volatile,

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample26.cs)]

Les méthodes BroadcastMarketStateChange et BroadcastMarketReset sont similaires à la méthode BroadcastStockPrice que vous avez déjà vu, à ceci près qu’ils appellent des méthodes différentes définies au niveau du client :

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample27.cs)]

### <a name="additional-functions-on-the-client-that-the-server-can-call"></a>Sur le client que le serveur peut appeler des fonctions supplémentaires

La fonction updateStockPrice gère désormais la grille et l’affichage du titre, et il utilise jQuery.Color Flash couleurs rouges et vertes.

Nouvelles fonctions dans *SignalR.StockTicker.js* activer et désactiver les boutons en fonction de marché état, et ils arrêter ou démarrer le défilement horizontal de cotations fenêtre. Étant donné que plusieurs fonctions sont ajoutées à ticker.client, le [jQuery étendre la fonction](http://api.jquery.com/jQuery.extend/) est utilisé pour les ajouter.

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample28.js)]

### <a name="additional-client-setup-after-establishing-the-connection"></a>Programme d’installation de client supplémentaires après avoir établi la connexion

Une fois que le client établit la connexion, il a un travail supplémentaire pour faire : déterminer si le marché est ouverte ou fermée afin d’appeler le marketOpened approprié ou la fonction de marketClosed et d’attacher les appels de méthode de serveur pour les boutons.

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample29.js)]

Les méthodes de serveur ne sont pas connectés jusqu'à les boutons jusqu'à ce qu’une fois la connexion est établie, afin que le code ne peut pas essayer d’appeler les méthodes du serveur avant d’être disponibles.

<a id="nextsteps"></a>

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment programmer une application SignalR qui diffuse des messages à partir du serveur à tous les clients connectés, à la fois sur une base périodique et en réponse aux notifications à partir de n’importe quel client. Le modèle de l’utilisation d’une instance de singleton multithread pour gérer l’état du serveur peut également également être utilisé dans plusieurs scénarios de jeu en ligne. Pour obtenir un exemple, consultez [le jeu ShootR basé sur SignalR](https://github.com/NTaylorMullen/ShootR).

Pour consulter des didacticiels qui montrent des scénarios de communication d’égal à égal, consultez [mise en route avec SignalR](introduction-to-signalr.md) et [en temps réel la mise à jour avec SignalR](tutorial-high-frequency-realtime-with-signalr.md).

Pour en savoir plus avancées concepts de développement SignalR, visitez les sites suivants pour le code source de SignalR et ressources :

- [ASP.NET SignalR](../../index.md)
- [Projet SignalR](http://signalr.net/)
- [SignalR Github et exemples](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

Pour une procédure pas à pas sur la façon de déployer une application SignalR dans Azure, consultez [à l’aide de SignalR avec les applications Web dans Azure App Service](../deployment/using-signalr-with-azure-web-sites.md). Pour plus d’informations sur la façon de déployer un projet web Visual Studio sur un Site Web de Windows Azure, consultez [créer une application web ASP.NET dans Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).
