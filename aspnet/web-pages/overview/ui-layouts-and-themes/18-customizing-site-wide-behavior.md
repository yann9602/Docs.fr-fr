---
uid: web-pages/overview/ui-layouts-and-themes/18-customizing-site-wide-behavior
title: "Personnalisation du comportement de l’échelle du Site pour ASP.NET Web Pages (Razor) Sites | Documents Microsoft"
author: tfitzmac
description: "Ce chapitre explique comment définir des paramètres de votre site Web ou un dossier entier, au lieu de simplement une page."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/17/2014
ms.topic: article
ms.assetid: e158bed7-226f-4275-b02e-7553bd58c669
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/18-customizing-site-wide-behavior
msc.type: authoredcontent
ms.openlocfilehash: b1caa26a23517bd976addfefac89375ae965eb91
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="customizing-site-wide-behavior-for-aspnet-web-pages-razor-sites"></a>Personnalisation du comportement de l’échelle du Site pour les Sites ASP.NET Web Pages (Razor)
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article explique comment définir des paramètres de site côté pour les pages dans un site Web ASP.NET Web Pages (Razor).
> 
> Ce que vous allez apprendre :
> 
> - Comment faire pour exécuter du code qui vous permet de jeu de valeurs (valeurs globales ou les paramètres d’assistance) pour toutes les pages dans un site.
> - Comment exécuter le code qui vous permet de définir des valeurs pour toutes les pages dans un dossier.
> - Charge de l’exécution de code avant et après une page.
> - Comment envoyer les erreurs à une page d’erreurs centrale.
> - Comment ajouter une authentification à toutes les pages dans un dossier.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 2
> - WebMatrix 3
> - ASP.NET Web Helpers Library (package NuGet)
>   
> 
> Ce didacticiel fonctionne également avec ASP.NET Web Pages 3 et Visual Studio 2013 (ou Visual Studio Express 2013 pour le Web), sauf que vous ne pouvez pas utiliser la bibliothèque de programmes d’assistance ASP.NET Web.


<a id="Adding_Website_Startup_Code"></a>
## <a name="adding-website-startup-code-for-aspnet-web-pages"></a>Ajout de Code de démarrage du site Web pour les Pages Web ASP.NET

Pour une grande partie du code que vous écrivez dans les Pages Web ASP.NET, une page individuelle peut contenir tout le code qui est requis pour cette page. Par exemple, si une page envoie un message électronique, il est possible de placer tout le code pour cette opération dans une seule page. Cela peut inclure le code pour initialiser les paramètres pour l’envoi de courrier électronique (autrement dit, pour le serveur SMTP) et pour l’envoi du message électronique.

Toutefois, dans certaines situations, vous souhaiterez exécuter du code avant l’exécution de n’importe quelle page sur le site. Cela est utile pour définir les valeurs qui peuvent être utilisées partout dans le site (appelé *valeurs globales*.) Par exemple, certains programmes d’assistance, que vous devez fournir des valeurs telles que les paramètres de messagerie ou les clés de compte. Il peut être utile de conserver ces paramètres dans les valeurs globales.

Vous pouvez pour cela créer une page nommée  *\_AppStart.cshtml* à la racine du site. Si cette page existe, il s’exécute la première fois à n’importe quelle page dans le site est demandée. Par conséquent, il est judicieux d’exécuter du code pour définir des valeurs globales. (Étant donné que  *\_AppStart.cshtml* possède un préfixe de trait de soulignement, ASP.NET n’envoie pas la page à un navigateur même si les utilisateurs demandent directement.)

Le diagramme suivant montre comment la  *\_AppStart.cshtml* page fonctionne. Lorsqu’une demande arrive pour une page, et s’il s’agit la première demande d’une page dans le site, ASP.NET vérifie en premier lieu si un  *\_AppStart.cshtml* page existe. Dans ce cas, tout code dans le  *\_AppStart.cshtml* page s’exécute et exécute la page demandée.

![[image]](18-customizing-site-wide-behavior/_static/image1.jpg)

## <a name="setting-global-values-for-your-website"></a>Définition des valeurs globales pour votre site Web

1. Dans le dossier racine d’un site Web WebMatrix, créez un fichier nommé  *\_AppStart.cshtml*. Le fichier doit être à la racine du site.
2. Remplacez le contenu existant avec les éléments suivants : 

    [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample1.cshtml)]

    Ce code stocke une valeur dans la `AppState` dictionnaire, qui est automatiquement disponible pour toutes les pages du site. Notez que la  *\_AppStart.cshtml* fichier n’est pas tout balisage qu’il contient. La page exécuter le code et puis rediriger vers la page initialement demandée.

    > [!NOTE]
    > Soyez prudent lorsque vous placez le code dans le  *\_AppStart.cshtml* fichier. Si des erreurs se produisent dans le code dans le  *\_AppStart.cshtml* fichier, le site Web ne démarre pas.
