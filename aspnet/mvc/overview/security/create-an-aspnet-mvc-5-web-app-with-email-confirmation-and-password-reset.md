---
uid: mvc/overview/security/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset
title: "Créer une application de web ASP.NET MVC 5 sécurisée avec se connectent, envoyer par courrier électronique de confirmation et le mot de passe de la réinitialisation (c#) | Documents Microsoft"
author: Rick-Anderson
description: "Ce didacticiel vous montre comment créer une application de web ASP.NET MVC 5 avec confirmation par courrier électronique et le mot de passe réinitialisé à l’aide du système d’appartenance ASP.NET Identity. Autorité de certification vous..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/26/2015
ms.topic: article
ms.assetid: d4911cb3-1afb-4805-b860-10818c4b1280
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/security/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset
msc.type: authoredcontent
ms.openlocfilehash: 5689031015279484cc616090a767a8c25eefa3c1
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="create-a-secure-aspnet-mvc-5-web-app-with-log-in-email-confirmation-and-password-reset-c"></a>Créer une application de web ASP.NET MVC 5 sécurisée avec se connectent, envoyer par courrier électronique de confirmation et le mot de passe de la réinitialisation (c#)
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> Ce didacticiel vous montre comment créer une application de web ASP.NET MVC 5 avec confirmation par courrier électronique et le mot de passe réinitialisé à l’aide du système d’appartenance ASP.NET Identity. Vous pouvez télécharger l’application terminée [ici](https://code.msdn.microsoft.com/MVC-5-with-2FA-email-8f26d952). Le téléchargement contient les programmes d’assistance de débogage qui vous permettent de tester une confirmation par courrier électronique et SMS sans configurer d’un courrier électronique ou le fournisseur SMS.
> 
> Ce didacticiel a été écrit par [Rick Anderson](https://blogs.msdn.com/rickAndy) (Twitter : [ @RickAndMSFT ](https://twitter.com/RickAndMSFT) ).


<a id="createMvc"></a>
## <a name="create-an-aspnet-mvc-app"></a>Créer une application ASP.NET MVC

Commencez par installer et exécuter [Visual Studio Express 2013 pour le Web](https://go.microsoft.com/fwlink/?LinkId=299058) ou [Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566). Installer [Visual Studio 2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390465) ou une version ultérieure.

> [!NOTE]
> Avertissement : Vous devez installer [Visual Studio 2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390465) ou une version ultérieure pour effectuer ce didacticiel.


1. Créez un projet Web ASP.NET et sélectionnez le modèle MVC. Web Forms prend également en charge ASP.NET Identity, afin que vous pouvez suivre des étapes similaires dans une application de formulaires web.  
    ![](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/_static/image1.png)
2. Laissez l’authentification par défaut en tant que **comptes d’utilisateur individuels**. Si vous souhaitez héberger l’application dans Azure, laissez la case à cocher activée. Plus loin dans ce didacticiel, nous déploiera vers Azure. Vous pouvez [ouvrir un compte Azure gratuitement](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
3. Définir le [projet pour utiliser SSL](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md).
4. Exécuter l’application, cliquez sur le **inscrire** lier et inscrire un utilisateur. À ce stade, la seule validation de l’adresse de messagerie est avec le [[EmailAddress]](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.emailaddressattribute(v=vs.110).aspx) attribut.
5. Dans l’Explorateur de serveurs, accédez à **données Connections\DefaultConnection\Tables\AspNetUsers**avec le bouton droit sur et sélectionnez **ouvrir la définition de la table**.

    L’illustration suivante montre le `AspNetUsers` schéma :

    ![](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/_static/image2.png)
6. Cliquez avec le bouton droit sur le **AspNetUsers** de table et sélectionnez **afficher les données de Table**.  
    ![](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/_static/image3.png)  
 À ce stade l’adresse de messagerie n’a pas été confirmé.
7. Cliquez sur la ligne et sélectionnez Supprimer. Vous allez ajouter cette adresse de messagerie à nouveau à l’étape suivante et envoyer un message électronique de confirmation.

## <a name="email-confirmation"></a>E-mail de confirmation

Il est recommandé de confirmer l’adresse de messagerie d’un nouvel enregistrement d’utilisateur pour vérifier qu’ils n’empruntent pas une autre personne (autrement dit, ils n’ont pas inscrit avec par quelqu'un d’autre adresse de messagerie). Supposons que vous aviez un forum de discussion, vous pouvez empêcher `"bob@example.com"` lors de l’inscription en tant que `"joe@contoso.com"`. Sans la confirmation par courrier électronique, `"joe@contoso.com"` Impossible d’obtenir le courrier indésirable à partir de votre application. Supposons que Bob accidentellement inscrit en tant que `"bib@example.com"` et vous n’aviez pas remarqué, il ne serait pas en mesure d’utiliser le mot de passe de récupération, car l’application n’a pas son e-mail valable. E-mail de confirmation offre uniquement une protection limitée de robots et ne fournit la protection à partir des expéditeurs déterminés, ils ont plusieurs alias de messagerie de travail qu’ils peuvent utiliser pour enregistrer.

En règle générale, vous souhaitez empêcher les nouveaux utilisateurs à partir de la validation des données à votre site web avant qu’ils ont été confirmées par courrier électronique, un message texte ou un autre mécanisme. <a id="build"></a>Dans les sections ci-dessous, nous activer la confirmation de courrier électronique et modifier le code pour empêcher les utilisateurs qui vient d’être inscrits de se connecter jusqu'à ce que leur courrier électronique a été confirmée.

<a id="SG"></a>
## <a name="hook-up-sendgrid"></a>Raccorder SendGrid

Bien que ce didacticiel montre uniquement comment ajouter la notification par courrier électronique via [SendGrid](http://sendgrid.com/), vous pouvez envoyer un e-mail à l’aide de SMTP et autres mécanismes (consultez [des ressources supplémentaires](#addRes)).

1. Dans la Console du Gestionnaire de Package, entrez ce qui suit la commande suivante : 

    [!code-console[Main](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/samples/sample1.cmd)]
2. Accédez à la [page d’inscription Azure SendGrid](https://go.microsoft.com/fwlink/?linkid=271033&clcid=0x409) et l’inscrire pour un compte SendGrid. Ajoutez un code similaire à ce qui suit pour configurer SendGrid :

    [!code-csharp[Main](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/samples/sample2.cs?highlight=3,5)]

Vous devrez ajouter qu'inclut les éléments suivants :

[!code-csharp[Main](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/samples/sample3.cs)]

Pour simplifier cet exemple, nous allons stocker les paramètres d’application dans le *web.config* fichier :

[!code-xml[Main](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/samples/sample4.xml)]

> [!WARNING]
> Sécurité - magasin jamais des données sensibles dans votre code source. Le compte et les informations d’identification sont stockées dans l’appSetting. Sur Azure, vous pouvez stocker en toute sécurité des ces valeurs sur le  **[configurer](https://blogs.msdn.com/b/webdev/archive/2014/06/04/queuebackgroundworkitem-to-reliably-schedule-and-run-long-background-process-in-asp-net.aspx)**  onglet dans le portail Azure. Consultez [meilleures pratiques pour le déploiement des mots de passe et autres données sensibles sur ASP.NET et Azure](../../../identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure.md).


### <a name="enable-email-confirmation-in-the-account-controller"></a>Activer la confirmation de courrier électronique dans le contrôleur de compte

[!code-csharp[Main](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/samples/sample5.cs?highlight=16-21)]

Vérifiez le *Views\Account\ConfirmEmail.cshtml* fichier possède la syntaxe razor correct. (Le @ caractère dans la première ligne peut être manquant. )

[!code-cshtml[Main](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/samples/sample6.cshtml?highlight=1)]

Exécutez l’application et cliquez sur le lien du Registre. Une fois que vous envoyez le formulaire d’inscription, vous êtes connecté.

![](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/_static/image4.png)

Vérifiez votre compte de messagerie, puis cliquez sur le lien pour confirmer votre adresse de messagerie.

<a id="require"></a>
## <a name="require-email-confirmation-before-log-in"></a>Exigez une confirmation par courrier électronique avant de journal dans

Actuellement une fois qu’un utilisateur termine le formulaire d’inscription, il est connecté. En règle générale, vous souhaitez confirmer leur adresse de messagerie avant de les enregistrer. Dans la section ci-dessous, nous allons modifier le code pour demander aux nouveaux utilisateurs pour qu’un message électronique de confirmation avant d’être enregistrées (authentifié). Mise à jour le `HttpPost Register` méthode avec les modifications en surbrillance suivantes :

[!code-csharp[Main](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/samples/sample7.cs?highlight=14-15,23-30)]

En supprimant le `SignInAsync` (méthode), l’utilisateur ne sera pas signé par l’inscription. Le `TempData["ViewBagLink"] = callbackUrl;` ligne peut être utilisé pour [déboguer l’application](#dbg) et tester l’inscription sans envoyer par courrier électronique. `ViewBag.Message`Permet d’afficher les instructions de confirmation. Le [télécharger exemple](https://code.msdn.microsoft.com/MVC-5-with-2FA-email-8f26d952) contient du code pour tester l’e-mail de confirmation sans configurer la messagerie et peut également être utilisé pour déboguer l’application.

Créer un `Views\Shared\Info.cshtml` et ajoutez le balisage razor suivant :

[!code-cshtml[Main](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/samples/sample8.cshtml)]

Ajouter le [attribut Authorize](https://msdn.microsoft.com/library/system.web.mvc.authorizeattribute(v=vs.118).aspx) à la `Contact` méthode d’action du contrôleur Home. Vous pouvez utiliser le clic sur le **Contact** lien pour vérifier les utilisateurs anonymes n’ont accès et les utilisateurs authentifiés n’ont pas accès.

[!code-csharp[Main](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/samples/sample9.cs?highlight=1)]

Vous devez également mettre à jour le `HttpPost Login` méthode d’action :

[!code-csharp[Main](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/samples/sample10.cs?highlight=13-22)]

Mise à jour la *Views\Shared\Error.cshtml* pour afficher le message d’erreur :

[!code-cshtml[Main](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/samples/sample11.cshtml?highlight=8-17)]

Supprimez tous les comptes dans le **AspNetUsers** table qui contient l’alias de messagerie électronique que vous souhaitez tester. Exécutez l’application et vérifiez que vous ne pouvez pas vous connecter avant d’avoir confirmé votre adresse de messagerie. Après avoir confirmé votre adresse de messagerie, cliquez sur le **Contact** lien.

<a id="reset"></a>
## <a name="password-recoveryreset"></a>Récupération/réinitialisation de mot de passe

Supprimez les caractères de commentaire de la `HttpPost ForgotPassword` méthode d’action dans le contrôleur de compte :

[!code-csharp[Main](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/samples/sample12.cs?highlight=17-20)]

Supprimez les caractères de commentaire de la `ForgotPassword` [ActionLink](https://msdn.microsoft.com/library/system.web.mvc.html.linkextensions.actionlink(v=vs.118).aspx) dans les *Views\Account\Login.cshtml* fichier de vue razor :

[!code-cshtml[Main](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/samples/sample13.cshtml?highlight=47-50)]

La page de connexion affiche maintenant un lien pour réinitialiser le mot de passe.

<a id="rsend"></a>
## <a name="resend-email-confirmation-link"></a>Renvoyer le lien de confirmation par courrier électronique

Une fois qu’un utilisateur crée un nouveau compte local, elles sont envoyées par courrier électronique un lien de confirmation qu’ils doivent utiliser avant de se connecter. Si l’utilisateur accidentellement supprime le message de confirmation, ou le message électronique n’arrive jamais, ils doivent le lien de confirmation envoyé à nouveau. Les modifications de code suivants montrent comment activer cette option.

Ajoutez la méthode d’assistance suivante vers le bas de la *Controllers\AccountController.cs* fichier :

[!code-csharp[Main](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/samples/sample14.cs)]

Mise à jour de la méthode Register pour utiliser l’Assistant Nouvelle :

[!code-csharp[Main](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/samples/sample15.cs?highlight=17)]

Mettre à jour de la méthode de connexion pour renvoyer le mot de passe lorsque si le compte d’utilisateur n’a pas été confirmé :

[!code-csharp[Main](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/samples/sample16.cs?highlight=20)]

<a id="combine"></a>
## <a name="combine-social-and-local-login-accounts"></a>Combiner des comptes de connexion de réseaux sociaux et local

Vous pouvez combiner les comptes locaux et réseaux sociaux en cliquant sur le lien de votre messagerie. Dans l’ordre suivant  **RickAndMSFT@gmail.com**  est tout d’abord créé en tant qu’une connexion locale, mais vous pouvez créer le compte en tant qu’un journal social en premier, puis ajouter une connexion locale.

![](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/_static/image5.png)

Cliquez sur le **gérer** lien. Notez la valeur 0 externe (connexions sociales) associé à ce compte.

![](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/_static/image6.png)

Cliquez sur le lien vers un autre journal dans le service et accepter les demandes d’application. Les deux comptes ont été combinés, vous ne pourrez pas vous connecter avec un compte. Vous pourriez vos utilisateurs pour ajouter des comptes locaux au cas où le journal des réseaux sociaux dans le service d’authentification est arrêté, ou plus probable qu’ils ont perdu l’accès à leur compte sociaux.

Dans l’image suivante, Tom est un journal des réseaux sociaux dans (que vous pouvez visualiser à partir de la **Logins externes : 1** présentée sur la page).

![](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/_static/image7.png)

En cliquant sur **choisir un mot de passe** vous permet d’ajouter un journal local sur avec le même compte.

![](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset/_static/image8.png)

## <a name="email-confirmation-in-more-depth"></a>E-mail de confirmation de manière plus approfondie

Mon didacticiel [Confirmation du compte et la récupération de mot de passe avec ASP.NET Identity](../../../identity/overview/features-api/account-confirmation-and-password-recovery-with-aspnet-identity.md) sont placées dans cette rubrique avec plus de détails.

<a id="dbg"></a>
## <a name="debugging-the-app"></a>Débogage de l’application

Si vous n’obtenez pas un message électronique contenant le lien :

- Vérifiez votre dossier courrier indésirable ou le courrier indésirable.
- Connectez-vous à votre compte SendGrid, puis cliquez sur le [lien de l’activité de messagerie](https://sendgrid.com/logs/index).

Pour tester le lien de vérification sans par courrier électronique, vous devez télécharger le [exemple terminé](https://code.msdn.microsoft.com/MVC-5-with-2FA-email-8f26d952). Le lien de confirmation et les codes de confirmation seront affichera sur la page.

<a id="addRes"></a>
## <a name="additional-resources"></a>Ressources supplémentaires

- [Des liens vers ASP.NET Identity recommandé de ressources](../../../identity/overview/getting-started/aspnet-identity-recommended-resources.md)
- [Compte de Confirmation et récupération de mot de passe avec ASP.NET Identity](../../../identity/overview/features-api/account-confirmation-and-password-recovery-with-aspnet-identity.md) des informations plus détaillées sur la confirmation de compte et de récupération du mot de passe.
- [Application MVC est 5 avec Facebook, Twitter, LinkedIn et d’authentification Google OAuth2](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) ce didacticiel vous montre comment écrire une application ASP.NET MVC 5 avec autorisation Facebook et Google OAuth 2. Il montre également comment ajouter des données supplémentaires à la base de données d’identité.
- [Déployer une application sécurisée ASP.NET MVC avec l’appartenance, OAuth et base de données SQL Azure](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data). Ce didacticiel ajoute le déploiement d’Azure, la sécurisation de votre application avec les rôles, l’utilisation de l’API d’appartenance pour ajouter des utilisateurs et des rôles et des fonctionnalités de sécurité supplémentaires.
- [Création d’une application de Google OAuth 2 et la connexion de l’application au projet](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md#goog)
- [Création de l’application de Facebook et la connexion de l’application au projet](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md#fb)
- [Configuration de SSL dans le projet](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md#ssl)
