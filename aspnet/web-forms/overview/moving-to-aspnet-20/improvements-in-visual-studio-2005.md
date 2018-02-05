---
uid: web-forms/overview/moving-to-aspnet-20/improvements-in-visual-studio-2005
title: "Améliorations dans Visual Studio 2005 | Documents Microsoft"
author: microsoft
description: "Visual Studio 2005 fournit les développeurs d’applications Web avec une longue liste d’améliorations apportées aux projets Web."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2005
ms.topic: article
ms.assetid: 72d90cd0-b3d9-454c-b2eb-ed0d9812f32c
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/moving-to-aspnet-20/improvements-in-visual-studio-2005
msc.type: authoredcontent
ms.openlocfilehash: aafc59980e807677d6023110d324365ce92bb5fc
ms.sourcegitcommit: d8aa1d314891e981460b5e5c912afb730adbb3ad
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2018
---
<a name="improvements-in-visual-studio-2005"></a>Améliorations dans Visual Studio 2005
====================
par [Microsoft](https://github.com/microsoft)

> Visual Studio 2005 fournit les développeurs d’applications Web avec une longue liste d’améliorations apportées aux projets Web.


Visual Studio 2005 fournit les développeurs d’applications Web avec une longue liste d’améliorations apportées aux projets Web. Aussi puissant que Visual Studio .NET 2002 et 2003 sont, se sont produites plusieurs réclamations dans la façon dont les projets Web ont été traitées. Visual Studio 2005 ajoute un nombre important de nouvelles fonctionnalités pour pouvoir gérer ces réclamations. Pour ceux qui préfèrent la façon dont Visual Studio .NET 2003 traitées compilation d’applications Web, consultez [projets d’Application Web](https://go.microsoft.com/fwlink/?LinkId=57870).

Dans ce module, également couvrent les améliorations apportées à la création du projet Web, de gestion et de développement. Dans un autre module, également couvrent les améliorations de la génération de projets Web et de les déployer.

## <a name="frontpage-server-extensions"></a>Extensions serveur FrontPage

Visual Studio .NET 2002 et 2003 requis sur les Extensions serveur FrontPage sur la zone pour pouvoir créer ou de générer des projets Web. Les développeurs avait le choix entre deux modes d’accès différents (mode d’accès au fichier ou les Extensions serveur FrontPage), les Extensions serveur FrontPage les deux permettant d’effectuer des tâches telles que la définition de la racine de l’application dans IIS, etc.

Visual Studio 2005 supprime la dépendance sur les Extensions serveur FrontPage pour les projets locaux. Visual Studio 2005 présent accède à la métabase IIS directement plutôt que les Extensions serveur FrontPage. Visual Studio 2005 prend également en charge FTP qui autorise l’accès de projet à distance sans nécessiter des Extensions serveur FrontPage.

Pour les développeurs qui souhaitent utiliser les Extensions serveur FrontPage dans leurs projets, l’option est toujours disponible. Toutefois, en fonction de retour d’informations à partir de la Communauté des développeurs ASP.NET, il n’est pas obligatoire.

> [!NOTE]
> Les Extensions serveur FrontPage sont toujours requises pour la création du projet à distance, lors de l’ouverture, etc.


## <a name="aspnet-development-server"></a>serveur de développement ASP.NET

Visual Studio 2005 est livré avec un serveur Web appelé serveur de développement ASP.NET. (Ce serveur Web était anciennement Cassini.)

Il existe plusieurs avantages du serveur de développement ASP.NET.

- Il est désormais possible pour les non-administrateurs à développer et déboguer sur un serveur Web.
- Le serveur de développement ASP.NET mappe dynamiquement des répertoires virtuels à n’importe quel emplacement dans le système de fichiers permettant à des emplacements de projet flexible.
- Sur Windows XP Professionnel, les utilisateurs qui utilisent déjà IIS sera désormais en mesure de créer des applications Web qui n’affectent pas la structure de fichier ou un dossier de leur Site Web par défaut dans IIS.

Aucune configuration spéciale n’est requise pour tirer parti du serveur de développement ASP.NET. Lorsqu’un projet Web qui est hébergé sur le système de fichiers est débogué ou parcouru, Visual Studio 2005 démarre automatiquement une instance du serveur de développement ASP.NET sur un port aléatoire pour la demande de service.

Seront abordées plus d’informations sur le serveur de développement ASP.NET plus loin dans ce module.

## <a name="improved-file-management"></a>Gestion améliorée des fichiers

Dans Visual Studio 2002 et 2003, un fichier projet (.vbproj pour VB.NET) et .csproj pour c# stockées des informations sur tous les fichiers dans l’application Web. L’affichage de l’Explorateur de solutions est basé sur les informations de fichier dans le fichier projet. Pour cette raison, l’Explorateur de solutions affiche souvent des informations incorrectes dans les cas où des éditeurs externes ont été utilisés. Visual Studio 2002 et 2003 est souvent remplacer les modifications de fichier ou n’affiche pas la version la plus récente des fichiers.

Visual Studio 2005 n’est absent du fichier projet. Au lieu de cela, il lit les informations de fichier et dossier directement à partir du disque, ce qui entraîne un affichage précis des fichiers dans votre projet. Étant donné que le dossier références de Visual Studio 2002 et 2003 ne représente pas un dossier réel dans votre application Web, Visual Studio 2005 supprime également le dossier références à partir de l’Explorateur de solutions. Pour accéder aux références de votre projet dans Visual Studio 2005, vous devez utiliser les pages de propriétés pour le projet.

## <a name="creating-web-projects"></a>Création de projets Web

Les développeurs Web ont de nombreuses nouvelles options disponibles pour la création du projet dans Visual Studio 2005. Sites Web peuvent maintenant être créés n’importe où dans le système de fichiers et peut ensuite être débogués ou visualisé à l’aide du nouveau serveur de développement ASP.NET. Les développeurs peuvent également créer des nouveaux sites Web à l’aide de FTP.

Cliquez ici pour afficher une procédure pas à pas vidéo de la création de projets Web dans Visual Studio 2005.


![](improvements-in-visual-studio-2005/_static/image1.png)


[Ouvrez plein écran](improvements-in-visual-studio-2005/_static/creating_projects1.wmv)


### <a name="file-system-projects"></a>Projets de système de fichiers

Comme vous l’avez vu dans la procédure pas à pas vidéo, vous pouvez choisir de créer des sites Web sur le système de fichiers sur l’ordinateur local ou sur un emplacement distant via un partage de fichiers. Les sites Web qui sont créés sur le système de fichiers sont explorés et déboguées à l’aide du serveur de développement ASP.NET.

> [!NOTE]
> Le serveur de développement ASP.NET peut provoquer une certaine confusion pour les clients. Si un projet Web est créé sur le système de fichiers dans la structure de répertoire IISs (par exemple, c:/inetpub/wwwroot), le site Web sera encore être parcouru via le serveur de développement ASP.NET lorsque lancé au sein de Visual Studio 2005. Par conséquent, toute configuration IIS (autrement dit, les méthodes d’authentification) n’est pas applicable.


Le projet web par défaut supprime également beaucoup de la surcharge par inclut uniquement une page Default.aspx, fichier default.cs et un dossier de l’application/_Data. Web.config et dossiers spéciaux (par exemple, application _code) sont ajoutés comme ils sont nécessaires. Votre projet web inclut uniquement les fichiers et dossiers dont vous avez besoin.

### <a name="http-projects"></a>Projets HTTP

Les projets HTTP peuvent être des projets qui sont créés sur un site Web IIS local ou sur un site Web distant. L’emplacement du projet par défaut est `http://localhost`. Si vous cliquez sur le bouton Parcourir, il existe deux options HTTP : serveur IIS Local et le Site distant. La principale différence dans ces deux options est la méthode dans laquelle les informations de site web s’affichent dans la boîte de dialogue Choisir un emplacement et comment les fichiers sont copiés sur le serveur Web.

L’option de serveur IIS Local lit les informations de site à partir de la métabase sur l’ordinateur local et les fichiers sont copiés à l’aide du système de fichiers. L’option de Site distant utilise les Extensions serveur FrontPage et les informations de site et les fichiers sont copiés à l’aide de HTTP et appels de procédure distante de FrontPage Server Extensions.

> [!NOTE]
> Le fichier de vs###/_tmp.htm et get/_aspx/_ver.aspx sont n’est plus utilisés pour déterminer les informations de version.


L’option de HTTP par défaut est le serveur IIS Local. Cette option lit la métabase IIS pour déterminer les sites qui sont disponibles et l’emplacement dans lequel créer le contenu. Vous pouvez sélectionner un autre dossier ou un répertoire virtuel en le sélectionnant dans l’arborescence. Vous pouvez également créer un nouveau répertoire virtuel, marquer les dossiers comme des applications, comme ainsi que supprimer des répertoires virtuels existants à partir de cette boîte de dialogue.


![La boîte de dialogue emplacement choisir](improvements-in-visual-studio-2005/_static/image1.gif)

**Figure 1**: la boîte de dialogue emplacement choisir


Contrairement à dans les versions antérieures de Visual Studio, si vous activez le **utilisez Secure Sockets Layer** case à cocher et le certificat SSL ne correspond pas à l’URL que vous effectuez des recherches, vous verrez une boîte de dialogue Alerte de sécurité vous demandant si vous le feriez pour comme continuer. À l’aide de Visual Studio .NET 2003, si le certificat n’était pas une correspondance, création du projet échoue.


![Certificat SSL alerte concernant sécurité](improvements-in-visual-studio-2005/_static/image2.gif)

**Figure 2**: alerte de sécurité concernant le certificat SSL


### <a name="note-on-host-headers"></a>Remarque sur les en-têtes d’hôtes

Si vous créez une application Web sur un site lié à une adresse IP spécifique, vous devez vous assurer qu’un en-tête d’hôte est configuré. Dans le cas contraire, Visual Studio crée le site à `http://localhost`, mais l’adresse IP n’est pas correctement résolues lorsque le site est parcouru ou de débogage à partir de l’IDE.

Si vous sélectionnez l’option de Site distant, la boîte de dialogue change pour vous permettre d’entrer l’URL de destination pour le nouveau site Web. Cette URL doit être sur un serveur qui a des Extensions serveur FrontPage activée. Si vous souhaitez utiliser avec votre serveur Web local à l’aide des Extensions serveur FrontPage, vous pouvez utiliser l’option de Site distant et spécifiez une URL locale.


![Création d’un Site Web sur un serveur distant](improvements-in-visual-studio-2005/_static/image1.jpg)

**Figure 3**: création d’un Site Web sur un serveur distant


Lorsque vous créez une application sur un site distant via SSL, si le certificat SSL ne correspond pas, la boîte de dialogue de confirmation est légèrement différente de celle de la boîte de dialogue affichée lorsque l’option de serveur IIS Local.


![L’alerte de sécurité du Site distant](improvements-in-visual-studio-2005/_static/image3.gif)

**Figure 4**: l’alerte de sécurité du Site distant


<a id="_Toc116100243"></a>

#### <a name="ftp"></a>FTP

Visual Studio 2005 propose une option permettant de créer des sites Web via FTP. Lorsque vous utilisez cette option, l’IDE crée les fichiers localement dans le dossier temporaire d’utilisateurs, puis utilise FTP pour déplacer les fichiers à l’emplacement FTP.

> [!NOTE]
> L’emplacement du dossier temporaire est c:/Documents and Settings /&lt;utilisateur&gt;/Local paramètres/Temp/VWDWebCache/&lt;Server&gt;/_&lt;nom de l’application&gt;


Lorsque vous utilisez l’option FTP, une boîte de dialogue Choisir un emplacement s’affiche. Vous entrez les informations de connexion FTP requises dans cette boîte de dialogue comme illustré ci-dessous.


![La boîte de dialogue emplacement FTP choisir](improvements-in-visual-studio-2005/_static/image2.jpg)

**Figure 5**: la boîte de dialogue emplacement FTP choisir


## <a name="lab-setup-ftp-site-and-create-a-project"></a>Lab : Le programme d’installation FTP du site et de créer un projet

Les étapes suivantes configurent le site FTP afin qu’un utilisateur dispose d’un emplacement qui seulement ils peuvent télécharger vers via FTP.

### <a name="install-the-ftp-service"></a>Installer le Service FTP

1. Ouvrir Ajout/Suppression de programmes, sélectionnez Ajouter/supprimer des composants Windows
2. Sélectionnez Internet Information Services (serveur d’applications sur Windows 2003), puis cliquez sur **détails**.
3. Vérifiez **Service de transfert de protocole FTP (File)** et cliquez sur **OK**.
4. Cliquez sur **suivant** pour installer le service FTP.

### <a name="create-a-new-folder-for-content"></a>Créer un nouveau dossier pour le contenu

1. Dans l’Explorateur Windows, créez un dossier appelé **User1** dans c:/inetpub/wwwroot.

#### <a name="configure-folders-and-permissions-on-folders"></a>Configurer des dossiers et des autorisations sur les dossiers.

1. Ouvrez le composant logiciel enfichable Internet Information Services dans Outils d’administration. Vous avez maintenant un dossier Sites FTP sous le nœud de nom d’ordinateur.
2. Développez **Sites FTP**.
3. Cliquez sur le **Site FTP par défaut**, sélectionnez **nouveau**, puis **répertoire virtuel**, puis cliquez sur **suivant**.
4. Entrez **User1** pour le nom du répertoire virtuel et cliquez sur **suivant**.
5. Entrez **c:/inetpub/wwwroot/User1** pour le chemin d’accès et cliquez sur **suivant**.
6. Cliquez sur **suivant** , puis **Terminer** pour terminer l’Assistant.
7. Cliquez sur le **User1** répertoire virtuel sous le Site FTP par défaut et sélectionnez **propriétés**.
8. Vérifiez le **écrire** case à cocher et cliquez sur **OK** pour fermer la boîte de dialogue.
9. Avec le bouton droit **Site FTP par défaut** et sélectionnez **propriétés**.
10. Sur le **comptes de sécurité** onglet, décochez la case **autoriser les connexions anonymes**.
11. Cliquez sur **Oui** dans la boîte de dialogue vous demandant si vous souhaitez continuer.
12. Cliquez sur **OK** pour fermer la boîte de dialogue.
13. Développez le **Site Web par défaut** sous le **Sites Web** nœud.
14. Cliquez sur le **User1** directory et sélectionnez **propriétés**
15. Dans le **paramètres de l’Application** , cliquez sur **créer** pour marquer le dossier en tant qu’application.
16. Cliquez sur **OK** pour fermer la boîte de dialogue.
17. Fermez le composant logiciel enfichable Internet Information Services.

### <a name="create-web-project"></a>Créer un projet web

1. Ouvrez Visual Studio 2005.
2. À partir de la **fichier** menu, sélectionnez **nouveau Site Web**.
3. Dans le **emplacement** liste déroulante, sélectionnez **FTP**.
4. Cliquez sur **Parcourir**.
5. Entrez **localhost** dans les **Server** zone de texte.
6. Entrez **User1** dans la zone de texte active.
7. Cliquez sur **ouvrir**. L’emplacement FTP sera entrée dans la boîte de dialogue Nouveau Site Web.
8. Cliquez sur **OK**.
9. Décochez la case **ouvrir une session anonyme** dans la boîte de dialogue session FTP, entrez vos informations d’identification, puis cliquez sur **OK**.
10. Quelle est l’URL pour le projet ? (L’URL pour le projet s’affichera dans l’Explorateur de solutions.)
11. À partir de la **générer** menu, sélectionnez **générer le Site Web** ou **générer la Solution**.
12. Avec le bouton droit sur Default.aspx dans l’Explorateur de solutions et sélectionnez **afficher dans le navigateur**.
13. Dans la boîte de dialogue URL du Site Web requise, entrez `http://localhost/user1` pour l’URL et cliquez sur **OK**.

> [!NOTE]
> Si vous obtenez une erreur indiquant l’impossibilité de charger le type /_Default, assurez-vous que vous exécutez ASP.NET 2.0 sur votre site Web et pas une version antérieure. Vous pouvez le faire à partir de l’onglet ASP.NET dans Internet Information Services.


## <a name="opening-web-projects"></a>Ouverture de projets Web

Ouvrir des projets Web est similaire à la création de projets. Les sections suivantes indiquent les zones pour garder un œil out pour lors de l’utilisation de l’IDE. Elle traite également travailler avec les projets Web à l’aide de HTTP et FTP.

Pour ouvrir un projet Web, sélectionnez Ouvrir le Site Web dans le menu fichier. Vous serez invité à la même boîte de dialogue Choisir un emplacement décrit précédemment et que vous disposez des mêmes options de quatre à votre disposition : système de fichiers, serveur IIS Local, FTP et Site distant.

<a id="_Toc116100245"></a>

## <a name="file-system"></a>Système de fichiers

Comme indiqué précédemment dans ce module, Visual Studio n’utilise plus un fichier projet. Par conséquent, si vous choisissez d’ouvrir un site Web du système de fichiers, vous avez en fait la possibilité de choisir n’importe quel dossier que vous le souhaitez, même si le dossier que vous choisissez n’a pas été créé comme un projet Web initialement dans Visual Studio. Par exemple, vous pouvez choisir d’ouvrir le dossier Mes Documents comme un site Web et de Visual Studio heureusement ouvrir et afficher vos fichiers comme indiqué ci-dessous.


![Mes Documents ouverts comme un Site Web](improvements-in-visual-studio-2005/_static/image3.jpg)

**Figure 6**: *Mes Documents* ouvert comme un Site Web


Étant donné que Visual Studio crée uniquement des fichiers et des dossiers lorsque cela est nécessaire, sans fichiers ou dossiers supplémentaires sont ajoutés à l’emplacement que vous ouvrez. Un effet secondaire de cette architecture est qu’il empêche l’imbrication des sites Web sur le système de fichiers. Par exemple, considérez la structure de répertoire suivante.

Projet Web à C:/monsiteweb

Un autre projet web à C:/monsiteweb/imbriqués

Lorsque vous ouvrez le site Web à c:/monsiteweb, le dossier Nested apparaîtra comme un sous-dossier de l’application.

<a id="_Toc116100246"></a>

## <a name="http"></a>HTTP

Lors de l’ouverture de sites Web via HTTP, les paramètres sont lus à partir de la métabase IIS (IIS locaux) ou utilisant les Extensions serveur FrontPage (Site distant). S’il existe des applications web imbriqués, ceux-ci sont également affichées avec une icône qui les identifie comme une application. Si vous êtes familiarisé avec l’utilisation des applications web de FrontPage, le comportement de Visual Studio 2005 est similaire.

Bien que Visual Studio affiche une icône pour les applications qui sont imbriquées sous l’application qui est actuellement ouvert dans l’IDE, il vous autorisera pas à développer pour voir leur contenu. Vous pouvez, toutefois, double-cliquer dessus pour les ouvrir. Lorsque vous procédez ainsi, vous verrez une boîte de dialogue vous demandant d’ouvrir l’application web (et de remplacer la solution actuellement ouverte) ou ajoutez l’application Web à votre solution actuelle.


![Double-clic sur une icône d’application imbriquée vous présente cette boîte de dialogue](improvements-in-visual-studio-2005/_static/image4.jpg)

**Figure 7**: double-clic sur une icône d’application imbriquée vous présente cette boîte de dialogue


<a id="_Toc116100247"></a>

## <a name="ftp-site"></a>Site FTP

Lorsque vous ouvrez un site via FTP, les fichiers sont toutes copiées localement dans votre dossier temporaire. Le chemin d’accès complet pour l’emplacement de stockage local s’affiche dans le volet Propriétés pour le projet et est créé en utilisant le format suivant.

C:/Documents and Settings /&lt;utilisateur&gt;/Local paramètres/Temp/VWDWebCache/&lt;Server&gt;/_&lt;nom de l’application&gt;

Lorsque vous utilisez FTP, Visual Studio devez spécifier l’URL de base pour votre projet afin que vous pouvez le rechercher comme indiqué ci-dessous. Si vous ne spécifiez pas une URL de base, Visual Studio vous demande pour qu’il la première fois que vous essayez de parcourir une page dans le site Web.


![Spécification d’une URL de Base pour les Sites FTP](improvements-in-visual-studio-2005/_static/image5.jpg)

**Figure 8**: spécification d’une URL de Base pour les Sites FTP


## <a name="improvements-in-compilation"></a>Améliorations de la Compilation

Travailler avec des applications Web dans Visual Studio 2005 est nettement plus rapide que les versions antérieures. Il s’agit dans aucune petite partie les modifications apportées à l’architecture de la compilation.

Dans Visual Studio 2002 et 2003, les applications Web ont été compilées dans un assembly principal résidant dans le dossier/bin. Dans Visual Studio 2005, un dossier de l’application/_Code a été ajouté. Classes et tout autre code d’interface utilisateur sont ajoutés au dossier application/_Code. Lorsque Visual Studio génère le projet, tous les fichiers dans le dossier application/_Code sont compilées dans un seul fichier App/_Code.dll. Le résultat de cette modification est que les builds suivantes sont beaucoup plus rapides que dans les versions précédentes.

> [!NOTE]
> L’utilitaire de ligne de commande MSBuild peut également être utilisé pour créer des applications Web ASP.NET. Cet outil est abordé dans le module 9.


Une autre amélioration de compilation est la nouvelle option de générer une Page dans le menu Générer. Cette fonctionnalité permet à un développeur reconstruire la page en cours uniquement (avec, des cours et des dépendances) afin que les modifications puissent être compilées plus rapidement. Parce que c# ne propose pas de compilation en arrière-plan à des fins de mise à jour IntelliSense, etc., ils bénéficieront énormément cette fonctionnalité, car il permettre d’IntelliSense pour mettre à jour rapidement par la reconstruction de simplement une seule page.

Les propriétés de Build pour un projet vous autorise à configurer le type de build qui se produit avant l’exécution de la page de démarrage. Les développeurs peuvent choisir de générer uniquement la page active pour que Visual Studio puisse commencer à déboguer des applications après des modifications du code plus rapidement.


![L’Action de démarrage de la Page de Build](improvements-in-visual-studio-2005/_static/image6.jpg)

**Figure 9**: l’Action de démarrage de la Page de Build


Une autre amélioration de Visual Studio et l’architecture d’ASP.NET est dans la zone d’édition et continuer. Dans Visual Studio 2005, les développeurs peuvent commencer à déboguer un projet et apporter des modifications de code sur le projet sans détacher le débogueur. En fait, vous pouvez littéralement commencer à déboguer un projet, ajoutez une nouvelle classe, ajoutez du code à cette classe, ajouter du code à votre page qui crée une nouvelle instance de cette classe et exécution d’une méthode de la classe, le tout sans détacher le débogueur. L’exécution du nouveau code est littéralement aussi simple que d’actualiser le navigateur !

Cliquez ici pour voir une procédure pas à pas vidéo de la modifier et continuer la fonction dans Visual Studio 2005.


![](improvements-in-visual-studio-2005/_static/image2.png)


[Ouvrez plein écran](improvements-in-visual-studio-2005/_static/editcontinue1.wmv)


Robuste modifier et continue de fonctionnalités dans ASP.NET 2.0 et Visual Studio 2005 est en raison d’une modification d’architecture pour les applications ASP.NET. Dans ASP.NET 1.x, les applications créées dans Visual Studio 2002/2003 ont été compilés dans un assembly qui a été enregistrée dans le dossier/bin. Toutes les classes, les pages, etc. pour l’application ont été compilés dans ce une DLL. Lors de l’exécution, ASP.NET serait compiler tous les contrôles, le balisage et le code ASP.NET dans les pages puis copiez ces DLL dans le dossier temporaire ASP.NET.

Dans Visual Studio 2005 à l’aide d’ASP.NET 2.0, les modèles de deux compilation montrer ci-dessus (une pour Visual Studio) et l’autre pour ASP.NET lors de l’exécution ont été fusionnés dans un modèle commun de la compilation. Cela signifie que tous les problèmes de compilation sont maintenant interceptées pendant la phase de développement à la place de lors de l’exécution. Il permet également le concepteur et prise en charge IntelliSense pour les fonctionnalités telles que les contrôles utilisateur et les pages maîtres.

Cliquez ici pour voir une procédure pas à pas vidéo de prise en charge du concepteur pour les contrôles utilisateur.


![](improvements-in-visual-studio-2005/_static/image3.png)


[Ouvrez plein écran](improvements-in-visual-studio-2005/_static/usercontrols1.wmv)


> [!NOTE]
> Lorsqu’un contrôle utilisateur est supprimé d’une page, le @Register directive reste dans le balisage et doit être supprimée manuellement afin d’éviter les erreurs d’analyse si le contrôle utilisateur est supprimé à partir du site Web.


Une autre amélioration dans le modèle de compilation de Visual Studio est la fonctionnalité de publier le Site Web. Étant donné que la fonctionnalité de publication permet de précompiler le site Web, les développeurs puissent bénéficier des performances de ne pas disposer de compiler quoi que ce soit à la demande. Il précompilation également tout le code source dans le dossier application/_Code dans une DLL afin qu’aucun code source ne doit être déployé.


![La boîte de dialogue de Site Web de publication](improvements-in-visual-studio-2005/_static/image7.jpg)

**La figure 10**: la boîte de dialogue de Site Web de publication


> [!NOTE]
> L’utilitaire aspnet/_compile.exe peut également être utilisé pour précompiler une application Web ASP.NET. Cet outil est abordé dans le module 9.


Lorsque vous publiez un site Web, les fichiers précompilés sont stockés dans le dossier des fichiers ASP.NET temporaires comme indiqué ci-dessous. Des fichiers avec un *.compiled* extension de fichier sont des fichiers XML qui définissent des dépendances pour les DLL particulières. Des contrôles Webform ou utilisateur sont compilés dans des DLL aléatoires qui commencent par *application /_Web /_*.

Si vous laissez le *autoriser ce site précompilé à être mis à jour* case à cocher activée, le balisage à l’intérieur de vos contrôles Web Forms et de l’utilisateur ne sera pas précompilée dans une DLL, ce qui vous permet d’apporter des modifications après le déploiement. Si vous préférez verrouiller le balisage afin que les modifications dans le contenu déployé ne sont pas autorisées, désactivez cette case à cocher.

Le *utiliser fixe pour les assemblys d’affectation de noms et une page* case à cocher permet de désactiver la compilation par lots afin que chaque page est compilé dans un assembly à nom fixe. Désactivation de cette case permet de tirer parti de la compilation par lots.

Le *activer de noms forts sur les assemblys précompilés* case à cocher vous permet de nom fort vos assemblys précompilés.

> [!NOTE]
> Dans ASP.NET 1.x, assemblys avec nom fort devaient être installé dans le Global Assembly Cache (GAC). Dans ASP.NET 2.0, vous n’êtes pas requis pour installer les assemblys avec nom fort dans le GAC.


![Un fichiers précompilé des Applications ASP.NET](improvements-in-visual-studio-2005/_static/image8.jpg)

**Figure 11**: un fichiers précompilé des Applications ASP.NET


> [!NOTE]
> Dans l’application ci-dessus, il n’existe aucun fichier web.config. S’il avait été, il aurait été appelé *PrecompiledApp.config* après la publication Web de processus de site.


## <a name="improvements-in-deployment"></a>Améliorations du déploiement

Comme avec Visual Studio 2002 et 2003, Visual Studio 2005 propose une fonctionnalité de copier un projet. Toutefois, la fonctionnalité a été suffisante dans Visual Studio 2005 et se nomme désormais de copier le Site Web.

La boîte de dialogue Copier le Site Web est divisée en un cadre de gauche et une image de droite. Le cadre de gauche est appelé le Site Web Source et le cadre de droite est appelé le Site Web distant. Un élément qui peut confondre certains développeurs est que le site est affiché dans le cadre de droite n’est pas nécessairement un site distant. Cela peut être un site sur le système de fichiers local ou sur l’instance locale d’IIS. En outre, le site est affiché dans le cadre de gauche n’est pas nécessairement le site Web source, car la boîte de dialogue vous permet de publier à partir du site Web distant *à* du site Web source.

Si vous copiez un projet vers un site Web distant, ce site doit avoir les Extensions serveur FrontPage installé dessus. Si elle n’est pas le cas, vous devrez vous connecter à l’aide de FTP. En revanche, si vous copiez un projet à l’instance locale de IIS, les Extensions serveur FrontPage ne sont pas requises.

> [!NOTE]
> Si vous essayez de créer un nouveau site Web sur l’instance locale de IIS et les Extensions serveur FrontPage 2002 sont installés, vous obtiendrez un message d’erreur indiquant que la création de sites Web n’est pas supporté sur un serveur SharePoint. Dans ce cas, vous avez la possibilité d’installer les Extensions serveur FrontPage 2000 ou de suppression des Extensions serveur FrontPage.


Cliquez ici pour une procédure pas à pas vidéo de la fonctionnalité de copier le Site Web.


![](improvements-in-visual-studio-2005/_static/image4.png)


[Ouvrez plein écran](improvements-in-visual-studio-2005/_static/copysite1.wmv)


## <a name="improvements-in-debugging"></a>Améliorations de débogage

Il existe quatre améliorations clés dans le débogage dans Visual Studio 2005.

- Débogage local en tant que non administrateur est possible en dehors de la zone.
- L’attribut de débogage pour l’élément de Compilation est désormais la valeur false par défaut.
- Le programme d’installation et la configuration le débogage distant est plus facile qu’avant.
- Vous pouvez maintenant déboguer un site Web ouvert via un emplacement FTP.

## <a name="debugging-as-a-non-administrator"></a>Débogage en tant que Non administrateur

L’ajout du serveur de développement ASP.NET permet des non-administrateurs à déboguer facilement les applications ASP.NET à l’emploi. Lors du débogage est une application ASP.NET en cours d’exécution sur le système de fichiers local, Visual Studio lance le serveur de développement ASP.NET dans le contexte de l’utilisateur connecté. Cet utilisateur peut déboguer ensuite cette application sans aucune configuration supplémentaire.

## <a name="debug-is-false-by-default"></a>Debug est False par défaut

Dans ASP.NET 1.x, le *déboguer* d’attribut dans le *compilation* élément du fichier web.config a été défini sur *true* par défaut. Il a toujours été recommandé que les développeurs définissez cet attribut sur *false* avant de déployer une application en production, mais étant donné que la plupart des développeurs ne comprennent pas totalement les conséquences de laisser l’attribut de débogage défini sur la valeur est true, ils simplement gauche en tant que-est.

Le plus grave problème ayant l’attribut de débogage défini sur true est qu’elle désactive le modèle de compilation par lots ASP.NETs. Par conséquent, chaque page est compilé dans une DLL distincte. Si un serveur Web application se compose de milliers de pages (pas rare des par quelque moyen que), qui signifie que plusieurs DLL petits milliers seront créés par l’application. Alors que ces DLL est de petite taille, ils ne sont pas chargés dans n’importe quel emplacement particulier dans la mémoire. Par conséquent, ils provoquent la fragmentation dans la mémoire système et peuvent contribuer à OutOfMemoryException occurrences.

Dans ASP.NET 2.0, l’attribut de débogage est définie sur false par défaut. Comme vous l’avez déjà vu, lorsqu’un développeur débogue une application ASP.NET dans Visual Studio 2005, ils sont invités à ajouter un fichier web.config avec débogage activé. Cela pourrait entraîner les mêmes inconvénients qui étaient présents dans ASP.NET 1.x, mais maintenant le développeur est averti clairement que l’attribut doit être réinitialisé sur false avant le déplacement de l’application en production.

## <a name="remote-debugging-setup-and-configuration"></a>Configuration et installation du débogage à distance

Dans Visual Studio 2002/2003, le débogage distant reposait sur la Machine Debug Manager (mdm.exe) et le processus vs7jit.exe. Par conséquent, résolution des problèmes de débogage distants étaient souvent une boîte noire pour les clients et il était souvent pas préférable de support technique.

Visual Studio 2005 supprime la dépendance sur les processus mdm.exe et vs7jit.exe. Au lieu de cela, il utilise désormais le service Remote Debug Monitor (msvsmon.exe).

La configuration requise pour le débogage à distance dans Visual Studio 2005 est assez simple. Vous devez exécuter msvsmon.exe sur le serveur distant avant de débogage. Vous pouvez installer le Remote Debug Monitor à partir du CD-ROM Visual Studio, ou vous pouvez simplement exécuter msvsmon.exe à partir d’un partage sans rien installer tout sur le serveur Web.

Lorsque vous exécutez msvsmon.exe, il est probable qu’il sera contester les ports bloqués pour le débogage distant. Heureusement, vous pouvez facilement débloquer des ports de droite dans la boîte de dialogue d’avertissement comme indiqué ci-dessous.


![Notification que le pare-feu Windows bloque le débogage distant](improvements-in-visual-studio-2005/_static/image9.jpg)

**Figure 12**: est de Notification que le pare-feu Windows bloque le débogage distant


Une fois que vous avez débloqué les ports nécessaires pour le débogage, vous verrez le Remote Debugging Monitor comme indiqué ci-dessous. À partir de cette interface, vous pouvez surveiller les connexions et modifier facilement des autorisations de débogage.


![Remote Debugging Monitor](improvements-in-visual-studio-2005/_static/image10.jpg)

**Figure 13**: Remote Debugging Monitor


Il est également possible de déboguer à distance une application Web ouverte via FTP. Les étapes sont les mêmes que ceux précédemment couvertes. Toutefois, vous devez spécifier une URL de base pour parcourir le projet FTP comme décrit précédemment dans ce module.

## <a name="lab-2"></a>Atelier 2

## <a name="remote-debugging-with-visual-studio-2005"></a>Débogage distant avec Visual Studio 2005

Cet atelier vous guidera tout débogage distant avec Visual Studio 2005.

Cliquez ici pour une procédure pas à pas vidéo de ce laboratoire.


![](improvements-in-visual-studio-2005/_static/image5.png)


[Ouvrez plein écran](improvements-in-visual-studio-2005/_static/remdebug1.wmv)


Cet atelier nécessite de disposer de deux ordinateurs, un en cours d’exécution Visual Studio 2005 et autres en cours d’exécution IIS 5 ou ultérieure.

1. Ouvrez Visual Studio 2005 et créer un nouveau site Web sur le serveur distant.

> [!NOTE]
> Vous pouvez créer le site Web sur une instance IIS distante ou via FTP.


1. À partir du serveur Web à distance, localisez msvsmon.exe sur l’ordinateur de développement à l’aide d’un chemin d’accès UNC et exécutez-le.  
 L’emplacement par défaut pour msvsmon.exe est //server/c$/Program Files/Microsoft Visual Studio 8/Common7/IDE/Remote débogueur/x86.
2. Si vous êtes invité à débloquer des ports pour le débogage distant, le faire.
3. À partir de l’ordinateur de développement, ouvrez le fichier code-behind pour Default.aspx et définir un point d’arrêt dans la méthode de Page/_charge.
4. Démarrez le débogage à partir de l’ordinateur de développement.

Vous devez atteindre le point d’arrêt comme prévu.

## <a name="aspnet-development-server"></a>serveur de développement ASP.NET

Comme nous avons déjà mentionné, Visual Studio 2005 est livré avec un serveur Web appelé serveur de développement ASP.NET. (Le serveur de développement ASP.NET est parfois appelée Cassini.) Ce serveur Web est un moyen pratique pour parcourir et déboguer des applications Web en cours d’exécution sur le système de fichiers.

Le serveur de développement ASP.NET est un serveur Web restreint. Il n’autorise pas les connexions à distance, il n’autorise pas les demandes à partir de n’importe quel utilisateur autre que l’utilisateur qui a démarré le serveur Web. Aussi, il n’a pas la capacité de traitement des pages ASP. Uniquement les ressources ASP.NET et les ressources HTML (y compris les images, fichiers CSS, etc.) sont pris en charge.

Le serveur de développement ASP.NET peut être lancé via la ligne de commande en exécutant le fichier WebDev.WebServer.exe c:/Windows/Microsoft.NET/Framework/v2.0./ */*  /  */*/*. La boîte de dialogue affiche les paramètres qui sont disponibles.


![](improvements-in-visual-studio-2005/_static/image11.jpg)

**Figure 14**


> [!NOTE]
> Le serveur de développement ASP.NET n’est pas pris en charge lors du lancement explicitement via la ligne de commande.
