---
uid: identity/overview/features-api/two-factor-authentication-using-sms-and-email-with-aspnet-identity
title: "Authentification à deux facteurs à l’aide de SMS et des messages électroniques avec ASP.NET Identity | Documents Microsoft"
author: HaoK
description: "Ce didacticiel vous indiquera comment configurer l’authentification à deux facteurs (2FA) à l’aide de SMS et des messages électroniques. Cet article a été rédigé par Rick Anderson ( @RickAndMSFT ), par..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/15/2015
ms.topic: article
ms.assetid: 053e23c4-13c9-40fa-87cb-3e9b0823b31e
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /identity/overview/features-api/two-factor-authentication-using-sms-and-email-with-aspnet-identity
msc.type: authoredcontent
ms.openlocfilehash: 0f9ff7cf74048a008b150da1e843ff15333269ab
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="two-factor-authentication-using-sms-and-email-with-aspnet-identity"></a>Authentification à deux facteurs avec l’identité de ASP.NET à l’aide de SMS et par courrier électronique
====================
par [arts martiaux de Hao](https://github.com/HaoK), [Pranav Rastogi](https://github.com/rustd), [Rick Anderson](https://github.com/Rick-Anderson), [Suhas Joshi](https://github.com/suhasj)

> Ce didacticiel vous indiquera comment configurer l’authentification à deux facteurs (2FA) à l’aide de SMS et des messages électroniques.
> 
> Cet article a été rédigé par Rick Anderson ([@RickAndMSFT](https://twitter.com/#!/RickAndMSFT)), Pranav Rastogi ([@rustd](https://twitter.com/rustd)), arts martiaux de Hao et Suhas Joshi. L’exemple de NuGet a été écrit principalement par arts martiaux de Hao.


Cette rubrique couvre les sujets suivants :

- [Création de l’exemple d’identité](#build)
- [Configurer SMS pour l’authentification à deux facteurs](#SMS)
- [Activer l’authentification à deux facteurs](#enable2)
- [Comment inscrire un fournisseur d’authentification à deux facteurs](#reg)
- [Combiner des comptes de connexion de réseaux sociaux et local](#combine)
- [Verrouillage de compte à partir des attaques en force brute](#lock)

<a id="build"></a>

## <a name="building-the-identity-sample"></a>Création de l’exemple d’identité

Dans cette section, vous utiliserez NuGet pour télécharger un exemple que nous travaillons avec. Commencez par installer et exécuter [Visual Studio Express 2013 pour le Web](https://go.microsoft.com/fwlink/?LinkId=299058) ou [Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566). Installation de Visual Studio [2013 Update 2](https://go.microsoft.com/fwlink/?LinkId=390521) ou une version ultérieure.

> [!NOTE]
> Avertissement : Vous devez installer Visual Studio [2013 Update 2](https://go.microsoft.com/fwlink/?LinkId=390521) pour effectuer ce didacticiel.


1. Créer un nouveau ***vide*** projet Web ASP.NET.
2. Dans la Console du Gestionnaire de Package, entrez ce qui suit les commandes suivantes :  
  
    `Install-Package SendGrid`  
    `Install-Package -Prerelease Microsoft.AspNet.Identity.Samples`  
  
 Dans ce didacticiel, nous allons utiliser [SendGrid](http://sendgrid.com/) pour envoyer un courrier électronique et [Twilio](https://www.twilio.com/) ou [ASPSMS](https://www.aspsms.com/asp.net/identity/testcredits/) pour sms texto. Le `Identity.Samples` package installe le code que nous travaillerons avec.
3. Définir le [projet pour utiliser SSL](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md).
4. *Facultatif*: suivez les instructions de mon [didacticiel de confirmation par courrier électronique](account-confirmation-and-password-recovery-with-aspnet-identity.md) pour raccorder SendGrid puis exécuter l’application et inscrire un compte de messagerie.
5. * Facultatif : * supprimez le code de confirmation de lien démonstration par courrier électronique à partir de l’exemple (le `ViewBag.Link` code dans le contrôleur de compte. Consultez le `DisplayEmail` et `ForgotPasswordConfirmation` les méthodes d’action et des vues razor).
6. * Facultatif : * supprimez la `ViewBag.Status` code à partir de contrôleurs de compte et de gérer et de la *Views\Account\VerifyCode.cshtml* et *Views\Manage\VerifyPhoneNumber.cshtml* vues razor. Vous pouvez également, garder la `ViewBag.Status` affichage pour tester le fonctionnement de cette application localement sans avoir à connecter et envoyer par courrier électronique et les messages SMS.

> [!NOTE]
> Avertissement : Si vous modifiez les paramètres de sécurité dans cet exemple, les applications de production devrez subir un audit de sécurité qui appelle explicitement les modifications apportées.


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
  
    ![](two-factor-authentication-using-sms-and-email-with-aspnet-identity/_static/image1.png)  
  
 Adresse :  
    `https://webservice.aspsms.com/aspsmsx2.asmx?WSDL`  
  
 Espace de noms :  
    `ASPSMSX2`
3. **Déterminer les informations d’identification de l’utilisateur du fournisseur SMS**  
  
 Twilio :  
 À partir de la **tableau de bord** onglet de votre compte Twilio, copie le **SID de compte** et **Auth jeton**.  
  
 ASPSMS :  
 À partir des paramètres de votre compte, accédez à **clé d’utilisateur a** et copiez-le avec votre définis par l’utilisateur **mot de passe**.  
  
 Nous allons stocker plus tard de ces valeurs dans les variables `SMSAccountIdentification` et `SMSAccountPassword` .
4. **Spécifiant l’ID d’expéditeur / donneur d’ordre**  
  
 Twilio :  
 À partir de la **numéros** , onglet de la copie de votre numéro de téléphone Twilio.  
  
 ASPSMS :  
 Dans le **déverrouiller les expéditeurs** déverrouiller un ou plusieurs expéditeurs de Menu, ou choisissez un donneur d’ordre alphanumérique (non pris en charge par tous les réseaux).  
  
 Nous allons stocker plus loin de cette valeur dans la variable `SMSAccountFrom` .
5. **Transfert des informations d’identification du fournisseur SMS dans l’application**  
  
 Rendre les informations d’identification et le numéro de téléphone de l’expéditeur disponible à l’application :

    [!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample1.cs)]

    > [!WARNING]
    > Sécurité - magasin jamais des données sensibles dans votre code source. Le compte et les informations d’identification sont ajoutées au code ci-dessus pour simplifier l’exemple. Consultez de Jon Atten [ASP.NET MVC : conserver les paramètres privés hors contrôle de code Source](http://typecastexception.com/post/2014/04/06/ASPNET-MVC-Keep-Private-Settings-Out-of-Source-Control.aspx).
6. **Implémentation du transfert de données au fournisseur SMS**  
  
 Configurer le `SmsService` classe dans le *application\_Start\IdentityConfig.cs* fichier.  
  
 Selon le fournisseur SMS utilisé activer soit le **Twilio** ou **ASPSMS** section : 

    [!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample2.cs)]
7. Exécutez l’application et connectez-vous avec le compte que vous avez enregistré précédemment.
8. Cliquez sur votre nom d’utilisateur, ce qui active le `Index` méthode d’action de `Manage` contrôleur.  
  
    ![](two-factor-authentication-using-sms-and-email-with-aspnet-identity/_static/image2.png)
9. Cliquez sur Ajouter.  
  
    ![](two-factor-authentication-using-sms-and-email-with-aspnet-identity/_static/image3.png)
10. En quelques secondes, vous obtiendrez un message texte avec le code de vérification. Entrer et appuyez sur **Submit**.  
  
    ![](two-factor-authentication-using-sms-and-email-with-aspnet-identity/_static/image4.png)
11. L’affichage de gestion affiche que votre numéro de téléphone a été ajouté.  
  
    ![](two-factor-authentication-using-sms-and-email-with-aspnet-identity/_static/image5.png)

### <a name="examine-the-code"></a>Examinez le code

[!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample3.cs?highlight=2)]

Le `Index` méthode d’action de `Manage` contrôleur définit le message d’état en fonction de votre action précédente et fournit des liens pour modifier votre mot de passe local ou d’ajouter un compte local. Le `Index` méthode affiche également l’état ou de votre 2FA téléphone numéro, logins externes, 2FA activée, n’oubliez pas de méthode 2FA pour ce navigateur (expliqué plus loin). En cliquant sur votre nom d’utilisateur (courrier électronique) dans la barre de titre ne transmet un message. En cliquant sur le **numéro de téléphone : supprimez** lien passe `Message=RemovePhoneSuccess` comme une chaîne de requête.  
  
`https://localhost:44300/Manage?Message=RemovePhoneSuccess`

[![](two-factor-authentication-using-sms-and-email-with-aspnet-identity/_static/image6.png)]

Le `AddPhoneNumber` méthode d’action affiche une boîte de dialogue pour entrer un numéro de téléphone qui permettre recevoir des messages SMS.

[!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample4.cs)]

![](two-factor-authentication-using-sms-and-email-with-aspnet-identity/_static/image7.png)

En cliquant sur le **envoyer le code de vérification** bouton publie le numéro de téléphone pour la requête HTTP POST `AddPhoneNumber` méthode d’action.

[!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample5.cs?highlight=12)]

Le `GenerateChangePhoneNumberTokenAsync` méthode génère le jeton de sécurité qui sera défini dans le message SMS. Si le service SMS a été configuré, le jeton est envoyé en tant que la chaîne &quot;votre code de sécurité est &lt;jeton&gt;&quot;. Le `SmsService.SendAsync` méthode est appelée de façon asynchrone, puis l’application est redirigée vers la `VerifyPhoneNumber` méthode d’action (qui affiche la boîte de dialogue), où vous pouvez entrer le code de vérification.

![](two-factor-authentication-using-sms-and-email-with-aspnet-identity/_static/image8.png)

Une fois que vous entrez le code et cliquez sur Envoyer, le code est validé dans la requête HTTP POST `VerifyPhoneNumber` méthode d’action.

[!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample6.cs)]

Le `ChangePhoneNumberAsync` méthode vérifie le code de sécurité publié. Si le code est correct, le numéro de téléphone est ajouté à la `PhoneNumber` champ le `AspNetUsers` table. Si cet appel réussit, la `SignInAsync` méthode est appelée :

[!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample7.cs)]

Le `isPersistent` paramètre définit si la session d’authentification est maintenue entre plusieurs demandes.

Lorsque vous modifiez votre profil de sécurité, un nouvel horodatage de sécurité est généré et stocké dans le `SecurityStamp` champ le *AspNetUsers* table. Notez que le `SecurityStamp` champ est différent à partir du cookie de sécurité. Le cookie de sécurité n’est pas stocké dans le `AspNetUsers` table (ou ailleurs dans la base de données d’identité). Le jeton de cookie de sécurité est auto-signé à l’aide de [DPAPI](https://msdn.microsoft.com/library/system.security.cryptography.protecteddata.aspx) et est créé avec le `UserId, SecurityStamp` et les informations d’heure d’expiration.

Le middleware du cookie vérifie le cookie à chaque demande. Le `SecurityStampValidator` méthode dans le `Startup` classe accède à la base de données et vérifie périodiquement, de tampon de sécurité tel que spécifié par le `validateInterval`. Cela se produit uniquement toutes les 30 minutes (dans notre exemple), sauf si vous modifiez votre profil de sécurité. L’intervalle de 30 minutes a été choisi pour réduire les allers-retours vers la base de données.

Le `SignInAsync` méthode doit être appelée lorsqu’une modification est apportée au profil de sécurité. Lorsque le profil de sécurité change, la base de données est mises à jour le `SecurityStamp` champ et sans appeler le `SignInAsync` méthode vous restera connecté *uniquement* jusqu'à ce que la prochaine fois que le pipeline OWIN accède à la base de données (la `validateInterval`). Vous pouvez le tester en modifiant le `SignInAsync` méthode pour retourner immédiatement et la définition du cookie `validateInterval` propriété de 30 minutes à 5 secondes :

[!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample8.cs?highlight=3)]

[!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample9.cs?highlight=20-21)]

Avec les modifications de code ci-dessus, vous pouvez modifier votre profil de sécurité (par exemple, en modifiant l’état de **activé à deux facteurs**) et vous allez être déconnecté dans 5 secondes lorsque la `SecurityStampValidator.OnValidateIdentity` méthode échoue. Supprimer la ligne de retour dans le `SignInAsync` (méthode), modifiez un autre profil de sécurité et que vous n’allez pas être déconnecté. Le `SignInAsync` méthode génère un nouveau cookie de sécurité.

<a id="enable2"></a>

## <a name="enable-two-factor-authentication"></a>Activer l’authentification à deux facteurs

Dans l’exemple d’application, vous devez utiliser l’interface utilisateur pour activer l’authentification à deux facteurs (2FA). Pour activer 2FA, cliquez sur votre nom d’utilisateur (alias de messagerie) dans la barre de navigation.![](two-factor-authentication-using-sms-and-email-with-aspnet-identity/_static/image9.png)  
Cliquez sur Activer 2FA.![](two-factor-authentication-using-sms-and-email-with-aspnet-identity/_static/image10.png) Déconnectez-vous, puis reconnectez-vous. Si vous avez activé la messagerie (voir mon [didacticiel précédent](account-confirmation-and-password-recovery-with-aspnet-identity.md)), vous pouvez sélectionner les SMS ou par courrier électronique pour 2FA.![](two-factor-authentication-using-sms-and-email-with-aspnet-identity/_static/image11.png) La page de codes de vérification s’affiche où vous pouvez saisir le code (à partir de SMS ou par courrier électronique).![](two-factor-authentication-using-sms-and-email-with-aspnet-identity/_static/image12.png) En cliquant sur le **n’oubliez pas de ce navigateur** case à cocher vous exempter de devoir utiliser 2FA pour vous connecter avec cet ordinateur et le navigateur. L’activation de 2FA et en cliquant sur le **n’oubliez pas de ce navigateur** vous fournira fort 2FA protection contre les utilisateurs malveillants, essayez d’accéder à votre compte, tant qu’ils n’ont pas accès à votre ordinateur. Pour cela, sur n’importe quel ordinateur privé que vous utilisez régulièrement. En définissant **n’oubliez pas de ce navigateur**, vous obtenez la sécurité renforcée de 2FA à partir d’ordinateurs que vous n’utilisez pas régulièrement, et la commodité sur ne pas avoir à passer par 2FA sur vos propres ordinateurs. 

<a id="reg"></a>
## <a name="how-to-register-a-two-factor-authentication-provider"></a>Comment inscrire un fournisseur d’authentification à deux facteurs

Lorsque vous créez un projet MVC, la *IdentityConfig.cs* fichier contient le code suivant pour inscrire un fournisseur d’authentification à deux facteurs :

[!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample10.cs?highlight=22-35)]

## <a name="add-a-phone-number-for-2fa"></a>Ajouter un numéro de téléphone pour 2FA

Le `AddPhoneNumber` méthode d’action dans le `Manage` génère un jeton de sécurité et l’envoie à téléphone numéro que vous avez fourni.

[!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample11.cs)]

Après avoir envoyé le jeton, il redirige vers le `VerifyPhoneNumber` méthode d’action, où vous pouvez entrer le code pour inscrire des SMS pour 2FA. SMS 2FA n’est pas utilisé jusqu'à ce que vous avez vérifié le numéro de téléphone.

## <a name="enabling-2fa"></a>L’activation de 2FA

Le `EnableTFA` 2FA permet de méthode d’action :

[!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample12.cs)]

Remarque la `SignInAsync` doit être appelé pour activer 2FA étant une modification pour le profil de sécurité. Lorsque 2FA est activé, l’utilisateur devra utiliser 2FA pour vous connecter, à l’aide de l’une des approches 2FA qu’ils ont enregistrés (SMS et par courrier électronique dans l’exemple).

Vous pouvez ajouter plusieurs fournisseurs 2FA tels que des générateurs de code QR ou vous pouvez écrire vous possédez (consultez [à l’aide de Google Authenticator avec ASP.NET Identity](http://www.beabigrockstar.com/blog/using-google-authenticator-asp-net-identity/)).

> [!NOTE]
> Les codes 2FA sont générés à l’aide de [algorithme de mot de passe à usage unique temporels](http://en.wikipedia.org/wiki/Time-based_One-time_Password_Algorithm) et les codes sont valides pendant six minutes. Si vous mettez plus de six minutes à entrer le code, vous obtenez un message d’erreur de code non valide.


<a id="combine"></a>

## <a name="combine-social-and-local-login-accounts"></a>Combiner des comptes de connexion de réseaux sociaux et local

Vous pouvez combiner les comptes locaux et réseaux sociaux en cliquant sur le lien de votre messagerie. Dans l’ordre suivant &quot; RickAndMSFT@gmail.com &quot; est tout d’abord créé en tant qu’une connexion locale, mais vous pouvez créer le compte en tant qu’un journal social en premier, puis ajouter une connexion locale.

![](two-factor-authentication-using-sms-and-email-with-aspnet-identity/_static/image13.png)

Cliquez sur le **gérer** lien. Notez la valeur 0 externe (connexions sociales) associé à ce compte.

![](two-factor-authentication-using-sms-and-email-with-aspnet-identity/_static/image14.png)

Cliquez sur le lien vers un autre journal dans le service et accepter les demandes d’application. Les deux comptes ont été combinés, vous ne pourrez pas vous connecter avec un compte. Vous pourriez vos utilisateurs pour ajouter des comptes locaux au cas où le journal des réseaux sociaux dans le service d’authentification est arrêté, ou plus probable qu’ils ont perdu l’accès à leur compte sociaux.

Dans l’image suivante, Tom est un journal des réseaux sociaux dans (que vous pouvez visualiser à partir de la **Logins externes : 1** présentée sur la page).

![](two-factor-authentication-using-sms-and-email-with-aspnet-identity/_static/image15.png)

En cliquant sur **choisir un mot de passe** vous permet d’ajouter un journal local sur avec le même compte.

![](two-factor-authentication-using-sms-and-email-with-aspnet-identity/_static/image16.png)

<a id="lock"></a>

## <a name="account-lockout-from-brute-force-attacks"></a>Verrouillage de compte à partir des attaques en force brute

Vous pouvez protéger les comptes sur votre application contre les attaques de dictionnaire en activant le verrouillage de l’utilisateur. Le code suivant dans la `ApplicationUserManager Create` méthode configure de verrouillage :

[!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample13.cs)]

Le code ci-dessus permet le verrouillage pour l’authentification à deux facteurs uniquement. Pendant que vous pouvez activer le verrouillage pour les connexions en modifiant `shouldLockout` à true dans le `Login` méthode du contrôleur de compte, nous vous recommandons de vous ne permettent pas de verrouillage pour les connexions, car elle rend le compte vulnérable aux [DOS](http://en.wikipedia.org/wiki/Denial-of-service_attack) connexion attaques. Dans l’exemple de code, le verrouillage est désactivé pour le compte administrateur créé dans le `ApplicationDbInitializer Seed` méthode :

[!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample14.cs?highlight=19)]

## <a name="requiring-a-user-to-have-a-validated-email-account"></a>Exiger un utilisateur possède un compte de messagerie validé

Le code suivant requiert un utilisateur d’avoir un compte de messagerie validé avant de se connecter :

[!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample15.cs?highlight=8-17)]

## <a name="how-signinmanager-checks-for-2fa-requirement"></a>Comment SignInManager vérifie pour la condition de 2FA

Les journaux locaux dans et journal sociaux dans, vérifiez si 2FA est activé. Si 2FA est activé, le `SignInManager` méthode d’ouverture de session retourne `SignInStatus.RequiresVerification`, et l’utilisateur sera redirigé vers la `SendCode` méthode d’action, où ils doivent entrer le code pour terminer le journal dans la séquence. Si l’utilisateur a RememberMe est défini sur le cookie d’utilisateurs locaux, les `SignInManager` retournera `SignInStatus.Success` et ils n’ont pas passer par 2FA.

[!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample16.cs?highlight=20-22)]

[!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample17.cs?highlight=10-11,17-18)]

Le code suivant illustre la `SendCode` méthode d’action. A [SelectListItem](https://msdn.microsoft.com/library/system.web.mvc.selectlistitem.aspx) est créée avec toutes les méthodes 2FA activées pour l’utilisateur. Le [SelectListItem](https://msdn.microsoft.com/library/system.web.mvc.selectlistitem.aspx) est passé à la [DropDownListFor](https://msdn.microsoft.com/library/system.web.ui.webcontrols.dropdownlist.aspx) assistance, ce qui permet à l’utilisateur à sélectionner l’approche 2FA (généralement par courrier électronique et SMS).

[!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample18.cs)]

Une fois que l’utilisateur enregistre l’approche 2FA, le `HTTP POST SendCode` l’appel de méthode d’action, le `SignInManager` envoie le code 2FA et l’utilisateur est redirigé vers la `VerifyCode` méthode d’action dans lequel ils peuvent entrer le code pour terminer la connexion.

[!code-csharp[Main](two-factor-authentication-using-sms-and-email-with-aspnet-identity/samples/sample19.cs?highlight=3,13-14,18)]

## <a name="2fa-lockout"></a>Verrouillage de 2FA

Bien que vous pouvez définir le verrouillage de compte sur les échecs de tentatives de mot de passe de connexion, cette approche rend votre connexion susceptibles d’être [DOS](http://en.wikipedia.org/wiki/Denial-of-service_attack) verrouillages. Nous vous recommandons de qu'utiliser le verrouillage de compte uniquement avec 2FA. Lorsque le `ApplicationUserManager` est créé, l’exemple de code définit le verrouillage de 2FA et `MaxFailedAccessAttemptsBeforeLockout` à cinq. Une fois qu’un utilisateur se connecte (via un compte local ou compte social), chaque tentative ayant échoué 2FA est stocké, et si le nombre maximal de tentatives est atteint, l’utilisateur est verrouillé pendant cinq minutes (vous pouvez définir l’heure avec verrouillage `DefaultAccountLockoutTimeSpan`).

<a id="addRes"></a>

## <a name="additional-resources"></a>Ressources supplémentaires

- [ASP.NET Identity recommandé ressources](../getting-started/aspnet-identity-recommended-resources.md) la liste complète des blogs, vidéos, didacticiels et great identité lie donc.
- [Application MVC est 5 avec Facebook, Twitter, LinkedIn et d’authentification Google OAuth2](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) montre également comment ajouter des informations de profil à la table des utilisateurs.
- [ASP.NET MVC et identité 2.0 : comprendre les aspects fondamentaux](http://typecastexception.com/post/2014/04/20/ASPNET-MVC-and-Identity-20-Understanding-the-Basics.aspx) par John Atten.
- [Confirmation du compte et la récupération de mot de passe avec l’identité de ASP.NET](account-confirmation-and-password-recovery-with-aspnet-identity.md)
- [Introduction à ASP.NET Identity](../getting-started/introduction-to-aspnet-identity.md)
- [Annonce de la version RTM d’ASP.NET Identity 2.0.0](https://blogs.msdn.com/b/webdev/archive/2014/03/20/test-announcing-rtm-of-asp-net-identity-2-0-0.aspx) par Pranav Rastogi.
- [Identité ASP.NET 2.0 : Configuration de la Validation du compte et l’autorisation de deux facteurs](http://typecastexception.com/post/2014/04/20/ASPNET-Identity-20-Setting-Up-Account-Validation-and-Two-Factor-Authorization.aspx) par John Atten.
