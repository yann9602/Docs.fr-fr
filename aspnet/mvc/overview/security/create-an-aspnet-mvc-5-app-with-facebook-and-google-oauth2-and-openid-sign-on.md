---
uid: mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on
title: "Créer MVC 5 application avec Facebook, Twitter, LinkedIn et Google OAuth2 Sign-on (c#) | Documents Microsoft"
author: Rick-Anderson
description: "Ce didacticiel vous montre comment générer une application web ASP.NET MVC 5 qui permet aux utilisateurs de se connecter en utilisant OAuth 2.0 avec les informations d’identification à partir d’un authenti externe..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/03/2015
ms.topic: article
ms.assetid: 81ee500f-fc37-40d6-8722-f1b64720fbb6
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on
msc.type: authoredcontent
ms.openlocfilehash: ccf4329e6684d07570bfaabfaa1a570664fb2ca3
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="create-an-aspnet-mvc-5-app-with-facebook-twitter-linkedin-and-google-oauth2-sign-on-c"></a>Créer une application ASP.NET MVC 5 avec Facebook, Twitter, LinkedIn et Google OAuth2 Sign-on (c#)
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> Ce didacticiel vous montre comment créer une application web ASP.NET MVC 5 qui permet aux utilisateurs de se connecter à l’aide de [OAuth 2.0](http://oauth.net/2/) avec les informations d’identification d’un fournisseur d’authentification externes, tels que Facebook, Twitter, LinkedIn, Microsoft ou Google. Par souci de simplicité, ce didacticiel se concentre sur l’utilisation des informations d’identification à partir de Facebook et Google.
> 
> L’activation de ces informations d’identification dans les sites web fournit des avantages significatifs, car des millions d’utilisateurs disposent déjà de comptes avec ces fournisseurs externes. Ces utilisateurs peuvent être plus de chances de souscrire à votre site, si elles n’ont pas à créer et à mémoriser un nouvel ensemble d’informations d’identification.
> 
> Voir aussi [application ASP.NET MVC 5 avec SMS et par courrier électronique d’authentification à deux facteurs](aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication.md).
> 
> Le didacticiel montre également comment ajouter des données de profil pour l’utilisateur et comment utiliser l’API d’appartenance pour ajouter des rôles. Ce didacticiel a été écrit par [Rick Anderson](https://blogs.msdn.com/rickAndy) (suivez me sur Twitter : [ @RickAndMSFT ](https://twitter.com/RickAndMSFT) ).


<a id="start"></a>
## <a name="getting-started"></a>Prise en main

Commencez par installer et exécuter [Visual Studio Express 2013 pour le Web](https://go.microsoft.com/fwlink/?LinkId=299058) ou [Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566). Installation de Visual Studio [2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390521) ou une version ultérieure. Pour Dropbox, GitHub, Linkedin, Instagram, mémoire tampon, force de vente, le flux, pile Exchange, Tripit, twitch, Twitter, Yahoo et bien plus encore l’aide, consultez ce [un guide qui](http://www.oauthforaspnet.com/).

> [!NOTE]
> Vous devez installer Visual Studio [2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390521) ou une version ultérieure pour utiliser Google OAuth 2 et déboguer localement sans avertissements de SSL.


Cliquez sur **nouveau projet** à partir de la **Démarrer** page, ou vous pouvez utiliser le menu et sélectionnez **fichier**, puis **nouveau projet**.

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image1.png)  
 

<a id="1st"></a>
## <a name="creating-your-first-application"></a>Créer votre première Application

Cliquez sur **nouveau projet**, puis sélectionnez **Visual C#** sur la gauche, puis **Web** , puis sélectionnez **Application Web ASP.NET**. Nommez votre projet « MvcAuth », puis **OK**.

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image2.png)

Dans le **nouveau projet ASP.NET** boîte de dialogue, cliquez sur **MVC**. Si l’authentification n’est pas **comptes d’utilisateur individuels**, cliquez sur le **modifier l’authentification** sélectionnez **comptes d’utilisateur individuels**. En vérifiant **hôte dans le cloud**, l’application sera très facile à héberger dans Azure.

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image3.png)

Si vous avez sélectionné **hôte dans le cloud**, terminez la boîte de dialogue Configurer.

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image4.png)


### <a name="use-nuget-to-update-to-the-latest-owin-middleware"></a>Utilisez NuGet pour mettre à jour vers la dernière intergiciel (middleware) OWIN

