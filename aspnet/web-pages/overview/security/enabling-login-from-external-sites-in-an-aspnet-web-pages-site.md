---
uid: web-pages/overview/security/enabling-login-from-external-sites-in-an-aspnet-web-pages-site
title: "Identifié à l’aide des Sites externes dans ASP.NET Web Pages (Razor) Site | Documents Microsoft"
author: tfitzmac
description: "Cet article explique comment se connecter à votre site ASP.NET Web Pages (Razor) à l’aide de Facebook, Google, Twitter, Yahoo et autres sites, autrement dit, la prise en charge..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/21/2014
ms.topic: article
ms.assetid: ef852096-a5bf-47b3-9945-125cde065093
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/security/enabling-login-from-external-sites-in-an-aspnet-web-pages-site
msc.type: authoredcontent
ms.openlocfilehash: 47d15686194b15b7b06a99d63125c19a41f91ed9
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="logging-in-using-external-sites-in-an-aspnet-web-pages-razor-site"></a>Connexion à l’aide des Sites externes dans un Site de Pages (Razor) Web ASP.NET
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article explique comment se connecter à votre site ASP.NET Web Pages (Razor) à l’aide de Facebook, Google, Twitter, Yahoo et autres sites, autrement dit, la prise en charge OAuth et OpenID dans votre site.
> 
> Ce que vous allez apprendre :
> 
> - Comment activer la connexion à partir d’autres sites lorsque vous utilisez le modèle de WebMatrix Starter Site.
> 
> Il s’agit de la fonctionnalité d’ASP.NET introduite dans l’article :
> 
> - Le `OAuthWebSecurity` helper.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 2
> - WebMatrix 3