3. Dans le dossier racine, créez une nouvelle page nommée *AppName.cshtml*.
4. Remplacez le balisage par défaut et le code avec les éléments suivants : 

    [!code-html[Main](18-customizing-site-wide-behavior/samples/sample2.html)]

    Ce code extrait la valeur à partir de la `AppState` objet que vous définissez dans le  *\_AppStart.cshtml* page.
5. Exécutez le *AppName.cshtml* page dans un navigateur. (Assurez-vous que la page est sélectionnée dans le **fichiers** espace de travail avant de l’exécuter.) La page affiche la valeur globale. 

    ![[image]](18-customizing-site-wide-behavior/_static/image2.jpg)

<a id="Setting_Values_For_Helpers"></a>
## <a name="setting-values-for-helpers"></a>Définition des valeurs pour les programmes d’assistance

Une utilisation optimale pour la  *\_AppStart.cshtml* fichier consiste à définir des valeurs pour les applications d’assistance que vous utilisez dans votre site et qui doivent être initialisés. Exemples : paramètres de messagerie pour le `WebMail` programme d’assistance et les clés publiques et privées pour le `ReCaptcha` helper. Dans ce cas, vous pouvez définir les valeurs qu’une seule fois dans le  *\_AppStart.cshtml* et puis ils sont déjà définies pour toutes les pages de votre site.

Cette procédure vous montre comment définir `WebMail` paramètres globalement. (Pour plus d’informations sur l’utilisation de la `WebMail` assistance, consultez [Ajout du courrier électronique à un Site de Pages Web ASP.NET](../getting-started/11-adding-email-to-your-web-site.md).)

1. Ajoutez la bibliothèque de programmes d’assistance ASP.NET Web à votre site Web, comme décrit dans [programmes d’assistance de l’installation dans un Site de Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=252372), si vous n’avez pas déjà ajouté.
2. Si vous n’avez pas déjà un  *\_AppStart.cshtml* de fichiers, dans le dossier racine d’un site Web, créez un fichier nommé  *\_AppStart.cshtml*.
3. Ajoutez le code suivant `WebMail` paramètres pour le  *\_AppStart.cshtml* fichier : 

    [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample3.cshtml?highlight=2-7)]

    Modifier les paramètres associés dans le code de messagerie suivantes :

    - Définissez `your-SMTP-host` au nom du serveur SMTP que vous avez accès.
    - Définissez `your-user-name-here` au nom d’utilisateur pour votre compte de serveur SMTP.
    - Définissez `your-account-password` au mot de passe pour votre compte de serveur SMTP.
    - Définissez `your-email-address-here` à votre adresse de messagerie. Il s’agit de l’adresse de messagerie, le message est envoyé à partir de. (Certains fournisseurs de messagerie ne vous permettent de spécifier une autre `From` adresse et utilise votre nom d’utilisateur en tant que le `From` adresse.)

    Pour plus d’informations sur les paramètres SMTP, consultez [configuration des paramètres de messagerie](https://go.microsoft.com/fwlink/?LinkID=202899#configuring_email_settings) dans l’article [envoi de courrier électronique à partir d’un Site ASP.NET Web Pages (Razor)](https://go.microsoft.com/fwlink/?LinkID=202899) et [problèmes liés à l’envoi de courrier électronique](https://go.microsoft.com/fwlink/?LinkId=253001#email)dans les [des Pages Web ASP.NET (Razor) Troubleshooting Guide](https://go.microsoft.com/fwlink/?LinkId=253001).
- Enregistrer le  *\_AppStart.cshtml* de fichiers et de le fermer.
- Dans le dossier racine d’un site Web, créer une nouvelle page nommée *TestEmail.cshtml*.
- Remplacez le contenu existant avec les éléments suivants : 

    [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample4.cshtml)]
- Exécutez le *TestEmail.cshtml* page dans un navigateur.
- Renseignez les champs pour vous envoyer un message électronique, puis cliquez sur **envoyer**.
- Vérifiez votre adresse de messagerie pour vous assurer que vous avez obtenu le message.

La partie importante de cet exemple est que les paramètres que vous ne modifiez généralement, telles que le nom de votre serveur SMTP et vos informations d’identification de la messagerie, sont définies dans le  *\_AppStart.cshtml* fichier. De cette façon, que vous n’avez pas besoin de les définir à nouveau dans chaque page où vous envoyez par courrier électronique. (Bien que si pour une raison quelconque, vous devez modifier ces paramètres, vous pouvez les définir individuellement dans une page.) Dans la page, vous définissez uniquement les valeurs qui sont généralement modifiés chaque fois, telles que le destinataire et le corps du message électronique.

