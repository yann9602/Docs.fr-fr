---
uid: web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-configuring-project-properties-4-of-12
title: "Déploiement d’Application Web ASP.NET avec SQL Server Compact à l’aide de Visual Studio ou Visual Web Developer : configuration des propriétés de projet - 4 12 | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels vous montre comment déployer (publier) ASP.NET projet d’application web qui inclut une base de données SQL Server Compact à l’aide de Visual Stu..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/17/2011
ms.topic: article
ms.assetid: 8b013630-842c-4d44-a6fc-c6be43e7210f
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-configuring-project-properties-4-of-12
msc.type: authoredcontent
ms.openlocfilehash: 2ba202a1a0d0ba752576e8906b739cc9e83fde2a
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="deploying-an-aspnet-web-application-with-sql-server-compact-using-visual-studio-or-visual-web-developer-configuring-project-properties---4-of-12"></a>Déploiement d’Application Web ASP.NET avec SQL Server Compact à l’aide de Visual Studio ou Visual Web Developer : configuration des propriétés de projet - 4 de 12
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet de démarrage](http://code.msdn.microsoft.com/Deploying-an-ASPNET-Web-4e31366b)

> Cette série de didacticiels vous montre comment déployer (publier) ASP.NET projet d’application web qui inclut une base de données SQL Server Compact à l’aide de Visual Studio 2012 RC ou Visual Studio Express 2012 RC pour le Web. Vous pouvez également utiliser Visual Studio 2010 si vous installez la mise à jour de publication Web. Pour une présentation de la série, consultez [le premier didacticiel de la série](deployment-to-a-hosting-provider-introduction-1-of-12.md).
> 
> Pour obtenir un didacticiel qui montre les fonctionnalités de déploiement introduites après la version RC de Visual Studio 2012, montre comment déployer des éditions de SQL Server autre que SQL Server Compact et montre comment déployer dans Azure App Service Web Apps, consultez [déploiement de Web ASP.NET à l’aide de Visual Studio](../../deployment/visual-studio-web-deployment/introduction.md).


## <a name="overview"></a>Vue d'ensemble

Certaines options de déploiement sont configurées dans les propriétés de projet qui sont stockées dans le fichier projet (la *.csproj* ou *.vbproj* fichier). Dans la plupart des cas, les valeurs par défaut de ces paramètres sont ce que vous le souhaitez, mais vous pouvez utiliser la **propriétés du projet** UI intégrées à Visual Studio pour travailler avec ces paramètres si vous devez les modifier. Dans ce didacticiel, vous passez en revue les paramètres de déploiement dans **propriétés du projet**. Vous également créez un fichier d’espace réservé qui provoque un dossier vide pour le déploiement.

## <a name="configuring-deployment-settings-in-the-project-properties-window"></a>Configuration des paramètres de déploiement dans la fenêtre de propriétés de projet

La plupart des paramètres qui affectent ce qui se passe lors du déploiement sont inclus dans le profil de publication, comme vous le verrez dans les didacticiels suivants. Certains paramètres que vous devez être conscient de se trouvent dans le **Package/Publication** onglets de la **propriétés du projet** fenêtre. Ces paramètres sont spécifiés pour chaque configuration de build, autrement dit, vous pouvez avoir des paramètres différents pour une version Release que vous disposez d’une version Debug.

Dans **l’Explorateur de solutions**, avec le bouton droit le **ContosoUniversity** projet, sélectionnez **propriétés**, puis sélectionnez le **Package/Publication Web**onglet.

![Package_Publish_Web_tab](deployment-to-a-hosting-provider-configuring-project-properties-4-of-12/_static/image1.png)

Lorsque la fenêtre s’affiche, la valeur par défaut indiquant les paramètres pour quelle que soit la configuration de build n’est actuellement active pour la solution. Si le **Configuration** zone n’indique pas **Active (Release)**, sélectionnez **version** afin d’afficher les paramètres de la configuration de build Release. Vous allez déployer les versions Release pour les environnements de test et de production.

![Package_Publish_Web_tab_selecting_Release](deployment-to-a-hosting-provider-configuring-project-properties-4-of-12/_static/image2.png)

Avec **Active (Release)** ou **version** sélectionné, vous voyez les valeurs qui sont efficaces lorsque vous déployez à l’aide de la configuration de build Release :

- Dans le **éléments à déployer** boîte, **uniquement les fichiers nécessaires pour exécuter l’application** est sélectionnée. Autres options sont **tous les fichiers dans ce projet** ou **tous les fichiers dans ce dossier de projet**. En laissant la sélection par défaut vous éviterez de déployer les fichiers de code source, par exemple. Ce paramètre est la raison pour laquelle pourquoi les dossiers qui contiennent les fichiers binaires SQL Server Compact devaient être inclus dans le projet. Pour plus d’informations sur ce paramètre, consultez **pourquoi ne pas tous les fichiers dans le dossier du projet sont déployés ?** dans [Forum aux questions du déploiement de projet d’Application ASP.NET Web](https://msdn.microsoft.com/en-us/library/ee942158.aspx).
- **Les symboles de débogage exclure généré** est sélectionnée. Vous ne le débogage lorsque vous utilisez cette configuration de build.
- **Exclure des fichiers de l’application\_dossier de données** n’est pas sélectionnée. Votre fichier de SQL Server Compact pour la base de données d’appartenance est dans ce dossier et vous devez le déployer. Lorsque vous déployez des mises à jour qui n’incluent pas les modifications de la base de données, vous devez sélectionner cette case à cocher.
- **Précompiler cette application avant la publication** n’est pas sélectionnée. Dans la plupart des scénarios, il est inutile de précompiler des projets d’application web. Pour plus d’informations sur cette option, consultez [onglet Package/Publication Web, les propriétés du projet](https://msdn.microsoft.com/en-us/library/dd410108(v=vs.110).aspx) et [avancé précompiler boîte de dialogue Paramètres](https://msdn.microsoft.com/en-us/library/hh475319(v=vs.110).aspx).
- **Inclure toutes les bases de données configurées sous l’onglet Package/Publication SQL** est sélectionnée, mais cette option n’a aucun effet maintenant, car vous n’êtes pas en train de configurer le **Package/Publication SQL** onglet. Cet onglet est pour une méthode de déploiement de base de données héritées qui permet d’être la seule option pour le déploiement de bases de données SQL Server. Vous allez utiliser le **Package/Publication SQL** onglet dans le [migration vers SQL Server](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12.md) didacticiel.
- Le **paramètres de Package de déploiement Web** section ne s’applique pas parce que vous utilisez un seul clic publier dans ces didacticiels.

Modifier la **Configuration** zone déroulante de débogage pour afficher les paramètres par défaut pour les versions Debug. Les valeurs sont identiques, sauf **exclure les symboles de débogage générés** est désactivée afin que vous puissiez déboguer lorsque vous déployez une build de débogage.

## <a name="making-sure-that-the-elmah-folder-gets-deployed"></a>Vérification que le dossier Elmah est déployé.

Comme vous l’avez vu dans le didacticiel précédent, le [package Elmah NuGet](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx) fournit les fonctionnalités pour la journalisation des erreurs et création de rapports. Dans l’application Contoso University Elmah a été configuré pour stocker les détails de l’erreur dans un dossier nommé *Elmah*:

![ELMAH dossier](deployment-to-a-hosting-provider-configuring-project-properties-4-of-12/_static/image3.png)

Exclusion de fichiers ou dossiers spécifiques du déploiement est une exigence courante ; un autre exemple consisterait à un dossier que les utilisateurs peuvent télécharger des fichiers. Vous ne souhaitez pas que les fichiers journaux ou téléchargé les fichiers qui ont été créés dans votre environnement de développement pour être déployée en production. Et si vous déployez une mise à jour en production, que vous ne souhaitez pas le processus de déploiement pour supprimer des fichiers qui existent dans la production. (Selon la façon dont vous définissez une option de déploiement, si un fichier existe dans le site de destination, mais pas du site source lorsque vous déployez, Web Deploy supprime de la destination.)

Comme vous l’avez vu plus haut dans ce didacticiel, les **éléments à déployer** option dans le **Package/Publication Web** onglet est définie sur **uniquement les fichiers nécessaires pour exécuter cette application**. Par conséquent, les fichiers journaux qui sont créés par Elmah dans le développement ne seront pas déployés, qui est ce que vous voulez. (Pour être déployé, ils doivent être inclus dans le projet et leurs **Action de génération** propriété devrait être défini sur **contenu**. Pour plus d’informations, consultez **pourquoi ne pas tous les fichiers dans le dossier du projet sont déployés ?** dans [Forum aux questions du déploiement de projet d’Application ASP.NET Web](https://msdn.microsoft.com/en-us/library/ee942158.aspx)). Toutefois, Web Deploy ne crée pas un dossier dans le site de destination sauf s’il existe au moins un fichier à copier à celui-ci. Par conséquent, vous allez ajouter un *.txt* fichier dans le dossier d’agir comme un espace réservé, afin que le dossier sera copié.

Dans **l’Explorateur de solutions**, cliquez sur le *Elmah* dossier, sélectionnez **ajouter un nouvel élément**et créez un fichier nommé *substitution.txt*. Placer le texte suivant : « Il s’agit d’un fichier d’espace réservé pour vous assurer que le dossier de déploiement. » Et enregistrez le fichier. C’est qu’il vous suffit pour faire en sorte que Visual Studio déploie ce fichier et le dossier, car le **Action de génération** propriété du *.txt* fichiers est définie sur **decontenu**par défaut.

Vous avez maintenant terminé toutes les tâches de configuration de déploiement. Dans l’étape suivante du didacticiel, vous allez déployer le site de l’Université de Contoso à l’environnement de test et testez-le.

>[!div class="step-by-step"]
[Précédent](deployment-to-a-hosting-provider-web-config-file-transformations-3-of-12.md)
[Suivant](deployment-to-a-hosting-provider-deploying-to-iis-as-a-test-environment-5-of-12.md)
