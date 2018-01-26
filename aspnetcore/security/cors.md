---
title: "L’activation de demandes de Cross-Origin (CORS)"
author: rick-anderson
description: "Ce document présente les CORS comme une norme pour autoriser ou rejeter les demandes cross-origin dans une application ASP.NET Core."
ms.author: riande
manager: wpickett
ms.date: 05/17/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/cors
ms.openlocfilehash: 9f53ce11f1659aa3416fe4fbb94183c64ab0dab5
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="enabling-cross-origin-requests-cors"></a>L’activation de demandes de Cross-Origin (CORS)

Par [Mike Wasson](https://github.com/mikewasson), [Shayne Boyer](https://twitter.com/spboyer), et [Tom Dykstra](https://github.com/tdykstra)

Sécurité du navigateur empêche une page web d’effectuer des demandes AJAX vers un autre domaine. Cette restriction est appelée le *stratégie de même origine*et un site malveillant empêche de lire des données sensibles à partir d’un autre site. Toutefois, vous pouvez parfois permettent aux autres sites demandes cross-origin à votre API web.

[Cross-origine partage des ressources](http://www.w3.org/TR/cors/) (CORS) est une norme W3C qui permet à un serveur d’abaisser la stratégie de même origine. À l’aide de CORS, un serveur peut autoriser explicitement les certaines demandes cross-origin lors du refus d’autres. CORS est plus sûre et plus flexible qu’antérieures techniques telles que [JSONP](https://wikipedia.org/wiki/JSONP). Cette rubrique montre comment activer les CORS dans une application ASP.NET Core.

## <a name="what-is-same-origin"></a>Quelle est la « même origine » ?

Deux URL ayant la même origine, s’ils ont des ports, des hôtes et des schémas identiques. ([RFC 6454](http://tools.ietf.org/html/rfc6454))

Ces deux URL ayant la même origine :

* `http://example.com/foo.html`

* `http://example.com/bar.html`

Ces URL ont des origines différentes à la précédente deux :

* `http://example.net`-Autre domaine

* `http://www.example.com/foo.html`-Autre sous-domaine

* `https://example.com/foo.html`-Autre schéma

* `http://example.com:9000/foo.html`-Autre port

> [!NOTE]
> Internet Explorer ne considère pas le port lors de la comparaison des origines.

## <a name="setting-up-cors"></a>Configuration des règles CORS

Pour configurer des règles CORS pour votre application Ajouter le `Microsoft.AspNetCore.Cors` package pour votre projet.

Ajouter les services CORS dans Startup.cs :

[!code-csharp[Main](cors/sample/CorsExample1/Startup.cs?name=snippet_addcors)]

## <a name="enabling-cors-with-middleware"></a>L’activation de CORS avec intergiciel (middleware)

Pour activer CORS pour votre application complète renforcent l’intergiciel (middleware) CORS votre pipeline de demande à l’aide de la `UseCors` méthode d’extension. Notez que l’intergiciel (middleware) CORS doit précéder les points de terminaison définis dans votre application que vous souhaitez prendre en charge les demandes cross-origin (par ex. avant tout appel à `UseMvc`).

Vous pouvez spécifier une stratégie de cross-origine lors de l’ajout de l’intergiciel (middleware) CORS à l’aide la `CorsPolicyBuilder` classe. Il existe deux manières de procéder. La première consiste à appeler UseCors avec une expression lambda :

[!code-csharp[Main](cors/sample/CorsExample1/Startup.cs?highlight=11,12&range=22-38)]

**Remarque :** l’URL doit être spécifiée sans barre oblique de fin (`/`). Si l’URL se termine avec `/`, la comparaison retournera `false` et aucun en-tête n’est renvoyé.

L’expression lambda prend un `CorsPolicyBuilder` objet. Vous trouverez une liste de la [les options de configuration](#cors-policy-options) plus loin dans cette rubrique. Dans cet exemple, la stratégie autorise les demandes cross-origin de `http://example.com` et aucune autre origine.

Notez que CorsPolicyBuilder possède une API fluent, donc vous pouvez chaîner des appels de méthode :

[!code-csharp[Main](../security/cors/sample/CorsExample3/Startup.cs?highlight=3&range=29-32)]

La deuxième approche consiste à définir une ou plusieurs stratégies CORS nommées, puis sélectionnez la stratégie par nom au moment de l’exécution.

[!code-csharp[Main](cors/sample/CorsExample2/Startup.cs?name=snippet_begin)]

Cet exemple ajoute une stratégie CORS nommée « AllowSpecificOrigin ». Pour sélectionner la stratégie, passez le nom à `UseCors`.

## <a name="enabling-cors-in-mvc"></a>L’activation de CORS dans MVC

Vous pouvez également utiliser MVC pour appliquer les CORS spécifiques par action, par contrôleur, ou pour tous les contrôleurs. Lors de l’utilisation de MVC activer CORS les mêmes services CORS sont utilisés, mais n’est pas de l’intergiciel (middleware) CORS.

### <a name="per-action"></a>Par action

Pour spécifier une stratégie CORS pour une action spécifique ajouter la `[EnableCors]` d’attribut à l’action. Spécifiez le nom de la stratégie.

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnAction)]

### <a name="per-controller"></a>Par contrôleur

Pour spécifier la stratégie CORS pour un contrôleur spécifique ajouter la `[EnableCors]` d’attribut à la classe de contrôleur. Spécifiez le nom de la stratégie.

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnController)]

### <a name="globally"></a>Global

Vous pouvez activer CORS globalement pour tous les contrôleurs en ajoutant le `CorsAuthorizationFilterFactory` filtre à la collection de filtres globaux :

[!code-csharp[Main](cors/sample/CorsMVC/Startup2.cs?name=snippet_configureservices)]

Est de l’ordre de priorité : Action de contrôleur, global. Stratégies au niveau de l’action sont prioritaires sur les stratégies au niveau du contrôleur et les stratégies au niveau du contrôleur sont prioritaires sur les stratégies globales.

### <a name="disable-cors"></a>Désactiver les CORS

Pour désactiver les CORS pour un contrôleur ou d’action, utilisez la `[DisableCors]` attribut.

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=DisableOnAction)]

