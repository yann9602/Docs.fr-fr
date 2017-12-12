---
uid: identity/overview/extensibility/change-primary-key-for-users-in-aspnet-identity
title: "Modifier une clé primaire pour les utilisateurs dans ASP.NET Identity | Documents Microsoft"
author: tfitzmac
description: "Dans Visual Studio 2013, l’application web par défaut utilise une valeur de chaîne de la clé pour les comptes d’utilisateur. Identité ASP.NET permet de modifier le type de la..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/30/2014
ms.topic: article
ms.assetid: 44925849-5762-4504-a8cd-8f0cd06f6dc3
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /identity/overview/extensibility/change-primary-key-for-users-in-aspnet-identity
msc.type: authoredcontent
ms.openlocfilehash: 79812efb4de2461fad3765d6005bbd20393e62b2
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="change-primary-key-for-users-in-aspnet-identity"></a>Modifier la clé primaire pour les utilisateurs dans ASP.NET Identity
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Dans Visual Studio 2013, l’application web par défaut utilise une valeur de chaîne de la clé pour les comptes d’utilisateur. Identité ASP.NET vous permet de modifier le type de la clé pour répondre aux besoins de vos données. Par exemple, vous pouvez modifier le type de la clé à partir d’une chaîne en entier.
> 
> Cette rubrique montre comment démarrer avec la valeur par défaut application web et modifier la clé de compte d’utilisateur en un entier. Vous pouvez utiliser les mêmes modifications à implémenter n’importe quel type de clé dans votre projet. Il montre comment effectuer ces modifications dans l’application web par défaut, mais vous pouvez appliquer des modifications similaires à une application personnalisée. Il montre les modifications nécessaires lorsque vous travaillez avec MVC ou Web Forms.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Visual Studio 2013 Update 2 (ou version ultérieure)
> - ASP.NET Identity 2.1 ou version ultérieure


Pour effectuer les étapes de ce didacticiel, vous devez disposer de Visual Studio 2013 Update 2 (ou version ultérieure) et une application web créée à partir du modèle d’Application Web ASP.NET. Le modèle modifié dans Update 3. Cette rubrique montre comment modifier le modèle de mise à jour 2 et 3 de la mise à jour.

Cette rubrique contient les sections suivantes :

- [Modifier le type de la clé dans la classe d’identité utilisateur](#userclass)
- [Ajouter des classes personnalisées de l’identité qui utilisent le type de clé](#customclass)
- [Modifier le Gestionnaire de contexte utilisateur et pour utiliser le type de clé](#context)
- [Modifier la configuration de démarrage à utiliser le type de clé](#startup)
- [Pour MVC avec mise à jour 2, modifiez le AccountController pour passer le type de clé](#mvcupdate2)
- [Pour MVC avec Update 3, modifier l’AccountController et un ManageController pour passer le type de clé](#mvcupdate3)
- [Pour les formulaires Web avec mise à jour 2, modifiez les pages de compte pour passer le type de clé](#webformsupdate2)
- [Pour les formulaires Web avec Update 3, modifiez les pages de compte pour passer le type de clé](#webformsupdate3)
- [Exécuter l’application](#run)
- [Autres ressources](#other)

<a id="userclass"></a>
## <a name="change-the-type-of-the-key-in-the-identity-user-class"></a>Modifier le type de la clé dans la classe d’identité utilisateur

Dans votre projet à partir du modèle d’Application Web ASP.NET, spécifiez que la classe ApplicationUser utilise un entier de la clé pour les comptes d’utilisateur. Dans IdentityModels.cs, modifiez la classe ApplicationUser hériter IdentityUser qui a un type de **int** pour le paramètre générique TKey. Vous passez également les noms des trois classes personnalisées dont vous n’avez pas encore implémentée.

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample1.cs?highlight=1-2)]

Vous avez changé le type de la clé, mais par défaut, le reste de l’application utilise toujours que la clé est une chaîne. Vous devez indiquer explicitement le type de la clé dans le code qui suppose une chaîne.

Dans le **ApplicationUser** de classe, de modifier le **GenerateUserIdentityAsync** méthode pour inclure le type int, comme indiqué dans le code en surbrillance ci-dessous. Cette modification n’est pas nécessaire pour les projets Web Forms avec le modèle de mise à jour 3.

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample2.cs?highlight=2)]

<a id="customclass"></a>
## <a name="add-customized-identity-classes-that-use-the-key-type"></a>Ajouter des classes personnalisées de l’identité qui utilisent le type de clé

Les autres classes d’identité, telles que IdentityUserRole, IdentityUserClaim, IdentityUserLogin, IdentityRole, UserStore, RoleStore, sont toujours configurés pour utiliser une clé de chaîne. Créer de nouvelles versions de ces classes qui spécifient un entier pour la clé. Vous n’avez pas besoin de fournir de code d’implémentation de ces classes, vous définissez principalement simplement int comme clé.

Ajoutez les classes suivantes dans votre fichier IdentityModels.cs.

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample3.cs)]

<a id="context"></a>
## <a name="change-the-context-class-and-user-manager-to-use-the-key-type"></a>Modifier le Gestionnaire de contexte utilisateur et pour utiliser le type de clé

Dans IdentityModels.cs, modifiez la définition de la **ApplicationDbContext** à utiliser votre nouvelle classe personnalisée des classes et une **int** pour la clé, comme indiqué dans le code en surbrillance.

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample4.cs?highlight=1-2)]

Le paramètre ThrowIfV1Schema n’est plus valide dans le constructeur. Modifiez le constructeur afin qu’il ne passez pas de valeur ThrowIfV1Schema.

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample5.cs)]

