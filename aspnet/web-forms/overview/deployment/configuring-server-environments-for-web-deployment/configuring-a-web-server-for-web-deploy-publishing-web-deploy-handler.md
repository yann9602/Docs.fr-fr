---
uid: web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler
title: "Publication de déploiement de configuration d’un serveur Web pour le Web (Gestionnaire de déploiement Web) | Documents Microsoft"
author: jrjlee
description: "Cette rubrique décrit comment configurer un serveur web d’Internet Information Services (IIS) pour prendre en charge la publication sur le web et déploiement à l’aide de la Han de déploiement Web IIS..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 90ebf911-1c46-4470-b876-1335bd0f590f
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler
msc.type: authoredcontent
ms.openlocfilehash: 81848c683fb9ddaa8942f030a520847a3c89fde0
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler"></a>Publication de déploiement de configuration d’un serveur Web pour le Web (Gestionnaire de déploiement Web)
====================
par [Jason Lee](https://github.com/jrjlee)

[Télécharger le PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Cette rubrique décrit comment configurer un serveur web d’Internet Information Services (IIS) pour prendre en charge la publication sur le web et déploiement à l’aide du Gestionnaire de déploiement Web IIS.
> 
> Lorsque vous travaillez avec Web Deploy 2.0 ou version ultérieure, il existe trois approches principales, que vous pouvez utiliser pour obtenir vos applications ou les sites sur un serveur web. Vous pouvez :
> 
> - Utilisez le *Service de l’Agent distant de déploiement Web*. Cette approche requiert moins de configuration du serveur web, mais vous devez fournir les informations d’identification d’un administrateur de serveur local pour pouvoir déployer quoi que ce soit sur le serveur.
> - Utilisez le *Gestionnaire de déploiement Web*. Cette approche est beaucoup plus complexe et nécessite davantage d’efforts initiale pour configurer le serveur web. Toutefois, lorsque vous utilisez cette approche, vous pouvez configurer IIS pour autoriser les utilisateurs non administrateurs effectuer le déploiement. Le Gestionnaire de déploiement Web est uniquement disponible dans IIS 7 ou version ultérieure.
> - Utilisez *déploiement hors connexion*. Cette approche requiert une configuration minimale du serveur web, mais un administrateur de serveur devez manuellement copier le package sur le serveur web et l’importer via le Gestionnaire des services Internet.
> 
> Pour plus d’informations sur les principales fonctionnalités, avantages et inconvénients de ces approches, consultez [en choisissant l’approche de droite pour le déploiement Web](choosing-the-right-approach-to-web-deployment.md).


Oui, si vous souhaitez autoriser les utilisateurs non-administrateurs à déployer du contenu vers des sites Web IIS spécifiques. Cette approche est souvent souhaitable dans ces types de scénarios :

- Environnements de production ou intermédiaire, où le compte de la personne ou le service qui déclenche le déploiement à distance est peu probable d’accéder aux informations de connexion d’un administrateur de serveur.
- Environnements hébergés, où vous souhaitez permettre aux utilisateurs distants pour mettre à jour leurs sites Web sans leur donner le contrôle total de vos serveurs web (ou l’accès à des sites Web d’une autre personne).

Dans les scénarios de développement ou de test, ou dans les organisations de petite taille, déploiement de contenu à l’aide des informations d’identification d’administrateur de serveur est souvent inférieur litige. Dans ces scénarios, configuration de vos serveurs web pour prendre en charge le déploiement à l’aide du [Web Deploy Remote Agent Service](configuring-a-web-server-for-web-deploy-publishing-remote-agent.md) propose une approche plus simple.

## <a name="task-overview"></a>Vue d’ensemble de la tâche

Pour configurer le serveur web pour accepter et de déployer des packages web à partir d’un ordinateur distant à l’aide de la méthode de gestionnaire de déploiement Web, vous devez :

- Créez ou choisissez, un compte utilisateur de domaine (le « utilisateur non administrateur ») dont informations d’identification que vous allez utiliser pour effectuer des déploiements.
- Installer IIS 7.5, y compris le Service de gestion Web et le module d’authentification de base.
- Installez Web Deploy 2.1 ou version ultérieure.
- Configurer le Service de gestion Web pour autoriser les connexions à distance et démarrer le service.
- Créer un site Web d’IIS pour héberger le contenu a été déployé.
- Accorder des autorisations de votre utilisateur non administrateur sur votre site Web dans le Gestionnaire des services Internet.
- Vérifiez que le Service de gestion Web des règles de délégation contrainte autorisent le service pour ajouter et modifier le contenu du site Web à l’aide de votre compte d’utilisateur non administrateur.
- Configurer les pare-feu pour autoriser les connexions entrantes sur le port 8172.

Pour héberger l’exemple de solution ContactManager plus précisément, vous devrez également :

- Installer le Kit de développement .NET Framework 4.0.
- Installez ASP.NET MVC 3.

Cette rubrique vous indique comment effectuer chacune de ces procédures. Les tâches et les procédures pas à pas dans cette rubrique supposent que vous avez démarré avec une build propre serveur exécutant Windows Server 2008 R2. Avant de continuer, vérifiez que :

- Windows Server 2008 R2 Service Pack 1 et toutes les mises à jour disponibles sont installés.
- Le serveur est joint au domaine.
- Le serveur a une adresse IP statique.

> [!NOTE]
> Pour plus d’informations sur la jonction des ordinateurs à un domaine, consultez [jonction au domaine et ouverture d’une session](https://technet.microsoft.com/library/cc725618(v=WS.10).aspx). Pour plus d’informations sur la configuration des adresses IP statiques, consultez [configurer une adresse IP statique](https://technet.microsoft.com/library/cc754203(v=ws.10).aspx).


## <a name="install-products-and-components"></a>Installer les produits et composants

Cette section vous guide dans l’installation des produits requis et composants sur le serveur web. Avant de commencer, la meilleure pratique consiste à exécuter Windows Update pour vous assurer que votre serveur est à jour.

Dans ce cas, vous devez installer les opérations suivantes :

- **Configuration recommandée pour IIS 7**. Cela permet la **serveur Web (IIS)** rôle sur votre serveur web et installe l’ensemble des modules IIS et les composants dont vous avez besoin pour héberger une application ASP.NET.
- **IIS : Service de gestion**. Cette commande installe le Service de gestion Web (WMSvc) dans IIS. Ce service permet la gestion à distance des sites Web IIS et expose le point de terminaison du Gestionnaire de déploiement Web pour les clients.
- **IIS : L’authentification de base**. Cette commande installe le module d’authentification de base IIS. Ce Service de gestion Web (WMSvc) vous permet d’authentifie les informations d’identification que vous fournissez.
- **Outil de déploiement 2.1 ou version ultérieure de Web**. Cette commande installe Web Deploy (et son fichier exécutable sous-jacent, MSDeploy.exe) sur votre serveur. Dans le cadre de ce processus, il installe le Gestionnaire de déploiement Web et il s’intègre avec le Service de gestion Web.
- **.NET framework 4.0**. Cela est nécessaire pour exécuter des applications qui ont été créées sur cette version du .NET Framework.
- **ASP.NET MVC 3**. Cette opération installe les assemblys que vous avez besoin pour exécuter des applications MVC 3.

> [!NOTE]
> Cette procédure pas à pas décrit l’utilisation de Web Platform Installer pour installer et configurer les différents composants. Bien que vous n’avez pas à utiliser Web Platform Installer, elle simplifie le processus d’installation en détectant les dépendances automatiquement et en garantissant que vous obtenez toujours les dernières versions de produit. Pour plus d’informations, consultez [Microsoft Web Platform Installer 3.0](https://go.microsoft.com/?linkid=9805118).


**Pour installer les produits requis et les composants**

1. Téléchargez et installez le [Web Platform Installer](https://go.microsoft.com/?linkid=9805118).
2. Lors de l’installation est terminée, le programme d’installation de la plateforme Web se lance automatiquement.

    > [!NOTE]
    > Vous pouvez maintenant lancer le programme d’installation de la plateforme Web à tout moment à partir de la **Démarrer** menu. Pour ce faire, sur le **Démarrer** menu, cliquez sur **tous les programmes**, puis cliquez sur **Microsoft Web Platform Installer**.
3. En haut de la **Web Platform Installer 3.0** fenêtre, cliquez sur **produits**.
4. Sur le côté gauche de la fenêtre, dans le volet de navigation, cliquez sur **infrastructures**.
5. Dans le **Microsoft .NET Framework 4** de ligne, si le .NET Framework n’est pas déjà installé, cliquez sur **ajouter**.

    > [!NOTE]
    > Vous avez peut-être déjà installé le .NET Framework 4.0 via Windows Update. Si un produit ou un composant est déjà installé, le programme d’installation de la plateforme Web indiquera en remplaçant le **ajouter** bouton avec le texte **installé**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image1.png)
6. Dans le **ASP.NET MVC 3 (Visual Studio 2010)** , cliquez sur **ajouter**.
7. Dans le volet de navigation, cliquez sur **Server**.
8. Dans le **Configuration IIS 7 recommandée** , cliquez sur **ajouter**.
9. Dans le **2.1 d’outil de déploiement Web** , cliquez sur **ajouter**.
10. Dans le **IIS : authentification de base** , cliquez sur **ajouter**.
11. Dans le **IIS : Service de gestion** , cliquez sur **ajouter**.
12. Cliquez sur **Installer**. Le programme d’installation de la plateforme Web affiche une liste de produits & #x 2014 ; ainsi que toutes les dépendances associées et les #x 2014 ; à installer et vous invite à accepter les termes du contrat de licence.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image2.png)
13. Passez en revue les termes du contrat de licence et si vous acceptez les termes du contrat, cliquez sur **J’accepte**.
14. Une fois l’installation terminée, cliquez sur **Terminer**, puis fermez le **Web Platform Installer 3.0** fenêtre.

Si vous avez installé le .NET Framework 4.0 avant d’installer IIS, vous devez exécuter le [outil ASP.NET IIS Registration](https://msdn.microsoft.com/library/k6h9cz8h(v=VS.100).aspx) (aspnet\_regiis.exe) pour inscrire la dernière version de ASP.NET avec IIS. Si vous ne le faites pas, vous trouverez que IIS sera contenu statique (comme les fichiers HTML) sans problème, mais elle retournera **404.0 d’erreur HTTP – introuvable** lorsque vous tentez d’accéder au contenu ASP.NET. Vous pouvez utiliser la procédure suivante pour vous assurer qu’ASP.NET 4.0 est enregistré.

**Pour inscrire ASP.NET 4.0 avec IIS**

1. Cliquez sur **Démarrer**, puis tapez **invite de commandes**.
2. Dans les résultats de recherche, cliquez sur **invite de commandes**, puis cliquez sur **exécuter en tant qu’administrateur**.
3. Dans la fenêtre d’invite de commandes, accédez à la **%WINDIR%\Microsoft.NET\Framework\v4.0.30319** active.
4. Tapez la commande suivante et appuyez sur ENTRÉE :

    [!code-console[Main](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/samples/sample1.cmd)]
5. Si vous envisagez d’héberger des applications web 64 bits à tout moment, vous devez également inscrire la version 64 bits de ASP.NET avec IIS. Pour ce faire, dans la fenêtre d’invite de commandes, accédez à la **%WINDIR%\Microsoft.NET\Framework64\v4.0.30319** active.
6. Tapez la commande suivante et appuyez sur ENTRÉE :

    [!code-console[Main](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/samples/sample2.cmd)]

Comme une bonne pratique, utilisez Windows Update à nouveau à ce stade pour télécharger et installer les mises à jour disponibles pour les nouveaux produits et les composants que vous avez installée.

## <a name="configure-the-web-management-service"></a>Configurer le Service de gestion Web

Maintenant que vous avez installé tous les éléments que nécessaires, l’étape suivante consiste à configurer le Service de gestion Web dans IIS. À un niveau élevé, vous aurez besoin effectuer ces tâches :

- Activer l’authentification de base au niveau du serveur.
- Configurer le Service de gestion Web pour accepter les connexions à distance.
- Démarrez le Service de gestion Web.
- Vérifiez que les règles de délégation de Service de gestion Web requis sont en place.

**Pour configurer le Service de gestion Web**

1. Sur le **Démarrer** menu, pointez sur **outils d’administration**, puis cliquez sur **Gestionnaire des Services Internet (IIS)**.
2. Dans le Gestionnaire des services Internet, dans le **connexions** volet, cliquez sur le nœud du serveur (par exemple, **STAGEWEB1**).

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image3.png)
3. Dans le volet central, sous **IIS**, double-cliquez sur **authentification**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image4.png)
4. Avec le bouton droit **l’authentification de base**, puis cliquez sur **activer**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image5.png)
5. Dans le **connexions** volet, cliquez sur le nœud du serveur pour revenir aux paramètres de niveau supérieur.
6. Dans le volet central, sous **gestion**, double-cliquez sur **Service de gestion**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image6.png)
7. Dans le volet central, sélectionnez **activer les connexions distantes**.

    > [!NOTE]
    > Si le Service de gestion Web est déjà en cours d’exécution, vous devez d’abord l’arrêter.
8. Dans le **Actions** volet, cliquez sur **Démarrer** pour démarrer le Service de gestion Web.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image7.png)
9. Si vous êtes invité à enregistrer vos paramètres, cliquez sur **Oui**.

    > [!NOTE]
    > Vous pouvez souhaiter également configurer le service pour démarrer automatiquement. Pour ce faire, ouvrez la console Services, cliquez sur **Service de gestion Web**, puis cliquez sur **propriétés**. Dans le **type de démarrage** liste déroulante, sélectionnez **automatique**, puis cliquez sur **OK**.
