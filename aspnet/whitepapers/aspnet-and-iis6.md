---
uid: whitepapers/aspnet-and-iis6
title: "En cours d’exécution ASP.NET 1.1 avec IIS 6.0 | Documents Microsoft"
author: rick-anderson
description: "Tandis que Windows Server 2003 inclut IIS 6.0 et ASP.NET 1.1, ces composants sont désactivés par défaut. Ce livre blanc décrit comment activer IIS 6.0 un..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/10/2010
ms.topic: article
ms.assetid: 5a5537bf-2aaa-49e7-839f-9e6522b829d8
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /whitepapers/aspnet-and-iis6
msc.type: content
ms.openlocfilehash: 1fcac7b8bc295ccf4e36189295b6bc2e4d328623
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="running-aspnet-11-with-iis-60"></a>En cours d’exécution ASP.NET 1.1 avec IIS 6.0
====================
> Tandis que Windows Server 2003 inclut IIS 6.0 et ASP.NET 1.1, ces composants sont désactivés par défaut. Ce livre blanc explique comment activer IIS 6.0 et ASP.NET 1.1 et vous recommande de plusieurs paramètres de configuration pour obtenir des performances optimales à partir de IIS et ASP.NET.
> 
> S’applique à IIS 6.0 et ASP.NET 1.1.


ASP.NET 1.1 est fourni avec Windows Server 2003, qui inclut également la version la plus récente d’Internet Information Server (IIS) version 6.0. IIS 6.0 et ASP.NET 1.1 sont conçus pour s’intégrer sans problème et ASP.NET désormais par défaut est le nouveau modèle de processus de travail IIS 6.0.

## <a name="aspnet-11-is-not-installed-by-default"></a>ASP.NET 1.1 n’est pas installé par défaut

Contrairement aux versions précédentes des systèmes d’exploitation de serveur de Microsoft, Internet Information Server (IIS) n’est pas activée par défaut ; et n’est pas ASP.NET 1.1. Il existe deux options pour l’activation d’IIS :

### <a name="enabling-iis-option-1---configure-your-server-wizard"></a>L’activation de IIS, option #1 - Assistant Configurer votre serveur

Windows Server 2003 est fourni à un nouveau « Assistant Configurer votre serveur » pour vous aider à configurer correctement votre serveur dans le mode souhaité.

Pour démarrer l’Assistant - note, pour exécuter l’Assistant, vous devez être connecté en tant qu’administrateur - atteindre : Démarrer | Programmes | Outils d’administration et sélectionnez « Configurer votre serveur ».

Une fois la sélection effectuée, vous devez voir l’écran d’ouverture de le « Assistant Configuration de votre serveur » :

![](aspnet-and-iis6/_static/image1.jpg)

Cliquez sur ' suivant &gt;' :

![](aspnet-and-iis6/_static/image2.jpg)

Cliquez sur ' suivant &gt;'

![](aspnet-and-iis6/_static/image3.jpg)

Dans cet écran, vous devez sélectionner « serveur d’applications (IIS, ASP.NET) en tant que les options de configuration.

Cliquez sur ' suivant &gt;'.

![](aspnet-and-iis6/_static/image4.jpg)

Après avoir sélectionné cette option pour configurer le serveur comme serveur d’applications, cet écran s’affichera invitant les fonctionnalités supplémentaires doivent être installées. Aucune de ces options est activée par défaut. Pour activer ASP.NET automatiquement, vous devez sélectionner « Activer les ASP. NET ».

Cliquez sur ' suivant &gt;'.

![](aspnet-and-iis6/_static/image5.jpg)

Cet écran affiche les options doivent être installés.

Cliquez sur ' suivant &gt;'.

![](aspnet-and-iis6/_static/image6.jpg)

Vous verrez cet écran pendant que vous avez sélectionnées sont en cours d’installation. Il est normal que l’autre boîte de dialogue apparaît comme services sont en cours d’installation. En outre, vous pouvez être invité pour l’emplacement du CD d’installation de Windows Server 2003.

Cliquez sur ' suivant &gt;» lorsque vous avez terminé.

![](aspnet-and-iis6/_static/image7.jpg)

Cliquez sur « Terminer » - Windows Server 2003 est maintenant configurée pour prendre en charge IIS 6.0 et ASP.NET 1.1.

### <a name="enabling-iis-option-2---manually-configuring-iis-and-aspnet"></a>L’activation de IIS, option #2 - Configuration manuelle d’IIS et ASP.NET

Si vous ne souhaitez pas utiliser le « Assistant Configurer votre serveur », vous pouvez éventuellement installer IIS 6.0 et ASP.NET 1.1 à l’aide de 'Ajout / Suppression de programmes' à partir du panneau.

Tout d’abord, ouvrez le panneau de configuration :

![](aspnet-and-iis6/_static/image8.jpg)

Ensuite, cliquez sur « Ajouter/supprimer des composants Windows » afin d’ouvrir l’Assistant Composants de Windows :

![](aspnet-and-iis6/_static/image9.jpg)

Mettez en surbrillance et « Serveur d’applications », puis cliquez sur « Détails » ? bouton :

