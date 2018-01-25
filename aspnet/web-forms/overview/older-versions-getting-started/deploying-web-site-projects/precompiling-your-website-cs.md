---
uid: web-forms/overview/older-versions-getting-started/deploying-web-site-projects/precompiling-your-website-cs
title: "La précompilation de votre site Web (c#) | Documents Microsoft"
author: rick-anderson
description: "Visual Studio offre les développeurs ASP.NET deux types de projets : projets d’Application Web (WAP) et les projets de Site Web (WSPs). Parmi le principales différences betwe..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/09/2009
ms.topic: article
ms.assetid: ecd5a4de-beb7-4d1d-bbbb-e31003633267
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deploying-web-site-projects/precompiling-your-website-cs
msc.type: authoredcontent
ms.openlocfilehash: f31f470b4d2b6736b98c0b7d88ea7a53ad1438b9
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="precompiling-your-website-c"></a>La précompilation de votre site Web (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/1/0/C/10CC829F-A808-4302-97D3-59989B8F9C01/ASPNET_Hosting_Tutorial_15_CS.zip) ou [télécharger le PDF](http://download.microsoft.com/download/5/C/5/5C57DB8C-5DEA-4B3A-92CA-4405544D313B/aspnet_tutorial15_Precompiling_cs.pdf)

> Visual Studio offre les développeurs ASP.NET deux types de projets : projets d’Application Web (WAP) et les projets de Site Web (WSPs). Une des principales différences entre les deux types de projets est que WAP doit avoir le code compilé explicitement avant le déploiement tandis que le code dans un WSP permettre être compilé automatiquement sur le serveur web. Toutefois, il est possible de précompilation d’un WSP avant le déploiement. Ce didacticiel présente les avantages de la précompilation et montre la précompilation d’un site Web à partir de Visual Studio et à partir de la ligne de commande.


## <a name="introduction"></a>Introduction

Visual Studio offre les développeurs ASP.NET deux différents types de projet : projets d’Application Web (WAP) et les projets de Site Web (WSP). Une des principales différences entre ces types de projets est que WAP requiert *compilation explicite* WSPs utiliser *compilation automatique*, par défaut. Avec WAP, vous compilez le code de l’application web dans un assembly unique, qui est créé dans le site Web `Bin` dossier. Déploiement implique la copie du contenu de balisage (le `.aspx.ascx`, et `.master` fichiers) dans le projet, ainsi que l’assembly dans le `Bin` dossier ; le code-behind des fichiers de classe eux-mêmes ne doivent pas être déployés. En revanche, vous déployez WSPs en copiant les pages de balisage et de leurs classes de code-behind correspondant à l’environnement de production. Les classes de code-behind sont compilées à la demande sur le serveur web.

> [!NOTE]
> Faire référence à la section « Explicite Compilation par rapport à une Compilation automatique » dans le [ *déterminer quels fichiers doivent être déployés* didacticiel](determining-what-files-need-to-be-deployed-cs.md) pour plus d’informations sur les différences entre le projet modèles de compilation explicite et automatique et comment le modèle de compilation affecte le déploiement.


L’option de compilation automatique est simple à utiliser. Aucune étape de compilation explicite, et seuls les fichiers qui ont été modifiées doivent être déployés, tandis que la compilation explicite et nécessite de déployer les pages modifiées de balisage et l’assembly compilé juste-. Toutefois, le déploiement automatique présente deux inconvénients potentiels :

- Étant donné que les pages doivent être compilées automatiquement lorsqu’ils sont tout d’abord visités, il peut y avoir un délai court, mais néanmoins notable lorsqu’une page ASP.NET est demandée pour la première fois après son déploiement.
- Compilation automatique requiert que les deux le balisage et la source de code déclaratif sur le serveur web. Cela peut être une option non si vous prévoyez de vente de l’application web pour les clients qui sera installé sur leurs serveurs web.

Si soit de deux au-dessus de défauts lexicaux de la transaction, vous pouvez soit basculer vers le modèle WAP ou *précompiler* WSP avant le déploiement. Ce didacticiel examine les options de précompilation mieux adaptées pour un site Web hébergé et guide à travers le processus de précompilation et le déploiement d’un site Web précompilé.

## <a name="an-overview-of-aspnet-code-generation-and-compilation"></a>Une vue d’ensemble de la génération du Code ASP.NET et la Compilation

Avant d’examiner les options de précompilation disponibles, tout d’abord parlons la génération de code et la compilation qui se produit lorsqu’une page ASP.NET est demandée pour la première fois, car il a été créé ou mis à jour. Comme vous le savez, les pages ASP.NET sont composés de deux parties : un balisage déclaratif dans le `.aspx` fichier et une partie du code source, généralement dans un fichier de classe code-behind distinct (`.aspx.cs`). Les étapes effectuées par le runtime lorsqu’une page ASP.NET est demandée dépend du modèle de compilation de l’application.

Avec WAP, code de source de la pages doit être explicitement compilé dans un assembly unique avant le déploiement. Pendant le déploiement, cet assembly et les différentes pages de balisage sont copiés dans l’environnement de production. Lorsqu’une demande arrive sur le serveur web pour une page ASP.NET, le runtime crée une instance de la classe code-behind de la page et appelle son `ProcessRequest` (méthode), qui démarre le cycle de vie de page et, finalement, génère le contenu de page, qui est retourné à le demandeur. Le runtime peut fonctionne à la classe code-behind de la page ASP.NET, car la classe code-behind a déjà été compilée dans un assembly avant le déploiement.

Avec WSPs et compilation automatique, il n’existe aucune étape de compilation explicite avant le déploiement. Au lieu de cela, déploiement implique la copie des déclaratif et le contenu du code source dans l’environnement de production. Lorsqu’une demande arrive sur le serveur web pour une page ASP.NET pour la première fois, car la page a été créée ou mises à jour, le runtime doit compiler tout d’abord la classe code-behind dans un assembly. Cet assembly compilé est enregistré dans le dossier `%WINDIR%\Microsoft.NET\Framework\v2.0.50727\Temporary ASP.NET Files`, bien que l’emplacement de ce dossier peut être personnalisé la `<compilation tempDirectory="" />` élément de `<system.web>`, généralement dans `Web.config`. Étant donné que l’assembly est enregistré sur le disque, il n’a pas besoin d’être recompilé pour les demandes suivantes à la même page.

> [!NOTE]
> Évidemment, il existe un léger délai lors de la demande d’une page pour la première fois (ou pour la première fois, car elle a été modifiée) dans un site qui utilise une compilation automatique comme il prend quelques minutes pour le serveur à compiler le code de la page et enregistrer l’assembly résultant de disque.


En bref, avec la compilation explicite vous sont nécessaires pour compiler le code source du site Web avant le déploiement, l’enregistrement de l’exécution d’avoir à effectuer cette étape. Avec la compilation automatique le runtime gère la compilation de code source des pages, mais avec un coût d’initialisation légère pour la première visite sur la page, car il a été créé ou mis à jour.

Mais qu’en est-il de la partie déclarative des pages ASP.NET (la `.aspx` fichier) ? Il est évident qu’il existe une relation entre la `.aspx` le code dans leurs classes de code-behind, comme les contrôles Web définis dans le balisage déclaratif et les fichiers sont accessibles dans le code. Il est également évident que le contenu de la `.aspx` fichiers influence le balisage rendu généré par la page. Comment fonctionne l’exécution avec le texte, HTML, et syntaxe de contrôle Web définie dans le `.aspx` fichier permettant de générer la page demandée du rendu de contenu ?

Je ne souhaite pas obtenir trop dévié de votre sujet sur les détails d’implémentation de bas niveau, qui varient entre WAP et WSPs, mais en bref, le runtime génère automatiquement un fichier de classe qui contient les différents contrôles Web en tant que les méthodes et les membres protégés. Ce fichier généré est implémenté comme un *classe partielle* à la classe code-behind correspondant. ([Classes partielles](http://www.dotnet-guide.com/partialclasses.html) autoriser pour le contenu d’une seule classe d’être réparties entre plusieurs fichiers.) Par conséquent, la classe code-behind est définie dans deux emplacements : dans le `.aspx.cs` fichier que vous créez et dans cette classe générée automatiquement créé par le runtime. Cette classe généré automatiquement est stockée dans le `%WINDIR%\Microsoft.NET\Framework\v2.0.50727\Temporary ASP.NET Files` dossier.

L’important d’empiéter ici est que pour une page ASP.NET doit restituer l’exécution à la fois son déclaratives et les parties du code source doivent être compilés dans un assembly. Avec WAP, le code source est explicitement compilé dans un assembly avant le déploiement, mais le balisage déclaratif doit toujours être converti dans le code et compilé par le runtime sur le serveur web. Avec WSPs à l’aide de la compilation automatique, le code source et le balisage déclaratif doivent être compilés par le serveur web.

Il est possible d’utiliser la compilation explicite avec le modèle WSP. Vous pouvez explicitement compiler la partie du code source, comme avec le modèle WAP. De plus, vous pouvez aussi compiler le balisage déclaratif.

## <a name="precompilation-options"></a>Options de précompilation

Le .NET Framework est fourni avec un [outil de compilation ASP.NET (`aspnet_compiler.exe`)](https://msdn.microsoft.com/library/ms229863.aspx) qui vous permet de compiler le code source (et même le contenu) d’une application ASP.NET créée à l’aide du modèle WSP. Cet outil a été publié avec la version 2.0 du .NET Framework et se trouve dans le `%WINDIR%\Microsoft.NET\Framework\v2.0.50727` dossier ; il peut être utilisé à partir de la ligne de commande ou lancé au sein de Visual Studio via l’option de publier le Site Web du menu Build.

L’outil de compilation fournit deux formes générales de la compilation : la précompilation pour le déploiement et la précompilation sur place. Avec la précompilation sur place, vous exécutez le `aspnet_compiler.exe` outil à partir de la ligne de commande et spécifiez le chemin d’accès du répertoire virtuel ou un chemin d’accès physique d’un site Web qui se trouve sur votre ordinateur. L’outil de compilation compile ensuite chaque page ASP.NET dans le projet, le stockage de la version compilée dans le `%WINDIR%\Microsoft.NET\Framework\v2.0.50727\Temporary ASP.NET Files` dossier comme si les pages avaient chacun été visités pour la première fois à partir d’un navigateur. Précompilation sur place peut accélérer la première demande effectuée dans des pages ASP.NET qui vient d’être déployés sur votre site, car elle évite le runtime d’avoir à effectuer cette étape. Toutefois, la précompilation sur place n’est pas utile pour la majorité des sites Web hébergés, car elle requiert que vous êtes en mesure d’exécuter des programmes à partir du serveur web en ligne de commande. Dans les environnements d’hébergement partagés, ce niveau d’accès n’est pas autorisé.

> [!NOTE]
> Pour plus d’informations sur la précompilation sur place, consultez [Comment : précompiler des Sites Web ASP.NET](https://msdn.microsoft.com/library/ms227972.aspx) et [la précompilation dans ASP.NET 2.0](http://www.odetocode.com/Articles/417.aspx).


Au lieu de compiler les pages du site Web à le `Temporary ASP.NET Files` dossier, la précompilation pour le déploiement compile les pages dans un répertoire de votre choix et dans un format qui peut être déployé dans l’environnement de production.

Il existe deux variantes de la précompilation pour le déploiement de ce didacticiel se penche : la précompilation avec une interface utilisateur de mettre à jour et la précompilation avec une interface utilisateur non modifiable. La précompilation avec une interface utilisateur de mettre à jour laisse le balisage déclaratif dans le `.aspx`, `.ascx`, et `.master` fichiers, permettant ainsi à un développeur afficher et, si vous le souhaitez, modifiez le balisage déclaratif sur le serveur de production. La précompilation avec une interface utilisateur non modifiable génère `.aspx` des pages void du contenu et supprime `.ascx` et `.master` fichiers, pour masquer le balisage déclaratif et interdisant un développeur de le modifier à partir de la environnement de production.

### <a name="precompiling-for-deployment-with-an-updatable-user-interface"></a>Précompilation pour le déploiement avec une Interface utilisateur de mettre à jour

La meilleure façon de comprendre la précompilation pour le déploiement est pour voir un exemple en action. Nous allons précompiler Book révisions WSP pour le déploiement à l’aide d’une interface utilisateur de mettre à jour. L’outil de compilation ASP.NET peut être appelée à partir du menu de génération de Visual Studio ou à partir de la ligne de commande. Cette section examine à l’aide de l’outil à partir de Visual Studio ; la section « précompilation à partir de la ligne de commande » examine l’outil compilateur d’en cours d’exécution à partir de la ligne de commande.

Ouvrez WSP révision Book dans Visual Studio, dans le menu Build et sélectionnez l’option de menu Publier le Site Web. Cette action lance la boîte de dialogue Publier le Site Web (consultez **Figure 1**), où vous pouvez spécifier l’emplacement cible, ou non l’interface utilisateur du site précompilé est modifiable et autres options de l’outil du compilateur. L’emplacement cible peut être un serveur web à distance ou un serveur FTP, mais pour l’instant, choisissez un dossier sur votre disque dur. Étant donné que nous souhaitons précompiler le site avec une interface utilisateur de mettre à jour, laissez la case à cocher « Autoriser ce site précompilé à être mis à jour » activée, puis cliquez sur OK.

[![](precompiling-your-website-cs/_static/image2.png)](precompiling-your-website-cs/_static/image1.png)

**Figure 1**: l’outil de Compilation ASP.NET sera précompiler votre site Web à l’emplacement cible spécifiée  
 ([Cliquez pour afficher l’image en taille réelle](precompiling-your-website-cs/_static/image3.png))

> [!NOTE]
> L’option Publier le Site Web dans le menu Build n’est pas disponible dans Visual Web Developer. Si vous utilisez Visual Web Developer, vous devez utiliser la version de ligne de commande de l’outil de compilation ASP.NET, qui est décrite dans la section « précompilation à partir de la ligne de commande ».


Après avoir précompilé le site Web, accédez à l’emplacement cible que vous avez entré dans la boîte de dialogue Publier le Site Web. Prenez un moment pour comparer le contenu de ce dossier avec le contenu de votre site Web. **Figure 2** affiche le dossier de site Web critiques. Notez qu’il contient à la fois `.aspx` et `.aspx.cs` fichiers. Notez également que la `Bin` répertoire n'inclut qu’un seul fichier, `Elmah.dll`, lequel nous avons ajouté dans le [didacticiel précédent](logging-error-details-with-elmah-cs.md)

[![](precompiling-your-website-cs/_static/image5.png)](precompiling-your-website-cs/_static/image4.png)

**Figure 2**: le répertoire du projet contient `.aspx` et `.aspx.cs` fichiers ; le `Bin` dossier inclut uniquement`Elmah.dll`  
 ([Cliquez pour afficher l’image en taille réelle](precompiling-your-website-cs/_static/image6.png))

**Figure 3** affiche le dossier d’emplacement cible dont le contenu ont été créé par l’outil de compilation ASP.NET. Ce dossier ne contient pas tous les fichiers code-behind. En outre, de ce dossier `Bin` répertoire inclut plusieurs assemblys et deux `.compiled` fichiers à la `Elmah.dll` assembly.

[![](precompiling-your-website-cs/_static/image8.png)](precompiling-your-website-cs/_static/image7.png)

**Figure 3**: le dossier d’emplacement cible inclut les fichiers pour le déploiement  
 ([Cliquez pour afficher l’image en taille réelle](precompiling-your-website-cs/_static/image9.png))

Contrairement à la compilation explicite dans WAP, la précompilation pour le processus de déploiement ne crée pas un assembly pour l’ensemble du site. Au lieu de cela, il lots ensemble plusieurs pages dans chaque assembly. Il compile également la `Global.asax` de fichiers (le cas échéant) dans son propre assembly, ainsi que toutes les classes dans le `App_Code` dossier. Les fichiers qui contiennent le balisage déclaratif pour ASP.NET web pages, les contrôles utilisateur et les pages maîtres (`.aspx`, `.ascx`, et `.master` des fichiers, respectivement) sont copiés en tant que-dans le répertoire d’emplacement cible. De même, le `Web.config` fichier est copié directement, ainsi que les fichiers statiques, tels que des images, des classes CSS et des fichiers PDF. Pour obtenir une description plus formelle de la façon dont l’outil de compilation gère divers types de fichiers, reportez-vous à [gestion de fichier au cours de précompilation ASP.NET](https://msdn.microsoft.com/library/e22s60h9.aspx).

> [!NOTE]
> Vous pouvez demander à l’outil de compilation pour créer un assembly par page ASP.NET, contrôle utilisateur ou page maître en activant la case à cocher « Utilisé fixé d’affectation de noms et assemblys de page unique » à partir de la boîte de dialogue Publier le Site Web. Chaque page ASP.NET compilée dans son propre assembly permet pour un contrôle plus précis sur le déploiement. Par exemple, si vous mis à jour d’une page web ASP.NET et nécessaires au déploiement de cette modification, vous devez déployer uniquement de cette page `.aspx` de fichiers et de l’assembly associé à l’environnement de production. Consultez [Comment : générer des noms fixes avec l’outil de Compilation ASP.NET](https://msdn.microsoft.com/library/ms228040.aspx) pour plus d’informations.


Le répertoire d’emplacement cible contient également un fichier qui ne fait pas partie du projet web précompilé, à savoir `PrecompiledApp.config`. Ce fichier informe le runtime ASP.NET que l’application a été précompilée et si elle a été précompilée avec une interface utilisateur de mettre à jour ou MIDI modifiable.

Enfin, prenez un moment pour ouvrir l’un de le `.aspx` fichiers à l’emplacement cible à l’aide de Visual Studio ou votre éditeur de texte de choix. Lors de la précompilation pour le déploiement avec une interface utilisateur de mettre à jour, les pages ASP.NET dans le répertoire d’emplacement cible contiennent le balisage exact de même que les fichiers correspondants sur le site Web.

### <a name="precompiling-for-deployment-with-a-non-updatable-user-interface"></a>Précompilation pour le déploiement avec une Interface utilisateur Non modifiable

L’outil compilateur de ASP.NET peut également servir à précompilation d’un site pour le déploiement avec une interface utilisateur non modifiable. Précompiler le site avec une interface utilisateur non modifiable fonctionne comme la précompilation avec une interface utilisateur de mettre à jour, la principale différence étant que les pages ASP.NET, les contrôles utilisateur et les pages maîtres dans le répertoire cible sont supprimés de leurs balises. Pour la précompilation d’un site Web pour le déploiement avec une interface utilisateur non modifiable, choisissez l’option Publier le Site Web dans le menu Générer, mais désactivez l’option « Autoriser ce site précompilé à être mis à jour » (voir **Figure 4**).

[![](precompiling-your-website-cs/_static/image11.png)](precompiling-your-website-cs/_static/image10.png)

**Figure 4**: désactiver le « Autoriser ce site précompilé à être mis à jour » Option à précompiler avec un Non modifiable l’interface utilisateur  
 ([Cliquez pour afficher l’image en taille réelle](precompiling-your-website-cs/_static/image12.png))

**Figure 5** affiche le dossier d’emplacement cible après la précompilation avec une interface utilisateur non modifiable.

[![](precompiling-your-website-cs/_static/image14.png)](precompiling-your-website-cs/_static/image13.png)

**Figure 5**: le dossier d’emplacement cible pour le déploiement avec une interface utilisateur Non modifiable  
 ([Cliquez pour afficher l’image en taille réelle](precompiling-your-website-cs/_static/image15.png))

Comparer **Figure 3** à **Figure 5**. Alors que les deux dossiers semblent identiques, notez que le dossier non modifiable de l’interface utilisateur ne dispose pas de la page maître, `Site.master`. Et pendant que **Figure 5** inclut les différentes pages ASP.NET, si vous affichez le contenu de ces fichiers, vous verrez avoir été supprimés de leur balisage déclaratif et remplacés par le texte d’espace réservé : « Ceci est un fichier de marquage généré par l’outil de précompilation et ne doit pas être supprimé ! »

[![](precompiling-your-website-cs/_static/image17.png)](precompiling-your-website-cs/_static/image16.png)

**Figure 5**: le balisage déclaratif a été supprimé à partir des Pages ASP.NET

Le `Bin` dans les dossiers **Figures 3** et **5** diffèrent plus sensiblement. En plus des assemblys, les `Bin` dossier **Figure 5** inclut un `.compiled` fichier pour chaque page ASP.NET, le contrôle utilisateur et la page maître.

La précompilation d’un site avec une interface utilisateur non modifiable est utile dans les situations où vous ne souhaitez pas de contenu des pages ASP.NET doivent être modifiées par la personne ou de la société qui installe ou gère le site Web dans l’environnement de production. Si vous générez une application web ASP.NET que vous vendez aux clients à installer sur leurs propres serveurs web, vous souhaiterez vous assurer qu’ils ne modifient pas l’apparence de votre site en modifiant directement le `.aspx` pages de les fournir. Par la précompilation de votre site Web avec une interface utilisateur non modifiable, vous expédiez l’espace réservé `.aspx` pages dans le cadre de l’installation, ce qui évite que vos clients d’examen ou de modifier leur contenu.

### <a name="precompiling-from-the-command-line"></a>La précompilation à partir de la ligne de commande

Arrière-plan, boîte de dialogue Publier le Site Web de Visual Studio appelle l’outil de compilation ASP.NET (`aspnet_compiler.exe`) pour précompiler le site Web. Vous pouvez également appeler cet outil à partir de la ligne de commande. En fait, si vous utilisez Visual Web Developer puis vous devrez exécuter l’outil compilateur de la ligne de commande, comme le menu Générer du Visual Web Developer n’inclut pas l’option Publier le Site Web.

Pour utiliser l’outil compilateur de ligne de commande, démarrez en suppression à la ligne de commande et en accédant au répertoire du framework, `%WINDIR%\Microsoft.NET\Framework\v2.0.50727`. Ensuite, entrez l’instruction suivante dans la ligne de commande :

`aspnet_compiler -p "physical_path_to_app" -v / -f -u "target_location_folder"`

La commande ci-dessus lance l’outil compilateur de ASP.NET (`aspnet_compiler.exe`) et via le `-p` commutateur, la charge de précompiler le site Web enraciné au *physique\_chemin d’accès\_à\_application*; Cette valeur doit être un nom tel que `C:\MySites\BookReviews`et doivent être délimitées par des guillemets doubles.

Le `-v` commutateur spécifie le répertoire virtuel du site. Si votre site est enregistré en tant que le site Web de valeur par défaut dans la métabase IIS, vous pouvez omettre le `-p` basculer et il suffit de spécifier le répertoire virtuel de l’application. Si vous utilisez la `-p` basculer, la procédure de la valeur du `-v` commutateur indique la racine du site Web et est utilisé pour résoudre les références de la racine de l’application. Par exemple, si vous spécifiez une valeur de `-v /MySite` puis référence dans l’application pour `~/path/file` sera résolu en tant que `~/MySite/path/file`. Étant donné que le site critiques se trouve dans le répertoire racine à ma société d’hébergement web, j’ai utilisé le commutateur `-v /`.

Le `-f` commutateur, le cas échéant, indique à l’outil de compilation pour remplacer le *cible\_emplacement\_dossier* active s’il existe déjà. Si vous omettez le `-f` commutateur et le dossier d’emplacement cible existe déjà, l’outil de compilation se ferment avec l’erreur : « erreur ASPRUNTIME : le répertoire cible n’est pas vide. Supprimez-le manuellement ou choisissez une autre cible. »

Le `-u` commutateur, le cas échéant, informe l’outil pour créer une interface utilisateur de mettre à jour. Ignorez ce commutateur pour précompiler le site avec une interface utilisateur non modifiable.

Enfin, le *cible\_emplacement\_dossier* est le chemin d’accès physique vers le répertoire d’emplacement cible ; cette valeur doit être un nom tel que `C:\MySites\Output\BookReviews`et doivent être délimitées par des guillemets doubles.

## <a name="deploying-the-precompiled-website"></a>Déploiement du site Web précompilé

À ce stade, nous avons vu comment utiliser l’outil de compilation ASP.NET pour la précompilation d’un site Web à l’aide de ces deux options d’interface utilisateur de mettre à jour et non modifiable. Toutefois, nos exemples jusque-là aient précompilé le site Web dans un dossier local et pas à l’environnement de production. La bonne nouvelle est que déployer le site Web précompilé est très simple et peut être effectué via Visual Studio ou un autre mécanisme de copie de fichier, comme à partir d’un client FTP autonome.

La boîte de dialogue Publier le Site Web (affiché dans la première **Figure 1**) dispose d’une option d’emplacement cible, qui indique où les fichiers du site Web précompilé sont copiés dans. Cet emplacement peut être un serveur web à distance ou un serveur FTP. En entrant un serveur distant dans cette zone de texte permet de précompiler et déploie le site Web sur le serveur spécifié en une seule étape. Vous pouvez également précompiler le site Web dans un dossier local et ensuite copier manuellement le contenu de ce dossier à l’environnement de production via FTP ou une autre approche.

Site Web précompilé automatiquement déployé via la boîte de dialogue Publier le Site Web de Visual Studio s’avère utile pour les sites simples où il n’existe aucune différence de configuration entre les environnements de développement et de production. Toutefois, comme indiqué dans le [ *les différences entre développement Configuration commune et Production* didacticiel](common-configuration-differences-between-development-and-production-cs.md) n’est pas rare que les différences d’exister. Par exemple, l’application web critiques utilise une autre base de données dans l’environnement de production que dans l’environnement de développement. Quand Visual Studio publie le site Web à un serveur distant, qu'il copie les informations du fichier de configuration dans l’environnement de développement.

Pour les sites avec des différences de configuration entre les environnements de développement et de production, il peut être préférable de précompiler le site dans un répertoire local, recopiez les fichiers de configuration spécifiques à la production, puis copiez le contenu de la sortie précompilé production.

Pour un petit rappel sur la copie des fichiers à partir de l’environnement de développement sur l’environnement de production, reportez-vous à la [ *déploiement de votre site Web à l’aide d’un FTP Client* ](deploying-your-site-using-an-ftp-client-cs.md) et [  *Déploiement de votre site Web à l’aide de Visual Studio* ](determining-what-files-need-to-be-deployed-cs.md) didacticiels.

## <a name="summary"></a>Récapitulatif

ASP.NET prend en charge deux modes de compilation : automatique et explicite. Comme expliqué dans les didacticiels précédents, les projets d’Application Web (WAP) utilisation de la compilation explicite tandis que les projets de Site Web (WSPs) utilisation de la compilation automatique, par défaut. Toutefois, il est possible de compiler un WSP avant le déploiement à l’aide de l’outil de compilation ASP.NET.

Ce didacticiel se concentre sur la précompilation de l’outil de compilation pour la prise en charge du déploiement. Lors de la précompilation pour le déploiement, l’outil de compilation crée un dossier d’emplacement cible Compile le code source de l’application web spécifié et les copie compilé les assemblys et les fichiers de contenu dans le dossier d’emplacement cible. L’outil de compilation peut être configuré pour créer une interface utilisateur de mettre à jour ou non modifiable. Lors de la précompilation avec une interface utilisateur non modifiable, le balisage déclaratif dans les fichiers de contenu est supprimé. En bref, la précompilation vous autorise à déployer votre application basée sur le projet de Site Web sans y compris les fichiers de code source et le balisage déclaratif supprimé, si vous le souhaitez.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Précompilation du Site Web ASP.NET](https://msdn.microsoft.com/library/ms228015.aspx)
- [Code-behind et la Compilation dans ASP.NET 2.0](https://msdn.microsoft.com/magazine/cc163675.aspx)
- [Précompilation dans ASP.NET](http://www.odetocode.com/Articles/417.aspx)
- [Options du Site précompilé dans ASP.NET](http://www.dotnetperls.com/precompiled)

>[!div class="step-by-step"]
[Précédent](logging-error-details-with-elmah-cs.md)
[Suivant](users-and-roles-on-the-production-website-cs.md)
