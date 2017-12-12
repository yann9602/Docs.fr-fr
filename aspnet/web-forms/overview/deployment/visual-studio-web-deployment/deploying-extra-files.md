---
uid: web-forms/overview/deployment/visual-studio-web-deployment/deploying-extra-files
title: "Déploiement de Web ASP.NET à l’aide de Visual Studio : déploiement de fichiers supplémentaires | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, en utilisant des éléments..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/23/2015
ms.topic: article
ms.assetid: 1cd91055-84bc-42c6-9d80-646f41429d4d
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/deploying-extra-files
msc.type: authoredcontent
ms.openlocfilehash: a34607b25f6cf502f5fbf2fe51bf1937f470159e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-web-deployment-using-visual-studio-deploying-extra-files"></a>Déploiement de Web ASP.NET à l’aide de Visual Studio : déploiement de fichiers supplémentaires
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet de démarrage](http://go.microsoft.com/fwlink/p/?LinkId=282627)

> Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, à l’aide de Visual Studio 2012 ou Visual Studio 2010. Pour plus d’informations sur la série, consultez [le premier didacticiel de la série](introduction.md).


## <a name="overview"></a>Vue d'ensemble

Ce didacticiel montre comment étendre Visual Studio web publier pipeline pour effectuer une tâche supplémentaire au cours du déploiement. La tâche consiste à copier des fichiers supplémentaires qui ne sont pas dans le dossier de projet pour le site de destination.

Pour ce didacticiel, vous allez copier un fichier supplémentaire : *robots.txt*. Vous souhaitez déployer ce fichier dans l’environnement intermédiaire, mais pas en production. Dans [le déploiement en Production](deploying-to-production.md) (didacticiel), vous ajouté ce fichier au projet et configuré la Production profil pour l’exclure de la publication. Dans ce didacticiel, vous verrez une autre méthode pour gérer cette situation, qui peuvent être utiles pour tous les fichiers que vous souhaitez déployer, mais ne souhaitez pas inclure dans le projet.

## <a name="move-the-robotstxt-file"></a>Déplacez le fichier robots.txt

Pour préparer une autre méthode de gestion des *robots.txt*, dans cette section du didacticiel, vous déplacez le fichier vers un dossier qui n’est pas inclus dans le projet, et vous supprimez *robots.txt* à partir de la mise en lots environnement. Il est nécessaire de supprimer le fichier de mise en lots afin que vous pouvez vérifier que votre nouvelle méthode de déploiement du fichier à cet environnement fonctionne correctement.

1. Dans **l’Explorateur de solutions**, avec le bouton droit le *robots.txt* de fichier et cliquez sur **exclure du projet**.
2. À l’aide de l’Explorateur de fichiers Windows, créez un nouveau dossier dans le dossier de solution et nommez-le *ExtraFiles*.
3. Déplacer le *robots.txt* de fichiers à partir de la *ContosoUniversity* dossier de projet pour le *ExtraFiles* dossier.

    ![Dossier de ExtraFiles](deploying-extra-files/_static/image1.png)
4. À l’aide de votre outil FTP, supprimez le *robots.txt* fichier à partir du site web intermédiaire.

    En guise d’alternative, vous pouvez sélectionner **supprimer les fichiers supplémentaires à la destination** sous **Options de publication du fichier** sur la **paramètres** onglet du profil de publication mise en lots, et republier dans l’environnement intermédiaire.

## <a name="update-the-publish-profile-file"></a>Mettre à jour le fichier de profil de publication

Vous ne devez *robots.txt* la zone de transit, donc le profil de publication uniquement, vous devez mettre à jour afin du pour déployer est mis en œuvre.

1. Dans Visual Studio, ouvrez *Staging.pubxml*.
2. À la fin du fichier, avant la fermeture `</Project>` , ajoutez le balisage suivant :

    [!code-xml[Main](deploying-extra-files/samples/sample1.xml)]

    Ce code crée un nouveau *cible* qui collectera les fichiers supplémentaires à déployer. Une cible est composée d’un ou plusieurs tâches MSBuild s’exécute en fonction des conditions que vous spécifiez.

    Le `Include` attribut spécifie que le dossier dans lequel rechercher les fichiers *ExtraFiles*, situé dans le même niveau que le dossier du projet. MSBuild collecte tous les fichiers à partir de ce dossier et un à partir de tous les sous-dossiers (l’astérisque double spécifie récursive sous-dossiers) de manière récursive. Avec ce code, vous pouvez placer plusieurs fichiers et les fichiers dans les sous-dossiers contenus dans le *ExtraFiles* dossier et tout sera déployé.

    Le `DestinationRelativePath` élément spécifie que les dossiers et les fichiers doivent être copiés dans le dossier racine du site web de destination, dans la même structure de fichiers et de dossiers comme ils sont trouvent dans le *ExtraFiles* dossier. Si vous souhaitez copier le *ExtraFiles* dossier lui-même, le `DestinationRelativePath` valeur serait *ExtraFiles\%(RecursiveDir)%(Filename)%(Extension)*.
3. À la fin du fichier, avant la fermeture `</Project>` , ajoutez le balisage suivant qui indique le moment d’exécuter la nouvelle cible.

    [!code-xml[Main](deploying-extra-files/samples/sample2.xml)]

    Ce code génère la nouvelle `CustomCollectFiles` cible doit être exécutée chaque fois que la cible de copie des fichiers dans le dossier de destination est exécutée. Il existe une cible distincte pour publication et la création de package de déploiement et la nouvelle cible est injectée dans les deux cibles au cas où vous décidez de déployer à l’aide d’un package de déploiement au lieu de la publication.

    Le *.pubxml* fichier ressemble maintenant à l’exemple suivant :

    [!code-xml[Main](deploying-extra-files/samples/sample3.xml?highlight=53-71)]
4. Enregistrez et fermez le *Staging.pubxml* fichier.

## <a name="publish-to-staging"></a>Publier dans l’environnement intermédiaire

À l’aide d’un seul clic publier ou de la ligne de commande, publier l’application à l’aide du profil de mise en lots.

Si vous utilisez un seul clic publier, vous pouvez vérifier dans le **aperçu** fenêtre qui *robots.txt* seront copiés. Sinon, utilisez l’outil FTP pour vérifier que le *robots.txt* fichier se trouve dans le dossier racine du site web après le déploiement.

## <a name="summary"></a>Résumé

Cette étape termine cette série de didacticiels sur le déploiement d’une application de web ASP.NET sur un fournisseur d’hébergement tiers. Pour plus d’informations sur les sujets abordés dans ces didacticiels, consultez le [ASP.NET Deployment Content Map](https://go.microsoft.com/fwlink/p/?LinkId=282413).

## <a name="more-information"></a>Complément d'information

Si vous savez comment travailler avec des fichiers de MSBuild, vous pouvez automatiser de nombreuses autres tâches de déploiement en écrivant du code *.pubxml* les fichiers (pour des tâches spécifiques à profil) ou le projet *. wpp.targets* fichier (pour vos tâches s’applique à tous les profils). Pour plus d’informations sur *.pubxml* et *. wpp.targets* de fichiers, consultez [Comment : modifier les paramètres de déploiement dans les fichiers de profil de publication (.pubxml) et le. wpp.targets fichier dans Visual Studio Web Projets](https://msdn.microsoft.com/en-us/library/ff398069). Pour une introduction au code de MSBuild, consultez **la structure d’un fichier projet** dans [série de déploiement d’entreprise : présentation du fichier de projet](../web-deployment-in-the-enterprise/understanding-the-project-file.md). Pour savoir comment travailler avec des fichiers de MSBuild pour effectuer des tâches pour vos propres scénarios, consultez cet ouvrage : [à l’intérieur de la Microsoft Build Engine : à l’aide de MSBuild et Team Foundation Build](http://msbuildbook.com) par Sayed Ibraham Hashimi et William Bartholomew.

## <a name="acknowledgements"></a>Remerciements

J’aimerais remercier les personnes suivantes qui a effectué des contributions significatives pour le contenu de cette série de didacticiels :

- [Blog d’Alberto Poblacion, MVP &amp; MCT, Espagne](https://mvp.microsoft.com/en-us/mvp/Alberto%20Poblacion%20Bolano-36772)
- Jarod Ferguson, MVP de développement de plate-forme de données, États-Unis
- Sévères Mittal, Microsoft
- [Jon Galloway](https://weblogs.asp.net/jgalloway) (twitter : [ @jongalloway ](http://twitter.com/jongalloway))
- [Kristina Olson, Microsoft](https://blogs.iis.net/krolson/default.aspx)
- [Protocole Mike, Microsoft](http://www.mikepope.com/blog/DisplayBlog.aspx)
- Mohit Srivastava, Microsoft
- [Raffaele Rialdi, Italie](http://www.iamraf.net/)
- [Rick Anderson, Microsoft](https://blogs.msdn.com/b/rickandy/)
- [Sayed Hashimi, Microsoft](http://sedodream.com/default.aspx)(twitter : [ @sayedihashimi ](http://twitter.com/sayedihashimi))
- [Scott Hanselman](http://www.hanselman.com/blog/) (twitter : [ @shanselman ](http://twitter.com/shanselman))
- [Scott Hunter, Microsoft](https://blogs.msdn.com/b/scothu/) (twitter : [ @coolcsh ](http://twitter.com/coolcsh))
- [Srđan Božović, Serbie](http://msforge.net/blogs/zmajcek/)
- [Vishal Joshi, Microsoft](http://vishaljoshi.blogspot.com/) (twitter : [ @vishalrjoshi ](http://twitter.com/vishalrjoshi))

>[!div class="step-by-step"]
[Précédent](command-line-deployment.md)
[Suivant](troubleshooting.md)