![](aspnet-and-iis6/_static/image10.jpg)

Pour installer ASP.NET, consultez « ASP. NET ».

Cliquez sur « OK » pour revenir à l’Assistant Composants Windows. Cliquez sur ' suivant &gt;' à partir de l’Assistant Composants de Windows pour commencer l’installation :

![](aspnet-and-iis6/_static/image11.jpg)

Il est normal que l’autre boîte de dialogue apparaît comme services sont en cours d’installation. En outre, vous pouvez être invité pour l’emplacement du CD d’installation de Windows Server 2003.

Lorsque l’installation est terminée, vous verrez le dernier écran de l’Assistant Composants Windows :

![](aspnet-and-iis6/_static/image12.jpg)

IIS 6.0 et ASP.NET 1.1 sont maintenant configurés et disponibles.

## <a name="recommended-settings"></a>Paramètres recommandés

Lors de l’exécution d’ASP.NET 1.1 avec IIS 6.0, il existe plusieurs paramètres de configuration qui sont recommandés pour obtenir des performances optimales à partir de ASP.NET :

- Configuration limites de mémoire de processus de travail
- Configuration du recyclage de processus de travail

### <a name="configuring-worker-process-memory-limits"></a>Configuration limites de mémoire de processus de travail

Par défaut, IIS 6.0 ne définit pas une limite sur la quantité de mémoire que IIS est autorisé à utiliser. ASP. Fonctionnalité de Cache de NET s’appuie sur une limite de mémoire si bien que le Cache peut supprimer proactive éléments inutilisés de la mémoire.

Il est recommandé de configurer la fonctionnalité d’IIS 6.0 de recyclage de la mémoire. Pour configurer ce gestionnaire des Services IIS ouvert (Démarrer | Programmes | Outils d’administration | Internet Information Services). Une fois qu’ouvert, développez le dossier « Pools d’applications » :

Pour chaque pool d’applications :

![](aspnet-and-iis6/_static/image13.jpg)

1. Avec le bouton droit sur le pool d’applications, par exemple Sélectionnez « Propriétés » et « DefaultAppPool » :

![](aspnet-and-iis6/_static/image14.jpg)

2. Ensuite, activez le recyclage de la mémoire en cliquant sur un « mémoire maximale utilisée (en mégaoctets) :'. La valeur ne doit pas être supérieure à la quantité de mémoire physique (pas virtuel) sur le serveur, une bonne approximation est de 60 % de la mémoire physique, par exemple, pour un serveur avec 512 Mo de mémoire physique, sélectionnez 310. Il est également recommandé que la valeur maximale ne dépasse pas 800 Mo lors de l’utilisation d’un espace d’adressage de 2 Go. Si l’espace d’adressage de mémoire du serveur est de 3 Go, la limite de mémoire maximale pour le processus de travail peut être aussi élevée que 1, 800 Mo :

![](aspnet-and-iis6/_static/image15.jpg)

Cliquez sur 'Appliquer' et le 'OK' pour quitter la boîte de dialogue de propriétés. Répétez cette procédure pour tous les pools d’applications disponibles.

### <a name="configuring-worker-recycling"></a>Configuration du recyclage de processus de travail

Par défaut, IIS 6.0 est configuré pour recycler les processus de travail toutes les 29 heures. Il s’agit d’un peu trop courte pour une application ASP.NET en cours d’exécution et il est recommandé de désactiver le recyclage des processus de travail automatique.

Pour désactiver le recyclage des processus de travail automatique, tout d’abord ouvrir le Gestionnaire des Services Internet (Démarrer | Programmes | Outils d’administration | Internet Information Services). Une fois qu’ouvert, développez le dossier « Pools d’applications » :

![](aspnet-and-iis6/_static/image16.jpg)

Pour chaque pool d’applications :

1. Avec le bouton droit sur le pool d’applications, par exemple Sélectionnez « Propriétés » et « DefaultAppPool » :

![](aspnet-and-iis6/_static/image17.jpg)

2. Décochez la case « recycler les processus de travail (en minutes) :':

![](aspnet-and-iis6/_static/image18.jpg)

Cliquez sur 'Appliquer' et le 'OK' pour quitter la boîte de dialogue de propriétés. Répétez cette procédure pour tous les pools d’applications disponibles.

## <a name="granting-write-access-to-the-file-system"></a>Octroi d’accès en écriture au système de fichiers

Si votre application requiert l’accès en écriture au système de fichiers et que vous utilisez NTFS, vous devez modifier une liste de contrôle d’accès (ACL) sur le dossier ou fichier pour accorder un accès ASP.NET à l’accès.

Par exemple, pour accorder ASP.NET accès en écriture à la c:\inetpub\wwwroot tout d’abord ouvrir l’Explorateur et accédez au répertoire :

![](aspnet-and-iis6/_static/image19.jpg)

Ensuite, avec le bouton droit sur le répertoire, par exemple, « wwwroot » et sélectionnez Propriétés. Une fois que la boîte de dialogue Propriétés s’ouvre, sélectionnez l’onglet « Sécurité » :