10. Dans le **connexions** volet, cliquez sur le nœud du serveur pour revenir aux paramètres de niveau supérieur.
11. Dans le volet central, sous **gestion**, double-cliquez sur **délégation de Service de gestion**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image8.png)
12. Vérifiez que le volet central contient un ensemble de règles.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image9.png)

    Ces règles permettent aux utilisateurs de Service de gestion Web autorisés à utiliser différents fournisseurs de déploiement Web. Par exemple, pour déployer des applications web et le contenu à IIS via le Gestionnaire de déploiement Web, il doit y avoir une règle de délégation qui permet à tous les utilisateurs du Service de gestion Web pour utiliser authentifiés le **contentPath** et **iisApp**  fournisseurs (la dernière règle que vous pouvez voir la capture d’écran).

    Si vous avez installé des produits et composants dans l’ordre décrit dans cette rubrique, la dernière version de Web Deploy doit ajouter automatiquement toutes les règles de délégation requis pour le Service de gestion Web. Si la page de délégation de Service de gestion n’affiche pas toutes les règles, vous devez les créer vous-même. Pour obtenir des instructions sur la façon de procéder, consultez [configurer le Gestionnaire de déploiement Web](https://go.microsoft.com/?linkid=9805124).
13. Dans le **connexions** volet, cliquez sur le nœud du serveur pour revenir aux paramètres de niveau supérieur.

## <a name="create-and-configure-an-iis-website"></a>Créer et configurer un site Web IIS

Avant de pouvoir déployer le contenu web à votre serveur, vous devez créer et configurer un site Web d’IIS pour héberger le contenu. Web Deploy pouvez déployer uniquement les packages web sur un site Web IIS existant ; Il ne peut pas créer le site Web pour vous. Vous devez également effectuer un peu de configuration supplémentaire pour permettre à votre compte non administrateur déployer le contenu à distance. À un niveau élevé, vous aurez besoin effectuer ces tâches :

- Créez un dossier sur le système de fichiers pour héberger votre contenu.
- Créer un site Web d’IIS pour prendre en charge le contenu et l’associer avec le dossier local.
- Des permissions de lecture à l’identité de pool d’applications sur le dossier local.
- Accorder des autorisations IIS nécessaires pour le compte de domaine qui sera déployée de votre application web.

Bien que rien ne vous empêche de déploiement de contenu au site Web par défaut dans IIS, cette approche n’est pas recommandée pour autre chose que des scénarios de test ou de démonstration. Pour simuler un environnement de production, vous devez créer un site Web IIS avec les paramètres qui sont spécifiques à la configuration requise de votre application.

**Pour créer un site Web IIS**

1. Sur le système de fichiers local, créez un dossier pour stocker votre contenu (par exemple, **C:\DemoSite**).
2. Sur le **Démarrer** menu, pointez sur **outils d’administration**, puis cliquez sur **Gestionnaire des Services Internet (IIS)**.
3. Dans le Gestionnaire des services Internet, dans le **connexions** volet, développez le nœud du serveur (par exemple, **STAGEWEB1**).

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image10.png)
4. Cliquez sur le **Sites** nœud, puis cliquez sur **ajouter un Site Web**.
5. Dans le **nom du Site** , tapez un nom pour le site Web IIS (par exemple, **DemoSite**).
6. Dans le **chemin d’accès physique** , tapez (ou accédez à) le chemin d’accès à votre dossier local (par exemple, **C:\DemoSite**).
7. Dans le **Port** , tapez le numéro de port sur lequel vous souhaitez héberger le site Web (par exemple, **85**).

    > [!NOTE]
    > Les numéros de port standard sont 80 pour HTTP et 443 pour HTTPS. Toutefois, si vous hébergez à ce site Web sur le port 80, vous devez arrêter le site Web par défaut avant que vous pouvez accéder à votre site.
