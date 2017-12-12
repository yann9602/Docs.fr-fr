---
uid: aspnet/overview/owin-and-katana/owin-oauth-20-authorization-server
title: "Serveur de d’autorisation OAuth 2.0 OWIN | Documents Microsoft"
author: hongyes
description: "Ce didacticiel vous guide sur l’implémentation d’un serveur d’autorisation OAuth 2.0 à l’aide d’intergiciel (middleware) OWIN OAuth. Ceci est un didacticiel avancé que seule Group..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/20/2014
ms.topic: article
ms.assetid: 20acee16-c70c-41e9-b38f-92bfcf9a4c1c
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/owin-and-katana/owin-oauth-20-authorization-server
msc.type: authoredcontent
ms.openlocfilehash: 8842f57df84d841df77b34e9645dbf4909f82d85
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="owin-oauth-20-authorization-server"></a>Serveur de d’autorisation OAuth 2.0 OWIN
====================
par [Hongye Sun](https://github.com/hongyes), [Praburaj Thiagarajan](https://github.com/Praburaj), [Rick Anderson](https://github.com/Rick-Anderson)

> Ce didacticiel vous guide sur l’implémentation d’un serveur d’autorisation OAuth 2.0 à l’aide d’intergiciel (middleware) OWIN OAuth. Il s’agit d’un didacticiel avancé qui ne présente les étapes pour créer un serveur d’autorisation de OWIN OAuth 2.0. Ce n’est pas un didacticiel pas à pas. [Télécharger l’exemple de code](https://code.msdn.microsoft.com/OWIN-OAuth-20-Authorization-ba2b8783/file/114932/1/AuthorizationServer.zip).
> 
> > [!NOTE]
> > Ce contour ne doit pas être destiné à être utilisée pour la création d’une application de production sécurisé. Ce didacticiel est destiné à fournir uniquement un plan sur l’implémentation d’un serveur d’autorisation OAuth 2.0 à l’aide d’intergiciel (middleware) OWIN OAuth.
> 
> 
> ## <a name="software-versions"></a>Versions du logiciel
> 
> | **Le didacticiel** | **Fonctionne également avec** |
> | --- | --- |
> | Windows 8.1 | Windows 8, Windows 7 |
> | [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads) | [Visual Studio 2013 Express pour Desktop](https://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express). Visual Studio 2012 avec la dernière mise à jour doit fonctionner, mais le didacticiel n’a pas été testé avec lui, et certaines boîtes de dialogue et les sélections de menu sont différentes. |
> | .NET 4.5 |  |
> 
> ## <a name="questions-and-comments"></a>Questions et des commentaires
> 
> Si vous avez des questions qui ne sont pas directement liées à ce didacticiel, vous pouvez les valider à [projet Katana sur GitHub](https://github.com/aspnet/AspNetKatana/). Pour des questions et des commentaires concernant le didacticiel lui-même, consultez la section commentaires au bas de la page.


Le [OAuth 2.0 framework](http://tools.ietf.org/html/rfc6749) permet à une application tierce obtenir un accès limité à un service HTTP. Au lieu d’utiliser les informations d’identification du propriétaire de la ressource pour accéder à une ressource protégée, le client obtient un jeton d’accès (qui est une chaîne indiquant une étendue spécifique, durée de vie et d’autres attributs d’accès). Les jetons d’accès sont émis vers des clients tiers par un serveur d’autorisation avec l’approbation du propriétaire de la ressource.

Ce didacticiel couvre :

- Comment créer un serveur d’autorisation pour prendre en charge d’autorisation quatre types d’accès et jetons d’actualisation :
    - Octroi d’un code d’autorisation
    - Implicit Grant
    - Informations d’identification de mot de passe de propriétaire de la ressource Grant
    - Octroi des informations d’identification client
- Création d’un serveur de ressources qui est protégé par un jeton d’accès.
- Création des clients OAuth 2.0.

<a id="prerequisites"></a>
## <a name="prerequisites"></a>Conditions préalables

- [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/downloads#d-2013-editions) ou gratuit [Visual Studio Express 2013](https://www.microsoft.com/visualstudio/eng/downloads#d-2013-express), comme indiqué dans **Versions logicielles** en haut de la page.
- Vous êtes familiarisé avec OWIN. Consultez [mise en route avec le projet Katana](https://msdn.microsoft.com/en-us/magazine/dn451439.aspx) et [Nouveautés OWIN et Katana](index.md).
- Vous êtes familiarisé avec [OAuth](http://tools.ietf.org/html/rfc6749) terminologie, y compris [rôles](http://tools.ietf.org/html/rfc6749#section-1.1), [flux du protocole](http://tools.ietf.org/html/rfc6749#section-1.2), et [octroi d’autorisation](http://tools.ietf.org/html/rfc6749#section-1.3). [Présentation de OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-1) constitue une bonne introduction.

## <a name="create-an-authorization-server"></a>Créer un serveur d’autorisation

Dans ce didacticiel, nous sera esquisse à peu près comment utiliser [OWIN](https://msdn.microsoft.com/en-us/magazine/dn451439.aspx) et ASP.NET MVC pour créer un serveur d’autorisation. Nous espérons bientôt fournir un téléchargement de l’exemple terminée, comme ce didacticiel n’inclut pas de chaque étape. Commencez par créer une application web vide nommée *AuthorizationServer* et installer les packages suivants :

- Microsoft.AspNet.Mvc
- Microsoft.Owin.Host.SystemWeb
- Microsoft.Owin.Security.OAuth
- Microsoft.Owin.Security.Cookies
- Microsoft.Owin.Security.Google (ou toute autre connexion sociale package tels que Microsoft.Owin.Security.Facebook)

Ajouter un [classe de démarrage OWIN](owin-startup-class-detection.md) sous le dossier racine du projet nommé *démarrage*.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample1.cs?highlight=8)]

Créer un *application\_Démarrer* dossier. Sélectionnez le *application\_Démarrer* dossier et utilisez Maj + Alt + A pour ajouter la version téléchargée de la *AuthorizationServer\App\_Start\Startup.Auth.cs* fichier.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample2.cs)]

Le code ci-dessus permet les cookies et l’authentification Google, qui sont utilisés par le serveur d’autorisation lui-même pour gérer les comptes de connexion/externe de l’application.

Le `UseOAuthAuthorizationServer` méthode d’extension est l’installation du serveur d’autorisation. Les options d’installation sont :

- `AuthorizeEndpointPath`: Le chemin d’accès de la demande où les applications clientes redirigeront l’agent utilisateur afin d’obtenir les utilisateurs de consentement pour émettre un jeton ou du code. Il doit commencer par une barre oblique, par exemple, «`/Authorize`».
- `TokenEndpointPath`: Les applications clientes de chemin d’accès demande communiquent directement pour obtenir le jeton d’accès. Il doit commencer par une barre oblique, par exemple « /Token ». Si le client est émis une [client\_secret](http://tools.ietf.org/html/rfc6749#appendix-A.2), il doit être fourni pour ce point de terminaison.
- `ApplicationCanDisplayErrors`: La valeur `true` si l’application web veut générer une page d’erreur personnalisée pour les erreurs de validation client de `/Authorize` point de terminaison. Cela est nécessaire uniquement pour les cas où le navigateur n’est pas redirigé vers l’application cliente, par exemple, lorsque le `client_id` ou `redirect_uri` sont incorrectes. Le `/Authorize` point de terminaison doit s’attendre à voir « oauth. Erreur «, « oauth. ErrorDescription » et « oauth. Propriétés de ErrorUri » sont ajoutées à l’environnement OWIN. 

    > [!NOTE]
    > Si ce n’est pas le cas, la valeur est true, le serveur d’autorisation retourne une page d’erreurs par défaut avec les détails de l’erreur.
- `AllowInsecureHttp`: True pour autoriser l’autoriser et demandes de jetons pour arriver sur des adresses URI HTTP et pour autoriser entrant `redirect_uri` autoriser les paramètres de la demande d’avoir des adresses URI HTTP. 

    > [!WARNING]
    > Sécurité - Il s’agit pour le développement.
- `Provider`: L’objet fourni par l’application pour traiter les événements déclenchés par l’intergiciel (middleware) serveur d’autorisation. L’application peut implémenter l’interface entièrement, ou il peut créer une instance de `OAuthAuthorizationServerProvider` et attribuer des délégués nécessaires pour les flux OAuth prend en charge par ce serveur.
- `AuthorizationCodeProvider`: Génère un code d’autorisation d’usage unique pour revenir à l’application cliente. Sécurisation de l’application pour le serveur OAuth soit **doit** fournir une instance de `AuthorizationCodeProvider` où le jeton produit par le `OnCreate/OnCreateAsync` événement est considéré comme valide pour un seul appel à `OnReceive/OnReceiveAsync`.
- `RefreshTokenProvider`: Génère un jeton d’actualisation qui peut être utilisé pour générer un nouveau jeton d’accès si nécessaire. Si non fourni le serveur d’autorisation ne retourne pas les jetons d’actualisation à partir de la `/Token` point de terminaison.

## <a name="account-management"></a>Gestion des comptes

OAuth ne tient pas compte où et comment gérer les informations de votre compte d’utilisateur. Il a [ASP.NET Identity](../../../identity/index.md) qui en est responsable. Dans ce didacticiel, nous simplifier le code de gestion de compte et assurez-vous que cet utilisateur peut ouvrir une session à l’aide de l’intergiciel OWIN cookie. Voici le code exemple simplifié pour la `AccountController`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample3.cs)]

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample4.cs?highlight=1)]

`ValidateClientRedirectUri`est utilisé pour valider le client avec son URL de redirection inscrite. `ValidateClientAuthentication`vérifie l’en-tête du schéma de base et le corps de formulaire pour obtenir des informations d’identification du client.

La page de connexion est indiquée ci-dessous :

![](owin-oauth-20-authorization-server/_static/image1.png)

Passez en revue OAuth 2 de l’IETF [octroi d’un Code d’autorisation](http://tools.ietf.org/html/rfc6749#section-4.1) section maintenant. 

**Fournisseur** (dans le tableau ci-dessous) est [OAuthAuthorizationServerOptions](https://msdn.microsoft.com/en-us/library/microsoft.owin.security.oauth.oauthauthorizationserveroptions(v=vs.111).aspx). Fournisseur, qui est de type `OAuthAuthorizationServerProvider`, qui contient tous les événements de serveur OAuth. 

| Étapes du flux à partir de la section d’octroi d’un Code d’autorisation | Téléchargement de l’exemple effectue ces étapes avec : |
| --- | --- |
|  |  |
| (A) le client lance le flux en dirigeant l’agent utilisateur du propriétaire de la ressource pour le point de terminaison d’autorisation. Le client inclut son identificateur de client, étendue demandée, état local et un URI de redirection à laquelle le serveur d’autorisation envoie à l’agent de l’utilisateur une fois que l’accès est accordé (ou refusé). | Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint |
|  |  |
| (B) le serveur d’autorisation authentifie le propriétaire de la ressource (via l’agent utilisateur) et établit si le propriétaire de la ressource autorise ou refuse la demande d’accès du client. | **&lt;Si l’utilisateur accorde l’accès&gt;**  Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint AuthorizationCodeProvider.CreateAsync |
|  |  |
| (C) en supposant que le propriétaire de la ressource accorde l’accès, le serveur d’autorisation redirige l’agent utilisateur vers le client à l’aide de la redirection de l’URI fourni plus haut (dans la demande ou lors de l’inscription du client). ... |  |
|  |  |
| (D), le client demande un jeton d’accès à partir du point de terminaison de jeton du serveur d’autorisation en incluant le code d’autorisation à l’étape précédente. Lors de la demande, le client s’authentifie auprès du serveur d’autorisation. Le client inclut l’URI utilisé pour obtenir le code d’autorisation pour la vérification de redirection. | Provider.MatchEndpoint Provider.ValidateClientAuthentication AuthorizationCodeProvider.ReceiveAsync Provider.ValidateTokenRequest Provider.GrantAuthorizationCode Provider.TokenEndpoint AccessTokenProvider.CreateAsync RefreshTokenProvider.CreateAsync |

Un exemple d’implémentation pour `AuthorizationCodeProvider.CreateAsync` et `ReceiveAsync` pour contrôler la création et la validation du code d’autorisation est indiqué ci-dessous.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample5.cs)]

Le code ci-dessus utilise un dictionnaire simultané en mémoire stockage du ticket de code et de l’identité et la restauration de l’identité après avoir reçu le code. Dans une application réelle, il serait remplacé par un magasin de données persistantes. Il est le point de terminaison d’autorisation du propriétaire des ressources accorder l’accès au client. En règle générale, il a besoin d’une interface utilisateur pour autoriser l’utilisateur à cliquer sur un bouton et confirmer l’allocation. Intergiciel (middleware) OWIN OAuth permet au code d’application gérer le point de terminaison d’autorisation. Dans notre exemple d’application, nous utilisons un contrôleur MVC appelé `OAuthController` pour le manipuler. Voici un exemple d’implémentation :

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample6.cs?highlight=15)]