![](aspnet-and-iis6/_static/image20.jpg)

Le répertoire c:\inetpub\wwwroot\ est un répertoire spécifique dans ce groupe IIS 6.0 spécial ' IIS\_WPG' est déjà allouée en lecture &amp; autorisations d’exécution, affichage du contenu du dossier et lecture. Toutefois, pour accorder l’autorisation d’écriture, vous devez cliquer sur la case à cocher Autoriser pour l’écriture :

![](aspnet-and-iis6/_static/image21.jpg)

IIS 6.0 a maintenant une autorisation d’écriture sur ce dossier. Pour accorder des autorisations d’écriture sur d’autres dossiers, procédez comme suit : une remarque, il se pouvez que vous deviez ajouter IIS\_groupe WPG si elle n’existe pas déjà.

> [!CAUTION]
> Octroi d’autorisation d’écriture sur le serveur IIS\_WPG autorisera toutes les applications ASP.NET écrire dans ce répertoire.

## <a name="supporting-integrated-authentication-with-sql-server"></a>Prise en charge de l’authentification intégrée avec SQL Server

L’authentification intégrée permet SQL Server pour tirer parti de l’authentification Windows NT pour valider les comptes de connexion SQL Server. Cela permet à l’utilisateur de contourner le processus d’ouverture de session SQL Server standard. Avec cette approche, un réseau peut accéder à une base de données SQL Server sans fournir une identification de connexion distincte ou le mot de passe, car SQL Server peut obtenir les informations d’utilisateur et mot de passe par le processus de sécurité de réseau Windows NT.

Le choix de l’authentification intégrée pour les applications ASP.NET d’est un bon choix, car aucune information d’identification n’est jamais stockées dans votre chaîne de connexion pour votre application. Au lieu de cela, la chaîne de connexion utilisée pour se connecter à SQL se présentera comme suit :

`"server=localhost; database=Northwind;Trusted_Connection=true"`

Cette chaîne de connexion indique à SQL Server à utiliser les informations d’identification Windows de l’application tente d’accéder à SQL Server. Dans le cas sur 6, cela serait un compte dans IIS\_groupe WPG.

Pour activer l’authentification intégrée entre SQL Server et ASP.NET, vous devez d’abord vous assurer que SQL Server est configuré pour l’authentification intégrée ou l’authentification en Mode mixte - Vérifiez auprès de votre administrateur pour le déterminer. Si SQL Server est dans un de ces deux modes, vous pouvez utiliser l’authentification intégrée.

Ouvrez SQL Server Enterprise Manager (Démarrer | Programmes | Microsoft SQL Server | Enterprise Manager), sélectionnez le serveur approprié et développez le dossier sécurité :

![](aspnet-and-iis6/_static/image22.jpg)

Si ' BUILTINT\IIS\_WPG' groupe n’est pas répertorié, avec le bouton droit sur les connexions et sélectionnez « Nouvelle connexion » :

![](aspnet-and-iis6/_static/image23.jpg)

Dans le ' nom : « zone de texte Entrez ' [nom du serveur ou du domaine] \IIS\_WPG' ou cliquez sur le bouton points de suspension pour ouvrir le sélecteur de groupe d’utilisateurs Windows NT :

![](aspnet-and-iis6/_static/image24.jpg)

Sélectionnez IIS l’ordinateur actuel\_groupe WPG et cliquez sur « Ajouter » et OK pour fermer le sélecteur.

Ensuite, vous devez également définir la base de données par défaut et les autorisations pour accéder à la base de données. Pour définir la base de données par défaut choisissez dans la liste déroulante, par exemple ci-dessous Northwind est sélectionnée :

![](aspnet-and-iis6/_static/image25.jpg)

Cliquez ensuite sur l’onglet accès de la base de données :

![](aspnet-and-iis6/_static/image26.jpg)

Cliquez sur la case à cocher Autoriser pour chaque base de données que vous souhaitez autoriser l’accès à. Vous devez également sélectionner des rôles de base de données, la vérification de la base de données\_propriétaire permettra à votre connexion a toutes les autorisations nécessaires pour gérer et utiliser la base de données sélectionnée.

Cliquez sur OK pour quitter la boîte de dialogue Propriétés. Votre application ASP.NET est maintenant configurée pour prendre en charge l’authentification intégrée de SQL Server.

## <a name="dont-run-aspnet-10-in-iis-60-native-mode"></a>N’exécutez pas ASP.NET version 1.0 en mode natif d’IIS 6.0

1.0 de ASP.NET sur IIS 6.0 est uniquement pris en charge en mode de compatibilité IIS 5.

Pour configurer ASP.NET 1.0 pour s’exécuter en mode de compatibilité IIS 5.0, ouvrez le Gestionnaire des Services Internet et cliquez avec le bouton droit sur Sites Web et sélectionnez Propriétés :

![](aspnet-and-iis6/_static/image27.jpg)

Basculez vers l’onglet Services et vérifier ? Exécuter les services Web en Mode d’Isolation 5.0 IIS ?

![](aspnet-and-iis6/_static/image28.jpg)