## <a name="cors-policy-options"></a>Options de stratégie CORS

Cette section décrit les différentes options que vous pouvez définir dans une stratégie CORS.

* [Définir les origines autorisées](#set-the-allowed-origins)

* [Définir les méthodes HTTP autorisées](#set-the-allowed-http-methods)

* [Définir les en-têtes de requête autorisée](#set-the-allowed-request-headers)

* [Définir les en-têtes de réponse exposé](#set-the-exposed-response-headers)

* [Informations d’identification dans les demandes cross-origin](#credentials-in-cross-origin-requests)

* [Définir le délai d’expiration en amont](#set-the-preflight-expiration-time)

Pour certaines options, il peut être utile de lire [fonctionne de la façon dont les CORS](#how-cors-works) première.

### <a name="set-the-allowed-origins"></a>Définir les origines autorisées

Pour autoriser un ou plusieurs origines spécifiques :

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=19-23)]

Pour autoriser toutes les origines :

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs??range=27-31)]

Réfléchissez bien avant d’autoriser des demandes à partir de n’importe quel origine. Cela signifie que tout site Web peut effectuer des appels d’AJAX à votre API.

### <a name="set-the-allowed-http-methods"></a>Définir les méthodes HTTP autorisées

Pour autoriser toutes les méthodes HTTP :

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=44-49)]

Cela affecte les demandes de contrôle préliminaire et l’en-tête de l’accès en contrôle-Allow-Methods.

### <a name="set-the-allowed-request-headers"></a>Définir les en-têtes de requête autorisée

Une demande préliminaire CORS peut inclure un en-tête Access-Control-Request-Headers, qui répertorie les en-têtes HTTP définis par l’application (appelé « author les en-têtes de demande »).

À des en-têtes d’autorisation spécifiques :

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=53-58)]

Pour autoriser tous les créer des en-têtes de demande :

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=62-67)]

Navigateurs ne sont pas entièrement cohérents dans leur définition Access-Control-Request-Headers. Si vous définissez les en-têtes sur tout élément autre que « * », vous devez inclure au moins « accepter », « content-type » et « origine », ainsi que tous les en-têtes personnalisés que vous souhaitez prendre en charge.

### <a name="set-the-exposed-response-headers"></a>Définir les en-têtes de réponse exposé

Par défaut, le navigateur n’expose pas tous les en-têtes de réponse à l’application. (Consultez [http://www.w3.org/TR/cors/#simple-response-header](http://www.w3.org/TR/cors/#simple-response-header).) Les en-têtes de réponse qui sont disponibles par défaut sont :

* Cache-Control

* Content-Language

* Content-Type

* Expires

* Dernière modification

* Pragma

La spécification CORS appelle ces *les en-têtes de réponse simple*. Pour apporter d’autres en-têtes accessibles à l’application :

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=71-76)]

### <a name="credentials-in-cross-origin-requests"></a>Informations d’identification dans les demandes cross-origin

Informations d’identification nécessitent un traitement particulier dans une demande CORS. Par défaut, le navigateur n’envoie pas d’informations d’identification avec une demande cross-origin. Informations d’identification incluent les cookies, ainsi que des schémas d’authentification HTTP. Pour envoyer des informations d’identification avec une demande cross-origin, le client doit définir XMLHttpRequest.withCredentials sur true.

