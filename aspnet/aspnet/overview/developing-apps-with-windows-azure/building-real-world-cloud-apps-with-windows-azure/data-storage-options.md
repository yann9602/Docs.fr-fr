---
uid: aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options
title: "Options de stockage de données (génération d’applications Cloud du monde réel avec Azure) | Documents Microsoft"
author: MikeWasson
description: "Les applications du Cloud monde réel construction avec Azure livres est basée sur une présentation développée par Scott Guthrie. Il explique 13 des modèles et des meilleures pratiques qui peuvent il..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/12/2014
ms.topic: article
ms.assetid: e51fcecb-cb33-4f9e-8428-6d2b3d0fe1bf
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options
msc.type: authoredcontent
ms.openlocfilehash: 3eb070167c36db7d8fb2e05af89716ee386b8211
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="data-storage-options-building-real-world-cloud-apps-with-azure"></a>Options de stockage de données (génération d’applications Cloud du monde réel avec Azure)
====================
par [Mike Wasson](https://github.com/MikeWasson), [Rick Anderson](https://github.com/Rick-Anderson), [Tom Dykstra](https://github.com/tdykstra)

[Téléchargement corriger projet](http://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4) ou [télécharger des livres](http://blogs.msdn.com/b/microsoft_press/archive/2014/07/23/free-ebook-building-cloud-apps-with-microsoft-azure.aspx)

> Le **construction réelle applications Cloud du monde avec Azure** livres est basé sur une présentation développée par Scott Guthrie. Il explique 13 modèles et pratiques qui peuvent vous aider à réussir le développement d’applications web pour le cloud. Pour plus d’informations sur le livre électronique, consultez [le premier chapitre](introduction.md).


La plupart des personnes sont utilisées pour les bases de données relationnelles, et ils ont tendance à ignorer les autres options de stockage de données lorsqu’ils concevez une application cloud. Le résultat peut être des performances réduites, dépenses haute ou pire, car [NoSQL](http://en.wikipedia.org/wiki/NoSQL) bases de données (non relationnelles) peuvent gérer certaines tâches plus efficacement que les bases de données relationnelles. Lorsque les clients nous posent pour tenter de résoudre un problème de stockage de données critiques, il est souvent parce qu’ils ont une base de données relationnelle dans laquelle une des options NoSQL aurait fonctionné mieux. Dans ces cas, le client aurait été préférable si ils avaient implémenté la solution NoSQL avant de déployer l’application en production.

En revanche, il serait également une erreur de supposer qu’une base de données NoSQL permettre effectuer toutes les opérations bien ou suffisamment bien. Il n’existe aucun choix de gestion des données meilleures unique pour toutes les tâches de stockage de données ; solutions de gestion des données différentes sont optimisées pour différentes tâches. La plupart des applications de cloud du monde réel ont diverses exigences de stockage de données et sont souvent pris en charge meilleures par une combinaison de plusieurs solutions de stockage de données.

L’objectif de ce chapitre est pour vous donner une idée plus large des options de stockage de données disponibles pour une application cloud et des conseils de base sur la façon de choisir celles qui correspondent à votre scénario. Il est préférable de connaître les options disponibles pour vous et pensez aux forces et les faiblesses avant de développer une application. Modification des options de stockage de données dans une application de production peut être extrêmement difficile, tels que de devoir modifier un moteur jet alors que le plan est en vol.

## <a name="data-storage-options-on-azure"></a>Options de stockage de données sur Azure

Le cloud rend relativement facile d’utiliser diverses relationnelle et les magasins de données NoSQL. Voici quelques-unes des plateformes de stockage de données que vous pouvez utiliser dans Azure.

![](data-storage-options/_static/image1.png)

Le tableau présente les quatre types de bases de données NoSQL :

- [Bases de données de clé/valeur](https://msdn.microsoft.com/en-us/library/dn313285.aspx#sec7) stocker un objet sérialisé unique pour chaque valeur de clé. Elles sont parfaites pour le stockage des volumes importants de données pour lesquelles vous souhaitez obtenir un élément d’une valeur de clé donnée et que vous n’avez pas à la requête basée sur d’autres propriétés de l’élément.

    [Stockage d’objets Blob Azure](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/) est une base de données de clé/valeur qui fonctionne comme le stockage de fichiers dans le cloud, avec les valeurs de clés qui correspondent aux noms de fichiers et de dossiers. Vous récupérez un fichier par son dossier et le nom de fichier, et non par la recherche de valeurs dans le contenu du fichier.

    [Stockage de Table Azure](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-tables/) est également une base de données de clé/valeur. Chaque valeur est appelée un *entité* (semblable à une ligne identifiée par une clé de partition et la clé de ligne) et contient plusieurs *propriétés* (similaires à des colonnes, mais pas toutes les entités dans une table doivent partager le même colonnes). Interrogation de colonnes que celles de la clé n’est pas très efficace et doit être évitée. Par exemple, vous pouvez stocker des données de profil utilisateur, avec une partition, le stockage d’informations sur un utilisateur. Vous pouvez stocker des données telles que le nom d’utilisateur, un hachage de mot de passe, date de naissance et ainsi de suite, dans les propriétés d’une entité distinctes ou dans des entités distinctes dans la même partition. Mais vous ne souhaitez pas de requête pour tous les utilisateurs avec une plage de dates de naissance donnée, et vous ne peut pas exécuter une requête de jointure entre la table de votre profil et une autre table. Stockage de table est plus évolutif et moins coûteux qu’une base de données relationnelle, mais il ne permet pas les requêtes complexes ou des jointures.
- [Documentdatabases](https://msdn.microsoft.com/en-us/library/dn313285.aspx#sec8) clé/valeur de bases de données dans laquelle les valeurs sont *documents*. « Document » n’est pas utilisé dans le sens d’un document Word ou Excel, mais signifie une collection de champs nommés et des valeurs, qui peut être un document enfant. Par exemple, dans une table d’historique de commande un document de commande peut avoir numéro de commande, date de commande et les champs de client ; et le champ customer peut avoir des champs nom et adresse. La base de données encode les données de champ dans un format tel que XML, YAML, JSON ou BSON ; ou elle peut utiliser le texte brut. Une fonctionnalité qui définit les bases de données de document en dehors des bases de données de clé/valeur est la possibilité d’effectuer des requêtes sur des champs et définir des index secondaires pour rendre l’interrogation plus efficace. Cette possibilité rend une base de données de document plus adapté pour les applications qui doivent récupérer des données en fonction de critères plus complexes que la valeur de la clé de document. Par exemple, dans une base de données de document de l’historique des commandes, vous pouvez interroger sur les différents champs tels que l’ID de produit, ID de client, nom du client et ainsi de suite. [MongoDB](http://www.mongodb.org/) est une base de données de documents les plus courants.
- [Bases de données de colonne-famille](https://msdn.microsoft.com/en-us/library/dn313285.aspx#sec9) sont des magasins de données qui vous permettent de stockage de données de structure dans des collections de colonnes en relation appelées familles de colonne de clé/valeur. Par exemple, une base de données de recensement peut avoir un groupe de colonnes pour le nom d’une personne (prénom, deuxième prénom, dernier), un groupe pour l’adresse de la personne et un groupe pour les informations de profil de la personne (DOB, sexe, etc.). La base de données peut ensuite stocker chaque famille de colonne dans une partition distincte tout en conservant toutes les données pour une personne associée à la même clé. Vous pouvez ensuite lire toutes les informations de profil sans avoir à lire toutes les informations nom et l’adresse. [Cassandra](http://cassandra.apache.org/) est une base de données de colonne-famille populaires.
- [Bases de données du graphique](https://msdn.microsoft.com/en-us/library/dn313285.aspx#sec10) stocker des informations comme une collection d’objets et de relations. Une base de données de graphique vise à permettre à une application exécuter efficacement des requêtes qui parcourent le réseau d’objets et les relations entre eux. Par exemple, les objets peuvent être employés dans une base de données des ressources humaines, et vous pourriez pour faciliter requêtes telles que « recherchent tous les employés travaillant directement ou indirectement de Scott. » [Neo4j](http://www.neo4j.org/) est une base de données de graphique populaires.

Par rapport aux bases de données relationnelles, les options NoSQL offrent beaucoup plus l’évolutivité et la rentabilité de stockage et l’analyse des données non structurées. L’inconvénient est qu’ils ne fournissent pas les queryability riche et les fonctionnalités d’intégrité de données fiable de bases de données relationnelles. NoSQL fonctionne bien pour les données de journal IIS, ce qui implique un volume élevé sans avoir besoin pour les requêtes de jointure. NoSQL ne fonctionne pas bien pour les transactions, bancaires qui nécessite l’intégrité des données absolu et implique plusieurs relations à d’autres données relatives aux comptes.

Il existe également une catégorie plus récente de la plateforme de base de données appelée [NewSQL](http://en.wikipedia.org/wiki/NewSQL) qui combine l’évolutivité d’une base de données NoSQL avec le queryability et l’intégrité transactionnelle de la base de données relationnelle. Bases de données NewSQL sont conçus pour stockage distribué et le traitement des requêtes, ce qui est souvent difficile à implémenter dans les bases de données « OldSQL ». [NuoDB](http://www.nuodb.com/) est un exemple de base de données NewSQL qui peut être utilisé sur Azure.

<a id="hadoop"></a>
## <a name="hadoop-and-mapreduce"></a>Hadoop et MapReduce

Les grands volumes de données que vous pouvez stocker dans les bases de données NoSQL peuvent être difficiles à analyser efficacement en temps voulu. Faire que vous pouvez utiliser une infrastructure telle que [Hadoop](http://hadoop.apache.org/) qui implémente [MapReduce](http://en.wikipedia.org/wiki/MapReduce) fonctionnalité. Essentiellement, le processus de MapReduce un est le suivant :

- Limite la taille des données qui doivent être traitée en sélectionnant des données stocker uniquement les données que vous avez besoin en fait d’analyser. Par exemple, vous souhaitez connaître la composition de votre utilisateur de base par année de naissance, afin seulement des années de naissance de votre magasin de données de profil utilisateur.
- Diviser les données en parties et les envoyer à différents ordinateurs pour le traitement. Ordinateur A calcule le nombre de personnes avec des dates de 1950-1959, l’ordinateur B effectue 1960-1969, etc. Ce groupe d’ordinateurs est appelé un *cluster Hadoop*.
- Réunissez les résultats de chaque partie précédent après que le traitement sur les parties est effectué. Vous avez maintenant une liste relativement courte du nombre de personnes pour chaque année de naissance et la tâche de calcul de pourcentages dans cette liste globale est facile à gérer.

Sur Azure, [HDInsight](https://azure.microsoft.com/en-us/services/hdinsight/) vous permet de traiter, d’analyser et d’exploiter les nouvelles données à l’aide de la puissance de Hadoop. Par exemple, vous pouvez l’utiliser pour analyser les journaux du serveur web :

- Activer la journalisation du serveur web à votre compte de stockage. Cela configure Azure d’écrire des journaux pour le Service Blob pour chaque requête HTTP à votre application. Le Service Blob est essentiellement de stockage de fichiers de cloud, et il s’intègre parfaitement avec HDInsight. 

    ![Journaux pour le stockage d’objets Blob](data-storage-options/_static/image2.png)
- Comme l’application obtient le trafic, les journaux du serveur web IIS sont écrites dans stockage d’objets Blob. 

    ![Journaux du serveur Web](data-storage-options/_static/image3.png)
- Dans le portail, cliquez sur **nouveau** - **Data Services** - **HDInsight** - **création rapide**, et spécifiez un nom de cluster HDInsight, la taille de cluster (nombre de nœuds de données du cluster HDInsight) et un nom d’utilisateur et le mot de passe pour le cluster HDInsight. 

    ![HDInsight](data-storage-options/_static/image4.png)

Vous pouvez maintenant configurer les tâches MapReduce à analyser vos journaux et obtenir des réponses aux questions telles que :

- Les heures de la journée mon application obtient-il le trafic plus ou moins ?
- Quels sont les pays est mon trafic en provenance de ?
- Quel est le revenu moyen voisinage des zones de que mon trafic provient. (Il existe un jeu de données publique qui vous donne un revenu de voisinage par adresse IP, et qui peut correspondre à par rapport à l’adresse IP dans les journaux du serveur web.)
- Manière dont sont revenus de voisinage corrélées à des pages spécifiques ou des produits sur le site ?

Vous pouvez ensuite utiliser les réponses aux questions telles que celles-ci pour cibler des publicités en fonction de la probabilité qu’un client serait intéressé ou susceptibles d’acheter un produit particulier.

Comme expliqué dans la [automatiser tout chapitre](automate-everything.md), la plupart des fonctions que vous pouvez effectuer dans le portail peut être automatisée et qui inclut la configuration et l’exécution des travaux d’analyse HDInsight. Un script de HDInsight classique peut comporter les étapes suivantes :

- Configurer un cluster HDInsight et le lier à votre compte de stockage pour l’entrée de stockage d’objets Blob.
- Télécharger les fichiers exécutables à la tâche MapReduce (fichiers .jar ou .exe) sur le cluster HDInsight.
- Envoyer un MapReduce qui stocke les données de sortie pour le stockage d’objets Blob.
- Attendez que le travail s’exécute.
- Supprimer le cluster HDInsight.
- Accéder à la sortie à partir du stockage d’objets Blob.

En exécutant un script qui ne sont pas, vous réduisez la quantité de temps que le cluster HDInsight est configuré, ce qui réduit les coûts.

<a id="paasiaas"></a>
## <a name="platform-as-a-service-paas-versus-infrastructure-as-a-service-iaas"></a>Plateforme en tant que Service (PaaS) par rapport à l’Infrastructure en tant que Service (IaaS)

Les options de stockage de données répertoriées précédemment incluent Platform-as-a-Service (PaaS) et les solutions d’Infrastructure-as-a-Service (IaaS). PaaS signifie que pour gérer l’infrastructure matérielle et logicielle et que vous utilisez simplement le service. Base de données SQL est une fonctionnalité PaaS Azure. Vous demander de bases de données, et en arrière-plan Azure définit et configure les ordinateurs virtuels et configure les bases de données. Vous n’avez pas accès direct aux machines virtuelles et que vous n’êtes pas obligé de les gérer. IaaS signifie que vous configurez, configurez et gérez des ordinateurs virtuels qui s’exécutent dans notre infrastructure de centre de données, et que vous placez tout ce que vous voulez sur les. Nous fournissons une galerie d’images de machine virtuelle préconfigurés pour les configurations courantes de la machine virtuelle. Par exemple, vous pouvez installer les images de machine virtuelle préconfigurés pour Windows Server 2008, Windows Server 2012, BizTalk Server, Oracle WebLogic Server, base de données Oracle, etc.

Solutions de données PaaS Azure offre sont les suivantes :

- Azure SQL Database (anciennement SQL Azure). Un cloud base de données relationnelle basée sur SQL Server.
- Stockage de Table Azure. Clé/valeur de la base de données NoSQL.
- Stockage d’objets Blob Azure. Stockage de fichiers dans le cloud.

Pour IaaS, vous pouvez exécuter tout ce que vous pouvez charger sur une machine virtuelle, par exemple :

- Bases de données relationnelles telles que SQL Server, Oracle, MySQL, SQL Compact, SQLite ou Postgres.
- Stocke les données de la clé/valeur comme memcache, Redis, Cassandra et Riak.
- Stocke les données de la colonne tel que HBase.
- Bases de données de document comme MongoDB RavenDB et CouchDB.
- Bases de données de graphique comme Neo4j.

![Options de stockage de données sur Azure](data-storage-options/_static/image5.png)

L’option IaaS vous propose les options de stockage de données quasiment illimitées, et bon nombre d'entre elles sont particulièrement faciles à utiliser, car vous pouvez créer des machines virtuelles à l’aide des images préconfigurées. Par exemple, dans le portail de gestion atteindre **virtuels**, cliquez sur le **Images** onglet, puis cliquez sur **Parcourir de dépôt de machines virtuelles**.

![Parcourir le dépôt de machines virtuelles](data-storage-options/_static/image6.png)

Vous voyez alors une liste de [des centaines d’images de machine virtuelle préconfigurées](http://www.hanselman.com/blog/Over400VirtualMachineImagesOfOpenSourceSoftwareStacksInTheVMDepotAzureGallery.aspx), et vous pouvez créer une machine virtuelle à partir d’une image qui a un système de gestion de base de données préinstallé, tels que MongoDB, Neo4J, Redis, Cassandra ou CouchDB :

![MongoDB dans le dépôt de machines virtuelles](data-storage-options/_static/image7.png)

Azure crée des options de stockage de données IaaS aussi facile à utiliser que possible, mais les offres PaaS présentent de nombreux avantages qui les rendent plus économique et plus pratique dans de nombreux scénarios :

- Vous n’êtes pas obligé de créer des ordinateurs virtuels, vous utilisez simplement le portail ou un script pour configurer un magasin de données. Si vous souhaitez une banque de données de 200 téraoctets, vous pouvez cliquer sur un bouton ou exécuter une commande et en secondes, il est prêt à utiliser.
- Vous n’êtes pas obligé de gérer ou de corriger les machines virtuelles utilisées par le service. Microsoft fait pour vous automatiquement.-vous n’avez pas à vous soucier de la configuration de l’infrastructure pour la disponibilité de mise à l’échelle ou trop élevée ; Microsoft gère tout cela pour vous.
- Vous n’êtes pas obligé d’acheter des licences ; droits de licence sont inclus dans les frais de service.
- Vous payez uniquement ce que vous utilisez.

Options de stockage de données de PaaS dans Azure incluent des offres par des fournisseurs tiers. Par exemple, vous pouvez choisir le [module complémentaire MongoLab](https://azure.microsoft.com/en-us/documentation/articles/store-mongolab-web-sites-dotnet-store-data-mongodb/) à partir du magasin Azure pour configurer une base de données MongoDB en tant que service.

## <a name="choosing-a-data-storage-option"></a>En choisissant une option de stockage de données

Sans une approche consiste à droite pour tous les scénarios. Si tout le monde indique que cette technologie est la réponse, la première chose à poser est « Quelle est la question ? », car différentes solutions sont optimisées pour différents éléments. Il existe des avantages définitifs pour le modèle relationnel ; C’est pourquoi il est présent pour la condition. Mais il existe également des côtés bas-SQL qui peuvent être traités par une solution NoSQL.

Fréquence à laquelle nous voient recommandé d’utiliser est une approche de composition, où vous utilisez SQL et NoSQL dans une solution unique. Même lorsque entendu dire qu’ils vous adoption NoSQL, si vous explorez leur démarche vous souvent trouver qu’ils utilisent plusieurs infrastructures NoSQL différentes : ils utilisent [CouchDB](http://wiki.apache.org/couchdb/Introduction), et [Redis](http://redis.io/)et [ Riak](http://basho.com/riak/) pour différents éléments. Même Facebook, qui utilise de manière intensive NoSQL, utilise différentes infrastructures NoSQL pour les différentes parties du service. La possibilité de combiner des approches de stockage de données est une des choses bien sur le cloud, car il est facile d’utiliser plusieurs solutions de données et de les intégrer dans une même application.

Voici quelques questions à considérer lorsque vous choisissez une approche :

| Sémantique des données | -Quel est le stockage et les données accès aux données essentielles sémantique (vous stockez des données relationnelles ou non structurées) ? Données non structurées, telles que des fichiers multimédias adaptée dans le stockage blob ; une collection de données associées, telles que les produits, les stocks, fournisseurs, les commandes des clients, etc., adaptée à une base de données relationnelle. |
| --- | --- |
| Prise en charge de la requête | -Est-il facile d’interroger les données ? -Les types de questions pouvant être efficacement invité ? Magasins de données de clé/valeur sont très efficace pour l’obtention d’une seule ligne avec une valeur de clé mais pas pour les requêtes complexes. Pour un magasin de données de profil utilisateur où vous obtenez toujours les données pour un utilisateur particulier, un magasin de données de clé/valeur risque de fonctionner correctement. pour un catalogue de produits dans lequel vous souhaitez obtenir des regroupements différents selon les différents attributs de produit, une base de données relationnelle fonctionnent mieux. Bases de données NoSQL peuvent stocker des volumes importants de données efficacement, mais vous devez la structure de la base de données autour de la façon dont l’application interroge les données, et ainsi plus difficile à effectuer des requêtes ad hoc. Une base de données relationnelle, vous pouvez créer presque n’importe quel type de requête. |
| Projection fonctionnelle | -Peut questions, les agrégations, etc., être exécutée côté serveur ? Si vous sélectionnez le nombre d’exécutions (\*) à partir d’une table dans SQL, il sera très efficacement effectuent tout le travail sur le serveur et retourner le nombre que je recherche. Si je veux le même calcul à partir d’un magasin de données NoSQL qui ne prennent pas en charge d’agrégation, inefficace « unbounded de requête » il sera probablement de délai d’attente. Même si la requête aboutit, j’ai récupérer toutes les données à partir du serveur au client et de compter les lignes sur le client. -Les langues ou les types d’expressions peuvent être utilisés ? Une base de données relationnelle, je peux utiliser SQL. Certaines bases de données NoSQL comme le stockage de Table Azure, je vais utiliser [OData](http://www.odata.org/), et je peux le faire filtrer sur la clé primaire et obtenir des projections (Sélectionner un sous-ensemble des champs disponibles). |
| Facilité d’extensibilité | -La fréquence et quantité est nécessaire les données à l’échelle ? -La plateforme n’implémente en mode natif avec montée en charge ? -Est-il facile à Ajout/Suppression de capacité (débit et taille) ? Tables et des bases de données relationnelles ne sont pas partitionnées automatiquement pour les rendre évolutives, afin qu’ils soient difficiles à l’échelle au-delà de certaines limitations. Magasins de données NoSQL comme le stockage Azure Table de partition, par nature, tous les éléments et presque aucune limite n’est à l’ajout de partitions. Vous pouvez facilement adapter le stockage de Table téraoctets 200, mais la taille maximale de la base de données pour la base de données SQL Azure est de 500 gigaoctets. Vous pouvez faire évoluer des données relationnelles en partitionnant en plusieurs bases de données, mais la configuration d’une application pour prendre en charge ce modèle implique une grande quantité de travail de programmation. |
| L’instrumentation et la facilité de gestion | -La simplicité est la plateforme à instrumenter, surveiller et gérer ? Vous devez être informé sur l’intégrité et les performances de votre banque de données, donc vous devez connaître au préalable les métriques une plateforme gratuitement, et ce que vous devez développer vous-même. |
| Opérations | -La simplicité est la plateforme pour déployer et exécuter sur Azure ? PaaS ? IaaS ? Linux ? Stockage de table et de la base de données SQL sont faciles à configurer sur Azure. Plateformes qui ne sont pas intégrées solutions PaaS Azure nécessitent davantage d’efforts. |
| Prise en charge des API | -Est une API disponible qui le rend facile à utiliser avec la plateforme ? Pour le Service de Table Azure est un kit de développement avec une API .NET qui prend en charge le modèle de programmation asynchrone .NET 4.5. Si vous écrivez une application .NET, il serez beaucoup plus facile à écrire et tester le code pour le Service de Table Azure par rapport à une autre clé/valeur colonne magasin plateforme de données qui n’a aucune API ou un moins complète. |
| Cohérence transactionnelle des données et d’intégrité | -Est essentiel que la plateforme prend en charge des transactions afin de garantir la cohérence des données ? Pour assurer le suivi des e-mails en masse envoyées, les performances et le coût de stockage de données faible peuvent être plus importantes que la prise en charge automatique des transactions ou l’intégrité référentielle de la plateforme de données, qui effectue le Service de Table Azure un bon choix. Pour le suivi de compte bancaire équilibre ou fournisseur une plateforme de base de données relationnelle qui fournit des garanties transactionnelles fort serait un meilleur choix. |
| Continuité des activités | -Faciles sont sauvegarde, restauration et récupération d’urgence ? Tôt ou tard seront corruption des données de production et vous aurez besoin d’une fonction d’annulation. Bases de données relationnelles ont souvent des autres fonctionnalités de restauration précis, telles que la capacité à restaurer à un point dans le temps. Comprendre les fonctionnalités de restauration sont disponibles dans chaque plateforme que vous envisagez d’utiliser est un facteur important à prendre en compte. |
| Coût | -Si plusieurs plateformes peut prendre en charge votre charge de travail de données, les comparer le coût ? Par exemple, si vous utilisez ASP.NET Identity, vous pouvez stocker les données de profil utilisateur dans le Service de Table Azure ou base de données SQL Azure. Si vous n’avez pas besoin la riche interrogation des fonctionnalités de base de données SQL, vous pouvez choisir les Tables Azure en partie, car elle coûte beaucoup moins pour un volume de stockage donné. |

Ce que nous déconseillons généralement est connaître la réponse aux questions dans chacune de ces catégories avant de choisir vos solutions de stockage de données.

En outre, votre charge de travail peut avoir des exigences spécifiques prises en charge par certaines plateformes mieux que d’autres. Exemple :

- Exiger de votre application auditer des fonctions ?
- Quels sont vos besoins en données longévité--des fonctions d’archivage ou purge automatisée avez-vous besoin ?
- Avez-vous des besoins de sécurité spécialisées ? Par exemple, les données incluent des informations d’identification personnelle (informations d’identification personnelle), mais vous devez être en mesure de vous assurer que les informations d’identification personnelle sont exclue des résultats de la requête.
- Si vous avez des données qui ne peut pas être stockées dans le cloud pour des raisons réglementaires ou technologiques, vous devrez peut-être une plate-forme de stockage de données de cloud qui facilite l’intégration avec votre stockage local.

## <a name="demo--using-sql-database-in-azure"></a>Démonstration – à l’aide de la base de données SQL dans Azure

L’application Fix It utilise une base de données relationnelle pour stocker des tâches. Le script Windows PowerShell de création environnement indiqué dans le [automatiser tout chapitre](automate-everything.md) crée deux instances de base de données SQL. Vous pouvez les consulter dans le portail en cliquant sur le **bases de données SQL** onglet.

![Bases de données SQL dans le portail](data-storage-options/_static/image8.png)

Il est également facile de créer des bases de données à l’aide du portail.

Cliquez sur **nouveau : les Services de données** -- **base de données SQL** -- **création rapide**, entrez un nom de base de données, choisissez un serveur que vous disposez déjà dans votre compte ou créez-en un, puis cliquez sur **créer la base de données SQL**.

![Nouvelle base de données SQL](data-storage-options/_static/image9.png)

Attendez quelques secondes, et que vous avez une base de données dans Azure, vous pouvez utiliser.

![Base de données SQL créée](data-storage-options/_static/image10.png)

Pour Azure en quelques secondes qu’il vous faudra un jour ou une semaine ou plus pour accomplir dans l’environnement local. Et dans la mesure où vous pouvez créer tout aussi facilement bases de données automatiquement dans un script ou à l’aide d’une API de gestion, vous pouvez dynamiquement monter en charge en répartissant vos données sur plusieurs bases de données < o : p >, tant que votre application a été programmée pour qui. </o : p >

Il s’agit d’un exemple de notre modèle Platform-as-a-Service. Vous n’êtes pas obligé de gérer les serveurs, nous le faire. Vous n’avez pas à vous soucier des sauvegardes, nous le faire. Il s’exécute en haute disponibilité – les données dans la base de données sont automatiquement répliquées sur trois serveurs. Si un ordinateur meurt, nous avons un basculement automatique, et vous ne perdez aucune donnée. Le serveur est mis à jour régulièrement, à vous soucier que vous n’avez pas besoin.

Cliquez sur un bouton et que vous obtenez la chaîne de connexion exact, vous avez besoin, vous pouvez commencer immédiatement à l’aide de la nouvelle base de données.

![Chaînes de connexion](data-storage-options/_static/image11.png)

Le tableau de bord vous montre l’historique de connexion et la quantité de stockage utilisée.

![Tableau de bord de base de données SQL](data-storage-options/_static/image12.png)

Vous pouvez gérer les bases de données dans le portail ou à l’aide des outils SQL Server, vous êtes déjà familier, y compris SQL Server Management Studio (SSMS) et les outils de Visual Studio, l’Explorateur d’objets de SQL Server (SSOX) et l’Explorateur de serveurs.

![SSOX](data-storage-options/_static/image13.png)

Une autre chose intéressante est le modèle de tarification. Vous pouvez démarrer le développement avec une base de données de 20 Mo gratuite et une base de données de production commence à environ 5 $ par mois. Vous payez uniquement pour la quantité de données que vous stockez en réalité dans la base de données, pas la capacité maximale. Vous n’êtes pas obligé d’acheter une licence.

Base de données SQL est facile à l’échelle. Pour l’application corriger, nous créons dans notre script d’automation de la base de données est limitée à 1 Go. Si vous souhaitez mettre à l’échelle jusqu'à 150 Go, vous pouvez accéder au portail et modifier ce paramètre ou exécuter une commande de l’API REST et en secondes, vous avez une base de données de 150 Go que vous pouvez déployer des données.

![Les tailles et les éditions de base de données SQL](data-storage-options/_static/image14.png)

C’est la puissance du cloud pour mettre en place l’infrastructure rapidement et facilement et commencer à utiliser immédiatement.

L’application Fix It utilise deux bases de données SQL, une pour l’appartenance (authentification et autorisation) et l’autre pour les données, et il s’agit tout que vous avez à faire pour configurer et mettre à l’échelle. Vous savez comment configurer les bases de données via des scripts Windows PowerShell et vous avez également vu combien il est facile à effectuer dans le portail.

## <a name="entity-framework-versus-direct-database-access-using-adonet"></a>Entity Framework au lieu de l’accès direct de la base de données à l’aide d’ADO.NET

L’application Fix It accède à ces bases de données à l’aide d’Entity Framework, ORM (mappeur objet / relationnel) de recommandée par Microsoft pour les applications .NET. Un ORM est un excellent outil qui facilite la productivité des développeurs, mais la productivité se fait au détriment de dégradation des performances dans certains scénarios. Dans une application cloud réelle vous ne qui effectue un choix entre l’utilisation d’EF ou à l’aide d’ADO.NET directement, vous allez utiliser à la fois. La plupart du temps lorsque vous écrivez du code qui fonctionne avec la base de données, l’obtention de performances maximales n’est pas critique et vous pouvez tirer parti de la codification simplifiée et de test que vous obtenez avec Entity Framework. Dans les situations où la surcharge de EF provoquerait performances inacceptable, vous pouvez écrire et exécuter vos propres requêtes à l’aide d’ADO.NET, dans l’idéal, en appelant des procédures stockées.

La méthode que vous utilisez pour accéder à la base de données que vous souhaitez réduire autant que possible « verbosité ». En d’autres termes, si vous pouvez obtenir toutes les données que vous avez besoin dans une plus grande requête jeu de résultats plutôt que des dizaines ou des centaines de plus petites, qui est généralement préférable d’utiliser. Par exemple, si vous avez besoin pour les étudiants de liste et les cours dans que ceux-ci inscrits, il est généralement préférable d’obtenir toutes les données dans une jointure de requêtes au lieu d’obtenir des étudiants dans une requête et l’exécution des requêtes distinctes pour le cours de chaque étudiant.

## <a name="sql-databases-and-the-entity-framework-in-the-fix-it-app"></a>Bases de données SQL et Entity Framework dans l’application Fix It

Dans l’application de corriger la `FixItContext` (classe), qui dérive de l’Entity Framework `DbContext` classe, identifie la base de données et spécifie les tables dans la base de données. Le contexte spécifie un jeu d’entités (table) pour les tâches et le code passe au contexte du nom de chaîne de connexion. Ce nom fait référence à une chaîne de connexion qui est définie dans le fichier Web.config.

[!code-csharp[Main](data-storage-options/samples/sample1.cs?highlight=4,8)]

La chaîne de connexion dans le *Web.config* fichier est nommé appdb (ici pointant vers la base de données de développement local) :

[!code-xml[Main](data-storage-options/samples/sample2.xml?highlight=3)]

Entity Framework crée un *FixItTasks* table basée sur les propriétés incluses dans le `FixItTask` classe d’entité. Il s’agit d’une simple classe POCO (objet CLR simple), ce qui signifie qu’il ne hériter ou avoir de dépendances sur Entity Framework. Mais Entity Framework sait comment créer une table basée sur celui-ci et exécuter des opérations (création en lecture-mise à jour-suppression) CRUD avec lui.

[!code-csharp[Main](data-storage-options/samples/sample3.cs)]

![Table de FixItTasks](data-storage-options/_static/image15.png)

L’application corriger inclut une interface de référentiel qu’il utilise pour les opérations CRUD d’utilisation de la banque de données.

[!code-csharp[Main](data-storage-options/samples/sample4.cs)]

Notez que les méthodes de référentiel sont tous les async, pour effectuer tout accès aux données d’une manière totalement asynchrone.

L’implémentation de référentiel appelle les méthodes async Entity Framework de travailler avec les données, notamment LINQ interroge ainsi que pour insérer, mettre à jour et les opérations de suppression. Voici un exemple du code pour la recherche d’une tâche corriger.

[!code-csharp[Main](data-storage-options/samples/sample5.cs)]

Vous remarquerez qu’il existe également certaines minutage et code d’erreur journalisation ici, nous allons examiner que plus tard dans le [chapitre surveillance et télémétrie](monitoring-and-telemetry.md).

<a id="sqliaas"></a>
## <a name="choosing-sql-database-paas-versus-sql-server-in-a-vm-iaas-in-azure"></a>Choix de la base de données SQL (PaaS) et SQL Server dans une machine virtuelle (IaaS) dans Azure

Une chose intéressante sur SQL Server et la base de données SQL Azure est que le modèle de programmation de base pour les deux est identique. Vous pouvez utiliser la plupart des mêmes compétences dans les deux environnements. Vous pouvez même utiliser une base de données SQL Server en cours de développement et une instance de la base de données SQL dans le cloud, qui est l’application de corriger le paramétrage.

En guise d’alternative, vous pouvez exécuter le même serveur SQL dans le cloud que vous exécutez sur site en l’installant sur les machines virtuelles IaaS. Pour certaines applications héritées, le SQL Server en cours d’exécution dans une machine virtuelle peut être une meilleure solution. Comme une base de données SQL Server s’exécute sur une machine virtuelle dédiée, il a plus de ressources disponibles à cet effet qu’une base de données de base de données SQL qui s’exécute sur un serveur partagé. Cela signifie une base de données SQL Server peut être plus volumineux et encore effectuer correctement. En règle générale, petits la taille de la base de données et de la taille de la table, plus le cas d’usage fonctionne pour la base de données SQL (PaaS).

Voici quelques conseils sur le choix entre les deux modèles.

| Base de données SQL Azure (PaaS) | SQL Server dans une Machine virtuelle (IaaS) |
| --- | --- |
| **Les professionnels de le** -vous n’êtes pas obligé de créer ou gérer des machines virtuelles, mettre à jour ou correctifs du système d’exploitation ou de SQL ; Azure fait pour vous. -Haute disponibilité intégrée, avec un contrat SLA de niveau base de données. -Faible coût total de possession (TCO), car vous payez uniquement ce que vous utilisez (aucune licence n’est requise). -Approprié pour traiter un grand nombre de bases de données plus petits (&lt;= 500 Go). -Permet de créer dynamiquement des nouvelles bases de données pour activer la montéent en puissance parallèle. | ***Les professionnels de le*** - fonctionnalité compatibles avec local SQL Server. -Peut implémenter SQL Server [haute disponibilité via AlwaysOn](https://www.microsoft.com/en-us/sqlserver/solutions-technologies/mission-critical-operations/high-availability.aspx) 2 + sur des machines virtuelles, avec un contrat SLA de niveau de la machine virtuelle. -Vous avez un contrôle complet sur la gestion de SQL. -Peut réutiliser vous possédez déjà ou payez par heure pour l’une des licences SQL. -Approprié pour la gestion des moins mais plus volumineux (1 To +) bases de données. |
| **Cons** -certaines fonctionnalités des écarts par rapport à locale SQL Server (un manque de [intégration du CLR](https://technet.microsoft.com/en-us/library/ms131102.aspx), [TDE](https://technet.microsoft.com/en-us/library/bb934049.aspx), [prise en charge la compression](https://technet.microsoft.com/en-us/library/cc280449.aspx), [SQL Serveur Reporting Services](https://technet.microsoft.com/en-us/library/ms159106.aspx), etc.)-limite de taille de base de données de 500 Go. | ***Cons*** - mises à jour/correctifs (système d’exploitation et SQL) sont de votre responsabilité - création et gestion des bases de données sont de votre responsabilité - disque IOPS (opérations d’entrée/sortie par seconde), limité à 8000 (via des lecteurs de 16 données). |

Si vous souhaitez utiliser SQL Server dans une machine virtuelle, vous pouvez utiliser votre propre licence SQL Server, ou vous pouvez payer pour l’une à l’heure. Par exemple, dans le portail ou via l’API REST, vous pouvez créer une nouvelle machine virtuelle à l’aide d’une image de SQL Server.

![Créer la machine virtuelle avec SQL Server](data-storage-options/_static/image16.png)

![Liste des images de machine virtuelle SQL Server](data-storage-options/_static/image17.png)

Lorsque vous créez une machine virtuelle avec une image de SQL Server, nous avons dépensé le coût de licence de SQL Server à l’heure en fonction de l’utilisation de la machine virtuelle. Si vous avez un projet qui ne va s’exécutent pendant quelques mois, il est plus économique payer par heure. Si vous pensez que votre projet va dernière ans, il est plus économique d’acheter la licence de la façon que vous le faites habituellement.

## <a name="summary"></a>Résumé

Le cloud computing facilement combiner et approches de stockage de données de correspondance pour mieux répondre aux besoins de votre application. Si vous créez une nouvelle application, après mûre réflexion sur les questions répertoriées ici pour choisir des approches qui continuent de fonctionner correctement lorsque votre application se développe. Le [chapitre suivant](data-partitioning-strategies.md) explique certaines des stratégies de partitionnement que vous pouvez utiliser pour combiner plusieurs approches de stockage de données.

## <a name="resources"></a>Ressources

Pour plus d'informations, voir les ressources ci-dessous.

Choix d’une plate-forme de base de données :

- [Accès aux données pour les Solutions hautement évolutives : à l’aide de SQL, NoSQL et persistance Polyglot](http://aka.ms/dag-doc). Stocke les livres par Microsoft Patterns and Practices qui passe en détail dans les différents types de données disponibles pour les applications cloud.
- [Microsoft Patterns and Practices - Guide Azure](https://msdn.microsoft.com/en-us/library/ff898430.aspx). Consultez le manuel de la cohérence des données, réplication des données et des instructions de synchronisation, le modèle de Table d’Index, le modèle de vue matérialisée.
- [De BASE : Acid Alternative](http://queue.acm.org/detail.cfm?id=1394128). L’article sur les compromis entre la cohérence des données et l’évolutivité.
- [Bases de sept données dans sept semaines : un Guide sur les bases de données modernes et le déplacement de NoSQL](https://www.amazon.com/Seven-Databases-Weeks-Modern-Movement/dp/1934356921). Livre par Eric Redmond et Jim R. Wilson. Fortement recommandé de vous présenter à la plage de plateformes de stockage de données disponibles aujourd'hui.

Choix entre SQL Server et la base de données SQL :

- [Version d’évaluation Premium pour obtenir des conseils de base de données SQL](https://msdn.microsoft.com/en-us/library/windowsazure/dn369873.aspx). Introduction à la base de données SQL Premium et des conseils sur quand choisir sur les éditions de base de données Web et Business SQL.
- [Instructions et Limitations (base de données SQL Azure)](https://msdn.microsoft.com/en-us/library/windowsazure/ff394102.aspx). Page du portail qui établit un lien vers la documentation sur les limitations de la base de données SQL, y compris un se concentre sur les fonctionnalités de SQL Server de cette base de données SQL ne prend en charge.
- [SQL Server dans des Machines virtuelles](https://msdn.microsoft.com/en-us/library/windowsazure/jj823132.aspx). Page du portail qui établit un lien vers la documentation sur l’exécution de SQL Server dans Azure.
- [Scott Guthrie explique les bases de données SQL dans Azure](https://azure.microsoft.com/en-us/documentation/videos/sql-in-azure-scottgu/). 6 minutes vidéo de présentation à la base de données SQL par Scott Guthrie.
- [Modèles d’application et les stratégies de développement de SQL Server dans des Machines virtuelles](https://msdn.microsoft.com/en-us/library/windowsazure/dn574746.aspx).

À l’aide d’Entity Framework et la base de données SQL dans une application Web ASP.NET

- [Prise en main de 6 EF à l’aide de MVC 5](../../../../mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md). Neuf parties série de didacticiels qui vous guide à travers la génération d’une application MVC qui utilise EF et déploie la base de données Azure et base de données SQL.
- [Déploiement de Web ASP.NET à l’aide de Visual Studio](../../../../web-forms/overview/deployment/visual-studio-web-deployment/introduction.md). Partie de douze série de didacticiels qui passe en plus de détails sur la façon de déployer une base de données en utilisant EF Code First.
- [Déployer une application sécurisée ASP.NET MVC 5 avec base de données SQL, OAuth et l’appartenance à un Site Web Azure](https://azure.microsoft.com/en-us/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/). Didacticiel pas à pas qui vous guide dans la création d’une application web qui utilise l’authentification, stocke les tables de l’application dans la base de données d’appartenance, modifie le schéma de base de données et déploie l’application sur Azure.
- [ASP.NET Data Access Content Map](https://go.microsoft.com/fwlink/p/?LinkId=282414). Liens vers des ressources pour travailler avec EF et de la base de données SQL.

À l’aide de MongoDB sur Azure :

- [MongoLab - MongoDB sur Azure](http://msopentech.com/opentech-projects/mongolab-mongodb-on-windows-azure/). Page du portail pour plus d’informations sur l’exécution de MongoDB sur Azure.
- [Créer un site web Azure qui se connecte à MongoDB en cours d’exécution sur un ordinateur virtuel dans Azure](https://azure.microsoft.com/en-us/documentation/articles/web-sites-dotnet-store-data-mongodb-vm/). Didacticiel pas à pas qui montre comment utiliser une base de données MongoDB dans une application web ASP.NET.

HDInsight (Hadoop sur Azure) :

- [HDInsight](https://azure.microsoft.com/en-us/documentation/services/hdinsight/). Portail vers la documentation HDInsight sur la [Azure](https://azure.microsoft.com/) site Web.
- [HDInsight et Hadoop : Big Data dans Azure](https://msdn.microsoft.com/en-us/magazine/dn385705.aspx). Article du MSDN Magazine en Bruno Terkaly et Ricardo Villalobos, présentation Hadoop sur Azure.
- [Microsoft Patterns and Practices - Guide Azure](https://msdn.microsoft.com/en-us/library/dn568099.aspx). Voir MapReduce modèle.

>[!div class="step-by-step"]
[Précédent](single-sign-on.md)
[Suivant](data-partitioning-strategies.md)
