---
title: "Authentification à deux facteurs avec SMS"
author: rick-anderson
description: "Montre comment configurer une authentification à deux facteurs (2FA) avec ASP.NET Core"
keywords: "ASP.NET Core, SMS, l’authentification, 2FA, l’authentification à deux facteurs, authentification à deux facteurs"
ms.author: riande
manager: wpickett
ms.date: 8/15/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/2fa
ms.openlocfilehash: 77404de2f367cb12ba25433899198b69f9e5a7f2
ms.sourcegitcommit: 9a22c64759a7285ba788a37039bea5fe95f45f21
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2017
---
# <a name="two-factor-authentication-with-sms"></a>Authentification à deux facteurs avec SMS

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [développeurs-Suisse](https://github.com/Swiss-Devs)

Ce didacticiel s’applique à ASP.NET Core 1.x uniquement. Consultez [génération permettant un Code QR pour les applications d’authentification dans ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) pour ASP.NET Core 2.0 et versions ultérieures.

Ce didacticiel montre comment configurer l’authentification à deux facteurs (2FA) à l’aide de SMS. Des instructions sont fournies pour [twilio](https://www.twilio.com/) et [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), mais vous pouvez utiliser n’importe quel autre fournisseur SMS. Nous vous recommandons de terminer [Confirmation du compte et la récupération de mot de passe](accconfirm.md) avant de commencer ce didacticiel.

Afficher le [exemple terminé](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA). [Comment télécharger](xref:tutorials/index#how-to-download-a-sample).

## <a name="create-a-new-aspnet-core-project"></a>Créez un nouveau projet ASP.NET Core

Créer une application web ASP.NET Core nommée `Web2FA` avec les comptes d’utilisateur individuels. Suivez les instructions de [SSL de l’application dans une application ASP.NET Core](xref:security/enforcing-ssl) pour configurer et exiger le protocole SSL.

### <a name="create-an-sms-account"></a>Créer un compte SMS

Créer un compte SMS, par exemple, à partir de [twilio](https://www.twilio.com/) ou [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/). Enregistrer les informations d’identification d’authentification (pour twilio : accountSid et du jeton d’authentification, pour ASPSMS : clé d’utilisateur a et le mot de passe).

#### <a name="figuring-out-sms-provider-credentials"></a>Déterminer les informations d’identification du fournisseur SMS

**Twilio :**  
Sous l’onglet tableau de bord de votre compte Twilio, copiez la **SID de compte** et **Auth jeton**.

**ASPSMS :**  
À partir des paramètres de votre compte, accédez à **clé d’utilisateur a** et copiez-le avec votre **mot de passe**.

Nous allons stocker ultérieurement ces valeurs avec l’outil Gestionnaire de secret dans les clés de `SMSAccountIdentification` et `SMSAccountPassword`.

#### <a name="specifying-senderid--originator"></a>Spécifiant l’ID d’expéditeur / donneur d’ordre

**Twilio :**  
À partir de l’onglet numéros, copiez votre Twilio **numéro de téléphone**. 

**ASPSMS :**  
Dans le Menu des expéditeurs déverrouiller, déverrouillage d’un ou plusieurs expéditeurs, ou choisissez un donneur d’ordre alphanumérique (non pris en charge par tous les réseaux). 

Nous allons stocker plus tard cette valeur avec l’outil Gestionnaire de la clé secrète au sein de la clé `SMSAccountFrom`.


### <a name="provide-credentials-for-the-sms-service"></a>Fournissez les informations d’identification pour le service SMS

Nous allons utiliser la [modèle d’Options](xref:fundamentals/configuration#options-config-objects) pour accéder aux paramètres de compte et une clé utilisateur. 

   * Créez une classe pour extraire la clé SMS sécurisée. Pour cet exemple, le `SMSoptions` classe est créée dans le *Services/SMSoptions.cs* fichier.

[!code-csharp[Main](2fa/sample/Web2FA/Services/SMSoptions.cs)]

Définir le `SMSAccountIdentification`, `SMSAccountPassword` et `SMSAccountFrom` avec la [outil Gestionnaire de secret](xref:security/app-secrets). Exemple :

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```
* Ajoutez le package NuGet pour le fournisseur SMS. À partir du Package Manager Console (PMC) exécuter :

**Twilio :**  
`Install-Package Twilio`

**ASPSMS :**  
`Install-Package ASPSMS`


* Ajoutez le code dans le *Services/MessageServices.cs* fichier pour activer les SMS. Utilisez la Twilio ou la section ASPSMS :


**Twilio :**  
[!code-csharp[Main](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**ASPSMS :**  
[!code-csharp[Main](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>Configurer le démarrage à utiliser`SMSoptions`

Ajouter `SMSoptions` au conteneur de service dans le `ConfigureServices` méthode dans le *Startup.cs*:

[!code-csharp[Main](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>Activer l’authentification à deux facteurs

Ouvrez le *Views/Manage/Index.cshtml* fichier de vue Razor et supprimer les caractères de commentaire (aucune balise n’est donc commenté).

## <a name="log-in-with-two-factor-authentication"></a>Se connecter avec l’authentification à deux facteurs

* Exécutez l’application et inscrire un nouvel utilisateur

![Affichage de Registre Web application ouverte dans Microsoft Edge](2fa/_static/login2fa1.png)

* Cliquez sur votre nom d’utilisateur, ce qui active la `Index` méthode d’action de contrôleur de gestion. Puis cliquez sur le numéro de téléphone **ajouter** lien.

![Gérer les affichages](2fa/_static/login2fa2.png)

* Ajouter un numéro de téléphone qui recevra le code de vérification et appuyez sur **envoyer le code de vérification**.

![Ajouter une page de numéro de téléphone](2fa/_static/login2fa3.png)

* Vous obtenez un message texte avec le code de vérification. Entrer et appuyez sur **envoyer**

![Vérifier la page du numéro de téléphone](2fa/_static/login2fa4.png)

Si vous n’obtenez pas un message texte, consultez la page du journal twilio.

* L’affichage de gestion affiche que votre numéro de téléphone a été ajouté avec succès.

![Gérer les affichages](2fa/_static/login2fa5.png)

* Appuyez sur **activer** pour activer l’authentification à deux facteurs.

![Gérer les affichages](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>Authentification à deux facteurs test

* Fermez la session.

* Se connecter.

* Le compte d’utilisateur a activé l’authentification à deux facteurs, vous devez fournir le second facteur d’authentification. Dans ce didacticiel vous avez activé la vérification par téléphone. Les modèles intégrés vous permettent également à configurer la messagerie en tant que le second facteur. Vous pouvez configurer des facteurs de deuxième supplémentaires pour l’authentification telles que des codes QR. Appuyez sur **envoyer**.

![Affichage du Code de vérification d’envoi](2fa/_static/login2fa7.png)

* Entrez le code que vous obtenez dans le message SMS.

* En cliquant sur le **n’oubliez pas de ce navigateur** case à cocher vous exempter de devoir utiliser 2FA pour vous connecter lorsque vous utilisez le même périphérique et le navigateur. L’activation de 2FA et en cliquant sur **n’oubliez pas de ce navigateur** vous fournira fort 2FA protection contre les utilisateurs malveillants, essayez d’accéder à votre compte, tant qu’ils n’ont pas accès à votre appareil. Pour cela, sur n’importe quel appareil privé que vous utilisez régulièrement. En définissant **n’oubliez pas de ce navigateur**, vous obtenez la sécurité renforcée de 2FA à partir d’appareils que vous n’utilisez pas régulièrement, et la commodité sur ne pas avoir à passer par 2FA sur vos propres appareils.

![Vérifier l’affichage](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>Verrouillage de compte pour la protection contre les attaques en force brute

Nous vous recommandons de qu'utiliser le verrouillage de compte avec 2FA. Une fois qu’un utilisateur se connecte (via un compte local ou social), chaque tentative ayant échoué 2FA est stockée et si le nombre maximal de tentatives (valeur par défaut est 5) est atteinte, l’utilisateur est verrouillé pendant cinq minutes (vous pouvez définir l’heure avec verrouillage `DefaultAccountLockoutTimeSpan`). Les éléments suivants configurent compte peuvent être verrouillés pendant 10 minutes après 10 tentatives ayant échoué.

[!code-csharp[Main](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)] 
