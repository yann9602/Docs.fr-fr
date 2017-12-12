---
uid: web-pages/overview/security/16-adding-security-and-membership
title: "Ajout de sécurité et l’appartenance à un site Web ASP.NET de Pages (Razor) Site | Documents Microsoft"
author: tfitzmac
description: "Ce chapitre explique comment sécuriser votre site Web de sorte que certaines pages sont disponibles uniquement pour les personnes qui se connectent. (Vous allez également apprendre à créer tha pages..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/24/2014
ms.topic: article
ms.assetid: 7a77c2c0-deea-4290-a9c3-97958891758e
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/security/16-adding-security-and-membership
msc.type: authoredcontent
ms.openlocfilehash: f0cee96005416bd9ef8befaf34890f415cf5ff3c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="adding-security-and-membership-to-an-aspnet-web-pages-razor-site"></a>Ajout de sécurité et l’appartenance à un Site de Pages (Razor) Web ASP.NET
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article explique comment sécuriser un site Web ASP.NET Web Pages (Razor) afin que certaines pages sont disponibles uniquement pour les personnes qui se connectent. (Vous allez également apprendre à créer des pages accessibles à toute personne.)
> 
> **Ce que vous allez apprendre :** 
> 
> - Comment créer un site Web qui a une page d’inscription et une page de connexion afin que, pour certaines pages, vous pouvez limiter l’accès aux membres uniquement.
> - Comment créer des pages publics et membres uniquement.
> - Comment définir des rôles, qui sont des groupes qui ont des autorisations de sécurité différent sur votre site, et comment affecter des utilisateurs à un rôle.
> - Comment utiliser CAPTCHA pour empêcher les programmes automatiques (robots) à partir de la création de comptes de membre.
>   
> 
> Voici les fonctionnalités d’ASP.NET introduites dans l’article :
> 
> - Le WebMatrix **Starter Site** modèle.
> - Le `WebSecurity` helper et `Roles` classe.
> - Le `ReCaptcha` helper.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 2
> - WebMatrix 3
> - Bibliothèque d’applications auxiliaires Web ASP.NET


Vous pouvez configurer votre site Web afin que les utilisateurs peuvent se connecter à &#8212; Autrement dit, afin que le site prend en charge *appartenance*. Cela peut être utile pour de nombreuses raisons. Par exemple, votre site peut avoir des pages qui doivent être disponibles uniquement pour les membres. Dans certains cas, vous pouvez avoir besoin d’utilisateurs de se connecter afin de vous envoyer des commentaires ou de laisser un commentaire.

Même si votre site Web prend en charge l’appartenance, les utilisateurs ne sont pas forcément nécessaire de se connecter avant qu’ils utilisent des pages sur le site. Les utilisateurs connectés ne sont pas appelés *les utilisateurs anonymes*.

Un utilisateur peut s’inscrire sur votre site Web et peut ensuite se connecter au site. Le site Web requiert un nom d’utilisateur (une adresse de messagerie) et un mot de passe pour confirmer que les utilisateurs sont ceux qu’il prétend être. Ce processus de connexion et de confirmer l’identité d’un utilisateur est appelé *authentification*.

Vous pouvez définir la sécurité et l’appartenance de différentes façons :

- Si vous utilisez WebMatrix, un moyen simple consiste à créer en tant que nouveau site basé sur le **Starter Site** modèle. Ce modèle est déjà configuré pour la sécurité et l’appartenance et a déjà une page d’inscription, une page de connexion et ainsi de suite.

    Le site créé par le modèle a également une option pour permettre aux utilisateurs de se connecter à l’aide d’un site externe comme Facebook, Google, ou Twitter.
- Si vous souhaitez ajouter la sécurité à un site existant, ou si vous ne souhaitez pas utiliser le **Starter Site** modèle, vous pouvez créer votre propre page d’inscription, page de connexion et ainsi de suite.

Cet article se concentre sur la première option &mdash; comment sécuriser à l’aide de la **Starter Site** modèle. Il fournit également des informations de base sur la façon d’implémenter votre propre sécurité et fournit des liens vers des informations sur la procédure à suivre. Il existe également des informations sur comment activer les connexions externes, ce qui est décrit plus en détail dans un article distinct.