Utilisez le Gestionnaire de package NuGet pour mettre à jour le [intergiciel (middleware) OWIN](../../../aspnet/overview/owin-and-katana/getting-started-with-owin-and-katana.md). Sélectionnez **mises à jour** dans le menu de gauche. Vous pouvez cliquer sur le **tout mettre à jour** bouton ou vous pouvez rechercher uniquement les packages OWIN (illustrés dans l’image suivante) :

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image5.png)

Dans l’image ci-dessous, seuls les packages OWIN sont affichés :

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image6.png)

À partir de Package Manager Console (PMC), vous pouvez entrer le `Update-Package` commande qui met à jour tous les packages.

Appuyez sur **F5** ou **Ctrl + F5** pour exécuter l’application. Dans l’image ci-dessous, le numéro de port est 1234. Lorsque vous exécutez l’application, vous verrez un numéro de port différent.

Selon la taille de la fenêtre du navigateur, vous devrez peut-être cliquer sur l’icône de navigation pour afficher les **accueil**, **sur**, **Contact**, **inscrire**et **connecter** des liens.

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image7.png)  
![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image8.png) 

<a id="ssl"></a>
## <a name="setting-up-ssl-in-the-project"></a>Configuration de SSL dans le projet

Pour vous connecter à des fournisseurs d’authentification tels que Google et Facebook, vous devez configurer IIS Express pour utiliser SSL. Il est important de garder à l’aide de SSL après l’ouverture de session et pas effacer HTTP, le cookie de connexion est uniquement en tant que secret en tant que votre nom d’utilisateur et un mot de passe et sans l’utilisation de SSL, vous l’envoyez en texte clair sur le réseau. En outre, vous avez déjà pris le temps d’effectuer le protocole de transfert et sécurisez le canal (qui est le bloc de ce qui rend HTTPS plus lent que HTTP) avant l’exécution, le pipeline MVC donc rediriger vers HTTP une fois que vous êtes connecté ne sont pas rendre la requête actuelle ou futur demandes beaucoup plus rapides.

1. Dans **l’Explorateur de solutions**, cliquez sur le **MvcAuth** projet.
2. Appuyez sur la touche F4 pour afficher les propriétés du projet. Également, dans le **vue** menu que vous pouvez sélectionner **fenêtre Propriétés**.
3. Modification **SSL activé** sur True.  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image9.png)
4. Copiez l’URL SSL (qui sera `https://localhost:44300/` , sauf si vous avez créé d’autres projets SSL).
5. Dans **l’Explorateur de solutions**, bouton droit sur le **MvcAuth** de projet et sélectionnez **propriétés**.
6. Sélectionnez le **Web** onglet et collez l’URL SSL dans le **Url Project** boîte. Enregistrez le fichier (CTRL + S). Vous aurez besoin de cette URL à configurer des applications de l’authentification Facebook et Google.  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image10.png)
7. Ajouter le [RequireHttps](https://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) attribut le `Home` contrôleur pour toutes les demandes doit utiliser HTTPS. Une approche la plus sûre consiste à ajouter la [RequireHttps](https://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) filtre à l’application. Consultez la section &quot;protéger l’Application avec SSL et de l’attribut autoriser&quot; dans mon tutoral [créer une application ASP.NET MVC avec l’authentification et de la base de données SQL et le déployer vers Azure App Service](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data). Vous trouverez ci-dessous une partie du contrôleur Home.

    [!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample1.cs?highlight=1)]
8. Appuyez sur CTRL+F5 pour exécuter l'application. Si vous avez installé le certificat dans le passé, vous pouvez ignorer le reste de cette section et atteindre [création d’une application de Google OAuth 2 et la connexion de l’application au projet](#goog), dans le cas contraire, suivez les instructions pour approbation auto-signé certificat IIS Express a généré.  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image11.png)
9. Lecture la **avertissement de sécurité** boîte de dialogue, puis cliquez sur **Oui** si vous souhaitez installer le certificat de localhost.  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image12.png)
10. Internet Explorer affiche la *accueil* page et aucun avertissement de SSL.  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image13.png)
11. Google Chrome également accepte le certificat et affiche le contenu HTTPS sans avertissement. Firefox utilise son propre magasin de certificats, il affiche un avertissement. Pour notre application vous pouvez cliquer en toute sécurité sur **conscient des risques de**.   
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image14.png)