<a id="Running_Code_Before_and_After"></a>
## <a name="running-code-before-and-after-files-in-a-folder"></a>Exécution du Code avant et après les fichiers dans un dossier

Tout comme vous pouvez utiliser  *\_AppStart.cshtml* pour écrire du code avant l’exécution de pages du site, vous pouvez écrire du code qui s’exécute avant (et après) n’importe quelle page dans un dossier particulier s’exécuter. Cela est utile pour les éléments tels que la définition de la même page de disposition pour toutes les pages dans un dossier, ou pour le contrôle auquel un utilisateur est connecté avant l’exécution d’une page dans le dossier.

Pour les pages en particulier des dossiers, vous pouvez créer code dans un fichier nommé  *\_PageStart.cshtml*. Le diagramme suivant montre comment la  *\_PageStart.cshtml* page fonctionne. Lorsqu’une demande arrive pour une page, ASP.NET vérifie en premier une  *\_AppStart.cshtml* page et qui exécute. Ensuite, ASP.NET vérifie s’il existe un  *\_PageStart.cshtml* page, et si tel est le cas, qui s’exécute. Il exécute ensuite la page demandée.

À l’intérieur de la  *\_PageStart.cshtml* page, vous pouvez spécifier où lors du traitement de la page demandée à exécuter en incluant un `RunPage` (méthode). Cela vous permet d’exécuter du code avant l’exécution de la page demandée, puis à nouveau après lui. Si vous n’incluez pas `RunPage`, tout le code dans  *\_PageStart.cshtml* s’exécute, puis sur la page demandée s’exécute automatiquement.

![[image]](18-customizing-site-wide-behavior/_static/image3.jpg)

ASP.NET vous permet de créer une hiérarchie de  *\_PageStart.cshtml* fichiers. Vous pouvez placer un  *\_PageStart.cshtml* fichier à la racine du site et dans les sous-dossiers. Lorsqu’une page est demandée, le  *\_PageStart.cshtml* fichier à l’exécution du niveau le plus élevé (plus proche de la racine du site), suivi par le  *\_PageStart.cshtml* fichier dans la prochaine sous-dossier, et ainsi de suite vers le bas de la structure de sous-dossier jusqu'à ce que la demande atteint le dossier qui contient la page demandée. Une fois tous les applicable  *\_PageStart.cshtml* fichiers ont été exécutés, l’exécution de la page demandée.

Par exemple, vous pouvez avoir la combinaison suivante de  *\_PageStart.cshtml* fichiers et *Default.cshtml* fichier :

[!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample5.cshtml)]

[!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample6.cshtml)]

[!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample7.cshtml)]

Lorsque vous exécutez */myfolder/default.cshtml*, vous verrez les éléments suivants :

[!code-console[Main](18-customizing-site-wide-behavior/samples/sample8.cmd)]

## <a name="running-initialization-code-for-all-pages-in-a-folder"></a>Code d’initialisation en cours d’exécution pour toutes les Pages dans un dossier

Une utilisation optimale pour  *\_PageStart.cshtml* fichiers consiste à initialiser la même page de disposition pour tous les fichiers dans un dossier unique.

1. Dans le dossier racine, créez un dossier nommé *InitPages*.
2. Dans le *InitPages* dossier de votre site Web, créez un fichier nommé  *\_PageStart.cshtml* et remplacez le balisage par défaut et le code par le code suivant : 

    [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample9.cshtml)]
3. Dans la racine du site Web, créez un dossier nommé *partagé*.
4. Dans le *Shared* dossier, créez un fichier nommé  *\_Layout1.cshtml* et remplacez le balisage par défaut et le code par le code suivant : 

    [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample10.cshtml)]
5. Dans le *InitPages* dossier, créez un fichier nommé *Content1.cshtml* et remplacer le contenu existant par le code suivant : 

    [!code-html[Main](18-customizing-site-wide-behavior/samples/sample11.html)]
6. Dans le *InitPages* dossier, créez un autre fichier nommé *Content2.cshtml* et remplacez le balisage par défaut avec les éléments suivants : 

    [!code-html[Main](18-customizing-site-wide-behavior/samples/sample12.html)]
7. Exécutez *Content1.cshtml* dans un navigateur. 

    ![[image]](18-customizing-site-wide-behavior/_static/image4.jpg)

    Lorsque le *Content1.cshtml* page s’exécute, le  *\_PageStart.cshtml* fichier définit `Layout` et définit également `PageData["MyBackground"]` une couleur. Dans *Content1.cshtml*, la disposition et les couleurs sont appliquées.
