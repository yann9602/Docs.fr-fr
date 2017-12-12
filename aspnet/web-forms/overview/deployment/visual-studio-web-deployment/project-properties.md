---
uid: web-forms/overview/deployment/visual-studio-web-deployment/project-properties
title: "Déploiement de Web ASP.NET à l’aide de Visual Studio : propriétés du projet | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, en utilisant des éléments..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/15/2013
ms.topic: article
ms.assetid: ec1cec4c-a75f-47af-a2ba-b1e2f971d24b
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/project-properties
msc.type: authoredcontent
ms.openlocfilehash: 68a1892dcf8055d8cc898f471a96d86e8abb64de
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-web-deployment-using-visual-studio-project-properties"></a>Déploiement de Web ASP.NET à l’aide de Visual Studio : propriétés du projet
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet de démarrage](http://go.microsoft.com/fwlink/p/?LinkId=282627)

> Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, à l’aide de Visual Studio 2012 ou Visual Studio 2010. Pour plus d’informations sur la série, consultez [le premier didacticiel de la série](introduction.md).


## <a name="overview"></a>Vue d'ensemble

Certaines options de déploiement sont configurées dans les propriétés de projet qui sont stockées dans le fichier projet (la *.csproj* ou *.vbproj* fichier). Dans la plupart des cas, les valeurs par défaut de ces paramètres sont ce que vous le souhaitez, mais vous pouvez utiliser la **propriétés du projet** UI intégrées à Visual Studio pour travailler avec ces paramètres si vous devez les modifier. Dans ce didacticiel, vous passez en revue les paramètres de déploiement dans **propriétés du projet**. Vous également créez un fichier d’espace réservé qui provoque un dossier vide pour le déploiement.

## <a name="configure-deployment-settings-in-the-project-properties-window"></a>Configurer les paramètres de déploiement dans la fenêtre de propriétés de projet

La plupart des paramètres qui affectent ce qui se passe lors du déploiement sont inclus dans le profil de publication, comme vous le verrez dans les didacticiels suivants. Certains paramètres que vous devez être conscient de se trouvent dans le **Package/Publication** onglets de la **propriétés du projet** fenêtre. Ces paramètres sont spécifiés pour chaque configuration de build, autrement dit, vous pouvez avoir des paramètres différents pour une version Release que vous disposez d’une version Debug.

Dans **l’Explorateur de solutions**, avec le bouton droit le **ContosoUniversity** projet, sélectionnez **propriétés**, puis sélectionnez le **Package/Publication Web**onglet.

![Onglet Package/Publication web](project-properties/_static/image1.png)

Lorsque la fenêtre s’affiche, la valeur par défaut indiquant les paramètres pour quelle que soit la configuration de build n’est actuellement active pour la solution. Si le **Configuration** zone n’indique pas **Active (Release)**, sélectionnez **version** afin d’afficher les paramètres de la configuration de build Release. Vous allez déployer les versions Release pour les environnements de test et de production.

![Sélection de configuration de build Release](project-properties/_static/image2.png)

Avec **Active (Release)** ou **version** sélectionné, vous voyez les valeurs qui sont efficaces lorsque vous déployez à l’aide de la configuration de build Release :

- Dans le **éléments à déployer** boîte, **uniquement les fichiers nécessaires pour exécuter l’application** est sélectionnée. Autres options sont **tous les fichiers dans ce projet** ou **tous les fichiers dans ce dossier de projet**. En laissant la sélection par défaut vous éviterez de déployer les fichiers de code source, par exemple. Ce paramètre est la raison pour laquelle pourquoi les dossiers qui contiennent les fichiers binaires SQL Server Compact devaient être inclus dans le projet. Pour plus d’informations sur ce paramètre, consultez **pourquoi ne pas tous les fichiers dans le dossier du projet sont déployés ?** dans [Forum aux questions du déploiement de projet d’Application ASP.NET Web](https://msdn.microsoft.com/en-us/library/ee942158.aspx).
- **Les symboles de débogage exclure généré** est sélectionnée. Vous ne le débogage lorsque vous utilisez cette configuration de build.
- **Inclure toutes les bases de données configurées sous l’onglet Package/Publication SQL** est sélectionnée. Spécifie si Visual Studio déployer des bases de données, ainsi que des fichiers. Bien que la case à cocher étiquette mentionne uniquement la **Package/Publication SQL** onglet, décocher cette case est également désactiver le déploiement de base de données qui est configuré dans le profil de publication. Vous allez effectuer que plus tard, afin de la case à cocher doit rester sélectionnée. Le **Package/Publication SQL** est utilisé pour une méthode que vous n’utiliserez pas dans ces didacticiels de publication de base de données héritée.
- Le **paramètres de Package de déploiement Web** section ne s’applique pas parce que vous utilisez un seul clic publier dans ces didacticiels.

Modifier la **Configuration** zone déroulante de débogage pour afficher les paramètres par défaut pour les versions Debug. Les valeurs sont identiques, sauf **exclure les symboles de débogage générés** est désactivée afin que vous puissiez déboguer lorsque vous déployez une build de débogage.

## <a name="make-sure-that-the-elmah-folder-gets-deployed"></a>Assurez-vous que le dossier Elmah est déployé.

Comme vous l’avez vu dans le didacticiel précédent, le [package Elmah NuGet](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx) fournit les fonctionnalités pour la journalisation des erreurs et création de rapports. Dans l’application Contoso University Elmah a été configuré pour stocker les détails de l’erreur dans un dossier nommé *Elmah*:

![ELMAH dossier](project-properties/_static/image3.png)

Exclusion de fichiers ou dossiers spécifiques du déploiement est une exigence courante ; un autre exemple consisterait à un dossier que les utilisateurs peuvent télécharger des fichiers. Vous ne souhaitez pas que les fichiers journaux ou téléchargé les fichiers qui ont été créés dans votre environnement de développement pour être déployée en production. Et si vous déployez une mise à jour en production, que vous ne souhaitez pas le processus de déploiement pour supprimer des fichiers qui existent dans la production. (Selon la façon dont vous définissez une option de déploiement, si un fichier existe dans le site de destination, mais pas du site source lorsque vous déployez, Web Deploy supprime de la destination.)

Comme vous l’avez vu plus haut dans ce didacticiel, les **éléments à déployer** option dans le **Package/Publication Web** onglet est définie sur **uniquement les fichiers nécessaires pour exécuter cette application**. Par conséquent, les fichiers journaux qui sont créés par Elmah dans le développement ne seront pas déployés, qui est ce que vous voulez. (Pour être déployé, ils doivent être inclus dans le projet et leurs **Action de génération** propriété devrait être défini sur **contenu**. Pour plus d’informations, consultez **pourquoi ne pas tous les fichiers dans le dossier du projet sont déployés ?** dans [Forum aux questions du déploiement de projet d’Application ASP.NET Web](https://msdn.microsoft.com/en-us/library/ee942158.aspx)). Toutefois, Web Deploy ne crée pas un dossier dans le site de destination sauf s’il existe au moins un fichier à copier à celui-ci. Par conséquent, vous allez ajouter un *.txt* fichier dans le dossier d’agir comme un espace réservé, afin que le dossier sera copié.

Dans **l’Explorateur de solutions**, cliquez sur le *Elmah* dossier, sélectionnez **ajouter un nouvel élément**et créer un fichier texte nommé *substitution.txt*. Placer le texte suivant : « Il s’agit d’un fichier d’espace réservé pour vous assurer que le dossier de déploiement. » Et enregistrez le fichier. C’est qu’il vous suffit pour faire en sorte que Visual Studio déploie ce fichier et le dossier, car le **Action de génération** propriété du *.txt* fichiers est définie sur **decontenu**par défaut.

## <a name="summary"></a>Résumé

Vous avez maintenant terminé toutes les tâches de configuration de déploiement. Dans l’étape suivante du didacticiel, vous allez déployer le site de l’Université de Contoso à l’environnement de test et testez-le.

>[!div class="step-by-step"]
[Précédent](web-config-transformations.md)
[Suivant](deploying-to-iis.md)
