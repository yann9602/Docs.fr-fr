---
uid: identity/overview/getting-started/adding-aspnet-identity-to-an-empty-or-existing-web-forms-project
title: "Ajout d’identité ASP.NET à un site Web vide ou existant Forms projet | Documents Microsoft"
author: raquelsa
description: "Ce didacticiel vous montre comment ajouter l’identité de ASP.NET (le nouveau système d’appartenance pour ASP.NET) à une application ASP.NET. Lorsque vous créez un nouveau Web Forms ou MVC..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/23/2013
ms.topic: article
ms.assetid: 1cbc0ed2-5bd6-4b62-8d34-4c193dcd8b25
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /identity/overview/getting-started/adding-aspnet-identity-to-an-empty-or-existing-web-forms-project
msc.type: authoredcontent
ms.openlocfilehash: 3ab67b93a32106c0b79f9e8d739d47835391edb5
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="adding-aspnet-identity-to-an-empty-or-existing-web-forms-project"></a>Projet de formulaires Ajout ASP.NET Identity, à un site Web vide ou existant
====================
par [Raquel Soares De Almeida](https://github.com/raquelsa)

> Ce didacticiel vous montre comment ajouter [ASP.NET Identity](introduction-to-aspnet-identity.md) (le nouveau système d’appartenance pour ASP.NET) à une application ASP.NET.
> 
> Lorsque vous créez un nouveau projet Web Forms ou MVC dans Visual Studio 2013 RTM avec des comptes individuels, Visual Studio installera tous les packages requis et ajouter toutes les classes nécessaires pour vous. Ce didacticiel illustre les étapes pour ajouter la prise en charge ASP.NET Identity à votre projet Web Forms existant ou un nouveau projet vide. J’ai présentent tous les packages NuGet que vous devez installer et vous devez ajouter des classes. Je passe en revue les exemples de formulaires Web pour l’inscription de nouveaux utilisateurs et la journalisation lors de la mise en surbrillance toutes les API de point d’entrée principal pour l’authentification et la gestion des utilisateurs. Cet exemple utilise l’implémentation par défaut de ASP.NET Identity pour le stockage de données SQL qui est basé sur Entity Framework. Ce didacticiel, nous allons utiliser la base de données locale pour la base de données SQL.
> 
> Ce didacticiel a été écrit par Raquel Soares De Almeida et Rick Anderson ( [ @RickAndMSFT ](https://twitter.com/#!/RickAndMSFT) ).


## <a name="getting-started-aspnet-identity"></a>Mise en route ASP.NET Identity

1. Commencez par installer et exécuter [Visual Studio Express 2013 pour le Web](https://go.microsoft.com/fwlink/?LinkId=299058) ou [Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566).
2. Cliquez sur **nouveau projet** à partir du début page, ou vous pouvez utiliser le menu et sélectionnez **fichier**, puis **nouveau projet**.
3. Sélectionnez **Visual C# i** n le volet gauche, puis **Web** , puis sélectionnez **Application Web ASP.NET**. Nommez votre projet « WebFormsIdentity », puis **OK**.   
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image1.png)
4. Dans le **nouveau projet ASP.NET** boîte de dialogue, sélectionnez le **vide** modèle.  
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image2.png)  
  
 Notez le **modifier l’authentification** bouton est désactivé et aucune prise en charge de l’authentification n’est fourni dans ce modèle. Les modèles Web Forms, MVC et API Web vous permettent de vous permet de sélectionner la méthode d’authentification. Pour plus d’informations, consultez [vue d’ensemble de l’authentification](../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#auth) .

## <a name="adding-identity-packages-to-your-app"></a>Ajout de Packages de l’identité à votre application

Dans l’Explorateur de solutions, cliquez sur votre projet et sélectionnez **gérer les Packages NuGet**. Dans la fenêtre de zone de texte Rechercher, tapez «*Identity.E*». Cliquez sur Installer pour ce package.   
  
![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image3.png)  
  
Notez que ce package installe les packages de dépendance : EntityFramework et Microsoft ASP.NET Identity Core.

## <a name="adding-web-forms-to-register-users"></a>Ajout de Web Forms pour inscrire des utilisateurs

1. Dans **l’Explorateur de solutions**, avec le bouton droit de votre projet, cliquez sur **ajouter**, puis **Web Form**.  
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image4.png)
2. Dans le **spécifier le nom de l’élément** boîte de dialogue, le nom du nouveau formulaire web **inscrire**, puis cliquez sur **OK**
3. Remplacez la balise dans le texte généré *Register.aspx* fichier avec le code ci-dessous. Les modifications de code sont mis en surbrillance.   

    [!code-aspx[Main](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/samples/sample1.aspx?highlight=9,12-40)]

    > [!NOTE]
    > Il s’agit simplement d’une version simplifiée de la *Register.aspx* fichier créé lorsque vous créez un nouveau projet ASP.NET Web Forms. Le balisage ci-dessus ajoute des champs de formulaire et un bouton pour inscrire un nouvel utilisateur.
4. Ouvrez le *Register.aspx.cs* de fichier et remplacez le contenu du fichier par le code suivant :

    [!code-csharp[Main](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/samples/sample2.cs)]

    > [!NOTE] 
    > 
    > 1. Le code ci-dessus est une version simplifiée de la *Register.aspx.cs* fichier créé lorsque vous créez un nouveau projet ASP.NET Web Forms.
    > 2. Le *IdentityUser* classe est l’implémentation EntityFramework par défaut de la *IUser* interface. *IUser* interface est l’interface minimale pour un utilisateur sur ASP.NET Identity Core.
    > 3. Le *UserStore* classe est l’implémentation EntityFramework par défaut d’un magasin d’utilisateurs. Cette classe implémente des interfaces minimales du cœur identité ASP.NET : *IUserStore*, *IUserLoginStore*, *IUserClaimStore* et *IUserRoleStore* .
    > 4. Le *UserManager* classe expose l’API qui enregistrera automatiquement les modifications associées à la *UserStore*.
    > 5. Le *IdentityResult* classe représente le résultat d’une opération d’identité.
5. Dans **l’Explorateur de solutions**, avec le bouton droit de votre projet, cliquez sur **ajouter**, **ajouter le dossier ASP.NET** , puis **application\_données**.  
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image5.png)
6. Ouvrez le *Web.config* et ajoutez une entrée de chaîne de connexion pour la base de données, nous allons utiliser pour stocker les informations utilisateur. La base de données sera créée lors de l’exécution par EntityFramework pour les entités d’identité. La chaîne de connexion est similaire à celui créé pour vous lorsque vous créez un projet Web Forms. Le code en surbrillance montre le balisage que vous devez ajouter :

    [!code-xml[Main](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/samples/sample3.xml?highlight=11-14)]
    
    > [!NOTE] 
    > Pour Visual Studio 2015 ou version ultérieure, remplacez `(localdb)\v11.0` avec `(localdb)\MSSQLLocalDB` dans votre chaîne de connexion.
    
7. Cliquez avec le bouton droit sur fichier *Register.aspx* dans votre projet, puis sélectionnez **définir comme Page de démarrage**. Appuyez sur Ctrl + F5 pour générer et exécuter l’application web. Entrez un nom d’utilisateur et un mot de passe, puis cliquez sur **inscrire**.  
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image6.png)  

    > [!NOTE]
    > Prend en charge la validation et ASP.NET Identity dans cet exemple, vous pouvez vérifier le comportement par défaut sur l’utilisateur et mot de passe validateurs sont fournis à partir du package de base de l’identité. Le programme de validation par défaut pour l’utilisateur (`UserValidator`) a une propriété `AllowOnlyAlphanumericUserNames` qui a la valeur par défaut est définie sur `true`. Le validateur de mot de passe par défaut (`MinimumLengthValidator`) garantit ce mot de passe a au moins 6 caractères. Les validateurs sont des propriétés sur `UserManager` qui peut être remplacée si vous souhaitez une validation personnalisée,

