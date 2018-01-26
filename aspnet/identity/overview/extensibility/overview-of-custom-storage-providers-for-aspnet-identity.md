---
uid: identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity
title: "Vue d’ensemble des fournisseurs de stockage personnalisés pour ASP.NET Identity | Documents Microsoft"
author: tfitzmac
description: "ASP.NET Identity est un système extensible qui vous permet de créer votre propre fournisseur de stockage et connectez-le à votre application sans utiliser de nouveau l’Affich..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/13/2014
ms.topic: article
ms.assetid: 681a9204-462e-4260-9a0b-19f0644d6ad7
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity
msc.type: authoredcontent
ms.openlocfilehash: f43f0a2dd80e26ecff15e5742e18264ddb5b26aa
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="overview-of-custom-storage-providers-for-aspnet-identity"></a>Vue d’ensemble des fournisseurs de stockage personnalisé d’identité ASP.NET
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> ASP.NET Identity est un système extensible qui vous permet de créer votre propre fournisseur de stockage et connectez-le à votre application sans utiliser de nouveau l’application. Cette rubrique décrit comment créer un fournisseur de stockage personnalisé pour ASP.NET Identity. Il traite des concepts importants pour la création de votre propre fournisseur de stockage, mais il n’est pas procédure pas à pas d’implémentation d’un fournisseur de stockage personnalisé.
> 
> Pour obtenir un exemple d’implémentation d’un fournisseur de stockage personnalisé, consultez [implémentation d’un fournisseur de stockage d’identité de ASP.NET MySQL personnalisé](implementing-a-custom-mysql-aspnet-identity-storage-provider.md).
> 
> Cette rubrique a été mis à jour pour ASP.NET Identity 2.0.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Visual Studio 2013 Update 2
> - Identité de ASP.NET 2


## <a name="introduction"></a>Introduction

Par défaut, le système d’identité ASP.NET stocke les informations utilisateur dans une base de données SQL Server et il utilise Entity Framework Code First pour créer la base de données. Pour de nombreuses applications, cette approche fonctionne bien. Toutefois, vous pouvez utiliser un autre type de mécanisme de persistance, tels que le stockage de Table, ou vous avez peut-être déjà des tables de base de données avec une structure très différente de l’implémentation par défaut. Dans les deux cas, vous pouvez écrire un fournisseur personnalisé pour votre mécanisme de stockage et connectez ce fournisseur de votre application.