<a id="goog"></a>
## <a name="creating-a-google-app-for-oauth-2-and-connecting-the-app-to-the-project"></a>Création d’une application de Google OAuth 2 et la connexion de l’application au projet

1. Accédez à la [Console des développeurs Google](https://console.developers.google.com/).
1. Si vous n’avez pas créé un projet, sélectionnez **informations d’identification** dans l’onglet gauche, puis sélectionnez **créer**.
1. Dans l’onglet gauche, cliquez sur **informations d’identification**.
1. Cliquez sur **créer les informations d’identification** puis **ID de client OAuth**. 

    1. Dans le **créer un identifiant Client** boîte de dialogue, conservez la valeur par défaut **application Web** pour le type d’application.
    2. Définir le **autorisé de JavaScript** origine à l’URL SSL utilisée ci-dessus (`https://localhost:44300/` , sauf si vous avez créé d’autres projets SSL)
    3. Définir le **l’URI de redirection autorisés** à :  
         `https://localhost:44300/signin-google`
5. Cliquez sur l’élément de menu écran consentement OAuth, puis nommez votre messagerie adresse et le produit. Lorsque vous avez terminé la forme, cliquez sur **enregistrer**.
6. Cliquez sur l’élément de menu de bibliothèque, recherchez **Google + API**, cliquez dessus, puis appuyez sur Activer.
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image15.png)  
  
 L’image ci-dessous montre les API activées.  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image16.png)
7. À partir du Gestionnaire d’API Google API, visitez le **informations d’identification** tab pour obtenir le **ID Client**. Téléchargement pour enregistrer un fichier JSON comportant des secrets de l’application. Copiez et collez le **ClientId** et **ClientSecret** dans le `UseGoogleAuthentication` méthode trouvée dans le *Startup.Auth.cs* de fichiers dans le *App_Start* dossier. Le **ClientId** et **ClientSecret** valeurs indiquées ci-dessous sont des exemples et ne fonctionnent pas.

    [!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample2.cs?highlight=37-39)]

    > [!WARNING]
    > Sécurité - magasin jamais des données sensibles dans votre code source. Le compte et les informations d’identification sont ajoutées au code ci-dessus pour simplifier l’exemple. Consultez [meilleures pratiques pour le déploiement des mots de passe et autres données sensibles sur ASP.NET et Service d’applications Azure](../../../identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure.md).
8. Appuyez sur **CTRL + F5** pour générer et exécuter l’application. Cliquez sur le **connecter** lien.  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image17.png)
9. Sous **utiliser un autre service pour vous connecter**, cliquez sur **Google**.  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image18.png)

    > [!NOTE]
    > Si vous omettez une des étapes ci-dessus, vous obtiendrez une erreur HTTP 401. Revérifier les étapes ci-dessus. Si vous omettez un paramètre requis (par exemple **nom de produit**), ajouter manquants élément et d’enregistrer, peut prendre quelques minutes pour l’authentification à utiliser.
10. Vous allez être redirigé vers le site de google où vous devrez entrer vos informations d’identification.   
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image19.png)
11. Après avoir entré vos informations d’identification, vous devez accorder des autorisations à l’application web que vous venez de créer :
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image20.png)
12. Cliquez sur **accepter**. Vous allez maintenant être redirigé vers le **inscrire** page de l’application MvcAuth où vous pouvez inscrire votre compte Google. Vous avez la possibilité de modifier le nom de l’inscription de messagerie local utilisé pour votre compte Gmail, mais vous souhaitez généralement conserver l’alias de messagerie électronique par défaut (autrement dit, celui vous avez utilisé pour l’authentification). Cliquez sur **Register**.  
  
    ![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image21.png)

<a id="fb"></a>
## <a name="creating-the-app-in-facebook-and-connecting-the-app-to-the-project"></a>Création de l’application de Facebook et la connexion de l’application au projet

Pour l’authentification Facebook OAuth2, vous devez copier certains paramètres dans votre projet à partir d’une application que vous créez dans Facebook.

