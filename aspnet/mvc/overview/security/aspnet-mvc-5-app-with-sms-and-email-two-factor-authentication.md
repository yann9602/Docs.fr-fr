---
uid: mvc/overview/security/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication
title: "Application ASP.NET MVC 5 avec SMS et l’authentification à deux facteurs de messagerie | Documents Microsoft"
author: Rick-Anderson
description: "Ce didacticiel vous montre comment créer une application de web ASP.NET MVC 5 avec l’authentification à deux facteurs. Vous devez exécuter l’application web de créer un sécurisé ASP.NET MVC 5 avec..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/20/2015
ms.topic: article
ms.assetid: f50a5cdb-c06a-46ed-aa14-fc5b049dc8dc
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/security/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication
msc.type: authoredcontent
ms.openlocfilehash: d6bc92f3cbe6b61332e33e8a507b4516bf5c15a5
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication"></a>Application ASP.NET MVC 5 avec SMS et par courrier électronique d’authentification à deux facteurs
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> Ce didacticiel vous montre comment créer une application de web ASP.NET MVC 5 avec l’authentification à deux facteurs. Vous devez effectuer [créer une application de web ASP.NET MVC 5 sécurisée avec se connectent, réinitialisation de confirmation et le mot de passe de messagerie](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset.md) avant de continuer. Vous pouvez télécharger l’application terminée [ici](https://code.msdn.microsoft.com/MVC-5-with-2FA-email-8f26d952). Le téléchargement contient les programmes d’assistance de débogage qui vous permettent de tester une confirmation par courrier électronique et SMS sans configurer d’un courrier électronique ou le fournisseur SMS.
> 
> Ce didacticiel a été écrit par [Rick Anderson](https://blogs.msdn.com/rickAndy) (Twitter : [ @RickAndMSFT ](https://twitter.com/RickAndMSFT) ).


- [Créer une application ASP.NET MVC](#createMvc)
- [Configurer SMS pour l’authentification à deux facteurs](#SMS)
- [Activer l’authentification à deux facteurs](#enable2)
- [Ressources supplémentaires pour MSBuild](#addRes)

<a id="createMvc"></a>
## <a name="create-an-aspnet-mvc-app"></a>Créer une application ASP.NET MVC

Commencez par installer et exécuter [Visual Studio Express 2013 pour le Web](https://go.microsoft.com/fwlink/?LinkId=299058) ou [Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566). Installer [Visual Studio 2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390465) ou une version ultérieure.

> [!NOTE]
> Avertissement : Vous devez effectuer [créer une application de web ASP.NET MVC 5 sécurisée avec se connectent, réinitialisation de confirmation et le mot de passe de messagerie](create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset.md) avant de continuer. Vous devez installer [Visual Studio 2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390465) ou une version ultérieure pour effectuer ce didacticiel.


1. Créez un projet Web ASP.NET et sélectionnez le modèle MVC. Web Forms prend également en charge ASP.NET Identity, afin que vous pouvez suivre des étapes similaires dans une application de formulaires web.  
    ![](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication/_static/image1.png)
2. Laissez l’authentification par défaut en tant que **comptes d’utilisateur individuels**. Si vous souhaitez héberger l’application dans Azure, laissez la case à cocher activée. Plus loin dans ce didacticiel, nous déploiera vers Azure. Vous pouvez [ouvrir un compte Azure gratuitement](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
3. Définir le [projet pour utiliser SSL](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md).

<a id="SMS"></a>
## <a name="set-up-sms-for-two-factor-authentication"></a>Configurer SMS pour l’authentification à deux facteurs

Ce didacticiel fournit des instructions relatives à l’aide de Twilio ou ASPSMS, mais vous pouvez utiliser n’importe quel autre fournisseur SMS.

1. **Création d’un compte d’utilisateur avec un fournisseur SMS**  
  
 Créer un [Twilio](https://www.twilio.com/try-twilio) ou [ASPSMS](https://www.aspsms.com/asp.net/identity/testcredits/) compte.
2. **Installation de packages supplémentaires ou l’ajout de références de service**  
  
 Twilio :  
 Dans la Console du Gestionnaire de Package, entrez la commande suivante :  
    `Install-Package Twilio`  
  
 ASPSMS :  
 La référence de service suivant doit être ajouté :  
  
    ![](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication/_static/image2.png)  
  
 Adresse :  
    `https://webservice.aspsms.com/aspsmsx2.asmx?WSDL`  
  
 Espace de noms :  
    `ASPSMSX2`
3. **Déterminer les informations d’identification de l’utilisateur du fournisseur SMS**  
  
 Twilio :  
 À partir de la **tableau de bord** onglet de votre compte Twilio, copie le **SID de compte** et **Auth jeton**.  
  
 ASPSMS :  
 À partir des paramètres de votre compte, accédez à **clé d’utilisateur a** et copiez-le avec votre définis par l’utilisateur **mot de passe**.  
  
 Nous allons stocker ultérieurement ces valeurs dans le *web.config* fichier dans les clés de `"SMSAccountIdentification"` et `"SMSAccountPassword"` .
4. **Spécifiant l’ID d’expéditeur / donneur d’ordre**  
  
 Twilio :  
 À partir de la **numéros** , onglet de la copie de votre numéro de téléphone Twilio.  
  
 ASPSMS :  
 Dans le **déverrouiller les expéditeurs** déverrouiller un ou plusieurs expéditeurs de Menu, ou choisissez un donneur d’ordre alphanumérique (non pris en charge par tous les réseaux).  
  
 Nous allons stocker ultérieurement cette valeur dans la *web.config* fichier au sein de la clé `"SMSAccountFrom"` .
5. **Transfert des informations d’identification du fournisseur SMS dans l’application**  
  
 Vérifiez les informations d’identification et le numéro de téléphone de l’expéditeur disponible à l’application. Pour simplifier les choses, nous allons stocker ces valeurs dans le *web.config* fichier. Lorsque nous déployez dans Azure, nous pouvons stocker les valeurs de manière sécurisée dans le **paramètres de l’application** section sur le site web de l’onglet Configurer. 

    [!code-xml[Main](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication/samples/sample1.xml?highlight=8-10)]

    > [!WARNING]
    > Sécurité - magasin jamais des données sensibles dans votre code source. Le compte et les informations d’identification sont ajoutées au code ci-dessus pour simplifier l’exemple. Consultez [meilleures pratiques pour le déploiement des mots de passe et autres données sensibles sur ASP.NET et Azure](../../../identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure.md).
6. **Implémentation du transfert de données au fournisseur SMS**  
  
 Configurer le `SmsService` classe dans le *application\_Start\IdentityConfig.cs* fichier.  
  
 Selon le fournisseur SMS utilisé activer soit le **Twilio** ou **ASPSMS** section : 

    [!code-csharp[Main](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication/samples/sample2.cs)]
7. Mise à jour la *Views\Manage\Index.cshtml* vue Razor : (Remarque : ne pas simplement supprimer les commentaires dans le code de sortie, utilisez le code ci-dessous.)  

    [!code-cshtml[Main](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication/samples/sample3.cshtml?highlight=29-66)]
8. Vérifiez le `EnableTwoFactorAuthentication` et `DisableTwoFactorAuthentication` méthodes d’action dans le `ManageController` ont le[[ValidateAntiForgeryToken]](https://msdn.microsoft.com/library/system.web.mvc.validateantiforgerytokenattribute(v=vs.118).aspx) attribut :  

    [!code-csharp[Main](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication/samples/sample4.cs?highlight=3,16)]
9. Exécutez l’application et connectez-vous avec le compte que vous avez enregistré précédemment.
10. Cliquez sur votre nom d’utilisateur, ce qui active le `Index` méthode d’action de `Manage` contrôleur.  
    ![](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication/_static/image3.png)
11. Cliquez sur Ajouter.  
    ![](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication/_static/image4.png)
12. Le `AddPhoneNumber` méthode d’action affiche une boîte de dialogue pour entrer un numéro de téléphone qui permettre recevoir des messages SMS.

    [!code-csharp[Main](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication/samples/sample5.cs)]

    ![](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication/_static/image5.png)
13. En quelques secondes, vous obtiendrez un message texte avec le code de vérification. Entrer et appuyez sur **Submit**.  
    ![](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication/_static/image6.png)
14. L’affichage de gestion affiche que votre numéro de téléphone a été ajouté.

<a id="enable2"></a>
## <a name="enable-two-factor-authentication"></a>Activer l’authentification à deux facteurs

Dans l’application du modèle généré, vous devez utiliser l’interface utilisateur pour activer l’authentification à deux facteurs (2FA). Pour activer 2FA, cliquez sur votre nom d’utilisateur (alias de messagerie) dans la barre de navigation.

![](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication/_static/image7.png)

Cliquez sur Activer 2FA.

![](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication/_static/image8.png)

Déconnexion, puis reconnectez-vous. Si vous avez activé la messagerie (voir mon [didacticiel précédent](../../../identity/overview/features-api/account-confirmation-and-password-recovery-with-aspnet-identity.md)), vous pouvez sélectionner les SMS ou par courrier électronique pour 2FA.

![](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication/_static/image9.png)

La page de codes de vérification s’affiche où vous pouvez saisir le code (à partir de SMS ou par courrier électronique).

![](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication/_static/image10.png)

En cliquant sur le **n’oubliez pas de ce navigateur** case à cocher vous exempter d’avoir à utiliser 2FA pour vous connecter lorsque vous utilisez le navigateur et l’appareil sur lequel vous avez coché la case. Tant que les utilisateurs malveillants ne peuvent pas accéder à votre appareil, l’activation de 2FA et en cliquant sur le **n’oubliez pas de ce navigateur** vous donne accès de mot de passe d’une étape pratique, tout en conservant la protection 2FA forts pour tous les accès à partir d’appareils non approuvé. Pour cela, sur n’importe quel appareil privé que vous utilisez régulièrement.

Ce didacticiel fournit une introduction rapide à l’activation 2FA sur une application ASP.NET MVC. Mon didacticiel [authentification à deux facteurs à l’aide de SMS et des messages électroniques avec ASP.NET Identity](../../../identity/overview/features-api/two-factor-authentication-using-sms-and-email-with-aspnet-identity.md) détaille le code-behind de l’exemple.

<a id="addRes"></a>
## <a name="additional-resources"></a>Ressources supplémentaires

- [Authentification à deux facteurs à l’aide de SMS et des messages électroniques avec ASP.NET Identity](../../../identity/overview/features-api/two-factor-authentication-using-sms-and-email-with-aspnet-identity.md) fournit de détails sur l’authentification à deux facteurs
- [Des liens vers ASP.NET Identity recommandé de ressources](../../../identity/overview/getting-started/aspnet-identity-recommended-resources.md)
- [Compte de Confirmation et récupération de mot de passe avec ASP.NET Identity](../../../identity/overview/features-api/account-confirmation-and-password-recovery-with-aspnet-identity.md) des informations plus détaillées sur la confirmation de compte et de récupération du mot de passe.
- [Application MVC est 5 avec Facebook, Twitter, LinkedIn et d’authentification Google OAuth2](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) ce didacticiel vous montre comment écrire une application ASP.NET MVC 5 avec autorisation Facebook et Google OAuth 2. Il montre également comment ajouter des données supplémentaires à la base de données d’identité.
- [Déployer une application sécurisée ASP.NET MVC avec l’appartenance, OAuth et base de données SQL Azure Web](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data). Ce didacticiel ajoute le déploiement d’Azure, la sécurisation de votre application avec les rôles, l’utilisation de l’API d’appartenance pour ajouter des utilisateurs et des rôles et des fonctionnalités de sécurité supplémentaires.
- [Création d’une application de Google OAuth 2 et la connexion de l’application au projet](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md#goog)
- [Création de l’application de Facebook et la connexion de l’application au projet](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md#fb)
- [Configuration de SSL dans le projet](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md#ssl)
