---
uid: signalr/overview/guide-to-the-api/mapping-users-to-connections
title: Mappage des utilisateurs de SignalR aux connexions | Documents Microsoft
author: tfitzmac
description: "Cette rubrique montre comment conserver les informations sur les utilisateurs et leurs connexions. Patrick Fletcher permettait d’écrire cette rubrique. Versions du logiciel utilisées dans cette rubrique..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/30/2014
ms.topic: article
ms.assetid: f80c08b1-3f1f-432c-980c-c7b6edeb31b1
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/guide-to-the-api/mapping-users-to-connections
msc.type: authoredcontent
ms.openlocfilehash: c4f95a3b65c57dd7cb7c5c7f1ee09daa17fa9616
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="mapping-signalr-users-to-connections"></a>Mappage des utilisateurs pour les connexions SignalR
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cette rubrique montre comment conserver les informations sur les utilisateurs et leurs connexions.
> 
> Patrick Fletcher permettait d’écrire cette rubrique.
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
> ## <a name="previous-versions-of-this-topic"></a>Versions précédentes de cette rubrique
> 
> Pour plus d’informations sur les versions antérieures de SignalR, consultez [SignalR des Versions antérieures](../older-versions/index.md).
> 
> ## <a name="questions-and-comments"></a>Questions et des commentaires
> 
> Veuillez laisser des commentaires sur la façon dont vous avez aimé ce didacticiel et nous pouvons améliorer dans les commentaires en bas de la page. Si vous avez des questions qui ne sont pas directement liées à ce didacticiel, vous pouvez les valider pour le [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


## <a name="introduction"></a>Introduction

Chaque client qui se connecte à un concentrateur passe un id de connexion unique. Vous pouvez récupérer cette valeur dans le `Context.ConnectionId` propriété du contexte du concentrateur. Si votre application doit associer un utilisateur à l’id de connexion et de conserver ce mappage, vous pouvez utiliser une des opérations suivantes :

- [Le fournisseur de code utilisateur (SignalR 2)](#IUserIdProvider)
- [Stockage en mémoire](#inmemory), par exemple un dictionnaire
- [Groupe SignalR pour chaque utilisateur](#groups)
- [Stockage permanent, externe](#database), tel qu’une table de base de données ou le stockage de table Windows Azure

Chacune de ces implémentations est indiqué dans cette rubrique. Vous utilisez la `OnConnected`, `OnDisconnected`, et `OnReconnected` méthodes de la `Hub` classe pour effectuer le suivi de l’état de connexion utilisateur.

La meilleure approche pour votre application dépend de :

- Le nombre de serveurs web qui héberge votre application.
- Si vous devez obtenir la liste des utilisateurs actuellement connectés.
- S’il faut conserver les informations de groupe et utilisateur lorsque l’application ou le serveur redémarre.
- Indique si la latence de l’appel à un serveur externe est un problème.

Le tableau suivant indique quelle approche fonctionne pour ces considérations.

|  | Plusieurs serveurs | Obtenir la liste des utilisateurs actuellement connectés. | Conserver les informations d’après le redémarrage | Performances optimales |
| --- | --- | --- | --- | --- |
| Fournisseur de nom d’utilisateur | ![](mapping-users-to-connections/_static/image1.png) |  |  | ![](mapping-users-to-connections/_static/image2.png) |
| En mémoire |  | ![](mapping-users-to-connections/_static/image3.png) |  | ![](mapping-users-to-connections/_static/image4.png) |
| Groupes d’utilisateur unique | ![](mapping-users-to-connections/_static/image5.png) |  |  | ![](mapping-users-to-connections/_static/image6.png) |
| Permanentes, externe | ![](mapping-users-to-connections/_static/image7.png) | ![](mapping-users-to-connections/_static/image8.png) | ![](mapping-users-to-connections/_static/image9.png) |  |

<a id="IUserIdProvider"></a>

## <a name="iuserid-provider"></a>Fournisseur de IUserID

Cette fonctionnalité permet aux utilisateurs de spécifier quel est le nom d’utilisateur basé sur un IRequest via une nouvelle interface IUserIdProvider.

**Le IUserIdProvider**

[!code-csharp[Main](mapping-users-to-connections/samples/sample1.cs)]

Par défaut, il y aura une implémentation qui utilise l’utilisateur `IPrincipal.Identity.Name` comme nom d’utilisateur. Pour modifier ce paramètre, enregistrer votre implémentation de `IUserIdProvider` avec l’hôte global au démarrage de votre application :

[!code-csharp[Main](mapping-users-to-connections/samples/sample2.cs)]

À partir d’au sein d’un concentrateur, vous serez en mesure d’envoyer des messages à ces utilisateurs via l’API suivante :

**Envoi d’un message à un utilisateur spécifique**

[!code-csharp[Main](mapping-users-to-connections/samples/sample3.cs?highlight=5)]

<a id="inmemory"></a>

## <a name="in-memory-storage"></a>Stockage en mémoire

Les exemples suivants montrent comment conserver les informations de connexion et d’utilisateur dans un dictionnaire qui est stocké en mémoire. Le dictionnaire utilise un `HashSet` pour stocker l’id de connexion. À tout moment, un utilisateur peut avoir plusieurs connexions à l’application de SignalR. Par exemple, un utilisateur qui est connecté via plusieurs appareils ou plusieurs onglets de navigateur aurait plus d’un id de connexion.

Si l’application s’arrête, toutes les informations sont perdues, mais il est nouveau rempli que les utilisateurs de nouveau établissent les connexions. Stockage en mémoire ne fonctionne pas si votre environnement inclut plusieurs serveurs web, parce que chaque serveur n’aurait une collection distincte de connexions.

Le premier exemple montre une classe qui gère le mappage des utilisateurs pour les connexions. La clé pour le HashSet sera le nom d’utilisateur.

[!code-csharp[Main](mapping-users-to-connections/samples/sample4.cs)]

L’exemple suivant montre comment utiliser la classe de mappage de connexion d’un concentrateur. L’instance de la classe est stockée dans un nom de variable `_connections`.

[!code-csharp[Main](mapping-users-to-connections/samples/sample5.cs)]

<a id="groups"></a>

## <a name="single-user-groups"></a>Groupes d’utilisateur unique

Vous pouvez créer un groupe pour chaque utilisateur et puis envoyez un message à ce groupe lorsque vous souhaitez atteindre seul cet utilisateur. Le nom de chaque groupe est le nom de l’utilisateur. Si un utilisateur a plusieurs connexions, chaque id de connexion est ajouté au groupe de l’utilisateur.

Vous devez supprimer pas manuellement l’utilisateur à partir du groupe lorsque l’utilisateur se déconnecte. Cette action est effectuée automatiquement par l’infrastructure SignalR.

L’exemple suivant montre comment implémenter des groupes d’utilisateur unique.

[!code-csharp[Main](mapping-users-to-connections/samples/sample6.cs)]

<a id="database"></a>

## <a name="permanent-external-storage"></a>Stockage permanent, externe

Cette rubrique montre comment utiliser une base de données ou le stockage de table Windows Azure pour stocker les informations de connexion. Cette approche fonctionne lorsque vous avez plusieurs serveurs web, car chaque serveur web peut interagir avec le même référentiel de données. Si vos serveurs web arrêter le travail ou le redémarrage de l’application, le `OnDisconnected` méthode n’est pas appelée. Par conséquent, il est possible que votre référentiel de données aura des enregistrements pour les ID de connexion qui ne sont plus valides. Pour nettoyer ces enregistrements orphelins, vous pouvez souhaiter annuler toute connexion qui a été créée en dehors d’un laps de temps qui s’applique à votre application. Les exemples de cette section incluent une valeur pour le suivi des modifications lorsque la connexion a été créée, mais n’affichent pas comment nettoyer les anciens enregistrements, car vous souhaiterez faire en tant que processus en arrière-plan.

### <a name="database"></a>Base de données

Les exemples suivants montrent comment conserver les informations de connexion et d’utilisateur dans une base de données. Vous pouvez utiliser n’importe quel technologie d’accès aux données ; Toutefois, l’exemple ci-dessous montre comment définir des modèles à l’aide d’Entity Framework. Ces modèles d’entité correspondent aux champs et des tables de base de données. Votre structure de données peut varier considérablement selon les besoins de votre application.

Le premier exemple montre comment définir une entité d’utilisateur qui peut être associée à de nombreuses entités de connexion.

[!code-csharp[Main](mapping-users-to-connections/samples/sample7.cs)]

Puis, dans le concentrateur, vous pouvez suivre l’état de chaque connexion avec le code ci-dessous.

[!code-csharp[Main](mapping-users-to-connections/samples/sample8.cs)]

<a id="azure"></a>
### <a name="azure-table-storage"></a>Stockage de table Windows Azure

L’exemple de stockage de table Azure suivant est semblable à l’exemple de base de données. Il n’inclut pas toutes les informations que vous devrez commencer avec le Service de stockage de Table Azure. Pour plus d’informations, consultez [comment utiliser le stockage de Table à partir de .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).

L’exemple suivant montre une entité de table pour stocker les informations de connexion. Il partitionne les données par nom d’utilisateur et identifie chaque entité par l’id de connexion pour un utilisateur peut avoir plusieurs connexions à tout moment.

[!code-csharp[Main](mapping-users-to-connections/samples/sample9.cs)]

Dans le concentrateur, vous suivre l’état de chaque connexion d’utilisateur.

[!code-csharp[Main](mapping-users-to-connections/samples/sample10.cs)]
