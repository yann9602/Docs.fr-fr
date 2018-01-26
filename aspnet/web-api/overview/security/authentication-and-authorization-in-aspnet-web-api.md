---
uid: web-api/overview/security/authentication-and-authorization-in-aspnet-web-api
title: Authentification et autorisation dans ASP.NET Web API | Documents Microsoft
author: MikeWasson
description: "Donne une vue d’ensemble de l’authentification et d’autorisation dans l’API Web ASP.NET."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/27/2012
ms.topic: article
ms.assetid: 6dfb51ea-9f4d-4e70-916c-8ef8344a88d6
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/security/authentication-and-authorization-in-aspnet-web-api
msc.type: authoredcontent
ms.openlocfilehash: 2a4b5ed8a712b061b4afdf5a3adc9378dd72b37f
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="authentication-and-authorization-in-aspnet-web-api"></a>Authentification et autorisation dans l’API Web ASP.NET
====================
par [Mike Wasson](https://github.com/MikeWasson)

Vous avez créé une API web, mais maintenant que vous souhaitez contrôler l’accès. Dans cette série d’articles, nous allons examiner certaines options pour la sécurisation d’une API web des utilisateurs non autorisés. Cette série couvre l’authentification et autorisation.

- *Authentification* est de connaître l’identité de l’utilisateur. Par exemple, Alice se connecte avec son nom d’utilisateur et un mot de passe et le serveur utilise le mot de passe pour authentifier d’Alice.
- *Autorisation* consiste à déterminer si un utilisateur est autorisé à effectuer une action. Par exemple, Alice a l’autorisation d’obtenir une ressource mais pas créer une ressource.

Le premier article de la série donne une vue d’ensemble de l’authentification et d’autorisation dans l’API Web ASP.NET. Autres rubriques décrivent les scénarios d’authentification courants pour l’API Web.

> [!NOTE]
> Grâce aux personnes consulté cette série et fournissaient des commentaires précieux à Microsoft : Rick Anderson, Levi Broderick, Barry Dorrans, Tom Dykstra, Hongmei Ge, David Matson, Daniel Roth, Tim Teebken.


## <a name="authentication"></a>Authentification

API Web part du principe que l’authentification se produit dans l’hôte. Pour l’hébergement web, IIS utilise des modules HTTP pour l’authentification est l’ordinateur hôte. Vous pouvez configurer votre projet pour utiliser un des modules d’authentification intégrées à IIS ou ASP.NET, ou écrire votre propre module HTTP pour effectuer une authentification personnalisée.

Lorsque l’hôte authentifie l’utilisateur, il crée un *principal*, qui est un [IPrincipal](https://msdn.microsoft.com/library/System.Security.Principal.IPrincipal.aspx) objet qui représente le contexte de sécurité sous lequel le code est en cours d’exécution. L’hôte attache le principal pour le thread actuel en définissant **Thread.CurrentPrincipal**. Le principal contient associé à un **identité** objet qui contient des informations sur l’utilisateur. Si l’utilisateur est authentifié, le **Identity.IsAuthenticated** propriété renvoie **true**. Pour les demandes anonymes, **IsAuthenticated** retourne **false**. Pour plus d’informations sur les principaux, consultez [en fonction du rôle de sécurité](https://msdn.microsoft.com/library/shz8h065.aspx).

### <a name="http-message-handlers-for-authentication"></a>Gestionnaires de messages HTTP pour l’authentification

Au lieu d’utiliser l’hôte pour l’authentification, vous pouvez placer la logique d’authentification dans un [Gestionnaire de messages HTTP](../advanced/http-message-handlers.md). Dans ce cas, le Gestionnaire de messages examine la requête HTTP et définit le principal.

Quand faut-il utiliser des gestionnaires de messages pour l’authentification ? Voici quelques compromis :

- Un module HTTP voit toutes les demandes qui passent par le pipeline ASP.NET. Un gestionnaire de messages ne voit que les demandes sont routées vers API Web.
- Vous pouvez définir des gestionnaires de messages par l’itinéraire, qui vous permet d’appliquer un schéma d’authentification à un itinéraire spécifique.
- Les modules HTTP sont spécifiques à IIS. Gestionnaires de messages sont indépendantes des hôtes pour qu’ils puissent être utilisées avec l’hébergement de sites web et d’auto-hébergement.
- Modules HTTP participent journalisation IIS, l’audit et ainsi de suite.
- Modules HTTP s’exécuter plus tôt dans le pipeline. Si vous gérez l’authentification dans un gestionnaire de messages, le principal pas obtenir la valeur jusqu'à ce que le gestionnaire s’exécute. En outre, le principal rétablit le principal précédent lorsque le Gestionnaire de messages s’écarte de la réponse.

En règle générale, si vous n’avez pas besoin de prendre en charge l’hébergement autonome, un module HTTP est une meilleure option. Si vous avez besoin prendre en charge l’auto-hébergement, envisagez un gestionnaire de messages.

### <a name="setting-the-principal"></a>Définition du Principal

Si votre application effectue une logique d’authentification personnalisée, vous devez définir le principal sur deux emplacements :

- **Thread.CurrentPrincipal**. Cette propriété est le moyen standard de définir le principal du thread dans .NET.
- **HttpContext.Current.User**. Cette propriété est spécifique à ASP.NET.

Le code suivant montre comment définir le principal :

[!code-csharp[Main](authentication-and-authorization-in-aspnet-web-api/samples/sample1.cs)]

Pour l’hébergement web, vous devez définir l’entité de sécurité dans les deux emplacements ; dans le cas contraire, le contexte de sécurité peut-être devenir incohérent. Pour l’auto-hébergement, toutefois, **HttpContext.Current** a la valeur null. Pour garantir que votre code est indifférent à l’hôte, par conséquent, rechercher les valeurs null avant d’assigner à **HttpContext.Current**, comme indiqué.

## <a name="authorization"></a>Autorisation

L’autorisation se produit ultérieurement dans le pipeline, vers le contrôleur. Qui vous permet de faire des choix plus précis lorsque vous accordez l’accès aux ressources.

- *Filtres d’autorisation* exécuter avant l’action du contrôleur. Si la demande n’est pas autorisée, le filtre retourne une réponse d’erreur et l’action n’est pas appelée.
- Au sein d’une action de contrôleur, vous pouvez obtenir le principal actuel à partir de la **ApiController.User** propriété. Par exemple, vous pouvez filtrer une liste de ressources en fonction du nom d’utilisateur, en retournant uniquement les ressources qui appartiennent à cet utilisateur.

![](authentication-and-authorization-in-aspnet-web-api/_static/image1.png)

<a id="auth3"></a>
### <a name="using-the-authorize-attribute"></a>À l’aide de la [autoriser] attribut

API Web fournit un filtre d’autorisation intégré, [AuthorizeAttribute](https://msdn.microsoft.com/library/system.web.http.authorizeattribute.aspx). Ce filtre vérifie si l’utilisateur est authentifié. Si ce n’est pas le cas, elle retourne le code d’état HTTP 401 (non autorisé), sans appeler l’action.

Vous pouvez appliquer le filtre globalement, au niveau du contrôleur ou au niveau des actions d’inidivual.

**Global**: pour limiter l’accès pour chaque contrôleur d’API Web, ajoutez le **AuthorizeAttribute** filtre à la liste de filtres globaux :

[!code-csharp[Main](authentication-and-authorization-in-aspnet-web-api/samples/sample2.cs)]

**Contrôleur**: pour restreindre l’accès pour un contrôleur spécifique, ajoutez le filtre en tant qu’attribut au contrôleur :

[!code-csharp[Main](authentication-and-authorization-in-aspnet-web-api/samples/sample3.cs)]

**Action**: pour restreindre l’accès pour des actions spécifiques, ajoutez l’attribut à la méthode d’action :

[!code-csharp[Main](authentication-and-authorization-in-aspnet-web-api/samples/sample4.cs)]

Ou bien, vous pouvez limiter le contrôleur et ensuite d’autoriser l’accès anonyme à des actions spécifiques, à l’aide de la `[AllowAnonymous]` attribut. Dans l’exemple suivant, la `Post` méthode est limitée, mais la `Get` méthode autorise l’accès anonyme.

[!code-csharp[Main](authentication-and-authorization-in-aspnet-web-api/samples/sample5.cs)]

Dans les exemples précédents, le filtre permet à tout utilisateur authentifié accéder aux méthodes restreintes ; Seuls les utilisateurs anonymes sont conservés. Vous pouvez également limiter l’accès à des utilisateurs spécifiques ou aux utilisateurs dans des rôles spécifiques :

[!code-csharp[Main](authentication-and-authorization-in-aspnet-web-api/samples/sample6.cs)]

> [!NOTE]
> Le **AuthorizeAttribute** filtre pour les contrôleurs de l’API Web se trouve dans le **System.Web.Http** espace de noms. Il existe un filtre similaire pour les contrôleurs MVC dans le **System.Web.Mvc** espace de noms qui n’est pas compatible avec les contrôleurs de l’API Web.


### <a name="custom-authorization-filters"></a>Filtres d’autorisation personnalisée

Pour écrire un filtre d’autorisation personnalisée, dérivez de l’une de ces types :

- **AuthorizeAttribute**. Étendez cette classe pour exécuter la logique d’autorisation en fonction de l’utilisateur actuel et les rôles de l’utilisateur.
- **AuthorizationFilterAttribute**. Étendez cette classe pour exécuter la logique d’autorisation synchrone qui n’est pas nécessairement basée sur l’utilisateur actuel ou le rôle.
- **IAuthorizationFilter**. Implémentez cette interface pour exécuter une logique d’autorisation asynchrone ; par exemple, si votre logique d’autorisation effectue des appels réseau ou d’e/s asynchrones. (Si votre logique d’autorisation est lié à l’UC, il est plus simple dériver à partir de **AuthorizationFilterAttribute**, car vous n’avez pas à écrire une méthode asynchrone.)

Le diagramme suivant illustre la hiérarchie de classes pour la **AuthorizeAttribute** classe.

![](authentication-and-authorization-in-aspnet-web-api/_static/image2.png)

### <a name="authorization-inside-a-controller-action"></a>Autorisation à l’intérieur d’une Action de contrôleur

Dans certains cas, vous pouvez autoriser une demande pour continuer, mais modifier le comportement en fonction de l’entité de sécurité. Par exemple, les informations que vous retournez peuvent changer en fonction du rôle de l’utilisateur. Au sein d’une méthode de contrôleur, vous pouvez obtenir le principe actuel à partir de la **ApiController.User** propriété.

[!code-csharp[Main](authentication-and-authorization-in-aspnet-web-api/samples/sample7.cs)]