8. Laissez le **nom d’hôte** zone vide, sauf si vous souhaitez configurer un enregistrement de nom de domaine (DNS) pour le site Web, puis cliquez sur **OK**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image11.png)

    > [!NOTE]
    > Dans un environnement de production, vous souhaiterez probablement héberger votre site Web sur le port 80 et de configurer un en-tête d’hôte, ainsi que les enregistrements DNS correspondants. Pour plus d’informations sur la configuration des en-têtes d’hôtes dans IIS 7, consultez [configurer un en-tête d’hôte pour un Site Web (IIS 7)](https://technet.microsoft.com/library/cc753195(WS.10).aspx). Pour plus d’informations sur le rôle de serveur DNS dans Windows Server 2008 R2, consultez [vue d’ensemble du serveur DNS](https://technet.microsoft.com/en-gb/library/cc770392.aspx) et [serveur DNS](https://technet.microsoft.com/windowsserver/dd448607).
9. Dans le volet **Actions** , sous **Modifier le Site**, cliquez sur **Liaisons**.
10. Dans le **liaisons de Site** boîte de dialogue, cliquez sur **ajouter**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image12.png)
11. Dans le **ajouter la liaison de Site** boîte de dialogue, définissez la **adresse IP** et **Port** pour correspondre à la configuration de votre site existant.
12. Dans le **nom d’hôte** , tapez le nom de votre serveur web (par exemple, **STAGEWEB1**), puis cliquez sur **OK**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image13.png)

    > [!NOTE]
    > La première liaison de site vous permet d’accéder au site localement à l’aide de l’adresse IP et le port ou `http://localhost:85`. La deuxième liaison de site vous permet d’accéder au site à partir d’autres ordinateurs du domaine à l’aide du nom de l’ordinateur (par exemple, http://stageweb1:85).
13. Dans le **liaisons de Site** boîte de dialogue, cliquez sur **fermer**.
14. Dans le **connexions** volet, cliquez sur **Pools d’applications**.
15. Dans le **Pools d’applications** volet, cliquez sur le nom de votre pool d’applications, puis cliquez sur **les paramètres de base**. Par défaut, le nom de votre pool d’applications correspond au nom de votre site Web (par exemple, **DemoSite**).
16. Dans le **version du .NET Framework** liste, sélectionnez **.NET Framework v4.0.30319**, puis cliquez sur **OK**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image14.png)

    > [!NOTE]
    > L’exemple de solution requiert .NET Framework 4.0. Cela n’est pas en général une exigence pour Web Deploy.