1. Dans votre navigateur, accédez à [https://developers.facebook.com/apps](https://developers.facebook.com/apps) et connectez-vous à entrer vos informations d’identification de Facebook.
2. Si vous n’êtes pas déjà inscrit en tant qu’un développeur de Facebook, cliquez sur **enregistrer en tant que développeur** et suivez les instructions à inscrire.
3. Sur le **applications** , cliquez sur **créer une application**.

    ![Créer une application](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image22.png)
4. Entrez un **nom de l’application** et **catégorie**, puis cliquez sur **créer application**.

    Cela doit être unique dans Facebook. Le **application Namespace** est la partie de l’URL que votre application utilise pour accéder à l’application Facebook pour l’authentification (par exemple, https://apps.facebook.com/ {application Namespace}). Si vous ne spécifiez pas un **application Namespace**, le **ID d’application** sera utilisé pour l’URL. Le **ID d’application** est un nombre long généré par le système qui s’affiche dans l’étape suivante.

    ![Créer la boîte de dialogue nouvelle application](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image23.png)
5. Envoyer la vérification de sécurité standard.

    ![Vérification de la sécurité](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image24.png)
6. Sélectionnez **paramètres** de la barre de menu de gauche.![ Barre de menus du développeur Facebook](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image25.png)
7. Sur le **base** section Paramètres de la page Sélectionnez **ajouter une plateforme** pour spécifier que vous ajoutez une application de site Web. ![Paramètres de base](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image26.png)
8. Sélectionnez **site Web** parmi les choix de la plateforme.  
  
    ![Choix de la plateforme](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image27.png)
9. Prenez note de votre **ID d’application** et votre **Secret d’application** afin que vous pouvez ajouter à la fois dans votre application MVC plus loin dans ce didacticiel. En outre, ajoutez l’URL du Site (`https://localhost:44300/`) pour tester votre application MVC. En outre, ajoutez un **messagerie du Contact**. Ensuite, sélectionnez **enregistrer les modifications**.   

    ![Page de détails de l’application de base](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image28.png)

    > [!NOTE]
    > Notez que vous serez uniquement en mesure de s’authentifier à l’aide de l’alias de messagerie que vous avez enregistré. Autres utilisateurs et les comptes de test ne sera pas en mesure d’enregistrer. Vous pouvez accorder des accès aux autres comptes de Facebook à l’application sur Facebook **rôles de développeur** onglet.
10. Dans Visual Studio, ouvrez *application\_Start\Startup.Auth.cs*.
11. Copiez et collez le **AppId** et **Secret d’application** dans le `UseFacebookAuthentication` (méthode). Le **AppId** et **Secret d’application** valeurs indiquées ci-dessous sont des exemples et ne fonctionnera pas.

    [!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample3.cs?highlight=33-35,38-39)]
12. Cliquez sur **enregistrer les modifications**.
13. Appuyez sur **CTRL + F5** pour exécuter l’application.


Sélectionnez **connecter** pour afficher la page de connexion. Cliquez sur **Facebook** sous **utiliser un autre service pour vous connecter.**

Entrez vos informations d’identification de Facebook.

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image29.png)

Vous devrez accorder l’autorisation de l’application pour accéder à votre profil public et la liste d’amis.

![Détails de l’application Facebook](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image30.png)

Vous êtes désormais connecté. Vous pouvez maintenant inscrire ce compte avec l’application.

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image31.png)

Lorsque vous inscrivez, une entrée est ajoutée à la *utilisateurs* table de la base de données d’appartenance.

<a id="mdb"></a>
## <a name="examine-the-membership-data"></a>Examiner les données d’appartenance

Dans le **vue** menu, cliquez sur **l’Explorateur de serveurs**.

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image32.png)

Développez **DefaultConnection (MvcAuth)**, développez **Tables**, cliquez droit **AspNetUsers** et cliquez sur **afficher les données de Table**.

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image33.png)

![données de la table aspnetusers](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image34.png)

<a id="ap"></a>
## <a name="adding-profile-data-to-the-user-class"></a>Ajout de données de profil à la classe d’utilisateur

Dans cette section vous ajouterez date de naissance et ville d’origine pour les données utilisateur pendant l’inscription, comme indiqué dans l’image suivante.

![reg avec accueil ville et Anniv.](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image35.png)

Ouvrez le *Models\IdentityModels.cs* de fichiers et d’ajouter des propriétés de zone urbaine accueil et de la date de naissance :

[!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample4.cs?highlight=3-4)]

Ouvrez le *Models\AccountViewModels.cs* de fichiers et le jeu de propriétés de zone urbaine de date et d’accueil dans de naissance `ExternalLoginConfirmationViewModel`.

[!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample5.cs?highlight=8-9)]

Ouvrez le *Controllers\AccountController.cs* et ajoutez le code pour la ville de date et d’accueil de naissance dans la `ExternalLoginConfirmation` méthode d’action comme indiqué :