Le `Authorize` action vérifie d’abord si l’utilisateur a ouvert une session le serveur d’autorisation. Si ce n’est pas le cas, l’intergiciel (middleware) d’authentification vérifie l’appelant de s’authentifier en utilisant le cookie « Application » et redirige vers la page de connexion. (Voir le code en surbrillance ci-dessus). Si l’utilisateur s’est connecté, il doit rendre la vue d’autoriser, comme indiqué ci-dessous :

![](owin-oauth-20-authorization-server/_static/image2.png)

Si le **Grant** bouton est sélectionné, le `Authorize` action va créer un nouveau identité de « Support » et connectez-vous avec elle. Il déclenche le serveur d’autorisation pour générer un jeton de support et l’envoyer au client avec la charge utile JSON. 

### <a name="implicit-grant"></a>Implicit Grant

Faire OAuth 2 de l’IETF [Implicit Grant](http://tools.ietf.org/html/rfc6749#section-4.2) section maintenant.

 Le [Implicit Grant](http://tools.ietf.org/html/rfc6749#section-4.2) flux indiqué dans la Figure 4 est le flux et OAuth OWIN de mappage qui le suit intergiciel (middleware).  

| Étapes du flux à partir de la section de Implicit Grant | Téléchargement de l’exemple effectue ces étapes avec : |
| --- | --- |
|  |  |
| (A) le client lance le flux en dirigeant l’agent utilisateur du propriétaire de la ressource pour le point de terminaison d’autorisation. Le client inclut son identificateur de client, étendue demandée, état local et un URI de redirection à laquelle le serveur d’autorisation envoie à l’agent de l’utilisateur une fois que l’accès est accordé (ou refusé). | Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint |
|  |  |
| (B) le serveur d’autorisation authentifie le propriétaire de la ressource (via l’agent utilisateur) et établit si le propriétaire de la ressource autorise ou refuse la demande d’accès du client. | **&lt;Si l’utilisateur accorde l’accès&gt;**  Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint AuthorizationCodeProvider.CreateAsync |
|  |  |
| (C) en supposant que le propriétaire de la ressource accorde l’accès, le serveur d’autorisation redirige l’agent utilisateur vers le client à l’aide de la redirection de l’URI fourni plus haut (dans la demande ou lors de l’inscription du client). ... |  |
|  |  |
| (D), le client demande un jeton d’accès à partir du point de terminaison de jeton du serveur d’autorisation en incluant le code d’autorisation à l’étape précédente. Lors de la demande, le client s’authentifie auprès du serveur d’autorisation. Le client inclut l’URI utilisé pour obtenir le code d’autorisation pour la vérification de redirection. |  |

Étant donné que nous avons déjà implémenté le point de terminaison d’autorisation (`OAuthController.Authorize` action) pour l’octroi d’un code d’autorisation, il active automatiquement également les flux implicites. Remarque : `Provider.ValidateClientRedirectUri` est utilisé pour valider l’ID de client avec son URL de redirection, qui protège le flux d’octroi implicite d’envoyer l’accès au jeton à des clients ([attaque de Man-in-the-middle](https://www.owasp.org/index.php/Man-in-the-middle_attack)).

### <a name="resource-owner-password-credentials-grant"></a>Informations d’identification de mot de passe de propriétaire de la ressource Grant

Faire OAuth 2 de l’IETF [octroi d’informations d’identification de mot de passe de propriétaire de ressource](http://tools.ietf.org/html/rfc6749#section-4.3) section maintenant.

 Le [octroi d’informations d’identification de mot de passe de propriétaire de ressource](http://tools.ietf.org/html/rfc6749#section-4.3) flux indiqué dans la Figure 5 est le flux et OAuth OWIN de mappage qui le suit intergiciel (middleware).  

| Étapes du flux à partir de la section de ressources propriétaire du mot de passe informations d’identification Grant | Téléchargement de l’exemple effectue ces étapes avec : |
| --- | --- |
|  |  |
| Le propriétaire de la ressource fournit au client avec son nom d’utilisateur et un mot de passe. |  |
|  |  |
| (B) le client demande un jeton d’accès à partir du point de terminaison de jeton du serveur d’autorisation en incluant les informations d’identification provenance du propriétaire de la ressource. Lors de la demande, le client s’authentifie auprès du serveur d’autorisation. | Provider.MatchEndpoint Provider.ValidateClientAuthentication Provider.ValidateTokenRequest Provider.GrantResourceOwnerCredentials Provider.TokenEndpoint AccessToken Provider.CreateAsync RefreshTokenProvider.CreateAsync |
|  |  |
| (C) le serveur d’autorisation authentifie le client et valide les informations d’identification du propriétaire de la ressource et s’il est valide, émet un jeton d’accès. |  |

Voici l’exemple d’implémentation pour `Provider.GrantResourceOwnerCredentials`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample7.cs)]

> [!NOTE]
> Le code ci-dessus est destiné à présenter cette section du didacticiel et ne doit pas être utilisé dans la sécurité ou les applications de production. Il ne vérifie pas les informations d’identification des propriétaires de ressources. Il suppose que chaque information d’identification n’est valide et qu’il crée une nouvelle identité pour celle-ci. La nouvelle identité sera utilisée pour générer le jeton d’accès et le jeton d’actualisation. Remplacez le code par votre propre code de gestion de compte sécurisé.


### <a name="client-credentials-grant"></a>Octroi des informations d’identification client

Faire OAuth 2 de l’IETF [octroi des informations d’identification du Client](http://tools.ietf.org/html/rfc6749#section-4.4) section maintenant.

 Le [octroi des informations d’identification du Client](http://tools.ietf.org/html/rfc6749#section-4.4) flux indiqué dans la Figure 6 est le flux et OAuth OWIN de mappage qui le suit intergiciel (middleware).  

| Étapes du flux à partir de la section d’octroi des informations d’identification du Client | Téléchargement de l’exemple effectue ces étapes avec : |
| --- | --- |
|  |  |
| (A) le client s’authentifie auprès du serveur d’autorisation et demande un jeton d’accès du point de terminaison de jeton. | Provider.MatchEndpoint Provider.ValidateClientAuthentication Provider.ValidateTokenRequest Provider.GrantClientCredentials Provider.TokenEndpoint AccessTokenProvider.CreateAsync RefreshTokenProvider.CreateAsync |
|  |  |
| (B) le serveur d’autorisation authentifie le client et s’il est valide, émet un jeton d’accès. |  |

Voici l’exemple d’implémentation pour `Provider.GrantClientCredentials`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample8.cs)]

> [!NOTE]
> Le code ci-dessus est destiné à présenter cette section du didacticiel et ne doit pas être utilisé dans la sécurité ou les applications de production. Remplacez le code par votre propre code de gestion de client sécurisé.


### <a name="refresh-token"></a>Jeton d’actualisation

Faire OAuth 2 de l’IETF [jeton d’actualisation](http://tools.ietf.org/html/rfc6749#section-1.5) section maintenant.

 Le [jeton d’actualisation](http://tools.ietf.org/html/rfc6749#section-1.5) flux indiqué dans la Figure 2 est le flux et OAuth OWIN de mappage qui le suit intergiciel (middleware).  

| Étapes du flux à partir de la section d’octroi des informations d’identification du Client | Téléchargement de l’exemple effectue ces étapes avec : |
| --- | --- |
|  |  |
| (G), le client demande un nouveau jeton d’accès par authentification avec le serveur d’autorisation et de présenter le jeton d’actualisation. Les conditions d’authentification client sont basées sur le type de client et sur les stratégies de serveur d’autorisation. | Provider.MatchEndpoint Provider.ValidateClientAuthentication RefreshTokenProvider.ReceiveAsync Provider.ValidateTokenRequest Provider.GrantRefreshToken Provider.TokenEndpoint AccessTokenProvider.CreateAsync RefreshTokenProvider.CreateAsync |
|  |  |
| (H) du serveur d’autorisation authentifie le client valide le jeton d’actualisation et s’il est valide, émet un jeton d’accès (et, éventuellement, un nouveau jeton d’actualisation). |  |

Voici l’exemple d’implémentation pour `Provider.GrantRefreshToken`: 

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample9.cs)]

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample10.cs)]

## <a name="create-a-resource-server-which-is-protected-by-access-token"></a>Créer un serveur de ressources qui est protégé par le jeton d’accès

Créer un projet d’application web vide et installez suivants des packages dans le projet :

- Microsoft.AspNet.WebApi.Owin
- Microsoft.Owin.Host.SystemWeb
- Microsoft.Owin.Security.OAuth

Créer une classe de démarrage et de configurer l’authentification et l’API Web. Consultez *AuthorizationServer\ResourceServer\Startup.cs* dans le téléchargement de l’exemple.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample11.cs)]