## <a name="verifying-the-localdb-identity-database-and-tables-generated-by-entity-framework"></a>Vérification de la base de données de l’identité de la base de données locale et les Tables générées par Entity Framework

1. Dans le **vue** menu, cliquez sur **l’Explorateur de serveurs**.  
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image7.png)
2. Développez **DefaultConnection (WebFormsIdentity)**, développez **Tables**, cliquez droit **AspNetUsers** et cliquez sur **afficher les données de Table**.  
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image8.png)  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image9.png)

## <a name="configuring-the-application-for-owin-authentication"></a>Configuration de l’application pour l’authentification OWIN

À ce stade nous avons uniquement prise en charge pour la création d’utilisateurs. Nous allons à présent, pour illustrer comment nous pouvons ajouter l’authentification pour un utilisateur de se connecter. ASP.NET Identity utilise l’authentification de Microsoft OWIN intergiciel (middleware) pour l’authentification par formulaire. L’authentification de Cookie OWIN est un cookie et le mécanisme d’authentification de base qui peut être utilisé par n’importe quelle infrastructure hébergée sur des revendications [OWIN](https://msdn.microsoft.com/magazine/dn451439.aspx) ou IIS. Avec ce modèle, les packages d’authentification utilisable sur plusieurs infrastructures, y compris ASP.NET MVC et les Web Forms. Pour plus d’informations sur le projet Katana et comment exécuter l’intergiciel (middleware) dans un voir agnostique hôte [mise en route avec le projet Katana](https://msdn.microsoft.com/magazine/dn451439.aspx).

## <a name="installing-authentication-packages-to-your-application"></a>L’installation des packages d’authentification pour votre application

1. Dans l’Explorateur de solutions, cliquez sur votre projet et sélectionnez **gérer les Packages NuGet**. Dans la fenêtre de zone de texte Rechercher, tapez «*Identity.Owin*». Cliquez sur Installer pour ce package.   
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image10.png)
2. Recherchez le package ***Microsoft.Owin.Host.SystemWeb*** et l’installer.   

    > [!NOTE]
    > Le **Microsoft.Aspnet.Identity.Owin** package contient un ensemble de classes d’extension OWIN pour gérer et configurer l’intergiciel (middleware) OWIN d’authentification à être consommés par les packages de base d’identité ASP.NET.  
    > Le **Microsoft.Owin.Host.SystemWeb** package contient un serveur OWIN qui permet aux applications de base OWIN s’exécutent sur IIS à l’aide du pipeline de demande ASP.NET. Pour plus d’informations, consultez [intergiciel OWIN dans IIS intégré pipeline](../../../aspnet/overview/owin-and-katana/owin-middleware-in-the-iis-integrated-pipeline.md).