[!code-csharp[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample6.cs?highlight=21-23)]

Ajouter la date de naissance et ville domestique à le *Views\Account\ExternalLoginConfirmation.cshtml* fichier :

[!code-cshtml[Main](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/samples/sample7.cshtml?highlight=27-40)]

Supprimer la base de données d’appartenance afin de pouvoir à nouveau enregistrer votre compte Facebook avec votre application et vérifiez que vous pouvez ajouter la nouvelle date de naissance et les informations de profil de ville d’origine.

À partir de **l’Explorateur de solutions**, cliquez sur le **afficher tous les fichiers** icône, puis cliquez sur droite *ajouter\_Data\aspnet-MvcAuth -&lt;dateStamp&gt;.mdf* et cliquez sur **supprimer**.

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image36.png)

À partir de la **outils** menu, cliquez sur **le Gestionnaire de Package NuGet**, puis cliquez sur **Package Manager Console** (PMC). Entrez les commandes suivantes dans le PMC.

1. Enable-Migrations
2. Migration ajouter Init
3. Update-Database

Exécutez l’application et utilisez FaceBook et Google pour vous connecter et d’inscrire certains utilisateurs.

## <a name="examine-the-membership-data"></a>Examiner les données d’appartenance

Dans le **vue** menu, cliquez sur **l’Explorateur de serveurs**.

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image37.png)

Bouton droit sur **AspNetUsers** et cliquez sur **afficher les données de Table**.

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image38.png)

Le `HomeTown` et `BirthDate` champs sont affichés ci-dessous.

![](create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on/_static/image39.png)

<a id="off"></a>
## <a name="logging-off-your-app-and-logging-in-with-another-account"></a>La déconnexion de votre application et se connecter avec un autre compte

Si vous ouvrez une session sur votre application avec Facebook et puis fermer la session et essayez de se connecter à nouveau avec un autre compte Facebook (à l’aide du même navigateur), vous serez immédiatement connecté pour le compte Facebook précédent, que vous avez utilisé. Pour utiliser un autre compte, vous devez accéder à Facebook et fermez la session à Facebook. La même règle s’applique à n’importe quel autre 3e partie fournisseur d’authentification. Ou bien, vous pouvez vous connecter avec un autre compte à l’aide d’un navigateur différent.

## <a name="next-steps"></a>Étapes suivantes

Consultez [présentation des fournisseurs de sécurité Yahoo et LinkedIn OAuth pour OWIN](http://www.jerriepelser.com/blog/introducing-the-yahoo-linkedin-oauth-security-providers-for-owin/) par Jerrie Pelser pour obtenir des instructions Yahoo et LinkedIn. Consultez Jerrie boutons de connexion sociale pour ASP.NET MVC 5 obtenir des boutons de connexion sociale activer des trucs assez.

Suivez le didacticiel de mon [créer une application ASP.NET MVC avec l’authentification et de la base de données SQL et le déployer vers Azure App Service](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data), qui continue de ce didacticiel et affiche les informations suivantes :

1. Comment déployer votre application sur Azure.
2. Comment sécuriser votre application avec des rôles.
3. Comment sécuriser votre application avec le [RequireHttps](https://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) et [Authorize](https://msdn.microsoft.com/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) filtres.
4. Comment utiliser l’API d’appartenance pour ajouter des utilisateurs et des rôles.

Veuillez laisser des commentaires sur la façon dont vous avez aimé ce didacticiel et nous pouvons améliorer. Vous pouvez également demander de nouvelles rubriques à [afficher Me comment avec le Code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code). Vous pouvez même demander et voter sur les nouvelles fonctionnalités à ajouter à ASP.NET. Par exemple, vous pouvez voter pour un outil [créer et gérer des utilisateurs et des rôles.](http://aspnet.uservoice.com/forums/41199-general-asp-net/suggestions/5646857-asp-net-identity-membership-db-tool-to-mangage-use)

Pour une bonne explication du fonctionnement des Services d’authentification externe ASP.NET, consultez de Robert McMurray [des Services d’authentification externe](https://asp.net/web-api/overview/security/external-authentication-services). Article de Robert va également dans les détails de l’activation de l’authentification de Microsoft et Twitter. Tom Dykstra l’excellent [didacticiel d’EF/MVC](../getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md) montre comment travailler avec Entity Framework.