Dans l’ordre de votre site Web traiter du contenu, l’identité du pool d’applications doit avoir autorisations de lecture sur le dossier local qui stocke le contenu. Pools d’applications dans IIS 7.5, exécutent une identité de pool d’applications unique par défaut (contrairement aux versions précédentes d’IIS, où des pools d’applications sont exécute généralement en utilisant le compte de Service réseau). L’identité du pool d’applications n’est pas un compte d’utilisateur réel et ne s’affichent pas sur toutes les listes d’utilisateurs ou les groupes & #x 2014 ; au lieu de cela, elle est créée dynamiquement lorsque le pool d’applications est démarré. Chaque identité de pool d’applications est ajoutée à la variable locale **IIS\_IUSRS** groupe de sécurité comme un élément masqué.

Pour accorder des autorisations à une identité de pool d’applications sur un fichier ou dossier, vous avez deux options :

- Affecter des autorisations à l’identité du pool d’applications directement, en utilisant le format **pool d’applications IIS\***[nom] * (par exemple, **IIS AppPool\DemoSite**).
- Assignation d’autorisations pour le **IIS\_IUSRS** groupe.

L’approche la plus courante consiste à affecter des autorisations à l’ordinateur local **IIS\_IUSRS** du groupe, car cette approche vous permet de modifier des pools d’applications sans reconfigurer les autorisations de système de fichiers. La procédure suivante utilise cette approche basée sur le groupe.