ASP.NET Identity est inclus par défaut dans la plupart des modèles Visual Studio 2013. Vous pouvez obtenir les mises à jour pour ASP.NET Identity via [Microsoft AspNet Identity EntityFramework NuGet package](http://www.nuget.org/packages/Microsoft.AspNet.Identity.EntityFramework/).

Cette rubrique comporte les sections suivantes :

- [Comprendre l’architecture](#architecture)
- [Comprendre les données stockées](#data)
- [Création de la couche d’accès aux données](#dal)
- [Personnaliser la classe d’utilisateur](#user)
- [Personnaliser le magasin de l’utilisateur](#userstore)
- [Personnaliser la classe de rôle](#role)
- [Personnaliser le magasin de rôles](#rolestore)
- [Reconfigurer l’application pour utiliser le nouveau fournisseur de stockage](#reconfigure)
- [D’autres implémentations de fournisseurs de stockage personnalisé](#other)

<a id="architecture"></a>
## <a name="understand-the-architecture"></a>Comprendre l’architecture

ASP.NET Identity se compose de classes appelées des responsables et des magasins. Les gestionnaires sont des classes de haut niveau qui utilise des développeurs d’applications pour effectuer des opérations, telles que la création d’un utilisateur, dans le système d’identité ASP.NET. Les magasins sont des classes de niveau inférieur qui spécifient le mode de conservation des entités, telles que les utilisateurs et les rôles. Magasins sont étroitement couplées avec le mécanisme de persistance, mais les gestionnaires sont dissociés de magasins, ce qui signifie que vous pouvez remplacer le mécanisme de persistance sans interrompre l’application entière.

Le diagramme suivant illustre la façon dont votre application web interagit avec les gestionnaires et magasins d’interagissent avec la couche d’accès aux données.

![](overview-of-custom-storage-providers-for-aspnet-identity/_static/image1.png)

Pour créer un fournisseur de stockage personnalisé pour l’identité d’ASP.NET, vous devez créer la source de données, la couche d’accès aux données et les classes de magasin qui interagissent avec cette couche d’accès aux données. Vous pouvez continuer à l’aide du même gestionnaire de l’API pour effectuer des opérations de données sur l’utilisateur, mais maintenant que les données sont enregistrées dans un système de stockage différents.

Vous n’avez pas besoin de personnaliser les classes de gestionnaire, car lors de la création d’une nouvelle instance de UserManager ou RoleManager vous fournissez le type de la classe d’utilisateur et de passer une instance de la classe du magasin en tant qu’argument. Cette approche vous permet de brancher vos classes personnalisées de la structure existante. Vous verrez comment instancier UserManager et RoleManager avec vos classes de magasin personnalisé dans la section [reconfigurer l’application pour utiliser le nouveau fournisseur de stockage](#reconfigure).

<a id="data"></a>
## <a name="understand-the-data-that-is-stored"></a>Comprendre les données stockées

Pour implémenter un fournisseur de stockage personnalisé, vous devez comprendre les types de données utilisées dans ASP.NET Identity et déterminer les fonctionnalités qui sont pertinentes pour votre application.

| Données | Description |
| --- | --- |
| Utilisateurs | Utilisateurs enregistrés de votre site web. Inclut le nom d’utilisateur et Id utilisateur. Peut inclure un mot de passe haché si les utilisateurs se connectent avec des informations d’identification qui sont spécifiques à votre site (plutôt qu’à l’aide des informations d’identification à partir d’un site externe comme Facebook) et l’horodatage de sécurité pour indiquer si quelque chose a changé dans les informations d’identification de l’utilisateur. Peut également inclure les adresses de messagerie, numéro de téléphone, l’authentification à deux facteurs est activée, le nombre d’échecs de connexion, et indique si un compte a été verrouillé. |
| Revendications d’utilisateur | Un ensemble d’instructions (ou les revendications) sur l’utilisateur qui représentent l’identité de l’utilisateur. Permet la plus grande expression d’identité de l’utilisateur que peut être obtenue via des rôles. |
| Connexions utilisateur | Informations sur le fournisseur d’authentification externe (tel que Facebook) à utiliser lors de la connexion d’un utilisateur. |
| Rôles | Groupes d’autorisation pour votre site. Inclut le nom de rôle Id et le rôle (par exemple, « Admin » ou « Employee »). |

<a id="dal"></a>
## <a name="create-the-data-access-layer"></a>Création de la couche d’accès aux données

Cette rubrique suppose que vous êtes familiarisé avec le mécanisme de persistance que vous vous apprêtez à utiliser et comment créer des entités pour ce mécanisme. Cette rubrique ne fournit pas de détails sur la façon de créer des référentiels ou classes d’accès aux données ; au lieu de cela, il fournit des suggestions sur les décisions de conception, que vous devez faire lorsque vous travaillez avec ASP.NET Identity.

Vous avez beaucoup de liberté lorsque vous concevez les référentiels pour personnalisé de fournisseur de magasin. Vous devez uniquement créer des référentiels pour les fonctionnalités que vous souhaitez utiliser dans votre application. Par exemple, si vous n’utilisez pas les rôles dans votre application, il est inutile créer le stockage des rôles ou des rôles d’utilisateur. Votre infrastructure existante et des technologies peuvent nécessiter une structure est très différente de l’implémentation par défaut d’identité ASP.NET. Dans votre couche d’accès aux données, vous fournir la logique permettant de travailler sur la structure de vos référentiels.

Pour une implémentation MySQL des référentiels de données pour l’identité de ASP.NET 2.0, consultez [MySQLIdentity.sql](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/MySQLIdentity.sql).

Dans la couche d’accès aux données, vous fournir la logique pour enregistrer les données d’identité de ASP.NET à votre source de données. La couche d’accès aux données pour votre fournisseur de stockage personnalisé peut inclure les classes suivantes pour stocker les informations utilisateur et le rôle.

| Classe | Description | Exemple |
| --- | --- | --- |
| Contexte | Encapsule les informations pour vous connecter à votre mécanisme de persistance et d’exécuter des requêtes. Cette classe est essentiel à la couche d’accès aux données. Les autres classes de données nécessitera une instance de cette classe pour effectuer leurs opérations. Vous serez également initialiser vos classes de magasin avec une instance de cette classe. | [MySQLDatabase](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/MySQLDatabase.cs) |
| Stockage de l’utilisateur | Stocke et récupère les informations de l’utilisateur (par exemple, le hachage du nom et mot de passe utilisateur). | [UserTable (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserTable.cs) |
| Stockage de rôle | Stocke et récupère les informations de rôle (par exemple, le nom de rôle). | [RoleTable (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/RoleTable.cs) |
| Stockage d’userclaims. | Stocke et récupère les informations de revendication d’utilisateur (par exemple, le type de revendication et la valeur). | [UserClaimsTable (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserClaimsTable.cs) |
| Stockage d’userlogins. | Stocke et récupère les informations de connexion d’utilisateur (par exemple, un fournisseur d’authentification externe). | [UserLoginsTable (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserLoginsTable.cs) |
| Stockage du rôle d’utilisateur | Stocke et récupère les rôles d’un utilisateur est assigné. | [UserRoleTable (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserRoleTable.cs) |

Là encore, vous devez uniquement implémenter les classes que vous souhaitez utiliser dans votre application.

Dans les classes d’accès aux données, vous fournissez le code pour effectuer des opérations de données pour votre mécanisme de persistance particulière. Par exemple, dans l’implémentation de MySQL, la classe UserTable contient une méthode pour insérer un nouvel enregistrement dans la table de base de données des utilisateurs. La variable nommée `_database` est une instance de la classe MySQLDatabase.

[!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample1.cs)]

Après avoir créé vos classes d’accès aux données, vous devez créer des classes de magasin qui appellent les méthodes spécifiques dans la couche d’accès aux données.

<a id="user"></a>
## <a name="customize-the-user-class"></a>Personnaliser la classe d’utilisateur

Lorsque vous implémentez votre propre fournisseur de stockage, vous devez créer une classe d’utilisateur qui est équivalente à la [IdentityUser](https://msdn.microsoft.com/library/microsoft.aspnet.identity.entityframework.identityuser(v=vs.108).aspx) classe dans le [Microsoft.ASP.NET.Identity.EntityFramework](https://msdn.microsoft.com/library/microsoft.aspnet.identity.entityframework(v=vs.108).aspx) espace de noms :

Le diagramme suivant illustre la classe IdentityUser que vous devez créer et l’interface à implémenter dans cette classe.

![](overview-of-custom-storage-providers-for-aspnet-identity/_static/image2.png)

Le [IUser&lt;TKey&gt; ](https://msdn.microsoft.com/library/dn613291(v=vs.108).aspx) interface définit les propriétés qui tente du UserManager à appeler lorsque l’exécution des opérations demandées. L’interface contient deux propriétés - Id et nom d’utilisateur. Le [IUser&lt;TKey&gt; ](https://msdn.microsoft.com/library/dn613291(v=vs.108).aspx) interface vous permet de spécifier le type de la clé pour l’utilisateur via l’objet générique **TKey** paramètre. Le type de la propriété Id correspond à la valeur du paramètre TKey.

L’infrastructure d’identité fournit également la [IUser](https://msdn.microsoft.com/library/microsoft.aspnet.identity.iuser(v=vs.108).aspx) interface (sans le paramètre générique) lorsque vous souhaitez utiliser une valeur de chaîne pour la clé.

La classe IdentityUser implémente IUser et contient des propriétés supplémentaires ou des constructeurs pour les utilisateurs sur votre site web. L’exemple suivant montre une classe IdentityUser qui utilise un entier pour la clé. Le champ Id est défini sur **int** corresponde à la valeur du paramètre générique. 

[!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample2.cs)]

 Pour une implémentation complète, consultez [IdentityUser (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/IdentityUser.cs). 

<a id="userstore"></a>
## <a name="customize-the-user-store"></a>Personnaliser le magasin de l’utilisateur

Vous créer également une classe UserStore qui fournit les méthodes pour toutes les opérations de données sur l’utilisateur. Cette classe est équivalente à la [UserStore&lt;TUser&gt; ](https://msdn.microsoft.com/library/dn315446(v=vs.108).aspx) classe dans le [Microsoft.ASP.NET.Identity.EntityFramework](https://msdn.microsoft.com/library/microsoft.aspnet.identity.entityframework(v=vs.108).aspx) espace de noms. Dans votre classe UserStore, vous implémentez le [IUserStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613276(v=vs.108).aspx) et une des interfaces facultatives. Vous sélectionnez les interfaces facultatives à implémenter, selon les fonctionnalités que vous souhaitez fournir dans votre application.

L’illustration suivante montre la classe UserStore, que vous devez créer et les interfaces appropriées.

![](overview-of-custom-storage-providers-for-aspnet-identity/_static/image3.png)

Le modèle de projet par défaut dans Visual Studio contient le code qui suppose que la plupart des interfaces facultatives ont été implémentées dans le magasin de l’utilisateur. Si vous utilisez le modèle par défaut avec un magasin d’utilisateurs personnalisé, vous devez implémenter des interfaces facultatives dans votre magasin de l’utilisateur ou modifier le code du modèle pour appeler des méthodes ne sont plus dans les interfaces que vous n’avez pas implémenté.

 L’exemple suivant montre une classe de magasin simple de l’utilisateur. Le **TUser** paramètre générique prend le type de votre classe d’utilisateur qui est généralement de la classe IdentityUser définie. Le **TKey** paramètre générique prend le type de votre clé de l’utilisateur. 

[!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample3.cs)]

 Dans cet exemple, le constructeur qui accepte un paramètre nommé *base de données* de type ExampleDatabase n'est qu’une illustration comment transmettre dans votre classe d’accès aux données. Par exemple, dans l’implémentation de MySQL, ce constructeur prend un paramètre de type MySQLDatabase. 

Dans votre classe UserStore, vous utilisez les classes d’accès aux données que vous avez créé pour effectuer des opérations. Par exemple, dans l’implémentation de MySQL, la classe UserStore a la méthode utilise createasync pour créer, qui utilise une instance de UserTable pour insérer un nouvel enregistrement. Le **insérer** méthode sur le **userTable** objet est la même méthode que celle qui a été indiquée dans la section précédente. 

[!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample4.cs)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>Interfaces à implémenter lors de la personnalisation du magasin de l’utilisateur

L’image suivante montre le plus de détails sur la fonctionnalité définie dans chaque interface. Toutes les interfaces facultatives héritent de IUserStore.

![](overview-of-custom-storage-providers-for-aspnet-identity/_static/image4.png)

- **IUserStore**  
 Le [IUserStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613278(v=vs.108).aspx) interface est la seule interface, vous devez implémenter dans votre magasin de l’utilisateur. Elle définit des méthodes pour la création, la mise à jour, la suppression et la récupération des utilisateurs.
- **IUserClaimStore**  
 Le [IUserClaimStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613265(v=vs.108).aspx) interface définit les méthodes que vous devez implémenter dans votre magasin de l’utilisateur à activer les revendications d’utilisateur. Il contienne des méthodes ou ajout, suppression et la récupération des revendications d’utilisateur.
- **IUserLoginStore**  
 Le [IUserLoginStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613272(v=vs.108).aspx) définit les méthodes que vous devez implémenter dans votre magasin de l’utilisateur pour permettre aux fournisseurs d’authentification externe. Il contient des méthodes pour ajouter, supprimer et la récupération des connexions utilisateur et une méthode pour récupérer un utilisateur selon les informations de connexion.
- **IUserRoleStore**  
 Le [IUserRoleStore&lt;TKey, TUser&gt; ](https://msdn.microsoft.com/library/dn613276(v=vs.108).aspx) interface définit les méthodes que vous devez implémenter dans votre magasin de l’utilisateur pour mapper un utilisateur à un rôle. Il contient des méthodes pour ajouter, supprimer et récupérer des rôles d’un utilisateur et une méthode pour vérifier si un utilisateur est assigné à un rôle.
- **IUserPasswordStore**  
 Le [IUserPasswordStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613273(v=vs.108).aspx) interface définit les méthodes que vous devez implémenter dans votre magasin de l’utilisateur pour conserver les hacher des mots de passe. Il contient des méthodes pour obtenir et définir le mot de passe haché et une méthode qui indique si l’utilisateur a défini un mot de passe.
- **IUserSecurityStampStore**  
 Le [IUserSecurityStampStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613277(v=vs.108).aspx) interface définit les méthodes que vous devez implémenter dans votre magasin de l’utilisateur à utiliser un tampon de sécurité permettant d’indiquer si les informations de compte de l’utilisateur a changé. . Cet horodatage est mis à jour lorsqu’un utilisateur modifie le mot de passe, ou ajoute ou supprime des connexions. Il contient des méthodes pour obtenir et définir l’horodatage de sécurité.
- **IUserTwoFactorStore**  
 Le [IUserTwoFactorStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613279(v=vs.108).aspx) interface définit les méthodes que vous devez implémenter pour implémenter l’authentification à deux facteurs. Il contient des méthodes pour obtenir et définir si l’authentification à deux facteurs est activée pour un utilisateur.
- **IUserPhoneNumberStore**  
 Le [IUserPhoneNumberStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613275(v=vs.108).aspx) interface définit les méthodes que vous devez implémenter pour stocker les numéros de téléphone des utilisateurs. Il contient des méthodes pour obtenir et définir le numéro de téléphone et indique si le numéro de téléphone est confirmé.
- **IUserEmailStore**  
 Le [IUserEmailStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613143(v=vs.108).aspx) interface définit les méthodes que vous devez implémenter pour stocker les adresses de messagerie d’utilisateur. Il contient des méthodes pour obtenir et définir l’adresse de messagerie et si l’adresse de messagerie est confirmée.
- **IUserLockoutStore**  
 Le [IUserLockoutStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613271(v=vs.108).aspx) interface définit les méthodes à implémenter pour stocker des informations sur le verrouillage d’un compte. Il contient des méthodes pour l’obtention du nombre actuel de tentatives d’accès ayant échoué, l’obtention et définition si le compte peut être verrouillé, mise en route et de définir la date de fin de verrouillage, incrémente le nombre de tentatives infructueuses et réinitialiser le nombre de tentatives ayant échoué.
- **IQueryableUserStore**  
 Le [IQueryableUserStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613267(v=vs.108).aspx) interface définit les membres que vous devez implémenter pour fournir un magasin d’utilisateurs utilisable. Il contient une propriété qui contient les utilisateurs utilisable dans une requête.

 Vous implémentez les interfaces qui sont nécessaires dans votre application ; par exemple, le IUserClaimStore, IUserLoginStore, IUserRoleStore, IUserPasswordStore et IUserSecurityStampStore les interfaces comme indiqué ci-dessous. 

[!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample5.cs)]

Pour une implémentation complète (y compris toutes les interfaces), consultez [UserStore (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserStore.cs).

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a>IdentityUserClaim, IdentityUserLogin et IdentityUserRole

L’espace de noms Microsoft.AspNet.Identity.EntityFramework contient des implémentations de la [IdentityUserClaim](https://msdn.microsoft.com/library/dn613250(v=vs.108).aspx), [IdentityUserLogin](https://msdn.microsoft.com/library/dn613251(v=vs.108).aspx), et [IdentityUserRole](https://msdn.microsoft.com/library/dn613252(v=vs.108).aspx) classes. Si vous utilisez ces fonctionnalités, vous souhaiterez créer vos propres versions de ces classes et de définir les propriétés de votre application. Toutefois, il est parfois plus efficace pour ne pas charger ces entités dans la mémoire lors de l’exécution des opérations de base (par exemple, ajout ou suppression de revendication de l’utilisateur). Au lieu de cela, les classes de magasin principal peuvent exécuter ces opérations directement sur la source de données. Par exemple, la méthode UserStore.GetClaimsAsync() peut appeler l’userClaimTable.FindByUserId(user. ID) méthode pour exécuter une requête sur table directement et retourner une liste de revendications.

[!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample6.cs)]

<a id="role"></a>
## <a name="customize-the-role-class"></a>Personnaliser la classe de rôle

Lorsque vous implémentez votre propre fournisseur de stockage, vous devez créer une classe de rôle qui est équivalente à la [IdentityRole](https://msdn.microsoft.com/library/microsoft.aspnet.identity.entityframework.identityrole(v=vs.108).aspx) classe dans le [Microsoft.ASP.NET.Identity.EntityFramework](https://msdn.microsoft.com/library/microsoft.aspnet.identity.entityframework(v=vs.108).aspx) espace de noms :

Le diagramme suivant illustre la classe IdentityRole que vous devez créer et l’interface à implémenter dans cette classe.

![](overview-of-custom-storage-providers-for-aspnet-identity/_static/image5.png)

Le [IRole&lt;TKey&gt; ](https://msdn.microsoft.com/library/dn613268(v=vs.108).aspx) interface définit les propriétés qui le RoleManager tente d’appeler lors de l’exécution des opérations demandées. L’interface contient deux propriétés - Id et nom. Le [IRole&lt;TKey&gt; ](https://msdn.microsoft.com/library/dn613268(v=vs.108).aspx) interface vous permet de spécifier le type de la clé pour le rôle via le type générique **TKey** paramètre. Le type de la propriété Id correspond à la valeur du paramètre TKey.

L’infrastructure d’identité fournit également la [IRole](https://msdn.microsoft.com/library/microsoft.aspnet.identity.irole(v=vs.108).aspx) interface (sans le paramètre générique) lorsque vous souhaitez utiliser une valeur de chaîne pour la clé.

L’exemple suivant montre une classe IdentityRole qui utilise un entier pour la clé. Le champ Id a la valeur int corresponde à la valeur du paramètre générique. 

[!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample7.cs)]

 Pour une implémentation complète, consultez [IdentityRole (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/IdentityRole.cs). 

<a id="rolestore"></a>
## <a name="customize-the-role-store"></a>Personnaliser le magasin de rôles

Vous créer également une classe RoleStore qui fournit les méthodes pour toutes les opérations de données sur des rôles. Cette classe est équivalente à la [RoleStore&lt;TRole&gt; ](https://msdn.microsoft.com/library/dn468181(v=vs.108).aspx) classe dans l’espace de noms Microsoft.ASP.NET.Identity.EntityFramework. Dans votre classe RoleStore, vous implémentez le [IRoleStore&lt;TRole, TKey&gt; ](https://msdn.microsoft.com/library/dn613266(v=vs.108).aspx) et éventuellement le [IQueryableRoleStore&lt;TRole, TKey&gt; ](https://msdn.microsoft.com/library/dn613262(v=vs.108).aspx) interface.

![](overview-of-custom-storage-providers-for-aspnet-identity/_static/image6.png)

L’exemple suivant montre une classe de rôle du magasin. Le paramètre générique TRole prend le type de votre classe de rôle qui est généralement de la classe IdentityRole définie. Le paramètre générique TKey prend le type de clé de votre rôle. 

[!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample8.cs)]

- **IRoleStore&lt;TRole&gt;**  
 Le [IRoleStore](https://msdn.microsoft.com/library/dn468195.aspx) interface définit les méthodes à implémenter dans votre classe de rôle du magasin. Il contient des méthodes pour la création, la mise à jour, suppression et la récupération des rôles.
- **RoleStore&lt;TRole&gt;**  
 Pour personnaliser les RoleStore, créez une classe qui implémente l’interface IRoleStore. Il vous suffit de mettre en œuvre de cette classe si à utiliser les rôles sur votre système. Le constructeur qui prend un paramètre nommé *base de données* de type ExampleDatabase n'est qu’une illustration comment transmettre dans votre classe d’accès aux données. Par exemple, dans l’implémentation de MySQL, ce constructeur prend un paramètre de type MySQLDatabase.  
  
 Pour une implémentation complète, consultez [RoleStore (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/RoleStore.cs) .

<a id="reconfigure"></a>
## <a name="reconfigure-application-to-use-new-storage-provider"></a>Reconfigurer l’application pour utiliser le nouveau fournisseur de stockage

Vous avez implémenté votre nouveau fournisseur de stockage. Maintenant, vous devez configurer votre application pour utiliser ce fournisseur de stockage. Si le fournisseur de stockage par défaut a été inclus dans votre projet, vous devez supprimer le fournisseur par défaut et remplacez-la par votre fournisseur.

### <a name="replace-default-storage-provider-in-mvc-project"></a>Remplacez le fournisseur de stockage par défaut dans un projet MVC

1. Dans le **gérer les Packages NuGet** fenêtre, désinstallez le **Microsoft ASP.NET Identity EntityFramework** package. Vous pouvez trouver ce package en recherchant dans les packages installés Identity.EntityFramework.  
    ![](overview-of-custom-storage-providers-for-aspnet-identity/_static/image7.png)Vous demandera si vous voulez également désinstaller Entity Framework. Si vous ne devez pas dans d’autres parties de votre application, vous pouvez la désinstaller.
2. Dans le fichier IdentityModels.cs dans le dossier de modèles, supprimez ou commentez la **ApplicationUser** et **ApplicationDbContext** classes. Dans une application MVC, vous pouvez supprimer l’intégralité du fichier IdentityModels.cs. Dans une application Web Forms, supprimez les deux classes, mais veillez à que conserver la classe d’assistance qui se trouve également dans le fichier IdentityModels.cs.
3. Si votre fournisseur de stockage se trouve dans un projet distinct, ajoutez une référence à celle-ci dans votre application web.
4. Remplacez toutes les références à `using Microsoft.AspNet.Identity.EntityFramework;` avec un à l’aide de l’instruction pour l’espace de noms de votre fournisseur de stockage.
5. Dans le **Startup.Auth.cs** de classe, de modifier le **ConfigureAuth** méthode à utiliser une instance unique du contexte approprié. 

    [!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample9.cs?highlight=3)]
6. Dans l’application\_dossier Démarrage, ouvrez **IdentityConfig.cs**. Dans la classe ApplicationUserManager, modifiez le **créer** méthode pour retourner un gestionnaire de l’utilisateur qui utilise votre magasin de l’utilisateur personnalisés. 

    [!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample10.cs?highlight=3)]
7. Remplacez toutes les références à **ApplicationUser** avec **IdentityUser**.
8. Le projet par défaut inclut certains membres de classe d’utilisateur qui ne sont pas définis dans l’interface IUser ; par exemple par courrier électronique, PasswordHash et GenerateUserIdentityAsync. Si votre classe d’utilisateur ne dispose pas de ces membres, vous devez les implémenter ou modifier le code qui utilise ces membres.
9. Si vous avez créé toutes les instances de RoleManager, modifier le code pour utiliser votre nouvelle classe RoleStore.  

    [!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample11.cs)]
10. Le projet par défaut est conçu pour une classe d’utilisateur qui a une valeur de chaîne pour la clé. Si votre classe d’utilisateur a un type différent de la clé (par exemple, un nombre entier), vous devez modifier le projet avec votre type. Consultez [modifier une clé primaire pour les utilisateurs dans ASP.NET Identity](change-primary-key-for-users-in-aspnet-identity.md).
11. Si nécessaire, ajoutez la chaîne de connexion dans le fichier Web.config.

<a id="other"></a>
## <a name="other-resources"></a>Autres ressources

- Blog : [implémentation d’identité ASP.NET](http://odetocode.com/blogs/scott/archive/2014/01/20/implementing-asp-net-identity.aspx)
- Code de didacticiel et GIT : [Simple.Data le fournisseur d’identité Asp.Net](http://designcoderelease.blogspot.co.uk/2015/03/simpledata-aspnet-identity-provider.html)
- Didacticiel :[configurer les comptes d’identité base et les vers une base de données externe](http://typecastexception.com/post/2013/10/27/Configuring-Db-Connection-and-Code-First-Migration-for-Identity-Accounts-in-ASPNET-MVC-5-and-Visual-Studio-2013.aspx). Par [ @xivSolutions ](https://twitter.com/xivSolutions).
- Didacticiel[: implémentation d’un fournisseur de stockage d’identité ASP.NET MySQL personnalisé](implementing-a-custom-mysql-aspnet-identity-storage-provider.md)
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
