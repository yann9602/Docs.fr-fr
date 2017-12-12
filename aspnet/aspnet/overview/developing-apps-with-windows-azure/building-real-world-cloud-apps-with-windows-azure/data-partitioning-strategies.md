---
uid: aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-partitioning-strategies
title: "Données (génération d’applications Cloud du monde réel avec Azure) de stratégies de partitionnement | Documents Microsoft"
author: MikeWasson
description: "Les applications du Cloud monde réel construction avec Azure livres est basée sur une présentation développée par Scott Guthrie. Il explique 13 des modèles et des meilleures pratiques qui peuvent il..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/12/2014
ms.topic: article
ms.assetid: 513837a7-cfea-4568-a4e9-1f5901245d24
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-partitioning-strategies
msc.type: authoredcontent
ms.openlocfilehash: 8eddb7af2d9032153b30ab54d5e882f0b46cd4ce
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="data-partitioning-strategies-building-real-world-cloud-apps-with-azure"></a>Données (génération d’applications Cloud du monde réel avec Azure) de stratégies de partitionnement
====================
par [Mike Wasson](https://github.com/MikeWasson), [Rick Anderson](https://github.com/Rick-Anderson), [Tom Dykstra](https://github.com/tdykstra)

[Téléchargement corriger projet](http://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4) ou [télécharger des livres](http://blogs.msdn.com/b/microsoft_press/archive/2014/07/23/free-ebook-building-cloud-apps-with-microsoft-azure.aspx)

> Le **construction réelle applications Cloud du monde avec Azure** livres est basé sur une présentation développée par Scott Guthrie. Il explique 13 modèles et pratiques qui peuvent vous aider à réussir le développement d’applications web pour le cloud. Pour plus d’informations sur la série, consultez [le premier chapitre](introduction.md).


Précédemment, nous avons vu comment il est facile de mettre à l’échelle de la couche web d’une application cloud, en ajoutant et supprimant des serveurs web. Mais si elles vous atteignez le même magasin de données, goulot d’étranglement de votre application déplace le serveur frontal pour le serveur principal, et la couche données est la plus difficile à l’échelle. Dans ce chapitre, nous examinons comment faire de votre couche données évolutive en partitionnant les données dans plusieurs bases de données relationnelles ou en combinant le stockage de base de données relationnelle avec d’autres options de stockage de données.

La configuration d’un schéma de partitionnement est meilleure terminé à l’avance pour la même raison mentionnée plus haut : il est très difficile à modifier votre stratégie de stockage de données une fois une application en production. Si vous pensez que dur au préalable sur les différentes approches, vous pouvez éviter d’avoir un « Twitter moment » lorsque votre application se bloque ou tombe en panne pendant un certain temps lorsque vous réorganisez les données et le code d’accès aux données de votre application.

## <a name="the-three-vs-of-data-storage"></a>Le trois Vs du stockage de données

Afin de déterminer si vous avez besoin d’une stratégie de partitionnement et il doit être, tenez compte des trois questions concernant vos données :

- Volume – la quantité de données allez-vous stocker en fin de compte ? Deux gigaoctets ? Quelques centaines de gigaoctets ? Téraoctets ? Pétaoctets ?
- Rapidité : quelle est la vitesse à laquelle vos données va croître ? Il est une application interne qui n’est pas générer une grande quantité de données ? Une application externe clients téléchargent les images et des vidéos dans ?
- Diverses : type de données va stocker ? Relationnel, images, les paires clé-valeur, les graphiques de réseaux sociaux ?

Si vous pensez que vous allez avoir un grand nombre de volume, de vitesse ou différents, vous devez soigneusement le type de schéma de partitionnement Active mieux votre application à l’échelle efficacement, car elle augmente, et pour vous assurer que vous n’exécutez dans les goulots d’étranglement.

Il existe trois approches de partitionnement :

- Le partitionnement vertical
- partitionnement horizontal
- Partitionnement hybride

## <a name="vertical-partitioning"></a>Le partitionnement vertical

Portioning verticale ressemble à diviser une table par colonnes : un jeu de colonnes est mis dans un magasin de données et un autre ensemble de colonnes sont placées dans un magasin de données différent.

Par exemple, supposons que mon application stocke des données sur les personnes, y compris les images :

![Table de données](data-partitioning-strategies/_static/image1.png)

Lorsque vous représenter ces données sous forme de table et que vous examinez des variétés différentes de données, vous pouvez voir que les trois colonnes de gauche ont des données de chaîne qui peuvent être stockées efficacement à une base de données relationnelle, tandis que les deux colonnes à droite sont essentiellement des tableaux d’octets c lle à partir des fichiers d’image. Il est possible de données de fichier image de stockage dans une base de données relationnelle, et un grand nombre de personnes qui, car ils ne souhaitent pas enregistrer les données dans le système de fichiers. Ils ne disposent pas d’un système de fichiers capable de stocker des volumes de données requis, ou ils pouvez gérer une sauvegarde distincte et restaurer le système. Cette approche fonctionne bien pour les bases de données locales et de petites quantités de données dans les bases de données cloud. Dans l’environnement local, il peut être plus facile de simplement laisser l’administrateur de prendre en charge tous les éléments.

Dans une base de données du cloud, le stockage est relativement coûteux, mais un grand nombre d’images peut rendre la taille de la base de données à croître au-delà des limites à laquelle elle peut fonctionner efficacement. Vous pouvez résoudre ces problèmes en partitionnant les données verticalement, ce qui signifie que vous choisissez le magasin de données le plus approprié pour chaque colonne dans votre table de données. Ce qui peut le mieux pour cet exemple est de placer les données de chaîne dans une base de données relationnelle et les images dans le stockage d’objets Blob.

![Table de données partitionné verticalement](data-partitioning-strategies/_static/image2.png)

Le stockage des images dans le stockage d’objets Blob à la place d’une base de données est plus pratique dans le cloud que dans un environnement sur site, car vous n’avez pas à vous soucier de la configuration des serveurs de fichiers ou de la gestion de sauvegarde et restauration des données stockées en dehors de la base de données relationnelle : tous les qui est géré automatiquement pour vous par le service de stockage d’objets Blob.

C’est l’approche de partitionnement nous implémentée dans l’application de corriger et nous allons examiner le code pour que, dans le [chapitre de stockage d’objets Blob](unstructured-blob-storage.md). Sans ce schéma de partitionnement et en supposant une taille d’image moyenne de 3 mégaoctets, l’application corriger serait uniquement en mesure de stocker environ 40 000 tâches avant l’activation de la taille maximale de la base de données de 150 Go. Après avoir supprimé les images, la base de données peut stocker 10 fois plus de tâches ; Vous pouvez accéder beaucoup plus de temps avant d’avoir à se soucier de l’implémentation d’un schéma de partitionnement horizontal. Et que l’application met à l’échelle, vos dépenses plus lentement, car la plupart de vos besoins en stockage vont dans le stockage d’objets Blob très peu onéreux.

## <a name="horizontal-partitioning-sharding"></a>(Partitionnement) de partitionnement horizontal

Portioning horizontale ressemble à diviser une table par les lignes : un ensemble de lignes sont placées dans un magasin de données, et un autre ensemble de lignes sont placées dans un magasin de données différent.

Étant donné le même jeu de données, une autre option serait pour stocker les différentes plages de noms de clients dans différentes bases de données.

![Table de données partitionnée horizontalement](data-partitioning-strategies/_static/image3.png)

Vous souhaitez être très prudents à votre schéma de partitionnement pour vous assurer que les données sont réparties uniformément afin d’éviter les zones réactives. Cet exemple simple à l’aide de la première lettre du nom ne respecte pas cette exigence, car un grand nombre de personnes ont des noms de famille qui commencent par certaines lettres courantes. Vous atteint les limites de taille de table plus tôt que prévu, car certaines bases de données obtiendriez très volumineux tandis que la plupart resterait petite.

Un inconvénient de partitionnement horizontal est qu’il peut être difficile à exécuter des requêtes sur l’ensemble des données. Dans cet exemple, une requête doit dessiner jusqu'à 26 bases de données pour obtenir toutes les données stockées par l’application.

## <a name="hybrid-partitioning"></a>Partitionnement hybride

Vous pouvez combiner le partitionnement vertical et horizontal. Par exemple, dans l’exemple de données, vous pourriez stocker les images dans le stockage d’objets Blob et partitionner horizontalement les données de chaîne.

![Hybride de table de données partitionnée](data-partitioning-strategies/_static/image4.png)

## <a name="partitioning-a-production-application"></a>Partitionnement d’une application de production

Point de vue conceptuel, il est facile de voir le fonctionne d’un schéma de partitionnement, mais n’importe quel schéma de partitionnement augmente la complexité du code et complique de nouveau que vous devez traiter. Si vous déplacez des images pour le stockage d’objets blob, que faire lorsque le service de stockage est hors service ? Comment gérer des objets blob sécurité ? Que se passe-t-il si le stockage de base de données et les objets blob sont désynchronisées ? Si vous êtes le partitionnement, comment allez-vous gérer les requêtes sur toutes les bases de données ?

Les complications sont faciles à gérer en tant que vous êtes sur leur planification avant de passer en production. De nombreuses personnes qui ne souhaitent qu’elles avaient plus tard. En moyenne notre équipe de l’équipe de consultants clients (CAT) Obtient indique une erreur grave des appels téléphoniques sur une fois par mois aux clients dont les applications sont tirant d’une manière très volumineuse, et ils n’avez pas cette planification. Et ils affirment que quelque chose comme : « aide ! Je tout placer dans un magasin de données unique, et dans les 45 jours je vais manquer d’espace sur ce dernier ! » Et si vous avez beaucoup de logique métier intégrée à la façon d’y accéder votre banque de données et que vous avez des clients qui utilisent votre application, il n’existe aucun moment opportun pour aller vers le bas pour un jour pendant la migration. Nous finir traverser efforts herculean pour aider à la partition de client de leurs données à la volée sans temps mort bas. Il est très attractive et très inquiétant, et pas quelque chose vous souhaitez participer si vous pouvez l’éviter ! Vous réfléchissez à ce sujet au préalable et les intégrer à votre application permettront à votre vie beaucoup plus facile si l’application devient plus tard.

## <a name="summary"></a>Résumé

Un schéma de partitionnement efficace peut activer votre application cloud à l’échelle en plusieurs pétaoctets de données dans le cloud sans les goulots d’étranglement. Et vous n’êtes pas obligé de payer au préalable pour machines massives ou infrastructure étendue comme vous le feriez si vous exécutiez l’application dans un centre de données local. Dans le cloud, vous pouvez vous pouvez ajouter de façon incrémentielle la capacité que vous en avez besoin, et que vous payez uniquement pour autant que vous utilisez lorsque vous l’utilisez.

Dans le [chapitre suivant](unstructured-blob-storage.md) nous verrons comment la corriger application implémente le partitionnement vertical en stockant les images dans le stockage d’objets Blob.

## <a name="resources"></a>Ressources

Pour plus d’informations sur les stratégies de partitionnement, consultez les ressources suivantes.

Documentation :

- [Meilleures pratiques pour la création de Services à grande échelle sur les Services Cloud Windows Azure](https://msdn.microsoft.com/en-us/library/windowsazure/jj717232.aspx). Livre blanc par Mark Simms et Michael Thomassy.
- [Microsoft Patterns and Practices - modèles de conception de cloud computing](https://msdn.microsoft.com/en-us/library/dn568099.aspx). Consultez les conseils de partitionnement des données, modèle de partitionnement.

Vidéos :

- [Prévention de défaillance : Création de Services de cloud computing évolutive et robuste](https://channel9.msdn.com/Series/FailSafe). Série en neuf parties par Ulrich Homann, Marc Mercuri et Mark Simms. Présente les principaux concepts et les principes d’architecture de manière très accessible et intéressante, avec récits tirés de l’expérience de Microsoft Customer Advisory Team (CAT) avec des clients réels. Consultez la discussion de partitionnement dans l’épisode de 7.
- [Construction Big : Leçons reçues à partir de clients Windows Azure - partie I](https://channel9.msdn.com/Events/Build/2012/3-029). Mark Simms traite des schémas de partitionnement, de stratégies de partitionnement, l’implémentation de partitionnement et les fédérations de base de données SQL, en commençant à 19:49. Similaire à la série de prévention de défaillance mais le passage en plus de détails sur les procédures.

Exemple de code :

- [Cloud Service Fundamentals dans Windows Azure](https://code.msdn.microsoft.com/Cloud-Service-Fundamentals-4ca72649). Exemple d’application qui inclut une base de données partitionnée. Pour obtenir une description du schéma de partitionnement implémentée, consultez [DAL – partitionnement de SGBDR](https://blogs.msdn.com/b/windowsazure/archive/2013/09/05/dal-sharding-of-rdbms.aspx) sur le blog de Windows Azure.

>[!div class="step-by-step"]
[Précédent](data-storage-options.md)
[Suivant](unstructured-blob-storage.md)
