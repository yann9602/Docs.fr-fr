---
uid: web-api/overview/testing-and-debugging/troubleshooting-http-405-errors-after-publishing-web-api-applications
title: "Résolution des problèmes de HTTP 405 erreurs après la publication Web API 2 Applications | Documents Microsoft"
author: rmcmurray
description: "Ce didacticiel explique comment résoudre les erreurs HTTP 405 après la publication d’une application API Web à un serveur web de production."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/01/2014
ms.topic: article
ms.assetid: 07ec7d37-023f-43ea-b471-60b08ce338f7
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/testing-and-debugging/troubleshooting-http-405-errors-after-publishing-web-api-applications
msc.type: authoredcontent
ms.openlocfilehash: 2455bbed891179466de8fb6ade3b0a8e66eadee6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="troubleshooting-http-405-errors-after-publishing-web-api-2-applications"></a>Résolution des problèmes de HTTP 405 erreurs après la publication Web API 2 Applications
====================
par [Robert McMurray](https://github.com/rmcmurray)

> Ce didacticiel explique comment résoudre les erreurs HTTP 405 après la publication d’une application API Web à un serveur web de production.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - [Internet Information Services (IIS)](https://www.iis.net/) (version 7 ou version ultérieure)
> - [Web API](../../index.md) (version 1 ou 2)


Les applications API Web utilisent plusieurs verbes HTTP courants : GET, POST, PUT, DELETE et parfois de correctif logiciel. Cela étant dit, les développeurs peuvent s’exécuter dans les situations où ces verbes sont implémentées par un autre module IIS sur leur serveur de production, ce qui conduit à une situation où le contrôleur d’API Web qui fonctionne correctement dans Visual Studio ou sur un serveur de développement retournera un HTTP 405 erreur lorsqu’elle est déployée sur un serveur de production. Heureusement, ce problème est facilement résolu, mais la résolution garantit une explication de la raison pour laquelle le problème se produit.

## <a name="what-causes-http-405-errors"></a>Les Causes HTTP 405 erreurs

La première étape pour découvrir comment les erreurs HTTP 405 d’incidents est de comprendre ce que signifie une erreur HTTP 405. Le principal qui régissent de document pour HTTP est [le document RFC 2616](http://www.ietf.org/rfc/rfc2616.txt), qui définit le code d’état HTTP 405 comme ***méthode non autorisée***et décrivent en détail ce code d’état comme une situation où &quot;la méthode spécifié dans la ligne de la requête n’est pas autorisée pour la ressource identifiée par l’URI de requête.&quot; En d’autres termes, le verbe HTTP n’est pas autorisé pour l’URL spécifique qu’un client HTTP a demandé.

En tant qu’une brève présentation, Voici plusieurs méthodes HTTP utilisées la plupart tel que défini dans le document RFC 2616, RFC 4918 et RFC 5789 :

| Méthode HTTP | Description |
| --- | --- |
| **TÉLÉCHARGER** | Cette méthode est utilisée pour récupérer des données à partir d’un URI et probablement la méthode HTTP plus utilisés. |
| **HEAD** | Cette méthode est très similaire à celles de la méthode GET, à ceci près qu’il ne récupère pas réellement les données à partir de l’URI de demande - il récupère simplement l’état HTTP. |
| **PUBLIER** | Cette méthode est généralement utilisée pour envoyer de nouvelles données à l’URI ; POST est souvent utilisée pour envoyer des données de formulaire. |
| **PUT** | Cette méthode est généralement utilisée pour les données brutes à l’URI ; PUT est souvent utilisée pour envoyer des données JSON ou XML pour les applications API Web. |
| **SUPPRIMER** | Cette méthode est utilisée pour supprimer les données à partir d’un URI. |
| **OPTIONS** | Cette méthode est généralement utilisée pour récupérer la liste des méthodes HTTP prises en charge pour un URI. |
| **DÉPLACEMENT DE LA COPIE** | Ces deux méthodes sont utilisées avec WebDAV et leur objectif est explicite. |
| **MKCOL** | Cette méthode est utilisée avec WebDAV et il est utilisé pour créer une collection (par exemple, un répertoire) à l’URI spécifié. |
| **PROPFIND PROPPATCH** | Ces deux méthodes sont utilisées avec WebDAV et ils sont utilisés pour interroger ou définir les propriétés d’un URI. |
| **DÉVERROUILLAGE DU VERROU** | Ces deux méthodes sont utilisées avec WebDAV et elles sont utilisées pour verrouiller/déverrouiller la ressource identifiée par l’URI de requête lors de la création. |
| **CORRECTIF LOGICIEL** | Cette méthode est utilisée pour modifier une ressource HTTP existante. |

Lorsqu’une de ces méthodes HTTP est configurée pour une utilisation sur le serveur, le serveur répond avec l’état HTTP et d’autres données qui convient à la demande. (Par exemple, une méthode GET peut recevoir un HTTP 200 ***OK*** réponse et une méthode PUT peuvent recevoir un HTTP 201 ***créé*** réponse.)

Si la méthode HTTP n’est pas configurée pour une utilisation sur le serveur, le serveur répond avec un 501 HTTP ***pas implémentée*** erreur.

Toutefois, lorsqu’une méthode HTTP est configurée pour être utilisé sur le serveur, mais elle a été désactivée pour un URI donné, le serveur répond avec un HTTP 405 ***méthode non autorisée*** erreur.

## <a name="example-http-405-error"></a>Exemple HTTP 405 erreur

Le suivant exemple demande et réponse HTTP illustrent une situation dans laquelle un client HTTP tente de placer la valeur à une application d’API Web sur un serveur web, le serveur retourne une erreur HTTP, ce qui indique que la méthode PUT pas autorisé :


Requête HTTP :


[!code-console[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample1.cmd)]


Réponse HTTP :


[!code-console[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample2.cmd)]


Dans cet exemple, le client HTTP envoyé une demande JSON valide à l’URL pour une application API Web sur un serveur web, mais le serveur a renvoyé un message d’erreur HTTP 405 lorsque ce qui indique que la méthode PUT n’était pas autorisée dans l’URL. En revanche, si l’URI de demande ne correspondait pas un itinéraire pour l’application de l’API Web, le serveur renvoie un HTTP 404 ***introuvable*** erreur.

## <a name="resolving-http-405-errors"></a>HTTP 405 résolution des erreurs

Il existe plusieurs raisons pour lesquelles un verbe HTTP spécifique ne pourra être, mais il existe un scénario principal qui est la cause de début de cette erreur dans IIS : plusieurs gestionnaires sont définis pour la même verbe/méthode, et un des gestionnaires bloque le gestionnaire attendu à partir de traitement de la demande. À titre d’explication, IIS traite les gestionnaires à partir de la première à la dernière basés sur les entrées de gestionnaire de commande dans les fichiers applicationHost.config et web.config, où la première combinaison de correspondance de chemin d’accès, verbe, ressources, etc., permettra de traiter la demande.

L’exemple suivant est un extrait d’un fichier applicationHost.config pour un serveur IIS qui a été retourner une erreur HTTP 405 lorsque vous utilisez la méthode PUT pour envoyer des données à une application de l’API Web. Dans cet extrait, plusieurs gestionnaires HTTP sont définies et chaque gestionnaire possède un ensemble différent de méthodes HTTP pour laquelle il est configuré - la dernière entrée dans la liste est le Gestionnaire de contenu statique, ce qui est le gestionnaire par défaut qui est utilisé une fois que les gestionnaires d’autres ont une chanc e pour examiner la demande :

[!code-xml[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample3.xml)]

Dans l’exemple ci-dessus, le Gestionnaire de WebDAV et le Gestionnaire d’URL sans Extension pour ASP.NET (ce qui est utilisé pour l’API Web) sont clairement définis pour des listes distinctes de méthodes HTTP. Notez que le Gestionnaire de la DLL ISAPI est configuré pour toutes les méthodes HTTP, même si cette configuration n’entraîne pas nécessairement une erreur. Toutefois, les paramètres de configuration tels que ce besoin pour prendre en compte lors de la résolution des erreurs HTTP 405.

Dans l’exemple ci-dessus, le Gestionnaire de la DLL ISAPI n’était pas le problème ; en fait, le problème n’a pas été défini dans le fichier applicationHost.config pour le serveur IIS, le problème a été provoqué par une entrée qui a été effectuée dans le fichier web.config lorsque l’application de l’API Web a été créée dans Visual Studio. L’extrait suivant à partir du fichier web.config de l’application affiche l’emplacement du problème :

[!code-xml[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample4.xml)]

Dans cet extrait, le Gestionnaire d’URL sans Extension pour ASP.NET est redéfini pour inclure des méthodes HTTP supplémentaires qui seront utilisées avec l’application de l’API Web. Toutefois, dans la mesure où un ensemble de méthodes HTTP similaire est défini pour le Gestionnaire de WebDAV, un conflit se produit. Dans ce cas, le Gestionnaire de WebDAV est défini et chargé par IIS, bien que WebDAV est désactivé pour le site Web qui inclut l’application de l’API Web. Pendant le traitement d’une requête HTTP PUT, IIS appelle le module de WebDAV, car il est défini pour le verbe PUT. Lorsque le module WebDAV est appelé, elle vérifie sa configuration et voit qu’elle est désactivée, elle retournera un HTTP 405 ***méthode non autorisée*** erreur pour toute requête qui ressemble à une demande de WebDAV. Pour résoudre ce problème, vous devez supprimer WebDAV à partir de la liste des modules HTTP pour le site Web dans lequel votre application API Web est définie. L’exemple suivant montre ce que que pourrait ressembler :

[!code-xml[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample5.xml)]

Ce scénario est souvent rencontré quand une application est publiée à partir d’un environnement de développement dans un environnement de production, cela se produit car la liste des gestionnaires/modules est différente entre vos environnements de développement et de production. Par exemple, si vous utilisez Visual Studio 2012 ou 2013 pour développer une application API Web, IIS Express 8 est le serveur web par défaut pour le test. Ce serveur web de développement est une version réduite de l’ensemble des fonctionnalités IIS fourni avec un produit serveur, et ce serveur web de développement contient peu de modifications qui ont été ajoutées pour les scénarios de développement. Par exemple, le module de WebDAV est souvent installé sur un serveur web de production qui exécute la version complète d’IIS, bien qu’il ne peut pas être en cours d’utilisation réelle. La version de développement d’IIS, (IIS Express), installe le module de WebDAV, mais les entrées pour le module WebDAV intentionnellement commentées, donc le module de WebDAV n’est jamais chargé sur IIS Express, sauf si vous modifiez votre configuration IIS Express paramètres permettant d’ajouter la fonctionnalité WebDAV pour votre installation IIS Express. Par conséquent, votre application web peut fonctionner correctement sur votre ordinateur de développement, mais vous pouvez rencontrer les erreurs HTTP 405 lorsque vous publiez votre application API Web à votre serveur web de production.

## <a name="summary"></a>Résumé

HTTP 405 sont provoquées lorsqu’une méthode HTTP n’est pas autorisée pour une URL demandée par un serveur web. Cette condition est souvent quand un gestionnaire particulier a été défini pour un verbe spécifique, et ce gestionnaire se substitue le gestionnaire que vous vous attendez à traiter la demande.

Si vous rencontrez une situation où vous recevez un message d’erreur HTTP 501, ce qui signifie que la fonctionnalité spécifique n’a pas été implémentée sur le serveur, cela signifie souvent qu’il n’existe aucun gestionnaire défini dans vos paramètres IIS qui correspond à la requête HTTP, qui indique probablement que quelque chose n’a pas été installée correctement sur votre système, ou quelque chose a modifié les paramètres IIS afin qu’il n’y aucun gestionnaire défini par cette méthode de prise en charge le protocole HTTP spécifique. Pour résoudre ce problème, vous devrez réinstaller une application qui tente d’utiliser une méthode HTTP pour laquelle il n’a aucun module correspondant ou les définitions de gestionnaire.
