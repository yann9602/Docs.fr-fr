---
uid: signalr/overview/performance/signalr-connection-density-testing-with-crank
title: "Densité de connexion SignalR test avec manivelle | Documents Microsoft"
author: tfitzmac
description: "Densité de connexion SignalR manivelle de test"
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/22/2015
ms.topic: article
ms.assetid: 148d9ca7-1af1-44b6-a9fb-91e261b9b463
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/performance/signalr-connection-density-testing-with-crank
msc.type: authoredcontent
ms.openlocfilehash: a3e8fdb47bd80d819358f9c48cd82fd51d6c0400
ms.sourcegitcommit: fe880bf4ed1c8116071c0e47c0babf3623b7f44a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2017
---
<a name="signalr-connection-density-testing-with-crank"></a>Densité de connexion SignalR manivelle de test
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article décrit comment utiliser l’outil de manivelle pour tester une application avec plusieurs clients simulés.


Une fois que votre application s’exécute dans son environnement d’hébergement (soit Azure rôle, IIS, web ou auto-hébergé à l’aide de Owin), vous pouvez tester la réponse de l’application à un haut niveau de densité de la connexion à l’aide de l’outil manivelle. L’environnement d’hébergement peut être un serveur Internet Information Services (IIS), un hôte Owin ou un rôle web Azure. (Remarque : les compteurs de Performance ne sont pas disponibles sur Azure App Service Web Apps, donc vous ne serez pas en mesure d’obtenir les données de performances à partir d’un test de densité de connexion.)

Densité de la connexion fait référence au nombre de connexions TCP simultanées qui peuvent être établies sur un serveur. Chaque connexion TCP entraîne sa propre surcharge et ouverture d’un grand nombre de connexions inactives parviendrez à créer un goulot d’étranglement de la mémoire.

[SignalR codebase](https://github.com/signalr/signalr) inclut un outil de test de charge appelé **Crank**. Vous trouverez la dernière version de manivelle dans [la branche Dev](https://github.com/SignalR/signalr/tree/dev) sur GitHub. Vous pouvez télécharger un fichier Zip archive de la branche Dev de SignalR codebase [ici](https://github.com/SignalR/SignalR/archive/dev.zip).

MANIVELLE peut servir à entièrement saturer la mémoire du serveur afin de calculer le nombre total de connexions inactives que possibles sur le matériel de serveur. Vous pouvez également vous pouvez également utiliser manivelle pour le test de charge du serveur sous une certaine quantité de mémoire est sollicitée, en identifiant des connexions jusqu'à ce qu’un nombre spécifique ou un seuil de mémoire spécifique est atteint.

Lors du test, il est important de clients à distance permet d’éviter toute concurrence pour les ressources (par exemple, les connexions TCP et mémoire). Surveiller les clients pour vous assurer qu’ils pas atteignez les goulots d’étranglement qui peuvent empêcher le serveur d’atteindre sa capacité maximale (processeur ou mémoire). Vous devrez peut-être augmenter le nombre de clients pour le chargement complet du serveur.

### <a name="running-a-connection-density-test"></a>Exécution d’un Test de densité de connexion

Cette section décrit les étapes nécessaires pour exécuter un test de densité de connexion d’une application SignalR.

1. Télécharger et générer les [codebase de la branche Dev de SignalR](https://github.com/SignalR/SignalR/archive/dev.zip). Dans une invite de commandes, accédez à &lt;répertoire du projet&gt;\src\Microsoft.AspNet.SignalR.Crank\bin\debug.
2. Déployer votre application dans son environnement d’hébergement prévue. Prenez note du point de terminaison qui utilise de votre application ; par exemple, dans l’application créée dans le [didacticiel de mise en route](../getting-started/tutorial-getting-started-with-signalr.md), le point de terminaison `http://<yourhost>:8080/signalr`.
3. Installer [compteurs de performance SignalR](signalr-performance.md#perfcounters) sur le serveur. Si votre application s’exécute sur Azure, consultez [à l’aide des compteurs de Performance SignalR dans un rôle Web Azure](using-signalr-performance-counters-in-an-azure-web-role.md).

Une fois que vous avez téléchargé et construit la base de code et installé les compteurs de performance sur votre ordinateur hôte, l’outil de ligne de commande manivelle se trouve dans le `src\Microsoft.AspNet.SignalR.Crank\bin\Debug` dossier.

Les options disponibles pour l’outil manivelle sont les suivantes :

- **/?** : Affiche l’écran d’aide. Les options disponibles sont également affichées si le **Url** paramètre est omis.
- **/ Url**: l’URL pour les connexions SignalR. Ce paramètre est obligatoire. Pour une application SignalR à l’aide du mappage par défaut, le chemin d’accès se termine par « / signalr ».
- **/ Transport**: le nom du transport utilisé. La valeur par défaut est `auto`, qui sélectionne le meilleur protocole disponible. Les options sont `WebSockets`, `ServerSentEvents`, et `LongPolling` (`ForeverFrame` n’est pas une option pour manivelle, depuis le client .NET plutôt que Internet Explorer est utilisé). Pour plus d’informations sur la sélection des transports par SignalR, consultez [du transport et secours](../getting-started/introduction-to-signalr.md#transports).
- **/ BatchSize**: le nombre de clients ajoutés dans chaque lot. La valeur par défaut est 50.
- **/ ConnectInterval**: l’intervalle en millisecondes entre l’ajout de connexions. La valeur par défaut est 500.
- **/ Connexions**: le nombre de connexions utilisé pour l’application de test de charge. La valeur par défaut est 100 000.
- **/ ConnectTimeout**: le délai d’attente en secondes avant l’abandon du test. La valeur par défaut est de 300.
- **MinServerMBytes**: les mégaoctets minimale du serveur à atteindre. La valeur par défaut est 500.
- **SendBytes**: la taille de la charge utile envoyée au serveur en octets. La valeur par défaut est 0.
- **SendInterval**: le délai en millisecondes entre les messages sur le serveur. La valeur par défaut est 500.
- **SendTimeout**: le délai d’attente en millisecondes pour les messages au serveur. La valeur par défaut est de 300.
- **ControllerUrl**: l’Url où un client destiné à héberger un concentrateur de contrôleur. La valeur par défaut est null (aucun hub contrôleur). Le concentrateur du contrôleur est démarré lorsque la session manivelle démarre ; aucune autre contact entre le contrôleur et manivelle est fait.
- **NumClients**: le nombre de clients simulés pour se connecter à l’application. La valeur par défaut est un.
- **Fichier journal**: le nom de fichier pour le fichier journal pour la série de tests. La valeur par défaut est `crank.csv`.
- **SampleInterval**: la durée en millisecondes entre les échantillons de compteur de performance. La valeur par défaut est 1000.
- **SignalRInstance**: le nom de l’instance des compteurs de performances sur le serveur. La valeur par défaut est d’utiliser l’état de connexion du client.

### <a name="example"></a>Exemple

La commande suivante teste un site nommé `pfsignalr` sur Azure qui héberge une application sur le port 8080 avec un concentrateur nommé « ControllerHub », à l’aide de 100 connexions.

`crank /Connections:100 /Url:http://pfsignalr.cloudapp.net:8080/signalr`
