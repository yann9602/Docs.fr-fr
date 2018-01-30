---
uid: web-forms/overview/older-versions-getting-started/deploying-web-site-projects/asp-net-hosting-options-vb
title: "ASP.NET (VB) d’Options d’hébergement | Documents Microsoft"
author: rick-anderson
description: "Les applications web ASP.NET sont généralement conçues, créé, testé dans un environnement de développement local et doivent être déployés vers un o d’environnement de production..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/01/2009
ms.topic: article
ms.assetid: 492f5ae2-bad7-4107-89a9-f04a9525dee7
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deploying-web-site-projects/asp-net-hosting-options-vb
msc.type: authoredcontent
ms.openlocfilehash: 54bac82a96a35d871d764849856c8e31f6570666
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
<a name="aspnet-hosting-options-vb"></a>Options d’hébergement ASP.NET (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le PDF](http://download.microsoft.com/download/E/8/9/E8920AE6-D441-41A7-8A77-9EF8FF970D8B/aspnet_tutorial01_Basics_vb.pdf)

> Les applications web ASP.NET sont généralement conçues, créées et testées dans un environnement de développement local et un doivent être déployées dans un environnement de production une fois qu’il est prêt pour la mise en production. Ce didacticiel fournit une vue d’ensemble du processus de déploiement et sert d’introduction pour cette série de didacticiels.


## <a name="introduction"></a>Introduction

Les applications Web sont généralement conçues, créées et testées dans un environnement de développement qui est accessible uniquement par les développeurs travaillant sur le site. Une fois que l’application est prête à être libéré, il est déplacé vers un environnement de production où le site est accessible par toute personne sur Internet. Ce processus de déploiement présente plusieurs difficultés :

- Un environnement de production doit exister et être correctement installé avant de pouvoir déployer une application ASP.NET ; en outre, l’environnement de production doit rester à jour avec les derniers correctifs de sécurité.
- L’ensemble correct de fichiers de balisage, les fichiers de code et les fichiers de prise en charge doit être copié à partir de l’environnement de développement à l’environnement de production. Pour les applications pilotées par les données, vous devrez peut-être copier le schéma de base de données et/ou des données, également.
- Il peut exister des différences de configuration entre les deux environnements. Du serveur de messagerie ou de chaîne de connexion de base de données utilisé dans l’environnement de développement sera probablement différent de celle de l’environnement de production. De plus, le comportement de l’application peut dépendre de l’environnement. Par exemple, lorsqu’une erreur se produit dans le développement des détails de l’erreur peuvent être affichés sur écran, mais lorsqu’une erreur se produit en production, une page d’erreur convivial doit être affichée à la place, et les détails d’erreur envoyé par courrier électronique pour les développeurs.

Pour prévenir le premier test - configuration et maintenance d’un environnement de production - nombreux particuliers et entreprises sous-traiter leurs environnements de production pour *fournisseurs d’hébergements web*. Un fournisseur d’hébergement web est une société qui gère l’environnement de production de votre part. Il existe des innombrables web fournisseurs d’hébergement, chacune avec différents prix et les niveaux de service ; consultez la section « Recherche d’un fournisseur d’hébergement Web » pour obtenir des conseils sur la localisation d’un fournisseur de services de ce type.

Il s’agit de la première d’une série de didacticiels qui examinent les étapes de déploiement d’une application de web ASP.NET dans un environnement de production géré par un fournisseur d’hébergement web. Au cours de ces didacticiels, nous allons examiner :

- Les fichiers devant être déployés sur le fournisseur de serveur web.
- Outils pour rationaliser le processus de déploiement.
- Comment déployer une base de données.
- Conseils pour le déploiement d’une base de données qui utilise [le fournisseur d’appartenances SQL et les rôles](../../older-versions-security/membership/creating-the-membership-schema-in-sql-server-cs.md), ainsi que les manières afin de reproduire l’outil Administration de site Web dans un environnement de production.
- Stratégies de mise à jour sans heurts de la base de données de production avec les modifications apportées au cours du développement.
- Techniques de consignation des erreurs qui se produisent sur la production et d’avertir les développeurs lorsqu’une erreur se produit.

Ces didacticiels sont destinés à être concis et pour fournir des instructions étape par étape avec beaucoup de captures d’écran pour vous guider dans le processus visuellement. Ce didacticiel première fournit une vue d’ensemble du processus de déploiement ASP.NET et des conseils sur la recherche d’un fournisseur d’hébergement web. C’est parti !

## <a name="an-overview-of-the-aspnet-deployment-process"></a>Une vue d’ensemble du processus de déploiement ASP.NET

En bref, le déploiement d’une application ASP.NET implique les trois étapes suivantes :

1. Configurer l’application web, le serveur web et la base de données dans l’environnement de production.
2. Synchroniser les pages ASP.NET, les fichiers de code, les assemblys dans le `Bin` dossier et les fichiers de prise en charge de HTML associés tels que les fichiers CSS et JavaScript.
3. Synchroniser le schéma de base de données et/ou des données.

Les informations de configuration pour une application web se trouve généralement dans le `Web.config` de fichiers et inclut des chaînes de connexion de base de données, les critères de la gestion des erreurs, URL de réécriture des règles et les informations de serveur de messagerie. Souvent, ces informations sont différentes pour une application en cours de développement par rapport à la même application en production. Par exemple, lorsque vous développez une application, il est préférable d’utiliser une base de données de développement afin que vous ne testez pas par rapport à la base de données de production. Par conséquent, les chaînes de connexion de base de données diffèrent généralement entre les applications de développement et de production. En raison de ces différences, phase de déploiement consiste à apporter des modifications aux informations de configuration de l’application web.

En plus des modifications de configuration d’application web, étape 1 peut également impliquer le configuration pour le serveur web et la base de données. Par exemple, si une page ASP.NET crée ou supprime des fichiers à partir d’un répertoire sur le serveur web du serveur web doit être configuré pour autoriser ces modifications du système de fichiers. De même, il peut exister des paramètres d’autorisation ou d’authentification qui doivent être apportées à la base de données.


Étape 2 implique la synchronisation de l’ensemble des pages ASP.NET essentiels et des fichiers de prise en charge entre les environnements de développement et de production. Le jeu d’ASP. Fichiers NET qui doivent être synchronisées entre les deux environnements varie selon le type de projet que vous avez créé dans Visual Studio et est la discussion dans le didacticiel suivant,  *[déterminer quels fichiers doivent être déployés](determining-what-files-need-to-be-deployed-vb.md)*. Les didacticiels troisième et quatrième -  *[déploiement de votre Site à l’aide de FTP](deploying-your-site-using-an-ftp-client-vb.md)*et  *[déploiement de votre Site à l’aide de Visual Studio](deploying-your-site-using-visual-studio-vb.md)*  -examiner différents outils et techniques pour la synchronisation de ces fichiers.

Lors de la création d’applications pilotées par les données sont généralement deux bases de données utilisés : une pour le développement et l’autre sur la production. Pendant le développement, le schéma de la base de données développement peut-être être modifié pour inclure les nouvelles tables, colonnes, procédures stockées et déclencheurs ou peut être modifié pour supprimer ou renommer des objets de base de données existante. Entre l’heure à laquelle ces modifications sont apportées et l’heure de que l’application est déployée en production, les bases de données de développement et de production ne sont pas synchronisées. Ce comportement asynchrone doit être résolu pendant le processus de déploiement. Didacticiels futures nous étudierons ces défis.

## <a name="finding-a-web-host-provider"></a>Recherche d’un fournisseur d’hébergement Web

Applications ASP.NET peuvent être déployées vers un serveur web qui contient le .NET Framework et Internet Information Services (IIS). Vous susceptible d’héberger un site à partir de votre ordinateur personnel, si que vous aviez une connexion haut débit à Internet et le savoir comment configurer votre routeur pour autoriser les demandes web entrantes. Vous pourriez également héberger un site à partir d’un ordinateur dans un intranet, comme de nombreuses sociétés. Toutefois, l’objectif de ces didacticiels, héberge votre site Web avec un fournisseur d’hébergement web.

> [!NOTE]
> [IIS](https://www.iis.net/) est le serveur web de niveau professionnel de Microsoft. Il est fourni avec les éditions non-accueil de Windows, tels que Windows Server 2008 et certaines éditions de Windows Vista. Vous n’avez pas besoin d’installer IIS pour prendre en charge les applications ASP.NET dans un environnement de développement, comme Visual Studio inclut le serveur Web de développement ASP.NET. Toutefois, le serveur Web de développement ASP.NET accepte uniquement les connexions locales et par conséquent ne peut pas être utilisé dans un environnement de production.


Avant de déployer votre site à un fournisseur d’hébergement web, vous devez décider quelle société commerciale. Il existe des innombrables web hébergeant des entreprises sur le marché ; une recherche de « société d’hébergement par le web » retourne des résultats de plus de cinq millions. Comment trouver celui qui vous convient ? Votre moteur de recherche est un bon point de départ, comme le sont comme des sites Web [TopHosts](http://www.tophosts.com/) et [HostCritique](http://www.hostcritique.net/), qui compare et contraste divers services d’hébergement. Je conseille également demander vos collègues et les collaborateurs de recommandations ; Vous pouvez également demander de recommandations relatives à la [qui héberge un Forum ouvert](https://forums.asp.net/158.aspx) ici à la [Forums ASP.NET](https://forums.asp.net/).

En général, sociétés d’hébergement Web offrent des plans d’hébergements partagés et dédiée de plans d’hébergement. Avec partagé qui héberge un hôte de serveur web unique des dizaines si ce n’est pas des centaines de différents sites Web. Avec hébergement dédié vous bail d’un ordinateur à partir de l’entreprise qui sert votre site et votre site uniquement. Un plan d’hébergement partagé peut inclure la prise en charge pour les pages ASP.NET, la possibilité de travailler avec les bases de données Microsoft Access, 5 Go d’espace disque et de 100 Go de mensuel de trafic de la bande passante pour 9,95 $ par mois. Un autre plan d’hébergement partagé peut inclure la prise en charge pour les pages ASP.NET, l’accès au serveur de base de données Microsoft SQL Server 2008, 10 Go d’espace disque et 250 Go de mensuel de trafic de la bande passante pour 19,95 $ par mois. Dédié plans d’hébergement sont généralement beaucoup plus coûteuses, d’évaluation des coûts plusieurs centaines en dollars par mois, mais offre de meilleures performances et davantage de contrôle que partagé options d’hébergement. Le plan que vous choisissez dépend de votre budget, la quantité de trafic reçoit de votre site Web et les fonctionnalités que vous pensez que vous avez besoin.

Deux considérations importantes lors du choix d’un fournisseur d’hébergement web sont le service clientèle et qualité de service. Si vous avez une question ou un problème de configuration, combien de temps faut-il de soumettre votre problème au support technique de l’hôte web jusqu'à ce que vous obtenez une réponse ? La fiabilité sont des services de l’entreprise ? Ils ont souvent des pannes de base de données ? La fréquence à laquelle leur serveur de messagerie passe en mode hors connexion ? Vous pouvez toujours demander une entreprise pour fournir des détails sur les temps de fonctionnement et obtenir des informations sur leur stratégie de service client, mais il n’est un moyen plus sûr pour solliciter les commentaires de clients actuelles et anciennes, vous pouvez effectuer via les forums en ligne, les groupes de discussion et les serveurs de liste .

> [!NOTE]
> Certaines sociétés d’hébergement web concentrent leurs activités sur une pile de technologie particulière, comme .NET ou [feu](http://en.wikipedia.org/wiki/LAMP_stack) (**L** inux, **A** pache, **M** ySQL, et **P** HP), assurez-vous que la société que vous sélectionnez héberge des applications ASP.NET. Vérifiez également qu’ils prennent en charge la version de ASP.NET que vous utilisez pour générer votre application. Et si vous générez une application orientée données, assurez-vous que l’hôte web offre le même serveur de base de données et de la même version que vous utilisez.


## <a name="summary"></a>Récapitulatif

Les applications web ASP.NET sont généralement conçues, créées et testées dans un environnement de développement local. Une fois qu’une version est prête pour la mise en production, il est déplacé vers un environnement de production. Bien qu’il soit possible d’hôte ASP.NET des sites Web sur votre ordinateur personnel ou des serveurs au sein de votre société, de nombreuses entreprises et les personnes choisissent d’externaliser leur hébergement à un fournisseur d’hébergement web.

Cette série de didacticiels examine les étapes pour déployer une application ASP.NET pour un fournisseur d’hôte web, exploration des problèmes courants. Ce didacticiel proposé une vue d’ensemble du processus de déploiement ASP.NET et donnez des conseils pour la recherche d’un fournisseur d’hôte web approprié. Le didacticiel suivant ressemble à ce que les fichiers ASP.NET doivent être copiés dans l’environnement de production lors du déploiement de votre site Web.

Bonne programmation !

### <a name="special-thanks-to"></a>Remerciements particuliers à...

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Teresa Murphy. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4GuysFromRolla.com).

>[!div class="step-by-step"]
[Précédent](users-and-roles-on-the-production-website-cs.md)
[Suivant](determining-what-files-need-to-be-deployed-vb.md)
