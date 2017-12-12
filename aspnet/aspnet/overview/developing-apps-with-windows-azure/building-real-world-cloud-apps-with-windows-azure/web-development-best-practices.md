---
uid: aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices
title: "Meilleures pratiques de développement (génération d’applications Cloud du monde réel avec Azure) de Web | Documents Microsoft"
author: MikeWasson
description: "Les applications du Cloud monde réel construction avec Azure livres est basée sur une présentation développée par Scott Guthrie. Il explique 13 des modèles et des meilleures pratiques qui peuvent il..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/12/2014
ms.topic: article
ms.assetid: 52d6c941-2cd9-442f-9872-2c798d6d90cd
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices
msc.type: authoredcontent
ms.openlocfilehash: a40a3779ddc416e141dd27b665f43830a43590b1
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="web-development-best-practices-building-real-world-cloud-apps-with-azure"></a>Développement Web meilleures pratiques (génération d’applications Cloud du monde réel avec Azure)
====================
par [Mike Wasson](https://github.com/MikeWasson), [Rick Anderson](https://github.com/Rick-Anderson), [Tom Dykstra](https://github.com/tdykstra)

[Téléchargement corriger projet](http://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4) ou [télécharger des livres](http://blogs.msdn.com/b/microsoft_press/archive/2014/07/23/free-ebook-building-cloud-apps-with-microsoft-azure.aspx)

> Le **construction réelle applications Cloud du monde avec Azure** livres est basé sur une présentation développée par Scott Guthrie. Il explique 13 modèles et pratiques qui peuvent vous aider à réussir le développement d’applications web pour le cloud. Pour plus d’informations sur le livre électronique, consultez [le premier chapitre](introduction.md).


Les trois premiers modèles ont été sur la configuration d’un processus de développement agile ; les autres sont sur l’architecture et le code. Celle-ci est une collection des meilleures pratiques de développement web :

- [Serveurs web sans état](#stateless) derrière un équilibreur de charge intelligent.
- [Évitez d’état de session](#sessionstate) (ou si vous ne pouvez pas l’éviter, utilisez le cache distribué plutôt que dans une base de données).
- [Utiliser un CDN](#cdn) aux ressources de fichiers statiques de cache de périmètre (images, des scripts).
- [Prise en charge de .NET 4.5 async](#async) pour éviter les blocages des appels.

Ces pratiques sont valides pour tout développement web, pas seulement pour les applications cloud, mais ils sont particulièrement importantes pour les applications cloud. Ils fonctionnent ensemble pour vous aider à tirer le meilleur parti de l’échelle très flexible offertes par l’environnement du cloud. Si vous ne suivez pas ces pratiques, vous allez exécuter des limitations lorsque vous essayez de mettre à l’échelle votre application.

<a id="stateless"></a>
## <a name="stateless-web-tier-behind-a-smart-load-balancer"></a>Couche web sans état derrière un équilibreur de charge intelligent

*Couche web sans état* signifie que vous ne stockez toutes les données d’application dans le système de fichiers ou de mémoire de serveur web. En conservant votre couche web sans état permet de fournir une meilleure expérience client et de réaliser des économies :

- Si la couche web est sans état, et il se trouve derrière un équilibreur de charge, vous pouvez répondre rapidement aux modifications de trafic d’application en ajoutant ou en supprimant les serveurs dynamiquement. Dans l’environnement de cloud où vous ne payez que pour les ressources serveur pour tant que les utiliser, possibilité de répondre aux modifications de la demande peut se traduire économie énorme.
- Un niveau web sans état est une architecture plus simple à l’échelle de l’application. Qui permet en trop répondre aux besoins de mise à l’échelle plus rapidement et passent à moindre coût de développement et de test dans le processus.
- Les serveurs de cloud, comme les serveurs locaux, doivent être corrigés et redémarré occasionnellement ; et si la couche web est sans état, un réacheminement du trafic lorsqu’un serveur est temporairement indisponible ne provoque pas d’erreurs ou un comportement inattendu.

La plupart des applications réelles avez besoin de stocker l’état d’une session web ; le point principal ici est ne pas de les stocker sur le serveur web. Vous pouvez stocker l’état d’autres manières, telles que sur le client dans des cookies ou hors processus côté serveur dans l’état de session ASP.NET à l’aide d’un fournisseur de cache. Vous pouvez stocker les fichiers dans [stockage Blob Windows Azure](unstructured-blob-storage.md) au lieu du système de fichiers local.

Par exemple de la façon dont il est facile de faire évoluer une application dans les Sites Web Windows Azure, si votre niveau web est sans état, consultez le **échelle** onglet pour un Site de Web Windows Azure dans le portail de gestion :

![Onglet échelle](web-development-best-practices/_static/image1.png)

Si vous souhaitez ajouter des serveurs web, vous pouvez simplement faire glisser le curseur de nombre d’instance à droite. Affectez-lui la valeur 5, puis cliquez sur **enregistrer**, et en quelques secondes, vous avez 5 serveurs web dans Windows Azure, gestion du trafic de votre site web.

![Cinq instances](web-development-best-practices/_static/image2.png)

Vous pouvez aussi simplement définir le nombre d’instances à 3 ou à 1. Lorsque vous faites évoluer précédent, vous démarrez dépenses immédiatement, car Windows Azure est facturé à la minute, pas à l’heure.

Vous pouvez également indiquer Windows Azure pour augmenter ou diminuer le nombre de serveurs web basé sur l’utilisation du processeur automatiquement. Dans l’exemple suivant, lors de l’utilisation du processeur est inférieure à 60 %, diminue le nombre de serveurs web à un minimum de 2, et si l’utilisation du processeur dépasse 80 %, augmente le nombre de serveurs web avec un maximum de 4.

![L’échelle en fonction de l’utilisation du processeur](web-development-best-practices/_static/image3.png)

Ou bien, que se passe-t-il si vous savez que votre site seulement sera occupé pendant les heures de travail ? Vous pouvez indiquer à Windows Azure pour exécuter plusieurs serveurs pendant la journée et réduire à un seul serveur soirs, nuits et week-ends. La série de captures d’écran suivante montre comment configurer le site web pour exécuter un serveur en dehors des heures et 4 serveurs pendant les heures de travail à partir de 8 h 00 à 17 h 00.

![Mettre à l’échelle par planification](web-development-best-practices/_static/image4.png)

![Définir les heures de planification](web-development-best-practices/_static/image5.png)

![Planification de la journée](web-development-best-practices/_static/image6.png)

![Planification de weeknight](web-development-best-practices/_static/image7.png)

![Planification de week-end](web-development-best-practices/_static/image8.png)

Et bien sûr tout ceci peut être effectué dans les scripts, ainsi que dans le portail.

La capacité de votre application de la montée en puissance parallèle est presque illimitée dans Windows Azure, tant que vous évitez les obstacles à l’ajout ou la suppression d’ordinateurs virtuels du serveur, en conservant la couche web sans état dynamiquement.

<a id="sessionstate"></a>
## <a name="avoid-session-state"></a>Évitez d’état de session

Il est souvent pas pratique dans une application cloud réelle pour éviter de stocker une forme d’état de session utilisateur, mais certaines approches un impact sur les performances et évolutivité plus que d’autres. Si vous avez stocker l’état, la meilleure solution consiste à limiter la taille de la quantité d’état et les stocker dans les cookies. Si ce n’est pas possible, la meilleure solution suivante est d’utiliser l’état de session ASP.NET avec un fournisseur pour [cache distribué en mémoire](distributed-caching.md#sessionstate). La pire solution à partir d’un point de vue de l’évolutivité et de performances est d’utiliser une base de données de fournisseur d’état de session.

<a id="cdn"></a>
## <a name="use-a-cdn-to-cache-static-file-assets"></a>Utiliser un CDN pour mettre en cache des ressources de fichiers statiques

CDN est l’acronyme de réseau de distribution de contenu. Vous fournissez des ressources de fichiers statiques comme des images et des fichiers de script à un fournisseur de CDN, et le fournisseur met en cache ces fichiers dans des centres de données dans le monde entier afin que chaque fois que les utilisateurs accèdent à votre application, ils réponse relativement rapide et une faible latence pour la mise en cache ressources. Cela accélère le temps de chargement global du site et réduit la charge sur vos serveurs web. CDN est particulièrement importante si vous avez atteint une audience est répartie géographiquement.

Windows Azure a un CDN et vous pouvez utiliser les autres CDN dans une application qui s’exécute dans Windows Azure ou de n’importe quel environnement d’hébergement web.

<a id="async"></a>
## <a name="use-net-45s-async-support-to-avoid-blocking-calls"></a>Prise en charge de .NET 4.5 asynchrone permet d’éviter de bloquer les appels

.NET 4.5 amélioré le langage de programmation c# et VB pour le rendre plus faciles à gérer les tâches de façon asynchrone. L’avantage de la programmation asynchrone n’est pas seulement pour le traitement parallèle des situations telles que lorsque vous souhaitez lancer simultanément plusieurs appels au service web. Il permet également de votre serveur web effectuer plus efficacement et fiable dans des conditions de charge élevée. Un serveur web uniquement un nombre limité de threads disponibles, et dans des conditions de charge élevée lorsque tous les threads sont en cours d’utilisation, les demandes entrantes doivent attendre que les threads ne sont pas libérées. Si votre code d’application ne gère pas les tâches comme la base de données de requêtes et les appels de service web en mode asynchrone, de nombreux threads sont bloqués inutilement pendant que le serveur attend une réponse d’e/s. Cela limite la quantité de trafic, que le serveur peut gérer dans des conditions de charge élevée. Avec la programmation asynchrone, les threads en attente d’un service web ou d’une base de données pour retourner des données sont libérées jusqu'à nouvelles demandes jusqu'à ce que les données de la réception. Dans un serveur web occupé, des centaines, voire des milliers de demandes peuvent ensuite être traitées rapidement qui sinon d’attente des threads être libéré.

Comme vous l’avez vu précédemment, il est aussi facile de réduire le nombre de serveurs web gère votre site web tel qu’il doit augmenter. Par conséquent, si un serveur peut obtenir un débit supérieur, vous n’avez pas besoin que la plupart de ces derniers, vous pouvant réduire vos coûts, car vous avez besoin de moins de serveurs pour un volume de trafic donné que vous le feriez dans le cas contraire.

Prise en charge pour le modèle de programmation asynchrone .NET 4.5 est inclus dans ASP.NET 4.5 pour Web Forms et MVC, API Web ; dans Entity Framework 6 et dans le [API de Windows Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/07/12/introducing-storage-client-library-2-1-rc-for-net-and-windows-phone-8.aspx).

### <a name="async-support-in-aspnet-45"></a>Prise en charge asynchrone dans ASP.NET 4.5

ASP.NET 4.5, prise en charge la programmation asynchrone a été ajoutée, non seulement à la langue, mais également pour les infrastructures de MVC, Web Forms et les API Web. Par exemple, une méthode d’action de contrôleur ASP.NET MVC reçoit des données à partir d’une requête web et transmet les données à une vue qui crée ensuite le code HTML à envoyer au navigateur. Fréquence à laquelle la méthode d’action doit obtenir des données à partir d’un service web ou de la base de données pour l’afficher dans une page web ou pour enregistrer les données entrées dans une page web. Dans ces scénarios, il est facile d’apporter la méthode d’action asynchrone : au lieu de retourner un *ActionResult* de l’objet, vous retournez *tâche&lt;ActionResult&gt;*  et marquez la méthode avec la *async* (mot clé). À l’intérieur de la méthode, lorsqu’une ligne de code lance une opération qui implique des temps d’attente, vous marquez avec le mot clé await.

Voici une méthode d’action simple qui appelle une méthode de référentiel pour une requête de base de données :

[!code-csharp[Main](web-development-best-practices/samples/sample1.cs)]

Et Voici la méthode qui gère l’appel de la base de données de façon asynchrone :

[!code-csharp[Main](web-development-best-practices/samples/sample2.cs?highlight=1,4)]

En arrière-plan, le compilateur génère le code asynchrone. Lorsque l’application effectue l’appel à `FindTaskByIdAsync`, ASP.NET effectue le `FindTask` demande se déroule le thread de travail et la rend disponible pour traiter une autre demande. Lorsque le `FindTask` demande est effectuée, un thread est redémarré pour continuer le traitement du code qui vient après cet appel. Dans l’intervalle entre le moment où le `FindTask` demande est initiée et lorsque les données sont retournées, vous disposez d’un thread disponible pour effectuer des tâches utiles qui seraient être bloqués dans l’attente de la réponse.

Il existe une surcharge pour le code asynchrone, mais dans des conditions de faible charge, cette surcharge est négligeable lors de conditions de forte charge que vous êtes en mesure de traiter les demandes qui sinon seraient être mises en attente pour les threads disponibles.

Il a été possible d’effectuer ce type de programmation asynchrone depuis ASP.NET 1.1, mais il était difficile d’écrire, sujette aux erreurs et difficile à déboguer. Maintenant que nous avons simplifié le codage pour elle dans ASP.NET 4.5, il n’existe aucune raison de ne pas le faire plus.

### <a name="async-support-in-entity-framework-6"></a>Prise en charge asynchrone dans Entity Framework 6

Dans le cadre de la prise en charge asynchrone dans 4.5, nous avons expédié prise en charge asynchrone pour les appels de service web, les sockets et le système de fichiers d’e/s, mais le modèle le plus courant pour les applications web est atteint une base de données et les bibliothèques de données n’a pas été prennent en charge asynchrone. Entity Framework 6 ajoute à présent prise en charge asynchrone pour l’accès de la base de données.

Dans Entity Framework 6 toutes les méthodes qui provoquent une requête ou une commande à envoyer à la base de données ont des versions asynchrones. L’exemple suivant montre la version asynchrone de la *trouver* (méthode).

[!code-csharp[Main](web-development-best-practices/samples/sample3.cs?highlight=8)]

Et cette prise en charge asynchrone fonctionne pas seulement pour les insertions, les suppressions, les mises à jour et recherche simple, elle fonctionne également avec les requêtes LINQ :

[!code-csharp[Main](web-development-best-practices/samples/sample4.cs?highlight=7,10)]

Il existe une `Async` version de la `ToList` méthode car, dans ce code, qui est la méthode qui entraîne une requête à envoyer à la base de données. Le `Where` et `OrderByDescending` méthodes uniquement configurer la requête, alors que le `ToListAsync` méthode exécute la requête et stocke la réponse dans le `result` variable.

## <a name="summary"></a>Résumé

Vous pouvez implémenter les recommandations de développement web présentées ici dans n’importe quel site web de programmation framework et n’importe quel environnement de cloud, mais nous avons outils dans ASP.NET et Windows Azure permet de simplifier. Si vous suivez ces modèles, vous pouvez faire évoluer facilement votre couche web, et vous allez réduire vos dépenses, car chaque serveur sera en mesure de gérer davantage de trafic.

Le [chapitre suivant](single-sign-on.md) examine comment le cloud permet des scénarios d’authentification uniques.

## <a name="resources"></a>Ressources

Pour plus d’informations, consultez les ressources suivantes.

Serveurs web sans état :

- [Microsoft Patterns et pratiques - Guide de l’échelle automatique](https://msdn.microsoft.com/en-us/library/dn589774.aspx).
- [Affinité dans les Sites Web Windows Azure de l’Instance de désactivation ARR](https://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/). Billet de blog de Erez Benari, explique l’affinité de session dans les Sites Web Windows Azure.

CDN :

- [Prévention de défaillance : Création de Services de cloud computing évolutive et robuste](https://channel9.msdn.com/Series/FailSafe). Série de vidéos de neuf parties par Ulrich Homann, Marc Mercuri et Mark Simms. Consultez la discussion du CDN dans l’épisode 3 en commençant à 1:34:00.
- [Microsoft Patterns et pratiques statiques hébergement du contenu de modèle](https://msdn.microsoft.com/en-us/library/dn589776.aspx)
- [Révisions du CDN](http://www.cdnreviews.com/). Vue d’ensemble des nombreuses CDN.

Programmation asynchrone :

- [Utilisation de méthodes asynchrones dans ASP.NET MVC 4](../../../../mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4.md). Didacticiel de Rick Anderson.
- [Programmation asynchrone avec Async et Await (c# et Visual Basic)](https://msdn.microsoft.com/en-us/library/vstudio/hh191443.aspx). Livre blanc MSDN qui explique le raisonnement pour la programmation asynchrone, comment il fonctionne dans ASP.NET 4.5 et comment écrire du code pour l’implémenter.
- [Requête de Async Entity Framework et enregistrer](https://msdn.microsoft.com/en-us/data/jj819165)
- [Comment créer des Applications Web ASP.NET en utilisant Async](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/DEV-B337#fbid=tgkT4SR_DK7). Vidéo de présentation par Rowan Miller. Inclut une démonstration de graphique de la programmation asynchrone comment faciliter une augmentation considérable de débit du serveur web dans des conditions de charge élevée.
- [Prévention de défaillance : Création de Services de cloud computing évolutive et robuste](https://channel9.msdn.com/Series/FailSafe). Série de vidéos de neuf parties par Ulrich Homann, Marc Mercuri et Mark Simms. Pour des discussions sur l’impact de la programmation asynchrone sur l’évolutivité, consultez épisode 4 et épisode 8.
- [L’astuce de l’utilisation de méthodes asynchrones dans ASP.NET 4.5 plus un piège n important](http://www.hanselman.com/blog/TheMagicOfUsingAsynchronousMethodsInASPNET45PlusAnImportantGotcha.aspx). Billet de blog par Scott Hanselman, principalement sur l’utilisation d’async dans les applications Web Forms ASP.NET.

Pour les meilleures pratiques de développement de serveur web supplémentaire, consultez les ressources suivantes :

- [Le correctif il exemple d’Application - meilleures pratiques](the-fix-it-sample-application.md#bestpractices). L’annexe de ce livre électronique répertorie des meilleures pratiques qui ont été implémentées dans l’application corriger.
- [Liste de vérification de développeur Web](http://webdevchecklist.com/asp.net)

>[!div class="step-by-step"]
[Précédent](continuous-integration-and-continuous-delivery.md)
[Suivant](single-sign-on.md)