8. Affichage *Content2.cshtml* dans un navigateur. 

    La disposition est la même, étant donné que les deux pages utilisent la même page de disposition et la couleur comme initialisé dans  *\_PageStart.cshtml*.

## <a name="using-pagestartcshtml-to-handle-errors"></a>À l’aide de \_PageStart.cshtml pour gérer les erreurs

Une autre bonne utiliser pour le  *\_PageStart.cshtml* fichier consiste à créer un moyen de gérer les erreurs de programmation (exceptions) qui peuvent se produire dans les *.cshtml* page dans un dossier. Cet exemple montre une façon de procéder.

1. Dans le dossier racine, créez un dossier nommé *InitCatch*.
2. Dans le *InitCatch* dossier de votre site Web, créez un fichier nommé  *\_PageStart.cshtml* et remplacez le balisage existant et le code par le code suivant : 

    [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample13.cshtml)]

    Dans ce code, vous exécutez la page demandée explicitement en appelant le `RunPage` méthode à l’intérieur d’un `try` bloc. Si des erreurs de programmation se produisent dans demandé de page, le code à l’intérieur de la `catch` bloc s’exécute. Dans ce cas, le code redirige vers une page (*Error.cshtml*) et transmet le nom du fichier qui a rencontré l’erreur dans le cadre de l’URL. (Vous allez créer la page dans quelques instants.)
3. Dans le *InitCatch* dossier de votre site Web, créez un fichier nommé *Exception.cshtml* et remplacez le balisage existant et le code par le code suivant : 

    [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample14.cshtml)]

    Pour des raisons de cet exemple, ce que vous faites dans cette page sont délibérément création d’une erreur en essayant d’ouvrir un fichier de base de données qui n’existe pas.
4. Dans le dossier racine, créez un fichier nommé *Error.cshtml* et remplacez le balisage existant et le code par le code suivant : 

    [!code-html[Main](18-customizing-site-wide-behavior/samples/sample15.html)]

    Dans cette page, l’expression `@Request["source"]` Obtient la valeur de l’URL et l’affiche.
5. Dans la barre d’outils, cliquez sur **enregistrer**.
6. Exécutez *Exception.cshtml* dans un navigateur. 

    ![[image]](18-customizing-site-wide-behavior/_static/image5.jpg)

    Car une erreur se produit dans *Exception.cshtml*, le  *\_PageStart.cshtml* page redirige vers le *Error.cshtml* fichier, qui affiche le message.

    Pour plus d’informations sur les exceptions, consultez [Introduction à ASP.NET Web Pages de programmation à l’aide de la syntaxe Razor](https://go.microsoft.com/fwlink/?LinkID=251587).

<a id="Using__PageStart.cshtml_to_Restrict_Folder_Access"></a>
## <a name="using-pagestartcshtml-to-restrict-folder-access"></a>À l’aide de \_PageStart.cshtml pour restreindre l’accès au dossier

Vous pouvez également utiliser le  *\_PageStart.cshtml* fichier pour restreindre l’accès à tous les fichiers dans un dossier.

1. Dans WebMatrix, créez un nouveau site Web à l’aide du **Site à partir du modèle** option.
2. Dans les modèles disponibles, sélectionnez **Starter Site**.
3. Dans le dossier racine, créez un dossier nommé *AuthenticatedContent*.
4. Dans le *AuthenticatedContent* dossier, créez un fichier nommé  *\_PageStart.cshtml* et remplacez le balisage existant et le code par le code suivant : 

    [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample16.cshtml)]

    Le code commence en empêchant que tous les fichiers dans le dossier mis en cache. (Cela est nécessaire pour les scénarios, tels que des ordinateurs publics, où vous souhaitez les pages mises en cache d’un utilisateur soit disponible pour l’utilisateur suivant). Ensuite, le code détermine si l’utilisateur est connecté au site avant de pouvoir afficher les pages dans le dossier. Si l’utilisateur n’est pas connecté, le code redirige vers la page de connexion. La page de connexion peut retourner l’utilisateur à la page qui a été demandée à l’origine si vous incluez une valeur de chaîne de requête nommée `ReturnUrl`.
5. Créer une nouvelle page dans le *AuthenticatedContent* dossier nommé *Page.cshtml*.
6. Remplacez le balisage par défaut avec les éléments suivants :  

    [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample17.cshtml)]
7. Exécutez *Page.cshtml* dans un navigateur. Le code vous redirige vers une page de connexion. Vous devez l’enregistrer avant de vous connecter. Une fois que vous avez enregistré et connecté, vous pouvez accéder à la page et afficher son contenu.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Ressources supplémentaires

[Introduction à la programmation à l’aide de la syntaxe Razor ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkID=251587)