> [!NOTE]
> Pour plus d’informations sur les identités du pool d’applications dans IIS 7.5, consultez [identités du Pool d’applications](https://go.microsoft.com/?linkid=9805123).


**Pour configurer les autorisations de dossier pour un site Web IIS**

1. Dans l’Explorateur Windows, accédez à l’emplacement de votre dossier local.
2. Cliquez avec le bouton droit, puis cliquez sur **propriétés**.
3. Sur le **sécurité** , cliquez sur **modifier**, puis cliquez sur **ajouter**.
4. Cliquez sur **emplacements**. Dans le **emplacements** boîte de dialogue, sélectionnez le serveur local, puis cliquez sur **OK**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image15.png)
5. Dans le **sélectionner des utilisateurs ou groupes** boîte de dialogue, tapez **IIS\_IUSRS**, cliquez sur **vérifier les noms**, puis cliquez sur **OK**.
6. Dans le **autorisations pour *** [nom du dossier]* boîte de dialogue, notez que le nouveau groupe a été affecté le **en lecture &amp; exécuter**, **répertorier le contenu du dossier**, et **En lecture** autorisations par défaut. Laissez inchangée et cliquez sur **OK**.
7. Cliquez sur **OK** pour fermer la *[nom du dossier] *** propriétés** boîte de dialogue.

