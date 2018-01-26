---
uid: web-pages/overview/getting-started/11-adding-email-to-your-web-site
title: "Envoi de courrier électronique à partir d’un serveur Web ASP.NET de Pages (Razor) Site | Documents Microsoft"
author: tfitzmac
description: "Ce chapitre explique comment envoyer un message électronique automatisé à partir d’un site Web."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2014
ms.topic: article
ms.assetid: fc49bcb9-f1a9-4048-8c3f-b60951853200
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/getting-started/11-adding-email-to-your-web-site
msc.type: authoredcontent
ms.openlocfilehash: c5878c3bc468daef050dcebee99f64441066409a
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="sending-email-from-an-aspnet-web-pages-razor-site"></a>Envoi de courrier électronique à partir d’un Site de Pages (Razor) Web ASP.NET
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article explique comment envoyer un message électronique à partir d’un site Web lorsque vous utilisez les Pages Web ASP.NET (Razor).
> 
> Ce que vous allez apprendre :
> 
> - Comment envoyer un message électronique à partir de votre site Web.
> - Comment joindre un fichier à un message électronique.
> 
> Il s’agit de la fonctionnalité d’ASP.NET introduite dans l’article :
> 
> - Le `WebMail` helper.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 3
>   
> 
> Ce didacticiel fonctionne également avec ASP.NET Web Pages 2.


<a id="Sending_Email_Messages"></a>
## <a name="sending-email-messages-from-your-website"></a>Envoi de Messages électroniques à partir de votre site Web

Il existe toutes sortes de raisons pour lesquelles vous devrez peut-être envoyer un courrier électronique à partir de votre site Web. Vous pouvez envoyer des messages de confirmation aux utilisateurs, ou vous pouvez envoyer des notifications à vous-même (par exemple, un nouvel utilisateur a inscrits.) Le `WebMail` helper facilite l’utilisation que vous pouvez envoyer par courrier électronique.

Pour utiliser le `WebMail` application d’assistance, vous devez avoir accès à un serveur SMTP. (Abréviation de *Simple Mail Transfer Protocol*.) Un serveur SMTP est un serveur de messagerie qui transfère uniquement les messages du destinataire server &#8212; Il s’agit du côté sortant des messages électroniques. Si vous utilisez un fournisseur d’hébergement de votre site Web, ils probablement configurer vous avec adresse de messagerie et il peuvent vous indiquer quel est le nom de votre serveur SMTP. Si vous travaillez à l’intérieur d’un réseau d’entreprise, un administrateur ou votre service informatique peut généralement donne les informations sur un serveur SMTP que vous pouvez utiliser. Si vous travaillez à votre domicile, vous pourrez même tester à l’aide de votre fournisseur de messagerie ordinaire, ce qui peut vous indiquer le nom du serveur SMTP. Vous devez généralement :

- Le nom du serveur SMTP.
- Le numéro de port. Il s’agit généralement de 25. Toutefois, votre fournisseur de services Internet peut nécessiter que vous pouvez utiliser le port 587. Si vous utilisez SSL (SSL) pour le courrier électronique, vous devrez peut-être un autre port. Vérifiez auprès de votre fournisseur de messagerie.
- Informations d’identification (nom d’utilisateur, mot de passe).

Dans cette procédure, vous créez deux pages. La première page comporte un formulaire permettant aux utilisateurs d’entrer une description, comme si elles ont été remplissant un formulaire de support technique. La première page envoie ses informations à une deuxième page. Dans la deuxième page, le code extrait les informations de l’utilisateur et envoie un message électronique. Elle affiche également un message confirmant que le rapport de problème a été reçu.

![[image]](11-adding-email-to-your-web-site/_static/image1.jpg)