À l’aide de XMLHttpRequest directement :

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'http://www.example.com/api/test');
xhr.withCredentials = true;
```

Dans jQuery :

```jQuery
$.ajax({
  type: 'get',
  url: 'http://www.example.com/home',
  xhrFields: {
    withCredentials: true
}
```

En outre, le serveur doit autoriser les informations d’identification. Pour autoriser les informations d’identification cross-origine :

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=80-85)]

La réponse HTTP inclut désormais un en-tête Access-contrôle-Allow-Credentials, lequel indique au navigateur que le serveur autorise les informations d’identification pour une demande cross-origin.

Si le navigateur envoie des informations d’identification, mais la réponse n’inclut pas un en-tête Access-contrôle-Allow-Credentials valid, le navigateur ne sera pas exposer la réponse à l’application et la requête AJAX échoue.

Soyez très prudent à l’autorisation d’informations d’identification de cross-origine, car cela signifie qu’un site Web à un autre domaine peut envoyer des informations d’identification d’un utilisateur de connecté à votre application sur l’utilisateur, sans l’utilisateur. CORS spec également États d’origine de ce paramètre à « * » (toutes les origines) n’est pas valide si l’en-tête Access-contrôle-Allow-Credentials est présent.

### <a name="set-the-preflight-expiration-time"></a>Définir le délai d’expiration en amont

L’en-tête Access-contrôle-Max-Age spécifie la durée pendant laquelle la réponse à la demande préliminaire peut être mis en cache. Pour définir cet en-tête :

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=89-94)]

<a name="cors-how-cors-works"></a>

## <a name="how-cors-works"></a>Fonctionnement des règles CORS

Cette section décrit ce qui se passe dans une demande CORS, au niveau des messages HTTP. Il est important de comprendre le fonctionnement de CORS, afin que vous pouvez configurer votre stratégie CORS correctement et résoudre les problèmes si les éléments ne fonctionnent pas comme prévu.

La spécification CORS introduit plusieurs nouveaux en-têtes HTTP qui permettent les demandes cross-origin. Si un navigateur prend en charge CORS, il définit ces en-têtes automatiquement pour les demandes cross-origin ; vous n’avez pas besoin de faire quelque chose de spécial dans votre code JavaScript.

Voici un exemple de demande cross-origin. L’en-tête « Origine » donne le domaine du site qui effectue la demande :

```
GET http://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: http://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: http://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

Si le serveur autorise la demande, il définit l’en-tête Access-Control-Allow-Origin dans la réponse. La valeur de cet en-tête correspond à l’en-tête d’origine à partir de la demande, ou est la valeur de caractère générique « * », ce qui signifie que toute origine est autorisée :

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: http://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

Si la réponse n’inclut pas l’en-tête Access-Control-Allow-Origin, la requête AJAX échoue. Plus précisément, le navigateur n’autorise pas la demande. Même si le serveur retourne une réponse correcte, le navigateur ne rend pas la réponse disponible à l’application cliente.

### <a name="preflight-requests"></a>Demandes préliminaires

Pour certaines requêtes CORS, le navigateur envoie une demande supplémentaire, appelée « demande préliminaire, » avant d’envoyer la demande réelle de la ressource. Le navigateur peut ignorer la demande préliminaire si les conditions suivantes sont remplies :

* La méthode de demande est GET, HEAD ou POST, et

* L’application ne définit pas les en-têtes de demande que Accept, Accept-Language, Content-Language, Content-Type ou dernier-ID d’événement, et

* L’en-tête Content-Type (si défini) est une des opérations suivantes :

  * application/x-www-form-urlencoded

  * multipart/form-data

  * texte brut

La règle sur les en-têtes de demande s’applique aux en-têtes de l’application définit en appelant setRequestHeader sur l’objet XMLHttpRequest. (La spécification CORS appelle ces « en-têtes de demande auteur ».) La règle ne s’applique pas aux en-têtes que le navigateur peut définir, telles que l’Agent utilisateur, ordinateur hôte ou Content-Length.

Voici un exemple d’une requête préliminaire :

```
OPTIONS http://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: http://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

La demande préliminaire utilise la méthode HTTP OPTIONS. Elle inclut deux en-têtes spéciales :

* Access-Control-Request-Method : Méthode HTTP qui sera utilisée pour la demande réelle.

* Access-Control-Request-Headers : Liste des en-têtes de demande que l’application est définie sur la demande réelle. (Là encore, cela n’inclut pas les en-têtes qui définit par le navigateur.)

Voici un exemple de réponse, en supposant que le serveur autorise la demande :

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: http://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

La réponse inclut un en-tête Access-contrôle-Allow-Methods qui répertorie les méthodes autorisées et éventuellement un en-tête Access-Control-autoriser-Headers, qui répertorie les en-têtes autorisés. Si la demande préliminaire réussit, le navigateur envoie la demande réelle, comme décrit précédemment.