## <a name="creating-website-security-using-the-starter-site-template"></a>Sécurité de site Web à l’aide du modèle de Site de démarrage

Dans WebMatrix, vous pouvez utiliser la **Starter Site** modèle pour créer un site Web qui contient les éléments suivants :

- Une base de données qui est utilisé pour stocker les noms d’utilisateur et mots de passe pour les membres.
- Une page d’inscription qui peuvent d’enregistrer les utilisateurs anonymes (nouveau).
- Une page de connexion et de déconnexion.
- Une page de récupération et de réinitialisation du mot de passe.

La procédure suivante décrit comment créer le site et le configurer.

1. Démarrer WebMatrix et dans le **Quick Start** page, sélectionnez **Site à partir du modèle**.
2. Sélectionnez le **Starter Site** modèle, puis cliquez sur **OK**. WebMatrix crée un nouveau site.
3. Dans le volet gauche, cliquez sur le **fichiers** sélecteur d’espace de travail.
4. Dans le dossier racine de votre site Web, ouvrez le  *\_AppStart.cshtml* fichier, qui est un fichier spécial qui est utilisé pour contenir des paramètres globaux. Il contient des instructions commentées à l’aide de la `//` caractères :

    [!code-csharp[Main](16-adding-security-and-membership/samples/sample1.cs)]

    Ces instructions configurer le `WebMail` application d’assistance, ce qui peut être utilisée pour envoyer un courrier électronique. Le système d’appartenance peut utiliser la messagerie pour envoyer des messages de confirmation lorsque les utilisateurs s’inscrire ou lorsqu’ils souhaitent modifier leurs mots de passe. (Par exemple, une fois que les utilisateurs s’inscrire, ils obtiennent un message électronique qui inclut un lien pour lesquels ils peuvent cliquer pour terminer le processus d’inscription.)

    Envoi de courrier électronique nécessite un accès à un serveur SMTP, comme décrit dans [Ajout du courrier électronique à un Site de Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202899). Vous allez stocker les paramètres de messagerie dans ce centre  *\_AppStart.cshtml* de fichiers afin que vous n’êtes pas obligé de les coder plusieurs fois dans chaque page qui peut envoyer un courrier électronique. (Vous n’avez pas besoin de configurer les paramètres SMTP pour configurer une base de données d’inscription ; vous devez uniquement les paramètres SMTP si vous souhaitez valider des utilisateurs à partir de leurs alias de messagerie électronique et permettre aux utilisateurs de réinitialiser un mot de passe oublié).
5. Ne pas commenter les instructions en supprimant `//` situé devant chacun d’eux.

    Si vous ne souhaitez pas configurer la confirmation par courrier électronique, vous pouvez ignorer cette étape et l’étape suivante. Si les valeurs SMTP ne sont pas définies, le nouveau compte est immédiatement disponible sans un e-mail de confirmation.