> [!NOTE]
> Pour que cet exemple reste simple, le code initialise le `WebMail` droite d’assistance dans la page où vous l’utilisez. Toutefois, pour des sites Web réels, il est préférable de placer le code d’initialisation comme suit dans un fichier global, afin que vous initialisez le `WebMail` auxiliaire pour tous les fichiers de votre site Web. Pour plus d’informations, consultez [personnalisation du comportement au niveau du Site pour ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=202906#Setting_Values_For_Helpers).


1. Créer un nouveau site Web.
2. Ajouter une nouvelle page nommée *EmailRequest.cshtml* et ajoutez le balisage suivant : 

    [!code-html[Main](11-adding-email-to-your-web-site/samples/sample1.html)]

    Notez que la `action` attribut de l’élément de formulaire a été définie *ProcessRequest.cshtml*. Cela signifie que le formulaire est envoyé à cette page au lieu de nouveau à la page actuelle.
3. Ajouter une nouvelle page nommée *ProcessRequest.cshtml* au site Web et ajoutez le code et le balisage suivant :   

    [!code-cshtml[Main](11-adding-email-to-your-web-site/samples/sample2.cshtml)]

    Dans le code, vous obtenez les valeurs des champs de formulaire qui ont été soumises à la page. Vous appelez ensuite la `WebMail` l’Assistant `Send` méthode pour créer et envoyer le message électronique. Dans ce cas, les valeurs à utiliser sont constituées de texte qui vous concaténez avec les valeurs qui ont été soumises à partir du formulaire.

    Le code de cette page se trouve dans un `try/catch` bloc. Si un message électronique ne fonctionne pas pour une raison quelconque, la tentative d’envoi (par exemple, les paramètres ne sont pas droit), le code dans le `catch` bloc s’exécute et définit le `errorMessage` variable à l’erreur qui s’est produite. (Pour plus d’informations sur `try/catch` blocs ou `<text>` balise [Introduction à ASP.NET Web Pages de programmation à l’aide de la syntaxe Razor](https://go.microsoft.com/fwlink/?LinkID=251587#ID_HandlingErrors).)

    Dans le corps de la page, si le `errorMessage` variable est vide (la valeur par défaut), l’utilisateur voit un message qui le message électronique a été envoyé. Si le `errorMessage` variable est définie sur true, l’utilisateur voit un message qu’a eu lieu lors de l’envoi du message.

    Notez que dans la partie de la page qui affiche un message d’erreur, il existe un test supplémentaire : `if(debuggingFlag)`. Il s’agit d’une variable que vous pouvez définir sur « True » si vous ne parvenez pas à envoyer par courrier électronique. Lorsque `debuggingFlag` a la valeur true, et s’il existe un problème d’envoi de courrier électronique, un message d’erreur supplémentaires s’affiche qui indique quelle que soit ASP.NET a signalé lorsqu’il a tenté d’envoyer le message électronique. Juste un avertissement, toutefois : les messages d’erreur lorsqu’il ne peut pas envoyer un message électronique des rapports ASP.NET peuvent être génériques. Par exemple, si ASP.NET ne peut pas contacter le serveur SMTP (par exemple, étant donné que vous avez apportées à une erreur dans le nom du serveur), l’erreur est `Failure sending mail`.

    > [!NOTE] 
    > 
    > **Important** si vous obtenez un message d’erreur d’un objet d’exception (`ex` dans le code), procédez comme *pas* régulièrement transmet ce message aux utilisateurs. Objets d’exception incluent souvent des informations que les utilisateurs ne sont pas supposées et qui peut même être une faille de sécurité. C’est pourquoi ce code inclut la variable `debuggingFlag` qui est utilisée comme commutateur pour afficher le message d’erreur et la raison pour laquelle la variable par défaut est définie sur false. Vous devez définir cette variable sur true (et par conséquent afficher le message d’erreur) *uniquement* si vous rencontrez un problème avec l’envoi de courrier électronique et que vous devez déboguer. Une fois que vous avez résolu tous les problèmes, définissez `debuggingFlag` à false.

    Modifier les paramètres associés dans le code de messagerie suivantes :

    - Définissez `your-SMTP-host` au nom du serveur SMTP que vous avez accès.
    - Définissez `your-user-name-here` au nom d’utilisateur pour votre compte de serveur SMTP.
    - Définissez `your-account-password` au mot de passe pour votre compte de serveur SMTP.
    - Définissez `your-email-address-here` à votre adresse de messagerie. Il s’agit de l’adresse de messagerie, le message est envoyé à partir de. (Certains fournisseurs de messagerie ne vous permettent de spécifier une autre `From` adresse et utilise votre nom d’utilisateur en tant que le `From` adresse.)

    > [!TIP] 
    > 
    > <a id="configuring_email_settings"></a>
    > ### <a name="configuring-email-settings"></a>Configuration des paramètres de messagerie
    > 
    > Il peut être un défi parfois s’assurer que vous disposez des paramètres corrects pour le serveur SMTP, numéro de port et ainsi de suite. Voici quelques conseils :
    > 
    > - Le nom du serveur SMTP est souvent quelque chose comme `smtp.provider.com` ou `smtp.provider.net`. Toutefois, si vous publiez votre site à un fournisseur d’hébergement, le nom du serveur SMTP à ce stade peut être `localhost`. Il s’agit, car une fois que vous avez publiée et que votre site est en cours d’exécution sur le serveur du fournisseur, le serveur de messagerie peut être local du point de vue de votre application. Cette modification dans les noms de serveur peut signifier que vous devez modifier le nom du serveur SMTP dans le cadre de votre processus de publication.
    > - Le numéro de port est généralement 25. Toutefois, certains fournisseurs exigent que vous pour utiliser le port 587 ou une autre port.
    > - Assurez-vous que vous utilisez les informations d’identification correctes. Si vous avez publié votre site à un fournisseur d’hébergement, utilisez les informations d’identification que le fournisseur a indiqué spécifiquement le sont pour le courrier électronique. Il peut s’agir différentes informations d’identification que vous permet de publier.
    > - Parfois, vous n’avez pas besoin d’informations d’identification du tout. Si vous envoyez par courrier électronique à l’aide de votre fournisseur de services Internet, votre fournisseur de messagerie déjà connaissez peut-être vos informations d’identification. Une fois que vous publiez, vous devrez peut-être utiliser différentes informations d’identification que lorsque vous testez sur votre ordinateur local.
    > - Si votre fournisseur de messagerie utilise le chiffrement, vous devez définir `WebMail.EnableSsl` à `true`.
4. Exécutez le *EmailRequest.cshtml* page dans un navigateur. (Assurez-vous que la page est sélectionnée dans le **fichiers** espace de travail avant de l’exécuter.)
5. Entrez votre nom et une description du problème, puis cliquez sur le **Submit** bouton. Vous êtes redirigé vers la *ProcessRequest.cshtml* page, qui confirme que votre message, et qui vous envoie un message électronique. 

    ![[image]](11-adding-email-to-your-web-site/_static/image2.jpg)

<a id="Sending_a_File"></a>
## <a name="sending-a-file-using-email"></a>Envoi d’un fichier par courrier électronique

Vous pouvez également envoyer des fichiers qui sont associés à des messages électroniques. Dans cette procédure, vous créez un fichier texte et deux pages HTML. Vous utiliserez le fichier texte en tant que pièce jointe de courrier électronique.

1. Dans le site Web, ajoutez un nouveau fichier texte et nommez-le *MyFile.txt*.
2. Copiez le texte suivant et collez-le dans le fichier : 

    `Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.`
3. Créer une page nommée *SendFile.cshtml* et ajoutez le balisage suivant : 

    [!code-html[Main](11-adding-email-to-your-web-site/samples/sample3.html)]
4. Créer une page nommée *ProcessFile.cshtml* et ajoutez le balisage suivant : 

    [!code-cshtml[Main](11-adding-email-to-your-web-site/samples/sample4.cshtml)]
5. Modifier les paramètres associés dans le code de l’exemple de messagerie suivantes :

    - Définissez `your-SMTP-host` au nom d’un serveur SMTP que vous avez accès.
    - Définissez `your-user-name-here` au nom d’utilisateur pour votre compte de serveur SMTP.
    - Définissez `your-email-address-here` à votre adresse de messagerie. Il s’agit de l’adresse de messagerie, le message est envoyé à partir de.
    - Définissez `your-account-password` au mot de passe pour votre compte de serveur SMTP.
    - Définissez `target-email-address-here` à votre adresse de messagerie. (Comme avant, vous devez normalement envoyer un courrier électronique à quelqu'un d’autre, mais pour le test, vous pouvez l’envoyer à vous-même.)
6. Exécutez le *SendFile.cshtml* page dans un navigateur.
7. Entrez votre nom, une ligne d’objet et le nom du fichier texte à joindre (*MyFile.txt*).
8. Cliquez sur le bouton `Submit`. Comme auparavant, vous êtes redirigé vers la *ProcessFile.cshtml* page, qui confirme que votre message, et qui vous envoie un message électronique avec le fichier joint.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Ressources supplémentaires


- [ASP.NET Web Pages (Razor) - Guide de résolution des problèmes](https://go.microsoft.com/fwlink/?LinkId=253001)
- [Protocole SMTP](https://msdn.microsoft.com/library/aa480435.aspx)
- [Personnalisation du comportement de l’échelle du Site pour les Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202906)
