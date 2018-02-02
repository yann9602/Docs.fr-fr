---
title: "Prévention des attaques Cross-Site Request Forgery (XSRF/CSRF) ASP.NET Core"
author: steve-smith
description: "Prévention des attaques Cross-Site Request Forgery (XSRF/CSRF) ASP.NET Core"
manager: wpickett
ms.author: riande
ms.date: 7/14/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/anti-request-forgery
ms.openlocfilehash: 079c36535b8c9e7229952a2f7bcd53174effa6af
ms.sourcegitcommit: f2a11a89037471a77ad68a67533754b7bb8303e2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="preventing-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>Prévention des attaques Cross-Site Request Forgery (XSRF/CSRF) ASP.NET Core

[Steve Smith](https://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), et [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-attack-does-anti-forgery-prevent"></a>Les attaques anti-contrefaçon n’empêche ?

Falsification de requête (également appelé XSRF ou CSRF, prononcé *voir-navigation*) est une attaque contre les applications hébergées par le web dans laquelle un site web malveillant peut influencer l’interaction entre un navigateur client et un site web qui approuve ce navigateur. Ces attaques sont rendu possible, car certains types de jetons d’authentification automatiquement avec chaque demande d’envoi de navigateurs web à un site web. Cette forme d’attaque de l’appelé aussi un *en un clic attaque* ou en tant que *vol de session*, car l’attaque tire parti de l’utilisateur est authentifié précédemment de session.

Voici un exemple d’une attaque CSRF :

1. Un utilisateur se connecte à `www.example.com`, à l’aide de l’authentification par formulaire.
2. Le serveur authentifie l’utilisateur et émet une réponse qui inclut un cookie d’authentification.
3. L’utilisateur visite un site malveillant.

   Le site contient un formulaire HTML semblable au suivant :

```html
   <h1>You Are a Winner!</h1>
     <form action="http://example.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw" />
       <input type="hidden" name="Amount" value="1000000" />
     <input type="submit" value="Click Me"/>
   </form>
```

Notez que l’action de formulaire valide sur le site vulnérable, pas pour le site malveillant. Il s’agit de la partie « cross-site » de CSRF.

4. L’utilisateur clique sur le bouton Envoyer. Le navigateur inclut automatiquement le cookie d’authentification pour le domaine demandé (le site vulnérable dans ce cas) avec la demande.
5. La demande s’exécute sur le serveur avec le contexte de l’utilisateur d’authentification et peut exécuter toute action qu’un utilisateur authentifié est autorisé à faire.

Cet exemple oblige l’utilisateur à cliquer sur le bouton du formulaire. La page malveillante pourrait :

* Exécuter un script qui envoie automatiquement le formulaire.
* Envoie un envoi de formulaire sous la forme d’une requête AJAX. 
* Utiliser un formulaire masqué avec CSS. 

Une attaque CSRF n’empêche pas l’utilisation de SSL, le site malveillant peut envoyer un `https://` demande. 

Certaines attaques de ciblent des points de terminaison de site qui répondent aux `GET` demandes, dans lequel la case une balise d’image peut être utilisé pour effectuer l’action (cette forme d’attaque est courant sur les sites de forum qui autorisent les images mais bloquent JavaScript). Les applications qui modifient l’état avec `GET` demandes sont vulnérables contre les attaques malveillantes.

Les attaques CSRF sont possibles contre les sites web qui utilisent des cookies pour l’authentification, étant donné que les navigateurs envoient tous les cookies pertinentes pour le site de destination. Toutefois, les attaques CSRF n’êtes pas limités pour exploiter les cookies. Par exemple, l’authentification de base et Digest sont également vulnérables. Une fois un utilisateur se connecte avec l’authentification de base ou Digest, le navigateur envoie automatiquement les informations d’identification jusqu'à ce que la session se termine.

Remarque : dans ce contexte, *session* fait référence à la session côté client au cours de laquelle l’utilisateur est authentifié. Il est sans rapport avec les sessions côté serveur ou [intergiciel (middleware) de session](xref:fundamentals/app-state).

Les utilisateurs peuvent se protéger contre les vulnérabilités CSRF par :
* Déconnexion de sites web lorsqu’ils ont terminé leur utilisation.
* Effacer les cookies de son navigateur régulièrement.

Toutefois, les vulnérabilités CSRF sont fondamentalement un problème avec l’application web, pas l’utilisateur final.

## <a name="how-does-aspnet-core-mvc-address-csrf"></a>La manière dont ASP.NET Core MVC adressez CSRF ?

> [!WARNING]
> ASP.NET Core implémente à l’aide de l’anti-request-falsification le [pile de protection des données ASP.NET Core](xref:security/data-protection/introduction). Protection des données ASP.NET Core doit être configurée pour fonctionner dans une batterie de serveurs. Consultez [configuration de la protection des données](xref:security/data-protection/configuration/overview) pour plus d’informations.

Configuration de protection de données de valeur par défaut de l’anti-request-falsification ASP.NET Core 

Dans ASP.NET Core MVC 2.0 le [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injecte les jetons anti-contrefaçon pour les éléments de formulaire HTML. Par exemple, le balisage suivant dans un fichier Razor génère automatiquement les jetons anti-contrefaçon :

```html
<form method="post">
  <!-- form markup -->
</form>
```

La génération automatique des jetons anti-contrefaçon pour les éléments de formulaire HTML se produit lorsque :

* Le `form` balise contient le `method="post"` attribut AND

  * L’attribut d’action est vide. ( `action=""`) OU
  * L’attribut d’action n’est pas fourni. (`<form method="post">`)

Vous pouvez désactiver la génération automatique d’anti-contrefaçon jetons pour les éléments de formulaire HTML en :

* Désactive explicitement `asp-antiforgery`. Exemple :

 ```html
  <form method="post" asp-antiforgery="false">
  </form>
  ```

* Choisir l’élément form en dehors des programmes d’assistance de balise à l’aide du programme d’assistance de balise [! annulations symbole](xref:mvc/views/tag-helpers/intro#opt-out).

 ```html
  <!form method="post">
  </!form>
  ```

* Supprimer le `FormTagHelper` à partir de la vue. Vous pouvez supprimer le `FormTagHelper` à partir d’une vue en ajoutant la directive suivante à la vue Razor :

 ```html
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [Pages Razor](xref:mvc/razor-pages/index) sont automatiquement protégés contre XSRF/CSRF. Vous n’êtes pas obligé d’écrire du code supplémentaire. Consultez [XSRF/CSRF et Pages Razor](xref:mvc/razor-pages/index#xsrf) pour plus d’informations.

L’approche la plus courante de défense contre les attaques CSRF est le modèle de jeton synchronisateur (STP). STP est une technique utilisée lorsque l’utilisateur demande une page de données de formulaire. Le serveur envoie un jeton avec l’identité de l’utilisateur actuel au client. Le client envoie le jeton sur le serveur pour la vérification. Si le serveur reçoit un jeton qui ne correspond pas à identité de l’utilisateur authentifié, la demande est rejetée. Le jeton est unique et imprévisibles. Le jeton peut également être utilisé pour garantir une mise en séquence correcte d’une série de requêtes (page 1 de la garantie que précède la page 2, ce qui précède la page 3). Tous les formulaires dans les modèles ASP.NET MVC de base génèrent des jetons de côté. Les deux exemples suivants de la logique de la vue de génèrent des jetons côtés :

```html
<form asp-controller="Manage" asp-action="ChangePassword" method="post">

</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    
}
```

Vous pouvez ajouter explicitement un jeton à côté d’un ``<form>`` élément sans l’aide de programmes d’assistance de balise avec l’application d’assistance HTML ``@Html.AntiForgeryToken``:


```html
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

Dans chacun des cas précédents, ASP.NET Core ajoutera un champ de formulaire masqué semblable au suivant :
```html
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkSldwD9CpLRyOtm6FiJB1Jr_F3FQJQDvhlHoLNJJrLA6zaMUmhjMsisu2D2tFkAiYgyWQawJk9vNm36sYP1esHOtamBEPvSk1_x--Sg8Ey2a-d9CV2zHVWIN9MVhvKHOSyKqdZFlYDVd69XYx-rOWPw3ilHGLN6K0Km-1p83jZzF0E4WU5OGg5ns2-m9Yw" />
```

ASP.NET Core inclut trois [filtres](xref:mvc/controllers/filters) pour l’utilisation des jetons de côté : ``ValidateAntiForgeryToken``, ``AutoValidateAntiforgeryToken``, et ``IgnoreAntiforgeryToken``.

<a name="vaft"></a>

### <a name="validateantiforgerytoken"></a>ValidateAntiForgeryToken

Le ``ValidateAntiForgeryToken`` est un filtre d’action qui peut être appliqué à une action individuelle, un contrôleur, ou globalement. Requêtes adressées à des actions qui ont ce filtre appliqué seront bloqués, sauf si la demande inclut un jeton valide de côté.

```c#
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();
    if (user != null)
    {
        var result = await _userManager.RemoveLoginAsync(user, account.LoginProvider, account.ProviderKey);
        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }
    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

Le ``ValidateAntiForgeryToken`` attribut requiert un jeton pour les demandes de méthodes d’action qu’il décore, y compris `HTTP GET` demandes. Si vous appliquez largement, vous pouvez la remplacer par la ``IgnoreAntiforgeryToken`` attribut.

### <a name="autovalidateantiforgerytoken"></a>AutoValidateAntiforgeryToken

Applications ASP.NET Core généralement ne pas générer de jetons de côté pour les méthodes sans échec HTTP (GET, HEAD, OPTIONS et TRACE). Au lieu d’appliquer globalement la ``ValidateAntiForgeryToken`` attribut et en remplaçant puis avec ``IgnoreAntiforgeryToken`` attributs, vous pouvez utiliser la ``AutoValidateAntiforgeryToken`` attribut. Cet attribut fonctionne de manière identique à la ``ValidateAntiForgeryToken`` d’attribut, sauf qu’elle ne nécessite pas les jetons pour les demandes effectuées à l’aide des méthodes HTTP suivantes :

* GET
* HEAD
* OPTIONS
* TRACE

Nous vous recommandons d’utiliser ``AutoValidateAntiforgeryToken`` largement pour les scénarios non-API. Cela garantit que vos actions POST sont protégées par défaut. L’alternative consiste à ignorer les jetons côtés par défaut, sauf si ``ValidateAntiForgeryToken`` est appliqué à la méthode d’action individuelle. Il est plus probable dans ce scénario pour une méthode d’action POST à gauche non protégé, en laissant votre application vulnérable aux attaques CSRF. Les publications anonymes même doivent envoyer le jeton côté.

Remarque : Les API n’ont aucun mécanisme automatique pour l’envoi de la partie non-cookie du jeton ; votre implémentation dépend probablement votre implémentation de code client. Certains exemples sont présentés ci-dessous.


Exemple (niveau de la classe) :

```c#
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

Exemple (global) :

```c#
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

<a name="iaft"></a>

### <a name="ignoreantiforgerytoken"></a>IgnoreAntiforgeryToken

Le ``IgnoreAntiforgeryToken`` filtre est utilisé pour éliminer le besoin d’un jeton de côté être présent pour une action donnée (ou un contrôleur). Quand il est appliqué, ce filtre remplace ``ValidateAntiForgeryToken`` et/ou ``AutoValidateAntiforgeryToken`` filtres spécifiés à un niveau supérieur (globalement ou sur un contrôleur).

```c#
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
  [HttpPost]
  [IgnoreAntiforgeryToken]
  public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
  {
    // no antiforgery token required
  }
}
```

## <a name="javascript-ajax-and-spas"></a>JavaScript, AJAX et SPAs

Dans les applications traditionnelles basées sur HTML côtés jetons sont passées au serveur à l’aide des champs de formulaire masqué. Dans les applications modernes basées JavaScript et des applications à page unique (ZPS), le nombre de requêtes est effectuée par programme. Ces demandes AJAX peuvent utiliser d’autres techniques (par exemple, les en-têtes de requête ou des cookies) pour envoyer le jeton. Si les cookies sont utilisés pour stocker les jetons d’authentification et pour authentifier les demandes sur le serveur de l’API, CSRF sera un problème potentiel. Toutefois, si le stockage local est utilisé pour stocker le jeton, CSRF vulnérabilité peut être atténuée, étant donné que les valeurs à partir du stockage local ne sont pas automatiquement envoyées au serveur à chaque nouvelle demande. Par conséquent, l’utilisation du stockage local pour stocker le jeton côté sur le client et envoie le jeton, comme un en-tête de demande est une approche recommandée.

### <a name="angularjs"></a>AngularJS

AngularJS utilise une convention à l’adresse CSRF. Si le serveur envoie un cookie avec le nom ``XSRF-TOKEN``, l’angulaire ``$http`` service, ajoutez la valeur de ce cookie à un en-tête lorsqu’il envoie une demande à ce serveur. Ce processus est automatique ; vous n’avez pas besoin de définir l’en-tête explicitement. Le nom d’en-tête est ``X-XSRF-TOKEN``. Le serveur doit détecter cet en-tête et valider son contenu.

Pour le travail de l’API ASP.NET principale avec la convention :

* Configurer votre application pour fournir un jeton dans un cookie appelé``XSRF-TOKEN``
* Configurer le côté service pour rechercher un en-tête nommé``X-XSRF-TOKEN``

```c#
services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
```

[Exemple d’affichage](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample).

### <a name="javascript"></a>JavaScript

À l’aide de JavaScript avec des vues, vous pouvez créer le jeton à l’aide d’un service à partir de votre affichage. Pour ce faire, vous injectez la `Microsoft.AspNetCore.Antiforgery.IAntiforgery` service dans la vue et appelez `GetAndStoreTokens`, comme indiqué :

[!code-csharp[Main](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,24)]

Cette approche élimine le besoin de traiter directement avec la définition des cookies à partir du serveur ou de les lire à partir du client.

JavaScript permettre également accéder aux jetons fournis dans des cookies et puis permet de contenu du cookie créer un en-tête avec sa valeur, comme indiqué ci-dessous.

```c#
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
  new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

Puis, en supposant que la construction de votre script demande à envoyer le jeton dans un en-tête appelé ``X-CSRF-TOKEN``, configurer le côté service pour rechercher le ``X-CSRF-TOKEN`` en-tête :

```c#
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

L’exemple suivant utilise jQuery pour effectuer une requête AJAX avec l’en-tête approprié :

```javascript
var csrfToken = $.cookie("CSRF-TOKEN");

$.ajax({
    url: "/api/password/changepassword",
    contentType: "application/json",
    data: JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }),
    type: "POST",
    headers: {
        "X-CSRF-TOKEN": csrfToken
    }
});
```

## <a name="configuring-antiforgery"></a>Configuration Antiforgery

`IAntiforgery`fournit l’API pour configurer le système de côté. Il peut être demandé dans le `Configure` méthode de la `Startup` classe. L’exemple suivant utilise l’intergiciel (middleware) à partir de la page d’accueil de l’application pour générer un jeton de côté et l’envoyer dans la réponse sous forme de cookie (à l’aide de la convention de dénomination angulaire par défaut est décrite ci-dessus) :


```c#
public void Configure(IApplicationBuilder app, 
    IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;
        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // We can send the request token as a JavaScript-readable cookie, 
            // and Angular will use it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
    //
}
```

### <a name="options"></a>Options

Vous pouvez personnaliser [options côtées](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions#fields_summary) dans `ConfigureServices`:

```c#
services.AddAntiforgery(options => 
{
  options.CookieDomain = "mydomain.com";
  options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
  options.CookiePath = "Path";
  options.FormFieldName = "AntiforgeryFieldname";
  options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
  options.RequireSsl = false;
  options.SuppressXFrameOptionsHeader = false;
});
```

<!-- QAfix fix table -->

|Option        | Description |
|------------- | ----------- |
|CookieDomain  | Le domaine du cookie. La valeur par défaut est `null`. |
|CookieName    | Le nom du cookie. Si non définie, le système génère un nom unique commençant par le `DefaultCookiePrefix` ( ». AspNetCore.Antiforgery. »). |
|CookiePath    | Le chemin d’accès défini sur le cookie. |
|FormFieldName | Le nom du champ de formulaire masqué utilisé par le système de côté pour effectuer le rendu côté des jetons dans les vues. |
|HeaderName    | Le nom de l’en-tête utilisé par le système de côté. Si `null`, le système considère uniquement les données de formulaire. |
|RequireSsl    | Spécifie si SSL est requise par le système de côté. La valeur par défaut est `false`. Si `true`, les demandes non-SSL échouent. |
|SuppressXFrameOptionsHeader  | Spécifie s’il faut supprimer la génération de la `X-Frame-Options` en-tête. Par défaut, l’en-tête est généré avec la valeur « SAMEORIGIN ». La valeur par défaut est `false`. |

Https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.cookieauthenticationoptions pour plus d’informations, consultez.

### <a name="extending-antiforgery"></a>Extension Antiforgery

Le [IAntiForgeryAdditionalDataProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) permet aux développeurs d’étendre le comportement du système anti-XSRF par des données supplémentaires aller-retour dans chaque jeton. Le [GetAdditionalData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider#Microsoft_AspNetCore_Antiforgery_IAntiforgeryAdditionalDataProvider_GetAdditionalData_Microsoft_AspNetCore_Http_HttpContext_) méthode est appelée chaque fois qu’un jeton de champ est généré, et la valeur de retour est incorporée dans le jeton généré. Un implémenteur peut retourner un horodatage, une valeur à usage unique ou toute autre valeur, puis appelez [ValidateAdditionalData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider#Microsoft_AspNetCore_Antiforgery_IAntiforgeryAdditionalDataProvider_ValidateAdditionalData_Microsoft_AspNetCore_Http_HttpContext_System_String_) pour valider ces données lorsque le jeton est validé. Nom d’utilisateur du client est déjà incorporée dans les jetons générés, il est donc inutile d’inclure ces informations. Si un jeton contient des données supplémentaires, mais non `IAntiForgeryAdditionalDataProvider` a été configuré, les données supplémentaires n’est pas validées.

## <a name="fundamentals"></a>Notions de base

Les attaques CSRF reposent sur le comportement du navigateur par défaut de l’envoi de cookies associés à un domaine avec chaque demande adressée à ce domaine. Ces cookies sont stockés dans le navigateur. Ils incluent souvent des cookies de session pour les utilisateurs authentifiés. L’authentification basée sur le cookie est une forme courante d’authentification. Systèmes d’authentification par jeton ont été gagnent en popularité, en particulier pour SPAs et autres scénarios « smart client ».

### <a name="cookie-based-authentication"></a>Authentification par cookie

Une fois qu’un utilisateur est authentifié à l’aide de leur nom d’utilisateur et un mot de passe, ils sont émis un jeton qui peut être utilisé pour les identifier et de valider qu’ils ont été authentifiées. Le jeton est stocké en tant que permet d’un cookie qui accompagne chaque demande du client. Génération et la validation de ce cookie sont effectuée par l’intergiciel (middleware) d’authentification de cookie. ASP.NET Core fournit le cookie [intergiciel (middleware)](xref:fundamentals/middleware/index) qui sérialise un principal d’utilisateur dans un cookie chiffré et puis, sur les demandes suivantes, valide le cookie, recrée le principal et l’affecte à la `User` propriété sur `HttpContext`.

Lorsqu’un cookie est utilisé, le cookie d’authentification est simplement un conteneur pour le ticket d’authentification de formulaires. Le ticket est passé en tant que la valeur du cookie d’authentification forms avec chaque demande et est utilisé par l’authentification par formulaire, sur le serveur, pour identifier un utilisateur authentifié.

Lorsqu’un utilisateur est connecté à un système, une session utilisateur est créée sur le côté serveur et est stockée dans une base de données ou d’un autre magasin persistant. Le système génère une clé de session qui pointe vers la session réelle dans le magasin de données et il est envoyé sous la forme d’un cookie de côté client. Le serveur web vérifie cette clé de session lorsqu’un utilisateur demande une ressource qui nécessite une autorisation. Le système vérifie si la session utilisateur associée est autorisé à accéder à la ressource demandée. Dans ce cas, la demande se poursuit. Dans le cas contraire, la demande retourne en tant que non autorisé. Dans cette approche, les cookies sont utilisés pour rendre l’application semble être avec état, car il est en mesure de « se souviennent » que l’utilisateur a précédemment authentifié avec le serveur.

### <a name="user-tokens"></a>Jetons d’utilisateur

L’authentification basée sur le jeton ne stocke pas session sur le serveur. Lorsqu’un utilisateur est connecté, ils sont émis un jeton (pas un jeton côté). Ce jeton conserve les données requises pour valider le jeton. Il contient également des informations d’utilisateur sous la forme de [revendications](https://docs.microsoft.com/dotnet/framework/security/claims-based-identity-model). Lorsqu’un utilisateur souhaite accéder à une ressource de serveur nécessitant une authentification, le jeton est envoyé au serveur avec un en-tête d’autorisation supplémentaires sous forme de porteur {jeton}. Cela rend l’application sans état, car dans chaque demande ultérieure le jeton est passé dans la demande pour la validation côté serveur. Ce jeton n’est pas *chiffrées*; il s’agit plutôt *codé*. Sur le côté serveur, le jeton peut être décodé pour accéder aux informations brutes dans le jeton. Pour envoyer le jeton dans les demandes suivantes, soit stockez-le dans le stockage local du navigateur ou dans un cookie. Ne vous préoccupez vulnérabilité XSRF si le jeton est stocké dans le stockage local, mais il s’agit d’un problème si le jeton est stocké dans un cookie.

### <a name="multiple-applications-are-hosted-in-one-domain"></a>Plusieurs applications sont hébergées dans un domaine

Bien que `example1.cloudapp.net` et `example2.cloudapp.net` sont des hôtes différents, il existe une relation de confiance implicite entre les hôtes sous le `*.cloudapp.net` domaine. Cette relation de confiance implicite permet à des hôtes potentiellement non fiables affecter l’autre les cookies (les même origine les stratégies qui régissent les requêtes AJAX ne pas nécessairement appliquer les cookies HTTP). Le runtime ASP.NET Core permet une limitation dans la mesure où le nom d’utilisateur est incorporée dans le jeton de champ. Même si un sous-domaine malveillant est en mesure de remplacer un jeton de session, il ne peut pas générer un jeton de champ valide pour l’utilisateur. Lorsqu’il est hébergé dans un tel environnement, les routines intégrées anti-XSRF toujours ne peut pas protéger contre le piratage de session ou connexion CSRF contre les attaques. Environnements d’hébergement partagés sont vunerable le piratage de session, connexion CSRF et autres attaques.

### <a name="additional-resources"></a>Ressources supplémentaires

* [XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) sur [ouvrir le projet de sécurité d’Application Web](https://www.owasp.org/index.php/Main_Page) (OWASP avoir).
