---
uid: identity/overview/getting-started/aspnet-identity-recommended-resources
title: "Ressources recommandé d’identité ASP.NET | Documents Microsoft"
author: Rick-Anderson
description: "Cette rubrique fournit des liens vers des ressources de documentation sur l’utilisation d’identité ASP.NET. Si vous connaissez un excellent billet de blog, stackoverflow thread ou n’importe quel autre lin..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/09/2015
ms.topic: article
ms.assetid: 0f78aec2-f509-46fa-b20f-d5208425d8ec
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /identity/overview/getting-started/aspnet-identity-recommended-resources
msc.type: authoredcontent
ms.openlocfilehash: 8050d37cfea91701e1cae0413feb41ca9a68baff
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-identity-recommended-resources"></a>Ressources recommandé d’identité ASP.NET
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> Cette rubrique fournit des liens vers des ressources de documentation sur l’utilisation d’identité ASP.NET.
> 
> Si vous connaissez un excellent blog post, [stackoverflow](http://stackoverflow.com) thread ou un autre lien pouvant être utile, [nous envoyer un courrier électronique](mailto:aspnetue@microsoft.com?subject=Identity recommended resources) avec le lien ou simplement laisser un message au bas de cette page.


- [Prise en main d’identité ASP.NET](#gettingstarted)
- [Nouveaux articles doit la lecture proposées](#feat)
- [Les intermédiaires d’identité ASP.NET](#adv)
- [Vidéos](#video)
- [Où poser des questions, demande de fonctionnalités, signaler un bogue et les builds nocturnes](#samp)
- [Billets de blog sur l’identité](#blog)
- [Fournisseurs de stockage personnalisés pour ASP.NET Identity](#cust)
- [Ressources supplémentaires Identity](#additional)
- [Q &amp; un (questions/réponses)](#qand)

<a id="gettingstarted"></a>
## <a name="getting-started-with-aspnet-identity"></a>Prise en main d’identité ASP.NET

- [Application MVC est 5 avec Facebook, Twitter, LinkedIn et d’authentification Google OAuth2](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) ce didacticiel vous montre comment écrire une application ASP.NET MVC 5 avec autorisation Facebook et Google OAuth 2. Il montre également comment ajouter des données supplémentaires à la base de données d’identité.
- [Déployer une application sécurisée ASP.NET MVC avec base de données SQL, OAuth et l’appartenance à un Azure](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data). Ce didacticiel ajoute le déploiement d’Azure, la sécurisation de votre application avec les rôles, l’utilisation de l’API d’appartenance pour ajouter des utilisateurs et des rôles et des fonctionnalités de sécurité supplémentaires.
- [Introduction à l’identité ASP.NET](introduction-to-aspnet-identity.md)
- [Créer une application de web ASP.NET MVC 5 sécurisée avec se connectent, réinitialisation de confirmation et le mot de passe de messagerie](../../../mvc/overview/security/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset.md)
- [Application ASP.NET MVC 5 avec SMS et par courrier électronique d’authentification à deux facteurs](../../../mvc/overview/security/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication.md)

<a id="feat"></a>
## <a name="new-featured-must-read-articles"></a>Nouveaux articles doit la lecture proposées

- [Procédure pas à pas : Identité de ASP.NET MVC avec l’authentification de compte Microsoft](http://www.benday.com/2014/02/25/walkthrough-asp-net-mvc-identity-with-microsoft-account-authentication/) par [Benjamin Day](http://www.benday.com/about/)
- [Modèles d’identité ASP.NET Identity 2.0 extension et à l’aide des clés de type entier au lieu de chaînes](http://typecastexception.com/post/2014/07/13/ASPNET-Identity-20-Extending-Identity-Models-and-Using-Integer-Keys-Instead-of-Strings.aspx)
- [Authentification du jeton AngularJS à l’aide d’ASP.NET Web API 2, Owin et identité](http://bitoftech.net/2014/06/09/angularjs-token-authentication-using-asp-net-web-api-2-owin-asp-net-identity/)
- [Thinktecture.IdentityManager en remplacement de la WSAT](http://www.hanselman.com/blog/ThinktectureIdentityManagerAsAReplacementForTheASPNETWebSiteAdministrationTool.aspx)
- [ASP.NET Identity 2.0 : Personnalisation des relations entre les utilisateurs et les rôles](http://typecastexception.com/post/2014/06/22/ASPNET-Identity-20-Customizing-Users-and-Roles.aspx)

<a id="adv"></a>
## <a name="intermediate-aspnet-identity"></a>Les intermédiaires d’identité ASP.NET

- [Confirmation du compte et la récupération de mot de passe avec l’identité de ASP.NET](../features-api/account-confirmation-and-password-recovery-with-aspnet-identity.md)
- [Authentification à deux facteurs avec l’identité de ASP.NET à l’aide de SMS et par courrier électronique](../features-api/two-factor-authentication-using-sms-and-email-with-aspnet-identity.md)
- [Migration d’un site Web existant à partir de l’appartenance SQL pour ASP.NET Identity](../migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity.md)
- [Projet de formulaires Ajout ASP.NET Identity, à un site Web vide ou existant](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project.md)
- MSDN Magazine [d’authentification externe avec ASP.NET Identity](https://msdn.microsoft.com/en-us/magazine/dn745860.aspx) par Dino Esposito
- MSDN Magazine[examine d’abord ASP.NET Identity](https://msdn.microsoft.com/en-us/magazine/dn605872.aspx) par Dino Esposito
- [Identité ASP.NET : verrouillage de l’utilisateur](http://tech.trailmax.info/2014/06/asp-net-identity-user-lockout/)

<a id="samp"></a>
## <a name="where-to-ask-questions-request-features-report-a-bug-and-nightly-builds"></a>Où poser des questions, demande de fonctionnalités, signaler un bogue et les builds nocturnes

- Pour le dépassement de capacité, utilisez la balise [aspnet-identity](http://stackoverflow.com/questions/tagged/asp.net-identity)
- Pour les forums ASP.NET, publiez sur le [Forum sur la sécurité](https://forums.asp.net/25.aspx) et ajoutez **ASP.NET Identity** au titre.
- [ASP.NET Identity sur GitHub](https://github.com/aspnet/AspNetIdentity) builds nocturnes Get, des fonctionnalités de demande, de bogues ouverts.

<a id="blog"></a>
## <a name="blog-posts-on-identity"></a>Billets de blog sur l’identité

- [Qu’est un SecurityStamp dans ASP.NET Identity ?](http://stackoverflow.com/questions/19487322/what-is-asp-net-identitys-iusersecuritystampstoretuser-interface/19505060#19505060)
- Par [John Atten](https://twitter.com/xivSolutions)

    - [Modèles d’identité ASP.NET Identity 2.0 extension et à l’aide des clés de type entier au lieu de chaînes](http://typecastexception.com/post/2014/07/13/ASPNET-Identity-20-Extending-Identity-Models-and-Using-Integer-Keys-Instead-of-Strings.aspx)
    - [ASP.NET Identity 2.0 : Personnalisation des relations entre les utilisateurs et les rôles](http://typecastexception.com/post/2014/06/22/ASPNET-Identity-20-Customizing-Users-and-Roles.aspx)
    - [ASP.NET MVC et identité 2.0 : comprendre les principes de base](http://typecastexception.com/post/2014/04/20/ASPNET-MVC-and-Identity-20-Understanding-the-Basics.aspx)
    - [Configuration de la Validation du compte et d’autorisation de deux facteurs](http://typecastexception.com/post/2014/04/20/ASPNET-Identity-20-Setting-Up-Account-Validation-and-Two-Factor-Authorization.aspx)
    - [Configuration de connexion de base de données et de Migration de Code en premier pour les comptes d’identité dans ASP.NET MVC 5 et Visual Studio 2013](http://typecastexception.com/post/2013/10/27/Configuring-Db-Connection-and-Code-First-Migration-for-Identity-Accounts-in-ASPNET-MVC-5-and-Visual-Studio-2013.aspx)
- Par [Taiseer Joudeh](http://bitoftech.net/taiseer-joudeh-blog/)

    - [Jeton l’authentification à l’aide d’ASP.NET Web API 2 et ASP.NET Identity Owin intergiciel (middleware)](http://bitoftech.net/2014/06/01/token-based-authentication-asp-net-web-api-2-owin-asp-net-identity/)
    - [Authentification du jeton AngularJS à l’aide d’ASP.NET Web API 2, Owin et identité](http://bitoftech.net/2014/06/09/angularjs-token-authentication-using-asp-net-web-api-2-owin-asp-net-identity/)
    - [Activer des jetons d’actualisation OAuth dans l’application AngularJS à l’aide des API Web ASP .NET 2 et Owin – partie 3.](http://bitoftech.net/2014/07/16/enable-oauth-refresh-tokens-angularjs-app-using-asp-net-web-api-2-owin/)
- Par [Anders Abel](https://twitter.com/anders_abel)

    - [Compréhension du Pipeline de l’authentification Owin externe](http://coding.abel.nu/2014/06/understanding-the-owin-external-authentication-pipeline/)
    - [ASP.NET Identity et vue d’ensemble Owin](http://coding.abel.nu/2014/06/asp-net-identity-and-owin-overview/)

 Par [K. Scott Allen](https://twitter.com/OdeToCode) sur Ode au Code

    - [ASP.NET Core Identity](http://odetocode.com/blogs/scott/archive/2013/11/25/asp-net-core-identity.aspx) ce billet de blog examine les abstractions principales, y compris IUser, IUserStore et le\*stocker des interfaces.
    - [Identité ASP.NET avec Entity Framework](http://odetocode.com/blogs/scott/archive/2014/01/03/asp-net-identity-with-the-entity-framework.aspx) dans les applications MVC 5, API Web et SPA, les chaînes de connexion et les contextes de la gestion des comptes d’utilisateur individuels
    - [Options de personnalisation avec l’identité de ASP.NET](http://odetocode.com/blogs/scott/archive/2014/01/09/customization-options-with-asp-net-identity.aspx)
    - [Implémentation d’identité ASP.NET](http://odetocode.com/blogs/scott/archive/2014/01/20/implementing-asp-net-identity.aspx)
- [Benjamin Day](http://www.benday.com/about/)[procédure pas à pas : identité de ASP.NET MVC avec l’authentification de compte Microsoft](http://www.benday.com/2014/02/25/walkthrough-asp-net-mvc-identity-with-microsoft-account-authentication/)
- [Brock Allen](https://twitter.com/BrockLAllen)

    - [Notions fondamentales sur les fournisseurs de connexion externe (connexions sociales) avec un intergiciel d’authentification OWIN/Katana](http://brockallen.com/2014/01/09/a-primer-on-external-login-providers-social-logins-with-owinkatana-authentication-middleware/)
    - [Présentation de IdentityReboot](http://brockallen.com/2014/02/11/introducing-identityreboot/): un ensemble d’extensions qui implémentent les principales fonctionnalités manquantes, j’ai se pour ASP.NET Identity.
- [Pranav Rastogi](https://twitter.com/rustd)

    - [Obtenir plus d’informations à partir de réseaux sociaux fournisseurs](https://blogs.msdn.com/b/webdev/archive/2013/10/16/get-more-information-from-social-providers-used-in-the-vs-2013-project-templates.aspx)
- [@beabigrockstar](https://twitter.com/beabigrockstar)(Jerrie Pelser)

    - [Authentification à 2 facteurs](http://www.beabigrockstar.com/blog/2-factor-authentication-with-asp-net-identity-2-0-beta-1/)
    - [À l’aide de Google Authenticator avec l’identité de ASP.NET](http://www.beabigrockstar.com/blog/using-google-authenticator-asp-net-identity/)
    - [Guide de l’authentification ASP.NET MVC 5](http://www.beabigrockstar.com/)
- [Obtenir des informations supplémentaires provenant de fournisseurs sociaux utilisés dans les modèles de projet Visual Studio 2013](https://blogs.msdn.com/b/webdev/archive/2013/10/16/get-more-information-from-social-providers-used-in-the-vs-2013-project-templates.aspx)
- [Création d’une simple application ToDo avec ASP.NET Identity et l’association d’utilisateurs avec ToDoes](https://blogs.msdn.com/b/webdev/archive/2013/10/20/building-a-simple-todo-application-with-asp-net-identity-and-associating-users-with-todoes.aspx)
- [Problèmes d’intégration de Google OpenId avec ASP.NET Identity](http://blog.technovert.com/2014/01/google-openid-integration-issues-asp-net-identity/) si vous obtenez l’erreur : HTTP erreur 404.15-introuvable sur le module de filtrage des demandes est configuré pour refuser une demande dans laquelle la chaîne de requête est trop longue
- [Thinktecture.IdentityManager en remplacement de la WSAT](http://www.hanselman.com/blog/ThinktectureIdentityManagerAsAReplacementForTheASPNETWebSiteAdministrationTool.aspx)
- [Authentification du jeton AngularJS à l’aide d’ASP.NET Web API 2, Owin et identité](http://bitoftech.net/2014/06/09/angularjs-token-authentication-using-asp-net-web-api-2-owin-asp-net-identity/)
- [Base d’identité Asp.net simple sans Entity Framework](https://code.msdn.microsoft.com/Simple-Aspnet-Identiy-Core-7475a961)
- [Utilisation des rôles dans ASP.NET Identity pour MVC](http://www.dotnetfunda.com/articles/show/2898/working-with-roles-in-aspnet-identity-for-mvc) par [Sheo Narayan](http://www.dotnetfunda.com/profile/sheonarayan.aspx)
- [Déplacement vers ASP.NET Identity à partir de l’appartenance ASP.NET](http://webdojo.sharepoint.com/ajmatthews/_layouts/15/start.aspx#/Lists/Posts/Post.aspx?ID=2) par Alistair Matthews

<a id="video"></a>
## <a name="videos"></a>Vidéos

- Channel 9 [sécurisation des Applications ASP.NET et Services : mise en forme de sécurité pour les Applications modernes](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B421#fbid=PhVT9E1WRtr?hashlink=fbid) par Ido Flatow
- Channel 9 [introduction d’identité ASP.NET](https://channel9.msdn.com/Events/dotnetConf/2014/ASP-NET-Identity-Security) par Pranav Rastogi
- Channel 9 [authentification ASP.NET à l’aide d’ASP.NET Identity](https://channel9.msdn.com/Shows/Web+Camps+TV/Special-Movember-Episode-ASPNET-Authentication-Provider) par Cory Fowler
- Channel 9 [générer des applications Web modernes : ASP.NET Identity](https://channel9.msdn.com/Series/Building-Modern-Web-Apps/03) par Jeff Koch
- Channel 9 [sécurisation de votre site Web avec ASP.NET Identity](https://channel9.msdn.com/Events/TechDays/Techdays-2014-the-Netherlands/Securing-your-website-with-ASP-NET-Identity) par Alex Thissen
- [Utiliser l’identité de ASP.NET sur un modèle de base de données existant](https://www.youtube.com/watch?v=elfqejow5hM) par Alexander Schmidt
- [ASP.NET une identité](https://www.youtube.com/watch?v=w8GD-QIusKk) par Ivaylo Kenov de Telerik
- [Tchèque ASP.NET Identity](https://www.youtube.com/watch?v=tVbZp5brcpY) dans ce cours, nous allons montrer comment déployer l’authentification de base ajouter la prise en charge des fournisseurs d’identité externes tels que Twitter ou Facebook et comment utiliser les mots de passe à usage unique (OTP). [ASP.NET Identity JD nástupce appartenance d’un fournisseur de rôle &#367; v ASP.NET, tedy knihovna pro zajišt &#283; ní autentizace uživatel &#367;. V této p &#345; ednášce si ukážeme, jak nasad]

<a id="cust"></a>
## <a name="custom-storage-providers-for-aspnet-identity"></a>Fournisseurs de stockage personnalisés pour ASP.NET Identity

Si vous souhaitez écrire votre propre fournisseur, consultez [vue d’ensemble de fournisseurs de stockage personnalisé pour ASP.NET Identity](../extensibility/overview-of-custom-storage-providers-for-aspnet-identity.md) et [implémentation ASP.NET Identity](http://odetocode.com/blogs/scott/archive/2014/01/20/implementing-asp-net-identity.aspx) et examinez la source de l’un des projets figurant dans les systèmes d’exploitation ci-dessous.

- Didacticiel : [vue d’ensemble des fournisseurs de stockage personnalisés pour ASP.NET Identity](../extensibility/overview-of-custom-storage-providers-for-aspnet-identity.md) par Tom FitzMacken
- Blog : [implémentation d’identité ASP.NET](http://odetocode.com/blogs/scott/archive/2014/01/20/implementing-asp-net-identity.aspx)
- Didacticiel :[configurer les comptes d’identité base et les vers une base de données externe](http://typecastexception.com/post/2013/10/27/Configuring-Db-Connection-and-Code-First-Migration-for-Identity-Accounts-in-ASPNET-MVC-5-and-Visual-Studio-2013.aspx). Par [ @xivSolutions ](https://twitter.com/xivSolutions).
- Didacticiel[: implémentation d’un fournisseur de stockage d’identité ASP.NET MySQL personnalisé](../extensibility/implementing-a-custom-mysql-aspnet-identity-storage-provider.md)
- [Les entités CodeFluent](http://blog.codefluententities.com/2014/04/30/asp-net-identity-v2-and-codefluent-entities/) par [SoftFluent](http://www.softfluent.com/)
- [Stockage de Table Azure](https://www.nuget.org/packages/accidentalfish.aspnet.identity.azure/) par James Randall.
- Stockage de Table Azure : [AspNet.Identity.TableStorage](https://github.com/stuartleeks/leeksnet.AspNet.Identity.TableStorage) par [ @stuartleeks ](https://twitter.com/stuartleeks).
- [CouchDB / Cloudant par Michel Wertheim.](https://github.com/danielwertheim/mycouch.aspnet.identity)
- Haîne élastique[h : identité élastique](https://github.com/bmbsqd/elastic-identity) par AB de Bombsquad.
- [MongoDB](http://www.nuget.org/packages/MongoDB.AspNet.Identity/) par Jonathan Sheely Jonathan Sheely.
- [NHibernate.AspNet.Identity](https://github.com/milesibastos/NHibernate.AspNet.Identity) par Antônio Milesi Bastos.
- [RavenDB](http://www.nuget.org/packages/AspNet.Identity.RavenDB/1.0.0) par [ @tourismgeek ](https://twitter.com/tourismgeek).
- [RavenDB.AspNet.Identity](https://github.com/ILMServices/RavenDB.AspNet.Identity) par [ILMServices](http://www.ilmservice.com/).
- Redis : [Redis.AspNet.Identity](https://github.com/aminjam/Redis.AspNet.Identity)
- Modèles T4 EF de générer du code pour un magasin d’utilisateurs « de base de données tout d’abord » : [AspNet.Identity.EntityFramework](https://github.com/cbfrank/AspNet.Identity.EntityFramework)

<a id="additional"></a>
## <a name="additional-aspnet-identity-resources"></a>Ressources supplémentaires ASP.NET Identity

- [Présentation des fournisseurs de sécurité Yahoo et LinkedIn OAuth pour OWIN](http://blog.beabigrockstar.com/introducing-the-yahoo-linkedin-oauth-security-providers-for-owin/) par Jerrie Pelser pour obtenir des instructions Yahoo et LinkedIn.

<a id="qand"></a>
## <a name="qampa-questionanswer"></a>Q&amp;un (questions/réponses)

- Verrouillé q : les utilisateurs qui ont activé « mémoriser » (sans devoir passer par 2FA sur cet ordinateur/navigateur) ne sont pas verrouillés. Pourquoi et comment empêcher que ? Réponse [ici](http://stackoverflow.com/questions/24312247/locked-out-users-can-login-if-they-have-auth-cookie).
- **Q**: Comment puis-je stocker des revendications personnalisées, telles que le nom d’utilisateur réel, dans le cookie d’identité ASP.NET pour éviter des requêtes de base de données inutiles sur chaque demande. Réponse [ici](http://stackoverflow.com/questions/23622047/identity-cookie-loses-custom-claim-information-after-a-period-of-time).
- **Mise à jour le hachage de mot de passe d’AspNetUser q :**: j’ai 2 projets. Un d’eux est à l’aide de l’authentification ASP.NET, l’autre utilise l’authentification Windows, qui est la partie de l’administration. Je veux le projet de l’administrateur pour pouvoir gérer les utilisateurs de l’autre. Je peux modifier tout sauf le mot de passe. [Répondre ici](http://stackoverflow.com/questions/23880666/updating-aspnetuser-password-hash).
- **Q**: Comment puis-je réinitialiser un mot de passe en tant qu’un administrateur pour d’autres utilisateurs ? Réponse [ici](http://stackoverflow.com/questions/23783249/identity-2-0-reset-password-by-admin/24211766#24211766).
- **Q**: puis-je modifier le nom affiché du champ nom d’utilisateur dans ASP.NET MVC IdentityUser ? Réponse [ici](http://stackoverflow.com/questions/23256650/can-i-change-the-displayed-name-of-the-username-field-in-asp-net-mvc-identityuse).
- **Q**: comment pouvez contrat aux utilisateurs des autorisations pour ajouter d’autres utilisateurs à certains rôles ? Réponse [ici](http://stackoverflow.com/questions/23695373/allow-users-to-grant-permissions-to-other-users-for-their-account-in-asp-net-ide).
- **Q**: le stockage des informations de profil dans la table AspNetUsers par rapport à la table AspNetUserClaims. Réponse [ici](http://stackoverflow.com/questions/23215727/is-there-any-benefit-to-storing-user-information-in-aspnetuserclaims-with-asp-ne).
- **Q**: Mémoriser mes informations lors de l’utilisation d’un fournisseur d’authentification externe. Réponse [ici](http://stackoverflow.com/questions/23180896/how-to-remember-the-login-in-mvc5-when-an-external-provider-is-used).
- **Q**: pourquoi chaque demande requiert une ApplicationDBContext, n’est pas que trop de surcharge ?. Réponse non, la surcharge est faible.
- Q : comment obtenir la liste des utilisateurs connectés ? Réponse [ici](http://stackoverflow.com/questions/22995653/getting-a-list-of-logged-in-users-in-asp-net-identity/).
- Q : Comment puis-je détecter quand un utilisateur se connecte avec Microsoft.AspNet.Identity ? Réponse [ici](http://stackoverflow.com/questions/22956486/how-can-i-detect-when-a-user-logs-in-with-microsoft-aspnet-identity/22970698#22970698).
- Q : comment obtenir des messages d’erreur localisés pour l’identité ? Réponse [ici](http://stackoverflow.com/questions/22835981/asp-net-identity-localization-publickeytoken/22845864#22845864).
- Q : comment pour configurer le CookieMiddleware pour obtenir des revendications actualisées toutes les 30 minutes ? Réponse [ici](http://stackoverflow.com/questions/22682663/how-to-hold-the-cookies-claims-updated-with-mcv5-owin/22796932#22796932).
- Q : comment modifier les revendications pour l’utilisateur une fois qu’ils ont connecté ? Réponse [ici](http://stackoverflow.com/questions/22768836/can-i-modify-claims-in-asp-net-identity-with-owin-after-calling-signin/22769963#22769963).
- Q : comment pour invalider les jetons de sécurité ? Réponse [ici](http://stackoverflow.com/questions/22755700/revoke-token-generated-by-usertokenprovider-in-asp-net-identity-2-0/22767286#22767286).
- Q : comment ce magasin les revendications dans le middleware du cookie ? Réponse [ici](http://stackoverflow.com/questions/22320632/storing-retrieving-user-data-without-database-when-using-owin-cookie-authenticat/22541856#22541856).
- Q : je souhaite disposer d’un code confidentiel ou une vérification sur chaque méthode d’action dans mon application MVC de la sécurité, mais j’aimerais stocker la réussite des utilisateurs afin qu’ils ne puissent entrer le code confidentiel à chaque demande à cette méthode d’action. Réponse [ici](http://stackoverflow.com/questions/22479958/security-check-an-user-to-a-access-controller-action/22486075#22486075).
- Q : je souhaite utiliser pour enregistrer l’adresse de messagerie retourné à partir d’un fournisseur de réseaux sociaux pour la base de données, comment procéder ? Réponse [ici](http://stackoverflow.com/questions/22888397/save-claims-to-db-on-login/22970969#22970969):
- Q : Comment puis-je détecter lorsqu’un utilisateur connecte à la fois avec/sans en un cookie « mémoriser » ? Réponse [ici](http://stackoverflow.com/questions/22956486/how-can-i-detect-when-a-user-logs-in-with-microsoft-aspnet-identity/22970698#22970698).
- Q : puis-je modifier les revendications d’identité ASP.NET avec OWIN après l’appel d’ouverture de session ? Réponse : Connexion de l’appel est exactement ce que vous êtes censé pour faire lorsque vous souhaitez modifier les revendications pour l’utilisateur. En fait, elle force le ClaimsIdentity à sérialiser dans le cookie, c’est pourquoi vous voyez la nouvelle revendication n’apparaissent dans les demandes suivantes.