En tant que dernière tâche, vous devez accorder les autorisations appropriées à l’utilisateur non administrateur dont informations d’identification que vous utiliserez pour déployer du contenu. Cet utilisateur nécessite les autorisations pour déployer du contenu à votre site Web à distance.

**Pour configurer les autorisations de site Web IIS pour un utilisateur non administrateur domaine**

1. Dans le Gestionnaire des services Internet, dans le **connexions** volet, cliquez sur le nœud de votre site Web (par exemple, **DemoSite**), pointez sur **déployer**, puis cliquez sur **Configure Web La publication de déploiement**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image16.png)
2. Dans le **configuration Web déployer de la publication** boîte de dialogue, à droite de la **sélectionner un utilisateur pour accorder des autorisations de publication** , cliquez sur le bouton de sélection.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image17.png)
3. Dans le **autoriser un utilisateur** boîte de dialogue, tapez le nom de domaine et d’utilisateur du compte que vous souhaitez utiliser pour déployer du contenu, puis cliquez sur **OK**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image18.png)
4. Dans le **configuration Web déployer de la publication** boîte de dialogue, cliquez sur **le programme d’installation**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image19.png)

    > [!NOTE]
    > Cette opération effectue deux fonctions clés en une seule étape. Tout d’abord, il accorde l’autorisation de l’utilisateur à modifier le site Web à distance via le Service de gestion Web, selon les règles de délégation que vous avez examiné dans la section précédente. En second lieu, il accorde l’utilisateur le contrôle total du dossier source pour le site Web, ce qui permet à l’utilisateur à ajouter, modifier et définir des autorisations sur le contenu du site Web.
