---
title: "Prévention des attaques de redirection ouvert dans une application ASP.NET Core | Documents Microsoft"
author: ardalis
description: "Montre comment empêcher des attaques de redirection ouvert par rapport à une application ASP.NET Core"
keywords: "Attaque de redirection ouvert ASP.NET Core, la sécurité,"
ms.author: riande
manager: wpickett
ms.date: 07/07/2017
ms.topic: article
ms.assetid: 4604e563-e91a-4ecd-b7ed-00b3f1eee2b5
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/preventing-open-redirects
ms.openlocfilehash: 4083845a77eb19d9ba9beb389a92ceb5c14edbde
ms.sourcegitcommit: f5cf472d49c2475e4d57654efd5fc0a4ccecba4c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2017
---
# <a name="preventing-open-redirect-attacks-in-an-aspnet-core-app"></a>Prévention des attaques de redirection ouvert dans une application ASP.NET Core

Une application web qui effectue une redirection vers une URL qui est spécifiée via la demande tels que les données de chaîne de requête ou un formulaire peut potentiellement être falsifiée rediriger les utilisateurs vers une URL externe, malveillante. Cette manipulation est appelée une attaque de redirection ouvert.

Chaque fois que votre logique d’application redirige vers une URL spécifiée, vous devez vérifier que l’URL de redirection n’a pas été falsifié. ASP.NET Core intègre des fonctionnalités pour aider à protéger les applications contre les attaques de redirection ouvert (également appelé open redirection).

## <a name="what-is-an-open-redirect-attack"></a>Qu’est une attaque de redirection ouvrir ?

Les applications Web rediriger fréquemment les utilisateurs vers une page de connexion lorsqu’ils accèdent aux ressources qui requièrent une authentification. La redirection typlically inclut un `returnUrl` paramètre querystring afin que l’utilisateur peut être retourné à l’URL demandée à l’origine une fois qu’ils ont connecté avec succès. Une fois que l’utilisateur s’authentifie, ils sont redirigés vers l’URL qui avait initialement demandée.

Étant donné que l’URL de destination est spécifié dans la chaîne de requête de la demande, un utilisateur malveillant peut falsifier la chaîne de requête. Une chaîne de requête falsifiée pourrait permettre au site pour rediriger l’utilisateur vers un site externe, malveillant. Cette technique est appelée une attaque de redirection (ou une redirection) ouverte.

### <a name="an-example-attack"></a>Une attaque de l’exemple

Un utilisateur malveillant peut développer une attaque conçue pour permettre l’accès utilisateur malveillant pour les informations d’identification d’un utilisateur ou les informations sensibles sur votre application. Pour lancer l’attaque, elles convaincre l’utilisateur clique sur un lien vers la page de connexion de votre site, avec un `returnUrl` valeur de chaîne de requête ajoutée à l’URL. Par exemple, le [NerdDinner.com](http://nerddinner.com) exemple d’application (écrite pour ASP.NET MVC) inclut ce une page de connexion ici : ``http://nerddinner.com/Account/LogOn?returnUrl=/Home/About``. L’attaque puis procédez comme suit :

1. Utilisateur clique sur un lien vers ``http://nerddinner.com/Account/LogOn?returnUrl=http://nerddiner.com/Account/LogOn`` (Notez que la deuxième URL est nerddi**n**er, pas nerddi**nn**er).
2. L’utilisateur parvient à se connecter.
3. L’utilisateur est redirigé (par le site) à ``http://nerddiner.com/Account/LogOn`` (site malveillant qui ressemble à un site réel).
4. L’utilisateur se connecte à nouveau (donnant malveillant leurs informations d’identification de site) et est redirigé vers le site réel.

L’utilisateur sera pensez probablement de leur première tentative de connexion a échoué, et leur autre a réussi. Ils allez restent très probablement pas au courant leurs informations d’identification ont été compromises.

![Processus d’attaque Redirection ouvert](preventing-open-redirects/_static/open-redirection-attack-process.png)

En plus des pages de connexion, certains sites fournissent des points de terminaison ou des pages de redirection. Imaginez que votre application comprend une page avec une redirection d’ouvrir, ``/Home/Redirect``. Un attaquant pourrait créer, par exemple, un lien dans un message électronique qui accède à ``[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login``. Un utilisateur à l’URL et voir qu'il commence par le nom de votre site. Approbation qui, elles devra cliquer sur le lien. La redirection ouvrir envoie ensuite l’utilisateur vers le site de hameçonnage, qui est identique à la vôtre, et l’utilisateur aurait probablement connexion pour qu’ils pensent est votre site.

## <a name="protecting-against-open-redirect-attacks"></a>Protection contre les attaques de redirection ouvert

Lorsque vous développez des applications web, traiter toutes les données fournies par l’utilisateur comme non fiables. Si votre application possède des fonctionnalités qui redirige l’utilisateur en fonction du contenu de l’URL, vérifiez que ces redirections sont effectuées uniquement localement dans votre application (ou à une URL connue, pas une URL qui peut être fournie dans la chaîne de requête).

### <a name="localredirect"></a>LocalRedirect

Utilisez le ``LocalRedirect`` méthode d’assistance à partir de la base de `Controller` classe :

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

``LocalRedirect``lève une exception si une URL non local est spécifiée. Dans le cas contraire, il se comporte comme le ``Redirect`` (méthode).

### <a name="islocalurl"></a>IsLocalUrl

Utilisez le [IsLocalUrl](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.iurlhelper#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) méthode pour tester l’URL avant de rediriger les :

L’exemple suivant montre comment vérifier si une URL est locale avant de rediriger.

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

Le `IsLocalUrl` méthode protège les utilisateurs en cours par inadvertance redirigés vers un site malveillant. Vous pouvez consigner les détails de l’URL qui a été fourni lorsqu’une URL non local est fournie dans une situation dans laquelle vous vous attendiez une URL locale. Journalisation de redirection URL peuvent faciliter le diagnostic des attaques de redirection.