Consultez *AuthorizationServer\ResourceServer\App\_Start\Startup.Auth.cs* dans le téléchargement de l’exemple.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample12.cs)]

Consultez *AuthorizationServer\ResourceServer\App\_Start\Startup.WebApi.cs* dans le téléchargement de l’exemple.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample13.cs)]

- `UseCors`méthode autorise les CORS pour tous les domaines.
- `UseOAuthBearerAuthentication`méthode permet d’intergiciel d’authentification du jeton de support OAuth qui recevra et valider le jeton de support à partir de l’en-tête d’autorisation dans la demande.
- `Config.SuppressDefaultHostAuthenticaiton`Supprime la valeur par défaut hôte principal authentifié à partir de l’application, par conséquent, toutes les demandes seront anonymes après cet appel.
- `HostAuthenticationFilter`Active l’authentification pour le type d’authentification. Dans ce cas, il est de type d’authentification de support.

Pour illustrer l’identité authentifiée, nous créons un ApiController pour générer des revendications de l’utilisateur actuel.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample14.cs)]

Si le serveur d’autorisation et le serveur de ressources ne sont pas sur le même ordinateur, l’intergiciel (middleware) OAuth utilise les clés d’ordinateur différents pour chiffrer et déchiffrer le jeton d’accès de support. Afin de partager la même clé privée entre les deux projets, nous ajoutons la même `machinekey` paramètre dans les deux *web.config* fichiers.