5. Dans le **configuration Web déployer de la publication** boîte de dialogue, cliquez sur **fermer**.

## <a name="configure-firewall-exceptions"></a>Configurer des Exceptions de pare-feu

Par défaut, le Service de gestion Web IIS écoute sur le port TCP 8172. Si le pare-feu Windows est activé sur votre serveur web, vous devez créer une règle de trafic entrant pour autoriser le trafic TCP sur le port 8172 (par défaut dans le pare-feu Windows, tout le trafic sortant est autorisé). Si vous utilisez un pare-feu tiers, vous devez créer des règles pour autoriser le trafic.

| Direction | À partir du Port | Au Port | Type de port |
| --- | --- | --- | --- |
| Trafic entrant | Any | 8172 | TCP |
| Sortant | 8172 | Any | TCP |
  

Pour plus d’informations sur la configuration des règles du pare-feu Windows, consultez [configuration des règles de pare-feu](https://technet.microsoft.com/library/dd448559(WS.10).aspx). Pour les pare-feu tiers, consultez la documentation du produit.

## <a name="conclusion"></a>Conclusion

Votre serveur web doit maintenant être prêt à accepter des déploiements à distance au Gestionnaire de déploiement Web via le Service de gestion Web. Avant de tenter de déployer une application web sur le serveur, vous voudrez vérifier ces points clés :

- Vous avez activé l’authentification de base au niveau du serveur dans IIS ?
- Vous avez activé des connexions à distance pour le Service de gestion Web ?
- Vous avez démarré le Service de gestion Web ?
- Existe-t-il des gestion des règles de délégation de service en place ?
- L’identité du pool d’applications dispose d’un accès en lecture au dossier source pour votre site Web ?
- Le compte d’utilisateur non administrateur dispose des autorisations au niveau du site dans IIS ?
- Votre pare-feu autorise les connexions entrantes vers le serveur sur le port TCP 8172 ?

## <a name="further-reading"></a>informations supplémentaires

Pour obtenir des conseils sur la configuration des fichiers de projet personnalisés Microsoft Build Engine (MSBuild) pour déployer des packages web pour le Gestionnaire de déploiement Web, consultez [configuration des propriétés de déploiement pour un environnement cible](configuring-deployment-properties-for-a-target-environment.md).

>[!div class="step-by-step"]
[Précédent](configuring-a-web-server-for-web-deploy-publishing-remote-agent.md)
[Suivant](configuring-a-web-server-for-web-deploy-publishing-offline-deployment.md)