## <a name="adding-owin-startup-and-authentication-configuration-classes"></a>Ajout de Classes de Configuration de l’authentification et de démarrage OWIN

1. Dans **l’Explorateur de solutions**, avec le bouton droit de votre projet, cliquez sur **ajouter**, puis **ajouter un nouvel élément**. Dans la fenêtre de zone de texte Rechercher, tapez «*owin*». Nom de la classe «*démarrage*» et cliquez sur **ajouter**.   
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image11.png)
2. Dans le fichier Startup.cs, ajoutez le code en surbrillance ci-dessous pour configurer l’authentification de cookie OWIN.

    [!code-csharp[Main](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/samples/sample4.cs?highlight=1,3,15-19)]

    > [!NOTE]
    > Cette classe contient le `OwinStartup` attribut pour spécifier la classe de démarrage OWIN. Chaque application OWIN a une classe de démarrage où vous spécifiez des composants pour le pipeline d’application. Consultez [détection de classe de démarrage OWIN](../../../aspnet/overview/owin-and-katana/owin-startup-class-detection.md) pour plus d’informations sur ce modèle.

## <a name="adding-web-forms-for-registering-and-logging-in-users"></a>Ajout de Web Forms pour l’inscription et connexion des utilisateurs

1. Ouvrez le *Register.cs* et ajoutez le code suivant qui enregistre l’utilisateur lors de l’inscription réussit. Les modifications sont mises en surbrillance ci-dessous.

    [!code-csharp[Main](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/samples/sample5.cs)]

    > [!NOTE] 
    > 
    > - Étant donné que ASP.NET Identity et l’authentification de Cookie OWIN sont basée sur les revendications du système, l’infrastructure requiert au développeur d’application générer un [ClaimsIdentity](https://msdn.microsoft.com/library/microsoft.identitymodel.claims.claimsidentity.aspx) pour l’utilisateur. ClaimsIdentity fournit des informations sur toutes les revendications pour l’utilisateur, telles que les rôles de l’utilisateur appartient. Vous pouvez également ajouter plusieurs revendications de l’utilisateur à ce stade.
    > - Vous pouvez vous connecter l’utilisateur à l’aide de la classe AuthenticationManager de OWIN et en appelant `SignIn` et en passant le ClaimsIdentity comme indiqué ci-dessus. Ce code sera connectez-vous à l’utilisateur et générer ainsi un cookie. Cet appel est analogue à [FormAuthentication.SetAuthCookie](https://msdn.microsoft.com/library/system.web.security.formsauthentication.setauthcookie.aspx) utilisé par le [FormsAuthentication](https://msdn.microsoft.com/library/system.web.security.formsauthenticationmodule.aspx) module.
2. Dans **l’Explorateur de solutions**, avec le bouton droit de votre projet, cliquez **ajouter**, puis **Web Form**. Nommez le formulaire web **connexion**.  
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image12.png)
3. Remplacez le contenu de la *Login.aspx* fichier avec le code suivant :  

    [!code-aspx[Main](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/samples/sample6.aspx)]
4. Remplacez le contenu de la *Login.aspx.cs* fichier avec les éléments suivants :  

    [!code-csharp[Main](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/samples/sample7.cs)]

    > [!NOTE] 
    > 
    > - Le `Page_Load` maintenant vérifie l’état de l’utilisateur actuel et prend des mesures en fonction de son `Context.User.Identity.IsAuthenticated` état.  
    >     **Afficher enregistrée dans le nom d’utilisateur** : l’infrastructure d’identité Microsoft ASP.NET a ajouté des méthodes d’extension sur [System.Security.Principal.IIdentity](https://msdn.microsoft.com/library/system.security.principal.iidentity.aspx) qui vous permet d’obtenir le `UserName` et `UserId` pour le Utilisateur connecté. Ces méthodes d’extension sont définies dans le `Microsoft.AspNet.Identity.Core` assembly. Ces méthodes d’extension sont le remplacement de [HttpContext.User.Identity.Name](https://msdn.microsoft.com/library/system.web.httpcontext.user.aspx) .
    > - Méthode de connexion :   
    >     `This`méthode remplace la précédente `CreateUser_Click` méthode dans cet exemple et cette maintenant connecte l’utilisateur après la création réussie de l’utilisateur.   
    >  L’infrastructure OWIN de Microsoft a ajouté des méthodes d’extension sur `System.Web.HttpContext` qui vous permet d’obtenir une référence à un `IOwinContext`. Ces méthodes d’extension sont définies dans `Microsoft.Owin.Host.SystemWeb` assembly. Le `OwinContext` classe expose un `IAuthenticationManager` propriété qui représente les fonctionnalités d’intergiciel (middleware) d’authentification disponibles sur la requête actuelle.  
    >  Vous pouvez vous connecter l’utilisateur à l’aide de la `AuthenticationManager` de OWIN et en appelant `SignIn` et en passant le `ClaimsIdentity` comme indiqué ci-dessus.   
    >  Identité ASP.NET et authentification de Cookie OWIN étant système basé sur les revendications, le framework requiert que l’application pour générer un `ClaimsIdentity` pour l’utilisateur.   
    >  Le `ClaimsIdentity` a des informations sur toutes les revendications pour l’utilisateur, telles que les rôles de l’utilisateur appartient. Vous pouvez également ajouter plusieurs revendications de l’utilisateur à ce stade  
    >  Ce code sera connectez-vous à l’utilisateur et générer ainsi un cookie. Cet appel est analogue à [FormAuthentication.SetAuthCookie](https://msdn.microsoft.com/library/system.web.security.formsauthentication.setauthcookie.aspx) utilisé par le [FormsAuthentication](https://msdn.microsoft.com/library/system.web.security.formsauthenticationmodule.aspx) module.
    > - `SignOut`méthode :   
    >  Obtient une référence à la `AuthenticationManager` OWIN et appelle `SignOut`. Cela est analogue à [FormsAuthentication.SignOut](https://msdn.microsoft.com/library/system.web.security.formsauthentication.signout.aspx) méthode utilisée par le [FormsAuthentication](https://msdn.microsoft.com/library/system.web.security.formsauthenticationmodule.aspx) module.
5. Appuyez sur **Ctrl + F5** pour générer et exécuter l’application web. Entrez un nom d’utilisateur et un mot de passe, puis cliquez sur **inscrire**.  
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image13.png)  
 Remarque : À ce stade, le nouvel utilisateur est créé et enregistré dans.
6. Cliquez sur **déconnecter** bouton. Vous allez être redirigé dans le journal dans le formulaire.
7. Entrez un nom d’utilisateur non valide ou un mot de passe et un clic sur **connecter** bouton.   
 Le `UserManager.Find` méthode retournera null et le message d’erreur : « *nom d’utilisateur non valide ou le mot de passe* » s’affiche.  
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image14.png)
