---
uid: web-forms/overview/older-versions-getting-started/deploying-web-site-projects/common-configuration-differences-between-development-and-production-vb
title: "Les différences de Configuration commune entre le développement et de Production (VB) | Documents Microsoft"
author: rick-anderson
description: "Dans les didacticiels précédentes, nous avons déployé notre site Web en copiant tous les fichiers pertinentes à partir de l’environnement de développement dans l’environnement de production. Toutefois, j’ai..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/01/2009
ms.topic: article
ms.assetid: 548e75f6-4d6c-4cb4-8da8-417915eb8393
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deploying-web-site-projects/common-configuration-differences-between-development-and-production-vb
msc.type: authoredcontent
ms.openlocfilehash: 8de1acada8713abf5f92c1f13fa82a5d4ccc18be
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="common-configuration-differences-between-development-and-production-vb"></a>Différences de Configuration commune entre le développement et de Production (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le PDF](http://download.microsoft.com/download/E/8/9/E8920AE6-D441-41A7-8A77-9EF8FF970D8B/aspnet_tutorial05_ConfigDifferences_vb.pdf)

> Dans les didacticiels précédentes, nous avons déployé notre site Web en copiant tous les fichiers pertinentes à partir de l’environnement de développement dans l’environnement de production. Toutefois, il n’est pas rare d’avoir des différences de configuration entre les environnements, et nécessite que chaque environnement dispose d’un fichier Web.config unique. Ce didacticiel examine les différences de configuration par défaut et examine les stratégies pour gérer les informations de configuration distincts.


## <a name="introduction"></a>Introduction


Les deux derniers didacticiels parcourue via le déploiement d’une application web simple. Le [ *déploiement de votre Site à l’aide d’un FTP Client* ](deploying-your-site-using-an-ftp-client-vb.md) didacticiel vous a montré comment utiliser un client FTP autonome pour copier les fichiers nécessaires à partir de l’environnement de développement jusqu'à la production. Le didacticiel précédent, [ *déploiement de votre Site à l’aide de Visual Studio*](deploying-your-site-using-visual-studio-vb.md), rechercher lors du déploiement à l’aide d’outil Copier le Site Web et l’option de publication de Visual Studio. Dans les deux didacticiels tous les fichiers de l’environnement de production était une copie d’un fichier sur l’environnement de développement. Toutefois, il n’est pas rare que les fichiers de configuration dans l’environnement de production, elles diffèrent de celles figurant dans l’environnement de développement. Configuration d’une application web est stockée dans le `Web.config` de fichiers et incluent généralement des informations sur les ressources externes, telles que la base de données web et serveurs de messagerie. Il indique également le comportement de l’application dans certaines situations, telles que le cours de l’action à entreprendre lorsqu’une exception non gérée se produit.

Lorsque vous déployez une application web, il est important que les informations de configuration correct se retrouver dans l’environnement de production. Dans la plupart des cas le `Web.config` Impossible de copier le fichier dans l’environnement de développement dans l’environnement de production en tant que-est. Au lieu de cela, une version personnalisée de `Web.config` doit être téléchargé en production. Ce didacticiel examine brièvement les différences de configuration plus courantes ; Il récapitule également certaines techniques pour gérer les informations de configuration différente entre les environnements.

## <a name="typical-configuration-differences-between-the-development-and-production-environments"></a>Configuration standard des différences entre le développement et les environnements de Production

Le `Web.config` fichier comprend un assortiment d’informations de configuration pour une application ASP.NET. Certaines de ces informations de configuration est le même quelle que soit l’environnement. Par exemple, les paramètres d’authentification et les règles d’autorisation d’URL indiquée dans le `Web.config` du fichier `<authentication>` et `<authorization>` éléments sont généralement identiques, quelle que soit l’environnement. Mais les autres informations de configuration - notamment des informations sur les ressources externes - généralement diffèrent selon l’environnement.

Chaînes de connexion de base de données sont un exemple d’informations de configuration qui diffère selon l’environnement. Lorsqu’une application web communique avec un serveur de base de données doit tout d’abord établir une connexion, et qui est possible grâce à une [chaîne de connexion](http://www.connectionstrings.com/Articles/Show/what-is-a-connection-string). Bien qu’il soit possible de coder en dur la chaîne de connexion de base de données directement dans les pages web ou le code qui se connecte à la base de données, il est préférable de placer `Web.config`de [ `<connectionStrings>` élément](https://msdn.microsoft.com/library/bf7sd233.aspx) afin que la chaîne de connexion les informations sont dans un emplacement unique et centralisé. Souvent une autre base de données est utilisé au cours du développement est utilisée dans la production ; Par conséquent, les informations de chaîne de connexion doivent être uniques pour chaque environnement.

> [!NOTE]
> Didacticiels futures Découvrez le déploiement d’applications pilotées par les données, à partir de laquelle nous allons approfondir les spécificités de la façon dont les chaînes de connexion de base de données sont stockées dans le fichier de configuration.


Le comportement attendu des environnements de développement et de production diffère considérablement. Une application web dans l’environnement de développement est créée, testé et débogué par un petit groupe de développeurs. Dans l’environnement de production cette même application visitée par un grand nombre d’utilisateurs simultané. ASP.NET inclut plusieurs fonctionnalités qui permettent aux développeurs de test et débogage d’une application, mais ces fonctions doivent être désactivées pour des raisons de performances et sécurité dans l’environnement de production. Examinons quelques paramètres de configuration de ce type.

### <a name="configuration-settings-that-impact-performance"></a>Paramètres de configuration qui affectent les performances

Lors de la visite d’une page ASP.NET pour la première fois (ou la première fois après que qu’il a changé), son balisage déclaratif doit être convertie en une classe et cette classe doit être compilée. Si l’application web utilise une compilation automatique classe code-behind de la page doit être compilé, trop. Vous pouvez configurer un vaste choix d’options de compilation via le `Web.config` du fichier [ `<compilation>` élément](https://msdn.microsoft.com/library/s10awwz0.aspx).

L’attribut de débogage est un des attributs plus importants dans le `<compilation>` élément. Si le `debug` attribut est défini sur « true », les assemblys compilés incluent les symboles de débogage, qui sont nécessaires lorsque vous déboguez une application dans Visual Studio. Toutefois, les symboles de débogage augmentent la taille de l’assembly et imposent des exigences de mémoire supplémentaire lors de l’exécution du code. En outre, lorsque la `debug` attribut est défini sur « true » à n’importe quel contenu retourné par `WebResource.axd` n'est pas mis en cache, ce qui signifie que que chaque fois qu’un utilisateur visite une page, ils devront télécharger à nouveau le contenu statique retourné par `WebResource.axd`.

> [!NOTE]
> `WebResource.axd`est un gestionnaire HTTP intégré introduit dans ASP.NET 2.0 qui utilisent des contrôles serveur pour récupérer des ressources incorporées, telles que les fichiers de script, des images, des fichiers CSS et autre contenu. Pour plus d’informations sur la façon de `WebResource.axd` fonctionne et comment vous pouvez l’utiliser pour accéder à des ressources incorporées à partir de vos contrôles serveur personnalisés, consultez [l’accès à incorporé ressources via une URL à l’aide de `WebResource.axd` ](http://aspnet.4guysfromrolla.com/articles/080906-1.aspx).


Le `<compilation>` l’élément `debug` attribut a généralement la valeur « true » dans l’environnement de développement. En fait, cet attribut doit être défini sur « true » pour déboguer une application web ; Si vous essayez de déboguer une application ASP.NET à partir de Visual Studio et le `debug` attribut est défini sur « false », Visual Studio affiche un message expliquant que l’application ne peut pas être déboguée jusqu'à ce que le `debug` attribut est défini sur « true » et, dans proposer à effectuer cette modification pour vous.

Vous devez **jamais** ont le `debug` attribut défini sur « true » dans un environnement de production en raison de son impact sur les performances. Pour une discussion plus approfondie sur ce sujet, reportez-vous à [Scott Guthrie](https://weblogs.asp.net/scottgu/)du billet de blog, [ne pas exécuter Production ASP.NET Applications avec `debug="true"` activé](https://weblogs.asp.net/scottgu/442448).

### <a name="custom-errors-and-tracing"></a>Le suivi et les erreurs personnalisées

Lorsqu’une exception non gérée se produit dans une application ASP.NET, il se propage jusqu'à l’exécution à partir de laquelle une des trois choses se produit :

- Un message d’erreur générique runtime s’affiche. Cette page indique à l’utilisateur qu’il y a une erreur d’exécution, mais ne fournit pas tous les détails concernant l’erreur.
- Un message d’exception détails s’affiche, qui inclut des informations sur l’exception qui a été levée uniquement.
- Une page d’erreur personnalisée s’affiche, qui est une page ASP.NET que vous créez qui affiche un message que vous le souhaitez.

Que se passe-t-il en cas d’une exception non prise en charge dépend de la `Web.config` du fichier [ `<customErrors>` section](https://msdn.microsoft.com/library/h0hfz6fc.aspx).

Lorsque vous développez et testez une application, qu'il est utile de consulter les détails d’une exception dans le navigateur. Toutefois, affichant les détails de l’exception dans une application de production est un risque de sécurité potentiel. En outre, il est unflattering et permet un aspect de votre site Web. Dans l’idéal, en cas d’une exception non prise en charge une application web dans l’environnement de développement affiche les détails de l’exception lors de la même application en production affiche une page d’erreurs personnalisée.

> [!NOTE]
> La valeur par défaut `<customErrors>` paramètre section affiche les détails de l’exception de message uniquement lorsque la page visitée via localhost et affiche la page d’erreur générique runtime dans le cas contraire. Ce n’est pas idéale, mais elle est garantie pour savoir que le comportement par défaut ne révèle pas les détails d’exception pour les visiteurs non local. Un didacticiel futures examine la `<customErrors>` section plus en détail et montre comment une page d’erreur personnalisée indiqué lorsqu’une erreur se produit en production.


Une autre fonctionnalité d’ASP.NET qui s’avère utile lors du développement est le suivi. Le suivi, s’il est activé, enregistre des informations sur chaque demande entrante et fournit une page web spéciale, `Trace.axd`, pour l’affichage des détails de la demande récente. Vous pouvez activer et configurer le suivi via la [ `<trace>` élément](https://msdn.microsoft.com/library/6915t83k.aspx) dans `Web.config`.

Si vous activez le traçage Assurez-vous que qu’il est désactivé en production. Étant donné que les informations de trace incluent les cookies, les données de session et les autres informations potentiellement sensibles, il est important de désactiver le suivi en production. La bonne nouvelle est que, par défaut, le suivi est désactivé et le `Trace.axd` fichier est uniquement accessible via localhost. Si vous modifiez ces paramètres par défaut dans le développement Assurez-vous qu’ils sont désactivés précédent en production.

## <a name="techniques-for-maintaining-separate-configuration-information"></a>Techniques pour gérer les informations de Configuration distincts

Avoir différents paramètres de configuration dans les environnements de développement et de production complique le processus de déploiement. Dans les didacticiels précédents de deux le processus de déploiement nécessitait la copie de tous les fichiers nécessaires à partir de développement en production, mais cette approche fonctionne uniquement si les informations de configuration sont la même dans les deux environnements. Il existe de nombreuses techniques pour le déploiement d’une application avec des informations de configuration. Nous allons catalogue certaines de ces options pour les applications web hébergées.

### <a name="manually-deploying-the-production-environment-configuration-file"></a>Déploiement manuel du fichier de Configuration d’environnement Production

L’approche la plus simple consiste à maintenir deux versions de le `Web.config` fichier : un pour l’environnement de développement et un pour l’environnement de production. Déploiement d’un site de production implique la copie tous les fichiers sur le serveur de production dans l’environnement de développement *sauf* pour la `Web.config` fichier. Au lieu de cela, l’environnement de production spécifiques `Web.config` fichier sera copié dans la production.

Cette approche n’est pas très sophistiquée, mais il est facile à implémenter, car les informations de configuration changent peu fréquemment. Il convient le mieux pour les applications avec une équipe de développement de petite taille qui sont hébergés sur un serveur web unique et dont les informations de configuration sont rarement modifiées. Il est plus tenable lors du déploiement manuel de fichiers de l’application à l’aide d’un client FTP autonome. Lorsque vous utilisez l’outil Copier le Site Web ou l’option de publication de Visual Studio, vous devez tout d’abord permuter spécifique au déploiement `Web.config` fichier celui spécifiques à la production avant de déployer, puis permuter après le déploiement est terminé.

### <a name="change-the-configuration-during-the-build-or-deployment-process"></a>Modifier la Configuration pendant la génération ou le processus de déploiement

Les discussions jusqu’ici ont pris par défaut un processus de génération et de déploiement ad-hoc. Nombreux grands projets logiciels ont formalisés plus les processus qui rendent l’utilisation de l’open source, maison, ou des outils tiers. Pour ces projets, vous pouvez probablement personnaliser le processus de génération ou de déploiement pour modifier les informations de configuration de façon appropriée avant qu’il est placé en production. Si vous générez votre application web à l’aide [MSBuild](http://en.wikipedia.org/wiki/MSBuild), [NAnt](http://nant.sourceforge.net/), ou un autre outil de génération, vous pouvez probablement ajouter une étape de génération pour modifier la `Web.config` fichier à inclure les paramètres spécifiques à la production. Ou votre flux de travail de déploiement impossible par programme se connecter au serveur de contrôle de code source et récupérer les `Web.config` fichier.

L’approche réel pour l’obtention des informations de configuration appropriées à la production varie largement en fonction de vos outils et les flux de travail. Par conséquent, nous passerons pas dans cette rubrique. Si vous utilisez un outil de génération populaires tels que MSBuild ou NAnt vous trouverez didacticiels et articles de déploiement spécifiques à ces outils via une recherche sur le web.

### <a name="managing-configuration-differences-via-the-web-deployment-project-add-in"></a>La gestion des différences de Configuration via le Web de déploiement de projet Complément

2006 Microsoft a publié le complément Project de Web Development pour Visual Studio 2005. Un complément pour Visual Studio 2008 a été publié en 2008. Ce complément permet aux développeurs ASP.NET créer un projet de déploiement Web séparé en même temps que leur projet d’application web qui, lors de la génération, explicitement compile l’application web et copie les fichiers à déployer vers un répertoire de sortie locale. Le projet d’Application Web utilise MSBuild en arrière-plan.

Par défaut, l’environnement de développement `Web.config` fichier est copié dans le répertoire de sortie, mais vous pouvez configurer le projet de déploiement Web pour personnaliser le

informations de configuration qui obtient copiées dans ce répertoire comme suit :

- Via `Web.config` remplacement de la section, dans lequel vous spécifiez la section remplacer et un fichier XML qui contient le texte de remplacement de fichiers.
- En fournissant un chemin d’accès à un fichier de source de configuration externe. Avec cette option est sélectionnée, le projet de déploiement Web copie un particulier `Web.config` dans le répertoire de sortie (plutôt que `Web.config` fichier utilisé dans l’environnement de développement).
- En ajoutant des règles personnalisées pour le fichier MSBuild utilisé par le projet de déploiement Web.

Pour déployer la build d’application web du projet de déploiement Web et puis copiez les fichiers du dossier de sortie du projet dans l’environnement de production.

Pour en savoir plus sur l’utilisation du projet de déploiement Web extraire [cet article de projets de déploiement Web](https://msdn.microsoft.com/magazine/cc163448.aspx) extrait du numéro d’avril 2007 de [MSDN Magazine](https://msdn.microsoft.com/magazine/default.aspx), ou consultez les liens dans la section obtenir des informations supplémentaires à la fin de ce didacticiel.

> [!NOTE]
> Vous ne pouvez pas utiliser le projet de déploiement Web avec Visual Web Developer, car le projet de déploiement Web est implémenté comme un Visual Studio complément et les éditions Visual Studio Express (y compris Visual Web Developer) ne prennent pas en charge les compléments.


## <a name="summary"></a>Récapitulatif

Les ressources externes et le comportement d’une application web en cours de développement sont en général différentes de celle utilisée lorsque la même application est en production. Par exemple, les chaînes de connexion de base de données, les options de compilation et le comportement lorsqu’une exception non gérée se produit couramment diffèrent entre les environnements. Le processus de déploiement doit prendre en compte ces différences. Que nous avons abordée dans ce didacticiel, l’approche la plus simple consiste à copier manuellement un autre fichier de configuration à l’environnement de production. Solutions spécifiques plus fluides sont possibles lorsque vous utilisez le Web déploiement de projet complément, ou avec un processus de génération ou de déploiement plus formel capable de gérer ces personnalisations.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Chaînes de connexion expliqués](http://www.connectionstrings.com/Articles/Show/what-is-a-connection-string)
- [@ ConnectionStrings.com de chaînes de connexion de base de données](http://www.connectionstrings.com/)
- [Ne pas exécuter des Applications ASP.NET de Production avec `debug="true"` activé](https://weblogs.asp.net/scottgu/Don_1920_t-run-production-ASP.NET-Applications-with-debug_3D001D20_true_1D20_-enabled)
- [Normalement répondre aux Exceptions non gérées - affichage des Pages d’erreur convivial](http://aspnet.4guysfromrolla.com/articles/090606-1.aspx)
- [Comment faire : Utiliser un projet de déploiement Web de Visual Studio 2008 ?](../../../videos/how-do-i/how-do-i-use-a-visual-studio-2008-web-deployment-project.md)
- [Paramètres de Configuration de la clé lors du déploiement d’une base de données](http://aspnet.4guysfromrolla.com/articles/121008-1.aspx)
- [Téléchargement de projets de déploiement Visual Studio 2008 Web](https://www.microsoft.com/downloads/details.aspx?FamilyId=0AA30AE8-C73B-4BDD-BB1B-FE697256C459&amp;displaylang=en) | [téléchargement de projets de déploiement Visual Studio 2005 Web](https://download.microsoft.com/download/9/4/9/9496adc4-574e-4043-bb70-bc841e27f13c/WebDeploymentSetup.msi)
- [Projets de déploiement Web de Visual Studio 2008](https://weblogs.asp.net/scottgu/archive/2005/11/06/429723.aspx) | [Visual Studio 2008 déploiement projet prise en charge Web publié](https://weblogs.asp.net/scottgu/archive/2008/01/28/vs-2008-web-deployment-project-support-released.aspx)
- [Projets de déploiement web](https://msdn.microsoft.com/magazine/cc163448.aspx)

>[!div class="step-by-step"]
[Précédent](deploying-your-site-using-visual-studio-vb.md)
[Suivant](core-differences-between-iis-and-the-asp-net-development-server-vb.md)