[!code-xml[Main](owin-oauth-20-authorization-server/samples/sample15.xml?highlight=8-10)]

## <a name="create-oauth-20-clients"></a>Créer des Clients OAuth 2.0

 Nous utilisons le [DotNetOpenAuth.OAuth2.Client](http://www.nuget.org/packages/DotNetOpenAuth.OAuth2.Client) package NuGet pour simplifier le code client.

### <a name="authorization-code-grant-client"></a>Client d’octroi de Code d’autorisation

 Ce client est une application MVC. Il déclenche un flux d’octroi de code d’autorisation pour obtenir l’accès au jeton à partir du serveur principal. Elle comporte une seule page comme indiqué ci-dessous :

![](owin-oauth-20-authorization-server/_static/image3.png)

- Le **Authorize** bouton redirige navigateur vers le serveur d’autorisation pour notifier le propriétaire de la ressource à accorder l’accès à ce client.
- Le **Actualiser** bouton obtenez un nouveau jeton d’accès et un jeton d’actualisation à l’aide de l’actualisation en cours jeton.
- Le **accès protégé ressource API** bouton appellera le serveur de ressources pour obtenir des données de revendications de l’utilisateur actuel et les afficher sur la page.

Voici l’exemple de code de la `HomeController` du client.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample16.cs)]