6. Modifier les paramètres de courrier électronique suivants dans le code :

    - Définissez `WebMail.SmtpServer` au nom du serveur SMTP que vous avez accès.
    - Laissez `WebMail.EnableSsl` la valeur `true`. Ce paramètre permet de sécuriser les informations d’identification sont envoyées au serveur SMTP en les chiffrant.
    - Définissez `WebMail.UserName` au nom d’utilisateur pour votre compte de serveur SMTP.
    - Définissez `WebMail.Password` au mot de passe pour votre compte de serveur SMTP.
    - Définissez `WebMail.From` à votre adresse de messagerie. Il s’agit de l’adresse de messagerie, le message est envoyé à partir de.

    > [!NOTE] 
    > 
    > **Conseil** pour plus d’informations sur les valeurs de ces propriétés, consultez [configuration des paramètres de messagerie](https://go.microsoft.com/fwlink/?LinkID=202906#configuring_email_settings) dans [personnalisation du comportement au niveau du Site pour ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkID=202906).
7. Enregistrez et fermez  *\_AppStart.cshtml*.
8. Exécutez le *Default.cshtml* page dans un navigateur.

    ![sécurité-appartenance-2](16-adding-security-and-membership/_static/image1.png)

    > [!NOTE]
    > Si vous voyez une erreur indiquant qu’une propriété doit être une instance de `ExtendedMembershipProvider`, le site ne peut pas être configuré pour utiliser le système d’appartenance ASP.NET Web Pages (SimpleMembership). Cela peut parfois se produire si le serveur du fournisseur d’hébergement est configuré différemment de votre serveur local. Pour résoudre ce problème, ajoutez l’élément suivant sur le site *Web.config* fichier :
    > 
    > [!code-xml[Main](16-adding-security-and-membership/samples/sample2.xml)]
    > 
    > Ajoutez cet élément en tant qu’enfant de la `<configuration>` élément et en tant qu’homologue de la `<system.web>` élément.
9. Dans le coin supérieur droit de la page, cliquez sur le **inscrire** lien. Le *Register.cshtml* page s’affiche.
10. Entrez un nom d’utilisateur et un mot de passe, puis activez **inscrire**.

    ![sécurité-appartenance-3](16-adding-security-and-membership/_static/image2.png)

    Lorsque vous avez créé le site Web à partir de la **Starter Site** modèle, une base de données nommée *StarterSite.sdf* a été créé dans la table *application\_données* dossier. Pendant l’inscription, vos informations utilisateur sont ajoutées à la base de données. Si vous définissez les valeurs SMTP, un message est envoyé à l’adresse de messagerie, que vous avez utilisé afin de terminer l’inscription.

    ![sécurité-appartenance-4](16-adding-security-and-membership/_static/image3.png)
11. Accédez à votre programme de messagerie et de trouver le message, ce qui aura votre code de confirmation et un lien hypertexte vers le site.
12. Cliquez sur le lien hypertexte pour activer votre compte. Le lien hypertexte de confirmation s’ouvre une page de confirmation d’inscription.

    ![sécurité-appartenance-5](16-adding-security-and-membership/_static/image4.png)
- Cliquez sur le **connexion** lier, puis connectez-vous en utilisant le compte que vous avez enregistré.

    Une fois que vous vous connectez, la **connexion** et **inscrire** liens sont remplacés par un **déconnexion** lien. Votre nom de connexion s’affiche sous forme de lien. (Le lien permet d’aller à une page dans laquelle vous pouvez modifier votre mot de passe.)

    ![sécurité-appartenance-6](16-adding-security-and-membership/_static/image5.png)

    > [!NOTE]
    > Par défaut, ASP.NET web pages envoyer des informations d’identification au serveur en texte clair (en tant que texte lisible). Un site de production doit-elle utiliser le protocole HTTP sécurisé (https://, également connu sous le *SSL* ou SSL) pour chiffrer les informations sensibles qui sont échangées avec le serveur. Vous pouvez requis messagerie envoi de messages à l’aide de SSL en définissant `WebMail.EnableSsl=true` comme dans l’exemple précédent. Pour plus d’informations sur le protocole SSL, consultez [sécurisation des Communications Web : les certificats SSL et https://](https://go.microsoft.com/fwlink/?LinkId=208660).

## <a name="additional-membership-functionality-in-the-site"></a>Fonctionnalité d’appartenance supplémentaires dans le Site

Votre site contient d’autres fonctionnalités qui permet aux utilisateurs de gérer leurs comptes. Les utilisateurs peuvent effectuer le des opérations suivantes :

- Modifier leurs mots de passe. Une fois connecté, il peut cliquer le nom d’utilisateur (qui est un lien). Cela mène à une page où ils peuvent créer un nouveau mot de passe (*Account/ChangePassword.cshtml*).
- Récupérer un mot de passe oublié. Sur la page de connexion, il existe un lien (**vous avez oublié votre mot de passe ?**) qui mène à une page (*Account/ForgotPassword.cshtml*) dans lequel ils peuvent entrer une adresse de messagerie. Le site leur envoie un message électronique contenant un lien sur lesquels ils peuvent cliquer pour définir un nouveau mot de passe (*Account/PasswordReset.cshtml*).

Vous pouvez également laisser les utilisateurs peuvent également se connecter en utilisant un site externe, comme expliqué ultérieurement.

<a id="Creating_a_Members-Only_Page"></a>
## <a name="creating-a-members-only-page"></a>Création d’une Page réservée aux membres

Pour l’instant, tout le monde peut parcourir à n’importe quelle page de votre site Web. Toutefois, vous pouvez avoir des pages qui sont disponibles uniquement pour les personnes qui se sont connectés (autrement dit, aux membres). ASP.NET vous permet de créer des pages qui sont accessibles uniquement par des membres connecté. En règle générale, si les utilisateurs anonymes essaient d’accéder à une page membres uniquement, vous les redirigez vers la page de connexion.

Dans cette procédure, vous allez créer un dossier qui contient les pages qui sont disponibles uniquement pour les utilisateurs connectés.

1. À la racine du site, créez un nouveau dossier. (Dans le ruban, cliquez sur la flèche située sous **nouveau** , puis **nouveau dossier**.)
2. Nommez le nouveau dossier *membres*.
3. À l’intérieur de la *membres* dossier, créez une nouvelle page et l’a nommé *MembersInformation.cshtml*.
4. Remplacez le contenu existant par le code et le balisage suivant :

    [!code-cshtml[Main](16-adding-security-and-membership/samples/sample3.cshtml)]

    Ce code teste le `IsAuthenticated` propriété de la `WebSecurity` objet, qui retourne `true` si l’utilisateur s’est connecté. Si l’utilisateur n’est pas connecté dans le code appelle `Response.Redirect` à envoyer à l’utilisateur le *Login.cshtml* page dans le *compte* dossier.

    L’URL de la redirection inclut un `returnUrl` interroger la valeur de chaîne qui utilise `Request.Url.LocalPath` pour définir le chemin d’accès de la page actuelle. Si vous définissez la `returnUrl` valeur dans la chaîne de requête comme suit (et si l’URL de retour est un chemin d’accès local), la page de connexion retournera les utilisateurs à cette page une fois, ils se connectent.

    Le code définit également  *\_SiteLayout.cshtml* page en tant que sa page de disposition. (Pour plus d’informations sur les pages de disposition, consultez [création d’une disposition cohérente dans les Sites ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=202891).)
5. Exécuter le site. Si vous êtes toujours connecté, cliquez sur le **déconnexion** bouton en haut de la page.
6. Dans le navigateur, demander la page */Members/MembersInformation*. Par exemple, l’URL peut ressembler à ceci :

    `http://localhost:38366/Members/MembersInformation`

    (Le numéro de port (38366) sera probablement différent dans l’URL.)

    Vous êtes redirigé vers la *Login.cshtml* page, car vous n’êtes pas connecté.
- Connectez-vous en utilisant le compte que vous avez créé précédemment. Vous êtes redirigé vers la *MembersInformation* page. Étant donné que vous êtes connecté, cette fois vous consultez le contenu de la page.

Pour sécuriser l’accès à plusieurs pages, vous pouvez effectuer ceci :

- Ajouter la vérification de sécurité à chaque page.
- Créer un  *\_PageStart.cshtml* page dans le dossier où vous conservez des pages protégées et ajoutez la vérification de la sécurité. Le  *\_PageStart.cshtml* page agit comme un type de page global pour toutes les pages dans le dossier. Cette technique est expliquée plus en détail dans [personnalisation du comportement au niveau du Site pour ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=202906#Using__PageStart.cshtml_to_Restrict_Folder_Access).

## <a name="creating-security-for-groups-of-users-roles"></a>Création de sécurité pour les groupes d’utilisateurs (rôles)

Si votre site comporte un grand nombre de membres, il n’est pas efficace pour vérifier les autorisations pour chaque utilisateur individuellement avant de leur permettre de voir une page. Ce que vous pouvez faire est de créer des groupes, ou *rôles*, que les membres individuels appartiennent à. Vous pouvez ensuite vérifier les autorisations basées sur le rôle. Dans cette section, vous allez créer un &quot;admin&quot; rôle, puis créer une page qui est accessible aux utilisateurs qui sont dans (qui appartient au) ce rôle.

Le système d’appartenance ASP.NET est configuré pour prendre en charge les rôles. Cependant, contrairement à l’inscription de l’appartenance et la connexion, le **Starter Site** modèle ne contient pas les pages qui vous aident à gérer les rôles. (La gestion des rôles sont une tâche administrative, plutôt qu’une tâche utilisateur.) Toutefois, vous pouvez ajouter des groupes directement dans la base de données d’appartenance dans WebMatrix.

1. Dans WebMatrix, cliquez sur le **bases de données** sélecteur d’espace de travail.
2. Dans le volet gauche, ouvrez le *StarterSite.sdf* ouverture d’un nœud, le **Tables** nœud, puis double-cliquez sur le *pages Web\_rôles* table.

    ![sécurité-appartenance-7](16-adding-security-and-membership/_static/image6.png)
3. Ajouter un rôle appelé &quot;admin&quot;. Le *RoleId* est renseigné automatiquement. (Il est la clé primaire et a été défini pour être un champ d’identité, comme expliqué dans [Introduction à l’utilisation d’une base de données dans les Sites ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=202893).)
4. Prenez note de ce qui est la valeur pour le *RoleId* champ. (S’il s’agit du premier rôle que vous définissez, il est égal à 1.)

    ![sécurité-appartenance-8](16-adding-security-and-membership/_static/image7.png)
5. Fermer le *pages Web\_rôles* table.
6. Ouvrez le *UserProfile* table.
7. Prenez note de la *UserId* valeur d’une ou plusieurs des utilisateurs dans la table et puis fermez la table.
8. Ouvrez le *pages Web\_UserInRoles* table et entrez un *UserID* et un *RoleID* valeur dans la table. Par exemple, pour placer l’utilisateur 2 dans la &quot;admin&quot; rôle, vous devez entrer des valeurs suivantes :

    ![sécurité-appartenance-9](16-adding-security-and-membership/_static/image8.png)
9. Fermer le *pages Web\_UsersInRoles* table.

    Maintenant que vous avez définis des rôles, vous pouvez configurer une page qui est accessible aux utilisateurs qui se trouvent dans ce rôle.
10. Dans le dossier racine du site Web, créez une nouvelle page nommée *AdminError.cshtml* et remplacer le contenu existant par le code suivant. Il s’agit de la page que les utilisateurs sont redirigés vers si elles ne sont pas autorisés à accéder à une page.

    [!code-cshtml[Main](16-adding-security-and-membership/samples/sample4.cshtml)]
11. Dans le dossier racine du site Web, créez une nouvelle page nommée *AdminOnly.cshtml* et remplacez le code existant par le code suivant :

    [!code-cshtml[Main](16-adding-security-and-membership/samples/sample5.cshtml)]

    Le `Roles.IsUserInRole` retourne de la méthode `true` si l’utilisateur actuel est membre du rôle spécifié (dans ce cas, le rôle « admin »).
12. Exécutez *Default.cshtml* dans un navigateur, mais ne pas se connecter. (Si vous êtes déjà connecté, déconnectez-vous.)
13. Dans la barre d’adresses du navigateur, ajoutez *AdminOnly* dans l’URL. (En d’autres termes, demander la *AdminOnly.cshtml* fichier.) Vous êtes redirigé vers la *AdminError.cshtml* page, car vous n’êtes pas actuellement connecté en tant qu’utilisateur dans le &quot;admin&quot; rôle.
14. Retour à *Default.cshtml* et connectez-vous en tant que l’utilisateur que vous avez ajouté à la &quot;admin&quot; rôle.
15. Accédez à *AdminOnly.cshtml* page. Cette fois, vous consultez la page.

## <a name="preventing-automated-programs-from-joining-your-website"></a>Empêcher les programmes automatiques de rejoindre votre site Web

La page de connexion s’arrête pas les programmes automatiques (parfois appelé *robots web* ou *robots*) auprès de votre site Web. Cette procédure décrit comment activer un test ReCaptcha pour la page d’inscription.

![/Media/38777/ch16securitymembership-18.jpg](16-adding-security-and-membership/_static/image1.jpg)

1. Inscrire votre site Web, consultez ReCaptcha.Net ([http://recaptcha.net](http://recaptcha.net)). Lorsque vous avez terminé l’inscription, vous obtenez une clé publique et une clé privée.
2. Ajoutez la bibliothèque de programmes d’assistance ASP.NET Web à votre site Web, comme décrit dans [programmes d’assistance de l’installation dans un Site de Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=252372), si vous n’avez pas encore.
3. Dans le *compte* dossier, ouvrez le fichier nommé *Register.cshtml*.
4. Dans le code en haut de la page, recherchez les lignes suivantes et les supprimer les commentaires en supprimant les `//` caractères de commentaire :

    [!code-csharp[Main](16-adding-security-and-membership/samples/sample6.cs)]
5. Remplacez `PRIVATE_KEY` par votre propre clé privée ReCaptcha.
6. Dans le balisage de la page, supprimez le `@*` et `*@` caractères de commentaire dans les lignes suivantes dans le balisage de page à partir de :

    [!code-cshtml[Main](16-adding-security-and-membership/samples/sample7.cshtml)]
7. Remplacez `PUBLIC_KEY` avec votre clé.
8. Si vous n’avez pas supprimé il déjà, supprimez le `<div>` élément qui contient le texte qui commence par « Pour activer la vérification CAPTCHA... ». (Supprimez la totalité de `<div>` élément et son contenu.)

1. Exécutez *Default.cshtml* dans un navigateur. Si vous êtes connecté au site, cliquez sur le **déconnexion** lien.
2. Cliquez sur le **inscrire** liez et l’inscription à l’aide du test CAPTCHA de test.

    ![sécurité-appartenance-10.](16-adding-security-and-membership/_static/image9.png)

Pour plus d’informations sur la `ReCaptcha` assistance, consultez [à un CATPCHA empêcher automatisée programmes (robots) à partir d’à l’aide de votre Site Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=251967).

<a id="Additional_Resources"></a>
## <a name="letting-users-log-in-using-an-external-site"></a>Permettant aux utilisateurs de se connecter à l’aide d’un Site externe

Le **Starter Site** modèle comprend le code et le balisage qui permet aux utilisateurs de se connecter à l’aide de Facebook, Windows Live, Twitter, Google ou Yahoo. Par défaut, cette fonctionnalité n’est pas activée. La procédure générale pour à l’aide de la définition par l’utilisateur, connectez-vous à l’aide de ces fournisseurs externes s’agit-il :

- Décider lequel vous souhaitez prendre en charge des sites externes.
- Si nécessaire, accédez à ce site et configurer une application de connexion. (Par exemple, vous devez procéder ainsi afin d’autoriser les connexions de Facebook.)
- Dans votre site, configurez le fournisseur. Dans la plupart des cas, il vous suffit de supprimer les commentaires du code dans le  *\_AppStart.cshtml* fichier.
- Ajoutez le balisage de la page d’inscription qui permet aux personnes lien vers le site externe pour la connexion. Vous pouvez généralement copier le balisage que vous avez besoin, modifiez le texte légèrement.

Vous trouverez des instructions pas à pas dans la rubrique [l’activation de la connexion à partir des Sites externes dans un Site de Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=251969).

Une fois un utilisateur se connecte à partir d’un autre site, l’utilisateur retourne à votre site et *associe* que la connexion avec votre site. En effet, cela crée une entrée de l’appartenance de votre site pour la connexion externe de l’utilisateur. Cela vous permet d’utiliser les fonctions normales d’appartenance (tels que les rôles) avec la connexion externe.

## <a name="adding-security-to-an-existing-website"></a>Ajout de la sécurité pour un site Web existant

La procédure décrite précédemment dans cet article s’appuie sur l’utilisation de la **Starter Site** modèle comme base pour la sécurité du site Web. Si elle n’est pas pratique pour vous permettre de démarrer à partir de la **Starter Site** modèle ou pour copier les pages concernées à partir d’un site basé sur ce modèle, vous pouvez implémenter le même type de sécurité de votre propre site en codant vous-même. Vous créez les mêmes types de pages : inscription, connexion et ainsi de suite, puis utilisez les classes et les programmes d’assistance pour définir l’appartenance à un.

Le processus de base est décrite dans le billet de blog [la façon la plus simple pour implémenter la sécurité de ASP.NET Razor](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2240). La plupart du travail est effectuée à l’aide les méthodes suivantes et les propriétés de la `WebSecurity` assistance :

- [WebSecurty.UserExists](https://msdn.microsoft.com/en-us/library/webmatrix.webdata.websecurity.userexists(v=vs.99).aspx), [WebSecurity.CreateUserAndAccount](https://msdn.microsoft.com/en-us/library/webmatrix.webdata.websecurity.createuserandaccount(v=vs.99).aspx). Ces méthodes vous permettent de déterminer si un utilisateur est déjà inscrit et pour les enregistrer.
- [WebSecurty.IsAuthenticated](https://msdn.microsoft.com/en-us/library/webmatrix.webdata.websecurity.isauthenticated(v=vs.99).aspx). Cette propriété vous permet de déterminer si l’utilisateur actuel est connecté. Cela est utile pour rediriger les utilisateurs vers une page de connexion si elles ne sont pas connectés.
- [WebSecurity.Login](https://msdn.microsoft.com/en-us/library/webmatrix.webdata.websecurity.login(v=vs.99).aspx), [WebSecurity.Logout](https://msdn.microsoft.com/en-us/library/webmatrix.webdata.websecurity.logout(v=vs.99).aspx). Ces méthodes connecter à un utilisateur ou out.
- [WebSecurity.CurrentUserName](https://msdn.microsoft.com/en-us/library/webmatrix.webdata.websecurity.currentusername(v=vs.99).aspx). Cette propriété est utile pour afficher le nom de connexion de l’utilisateur actuel (si l’utilisateur est connecté).
- [WebSecurity.ConfirmAccount](https://msdn.microsoft.com/en-us/library/gg569286(v=vs.99).aspx). Cette méthode est utile si vous configurez l’e-mail de confirmation pour l’inscription. (Détails sont décrits dans le billet de blog [à l’aide de la fonctionnalité de confirmation pour la sécurité des Pages Web ASP.NET](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2267).)

Pour gérer les rôles, vous pouvez utiliser la [rôles](https://msdn.microsoft.com/en-us/library/gg538398(v=vs.99).aspx) et [appartenance](https://msdn.microsoft.com/en-us/library/gg569035(v=vs.99).aspx) des classes, comme décrit dans l’entrée de blog.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Personnalisation du comportement de l’échelle du Site](https://go.microsoft.com/fwlink/?LinkId=202906)
- [Sécurisation des Communications Web : Les certificats SSL et https://](https://go.microsoft.com/fwlink/?LinkId=208660)
- [La façon la plus simple pour implémenter la sécurité de ASP.NET Razor](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2240) et [à l’aide de la fonctionnalité de confirmation pour la sécurité des Pages Web ASP.NET](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2267). Il s’agit des billets de blog qui décrivent comment implémenter les fonctionnalités d’appartenance ASP.NET sans utiliser le **Starter Site** modèle.
- [L’activation de la connexion à partir des Sites externes dans un Site de Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=251969)
- [Référence de l’API de classe WebSecurity](https://msdn.microsoft.com/en-us/library/webmatrix.webdata.websecurity(v=vs.99)) (MSDN)
- [Référence de l’API de classe SimpleRoleProvider](https://msdn.microsoft.com/en-us/library/webmatrix.webdata.simpleroleprovider(v=vs.99)) (MSDN)
- [Référence de l’API de classe SimpleMembershipProvider](https://msdn.microsoft.com/en-us/library/webmatrix.webdata.simplemembershipprovider(v=vs.99)) (MSDN)
