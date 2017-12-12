---
uid: web-api/overview/advanced/http-cookies
title: Les Cookies HTTP dans ASP.NET Web API | Documents Microsoft
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/17/2012
ms.topic: article
ms.assetid: 243db2ec-8f67-4a5e-a382-4ddcec4b4164
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/advanced/http-cookies
msc.type: authoredcontent
ms.openlocfilehash: e17c51946a268aa13ec035d18dc516928c9f4419
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="http-cookies-in-aspnet-web-api"></a>Cookies HTTP dans l’API Web ASP.NET
====================
par [Mike Wasson](https://github.com/MikeWasson)

Cette rubrique décrit comment envoyer et recevoir des cookies HTTP dans l’API Web.

## <a name="background-on-http-cookies"></a>Informations générales sur les Cookies HTTP

Cette section donne une vue d’ensemble de la façon dont les cookies sont implémentés au niveau HTTP. Pour plus d’informations, consultez [RFC 6265](http://tools.ietf.org/html/rfc6265).

Un cookie est un élément de données qu’un serveur envoie des réponses HTTP. Le client stocke le cookie (facultatif) et le retourne subsequet demandes. Ainsi, le client et le serveur partager l’état. Pour définir un cookie, le serveur inclut un en-tête Set-Cookie dans la réponse. Le format d’un cookie est une paire nom-valeur, avec des attributs facultatifs. Exemple :

[!code-powershell[Main](http-cookies/samples/sample1.ps1)]

Voici un exemple avec des attributs :

[!code-powershell[Main](http-cookies/samples/sample2.ps1)]

Pour retourner un cookie sur le serveur, le client contient un en-tête Cookie dans les demandes ultérieures.

[!code-console[Main](http-cookies/samples/sample3.cmd)]

![](http-cookies/_static/image1.png)

Une réponse HTTP peut inclure plusieurs en-têtes Set-Cookie.

[!code-powershell[Main](http-cookies/samples/sample4.ps1)]

Le client retourne plusieurs cookies à l’aide d’un seul en-tête Cookie.

[!code-console[Main](http-cookies/samples/sample5.cmd)]

La portée et la durée d’un cookie sont contrôlées par les attributs suivants dans l’en-tête Set-Cookie :

- **Domaine**: indique aux clients de domaine qui doit recevoir le cookie. Par exemple, si le domaine est « example.com », le client retourne le cookie à chaque sous-domaine d’exemple.com. Si non spécifié, le domaine est le serveur d’origine.
- **Chemin d’accès**: restreint le cookie pour le chemin d’accès spécifié au sein du domaine. Si non spécifié, le chemin d’accès de l’URI de demande est utilisé.
- **Expiration**: définit une date d’expiration du cookie. Le client supprime le cookie expiré.
- **Max-Age**: définit la durée de vie maximale pour le cookie. Le client supprime le cookie lorsqu’il atteint l’âge maximal.

Si les deux `Expires` et `Max-Age` sont définies, `Max-Age` est prioritaire. Si aucune n’est définie, le client supprime le cookie lors de la session actuelle se termine. (La signification exacte de « session » est déterminée par l’agent utilisateur).

Toutefois, sachez que les clients peuvent ignorer les cookies. Par exemple, un utilisateur peut désactiver les cookies pour des raisons de confidentialité. Les clients peuvent supprimer les cookies avant leur expiration ou limitent le nombre de cookies stockés. Pour des raisons de confidentialité, les clients rejettent souvent des cookies de « tiers », où le domaine ne correspond pas au serveur d’origine. En bref, le serveur de ne doit pas dépendre pour obtenir les cookies qu’il définit.

## <a name="cookies-in-web-api"></a>Cookies dans l’API Web

Pour ajouter un cookie à une réponse HTTP, créez un **CookieHeaderValue** instance qui représente le cookie. Appelez ensuite la **AddCookies** méthode d’extension, qui est définie dans le **System.Net.Http. HttpResponseHeadersExtensions** (classe), pour ajouter le cookie.

Par exemple, le code suivant ajoute un cookie au sein d’une action de contrôleur :

[!code-csharp[Main](http-cookies/samples/sample6.cs)]

Notez que **AddCookies** prend un tableau de **CookieHeaderValue** instances.

Pour extraire les cookies à partir d’une demande du client, appelez le **GetCookies** méthode :

[!code-csharp[Main](http-cookies/samples/sample7.cs)]

A **CookieHeaderValue** contient une collection de **CookieState** instances. Chaque **CookieState** représente un cookie. Utilisez la méthode de l’indexeur pour obtenir un **CookieState** par nom, comme indiqué.

## <a name="structured-cookie-data"></a>Données de Cookie structurée

De nombreux navigateurs limitent le nombre de cookies ils stockera &#8212; à la fois le nombre total et le nombre par domaine. Par conséquent, il peut être utile de placer des données structurées dans un cookie unique, au lieu de définir plusieurs cookies.

> [!NOTE]
> RFC 6265 ne définit pas la structure des données de cookie.


À l’aide de la **CookieHeaderValue** (classe), vous pouvez passer une liste de paires nom-valeur pour les données de cookie. Ces paires nom-valeur sont encodés en tant que données de forme codée URL dans l’en-tête Set-Cookie :

[!code-csharp[Main](http-cookies/samples/sample8.cs)]

Le code précédent génère l’en-tête Set-Cookie suivant :

[!code-powershell[Main](http-cookies/samples/sample9.ps1)]

Le **CookieState** classe fournit une méthode de l’indexeur pour lire les valeurs de secondaire à partir d’un cookie dans le message de demande :

[!code-csharp[Main](http-cookies/samples/sample10.cs)]

## <a name="example-set-and-retrieve-cookies-in-a-message-handler"></a>Exemple : Définir et récupérer des Cookies dans un gestionnaire de messages

Les exemples précédents ont montré comment utiliser les cookies à partir d’un contrôleur d’API Web. Une autre option consiste à utiliser [gestionnaires de messages](http-message-handlers.md). Gestionnaires de messages sont appelés précédemment dans le pipeline de contrôleurs. Un gestionnaire de messages peut lire les cookies à partir de la demande avant que la demande n’atteigne le contrôleur, ou ajouter des cookies à la réponse après que le contrôleur a généré la réponse.

![](http-cookies/_static/image2.png)

Le code suivant montre un gestionnaire de messages pour la création d’ID de session. L’ID de session est stocké dans un cookie. Le gestionnaire vérifie la demande pour le cookie de session. Si la demande n’inclut pas le cookie, le gestionnaire génère un ID de la nouvelle session. Dans les deux cas, le gestionnaire stocke l’ID de session dans le **HttpRequestMessage.Properties** sac de propriétés. Il ajoute également le cookie de session à la réponse HTTP.

Cette implémentation ne valide pas que l’ID de session du client a été réellement émis par le serveur. Ne l’utilisez comme une forme d’authentification ! Le point de l’exemple est d’illustrer la gestion des cookies HTTP.

[!code-csharp[Main](http-cookies/samples/sample11.cs)]

Un contrôleur peut obtenir l’ID de session à partir de la **HttpRequestMessage.Properties** sac de propriétés.

[!code-csharp[Main](http-cookies/samples/sample12.cs)]