`DotNetOpenAuth`nécessite SSL par défaut. Étant donné que notre démonstration à l’aide de HTTP, vous devez ajouter suivant dans le fichier de configuration :

[!code-xml[Main](owin-oauth-20-authorization-server/samples/sample17.xml?highlight=4-6)]

> [!WARNING]
> Sécurité - jamais désactiver SSL dans une application de production. Vos informations d’identification de connexion sont désormais envoyées en texte clair sur le réseau. Le code ci-dessus est juste pour le débogage local exemple et l’exploration.


### <a name="implicit-grant-client"></a>Client d’octroi implicite

Ce client utilise JavaScript pour :

1. Ouvrez une nouvelle fenêtre et rediriger vers le point de terminaison authorize du serveur d’autorisation.
2. Obtenir le jeton d’accès à partir des fragments d’URL lorsqu’il redirige précédent.

L’illustration suivante montre ce processus :

![](owin-oauth-20-authorization-server/_static/image4.png)

Le client doit avoir deux pages : une pour la page d’accueil et l’autre pour le rappel. Voici l’exemple de code JavaScript de code se trouve dans le *Index.cshtml* fichier :

[!code-cshtml[Main](owin-oauth-20-authorization-server/samples/sample18.cshtml)]

Voici le rappel de la gestion du code dans *SignIn.cshtml* fichier :

[!code-cshtml[Main](owin-oauth-20-authorization-server/samples/sample19.cshtml)]

> [!NOTE]
> Une meilleure pratique consiste à déplacer le code JavaScript dans un fichier externe et incorporer pas avec le balisage Razor. Pour que cet exemple reste simple, qu’elles ont été combinées.


### <a name="resource-owner-password-credentials-grant-client"></a>Mot de passe de propriétaire de la ressource d’informations d’identification de Client de Grant

Nous utilise une application console à la démonstration de ce client. Voici le code :

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample20.cs)]

### <a name="client-credentials-grant-client"></a>Client de l’octroi des informations d’identification client

Comme pour l’allocation de ressources propriétaire du mot de passe informations d’identification, code d’application console est ici :

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample21.cs)]
