---
uid: web-forms/overview/deployment/visual-studio-web-deployment/command-line-deployment
title: "Déploiement de Web ASP.NET à l’aide de Visual Studio : déploiement de la ligne de commande | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, en utilisant des éléments..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/15/2013
ms.topic: article
ms.assetid: 82b8dea0-f062-4ee4-8784-3ffa30fbb1ca
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/command-line-deployment
msc.type: authoredcontent
ms.openlocfilehash: 8446b3fc05e3ef4a5a30c753c989252fd7f1a56f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-web-deployment-using-visual-studio-command-line-deployment"></a>Déploiement de Web ASP.NET à l’aide de Visual Studio : déploiement de la ligne de commande
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet de démarrage](http://go.microsoft.com/fwlink/p/?LinkId=282627)

> Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, à l’aide de Visual Studio 2012 ou Visual Studio 2010. Pour plus d’informations sur la série, consultez [le premier didacticiel de la série](introduction.md).


## <a name="overview"></a>Vue d'ensemble

Ce didacticiel vous montre comment appeler le web de Visual Studio publier le pipeline à partir de la ligne de commande. Cela est utile pour les scénarios où vous souhaitez [automatiser le processus de déploiement](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery.md) au lieu de faire manuellement dans Visual Studio, généralement en utilisant un [système de contrôle de version code source](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control.md).

## <a name="make-a-change-to-deploy"></a>Apportez une modification à déployer

Actuellement, la page à propos affiche le code du modèle.

![Sur la page avec le code de modèle](command-line-deployment/_static/image1.png)

Vous allez remplacer qui avec du code qui affiche un résumé de l’inscription d’étudiants.

Ouvrez le *About.aspx* page, de supprimer toutes les marques à l’intérieur de la `MainContent` `Content` élément et insérez le balisage suivant à la place :

[!code-aspx[Main](command-line-deployment/samples/sample1.aspx)]

Exécutez le projet et sélectionnez le **sur** page.

![Sur la page](command-line-deployment/_static/image2.png)

## <a name="deploy-to-test-by-using-the-command-line"></a>Déploiement de Test à l’aide de la ligne de commande

Vous ne déployez une autre modification de base de données, dans ce déploiement de base de données dbDacFx désactiver pour la base de données ContosoUniversity aspnet. Ouvrez le **publier le site Web** Assistant et dans chacun des trois publier les profils, désactivez le **mise à jour de la base de données** case à cocher sur la **paramètres** onglet.

Dans la page d’accueil de Windows 8, recherchez **invite de commandes développeur pour VS2012**.

Cliquez sur l’icône de **invite de commandes développeur pour VS2012** et cliquez sur **exécuter en tant qu’administrateur**.

Entrez la commande suivante à l’invite de commandes, en remplaçant le chemin d’accès au fichier de solution avec le chemin d’accès à votre fichier de solution :

[!code-console[Main](command-line-deployment/samples/sample2.cmd)]

MSBuild génère la solution et le déploie sur l’environnement de test.

![Sortie de la ligne de commande](command-line-deployment/_static/image3.png)

Ouvrez un navigateur et accédez à `http://localhost/ContosoUniversity`, puis cliquez sur le **sur** page pour vérifier que le déploiement a réussi.

Si vous n’avez pas encore créé d’étudiants dans le test, vous verrez une page vide sous la **étudiant corps statistiques** titre. Accédez à la **étudiants** , cliquez sur **ajouter un étudiant**et ajouter des étudiants, puis revenez à la **sur** page pour voir les statistiques de student.

![Sur la page dans un environnement de Test](command-line-deployment/_static/image4.png)

## <a name="key-command-line-options"></a>Options de la clé de ligne de commande

La commande que vous avez entré, le chemin d’accès du fichier de solution et deux propriétés sont passés à MSBuild :

[!code-console[Main](command-line-deployment/samples/sample3.cmd)]

### <a name="deploying-the-solution-versus-deploying-individual-projects"></a>Déploiement de la solution de déploiement des projets individuels

En spécifiant le fichier de solution entraîne par tous les projets dans la solution à générer. Si vous avez plusieurs projets web dans la solution, le comportement de MSBuild suivant s’applique :

- Les propriétés que vous spécifiez sur la ligne de commande sont passées à chaque projet. Par conséquent, chaque projet web doit avoir un profil de publication avec le nom que vous spécifiez. Si vous spécifiez `/p:PublishProfile=Test`, chaque projet web doit avoir un profil de publication nommé *Test*.
- Vous pouvez correctement publier un projet quand même à générer un autre. Pour plus d’informations, consultez le thread stackoverflow [MSBuild échoue avec deux packages](http://stackoverflow.com/questions/14226451/msbuild-fails-with-two-packages).

Si vous spécifiez un projet individuel au lieu d’une solution, vous devez ajouter un paramètre qui spécifie la version de Visual Studio. Si vous utilisez Visual Studio 2012, la ligne de commande serait similaire à l’exemple suivant :

[!code-console[Main](command-line-deployment/samples/sample4.cmd?highlight=1)]

Le numéro de version de Visual Studio 2010 est 10.0. Pour plus d’informations, consultez [compatibilité de projet Visual Studio et VisualStudioVersion](http://sedodream.com/2012/08/19/VisualStudioProjectCompatabilityAndVisualStudioVersion.aspx) sur le blog de Sayed Hashimi.

### <a name="specifying-the-publish-profile"></a>Spécifier le profil de publication

Vous pouvez spécifier le profil de publication par nom ou par le chemin complet vers le *.pubxml* de fichiers, comme indiqué dans l’exemple suivant :

[!code-console[Main](command-line-deployment/samples/sample5.cmd?highlight=1)]

### <a name="web-publish-methods-supported-for-command-line-publishing"></a>Méthodes prises en charge pour la publication en ligne de commande de publication Web

Trois méthodes de publication sont pris en charge pour la publication de la ligne de commande :

- `MSDeploy`-Publier à l’aide de Web Deploy.
- `Package`-Publier en créant un Package de déploiement Web. Vous devez l’installer séparément à partir de la commande MSBuild qui le crée.
- `FileSystem`-Publier en copiant les fichiers dans un dossier spécifié.

### <a name="specifying-the-build-configuration-and-platform"></a>Spécification de la plateforme et la configuration de build

La plateforme et la configuration de build doivent être définies dans Visual Studio ou sur la ligne de commande. Les profils de publication incluent des propriétés qui sont nommées `LastUsedBuildConfiguration` et `LastUsedPlatform`, mais vous ne pouvez pas définir ces propriétés afin de déterminer la façon dont le projet est généré. Pour plus d’informations, consultez [MSBuild : comment définir la propriété de configuration](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) sur le blog de Sayed Hashimi.

## <a name="deploy-to-staging"></a>Déployer dans l’environnement intermédiaire

Pour déployer sur Azure, vous devez ajouter le mot de passe à la ligne de commande. Si vous avez enregistré le mot de passe dans le profil de publication dans Visual Studio, il a été stocké dans un format chiffré dans la votre *. pubxml.user* fichier. Ce fichier n’est pas accessible par MSBuild lorsque vous effectuez un déploiement de la ligne de commande, donc vous devez passer le mot de passe dans un paramètre de ligne de commande.

1. Copier le mot de passe dont vous avez besoin à partir de la *.publishsettings* fichier que vous avez téléchargé précédemment pour le site web intermédiaire. Le mot de passe est la valeur de la `userPWD` attribut pour le Web Deploy `publishProfile` élément.

    ![Mot de passe de Web Deploy](command-line-deployment/_static/image5.png)
2. Dans la page d’accueil de Windows 8, recherchez **invite de commandes développeur pour VS2012**, puis cliquez sur l’icône pour ouvrir l’invite de commandes. (Vous n’avez pas pour l’ouvrir en tant qu’administrateur instant, car vous ne déployez pas sur le serveur IIS sur l’ordinateur local.)
3. Entrez la commande suivante à l’invite de commandes, en remplaçant le chemin d’accès au fichier de solution avec le chemin d’accès à votre fichier solution et le mot de passe avec votre mot de passe :

    [!code-console[Main](command-line-deployment/samples/sample6.cmd)]

    Notez que cette ligne de commande inclut un paramètre supplémentaire : `/p:AllowUntrustedCertificate=true`. Comme ce didacticiel est écrit, le `AllowUntrustedCertificate` propriété doit être définie lorsque vous publiez sur Azure à partir de la ligne de commande. Quand le correctif pour ce bogue est publié, vous n’aurez pas ce paramètre.
4. Ouvrez un navigateur et accédez à l’URL de votre site intermédiaire, puis cliquez sur le **sur** page pour vérifier que le déploiement a réussi.

    Comme vous l’avez vu précédemment pour l’environnement de test, vous devrez créer des étudiants pour voir les statistiques sur les **sur** page.

## <a name="deploy-to-production"></a>Déployer en production

Le processus de déploiement en production est similaire au processus de mise en lots.

1. Copier le mot de passe dont vous avez besoin à partir de la *.publishsettings* fichier que vous avez téléchargé précédemment pour le site web de production.
2. Ouvrez **invite de commandes développeur pour VS2012**.
3. Entrez la commande suivante à l’invite de commandes, en remplaçant le chemin d’accès au fichier de solution avec le chemin d’accès à votre fichier solution et le mot de passe avec votre mot de passe :

    [!code-console[Main](command-line-deployment/samples/sample7.cmd)]

    Pour un site de production réel, s’il existe également une modification de la base de données, vous en général, copiez la *application\_offline.htm* au site avant le déploiement de fichiers et le supprimer après un déploiement réussi.
4. Ouvrez un navigateur et accédez à l’URL de votre site intermédiaire, puis cliquez sur le **sur** page pour vérifier que le déploiement a réussi.

## <a name="summary"></a>Résumé

Vous avez déployé une mise à jour de l’application à l’aide de la ligne de commande.

![Sur la page dans un environnement de Test](command-line-deployment/_static/image6.png)

Dans l’étape suivante du didacticiel, vous verrez un exemple montrant comment étendre le site web Pipe-Line de publication. L’exemple illustre comment déployer des fichiers qui ne sont pas inclus dans le projet.

>[!div class="step-by-step"]
[Précédent](deploying-a-database-update.md)
[Suivant](deploying-extra-files.md)