Ouvrez IdentityConfig.cs et modifiez le **ApplicationUserManger** classe pour conserver les données de banque de classe à utiliser votre nouvel utilisateur et un **int** pour la clé.

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample6.cs?highlight=1,3,12,14,32,37,48)]

Dans le modèle de mise à jour 3, vous devez modifier la classe ApplicationSignInManager.

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample7.cs?highlight=1)]

<a id="startup"></a>
## <a name="change-start-up-configuration-to-use-the-key-type"></a>Modifier la configuration de démarrage à utiliser le type de clé

Dans Startup.Auth.cs, remplacez le code OnValidateIdentity, comme indiqué ci-dessous. Notez que la définition de getUserIdCallback, analyse la valeur de chaîne en un entier.

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample8.cs?highlight=7-12)]

Si votre projet ne reconnaît pas l’implémentation générique de la **GetUserId** (méthode), vous devez mettre à jour le package NuGet d’identité ASP.NET à la version 2.1

Vous avez effectué un grand nombre de modifications dans les classes d’infrastructure utilisées par ASP.NET Identity. Si vous essayez de compiler le projet, vous remarquez de nombreuses erreurs. Heureusement, les erreurs restantes sont similaires. La classe d’identité attend un entier pour la clé, mais le contrôleur (ou un formulaire Web) est le passage d’une valeur de chaîne. Dans chaque cas, vous devez convertir à partir d’une chaîne et un entier en appelant **GetUserId&lt;int&gt;**. Vous pouvez parcourir la liste d’erreurs de compilation ou suivre les modifications ci-dessous.

Les modifications restantes dépendent du type de projet que vous créez et la mise à jour que vous avez installé dans Visual Studio. Vous pouvez accéder directement à la section relative aux via les liens suivants

- [Pour MVC avec mise à jour 2, modifiez le AccountController pour passer le type de clé](#mvcupdate2)
- [Pour MVC avec Update 3, modifier l’AccountController et un ManageController pour passer le type de clé](#mvcupdate3)
- [Pour les formulaires Web avec mise à jour 2, modifiez les pages de compte pour passer le type de clé](#webformsupdate2)
- [Pour les formulaires Web avec Update 3, modifiez les pages de compte pour passer le type de clé](#webformsupdate3)

<a id="mvcupdate2"></a>
## <a name="for-mvc-with-update-2-change-the-accountcontroller-to-pass-the-key-type"></a>Pour MVC avec mise à jour 2, modifiez le AccountController pour passer le type de clé

Ouvrez le fichier AccountController.cs. Vous devez modifier les méthodes suivantes.

**ConfirmEmail** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample9.cs?highlight=1,3)]

**Dissocier** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample10.cs?highlight=5,9)]

**Manage(ManageUserViewModel)** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample11.cs?highlight=11,17,41)]

**LinkLoginCallback** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample12.cs?highlight=10)]

**RemoveAccountList** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample13.cs?highlight=3)]

**HasPassword** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample14.cs?highlight=3)]

Vous pouvez désormais [exécuter l’application](#run) et inscrire un nouvel utilisateur.

<a id="mvcupdate3"></a>
## <a name="for-mvc-with-update-3-change-the-accountcontroller-and-managecontroller-to-pass-the-key-type"></a>Pour MVC avec Update 3, modifier l’AccountController et un ManageController pour passer le type de clé

Ouvrez le fichier AccountController.cs. Vous devez modifier la méthode suivante.

**ConfirmEmail** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample15.cs?highlight=1,3)]

