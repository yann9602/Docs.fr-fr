---
uid: web-forms/overview/older-versions-getting-started/deploying-web-site-projects/determining-what-files-need-to-be-deployed-cs
title: "Déterminer quels fichiers doivent être déployés (c#) | Documents Microsoft"
author: rick-anderson
description: "Les fichiers devant être déployés à partir de l’environnement de développement à l’environnement de production dépend en partie indique si l’application ASP.NET a été générée nous..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/01/2009
ms.topic: article
ms.assetid: f8d78a88-cc91-40d8-bce3-3d7954f6033b
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deploying-web-site-projects/determining-what-files-need-to-be-deployed-cs
msc.type: authoredcontent
ms.openlocfilehash: d58956323275a46b44b36d4f19db4d2f607e3916
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="determining-what-files-need-to-be-deployed-c"></a>Déterminer quels fichiers doivent être déployés (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/4/5/F/45F815EC-8B0E-46D3-9FB8-2DC015CCA306/ASPNET_Hosting_Tutorial_02_CS.zip) ou [télécharger le PDF](http://download.microsoft.com/download/E/8/9/E8920AE6-D441-41A7-8A77-9EF8FF970D8B/aspnet_tutorial02_FilesToDeploy_cs.pdf)

> Les fichiers devant être déployés à partir de l’environnement de développement à l’environnement de production dépend en partie indique si l’application ASP.NET a été générée à l’aide du modèle de Site Web ou d’un modèle d’Application Web. En savoir plus sur ces deux modèles de projet et comment le modèle de projet affecte le déploiement.


## <a name="introduction"></a>Introduction

Déploiement d’une application web ASP.NET implique la copie des fichiers liés à ASP.NET à partir de l’environnement de développement vers l’environnement de production. Les fichiers ASP.NET incluent le balisage de page web ASP.NET et les fichiers de prise en charge de code et côté client et côté serveur. Les fichiers de prise en charge côté client sont les fichiers référencés par vos pages web et envoyé directement au navigateur - images, CSS et JavaScript, par exemple. Prise en charge côté serveur de fichiers incluent ceux qui sont utilisés pour traiter une demande sur le côté serveur. Cela inclut les fichiers de configuration, les services web, les fichiers de classe, typés et LINQ à des fichiers SQL, entre autres.

En général, tous les fichiers de prise en charge côté client doivent être copiés à partir de l’environnement de développement à l’environnement de production, mais les fichiers de prise en charge côté serveur sont copiés dépend de si vous explicitement compilez le code côté serveur dans un assembly (un `.dll` fichier) ou si vous rencontrez ces assemblys généré automatiquement. Ce didacticiel met en évidence les fichiers devant être déployé lorsque explicitement la compilation du code dans un assembly plutôt que cette étape de compilation se produisent automatiquement.

## <a name="explicit-compilation-versus-automatic-compilation"></a>Compilation explicite par rapport à une Compilation automatique

Les pages web ASP.NET sont divisées en déclaratif balisage et le code source. La partie du balisage déclaratif inclut HTML, les commandes et syntaxe de liaison de données ; la partie du code contient les gestionnaires d’événements écrits en code Visual Basic ou c#. Les parties de balisage et code sont généralement séparés dans différents fichiers : `WebPage.aspx` contient le balisage déclaratif lors `WebPage.aspx.cs` héberge le code.

Envisagez d’une page ASP.NET nommée Clock.aspx qui contient un contrôle d’étiquette dont la propriété Text est définie à la date et heure actuelles lorsque la page se charge. La partie du balisage déclaratif (dans `Clock.aspx`) contient le balisage pour un contrôle Web Label -`<asp:Label runat="server" id="TimeLabel" />` - lors de la partie du code (dans `Clock.aspx.cs`) aurait un `Page_Load` Gestionnaire d’événements par le code suivant :

[!code-csharp[Main](determining-what-files-need-to-be-deployed-cs/samples/sample1.cs)]

Dans l’ordre pour le moteur ASP.NET traiter une demande de cette page, la partie de la page code (le `WebPage.aspx.cs` fichier) doit tout d’abord être compilé. Cette compilation peut se produire automatiquement ou explicitement.

Si la compilation se produit de manière explicite, le code source de l’application entière est compilé dans un ou plusieurs assemblys (`.dll` fichiers) situé dans l’application `Bin` active. Si la compilation s’effectue automatiquement, générée automatiquement résultant assembly est, par défaut, placé dans le `Temporary ASP.NET` dossier de fichiers, qui se trouve à `%WINDOWS%\Microsoft.NET\Framework\`  *&lt;version&gt;*, Bien que cet emplacement est configurable via le [ `<compilation>` élément](https://msdn.microsoft.com/library/s10awwz0.aspx) dans `Web.config`. Avec la compilation explicite, vous devez prendre une action pour compiler le code de l’application ASP.NET dans un assembly, et cette étape se produit avant le déploiement. Avec la compilation automatique du processus de compilation se produit sur le serveur web lors du premier accès de la ressource.

Quel que soit le mode de compilation que vous utilisez, la partie de balisage de toutes les pages ASP.NET (la `WebPage.aspx` fichiers) doivent être copiés dans l’environnement de production. Avec la compilation explicite, vous devez copier les assemblys dans le `Bin` dossier, mais vous n’avez pas besoin de copier des parties du code des pages ASP.NET (la `WebPage.aspx.cs` fichiers). Avec la compilation automatique, vous devez copier les fichiers de partie de code afin que le code est présent et peut être compilé automatiquement lorsque la page est visitée. La partie de balisage de chaque page web ASP.NET inclut un `@Page` directive avec des attributs qui indiquent si les code associé de la page a été compilé déjà explicitement ou s’il doit être compilé automatiquement. Par conséquent, l’environnement de production permettre fonctionner de manière transparente avec un modèle de compilation, et vous n’avez pas besoin d’appliquer les paramètres de configuration spéciaux pour indiquer que la compilation explicite ou automatique est utilisée.

Le tableau 1 résume les différents fichiers à déployer lors de l’utilisation de la compilation explicite par rapport à une compilation automatique. Notez que quelle que soit la compilation modèle utilisé que vous déployez toujours les assemblys dans le `Bin` dossier, si ce dossier existe. Le `Bin` dossier contient les assemblys spécifiques à l’application web, notamment le code source compilés lorsque vous utilisez le modèle de compilation explicite. Le `Bin` répertoire contient également des assemblys à partir d’autres projets et tous les assemblys tiers ou open source, vous pouvez utiliser, et ils doivent être sur le serveur de production. Par conséquent, en règle générale, copiez la `Bin` dossier jusqu'à lors du déploiement de production. (Si vous utilisez le modèle de compilation automatique et que vous n’utilisez pas de tous les assemblys externes, vous n’avez pas un `Bin` répertoire - OK !)

| **Modèle de compilation** | **Déployer le fichier de balisage partie ?** | **Déployer le fichier de Code Source ?** | **Déployer des assemblys dans `Bin` Active ?** |
| --- | --- | --- | --- |
| Compilation explicite | Oui | Non | Oui |
| Compilation automatique | Oui | Oui | Oui (si elle existe) |

**Tableau 1 :** les fichiers que vous déployez varie selon le modèle de compilation utilisé.

## <a name="taking-a-trip-down-memory-lane"></a>En prenant un voyage dans le couloir de mémoire

Quelle approche de compilation est utilisée dépend en partie, de mode de gestion de l’application ASP.NET dans Visual Studio. Depuis. Début du réseau de l’année 2000 a quatre versions de Visual Studio - Visual Studio 2008, Visual Studio 2005, Visual Studio .NET 2002 et Visual Studio .NET 2003. Visual Studio .NET 2002 et 2003 géré des applications ASP.NET à l’aide de la *modèle de projet d’Application Web*. Les fonctionnalités clées du modèle de projet d’Application Web sont :

- Les fichiers que la composition du projet sont définis dans un seul fichier de projet. Tous les fichiers non définis dans le fichier de projet ne sont pas considérés partie de l’application web par Visual Studio.
- Utilise la compilation explicite. Génération du projet compile les fichiers de code dans le projet dans un assembly unique qui est placé dans le `Bin` dossier.

Lorsque Microsoft a publié Visual Studio 2005, ils supprimé la prise en charge pour le modèle de projet d’Application Web et remplacement par le modèle de projet de Site Web. Le modèle de projet de Site Web différenciés lui-même à partir du modèle de projet d’Application Web de plusieurs manières :

- Au lieu d’avoir un fichier de projet unique qui définit les fichiers du projet, le système de fichiers est utilisé à la place. En bref, tous les fichiers dans le dossier d’application web (ou les sous-dossiers) sont considérées comme partie du projet.
- Génération d’un projet dans Visual Studio ne crée pas un assembly dans le `Bin` active. Au lieu de cela, la génération d’un projet de Site Web signale les erreurs de compilation.
- Prise en charge pour la compilation automatique. Projets de Site Web sont généralement déployés en copiant le balisage et le code source dans l’environnement de production, bien que le code puisse être précompilée (compilation explicite).

Microsoft réactivée le modèle de projet d’Application Web version Visual Studio 2005 Service Pack 1. Toutefois, Visual Web Developer a continué à prennent uniquement en charge le modèle de projet de Site Web. La bonne nouvelle est que cette limitation a été supprimée avec Visual Web Developer 2008 Service Pack 1. Aujourd'hui, vous pouvez créer des applications ASP.NET dans Visual Studio (et Visual Web Developer) à l’aide du modèle de projet d’Application Web ou le modèle de projet de Site Web. Les deux modèles ont leurs avantages et inconvénients. Reportez-vous à [Introduction aux projets d’Application Web : comparaison des projets de Site Web et les projets d’Application Web](https://msdn.microsoft.com/library/aa730880.aspx#wapp_topic5) pour obtenir une comparaison des deux modèles et pour aider à décider quel modèle de projet convient le mieux à votre situation.

## <a name="exploring-the-sample-web-application"></a>Explorer l’exemple d’Application Web

Le téléchargement de ce didacticiel inclut une application ASP.NET appelée critiques. Le site Web imite un site Web de passe-temps une personne peut créer à partager leur livre passe en revue avec la Communauté en ligne. Cette application de web ASP.NET est très simple et comprend les ressources suivantes :

- `Web.config`, fichier de configuration de l’application.
- Une page maître (`Site.master`).
- Sept différentes pages ASP.NET : 

    - ~`/Default.aspx`-page d’accueil du site.
    - ~`/About.aspx`-une page « Sur le Site ».
    - ~`/Fiction/Default.aspx`-une page qui répertorie les livres de science-fiction qui ont été vérifiées. 

        - ~`/Fiction/Blaze.aspx`-une revue de la nouvelle Richard Bachman *vérifie*.
    - ~/`Tech/Default.aspx`-une page qui répertorie les livres de la technologie qui ont été vérifiées. 

        - ~/`Tech/CYOW.aspx`-une revue de *créer votre propre site Web de*.
        - ~/`Tech/TYASP35.aspx`-une revue de *apprendre vous-même ASP.NET 3.5 des dernières 24 heures*.
- Trois fichiers CSS différents dans le dossier Styles.
- Quatre fichiers image - un optimisé par ASP.NET logo et les images d’arrière-plan de livres passés en revue trois - tous les situé dans le `Images` dossier.
- A `Web.sitemap` fichier, qui définit le plan de site et est utilisé pour afficher les menus dans le `Default.aspx` pages dans le répertoire racine et `Fiction` et `Tech` dossiers.
- Un fichier de classe nommé `BasePage.cs` qui définit une base `Page` classe. Cette classe étend les fonctionnalités de la `Page` classe définissant automatiquement la `Title` propriété basée sur la position de la page dans le plan de site. En bref, toute classe code-behind d’ASP.NET qui s’étend `BasePage` (au lieu de `System.Web.UI.Page`) aura son titre, une valeur en fonction de sa position dans le plan de site. Par exemple, lorsque vous affichez le ~ /`Tech/CYOW.aspx` page, le titre est défini à « Accueil : technologie : créer votre propre site Web ».

La figure 1 illustre une capture d’écran du site Web critiques lorsqu’ils sont affichés via un navigateur. Ici, vous voyez la page ~ /`Tech/TYASP35.aspx`, qui examine le carnet de *apprendre vous-même ASP.NET 3.5 des dernières 24 heures*. La barre de navigation qui s’étend sur la partie supérieure de la page et le menu de la colonne de gauche sont basées sur la structure du plan de site définie dans `Web.sitemap`. L’image dans l’angle supérieur droit est une des images se trouvent dans la couverture de livre le `Images` dossier. Apparence du site Web sont définies via en cascade décrits par les fichiers CSS dans le dossier Styles, la mise en page principale est définie dans la page maître, de règles de feuille de style `Site.master`.


[![Le site Web du livre examine offre revues sur un large éventail de titres](determining-what-files-need-to-be-deployed-cs/_static/image2.png)](determining-what-files-need-to-be-deployed-cs/_static/image1.png)

**Figure 1 :** le site Web du livre examine offre revues sur un large éventail de titres ([cliquez pour afficher l’image en taille réelle](determining-what-files-need-to-be-deployed-cs/_static/image3.png))


Cette application n’utilise pas d’une base de données ; chaque révision est implémentée comme une page web distincte dans l’application. Ce didacticiel (et les didacticiels plusieurs suivants) guident de déploiement d’une application web qui ne dispose pas d’une base de données. Toutefois, dans un didacticiel futures nous améliorer cette application pour stocker les révisions, les commentaires des lecteurs et les autres informations dans une base de données et Explorer les étapes qui doivent être exécutées pour correctement déployer une application web pilotées par les données.

> [!NOTE]
> Ces didacticiels vous concentrer sur l’hébergement d’applications ASP.NET avec un fournisseur d’hébergement web et ne pas Explorer des rubriques connexes tels que ASP. Système de mappage de site ou à l’aide d’une base de NET `Page` classe. Pour plus d’informations sur ces technologies et pour plus d’informations sur d’autres sujets abordés dans ce didacticiel, consultez la section obtenir des informations supplémentaires à la fin de chaque didacticiel.


Téléchargement de ce didacticiel comporte deux copies de l’application web, chacun implémenté en tant qu’un autre type de projet Visual Studio : BookReviewsWAP, un projet d’Application Web et BookReviewsWSP, un projet de Site Web. Les deux projets ont été créées avec Visual Web Developer 2008 SP1 et utilisent ASP.NET 3.5 SP1. Pour travailler avec ces projets démarrer à décompresser le contenu sur votre bureau. Pour ouvrir le projet d’Application Web (BookReviewsWAP), accédez au dossier BookReviewsWAP et double-cliquez sur le fichier Solution `BookReviewsWAP.sln`. Pour ouvrir le projet de Site Web (BookReviewsWSP), lancez Visual Studio, puis, dans le menu fichier, choisissez l’option Ouvrir le Site Web, accédez à la `BookReviewsWSP` dossier sur votre bureau, cliquez sur OK.


Les deux sections suivantes de ce didacticiel examiner les fichiers que vous devez copier dans l’environnement de production lors du déploiement de l’application. Les deux didacticiels -  *[déploiement de votre Site à l’aide de FTP](deploying-your-site-using-an-ftp-client-cs.md)*  et  *[déploiement de votre Site à l’aide de Visual Studio](deploying-your-site-using-visual-studio-cs.md)*  -afficher les différentes façons de Copiez ces fichiers vers un fournisseur d’hébergement web.

## <a name="determining-the-files-to-deploy-for-the-web-application-project"></a>Déterminer les fichiers à déployer pour le projet d’Application Web

Le modèle de projet d’Application Web utilise la compilation explicite - code de source du projet est compilé dans un assembly unique chaque fois que vous générez l’application. Cette compilation inclut les fichiers de code-behind des pages ASP.NET (~ /`Default.aspx.cs`, ~ /`About.aspx.cs`, et ainsi de suite), ainsi que le `BasePage.cs` classe. L’assembly résultant est nommé BookReviewsWAP.dll et se trouve dans l’application `Bin` active.

La figure 2 illustre les fichiers qui composent le projet d’Application Web Book révisions.


[![L’Explorateur de solutions répertorie les fichiers qui composent le projet d’Application Web](determining-what-files-need-to-be-deployed-cs/_static/image5.png)](determining-what-files-need-to-be-deployed-cs/_static/image4.png)

**Figure 2**: l’Explorateur de solutions répertorie les fichiers qui composent le projet d’Application Web


Pour déployer une application ASP.NET développée à l’aide de début du modèle de projet d’Application Web à la génération de l’application afin de compiler explicitement le code source le plus récent dans un assembly. Ensuite, copiez les fichiers suivants à l’environnement de production :

- Les fichiers qui contiennent le balisage déclaratif pour chaque ASP.NET page, tels que ~ /`Default.aspx`, ~ /`About.aspx`, et ainsi de suite. En outre, la copie du balisage déclaratif pour toutes les pages maîtres et les contrôles utilisateur.
- Les assemblys (`.dll` fichiers) dans le `Bin` dossier. Vous n’avez pas besoin copier les fichiers de base de données de programme (`.pdb`) ou des fichiers XML que vous pouvez trouver dans le `Bin` active.

Vous n’avez pas besoin de copier les fichiers de code source des pages ASP.NET à l’environnement de production, ni vous devez copier le `BasePage.cs` fichier de classe.

> [!NOTE]
> Comme le montre la Figure 2, le `BasePage` classe est implémentée comme un fichier de classe dans le projet, placé dans un dossier nommé `HelperClasses`. Lorsque le projet est compilé le code dans le `BasePage.cs` fichier est compilé en même temps que les classes de code-behind des pages ASP.NET dans l’assembly unique, `BookReviewsWAP.dll.` ASP.NET dispose d’un dossier spécial nommé `App_Code` qui est conçu pour contenir les fichiers de classe pour le Web Projets de site. Le code dans le `App_Code` dossier est automatiquement compilé et ne doit donc pas être utilisé avec les projets d’Application Web. Au lieu de cela, vous devez placer les fichiers de classe de votre application dans un dossier normal nommé `HelperClasses`, ou `Classes`, ou quelque chose de similaire. Ou bien, vous pouvez placer les fichiers de classe dans un projet de bibliothèque de classes distinct.


En plus de copier les fichiers liés à ASP.NET de balisage et l’assembly dans le `Bin` dossier, vous devez également copier les fichiers de prise en charge côté client - les images et les fichiers CSS -, ainsi que les autres fichiers de prise en charge côté serveur, `Web.config` et `Web.sitemap`. Ce côté client et serveur prend en charge la nécessité de fichiers à copier sur l’environnement de production que vous utilisiez la compilation explicite ou automatique.

## <a name="determining-the-files-to-deploy-for-the-web-site-project-files"></a>Déterminer les fichiers à déployer pour les fichiers de projet de Site Web

Le modèle de projet de Site Web prend en charge la compilation automatique, une fonctionnalité non disponible lorsque vous utilisez le modèle de projet d’Application Web. Avec la compilation explicite, vous devez compiler le code source de votre projet dans un assembly et copier cet assembly à l’environnement de production. En revanche, avec la compilation automatique, vous copiez simplement le code source pour l’environnement de production, et il est compilé par le runtime à la demande en fonction des besoins.

L’option de menu de génération dans Visual Studio est présente dans les projets d’Application Web et projets de Site Web. Création d’un projet d’Application Web compile le code de source du projet dans un assembly unique situé dans le `Bin` répertoire ; la création d’un projet de Site Web vérifie les erreurs de compilation, mais ne crée pas de tous les assemblys. Pour déployer une application ASP.NET développée à l’aide du modèle projet de Site Web toutes les, que vous devez faire est copie les fichiers appropriés à l’environnement de production, mais je vous encourage à la première génération du projet pour vous assurer qu’il n’y a aucune erreur de compilation.

La figure 3 illustre les fichiers qui composent les révisions du Site Web de projet.


 [![L’Explorateur de solutions répertorie les fichiers qui composent le projet de Site Web](determining-what-files-need-to-be-deployed-cs/_static/image7.png)](determining-what-files-need-to-be-deployed-cs/_static/image6.png) 

**Figure 3**: l’Explorateur de solutions répertorie les fichiers qui composent le projet de Site Web


Déploiement d’un projet de Site Web implique de copier tous les fichiers ASP.NET à l’environnement de production - qui inclut les pages de balisage pour les pages ASP.NET, les pages maîtres et les contrôles utilisateur, ainsi que leurs fichiers de code. Vous devez également copier les fichiers de classe, tels que BasePage.cs. Notez que la `BasePage.cs` fichier se trouve dans le `App_Code` dossier, qui est un dossier ASP.NET spécial utilisé dans les projets de Site Web pour les fichiers de classe. Le dossier spécial doit être créé sur la production, ainsi que les fichiers de classe dans le `App_Code` dossier sur l’environnement de développement doit être copié vers le `App_Code` dossier de production.

En plus de copier les fichiers de code source et de balisage ASP.NET, vous devez également copier les fichiers de prise en charge côté client - les images et les fichiers CSS -, ainsi que les autres fichiers de prise en charge côté serveur, `Web.config` et `Web.sitemap`.

> [!NOTE]
> Projets de Site Web permet également la compilation explicite. Un didacticiel futures examine comment compiler explicitement le projet de Site Web.


## <a name="summary"></a>Récapitulatif

Déploiement d’une application ASP.NET implique la copie des fichiers nécessaires à partir de l’environnement de développement à l’environnement de production. Le jeu précis de fichiers qui doivent être synchronisées varie selon que le code de l’application ASP.NET est explicitement ou automatiquement compilé. La stratégie de compilation employée dépend si Visual Studio est configuré pour gérer l’application ASP.NET à l’aide du modèle de projet d’Application Web ou le modèle de projet de Site Web.

Le modèle de projet d’Application Web utilise la compilation explicite et compile le code du projet dans un assembly unique dans le `Bin` dossier. Lors du déploiement de l’application, la partie du balisage des pages ASP.NET et le contenu de la `Bin` dossier doit être transmis à l’environnement de production ; le code source dans l’application - les fichiers de code et des classes de code-behind, par exemple - est inutile. doit être copié vers l’environnement de production.

Le modèle de projet de Site Web utilise une compilation automatique par défaut, bien qu’il soit possible de compiler un projet de Site Web, comme nous le verrons dans les futures didacticiels. Déploiement d’une application ASP.NET qui utilise une compilation automatique nécessite que la partie de balisage *et* code source doit être copié dans l’environnement de production. Le code est compilé automatiquement sur l’environnement de production lorsqu’elle est demandée pour la première fois.

Maintenant que nous avons examiné les fichiers devant être synchronisés entre les environnements de développement et de production, nous sommes prêts à déployer l’application critiques à un fournisseur d’hébergement web.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Vue d’ensemble de la Compilation ASP.NET](https://msdn.microsoft.com/library/ms178466.aspx)
- [Contrôles utilisateur ASP.NET](https://msdn.microsoft.com/library/y6wb1a0e.aspx)
- [Examen des ASP. Navigation du Site de réseau](http://aspnet.4guysfromrolla.com/articles/111605-1.aspx)
- [Introduction aux projets d’Application Web](https://msdn.microsoft.com/library/aa730880.aspx)
- [Didacticiels de Page maître](../master-pages/creating-a-site-wide-layout-using-master-pages-cs.md)
- [Partage de Code entre les Pages](https://quickstarts.asp.net/QuickStartv20/aspnet/doc/pages/code.aspx)
- [À l’aide d’une classe de Base personnalisée pour les Classes de Code-Behind de vos Pages ASP.NET](http://aspnet.4guysfromrolla.com/articles/041305-1.aspx)
- [Système de projet de Site Web de Visual Studio 2005 : définition et pourquoi cela A-t-il été ?](https://weblogs.asp.net/scottgu/archive/2005/08/21/423201.aspx)
- [Procédure pas à pas : Conversion d’un projet de Site Web à un projet d’Application Web dans Visual Studio](https://msdn.microsoft.com/library/aa983476.aspx)

>[!div class="step-by-step"]
[Précédent](asp-net-hosting-options-cs.md)
[Suivant](deploying-your-site-using-an-ftp-client-cs.md)