Les Pages Web ASP.NET inclut la prise en charge de [OAuth](http://oauth.net/) et [OpenID](http://openid.net/) fournisseurs. À l’aide de ces fournisseurs, vous pouvez permettre aux utilisateurs de se votre site à l’aide de leurs informations d’identification existantes à partir de Facebook, Twitter, Microsoft et Google. Par exemple, pour vous connecter à l’aide d’un compte Facebook, les utilisateurs peuvent simplement sélectionner une icône de Facebook, qui redirige vers la page de connexion Facebook où ils entrent leurs informations de l’utilisateur. Ils peuvent ensuite associer la connexion Facebook à leur compte sur votre site. Une amélioration associée aux fonctionnalités d’appartenance Web Pages est que les utilisateurs peuvent associer plusieurs connexions (y compris les connexions à partir des sites de réseau social) avec un seul compte sur votre site Web.

Cette illustration montre la page de connexion à partir de la **Starter Site** modèle, où un utilisateur peut choisir une icône Facebook, Twitter, Google ou Microsoft pour activer la journalisation à l’aide d’un compte externe :

![fournisseurs externes](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image1.png)

Vous pouvez activer l’appartenance OAuth et OpenID en supprimant commentaires quelques lignes de code dans le **Starter Site** modèle. Les méthodes et propriétés que vous utilisez pour travailler avec OAuth et OpenID fournisseurs sont dans le `WebMatrix.Security.OAuthWebSecurity` classe. Le **Starter Site** modèle inclut une infrastructure d’abonnement complet, avec une page de connexion, une base de données d’appartenance et tout le code que vous avez besoin permettre aux utilisateurs de se votre site à l’aide des informations d’identification locales ou celles d’un autre site .

Cette section fournit un exemple montrant comment permettre aux utilisateurs de se connecter à partir des sites externes à un site qui est basé sur le **Starter Site** modèle. Après avoir créé un site de démarrage, vous effectuez ce (suivre plus d’informations) :

- Pour les sites qui utilisent un fournisseur OAuth (Facebook, Twitter et Microsoft), vous créez une application sur le site externe. Cela vous donne les clés d’application dont vous avez besoin pour appeler la fonctionnalité de connexion pour ces sites.
- Pour les sites qui utilisent un fournisseur OpenID (Google), il est inutile de créer une application. Pour tous ces sites, vous devez disposer un compte pour se connecter et créer des applications de développeur.

    > [!NOTE]
    > Applications Microsoft acceptent uniquement une URL dynamique pour un site Web de travail, vous ne pouvez pas utiliser une URL de site Web local pour le test des connexions.
- Modifier quelques fichiers dans votre site Web afin de spécifier le fournisseur d’authentification approprié et soumettre une connexion au site que vous souhaitez utiliser.

Cet article fournit des instructions distinctes pour les tâches suivantes :

- [L’activation des connexions de Google](#To_enable_Google_logins)
- [L’activation des connexions d’accès Facebook](#To_enable_Facebook_logins)
- [L’activation des connexions d’accès Twitter](#To_enable_Twitter_logins)

<a id="To_enable_Google_logins"></a>
## <a name="enabling-google-logins"></a>L’activation des connexions de Google

1. Créez ou ouvrez un site ASP.NET Web Pages est basé sur le modèle de WebMatrix Starter Site.
2. Ouvrez le  *\_AppStart.cshtml* page et supprimez les commentaires de la ligne de code suivante. 

    [!code-css[Main](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/samples/sample1.css)]

### <a name="testing-google-login"></a>Test de la connexion de Google

1. Exécutez le *default.cshtml* page de votre site et choisissez la **connecter** bouton.
2. Sur le *connexion* page, dans le **utiliser un autre service pour vous connecter** section, choisissez la **Google** ou **Yahoo** bouton Envoyer. Cet exemple utilise la connexion de Google. 

    La page web redirige la demande vers la page de connexion Google.

    ![Connexion Google](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image2.png)
3. Entrez les informations d’identification d’un compte Google existant.
4. Si Google vous demande si vous souhaitez autoriser *Localhost* pour utiliser les informations du compte, cliquez sur **autoriser**.

    Le code utilise le jeton de Google pour authentifier l’utilisateur et il retourne ensuite à cette page sur votre site Web. Cette page permet aux utilisateurs d’associer leur connexion Google avec un compte existant sur votre site Web, ou ils peuvent inscrire un nouveau compte sur votre site à associer la connexion externe.

    ![OAuth-5](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image3.png)
5. Choisissez le **associer** bouton. Le navigateur retourne à la page d’accueil de votre application.

<a id="To_enable_Facebook_logins"></a>
## <a name="enabling-facebook-logins"></a>L’activation des connexions d’accès Facebook

1. Accédez à la [site des développeurs Facebook](https://developers.facebook.com/apps) (se connecter si vous n’êtes pas déjà connecté).
2. Choisissez le **créer une application** bouton, puis suivez les invites pour nommer et de créer l’application.
3. Dans la section **sélectionnez la façon dont votre application s’intègre avec Facebook**, choisissez le **site Web** section.
4. Renseignez le **URL du Site** champ avec l’URL de votre site (par exemple, `http://www.example.com`). Le **domaine** champ est facultatif ; vous pouvez l’utiliser pour fournir l’authentification pour un domaine entier (tel que *example.com*). 

    > [!NOTE]
    > Si vous exécutez un site sur votre ordinateur local avec une URL telle que `http://localhost:12345` (où le nombre est un numéro de port local), vous pouvez ajouter cette valeur à la **URL du Site** champ pour tester votre site. Toutefois, chaque fois que le numéro de port de vos modifications de site local, vous devez mettre à jour le **URL du Site** champ de votre application.
5. Choisissez le **enregistrer les modifications** bouton.
6. Choisissez le **applications** onglet à nouveau et afficher la page de démarrage pour votre application.
7. Copie le **ID d’application** et **Secret d’application** valeurs pour votre application et les coller dans un fichier texte temporaire. Vous allez passer ces valeurs pour le fournisseur de Facebook dans votre code de site Web.
8. Quitter le site de développement Facebook.

Maintenant vous apportez des modifications à deux pages de votre site Web afin que les utilisateurs seront en mesure de se connecter au site à l’aide de leur compte Facebook.

1. Créez ou ouvrez un site ASP.NET Web Pages est basé sur le modèle de WebMatrix Starter Site.
2. Ouvrez le  *\_AppStart.cshtml* page et les commentaires du code pour le fournisseur Facebook OAuth. Le bloc de code sans commentaire ressemble à ceci : 

    [!code-csharp[Main](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/samples/sample2.cs)]
3. Copie le **ID d’application** valeur à partir de l’application Facebook en tant que la valeur de le `appId` paramètre (à l’intérieur des guillemets simples).
4. Copie **Secret d’application** valeur à partir de l’application Facebook en tant que le `appSecret` la valeur du paramètre.
5. Enregistrez et fermez le fichier.

### <a name="testing-facebook-login"></a>Test de connexion Facebook

1. Exécution du site *default.cshtml* page et choisissez la **connexion** bouton.
2. Sur le *connexion* page, dans le **utiliser un autre service pour vous connecter** , choisissez le **Facebook** icône. 

    La page web redirige la demande vers la page de connexion Facebook.

    ![OAuth-2](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image4.png)
3. Connectez-vous à un compte Facebook. 

    Le code utilise le jeton de Facebook pour vous authentifier et renvoie à une page où vous pouvez associer votre compte de connexion Facebook avec un compte de connexion de votre site. Votre utilisateur nom ou adresse de messagerie est renseigné dans la **messagerie** champ sur le formulaire.

    ![OAuth-5](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image5.png)
4. Choisissez le **associer** bouton. 

    Le navigateur retourne à la page d’accueil et que vous êtes connecté.

<a id="To_enable_Twitter_logins"></a>
## <a name="enabling-twitter-logins"></a>L’activation des connexions d’accès Twitter

1. Accédez à la [site des développeurs Twitter](https://dev.twitter.com/).
2. Choisissez le **créer une application** lier et ouvrir une session sur le site.
3. Sur le **créer une Application** écran, renseignez le **nom** et **Description** champs.
4. Dans le **site Web** , entrez l’URL de votre site (par exemple, `http://www.example.com`). 

    > [!NOTE]
    > Si vous testez votre site localement (à l’aide d’une URL telle que `http://localhost:12345`), Twitter ne peut pas accepter l’URL. Toutefois, vous pourrez peut-être utiliser l’adresse IP de bouclage locale (par exemple `http://127.0.0.1:12345`). Cela simplifie le processus de test de votre application localement. Toutefois, chaque fois que le numéro de port de votre site local change, vous devez mettre à jour le **site Web** champ de votre application.
5. Dans le **URL de rappel** , saisissez une URL de la page dans votre site Web que vous souhaitez que les utilisateurs renvoyer à une fois la connexion Twitter. Par exemple, pour envoyer les utilisateurs vers la page d’accueil du Site de démarrage (qui reconnaît leur état connecté), entrez la même URL que vous avez entré dans le **site Web** champ.
6. Acceptez les termes du contrat et sélectionnez le **créer votre application Twitter** bouton.
7. Sur le **Mes Applications** accueil page, choisissez l’application que vous avez créé.
8. Sur le **détails** onglet, faites défiler vers le bas et sélectionnez le **créer mon jeton d’accès** bouton.
9. Sur le **détails** onglet, copiez la **clé de consommateur** et **Secret de consommateur** valeurs pour votre application et les coller dans un fichier texte temporaire. Vous allez passer ces valeurs au fournisseur Twitter dans votre code de site Web.
10. Quittez le site Twitter.

Maintenant vous apportez des modifications à deux pages de votre site Web afin que les utilisateurs pourront se connecter au site à l’aide de leur compte Twitter.

1. Créez ou ouvrez un site ASP.NET Web Pages est basé sur le modèle de WebMatrix Starter Site.
2. Ouvrez le  *\_AppStart.cshtml* page et les commentaires du code pour le fournisseur OAuth pour Twitter. Le bloc de code sans commentaire ressemble à ceci : 

    [!code-csharp[Main](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/samples/sample3.cs)]
3. Copie le **clé de consommateur** valeur à partir de l’application Twitter comme valeur de le `consumerKey` paramètre (à l’intérieur des guillemets simples).
4. Copie le **Secret de consommateur** valeur à partir de l’application Twitter comme valeur de le `consumerSecret` paramètre.
5. Enregistrez et fermez le fichier.

### <a name="testing-twitter-login"></a>Test de connexion Twitter

1. Exécutez le *default.cshtml* page de votre site et choisissez la **connexion** bouton.
2. Sur le *connexion* page, dans le **utiliser un autre service pour vous connecter** , choisissez le **Twitter** icône. 

    La page web redirige la demande vers une page de connexion Twitter pour l’application que vous avez créé.

    ![Oauth-4](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image6.png)
3. Connectez-vous à un compte Twitter.
4. Le code utilise le jeton Twitter pour authentifier l’utilisateur et vous ramène à une page dans laquelle vous pouvez associer votre nom de connexion avec votre compte de site Web. Votre nom ou adresse de messagerie est renseigné dans la **messagerie** champ sur le formulaire.

    ![OAuth-5](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image7.png)
5. Choisissez le **associer** bouton. 

    Le navigateur retourne à la page d’accueil et que vous êtes connecté.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Ressources supplémentaires


- [Personnalisation du comportement de l’échelle du Site](https://go.microsoft.com/fwlink/?LinkId=202906)
- [Ajout de sécurité et l’appartenance à un Site de Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkID=202904)
