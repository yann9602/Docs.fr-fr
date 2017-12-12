---
uid: aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
title: "Contrôle de code (génération d’applications Cloud du monde réel avec Azure) source | Documents Microsoft"
author: MikeWasson
description: "Les applications du Cloud monde réel construction avec Azure livres est basée sur une présentation développée par Scott Guthrie. Il explique 13 des modèles et des meilleures pratiques qui peuvent il..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/23/2015
ms.topic: article
ms.assetid: 2a0370d3-c2fb-4bf3-88b8-aad5a736c793
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
msc.type: authoredcontent
ms.openlocfilehash: f244e6bd1cd8abd23b64d07ccafcef5c4db1029b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="source-control-building-real-world-cloud-apps-with-azure"></a>Contrôle de code source (génération d’applications Cloud du monde réel avec Azure)
====================
par [Mike Wasson](https://github.com/MikeWasson), [Rick Anderson](https://github.com/Rick-Anderson), [Tom Dykstra](https://github.com/tdykstra)

[Téléchargement corriger projet](http://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4) ou [télécharger des livres](http://blogs.msdn.com/b/microsoft_press/archive/2014/07/23/free-ebook-building-cloud-apps-with-microsoft-azure.aspx)

> Le **construction réelle applications Cloud du monde avec Azure** livres est basé sur une présentation développée par Scott Guthrie. Il explique 13 modèles et pratiques qui peuvent vous aider à réussir le développement d’applications web pour le cloud. Pour plus d’informations sur le livre électronique, consultez [le premier chapitre](introduction.md).


Contrôle de code source est essentiel pour tous les projets de développement cloud, pas seulement les environnements d’équipe. Vous ne pensez de la modification de code source, ou même un document Word sans une fonction d’annulation et de sauvegardes automatiques et de contrôle de code source vous donne ces fonctions au niveau du projet où ils peuvent gagner du temps quand une erreur survient. Avec les services de contrôle de source de cloud, vous n’avez plus à vous soucier de configuration complexe, et vous pouvez utiliser gratuitement jusqu'à 5 utilisateurs de contrôle de code source Visual Studio Online.

La première partie de ce chapitre explique les trois meilleures pratiques clés à prendre en compte :

- [Traiter des scripts d’automatisation en tant que code source](#scripts) et la version les ainsi que votre code d’application.
- [Ne jamais rechercher dans les clés secrètes](#secrets) (des données sensibles telles que des informations d’identification) dans un référentiel de code source.
- [Configurer les branches source](#devops) pour activer le flux de travail DevOps.

Le reste de ce chapitre fournit des exemples d’implémentation de ces modèles dans Visual Studio, Azure et Visual Studio Online :

- [Ajouter des scripts dans le contrôle de code source dans Visual Studio](#vsscripts)
- [Stocker des données sensibles dans Azure](#appsettings)
- [Utiliser Git dans Visual Studio et Visual Studio Online](#gittfs)

<a id="scripts"></a>
## <a name="treat-automation-scripts-as-source-code"></a>Scripts d’automatisation de traiter en tant que code source

Lorsque vous travaillez sur un projet cloud que vous modifiez fréquemment les choses, et vous voulez être en mesure de réagir rapidement aux problèmes signalés par vos clients. Réagir rapidement implique l’utilisation de scripts d’automatisation, comme expliqué dans la [automatiser tout](automate-everything.md) chapitre. Tous les scripts que vous utilisez pour créer votre environnement, déployer sur celle-ci, l’échelle, etc., doivent être synchronisés avec votre code source de l’application.

Pour conserver la synchronisation avec le code de scripts, les stocker dans votre système de contrôle de code source. Si vous devez annuler les modifications apportées au code de production qui est différent du code de développement ou un correctif rapide, il est inutile de perdre le temps à tenter de localiser les paramètres ont été modifiés ou les membres de l’équipe ont des copies de la version que vous avez besoin. Vous êtes assuré que les scripts que vous avez besoin sont synchronisés avec la base de code que vous avez besoin pour, vous êtes assuré que tous les membres de l’équipe fonctionnent avec les mêmes scripts. Si vous avez besoin automatiser les tests et le déploiement d’un correctif à chaud pour la production ou de développement des nouvelles fonctionnalités, vous aurez le script approprié pour le code qui doit être mis à jour.

<a id="secrets"></a>
## <a name="dont-check-in-secrets"></a>Ne pas vérifier dans les clés secrètes

Un référentiel de code source est généralement accessible aux personnes trop nombreux pour qu’il soit un emplacement sécurisé de manière appropriée pour les données sensibles telles que les mots de passe. Si les scripts s’appuient sur les secrets tels que les mots de passe, paramétrer ces paramètres afin qu’ils ne sont enregistrés dans le code source et stocker vos clés secrètes vers un autre emplacement.

Par exemple, Azure vous permet de que télécharger des fichiers qui contiennent des paramètres de publication afin d’automatiser la création de profils de publication. Ces fichiers incluent les noms d’utilisateur et mots de passe qui sont autorisés à gérer vos services Azure. Si vous utilisez cette méthode pour créer des profils de publication, et si vous archivez ces fichiers au contrôle de code source, toute personne ayant accès à votre référentiel peut voir les noms d’utilisateur et les mots de passe. Vous pouvez stocker en toute sécurité le mot de passe dans le profil de la publication, car elle est chiffrée et se trouve dans un *. pubxml.user* fichier par défaut n’est pas inclus dans le contrôle de code source.

<a id="devops"></a>
## <a name="structure-source-branches-to-facilitate-devops-workflow"></a>Branches de code source de structure pour faciliter le flux de travail DevOps

Comment implémenter des branches dans votre référentiel affecte votre capacité à développer de nouvelles fonctionnalités et résoudre les problèmes en production. Voici un modèle que beaucoup de moyenne taille équipes utilisation :

![Structure de la branche source](source-control/_static/image1.png)

La branche principale correspond toujours au code de production. Branches sous forme de base correspondent aux différentes étapes dans le cycle de vie de développement. La branche développement est où vous implémentez de nouvelles fonctionnalités. Pour une petite équipe peut uniquement avoir principale et développement, mais nous vous recommandons souvent de personnes une branche intermédiaire entre le développement et master. Vous pouvez utiliser la mise en lots pour les tests d’intégration finale avant une mise à jour est déplacé vers la production.

Pour les grandes équipes peuvent être les branches distinctes pour chaque nouvelle fonctionnalité ; pour une plus petite équipe, vous pouvez avoir tout le monde archiver à la branche de développement.

Si vous avez une branche pour chaque fonctionnalité, lors de la fonctionnalité A est prêt fusion vous ses modifications du code source dans le développement de créer des branches et vers le bas dans les autres branches de fonctionnalité. Ce code source des processus de fusion peut prendre du temps, et pour éviter ce travail tout en conservant les fonctionnalités distincts, certaines équipes implémentent une alternative appelée  *[Active ou désactive des fonctionnalités](http://en.wikipedia.org/wiki/Feature_toggle)*  (également appelée *fonctionnalité indicateurs*). Cela signifie que tout le code de toutes les fonctionnalités est dans la même branche, mais vous activer ou désactiver chaque fonctionnalité en utilisant des commutateurs dans le code. Par exemple, supposons que la fonctionnalité A est un nouveau champ pour les tâches d’application corriger et de fonctionnalité B ajoute des fonctionnalités de mise en cache. Le code pour les deux fonctionnalités peut être dans la branche de développement, mais l’affichage seul application le nouveau champ quand une variable est définie sur true et il utilise uniquement la mise en cache lorsqu’une variable différente est définie sur true. Si la fonctionnalité A n’est pas prête à être promu, mais la fonctionnalité B est prête, vous pouvez promouvoir le code en Production avec le commutateur de fonctionnalité A désactivé et activer la fonctionnalité B. Vous pouvez terminer la fonctionnalité A et promouvoir une version ultérieure, toutes avec aucune fusion de code source.

Si vous utilisez des branches ou Active/désactive des fonctionnalités, ou non une structure de branches, comme cela vous permet de flux de votre code à partir de développement vers la production d’une manière agile et reproductible.

Cette structure vous permet également de réagir rapidement aux commentaires des clients. Si vous avez besoin effectuer une correction rapide en production, vous pouvez également le faire efficacement dans une solution agile. Vous pouvez créer une branche hors principale ou de transit et lorsqu’il sera prêt fusionner des principales d’et vers le bas en branches de développement et de fonctionnalité.

![Branche de correctif logiciel](source-control/_static/image2.png)

Sans une structure de branches à ceci avec la séparation de branches de développement et de production, un problème de production est susceptible de vous à la position de devoir à promouvoir le nouveau code de fonction, ainsi que votre correctif de production. Le nouveau code de fonctionnalité n’est peut-être pas entièrement testée et prête pour production, et vous devrez peut-être effectuer beaucoup de travail de sauvegarde des modifications qui ne sont pas prêtes. Ou bien, vous devrez peut-être retarder votre correctif afin de tester les modifications et de les préparer à déployer.

Vous verrez ensuite des exemples montrant comment implémenter ces trois modèles dans Visual Studio, Azure et Visual Studio Online. Voici des exemples plutôt que des instructions détaillées de comment-à--informatique ; Pour obtenir des instructions détaillées qui procurent toutes les le contexte nécessaire, consultez la [ressources](#resources) section à la fin de ce chapitre.

<a id="vsscripts"></a>
## <a name="add-scripts-to-source-control-in-visual-studio"></a>Ajouter des scripts dans le contrôle de code source dans Visual Studio

Vous pouvez ajouter des scripts pour le contrôle de code source dans Visual Studio en les incluant dans un dossier de solution Visual Studio (en supposant que votre projet est sous contrôle de code source). Voici une façon d’y parvenir.

Créez un dossier pour les scripts dans votre dossier de solution (le même dossier que celui qui a votre *.sln* fichier).

![Dossier d’Automation](source-control/_static/image3.png)

Copiez les fichiers de script dans le dossier.

![Contenu du dossier Automation](source-control/_static/image4.png)

Dans Visual Studio, ajoutez un dossier de solution pour le projet.

![Sélection de menu Nouveau dossier de Solution](source-control/_static/image5.png)

Et ajoutez les fichiers de script dans le dossier de solution.

![Ajouter la sélection de menu d’un élément existant](source-control/_static/image6.png)

![Boîte de dialogue Ajouter un élément existant](source-control/_static/image7.png)

Les fichiers de script sont désormais incluses dans votre projet et le contrôle de code source effectue le suivi des modifications de la version, ainsi que les modifications de code source correspondantes.

<a id="appsettings"></a>
## <a name="store-sensitive-data-in-azure"></a>Stocker des données sensibles dans Azure

Si vous exécutez votre application dans un Site Web Azure, un pour éviter de stocker des informations d’identification dans le contrôle de code source consiste à les stocker dans Azure.

Par exemple, l’application corriger stocke dans son fichier Web.config fichier deux chaînes de connexion qui ont des mots de passe en production et une clé qui donne accès à votre compte de stockage Azure.

[!code-xml[Main](source-control/samples/sample1.xml?highlight=2-3,11)]

Si vous placez les valeurs réelles de production de ces paramètres dans votre *Web.config* fichier, ou si vous les placez dans le *Web.Release.config* fichier pour configurer une transformation Web.config pour les insérer au cours du déploiement ils allez stockées dans le référentiel de code source. Si vous entrez les chaînes de connexion de base de données dans la production profil de publication, le mot de passe est à votre *.pubxml* fichier. (Vous pouvez exclure les *.pubxml* de fichiers à partir du contrôle de code source, mais vous perdez l’avantage de partage de tous les autres paramètres de déploiement.)

Azure vous offre une alternative pour la **appSettings** et chaînes de connexion sections de la *Web.config* fichier. Voici la partie appropriée de la **Configuration** onglet d’un site web du portail de gestion Azure :

![appSettings et connectionStrings dans le portail](source-control/_static/image8.png)

Lorsque vous déployez un projet pour ce site web et l’application s’exécute, les valeurs que vous avez stocké dans Azure remplacent toutes les valeurs sont dans le fichier Web.config.

Vous pouvez définir ces valeurs dans Azure à l’aide du portail de gestion ou de scripts. Le script d’automatisation de la création environnement vous l’avez vu dans les [automatiser tout](automate-everything.md) chapitre crée une base de données SQL Azure, obtient le stockage et les chaînes de connexion de base de données SQL et stocke ces clés secrètes dans les paramètres de votre site web.

[!code-powershell[Main](source-control/samples/sample2.ps1)]

[!code-powershell[Main](source-control/samples/sample3.ps1)]

Notez que les scripts sont paramétrables afin que les valeurs réelles ne rendues persistantes dans le référentiel source.

Lorsque vous exécutez localement dans votre environnement de développement, l’application lit votre fichier Web.config local et votre connexion à une base de données de base de données locale SQL Server dans des points de chaîne le *application\_données* dossier de votre projet web. Lorsque vous exécutez l’application dans Azure et l’application tente de lire ces valeurs à partir du fichier Web.config, il récupère et utilise sont les valeurs stockées pour le Site Web, pas ce qui est réellement dans le fichier Web.config.

<a id="gittfs"></a>
## <a name="use-git-in-visual-studio-and-visual-studio-online"></a>Utiliser Git dans Visual Studio et Visual Studio Online

Vous pouvez utiliser n’importe quel environnement de contrôle de code source pour implémenter la structure de branches DevOps présentée précédemment. Pour les équipes multisites un [système de contrôle de version distribué](http://en.wikipedia.org/wiki/Distributed_revision_control) (gérer) fonctionnent mieux ; pour les autres équipes un [système centralisé](http://en.wikipedia.org/wiki/Revision_control) fonctionnent mieux.

[GIT](http://git-scm.com/) est un gérer est devenu très populaire. Lorsque vous utilisez Git pour le contrôle de code source, vous avez une copie complète du référentiel avec l’ensemble de son historique sur votre ordinateur local. De nombreuses personnes préfèrent que car il est plus facile pour poursuivre votre travail lorsque vous n’êtes pas connecté au réseau, vous pouvez continuer à effectuer des validations et restaurations, créer et changer de branches et ainsi de suite. Même si vous êtes connecté au réseau, il est plus facile et plus rapide de créer des branches et de changer de branches lorsque tout est local. Vous pouvez également effectuer des validations locales et les restaurations sans avoir un impact sur d’autres développeurs. Et vous pouvez traiter par lots validations avant de les envoyer au serveur.

[Microsoft Visual Studio Online](https://www.visualstudio.com/)(VSO), anciennement connu en tant que Team Foundation Service, propose les deux Git et [Team Foundation Version Control](https://msdn.microsoft.com/en-us/library/ms181237(v=vs.120).aspx) (TFVC ; centralisée de contrôle de code source). Ici chez Microsoft dans le groupe Azure certaines équipes utilisent centralisée contrôle de code source certains utilisent distribuée, et certains utilisent une combinaison (centralisée pour certains projets et distribuée pour d’autres projets). Le service Visual Studio Online est gratuit pour les utilisateurs jusqu'à 5. Vous pouvez vous inscrire pour un plan gratuit [ici](https://go.microsoft.com/fwlink/?LinkId=307137).

Visual Studio 2013 inclut intégrées de première classe [prise en charge Git](https://msdn.microsoft.com/en-us/library/hh850437.aspx); Voici une rapide démonstration de comment cela fonctionne.

Dans un projet ouvert dans Visual Studio 2013, cliquez sur la solution dans **l’Explorateur de solutions**, puis choisissez **ajouter la Solution au contrôle de code Source**.

![Ajouter la Solution au contrôle de code Source](source-control/_static/image9.png)

Visual Studio vous demande si vous souhaitez utiliser TFVC (contrôle de version centralisé) ou Git.

![Contrôle de code Source](source-control/_static/image10.png)

Lorsque vous sélectionnez Git et cliquez sur **OK**, Visual Studio crée un référentiel Git local dans votre dossier de solution. Le nouveau référentiel ne comporte aucun fichier Vous devez les ajouter à l’espace de stockage en effectuant une validation Git. Avec le bouton droit de la solution dans **l’Explorateur de solutions**, puis cliquez sur **valider**.

![Valider](source-control/_static/image11.png)

Visual Studio automatiquement prépare tous les fichiers de projet pour la validation et les répertorie dans **Team Explorer** dans les **modifications incluses** volet. (S’il existe certaines vous souhaitez inclure dans la validation, vous pouvez sélectionner, avec le bouton droit, puis cliquez sur **exclure**.)

![Team Explorer](source-control/_static/image12.png)

Entrez un commentaire de validation, cliquez sur **validation**, et Visual Studio exécute la validation et affiche le code de validation.

![Modifications de Team Explorer](source-control/_static/image13.png)

Maintenant si vous modifiez du code afin qu’il soit différent de ce qui est dans le référentiel, vous pouvez facilement afficher les différences. Avec le bouton d’un fichier que vous avez modifié, sélectionnez **comparer avec Unmodified**, et que vous obtenez un affichage de comparaison qui affiche vos modifications non validées.

![Comparer avec non modifié](source-control/_static/image14.png)

![Modifications d’affichage diff](source-control/_static/image15.png)

Vous pouvez facilement voir les modifications que vous apportez et les archivez.

Supposons que vous devez apporter une branche, vous pouvez le faire trop dans Visual Studio. Dans **Team Explorer**, cliquez sur **nouvelle branche**.

![Nouvelle branche de Team Explorer](source-control/_static/image16.png)

Entrez un nom de branche, cliquez sur **créer une branche**, et si vous avez sélectionné **branche de récupération**, Visual Studio extrait automatiquement la nouvelle branche.

![Nouvelle branche de Team Explorer](source-control/_static/image17.png)

Vous pouvez maintenant modifier les fichiers et les archiver dans cette branche. Et vous pouvez facilement basculer entre les branches et de Visual Studio automatiquement les fichiers à la branche que vous ont extrait les synchronisations. Dans cet exemple, la page web titre dans  *\_Layout.cshtml* a été remplacé par « Correctif 1 » dans les distributions de HotFix1 branche.

![Branche de distributions de Hotfix1](source-control/_static/image18.png)

Si vous basculez vers le contrôleur de créer une branche, le contenu de la  *\_Layout.cshtml* fichier revient automatiquement à ce qu’ils sont dans la branche principale.

![Branche principale](source-control/_static/image19.png)

Présente un exemple simple de la façon dont vous pouvez rapidement créer une branche et retourner dans les deux sens entre les branches. Cette fonctionnalité permet à un flux de travail hautement agile à l’aide de la structure de branche et des scripts d’automatisation présentés dans le [automatiser tout](automate-everything.md) chapitre. Par exemple, vous pouvez être vous travaillez dans la branche de développement, créer une branche de correctif logiciel sur master, basculer vers la nouvelle branche, apportez vos modifications il et les valider, puis basculez vers la branche de développement et continuer à ce que vous faisiez.

Ce que vous l’avez vu ici est la façon dont vous travaillez avec un référentiel Git local dans Visual Studio. Dans un environnement d’équipe vous généralement également transmettre des modifications à un référentiel commun. Les outils de Visual Studio vous permettent également pointer vers un référentiel Git distant. Vous pouvez utiliser GitHub.com à cette fin, ou vous pouvez utiliser [Git dans Visual Studio Online](https://msdn.microsoft.com/en-us/library/hh850437.aspx) intégré avec toutes les autres fonctionnalités Visual Studio Online telles que l’élément de travail et de suivi des bogues.

Cela n’est pas la seule façon, vous pouvez implémenter une stratégie de création de branche agile, bien sûr. Vous pouvez activer le même flux de travail agile à l’aide d’un référentiel de contrôle source centralisée.

## <a name="summary"></a>Résumé

Mesurer le succès de votre système de contrôle de source en fonction de la rapidité avec laquelle vous pouvez apporter une modification et obtenir dynamique d’une manière sûre et prévisible. Si vous avez pris peur apporter une modification, car vous devez effectuer une ou deux jours de tests manuels sur celui-ci, vous vous demandez peut-être ce que vous devez faire process-wise ou test-wise afin que vous pouvez effectuer cette modification en quelques minutes ou au pire n’est plus d’une heure. Une stratégie pour y parvenir consiste à implémenter l’intégration continue et livraison continue, que nous examinerons dans le [chapitre suivant](continuous-integration-and-continuous-delivery.md).

<a id="resources"></a>
## <a name="resources"></a>Ressources

Le [Visual Studio Online](https://www.visualstudio.com/) portail fournit des services de prise en charge et de documentation, et vous pouvez vous inscrire pour un compte. Si vous avez Visual Studio 2012 et que vous souhaitez utiliser Git, consultez [Visual Studio Tools pour Git](https://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c).

Pour plus d’informations sur TFVC (contrôle de version centralisé) et Git (contrôle de version distribuée), consultez les ressources suivantes :

- [Le système de contrôle de version dois-je utiliser : TFVC ou Git ?](https://msdn.microsoft.com/en-us/library/vstudio/ms181368.aspx#tfvc_or_git_summary) La documentation MSDN, inclut un tableau récapitulant les différences entre TFVC et Git.
- [Ainsi, j’aime Team Foundation Server et j’aime Git, mais qui est une meilleure ?](https://blogs.msdn.com/b/visualstudiouk/archive/2013/08/05/well-i-like-team-foundation-server-and-i-like-git-but-which-is-better.aspx) Comparaison de Git et TFVC.

Pour plus d’informations sur les stratégies de création de branche, consultez les ressources suivantes :

- [Création d’un Pipeline de mise en production avec Team Foundation Server 2012](https://msdn.microsoft.com/en-us/library/dn449957.aspx). Documentation Microsoft Patterns and Practices. Consultez le chapitre 6 pour une présentation des stratégies de branchement. La fonctionnalité préconise bascule sur les branches de fonctionnalité et si les branches pour les fonctionnalités sont utilisées, éducateurs gardant éphémères (heures ou jours au maximum).
- [Guide du contrôle de version](https://aka.ms/vsarsolutions). Guide pour les stratégies de branchement par le groupe ALM Rangers. Sous l’onglet téléchargements, consultez Strategies.pdf de création de branche.
- [Développement de logiciels avec fonctionnalité bascules](https://msdn.microsoft.com/en-us/magazine/dn683796.aspx). Article du MSDN Magazine.
- [Activer/désactiver des fonctionnalités](http://martinfowler.com/bliki/FeatureToggle.html). Introduction à la fonctionnalité Bascule / fonctionnalité flags sur le blog de Fowler.
- [Active/désactive vs Branches de la fonctionnalité de fonctionnalité](http://geekswithblogs.net/Optikal/archive/2013/02/10/152069.aspx). Une autre entrée de blog sur la fonctionnalité Active/désactive, par Dylan Smith.

Pour plus d’informations sur la gestion des informations sensibles qui ne doivent pas être conservées dans les référentiels de contrôle de source, consultez les ressources suivantes :

- [Meilleures pratiques pour le déploiement des mots de passe et autres données sensibles sur ASP.NET et Service d’applications Azure](../../../../identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure.md).
- [Les Sites Web Azure : Comment chaînes d’Application et de connexion chaînes](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Décrit la fonctionnalité Azure qui substitue `appSettings` et `connectionStrings` les données dans le *Web.config* fichier.
- [Custom configuration et application des paramètres dans les Sites Web Azure - avec Stefan Schackow](https://azure.microsoft.com/documentation/videos/configuration-and-app-settings-of-azure-web-sites/).

Pour plus d’informations sur les autres méthodes pour la conservation des informations sensibles en dehors du contrôle de code source, consultez [ASP.NET MVC : conserver privé paramètres de contrôle de code Source](http://typecastexception.com/post/2014/04/06/ASPNET-MVC-Keep-Private-Settings-Out-of-Source-Control.aspx).

>[!div class="step-by-step"]
[Précédent](automate-everything.md)
[Suivant](continuous-integration-and-continuous-delivery.md)