**SendCode** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample16.cs?highlight=4)]

Ouvrez le fichier ManageController.cs. Vous devez modifier les méthodes suivantes.

**Index** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample17.cs?highlight=15-17)]

**RemoveLogin** méthodes

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample18.cs?highlight=3,13,17)]

**AddPhoneNumber** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample19.cs?highlight=9)]

**EnableTwoFactorAuthentication** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample20.cs?highlight=3-4)]

**DisableTwoFactorAuthentication** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample21.cs?highlight=3-4)]

**VerifyPhoneNumber** méthodes

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample22.cs?highlight=4,18,21)]

**RemovePhoneNumber** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample23.cs?highlight=3,8)]

**ChangePassword** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample24.cs?highlight=10,13)]

**SetPassword** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample25.cs?highlight=5,8)]

**ManageLogins** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample26.cs?highlight=7,12)]

**LinkLoginCallback** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample27.cs?highlight=8)]

**HasPassword** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample28.cs?highlight=3)]

**HasPhoneNumber** (méthode)

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample29.cs?highlight=3)]

Vous pouvez désormais [exécuter l’application](#run) et inscrire un nouvel utilisateur.

<a id="webformsupdate2"></a>
## <a name="for-web-forms-with-update-2-change-account-pages-to-pass-the-key-type"></a>Pour les formulaires Web avec mise à jour 2, modifiez les pages de compte pour passer le type de clé

Pour les formulaires Web avec mise à jour 2, vous devez modifier les pages suivantes.

**Confirm.aspx.CX**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample30.cs?highlight=8)]

**RegisterExternalLogin.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample31.cs?highlight=36)]

**Manage.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample32.cs?highlight=3,22,47,52,69,85,93,98)]

Vous pouvez désormais [exécuter l’application](#run) et inscrire un nouvel utilisateur.

<a id="webformsupdate3"></a>
## <a name="for-web-forms-with-update-3-change-account-pages-to-pass-the-key-type"></a>Pour les formulaires Web avec Update 3, modifiez les pages de compte pour passer le type de clé

Pour les formulaires Web avec Update 3, vous devez modifier les pages suivantes.

**Confirm.aspx.CX**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample33.cs?highlight=8)]

**RegisterExternalLogin.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample34.cs?highlight=36)]

**Manage.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample35.cs?highlight=11,27,32,34,82,87,99,108)]

**VerifyPhoneNumber.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample36.cs?highlight=8,23,27)]

**AddPhoneNumber.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample37.cs?highlight=7)]

**ManagePassword.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample38.cs?highlight=11,47,50,68)]

**ManageLogins.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample39.cs?highlight=16,23,32,41,45)]

**TwoFactorAuthenticationSignIn.aspx.cs**

[!code-csharp[Main](change-primary-key-for-users-in-aspnet-identity/samples/sample40.cs?highlight=14-15,29,53)]

<a id="run"></a>
## <a name="run-application"></a>Exécuter l’application

Vous avez terminé les modifications nécessaires dans le modèle d’Application Web par défaut. Exécutez l’application et inscrire un nouvel utilisateur. Après l’inscription de l’utilisateur, vous remarquerez que la table de AspNetUsers a une colonne d’Id qui est un entier.

![nouvelle clé primaire](change-primary-key-for-users-in-aspnet-identity/_static/image1.png)

Si vous avez précédemment créé à l’identité ASP.NET tables avec une autre clé primaire, vous devez apporter des modifications supplémentaires. Si possible, supprimez simplement la base de données existante. La base de données sera recréée avec le design correct lorsque vous exécutez l’application web et que vous ajoutez un nouvel utilisateur. Si la suppression n’est pas possible, exécutez les migrations code first pour modifier les tables. Toutefois, la nouvelle clé primaire entier ne sera pas être définie comme une propriété d’identité de SQL dans la base de données. Vous devez définir manuellement la colonne d’Id en tant qu’identité.

<a id="other"></a>
## <a name="other-resources"></a>Autres ressources

- [Vue d’ensemble des fournisseurs de stockage personnalisé d’identité ASP.NET](overview-of-custom-storage-providers-for-aspnet-identity.md)
- [Migration d’un site Web existant à partir de l’appartenance SQL pour ASP.NET Identity](../migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity.md)
- [Migration de données universel de fournisseur pour l’appartenance et les profils utilisateur à l’identité ASP.NET](../migrations/migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity.md)
- [Exemple d’application](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/ChangePK/readme.txt) avec une clé primaire modifiée
