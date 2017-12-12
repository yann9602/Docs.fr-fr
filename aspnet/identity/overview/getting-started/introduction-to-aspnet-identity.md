---
uid: identity/overview/getting-started/introduction-to-aspnet-identity
title: "Introduction à ASP.NET Identity | Documents Microsoft"
author: jongalloway
description: "Le système d’appartenance ASP.NET a été introduit avec ASP.NET 2.0 différée depuis 2005 et puis de nombreuses modifications ont été dans le typicall d’applications méthodes web..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/17/2013
ms.topic: article
ms.assetid: 38717fc1-5989-43cf-952d-4007cc1dd923
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /identity/overview/getting-started/introduction-to-aspnet-identity
msc.type: authoredcontent
ms.openlocfilehash: a66e2a80668dbf291b9cc34f205b546b72d92bcc
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="introduction-to-aspnet-identity"></a>Introduction à l’identité ASP.NET
====================
par [Jon Galloway](https://github.com/jongalloway), [Pranav Rastogi](https://github.com/rustd), [Rick Anderson](https://github.com/Rick-Anderson), [Tom Dykstra](https://github.com/tdykstra)

> Le système d’appartenance ASP.NET a été introduit avec ASP.NET 2.0 différée depuis 2005 et puis de nombreuses modifications ont été comme les applications web gèrent généralement l’authentification et l’autorisation. Identité ASP.NET est une nouvelle détaillée de ce que le système d’appartenance doit être quand vous créez des applications modernes pour le web, un téléphone ou une tablette.
> 
> Cet article a été écrit par Pranav Rastogi ([@rustd](https://twitter.com/rustd)), Jon Galloway ([@jongalloway](https://twitter.com/jongalloway)), Tom Dykstra et Rick Anderson ([ @RickAndMSFT ](https://twitter.com/#!/RickAndMSFT) ).


## <a name="background-membership-in-aspnet"></a>Arrière-plan : L’appartenance dans ASP.NET

### <a name="aspnet-membership"></a>Appartenance d’ASP.NET

[L’appartenance ASP.NET](https://msdn.microsoft.com/en-us/library/yh26yfzy(v=VS.100).aspx) a été conçu pour répondre aux exigences l’appartenance au site qui étaient courantes dans 2005, impliquant l’authentification par formulaire et une base de données SQL Server pour les noms d’utilisateur, les mots de passe et les données de profil. Aujourd'hui, il est un tableau de beaucoup plus large d’options de stockage de données pour les applications web, la plupart des développeurs et activer ou leurs sites pour utiliser des fournisseurs d’identité sociaux pour la fonctionnalité d’authentification et d’autorisation. Les limitations de conception de l’appartenance d’ASP.NET compliquer cette transition :

- Le schéma de base de données a été conçu pour SQL Server et vous ne pouvez pas le modifier. Vous pouvez ajouter des informations de profil, mais les données supplémentaires sont placées dans une autre table, ce qui rend difficile pour l’accès par quelque moyen que sauf via l’API du fournisseur de profil.
- Le système du fournisseur permet de modifier le magasin de données de sauvegarde, mais le système est conçu autour des hypothèses appropriés pour une base de données relationnelle. Vous pouvez écrire un fournisseur pour stocker les informations d’appartenance dans un mécanisme de stockage non relationnelles, telles que les Tables de stockage Azure, mais vous devrez contourner la structure relationnelle en écrivant une grande quantité de code et un grand nombre de `System.NotImplementedException` exceptions pour les méthodes qui ne s’applique aux bases de données NoSQL.
- Étant donné que la fonctionnalité de déconnexion/journal est basée sur l’authentification par formulaire, le système d’appartenance ne peut pas utiliser [OWIN](../../../aspnet/overview/owin-and-katana/an-overview-of-project-katana.md). OWIN inclut les composants d’intergiciel (middleware) pour l’authentification, y compris la prise en charge des connexions à l’aide de fournisseurs d’identité externes (comme Google Accounts Microsoft, Facebook, Twitter) et les connexions à l’aide de comptes de société à partir de Active Directory sur site ou Azure Active Directory. OWIN prend également en charge OAuth 2.0, JSON et CORS.

### <a name="aspnet-simple-membership"></a>Appartenance d’ASP.NET Simple

[L’appartenance d’ASP.NET simple](../../../web-pages/overview/security/16-adding-security-and-membership.md) a été développé comme un système d’appartenance pour ASP.NET Web Pages. Il a été libéré avec WebMatrix et Visual Studio 2010 SP1. L’objectif de l’appartenance Simple a été pour le rendre facile d’ajouter des fonctionnalités d’appartenance à une application Web Pages.

L’appartenance simple facilitent personnaliser les informations de profil utilisateur, mais il porte toujours les autres problèmes avec l’appartenance d’ASP.NET, et il présente certaines limitations :

- Il était difficile de conserver les données du système d’appartenance dans un magasin non relationnelles.
- Vous ne pouvez pas l’utiliser avec OWIN.
- Il ne fonctionne pas correctement avec les fournisseurs d’appartenance d’ASP.NET existants, et il n’est pas extensible.

### <a name="aspnet-universal-providers"></a>Fournisseurs universels ASP.NET

[ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx) ont été développés pour rendre possible rendre persistantes les informations d’appartenance dans Microsoft Azure SQL Database et qu’ils fonctionnent également avec SQL Server Compact. Les fournisseurs universels sur Entity Framework Code First, ce qui signifie que les fournisseurs universels peuvent être utilisés pour conserver les données dans n’importe quel magasin pris en charge par EF ont été générés. Avec les fournisseurs universels, le schéma de base de données a été nettoyé de nombreuses également.

Les fournisseurs universels sont basées sur l’infrastructure de l’appartenance d’ASP.NET, afin qu’ils continueront à être les mêmes limitations que le fournisseur SqlMembership. Autrement dit, ils ont été conçus pour les bases de données relationnelles et il est difficile de personnaliser les informations de profil et d’utilisateur. Ces fournisseurs utilisent toujours l’authentification par formulaire pour la fonctionnalité de connexion et déconnexion.

## <a name="aspnet-identity"></a>ASP.NET Identity

Comme l’appartenance au récit dans ASP.NET a évolué au fil des années, l’équipe ASP.NET a appris beaucoup de commentaires des clients.

Partant du principe que les utilisateurs seront connecteront à entrer un nom d’utilisateur et un mot de passe qu’ils ont enregistrées dans votre application n’est plus valide. Le site web est devenue plus de réseaux sociaux. Les utilisateurs interagissent avec eux en temps réel par le biais des canaux de réseaux sociaux tels que Facebook, Twitter et autres sites web de réseaux sociaux. Les développeurs devront être en mesure de se connecter avec leurs identités sociales afin qu’ils peuvent avoir une riche expérience sur leurs sites web. Un système d’appartenance moderne doit activer le journal-ins en fonction de la redirection vers des fournisseurs d’authentification tels que Facebook, Twitter et d’autres.

À mesure que le développement web évolué, par conséquent, ne les modèles de développement web. Unité de code de l’application de test est devenu un problème de base pour les développeurs d’applications. ASP.NET a ajouté une nouvelle infrastructure basée sur le modèle Model-View-Controller (MVC), en partie pour aider les développeurs à générer des applications ASP.NET pouvant être testées unité en 2008. Les développeurs qui souhaitent unité tester leur logique d’application que vous voulez également être en mesure de procéder avec le système d’appartenance.

Compte tenu de ces modifications dans le développement d’applications web, ASP.NET Identity a été développé avec les objectifs suivants :

- **Un système d’identité ASP.NET**

    - ASP.NET Identity peut être utilisé avec toutes les infrastructures d’ASP.NET, tels que ASP.NET MVC, Web Forms, les Pages Web, API Web et SignalR.
    - Identité de ASP.NET peut être utilisée lorsque vous créez des applications web, téléphone, magasin ou hybride.
- **Facilité de brancher les données de profil sur l’utilisateur**

    - Vous contrôlez le schéma de l’utilisateur et les informations de profil. Par exemple, vous pouvez facilement activer le système pour stocker les dates de naissance entrées par les utilisateurs lorsqu’ils s’inscrivent à un compte dans votre application.

- **Contrôle de persistance**

    - Par défaut, le système d’identité ASP.NET stocke toutes les informations utilisateur dans une base de données. ASP.NET Identity utilise Entity Framework Code First pour implémenter tous son mécanisme de persistance.
    - Étant donné que vous contrôlez les schémas de base de données, des tâches courantes telles que la modification des noms de table ou la modification le type de données de clés primaires est simple à réaliser.
    - Il est facile d’intégrer dans différents mécanismes de stockage tels que SharePoint, Service de Table de stockage Azure, les bases de données NoSQL, etc., sans avoir à lever `System.NotImplementedExceptions` exceptions.
- **Capacité de test unitaire**

    - ASP.NET Identity rend les unités plus l’application web testables. Vous pouvez écrire des tests unitaires pour les parties de votre application qui utilisent ASP.NET Identity.
- **Fournisseur de rôles**

    - Il existe un fournisseur de rôles qui vous permet de restreindre l’accès aux parties de votre application par les rôles. Vous pouvez facilement créer des rôles, tels que « Admin » et ajouter des utilisateurs aux rôles.
- **Basée sur les revendications**

    - ASP.NET Identity prend en charge l’authentification basée sur les revendications, où l’identité de l’utilisateur est représentée comme un ensemble de revendications. Revendications permettent aux développeurs d’être beaucoup plus expressif dans la description de l’identité d’un utilisateur à autorisent les rôles. Alors que l’appartenance au rôle est simplement une valeur booléenne (membre ou non membres), une revendication peut inclure des informations détaillées sur l’identité et l’appartenance de l’utilisateur.
- **Fournisseurs de connexion sociale**

    - Vous pouvez facilement ajouter des connexions réseaux sociale tels que Microsoft Account, Facebook, Twitter, Google et d’autres à votre application et stocker les données spécifiques à l’utilisateur dans votre application.
- **Azure Active Directory**

    - Vous pouvez également ajouter des fonctionnalités d’ouverture de session à l’aide d’Azure Active Directory et stocker les données spécifiques à l’utilisateur dans votre application. Pour plus d’informations, consultez [comptes professionnels](../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#orgauth) dans la création de projets Web ASP.NET dans Visual Studio 2013
- **Intégration de OWIN**

    - Authentification ASP.NET repose désormais sur un intergiciel (middleware) OWIN qui peut être utilisé sur n’importe quel hôte OWIN. Identité ASP.NET n’a pas de dépendances sur System.Web. Il est une infrastructure OWIN entièrement conforme et peut être utilisé dans n’importe quelle application OWIN hébergé.
    - ASP.NET Identity utilise l’authentification OWIN de journal/journal sorti des utilisateurs dans le site web. Cela signifie qu’au lieu d’utiliser FormsAuthentication pour générer le cookie, l’application utilise OWIN CookieAuthentication à cet effet.
- **Package NuGet**

    - ASP.NET Identity est redistribué sous forme de package NuGet qui est installé dans les modèles ASP.NET MVC, Web Forms et API Web qui sont fournis avec Visual Studio 2013. Vous pouvez télécharger ce package NuGet à partir de la galerie NuGet.
    - Libération d’identité ASP.NET en tant qu’un NuGet package plus facilement l’équipe ASP.NET effectuer une itération sur les nouvelles fonctionnalités et des correctifs de bogues et fournir aux développeurs de façon flexible.

## <a name="getting-started-with-aspnet-identity"></a>Prise en main d’identité ASP.NET

Identité ASP.NET est utilisée dans les modèles de projet Visual Studio 2013 pour ASP.NET MVC, Web Forms, API Web et SPA. Dans cette procédure pas à pas, nous illustrent la façon dont les modèles de projet utilisent ASP.NET Identity pour ajouter des fonctionnalités pour vous inscrire, connectez-vous et déconnecter un utilisateur.

ASP.NET Identity est implémentée à l’aide de la procédure suivante. Cet article vise à vous donner une vue d’ensemble de haut niveau de l’identité de ASP.NET ; Vous pouvez suivre pas à pas, ou simplement lire les détails. Pour obtenir des instructions plus détaillées sur la création d’applications à l’aide d’ASP.NET Identity, y compris à l’aide de la nouvelle API pour ajouter des utilisateurs, rôles et les informations de profil, consultez la section étapes suivantes à la fin de cet article.

1. Créez une application ASP.NET MVC avec des comptes individuels. Vous pouvez utiliser ASP.NET Identity dans ASP.NET MVC, Web Forms, API Web, etc. de SignalR. Dans cet article, nous allons commencer avec une application ASP.NET MVC.  
  
    ![](introduction-to-aspnet-identity/_static/image1.png)
2. Le projet créé contient trois packages suivants pour ASP.NET Identity.

    - [`Microsoft.AspNet.Identity.EntityFramework`](http://www.nuget.org/packages/Microsoft.AspNet.Identity.EntityFramework/)  
 Ce package est l’implémentation d’Entity Framework d’identité ASP.NET qui rendront les données d’identité ASP.NET et le schéma de SQL Server.
    - [`Microsoft.AspNet.Identity.Core`](http://www.nuget.org/packages/Microsoft.AspNet.Identity.Core/)  
 Ce package a les interfaces principales pour ASP.NET Identity. Ce package peut être utilisé pour écrire une implémentation pour ASP.NET Identity persistance différent de cibles stocke telles que le stockage Table Azure, NoSQL bases de données etc.
    - [`Microsoft.AspNet.Identity.OWIN`](http://www.nuget.org/packages/Microsoft.AspNet.Identity.Owin/)  
 Ce package contient des fonctionnalités qui sont utilisée pour incorporer une authentification OWIN avec ASP.NET Identity dans les applications ASP.NET. Il est utilisé lorsque vous ajoutez un journal de fonctionnalités à votre application et un appel dans l’intergiciel (middleware) OWIN une authentification de Cookie pour générer un cookie.
3. Création d’un utilisateur.  
 Lancer l’application, puis cliquez sur le **inscrire** lien pour créer un utilisateur. L’illustration suivante montre la page d’inscription qui collecte le nom d’utilisateur et un mot de passe.  
  
    ![](introduction-to-aspnet-identity/_static/image2.png)  
  
 Lorsque l’utilisateur clique sur le **inscrire** bouton, le `Register` action du contrôleur de compte permet de créer l’utilisateur en appelant l’API d’identité ASP.NET, comme indiqué ci-dessous :

    [!code-csharp[Main](introduction-to-aspnet-identity/samples/sample1.cs?highlight=8-9)]
4. Se connecter.  
 Si l’utilisateur a été créé avec succès, elle est connecté par le `SignInAsync` (méthode).  

    [!code-csharp[Main](introduction-to-aspnet-identity/samples/sample2.cs?highlight=12)]

    [!code-csharp[Main](introduction-to-aspnet-identity/samples/sample3.cs?highlight=5-6)]

 Le code en surbrillance ci-dessus dans le `SignInAsync` méthode génère une [ClaimsIdentity](https://msdn.microsoft.com/en-us/library/system.security.claims.claimsidentity.aspx). Identité ASP.NET et authentification de Cookie OWIN étant système basé sur les revendications, le framework requiert l’application pour générer un ClaimsIdentity pour l’utilisateur. ClaimsIdentity fournit des informations sur toutes les revendications pour l’utilisateur, telles que les rôles de l’utilisateur appartient. Vous pouvez également ajouter plusieurs revendications de l’utilisateur à ce stade.  
  
 Le code en surbrillance ci-dessous dans le `SignInAsync` méthode se connecte l’utilisateur à l’aide de la classe AuthenticationManager de OWIN et en appelant `SignIn` et en passant le ClaimsIdentity.  

    [!code-csharp[Main](introduction-to-aspnet-identity/samples/sample4.cs?highlight=8-11)]
5. Fermez la session.  
 En cliquant sur le **session** lien appelle l’action de fermeture de session dans le contrôleur de compte. 

    [!code-csharp[Main](introduction-to-aspnet-identity/samples/sample5.cs?highlight=6)]

 La mise en surbrillance le code ci-dessus OWIN `AuthenticationManager.SignOut` (méthode). Cela est analogue à [FormsAuthentication.SignOut](https://msdn.microsoft.com/en-us/library/system.web.security.formsauthentication.signout.aspx) méthode utilisée par le [FormsAuthentication](https://msdn.microsoft.com/en-us/library/system.web.security.formsauthenticationmodule.aspx) module dans les formulaires Web.

## <a name="components-of-aspnet-identity"></a>Composants d’identité ASP.NET

Le diagramme ci-dessous illustre les composants du système d’identité ASP.NET (cliquez sur [cela](introduction-to-aspnet-identity/_static/image3.png) ou sur le diagramme pour l’agrandir). Les packages en vert constituent le système d’identité ASP.NET. Tous les packages sont des dépendances qui sont requis pour utiliser le système d’identité ASP.NET dans les applications ASP.NET.

[![](introduction-to-aspnet-identity/_static/image5.png)](introduction-to-aspnet-identity/_static/image4.png)

Voici une brève description des packages NuGet non mentionnés précédemment :

- [Microsoft.Owin.Security.Cookies](http://www.nuget.org/packages/Microsoft.Owin.Security.Cookies/)  
 Intergiciel (middleware) qui permet à une application utiliser un cookie basé similaire à ASP de l’authentification. Authentification de formulaires de NET.
- [EntityFramework](http://www.nuget.org/packages/EntityFramework/)  
 Entity Framework est la technologie d’accès aux données recommandée par Microsoft pour les bases de données relationnelles.

## <a name="migrating-from-membership-to-aspnet-identity"></a>Migration à partir de l’appartenance à l’identité ASP.NET

Nous espérons bientôt fournissent des conseils sur la migration de vos applications existantes qui utilisent l’appartenance d’ASP.NET ou l’appartenance Simple vers le nouveau système d’identité ASP.NET.

## <a name="next-steps"></a>Étapes suivantes

- [Créer une application ASP.NET MVC 5 avec Facebook et Google OAuth2 et authentification OpenID](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md)  
 Le didacticiel utilise l’API d’identité ASP.NET pour ajouter des informations de profil vers la base de données utilisateur et comment s’authentifier auprès de Google et Facebook.
- [Créer une application ASP.NET MVC avec l’authentification et de la base de données SQL et le déployer vers Azure App Service](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data)  
 Ce didacticiel montre comment utiliser l’API d’identité pour ajouter des utilisateurs et des rôles.
- [Comptes d’utilisateur individuels](../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#indauth) lors de la création des projets Web ASP.NET dans Visual Studio 2013
- [Comptes de société](../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#orgauth) lors de la création des projets Web ASP.NET dans Visual Studio 2013
- [Personnalisation des informations de profil dans ASP.NET Identity dans les modèles de VS 2013](https://blogs.msdn.com/b/webdev/archive/2013/10/16/customizing-profile-information-in-asp-net-identity-in-vs-2013-templates.aspx)
- [Obtenir des informations supplémentaires provenant de fournisseurs sociaux utilisés dans les modèles de projet Visual Studio 2013](https://blogs.msdn.com/b/webdev/archive/2013/10/16/get-more-information-from-social-providers-used-in-the-vs-2013-project-templates.aspx)
- [https://github.com/rustd/AspnetIdentitySample](https://github.com/rustd/AspnetIdentitySample)  
 Exemple d’application qui montre comment ajouter la prise en charge de l’utilisateur et les rôles de base et comment effectuer des rôles et gestion des utilisateurs.
