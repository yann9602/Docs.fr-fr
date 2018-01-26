---
uid: aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/distributed-caching
title: "(Génération d’applications Cloud du monde réel avec Azure) de mise en cache distribuée | Documents Microsoft"
author: MikeWasson
description: "Les applications du Cloud monde réel construction avec Azure livres est basée sur une présentation développée par Scott Guthrie. Il explique 13 des modèles et des meilleures pratiques qui peuvent il..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/20/2015
ms.topic: article
ms.assetid: 406518e9-3817-49ce-8b90-e82bc461e2c0
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/distributed-caching
msc.type: authoredcontent
ms.openlocfilehash: 24ede9cb9289c84140f6e2573f9d526f19cac64b
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="distributed-caching-building-real-world-cloud-apps-with-azure"></a>Mise en cache (construction Cloud réelle les applications distribuées avec Azure)
====================
par [Mike Wasson](https://github.com/MikeWasson), [Rick Anderson](https://github.com/Rick-Anderson), [Tom Dykstra](https://github.com/tdykstra)

[Téléchargement corriger projet](http://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4) ou [télécharger des livres](http://blogs.msdn.com/b/microsoft_press/archive/2014/07/23/free-ebook-building-cloud-apps-with-microsoft-azure.aspx)

> Le **construction réelle applications Cloud du monde avec Azure** livres est basé sur une présentation développée par Scott Guthrie. Il explique 13 modèles et pratiques qui peuvent vous aider à réussir le développement d’applications web pour le cloud. Pour plus d’informations sur le livre électronique, consultez [le premier chapitre](introduction.md).


Le chapitre précédent examiné la gestion d’erreurs transitoires et mentionné à la mise en cache en tant qu’une stratégie de disjoncteur. Ce chapitre fournit plus d’informations sur la mise en cache, y compris quand l’utiliser, modèles courants pour l’utiliser et comment l’implémenter dans Azure.

## <a name="what-is-distributed-caching"></a>Ce qui est mise en cache distribué

Un cache fournit un débit élevé, l’accès à faible latence pour les données d’application fréquemment, en stockant les données en mémoire. Pour une application cloud le type plus utiles du cache est un cache distribué, ce qui signifie que les données ne sont pas stockées sur la mémoire du serveur de site web individuel, mais d’autres ressources de cloud, et les données mises en cache sont accessible à tous les serveurs d’une application web (ou autres cloud de machines virtuelles qu’ar e utilisé par l’application).

![Diagramme affichant plusieurs serveurs web accèdent aux serveurs de cache même](distributed-caching/_static/image1.png)

Lors de l’application met à l’échelle en ajoutant ou supprimant des serveurs, ou lorsque les serveurs sont remplacés en raison de mises à niveau ou des erreurs, les données mises en cache restent accessibles pour chaque serveur qui exécute l’application.

En évitant l’accès aux données de latence élevée d’un magasin de données permanentes, la mise en cache peut considérablement améliorer la réactivité des applications. Par exemple, la récupération des données à partir du cache est beaucoup plus rapide que récupérer à partir d’une base de données relationnelle.

Un autre avantage de la mise en cache est réduit le trafic vers le magasin de données permanentes, ce qui peut réduire les coûts en cas de sortie des données des frais pour le magasin de données persistantes.

## <a name="when-to-use-distributed-caching"></a>Quand utiliser la mise en cache distribuée

Mise en cache fonctionne mieux pour des charges de travail qui effectue une lecture plus que l’écriture de données, et lorsque le modèle de données prend en charge l’organisation de clé/valeur que vous utilisez pour stocker et récupérer des données dans le cache. Il est également plus utile lorsque des utilisateurs d’applications partagent un grand nombre de données communes ; par exemple, cache fournirait pas autant d’avantages si chaque utilisateur extrait généralement les données uniques à un utilisateur. Un exemple où la mise en cache peut être très utile est un catalogue de produits, car les données ne changent pas fréquemment, et tous les clients recherchent les mêmes données.

L’avantage de la mise en cache devient plus en plus mesurable plus une application met à l’échelle, comme les limites de débit et de la latence de la banque de données persistantes deviennent plus d’une limite sur les performances globales de l’application. Toutefois, vous pouvez implémenter la mise en cache pour des raisons autres que les performances ainsi. Pour les données qui ne doit pas nécessairement être parfaitement à jour lorsque affichée à l’utilisateur, accès au cache peuvent servir un disjoncteur pour le magasin de données persistantes ne répond pas ou indisponible.

## <a name="popular-cache-population-strategies"></a>Stratégies de remplissage du cache populaires

Pour pouvoir récupérer les données à partir du cache, vous devez stocker il tout d’abord. Il existe plusieurs stratégies pour obtenir des données dont vous avez besoin dans un cache :

- À la demande / Cache Aside

    L’application tente de récupérer des données à partir du cache, et lorsque le cache n’a pas les données (un « miss »), l’application stocke les données dans le cache afin qu’il sera disponible la prochaine fois. La prochaine fois que l’application tente d’obtenir les mêmes données, il recherche qu’elle recherche dans le cache (un « accès »). Pour empêcher l’extraction des données mises en cache qui ont été modifiées sur la base de données, vous invalidez le cache lorsque des modifications apportées au magasin de données.
- Push de données en arrière-plan

    Services d’arrière-plan transmettre des données dans le cache selon une planification régulière et l’application toujours extrait à partir du cache. Cette approche fonctionne très bien avec les sources de données de latence élevée qui ne nécessitent pas vous toujours retourne les données les plus récentes.
- Disjoncteur

    L’application normalement communique directement avec la banque de données persistantes, mais lorsque le magasin de données persistant a un problème de disponibilité, l’application récupère des données à partir du cache. Données ont été placées dans le cache à l’aide de la stratégie de push de données en arrière-plan ou cache côté. Il s’agit d’une défaillance de la gestion des stratégie plutôt qu’une stratégie d’amélioration des performances.

Afin de conserver les données dans le cache en cours, vous pouvez supprimer les entrées de cache associées lorsque votre application crée, les mises à jour ou supprime des données. S’il est très bien pour votre application obtenir des données sont légèrement obsolètes parfois, vous pouvez compter sur une heure d’expiration peut être configuré pour définir une limite sur l’ancienneté du cache de données.

Vous pouvez configurer l’expiration absolue (temps écoulé depuis que l’élément de cache a été créé) ou expiration (la quantité de temps depuis la dernière fois qu’un élément de cache a été accédé). Expiration absolue est utilisée lorsque vous êtes dépendant le mécanisme d’expiration du cache pour empêcher que les données ne devienne trop périmées. Dans l’application corriger, nous allons supprimer manuellement les éléments du cache périmées, et nous allons utiliser l’expiration décalée pour conserver les données les plus récentes dans le cache. Quelle que soit la stratégie d’expiration que vous choisissez, le cache automatiquement supprimez les anciens éléments (moins récemment utilisées ou LRU) lorsque mémoire limite le cache est atteinte.

## <a name="sample-cache-aside-code-for-fix-it-app"></a>Exemple de code de type cache-aside pour corriger application

Dans l’exemple de code suivant, nous vérifions le cache tout d’abord lors de la récupération d’une tâche corriger. Si la tâche se trouve dans le cache, nous renvoyons Si la valeur n’est pas trouvé, nous l’obtenir à partir de la base de données et les stocker dans le cache. Les modifications que vous effectueriez ajouter la mise en cache pour le `FindTaskByIdAsync` méthode sont mises en surbrillance.

[!code-csharp[Main](distributed-caching/samples/sample1.cs?highlight=5,9-11,13-15,19)]

Lorsque vous mettez à jour ou supprimez une tâche corriger, vous devez annuler la tâche (supprimer) la mise en cache. Dans le cas contraire, futur tente de lire que cette tâche doit continuer à obtenir les anciennes données à partir du cache.

[!code-csharp[Main](distributed-caching/samples/sample2.cs?highlight=7)]

Voici des exemples pour illustrer le code de mise en cache simple ; la mise en cache n’a pas été implémentée dans le projet téléchargeable de corriger.

## <a name="azure-caching-services"></a>Services de mise en cache Azure

Azure offre les services de mise en cache suivantes : [Cache Redis Azure](https://msdn.microsoft.com/library/dn690523.aspx) et [du Cache géré Azure](https://msdn.microsoft.com/library/dn386094.aspx). Cache Redis Azure est basé sur le courant [Cache Redis open source](http://redis.io/) et le premier choix pour la plupart des met en cache des scénarios.

<a id="sessionstate"></a>
## <a name="aspnet-session-state-using-a-cache-provider"></a>État de session ASP.NET à l’aide d’un fournisseur de cache

Comme indiqué dans le [chapitre de web développement meilleures pratiques](web-development-best-practices.md), une meilleure pratique consiste à éviter à l’aide de l’état de session. Si votre application requiert l’état de session, la meilleure pratique suivante est pour éviter le fournisseur de mémoire par défaut car qui ne permet pas de montée en charge (plusieurs instances du serveur web). Le fournisseur d’état de session ASP.NET SQL Server permet à un site qui s’exécute sur plusieurs serveurs web à utiliser l’état de session, mais elle implique un coût d’une latence élevée par rapport à un fournisseur en mémoire. La meilleure solution si vous devez utiliser l’état de session consiste à utiliser un fournisseur de cache, telles que la [fournisseur d’état de Session pour Azure Cache](https://msdn.microsoft.com/library/windowsazure/gg185668.aspx).

## <a name="summary"></a>Récapitulatif

Vous avez vu comment l’application Fix It peut implémenter la mise en cache afin d’améliorer l’évolutivité et des temps de réponse et de permettre à l’application de continuer à être réactif pour les opérations de lecture lors de la base de données n’est pas disponible. Dans le [chapitre suivant](queue-centric-work-pattern.md) nous allons montrer comment mieux améliorer l’évolutivité et de rendre l’application de continuer à être réactif pour les opérations d’écriture.

## <a name="resources"></a>Ressources

Pour plus d’informations sur la mise en cache, consultez les ressources suivantes.

Documentation

- [Cache Azure](https://msdn.microsoft.com/library/gg278356.aspx). Documentation MSDN officielle de mise en cache dans Azure.
- [Microsoft Patterns and Practices - Guide Azure](https://msdn.microsoft.com/library/dn568099.aspx). Consultez le Guide de mise en cache et le modèle de type Cache-Aside.
- [Prévention de défaillance : Aide sur les Architectures de Cloud résilientes](https://msdn.microsoft.com/library/windowsazure/jj853352.aspx). Livre blanc par Marc Mercuri, Ulrich Homann et Andrew Townhill. Consultez la section sur la mise en cache.
- [Meilleures pratiques pour la création de Services à grande échelle sur les Services Cloud Azure](https://msdn.microsoft.com/library/windowsazure/jj717232.aspx). W. Livre blanc par Mark Simms et Michael Thomassy. Consultez la section sur la mise en cache distribuée.
- [Mise en cache sur le chemin d’accès à l’extensibilité distribuée](https://msdn.microsoft.com/magazine/dd942840.aspx). Un article de MSDN Magazine (2009) plus anciens, mais une présentation clairement écrite à la mise en cache distribuée en général. passe en détail les sections de mise en cache des livres blancs de prévention de défaillance et les meilleures pratiques.

Vidéos

- [Prévention de défaillance : Création de Services de cloud computing évolutive et robuste](https://channel9.msdn.com/Series/FailSafe). Série en neuf parties par Ulrich Homann, Marc Mercuri et Mark Simms. Présente une vue de niveau 400 comment concevoir des applications cloud. Cette série se concentre sur la théorie et les raisons pour lesquelles ; Pour plus d’informations sur les procédures, consultez la série Big bâtiment par Mark Simms. Consultez la mise en cache épisode 3 en commençant à 1:24:14.
- [Construction Big : Leçons reçues des clients Azure - partie I](https://channel9.msdn.com/Events/Build/2012/3-029). Simon Davies traite le démarrage de la mise en cache distribuée à 46:00. Similaire à la série de prévention de défaillance mais le passage en plus de détails sur les procédures. La présentation a été attribuée le 31 octobre 2012, afin qu’il ne couvre pas le service de mise en cache des applications Web dans Azure App Service a été introduite dans 2013.

Exemple de code

- [Cloud Service Fundamentals dans Azure](https://code.msdn.microsoft.com/Cloud-Service-Fundamentals-4ca72649). Exemple d’application qui implémente la mise en cache distribuée. Consultez le blog d’accompagnement [Cloud Service Fundamentals : principes fondamentaux de la mise en cache](https://blogs.msdn.com/b/windowsazure/archive/2013/10/03/cloud-service-fundamentals-caching-basics.aspx).

>[!div class="step-by-step"]
[Précédent](transient-fault-handling.md)
[Suivant](queue-centric-work-pattern.md)
