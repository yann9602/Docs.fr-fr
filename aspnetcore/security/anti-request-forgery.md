---
title: "Prévention des attaques Cross-Site Request Forgery (XSRF/CSRF) ASP.NET Core"
author: steve-smith
ms.author: riande
description: "Prévention des attaques Cross-Site Request Forgery (XSRF/CSRF) ASP.NET Core"
manager: wpickett
ms.date: 7/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/anti-request-forgery
ms.openlocfilehash: 3c0f90dd9894c362c0d7fef5d1f1da076991605c
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="preventing-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="4a250-103">Prévention des attaques Cross-Site Request Forgery (XSRF/CSRF) ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4a250-103">Preventing Cross-Site Request Forgery (XSRF/CSRF) Attacks in ASP.NET Core</span></span>

<span data-ttu-id="4a250-104">[Steve Smith](https://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4a250-104">[Steve Smith](https://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="what-attack-does-anti-forgery-prevent"></a><span data-ttu-id="4a250-105">Les attaques anti-contrefaçon n’empêche ?</span><span class="sxs-lookup"><span data-stu-id="4a250-105">What attack does anti-forgery prevent?</span></span>

<span data-ttu-id="4a250-106">Falsification de requête (également appelé XSRF ou CSRF, prononcé *voir-navigation*) est une attaque contre les applications hébergées par le web dans laquelle un site web malveillant peut influencer l’interaction entre un navigateur client et un site web qui approuve ce navigateur.</span><span class="sxs-lookup"><span data-stu-id="4a250-106">Cross-site request forgery (also known as XSRF or CSRF, pronounced *see-surf*) is an attack against web-hosted applications whereby a malicious web site can influence the interaction between a client browser and a web site that trusts that browser.</span></span> <span data-ttu-id="4a250-107">Ces attaques sont rendu possible, car certains types de jetons d’authentification automatiquement avec chaque demande d’envoi de navigateurs web à un site web.</span><span class="sxs-lookup"><span data-stu-id="4a250-107">These attacks are made possible because web browsers send some types of authentication tokens automatically with every request to a web site.</span></span> <span data-ttu-id="4a250-108">Cette forme d’attaque est également appelé un *en un clic attaque* ou en tant que *vol de session*, car l’attaque tire parti de l’utilisateur est authentifié précédemment de session.</span><span class="sxs-lookup"><span data-stu-id="4a250-108">This form of exploit is also known as a *one-click attack* or as *session riding*, because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="4a250-109">Voici un exemple d’une attaque CSRF :</span><span class="sxs-lookup"><span data-stu-id="4a250-109">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="4a250-110">Un utilisateur se connecte à `www.example.com`, à l’aide de l’authentification par formulaire.</span><span class="sxs-lookup"><span data-stu-id="4a250-110">A user logs into `www.example.com`, using forms authentication.</span></span>
2. <span data-ttu-id="4a250-111">Le serveur authentifie l’utilisateur et émet une réponse qui inclut un cookie d’authentification.</span><span class="sxs-lookup"><span data-stu-id="4a250-111">The server authenticates the user and issues a response that includes an authentication cookie.</span></span>
3. <span data-ttu-id="4a250-112">L’utilisateur visite un site malveillant.</span><span class="sxs-lookup"><span data-stu-id="4a250-112">The user visits a malicious site.</span></span>

   <span data-ttu-id="4a250-113">Le site contient un formulaire HTML semblable au suivant :</span><span class="sxs-lookup"><span data-stu-id="4a250-113">The malicious site contains an HTML form similar to the following:</span></span>

```html
   <h1>You Are a Winner!</h1>
     <form action="http://example.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw" />
       <input type="hidden" name="Amount" value="1000000" />
     <input type="submit" value="Click Me"/>
   </form>
```

<span data-ttu-id="4a250-114">Notez que l’action de formulaire valide sur le site vulnérable, pas pour le site malveillant.</span><span class="sxs-lookup"><span data-stu-id="4a250-114">Notice that the form action posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="4a250-115">Il s’agit de la partie « cross-site » de CSRF.</span><span class="sxs-lookup"><span data-stu-id="4a250-115">This is the “cross-site” part of CSRF.</span></span>

4. <span data-ttu-id="4a250-116">L’utilisateur clique sur le bouton Envoyer.</span><span class="sxs-lookup"><span data-stu-id="4a250-116">The user clicks the submit button.</span></span> <span data-ttu-id="4a250-117">Le navigateur inclut automatiquement le cookie d’authentification pour le domaine demandé (le site vulnérable dans ce cas) avec la demande.</span><span class="sxs-lookup"><span data-stu-id="4a250-117">The browser automatically includes the authentication cookie for the requested domain (the vulnerable site in this case) with the request.</span></span>
5. <span data-ttu-id="4a250-118">La demande s’exécute sur le serveur avec le contexte de l’utilisateur d’authentification et peut faire tout ce qu’un utilisateur authentifié est autorisé à faire.</span><span class="sxs-lookup"><span data-stu-id="4a250-118">The request runs on the server with the user’s authentication context and can do anything that an authenticated user is allowed to do.</span></span>

<span data-ttu-id="4a250-119">Cet exemple oblige l’utilisateur à cliquer sur le bouton du formulaire.</span><span class="sxs-lookup"><span data-stu-id="4a250-119">This example requires the user to click the form button.</span></span> <span data-ttu-id="4a250-120">La page malveillante pourrait :</span><span class="sxs-lookup"><span data-stu-id="4a250-120">The malicious page could:</span></span>

* <span data-ttu-id="4a250-121">Exécuter un script qui envoie automatiquement le formulaire.</span><span class="sxs-lookup"><span data-stu-id="4a250-121">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="4a250-122">Envoie un envoi de formulaire sous la forme d’une requête AJAX.</span><span class="sxs-lookup"><span data-stu-id="4a250-122">Sends a form submission as an AJAX request.</span></span> 
* <span data-ttu-id="4a250-123">Utiliser un formulaire masqué avec CSS.</span><span class="sxs-lookup"><span data-stu-id="4a250-123">Use a hidden form with CSS.</span></span> 

<span data-ttu-id="4a250-124">À l’aide de SSL n’empêche pas une attaque CSRF, le site malveillant peut envoyer un `https://` demande.</span><span class="sxs-lookup"><span data-stu-id="4a250-124">Using SSL does not prevent a CSRF attack, the malicious site can send an `https://` request.</span></span> 

<span data-ttu-id="4a250-125">Certaines attaques de ciblent des points de terminaison de site qui répondent aux `GET` demandes, dans lequel la case une balise d’image peut être utilisé pour effectuer l’action (cette forme d’attaque est courant sur les sites de forum qui autorisent les images mais bloquent JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4a250-125">Some attacks  target site endpoints that respond to `GET` requests, in which case an image tag can be used to perform the action (this form of attack is common on forum sites that permit images but block JavaScript).</span></span> <span data-ttu-id="4a250-126">Les applications qui modifient l’état avec `GET` demandes sont vulnérables contre les attaques malveillantes.</span><span class="sxs-lookup"><span data-stu-id="4a250-126">Applications that change state with `GET` requests are  vulnerable from malicious attacks.</span></span>

<span data-ttu-id="4a250-127">Les attaques CSRF sont possibles contre les sites web qui utilisent des cookies pour l’authentification, étant donné que les navigateurs envoient tous les cookies pertinentes pour le site de destination.</span><span class="sxs-lookup"><span data-stu-id="4a250-127">CSRF attacks are possible against web sites that use cookies for authentication, because browsers send all relevant cookies to the destination web site.</span></span> <span data-ttu-id="4a250-128">Toutefois, les attaques CSRF n’êtes pas limités pour exploiter les cookies.</span><span class="sxs-lookup"><span data-stu-id="4a250-128">However, CSRF attacks are not limited to exploiting cookies.</span></span> <span data-ttu-id="4a250-129">Par exemple, l’authentification de base et Digest sont également vulnérables.</span><span class="sxs-lookup"><span data-stu-id="4a250-129">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="4a250-130">Une fois un utilisateur se connecte avec l’authentification de base ou Digest, le navigateur envoie automatiquement les informations d’identification jusqu'à ce que la session se termine.</span><span class="sxs-lookup"><span data-stu-id="4a250-130">After a user logs in with Basic or Digest authentication, the browser automatically sends the credentials until the session ends.</span></span>

<span data-ttu-id="4a250-131">Remarque : dans ce contexte, *session* fait référence à la session côté client au cours de laquelle l’utilisateur est authentifié.</span><span class="sxs-lookup"><span data-stu-id="4a250-131">Note: In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="4a250-132">Il n’est pas lié à des sessions de côté serveur ou [intergiciel (middleware) de session](xref:fundamentals/app-state).</span><span class="sxs-lookup"><span data-stu-id="4a250-132">It is unrelated to server-side sessions or [session middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="4a250-133">Les utilisateurs peuvent se protéger contre les vulnérabilités CSRF par :</span><span class="sxs-lookup"><span data-stu-id="4a250-133">Users can guard against CSRF vulnerabilities by:</span></span>
* <span data-ttu-id="4a250-134">Déconnexion de sites web lorsqu’ils ont terminé leur utilisation.</span><span class="sxs-lookup"><span data-stu-id="4a250-134">Logging off of web sites when they have finished using them.</span></span>
* <span data-ttu-id="4a250-135">Effacer les cookies de son navigateur régulièrement.</span><span class="sxs-lookup"><span data-stu-id="4a250-135">Clearing their browser's cookies periodically.</span></span>

<span data-ttu-id="4a250-136">Toutefois, les vulnérabilités CSRF sont fondamentalement un problème avec l’application web, pas l’utilisateur final.</span><span class="sxs-lookup"><span data-stu-id="4a250-136">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="how-does-aspnet-core-mvc-address-csrf"></a><span data-ttu-id="4a250-137">La manière dont ASP.NET Core MVC adressez CSRF ?</span><span class="sxs-lookup"><span data-stu-id="4a250-137">How does ASP.NET Core MVC address CSRF?</span></span>

> [!WARNING]
> <span data-ttu-id="4a250-138">ASP.NET Core implémente à l’aide de l’anti-request-falsification le [pile de protection des données ASP.NET Core](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="4a250-138">ASP.NET Core implements anti-request-forgery using the [ASP.NET Core data protection stack](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="4a250-139">Protection des données ASP.NET Core doit être configurée pour fonctionner dans une batterie de serveurs.</span><span class="sxs-lookup"><span data-stu-id="4a250-139">ASP.NET Core data protection must be configured to work in a server farm.</span></span> <span data-ttu-id="4a250-140">Consultez [configuration de la protection des données](xref:security/data-protection/configuration/overview) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="4a250-140">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

<span data-ttu-id="4a250-141">Configuration de protection de données de valeur par défaut de l’anti-request-falsification ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4a250-141">ASP.NET Core anti-request-forgery  default data protection configuration</span></span> 

<span data-ttu-id="4a250-142">Dans ASP.NET Core MVC 2.0 le [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injecte les jetons anti-contrefaçon pour les éléments de formulaire HTML.</span><span class="sxs-lookup"><span data-stu-id="4a250-142">In ASP.NET Core MVC 2.0 the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects anti-forgery tokens for HTML form elements.</span></span> <span data-ttu-id="4a250-143">Par exemple, le balisage suivant dans un fichier Razor génère automatiquement les jetons anti-contrefaçon :</span><span class="sxs-lookup"><span data-stu-id="4a250-143">For example, the following markup in a Razor file will automatically generate anti-forgery tokens:</span></span>

```html
<form method="post">
  <!-- form markup -->
</form>
```

<span data-ttu-id="4a250-144">La génération automatique des jetons anti-contrefaçon pour les éléments de formulaire HTML se produit lorsque :</span><span class="sxs-lookup"><span data-stu-id="4a250-144">The automatic generation of anti-forgery tokens for HTML form elements happens when:</span></span>

* <span data-ttu-id="4a250-145">Le `form` balise contient le `method="post"` attribut AND</span><span class="sxs-lookup"><span data-stu-id="4a250-145">The `form` tag contains the `method="post"` attribute AND</span></span>

  * <span data-ttu-id="4a250-146">L’attribut d’action est vide.</span><span class="sxs-lookup"><span data-stu-id="4a250-146">The action attribute is empty.</span></span> <span data-ttu-id="4a250-147">( `action=""`) OU</span><span class="sxs-lookup"><span data-stu-id="4a250-147">( `action=""`) OR</span></span>
  * <span data-ttu-id="4a250-148">L’attribut d’action n’est pas fourni.</span><span class="sxs-lookup"><span data-stu-id="4a250-148">The action attribute is not supplied.</span></span> <span data-ttu-id="4a250-149">(`<form method="post">`)</span><span class="sxs-lookup"><span data-stu-id="4a250-149">(`<form method="post">`)</span></span>

<span data-ttu-id="4a250-150">Vous pouvez désactiver la génération automatique d’anti-contrefaçon jetons pour les éléments de formulaire HTML en :</span><span class="sxs-lookup"><span data-stu-id="4a250-150">You can disable automatic generation of anti-forgery tokens for HTML form elements by:</span></span>

* <span data-ttu-id="4a250-151">Désactive explicitement `asp-antiforgery`.</span><span class="sxs-lookup"><span data-stu-id="4a250-151">Explicitly disabling `asp-antiforgery`.</span></span> <span data-ttu-id="4a250-152">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4a250-152">For example</span></span>

 ```html
  <form method="post" asp-antiforgery="false">
  </form>
  ```

* <span data-ttu-id="4a250-153">Choisir l’élément form en dehors des programmes d’assistance de balise à l’aide du programme d’assistance de balise [! annulations symbole](xref:mvc/views/tag-helpers/intro#opt-out).</span><span class="sxs-lookup"><span data-stu-id="4a250-153">Opt the form element out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out).</span></span>

 ```html
  <!form method="post">
  </!form>
  ```

* <span data-ttu-id="4a250-154">Supprimer le `FormTagHelper` à partir de la vue.</span><span class="sxs-lookup"><span data-stu-id="4a250-154">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="4a250-155">Vous pouvez supprimer le `FormTagHelper` à partir d’une vue en ajoutant la directive suivante à la vue Razor :</span><span class="sxs-lookup"><span data-stu-id="4a250-155">You can remove the `FormTagHelper` from a view by adding the following directive to the Razor view:</span></span>

 ```html
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="4a250-156">[Pages Razor](xref:mvc/razor-pages/index) sont automatiquement protégés contre XSRF/CSRF.</span><span class="sxs-lookup"><span data-stu-id="4a250-156">[Razor Pages](xref:mvc/razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="4a250-157">Vous n’êtes pas obligé d’écrire du code supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="4a250-157">You don't have to write any additional code.</span></span> <span data-ttu-id="4a250-158">Consultez [XSRF/CSRF et Pages Razor](xref:mvc/razor-pages/index#xsrf) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="4a250-158">See [XSRF/CSRF and Razor Pages](xref:mvc/razor-pages/index#xsrf) for more information.</span></span>

<span data-ttu-id="4a250-159">L’approche la plus courante de défense contre les attaques CSRF est le modèle de jeton synchronisateur (STP).</span><span class="sxs-lookup"><span data-stu-id="4a250-159">The most common approach to defending against CSRF attacks is the synchronizer token pattern (STP).</span></span> <span data-ttu-id="4a250-160">STP est une technique utilisée lorsque l’utilisateur demande une page de données de formulaire.</span><span class="sxs-lookup"><span data-stu-id="4a250-160">STP is a technique used when the user requests a page with form data.</span></span> <span data-ttu-id="4a250-161">Le serveur envoie un jeton avec l’identité de l’utilisateur actuel au client.</span><span class="sxs-lookup"><span data-stu-id="4a250-161">The server sends a token associated with the current user's identity to the client.</span></span> <span data-ttu-id="4a250-162">Le client envoie le jeton sur le serveur pour la vérification.</span><span class="sxs-lookup"><span data-stu-id="4a250-162">The client sends back the token to the server for verification.</span></span> <span data-ttu-id="4a250-163">Si le serveur reçoit un jeton qui ne correspond pas à identité de l’utilisateur authentifié, la demande est rejetée.</span><span class="sxs-lookup"><span data-stu-id="4a250-163">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span> <span data-ttu-id="4a250-164">Le jeton est unique et imprévisibles.</span><span class="sxs-lookup"><span data-stu-id="4a250-164">The token is unique and unpredictable.</span></span> <span data-ttu-id="4a250-165">Le jeton peut également être utilisé pour garantir une mise en séquence correcte d’une série de requêtes (page 1 de la garantie que précède la page 2, ce qui précède la page 3).</span><span class="sxs-lookup"><span data-stu-id="4a250-165">The token can also be used to ensure proper sequencing of a series of requests (ensuring page 1 precedes page 2 which precedes page 3).</span></span> <span data-ttu-id="4a250-166">Tous les formulaires dans les modèles ASP.NET MVC de base génèrent des jetons de côté.</span><span class="sxs-lookup"><span data-stu-id="4a250-166">All the forms in ASP.NET Core MVC templates generate antiforgery tokens.</span></span> <span data-ttu-id="4a250-167">Les deux exemples suivants de la logique de la vue de génèrent des jetons côtés :</span><span class="sxs-lookup"><span data-stu-id="4a250-167">The following two examples of view logic generate antiforgery tokens:</span></span>

```html
<form asp-controller="Manage" asp-action="ChangePassword" method="post">

</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    
}
```

<span data-ttu-id="4a250-168">Vous pouvez ajouter explicitement un jeton à côté d’un ``<form>`` élément sans l’aide de programmes d’assistance de balise avec l’application d’assistance HTML ``@Html.AntiForgeryToken``:</span><span class="sxs-lookup"><span data-stu-id="4a250-168">You can explicitly add an antiforgery token to a ``<form>`` element without using tag helpers with the HTML helper ``@Html.AntiForgeryToken``:</span></span>


```html
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

```html
In each of the preceding cases, ASP.NET Core will add a hidden form field similar to the following:

<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkSldwD9CpLRyOtm6FiJB1Jr_F3FQJQDvhlHoLNJJrLA6zaMUmhjMsisu2D2tFkAiYgyWQawJk9vNm36sYP1esHOtamBEPvSk1_x--Sg8Ey2a-d9CV2zHVWIN9MVhvKHOSyKqdZFlYDVd69XYx-rOWPw3ilHGLN6K0Km-1p83jZzF0E4WU5OGg5ns2-m9Yw" />
```

<span data-ttu-id="4a250-169">ASP.NET Core inclut trois [filtres](xref:mvc/controllers/filters) pour l’utilisation des jetons de côté : ``ValidateAntiForgeryToken``, ``AutoValidateAntiforgeryToken``, et ``IgnoreAntiforgeryToken``.</span><span class="sxs-lookup"><span data-stu-id="4a250-169">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens: ``ValidateAntiForgeryToken``, ``AutoValidateAntiforgeryToken``, and ``IgnoreAntiforgeryToken``.</span></span>

<a name="vaft"></a>

### <a name="validateantiforgerytoken"></a><span data-ttu-id="4a250-170">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="4a250-170">ValidateAntiForgeryToken</span></span>

<span data-ttu-id="4a250-171">Le ``ValidateAntiForgeryToken`` est un filtre d’action qui peut être appliqué à une action individuelle, un contrôleur, ou globalement.</span><span class="sxs-lookup"><span data-stu-id="4a250-171">The ``ValidateAntiForgeryToken`` is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="4a250-172">Requêtes adressées à des actions qui ont ce filtre appliqué seront bloqués, sauf si la demande inclut un jeton valide de côté.</span><span class="sxs-lookup"><span data-stu-id="4a250-172">Requests made to actions that have this filter applied will be blocked unless the request includes a valid antiforgery token.</span></span>

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

<span data-ttu-id="4a250-173">Le ``ValidateAntiForgeryToken`` attribut requiert un jeton pour les demandes de méthodes d’action qu’il décore, y compris `HTTP GET` demandes.</span><span class="sxs-lookup"><span data-stu-id="4a250-173">The ``ValidateAntiForgeryToken`` attribute requires a token for requests to action methods it decorates, including `HTTP GET` requests.</span></span> <span data-ttu-id="4a250-174">Si vous appliquez largement, vous pouvez la remplacer par la ``IgnoreAntiforgeryToken`` attribut.</span><span class="sxs-lookup"><span data-stu-id="4a250-174">If you apply it broadly, you can override it with the ``IgnoreAntiforgeryToken`` attribute.</span></span>

### <a name="autovalidateantiforgerytoken"></a><span data-ttu-id="4a250-175">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="4a250-175">AutoValidateAntiforgeryToken</span></span>

<span data-ttu-id="4a250-176">Généralement, les applications ASP.NET Core ne génèrent pas côté des jetons pour les méthodes sans échec HTTP (GET, HEAD, OPTIONS et TRACE).</span><span class="sxs-lookup"><span data-stu-id="4a250-176">ASP.NET Core apps generally do not generate antiforgery tokens for HTTP safe methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="4a250-177">Au lieu d’appliquer globalement la ``ValidateAntiForgeryToken`` attribut et en remplaçant puis avec ``IgnoreAntiforgeryToken`` attributs, vous pouvez utiliser la ``AutoValidateAntiforgeryToken`` attribut.</span><span class="sxs-lookup"><span data-stu-id="4a250-177">Instead of broadly applying the ``ValidateAntiForgeryToken`` attribute and then overriding it with ``IgnoreAntiforgeryToken`` attributes, you can use the ``AutoValidateAntiforgeryToken`` attribute.</span></span> <span data-ttu-id="4a250-178">Cet attribut fonctionne de manière identique à la ``ValidateAntiForgeryToken`` d’attribut, sauf qu’elle ne nécessite pas les jetons pour les demandes effectuées à l’aide des méthodes HTTP suivantes :</span><span class="sxs-lookup"><span data-stu-id="4a250-178">This attribute works identically to the ``ValidateAntiForgeryToken`` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="4a250-179">GET</span><span class="sxs-lookup"><span data-stu-id="4a250-179">GET</span></span>
* <span data-ttu-id="4a250-180">HEAD</span><span class="sxs-lookup"><span data-stu-id="4a250-180">HEAD</span></span>
* <span data-ttu-id="4a250-181">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="4a250-181">OPTIONS</span></span>
* <span data-ttu-id="4a250-182">TRACE</span><span class="sxs-lookup"><span data-stu-id="4a250-182">TRACE</span></span>

<span data-ttu-id="4a250-183">Nous vous recommandons d’utiliser ``AutoValidateAntiforgeryToken`` largement pour les scénarios non-API.</span><span class="sxs-lookup"><span data-stu-id="4a250-183">We recommend you use ``AutoValidateAntiforgeryToken`` broadly for non-API scenarios.</span></span> <span data-ttu-id="4a250-184">Cela garantit que vos actions POST sont protégées par défaut.</span><span class="sxs-lookup"><span data-stu-id="4a250-184">This ensures your POST actions are protected by default.</span></span> <span data-ttu-id="4a250-185">L’alternative consiste à ignorer les jetons côtés par défaut, sauf si ``ValidateAntiForgeryToken`` est appliqué à la méthode d’action individuelle.</span><span class="sxs-lookup"><span data-stu-id="4a250-185">The alternative is to ignore antiforgery tokens by default, unless ``ValidateAntiForgeryToken`` is applied to the individual action method.</span></span> <span data-ttu-id="4a250-186">Il est plus probable dans ce scénario pour une méthode d’action POST à gauche non protégé, en laissant votre application vulnérable aux attaques CSRF.</span><span class="sxs-lookup"><span data-stu-id="4a250-186">It's more likely in this scenario for a POST action method to be left unprotected, leaving your app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="4a250-187">Les publications anonymes même doivent envoyer le jeton côté.</span><span class="sxs-lookup"><span data-stu-id="4a250-187">Even anonymous POSTS should send the antiforgery token.</span></span>

<span data-ttu-id="4a250-188">Remarque : Les API n’ont aucun mécanisme automatique pour l’envoi de la partie non-cookie du jeton ; votre implémentation dépend probablement votre implémentation de code client.</span><span class="sxs-lookup"><span data-stu-id="4a250-188">Note: APIs don't have an automatic mechanism for sending the non-cookie part of the token; your implementation will likely depend on your client code implementation.</span></span> <span data-ttu-id="4a250-189">Certains exemples sont présentés ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="4a250-189">Some examples are shown below.</span></span>


<span data-ttu-id="4a250-190">Exemple (niveau de la classe) :</span><span class="sxs-lookup"><span data-stu-id="4a250-190">Example (class level):</span></span>

```c#
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="4a250-191">Exemple (global) :</span><span class="sxs-lookup"><span data-stu-id="4a250-191">Example (global):</span></span>

```c#
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

<a name="iaft"></a>

### <a name="ignoreantiforgerytoken"></a><span data-ttu-id="4a250-192">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="4a250-192">IgnoreAntiforgeryToken</span></span>

<span data-ttu-id="4a250-193">Le ``IgnoreAntiforgeryToken`` filtre est utilisé pour éliminer le besoin d’un jeton de côté être présent pour une action donnée (ou un contrôleur).</span><span class="sxs-lookup"><span data-stu-id="4a250-193">The ``IgnoreAntiforgeryToken`` filter is used to eliminate the need for an antiforgery token to be present for a given action (or controller).</span></span> <span data-ttu-id="4a250-194">Quand il est appliqué, ce filtre remplace ``ValidateAntiForgeryToken`` et/ou ``AutoValidateAntiforgeryToken`` filtres spécifiés à un niveau supérieur (globalement ou sur un contrôleur).</span><span class="sxs-lookup"><span data-stu-id="4a250-194">When applied, this filter will override ``ValidateAntiForgeryToken`` and/or ``AutoValidateAntiforgeryToken`` filters specified at a higher level (globally or on a controller).</span></span>

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

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="4a250-195">JavaScript, AJAX et SPAs</span><span class="sxs-lookup"><span data-stu-id="4a250-195">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="4a250-196">Dans les applications traditionnelles basées sur HTML côtés jetons sont passées au serveur à l’aide des champs de formulaire masqué.</span><span class="sxs-lookup"><span data-stu-id="4a250-196">In traditional HTML-based applications, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="4a250-197">Dans les applications modernes basées JavaScript et des applications à page unique (ZPS), le nombre de requêtes est effectuée par programme.</span><span class="sxs-lookup"><span data-stu-id="4a250-197">In modern JavaScript-based apps and single page applications (SPAs), many requests are made programmatically.</span></span> <span data-ttu-id="4a250-198">Ces demandes AJAX peuvent utiliser d’autres techniques (par exemple, les en-têtes de requête ou des cookies) pour envoyer le jeton.</span><span class="sxs-lookup"><span data-stu-id="4a250-198">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span> <span data-ttu-id="4a250-199">Si les cookies sont utilisés pour stocker les jetons d’authentification et pour authentifier les demandes sur le serveur de l’API, CSRF sera un problème potentiel.</span><span class="sxs-lookup"><span data-stu-id="4a250-199">If cookies are used to store authentication tokens and to authenticate API requests on the server, then CSRF will be a potential problem.</span></span> <span data-ttu-id="4a250-200">Toutefois, si le stockage local est utilisé pour stocker le jeton, CSRF vulnérabilité peut être atténuée, étant donné que les valeurs à partir du stockage local ne sont pas automatiquement envoyées au serveur à chaque nouvelle demande.</span><span class="sxs-lookup"><span data-stu-id="4a250-200">However, if local storage is used to store the token, CSRF vulnerability may be mitigated, since values from local storage are not sent automatically to the server with every new request.</span></span> <span data-ttu-id="4a250-201">Par conséquent, l’utilisation du stockage local pour stocker le jeton côté sur le client et envoie le jeton, comme un en-tête de demande est une approche recommandée.</span><span class="sxs-lookup"><span data-stu-id="4a250-201">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="angularjs"></a><span data-ttu-id="4a250-202">AngularJS</span><span class="sxs-lookup"><span data-stu-id="4a250-202">AngularJS</span></span>

<span data-ttu-id="4a250-203">AngularJS utilise une convention à l’adresse CSRF.</span><span class="sxs-lookup"><span data-stu-id="4a250-203">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="4a250-204">Si le serveur envoie un cookie avec le nom ``XSRF-TOKEN``, l’angulaire ``$http`` service, ajoutez la valeur de ce cookie à un en-tête lorsqu’il envoie une demande à ce serveur.</span><span class="sxs-lookup"><span data-stu-id="4a250-204">If the server sends a cookie with the name ``XSRF-TOKEN``, the Angular ``$http`` service will add the value from this cookie to a header when it sends a request to this server.</span></span> <span data-ttu-id="4a250-205">Ce processus est automatique ; vous n’avez pas besoin de définir l’en-tête explicitement.</span><span class="sxs-lookup"><span data-stu-id="4a250-205">This process is automatic; you don't need to set the header explicitly.</span></span> <span data-ttu-id="4a250-206">Le nom d’en-tête est ``X-XSRF-TOKEN``.</span><span class="sxs-lookup"><span data-stu-id="4a250-206">The header name is ``X-XSRF-TOKEN``.</span></span> <span data-ttu-id="4a250-207">Le serveur doit détecter cet en-tête et valider son contenu.</span><span class="sxs-lookup"><span data-stu-id="4a250-207">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="4a250-208">Pour le travail de l’API ASP.NET principale avec la convention :</span><span class="sxs-lookup"><span data-stu-id="4a250-208">For ASP.NET Core API work with this convention:</span></span>

* <span data-ttu-id="4a250-209">Configurer votre application pour fournir un jeton dans un cookie appelé``XSRF-TOKEN``</span><span class="sxs-lookup"><span data-stu-id="4a250-209">Configure your app to provide a token in a cookie called ``XSRF-TOKEN``</span></span>
* <span data-ttu-id="4a250-210">Configurer le côté service pour rechercher un en-tête nommé``X-XSRF-TOKEN``</span><span class="sxs-lookup"><span data-stu-id="4a250-210">Configure the antiforgery service to look for a header named ``X-XSRF-TOKEN``</span></span>

```c#
services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
```

<span data-ttu-id="4a250-211">[Exemple d’affichage](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample).</span><span class="sxs-lookup"><span data-stu-id="4a250-211">[View sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample).</span></span>

### <a name="javascript"></a><span data-ttu-id="4a250-212">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4a250-212">JavaScript</span></span>

<span data-ttu-id="4a250-213">À l’aide de JavaScript avec des vues, vous pouvez créer le jeton à l’aide d’un service à partir de votre affichage.</span><span class="sxs-lookup"><span data-stu-id="4a250-213">Using JavaScript with views, you can create the token using a service from within your view.</span></span> <span data-ttu-id="4a250-214">Pour ce faire, vous injectez la `Microsoft.AspNetCore.Antiforgery.IAntiforgery` service dans la vue et appelez `GetAndStoreTokens`, comme indiqué :</span><span class="sxs-lookup"><span data-stu-id="4a250-214">To do so, you inject the `Microsoft.AspNetCore.Antiforgery.IAntiforgery` service into the view and call `GetAndStoreTokens`, as shown:</span></span>

<span data-ttu-id="4a250-215">[!code-csharp[Main](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,24)]</span><span class="sxs-lookup"><span data-stu-id="4a250-215">[!code-csharp[Main](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,24)]</span></span>

<span data-ttu-id="4a250-216">Cette approche élimine le besoin de traiter directement avec la définition des cookies à partir du serveur ou de les lire à partir du client.</span><span class="sxs-lookup"><span data-stu-id="4a250-216">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="4a250-217">JavaScript permettre également accéder aux jetons fournis dans des cookies et puis permet de contenu du cookie créer un en-tête avec sa valeur, comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="4a250-217">JavaScript can also access tokens provided in cookies, and then use the cookie's contents to create a header with the token's value, as shown below.</span></span>

```c#
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
  new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="4a250-218">Puis, en supposant que la construction de votre script demande à envoyer le jeton dans un en-tête appelé ``X-CSRF-TOKEN``, configurer le côté service pour rechercher le ``X-CSRF-TOKEN`` en-tête :</span><span class="sxs-lookup"><span data-stu-id="4a250-218">Then, assuming you construct your script requests to send the token in a header called ``X-CSRF-TOKEN``, configure the antiforgery service to look for the ``X-CSRF-TOKEN`` header:</span></span>

```c#
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="4a250-219">L’exemple suivant utilise jQuery pour effectuer une requête AJAX avec l’en-tête approprié :</span><span class="sxs-lookup"><span data-stu-id="4a250-219">The following example uses jQuery to make an AJAX request with the appropriate header:</span></span>

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

## <a name="configuring-antiforgery"></a><span data-ttu-id="4a250-220">Configuration Antiforgery</span><span class="sxs-lookup"><span data-stu-id="4a250-220">Configuring Antiforgery</span></span>

<span data-ttu-id="4a250-221">`IAntiforgery`fournit l’API pour configurer le système de côté.</span><span class="sxs-lookup"><span data-stu-id="4a250-221">`IAntiforgery` provides the API to configure the antiforgery system.</span></span> <span data-ttu-id="4a250-222">Il peut être demandé dans le `Configure` méthode de la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="4a250-222">It can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="4a250-223">L’exemple suivant utilise l’intergiciel (middleware) à partir de la page d’accueil de l’application pour générer un jeton de côté et l’envoyer dans la réponse sous forme de cookie (à l’aide de la convention de dénomination angulaire par défaut est décrite ci-dessus) :</span><span class="sxs-lookup"><span data-stu-id="4a250-223">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described above):</span></span>


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

### <a name="options"></a><span data-ttu-id="4a250-224">Options</span><span class="sxs-lookup"><span data-stu-id="4a250-224">Options</span></span>

<span data-ttu-id="4a250-225">Vous pouvez personnaliser [options côtées](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions#fields_summary) dans `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4a250-225">You can customize [antiforgery options](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions#fields_summary) in `ConfigureServices`:</span></span>

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

|<span data-ttu-id="4a250-226">Option</span><span class="sxs-lookup"><span data-stu-id="4a250-226">Option</span></span>        | <span data-ttu-id="4a250-227">Description</span><span class="sxs-lookup"><span data-stu-id="4a250-227">Description</span></span> |
|------------- | ----------- |
|<span data-ttu-id="4a250-228">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="4a250-228">CookieDomain</span></span>  | <span data-ttu-id="4a250-229">Le domaine du cookie.</span><span class="sxs-lookup"><span data-stu-id="4a250-229">The domain of the cookie.</span></span> <span data-ttu-id="4a250-230">La valeur par défaut est `null`.</span><span class="sxs-lookup"><span data-stu-id="4a250-230">Defaults to `null`.</span></span> |
|<span data-ttu-id="4a250-231">CookieName</span><span class="sxs-lookup"><span data-stu-id="4a250-231">CookieName</span></span>    | <span data-ttu-id="4a250-232">Le nom du cookie.</span><span class="sxs-lookup"><span data-stu-id="4a250-232">The name of the cookie.</span></span> <span data-ttu-id="4a250-233">Si non définie, le système génère un nom unique commençant par le `DefaultCookiePrefix` ( ». AspNetCore.Antiforgery. »).</span><span class="sxs-lookup"><span data-stu-id="4a250-233">If not set, the system will generate a unique name beginning with the `DefaultCookiePrefix` (".AspNetCore.Antiforgery.").</span></span> |
|<span data-ttu-id="4a250-234">CookiePath</span><span class="sxs-lookup"><span data-stu-id="4a250-234">CookiePath</span></span>    | <span data-ttu-id="4a250-235">Le chemin d’accès défini sur le cookie.</span><span class="sxs-lookup"><span data-stu-id="4a250-235">The path set on the cookie.</span></span> |
|<span data-ttu-id="4a250-236">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="4a250-236">FormFieldName</span></span> | <span data-ttu-id="4a250-237">Le nom du champ de formulaire masqué utilisé par le système de côté pour effectuer le rendu côté des jetons dans les vues.</span><span class="sxs-lookup"><span data-stu-id="4a250-237">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
|<span data-ttu-id="4a250-238">HeaderName</span><span class="sxs-lookup"><span data-stu-id="4a250-238">HeaderName</span></span>    | <span data-ttu-id="4a250-239">Le nom de l’en-tête utilisé par le système de côté.</span><span class="sxs-lookup"><span data-stu-id="4a250-239">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="4a250-240">Si `null`, le système considère uniquement les données de formulaire.</span><span class="sxs-lookup"><span data-stu-id="4a250-240">If `null`, the system will consider only form data.</span></span> |
|<span data-ttu-id="4a250-241">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="4a250-241">RequireSsl</span></span>    | <span data-ttu-id="4a250-242">Spécifie si SSL est requise par le système de côté.</span><span class="sxs-lookup"><span data-stu-id="4a250-242">Specifies whether SSL is required by the antiforgery system.</span></span> <span data-ttu-id="4a250-243">La valeur par défaut est `false`.</span><span class="sxs-lookup"><span data-stu-id="4a250-243">Defaults to `false`.</span></span> <span data-ttu-id="4a250-244">Si `true`, les demandes non-SSL échouent.</span><span class="sxs-lookup"><span data-stu-id="4a250-244">If `true`, non-SSL requests will fail.</span></span> |
|<span data-ttu-id="4a250-245">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="4a250-245">SuppressXFrameOptionsHeader</span></span>  | <span data-ttu-id="4a250-246">Spécifie s’il faut supprimer la génération de la `X-Frame-Options` en-tête.</span><span class="sxs-lookup"><span data-stu-id="4a250-246">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="4a250-247">Par défaut, l’en-tête est généré avec la valeur « SAMEORIGIN ».</span><span class="sxs-lookup"><span data-stu-id="4a250-247">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="4a250-248">La valeur par défaut est `false`.</span><span class="sxs-lookup"><span data-stu-id="4a250-248">Defaults to `false`.</span></span> |

<span data-ttu-id="4a250-249">Https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.cookieauthenticationoptions pour plus d’informations, consultez.</span><span class="sxs-lookup"><span data-stu-id="4a250-249">See https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.cookieauthenticationoptions for more info.</span></span>

### <a name="extending-antiforgery"></a><span data-ttu-id="4a250-250">Extension Antiforgery</span><span class="sxs-lookup"><span data-stu-id="4a250-250">Extending Antiforgery</span></span>

<span data-ttu-id="4a250-251">Le [IAntiForgeryAdditionalDataProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) permet aux développeurs d’étendre le comportement du système anti-XSRF par des données supplémentaires aller-retour dans chaque jeton.</span><span class="sxs-lookup"><span data-stu-id="4a250-251">The [IAntiForgeryAdditionalDataProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-XSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="4a250-252">Le [GetAdditionalData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider#Microsoft_AspNetCore_Antiforgery_IAntiforgeryAdditionalDataProvider_GetAdditionalData_Microsoft_AspNetCore_Http_HttpContext_) méthode est appelée chaque fois qu’un jeton de champ est généré, et la valeur de retour est incorporée dans le jeton généré.</span><span class="sxs-lookup"><span data-stu-id="4a250-252">The [GetAdditionalData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider#Microsoft_AspNetCore_Antiforgery_IAntiforgeryAdditionalDataProvider_GetAdditionalData_Microsoft_AspNetCore_Http_HttpContext_) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="4a250-253">Un implémenteur peut retourner un horodatage, une valeur à usage unique ou toute autre valeur, puis appelez [ValidateAdditionalData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider#Microsoft_AspNetCore_Antiforgery_IAntiforgeryAdditionalDataProvider_ValidateAdditionalData_Microsoft_AspNetCore_Http_HttpContext_System_String_) pour valider ces données lorsque le jeton est validé.</span><span class="sxs-lookup"><span data-stu-id="4a250-253">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider#Microsoft_AspNetCore_Antiforgery_IAntiforgeryAdditionalDataProvider_ValidateAdditionalData_Microsoft_AspNetCore_Http_HttpContext_System_String_) to validate this data when the token is validated.</span></span> <span data-ttu-id="4a250-254">Nom d’utilisateur du client est déjà incorporée dans les jetons générés, il est donc inutile d’inclure ces informations.</span><span class="sxs-lookup"><span data-stu-id="4a250-254">The client's username is already embedded in the generated tokens, so there is no need to include this information.</span></span> <span data-ttu-id="4a250-255">Si un jeton contient des données supplémentaires, mais non `IAntiForgeryAdditionalDataProvider` a été configuré, les données supplémentaires ne sont pas validées.</span><span class="sxs-lookup"><span data-stu-id="4a250-255">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` has been configured, the supplemental data is not validated.</span></span>

## <a name="fundamentals"></a><span data-ttu-id="4a250-256">Notions de base</span><span class="sxs-lookup"><span data-stu-id="4a250-256">Fundamentals</span></span>

<span data-ttu-id="4a250-257">Les attaques CSRF reposent sur le comportement du navigateur par défaut de l’envoi de cookies associés à un domaine avec chaque demande adressée à ce domaine.</span><span class="sxs-lookup"><span data-stu-id="4a250-257">CSRF attacks rely on the default browser behavior of sending cookies associated with a domain with every request made to that domain.</span></span> <span data-ttu-id="4a250-258">Ces cookies sont stockés dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="4a250-258">These cookies are stored within the browser.</span></span> <span data-ttu-id="4a250-259">Ils incluent souvent des cookies de session pour les utilisateurs authentifiés.</span><span class="sxs-lookup"><span data-stu-id="4a250-259">They frequently include session cookies for authenticated users.</span></span> <span data-ttu-id="4a250-260">L’authentification basée sur le cookie est une forme courante d’authentification.</span><span class="sxs-lookup"><span data-stu-id="4a250-260">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="4a250-261">Systèmes d’authentification par jeton ont été gagnent en popularité, en particulier pour SPAs et autres scénarios « smart client ».</span><span class="sxs-lookup"><span data-stu-id="4a250-261">Token-based authentication systems have been growing in popularity, especially for SPAs and other "smart client" scenarios.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="4a250-262">Authentification par cookie</span><span class="sxs-lookup"><span data-stu-id="4a250-262">Cookie-based authentication</span></span>

<span data-ttu-id="4a250-263">Une fois qu’un utilisateur est authentifié à l’aide de leur nom d’utilisateur et un mot de passe, ils sont émis un jeton qui peut être utilisé pour les identifier et de valider qu’ils ont été authentifiées.</span><span class="sxs-lookup"><span data-stu-id="4a250-263">Once a user has authenticated using their username and password, they are issued a token that can be used to identify them and validate that they have been authenticated.</span></span> <span data-ttu-id="4a250-264">Le jeton est stocké en tant que permet d’un cookie qui accompagne chaque demande du client.</span><span class="sxs-lookup"><span data-stu-id="4a250-264">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="4a250-265">Génération et la validation de ce cookie sont effectuée par l’intergiciel (middleware) d’authentification de cookie.</span><span class="sxs-lookup"><span data-stu-id="4a250-265">Generating and validating this cookie is done by the cookie authentication middleware.</span></span> <span data-ttu-id="4a250-266">ASP.NET Core fournit le cookie [intergiciel (middleware)](../fundamentals/middleware.md) qui sérialise un principal d’utilisateur dans un cookie chiffré et puis, sur les demandes suivantes, valide le cookie, recrée le principal et l’affecte à la `User` propriété sur `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="4a250-266">ASP.NET Core provides cookie [middleware](../fundamentals/middleware.md) which serializes a user principal into an encrypted cookie and then, on subsequent requests, validates the cookie, recreates the principal and assigns it to the `User` property on `HttpContext`.</span></span>

<span data-ttu-id="4a250-267">Lorsqu’un cookie est utilisé, le cookie d’authentification est simplement un conteneur pour le ticket d’authentification de formulaires.</span><span class="sxs-lookup"><span data-stu-id="4a250-267">When a cookie is used, The authentication cookie is just a container for the forms authentication ticket.</span></span> <span data-ttu-id="4a250-268">Le ticket est passé en tant que la valeur du cookie d’authentification forms avec chaque demande et est utilisé par l’authentification par formulaire, sur le serveur, pour identifier un utilisateur authentifié.</span><span class="sxs-lookup"><span data-stu-id="4a250-268">The ticket is passed as the value of the forms authentication cookie with each request and is used by forms authentication, on the server, to identify an authenticated user.</span></span>

<span data-ttu-id="4a250-269">Lorsqu’un utilisateur est connecté à un système, une session utilisateur est créée sur le côté serveur et est stockée dans une base de données ou d’un autre magasin persistant.</span><span class="sxs-lookup"><span data-stu-id="4a250-269">When a user is logged in to a system, a user session is created on the server-side and is stored in a database or some other persistent store.</span></span> <span data-ttu-id="4a250-270">Le système génère une clé de session qui pointe vers la session réelle dans le magasin de données et il est envoyé sous la forme d’un cookie de côté client.</span><span class="sxs-lookup"><span data-stu-id="4a250-270">The system generates a session key that points to the actual session in the data store and it is sent as a client side cookie.</span></span> <span data-ttu-id="4a250-271">Le serveur web vérifie cette clé de session lorsqu’un utilisateur demande une ressource qui nécessite une autorisation.</span><span class="sxs-lookup"><span data-stu-id="4a250-271">The web server will check this session key any time a user requests a resource that requires authorization.</span></span> <span data-ttu-id="4a250-272">Le système vérifie si la session utilisateur associée est autorisé à accéder à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="4a250-272">The system checks whether the associated user session has the privilege to access the requested resource.</span></span> <span data-ttu-id="4a250-273">Dans ce cas, la demande se poursuit.</span><span class="sxs-lookup"><span data-stu-id="4a250-273">If so, the request continues.</span></span> <span data-ttu-id="4a250-274">Dans le cas contraire, la demande retourne en tant que non autorisé.</span><span class="sxs-lookup"><span data-stu-id="4a250-274">Otherwise, the request returns as not authorized.</span></span> <span data-ttu-id="4a250-275">Dans cette approche, les cookies sont utilisés pour rendre l’application semble être avec état, car il est en mesure de « se souviennent » que l’utilisateur a précédemment authentifié avec le serveur.</span><span class="sxs-lookup"><span data-stu-id="4a250-275">In this approach, cookies are used to make the application appear to be stateful, since it is able to "remember" that the user has previously authenticated with the server.</span></span>

### <a name="user-tokens"></a><span data-ttu-id="4a250-276">Jetons d’utilisateur</span><span class="sxs-lookup"><span data-stu-id="4a250-276">User tokens</span></span>

<span data-ttu-id="4a250-277">L’authentification basée sur le jeton ne stocke pas session sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="4a250-277">Token-based authentication doesn’t store session on the server.</span></span> <span data-ttu-id="4a250-278">En revanche, lorsqu’un utilisateur est connecté, ils sont émis un jeton (pas un jeton côté).</span><span class="sxs-lookup"><span data-stu-id="4a250-278">Instead, when a user is logged in they are issued a token (not an antiforgery token).</span></span> <span data-ttu-id="4a250-279">Ce jeton conserve toutes les données qui sont nécessaire pour valider le jeton.</span><span class="sxs-lookup"><span data-stu-id="4a250-279">This token holds all the data that is required to validate the token.</span></span> <span data-ttu-id="4a250-280">Il contient également des informations d’utilisateur, sous la forme de [revendications](https://docs.microsoft.com/dotnet/framework/security/claims-based-identity-model).</span><span class="sxs-lookup"><span data-stu-id="4a250-280">It also contains user information, in the form of [claims](https://docs.microsoft.com/dotnet/framework/security/claims-based-identity-model).</span></span> <span data-ttu-id="4a250-281">Lorsqu’un utilisateur souhaite accéder à une ressource de serveur nécessitant une authentification, le jeton est envoyé au serveur avec un en-tête d’autorisation supplémentaires sous forme de porteur {jeton}.</span><span class="sxs-lookup"><span data-stu-id="4a250-281">When a user wants to access a server resource requiring authentication, the token is sent to the server with an additional authorization header in form of Bearer {token}.</span></span> <span data-ttu-id="4a250-282">Cela rend l’application sans état, car dans chaque demande ultérieure le jeton est passé dans la demande pour la validation côté serveur.</span><span class="sxs-lookup"><span data-stu-id="4a250-282">This makes the application stateless since in each subsequent request the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="4a250-283">Ce jeton n’est pas *chiffrées*; il s’agit plutôt *codé*.</span><span class="sxs-lookup"><span data-stu-id="4a250-283">This token is not *encrypted*; rather it is *encoded*.</span></span> <span data-ttu-id="4a250-284">Sur le côté serveur, le jeton peut être décodé pour accéder aux informations brutes dans le jeton.</span><span class="sxs-lookup"><span data-stu-id="4a250-284">On the server-side the token can be decoded to access the raw information within the token.</span></span> <span data-ttu-id="4a250-285">Pour envoyer le jeton dans les demandes suivantes, vous pouvez soit l’enregistrer dans le stockage local du navigateur ou dans un cookie.</span><span class="sxs-lookup"><span data-stu-id="4a250-285">To send the token in subsequent requests, you can either store it in browser’s local storage or in a cookie.</span></span> <span data-ttu-id="4a250-286">Vous n’avez pas à vous soucier de vulnérabilité XSRF si ce dernier est stocké dans le stockage local, mais il s’agit d’un problème si le jeton est stocké dans un cookie.</span><span class="sxs-lookup"><span data-stu-id="4a250-286">You don’t have to worry about XSRF vulnerability if your token is stored in the local storage, but it is an issue if the token is stored in a cookie.</span></span>

### <a name="multiple-applications-are-hosted-in-one-domain"></a><span data-ttu-id="4a250-287">Plusieurs applications sont hébergées dans un domaine</span><span class="sxs-lookup"><span data-stu-id="4a250-287">Multiple applications are hosted in one domain</span></span>

<span data-ttu-id="4a250-288">Bien que `example1.cloudapp.net` et `example2.cloudapp.net` sont des hôtes différents, il existe une relation d’approbation implicite entre tous les hôtes de le `*.cloudapp.net` domaine.</span><span class="sxs-lookup"><span data-stu-id="4a250-288">Even though `example1.cloudapp.net` and `example2.cloudapp.net` are different hosts, there is an implicit trust relationship between all hosts under the `*.cloudapp.net` domain.</span></span> <span data-ttu-id="4a250-289">Cette relation de confiance implicite permet à des hôtes potentiellement non fiables affecter l’autre les cookies (les même origine les stratégies qui régissent les requêtes AJAX ne pas nécessairement s’appliquent pour les cookies HTTP).</span><span class="sxs-lookup"><span data-stu-id="4a250-289">This implicit trust relationship allows potentially untrusted hosts to affect each other’s cookies (the same-origin policies that govern AJAX requests do not necessarily apply to HTTP cookies).</span></span> <span data-ttu-id="4a250-290">Le runtime ASP.NET Core permet une limitation dans la mesure où le nom d’utilisateur est incorporée dans le jeton de champ, donc, même si un sous-domaine malveillant est en mesure de remplacer un jeton de session qu’il est impossible de générer un jeton de champ valide pour l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4a250-290">The ASP.NET Core runtime provides some mitigation in that the username is embedded into the field token, so even if a malicious subdomain is able to overwrite a session token it will be unable to generate a valid field token for the user.</span></span> <span data-ttu-id="4a250-291">Toutefois, lorsqu’il est hébergé dans un tel environnement les routines intégrées anti-XSRF toujours ne peut pas protéger contre le piratage de session ou connexion CSRF contre les attaques.</span><span class="sxs-lookup"><span data-stu-id="4a250-291">However, when hosted in such an environment the built-in anti-XSRF routines still cannot defend against session hijacking or login CSRF attacks.</span></span> <span data-ttu-id="4a250-292">Environnements d’hébergement partagés sont vunerable le piratage de session, connexion CSRF et autres attaques.</span><span class="sxs-lookup"><span data-stu-id="4a250-292">Shared hosting environments are vunerable to session hijacking, login CSRF, and other attacks.</span></span>


### <a name="additional-resources"></a><span data-ttu-id="4a250-293">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="4a250-293">Additional Resources</span></span>

* <span data-ttu-id="4a250-294">[XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) sur [ouvrir le projet de sécurité d’Application Web](https://www.owasp.org/index.php/Main_Page) (OWASP avoir).</span><span class="sxs-lookup"><span data-stu-id="4a250-294">[XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
