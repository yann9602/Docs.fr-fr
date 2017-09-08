---
title: "Prévention des attaques de redirection ouvert dans une application ASP.NET Core | Documents Microsoft"
author: ardalis
description: "Montre comment empêcher des attaques de redirection ouvert par rapport à une application ASP.NET Core"
keywords: "Attaque de redirection ouvert ASP.NET Core, la sécurité,"
ms.author: riande
manager: wpickett
ms.date: 07/7/2017
ms.topic: article
ms.assetid: 4604e563-e91a-4ecd-b7ed-00b3f1eee2b5
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/preventing-open-redirects
ms.openlocfilehash: 7a62b08d641de5a9df5c2f7d89bf6bf97ed8e39d
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="preventing-open-redirect-attacks-in-an-aspnet-core-app"></a><span data-ttu-id="ed6e1-104">Prévention des attaques de redirection ouvert dans une application ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ed6e1-104">Preventing Open Redirect Attacks in an ASP.NET Core app</span></span>

<span data-ttu-id="ed6e1-105">Une application web qui effectue une redirection vers une URL qui est spécifiée via la demande tels que les données de chaîne de requête ou un formulaire peut potentiellement être falsifiée rediriger les utilisateurs vers une URL externe, malveillante.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-105">A web app that redirects to a URL that is specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="ed6e1-106">Cette manipulation est appelée une attaque de redirection ouvert.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-106">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="ed6e1-107">Chaque fois que votre logique d’application redirige vers une URL spécifiée, vous devez vérifier que l’URL de redirection n’a pas été falsifié.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-107">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="ed6e1-108">ASP.NET Core intègre des fonctionnalités pour aider à protéger les applications contre les attaques de redirection ouvert (également appelé open redirection).</span><span class="sxs-lookup"><span data-stu-id="ed6e1-108">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="ed6e1-109">Qu’est une attaque de redirection ouvrir ?</span><span class="sxs-lookup"><span data-stu-id="ed6e1-109">What is an open redirect attack?</span></span>

<span data-ttu-id="ed6e1-110">Les applications Web rediriger fréquemment les utilisateurs vers une page de connexion lorsqu’ils accèdent aux ressources qui requièrent une authentification.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-110">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="ed6e1-111">La redirection typlically inclut un `returnUrl` paramètre querystring afin que l’utilisateur peut être retourné à l’URL demandée à l’origine une fois qu’ils ont connecté avec succès.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-111">The redirection typlically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="ed6e1-112">Une fois que l’utilisateur s’authentifie, ils sont redirigés vers l’URL qui avait initialement demandée.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-112">After the user authenticates, they are redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="ed6e1-113">Étant donné que l’URL de destination est spécifié dans la chaîne de requête de la demande, un utilisateur malveillant peut falsifier la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-113">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="ed6e1-114">Une chaîne de requête falsifiée pourrait permettre au site pour rediriger l’utilisateur vers un site externe, malveillant.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-114">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="ed6e1-115">Cette technique est appelée une attaque de redirection (ou une redirection) ouverte.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-115">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="ed6e1-116">Une attaque de l’exemple</span><span class="sxs-lookup"><span data-stu-id="ed6e1-116">An example attack</span></span>

<span data-ttu-id="ed6e1-117">Un utilisateur malveillant peut développer une attaque conçue pour permettre l’accès utilisateur malveillant pour les informations d’identification d’un utilisateur ou les informations sensibles sur votre application.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-117">A malicious user could develop an attack intended to allow the malicious user access to a user's credentials or sensitive information on your app.</span></span> <span data-ttu-id="ed6e1-118">Pour lancer l’attaque, elles convaincre l’utilisateur clique sur un lien vers la page de connexion de votre site, avec un `returnUrl` valeur de chaîne de requête ajoutée à l’URL.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-118">To begin the attack, they convince the user to click a link to your site's login page, with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="ed6e1-119">Par exemple, le [NerdDinner.com](http://nerddinner.com) exemple d’application (écrite pour ASP.NET MVC) inclut ce une page de connexion ici : ``http://nerddinner.com/Account/LogOn?returnUrl=/Home/About``.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-119">For example, the [NerdDinner.com](http://nerddinner.com) sample application (written for ASP.NET MVC) includes such a login page here: ``http://nerddinner.com/Account/LogOn?returnUrl=/Home/About``.</span></span> <span data-ttu-id="ed6e1-120">L’attaque puis procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="ed6e1-120">The attack then follows these steps:</span></span>

1. <span data-ttu-id="ed6e1-121">Utilisateur clique sur un lien vers ``http://nerddinner.com/Account/LogOn?returnUrl=http://nerddiner.com/Account/LogOn`` (Notez que la deuxième URL est nerddi**n**er, pas nerddi**nn**er).</span><span class="sxs-lookup"><span data-stu-id="ed6e1-121">User clicks a link to ``http://nerddinner.com/Account/LogOn?returnUrl=http://nerddiner.com/Account/LogOn`` (note, second URL is nerddi**n**er, not nerddi**nn**er).</span></span>
2. <span data-ttu-id="ed6e1-122">L’utilisateur parvient à se connecter.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-122">The user logs in successfully.</span></span>
3. <span data-ttu-id="ed6e1-123">L’utilisateur est redirigé (par le site) à ``http://nerddiner.com/Account/LogOn`` (site malveillant qui ressemble à un site réel).</span><span class="sxs-lookup"><span data-stu-id="ed6e1-123">The user is redirected (by the site) to ``http://nerddiner.com/Account/LogOn`` (malicious site that looks like real site).</span></span>
4. <span data-ttu-id="ed6e1-124">L’utilisateur se connecte à nouveau (donnant malveillant leurs informations d’identification de site) et est redirigé vers le site réel.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-124">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="ed6e1-125">L’utilisateur sera pensez probablement de leur première tentative de connexion a échoué, et leur autre a réussi.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-125">The user will likely believe their first attempt to log in failed, and their second one was successful.</span></span> <span data-ttu-id="ed6e1-126">Ils allez restent très probablement pas au courant leurs informations d’identification ont été compromises.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-126">They'll most likely remain unaware their credentials have been compromised.</span></span>

![Processus d’attaque Redirection ouvert](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="ed6e1-128">En plus des pages de connexion, certains sites fournissent des points de terminaison ou des pages de redirection.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-128">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="ed6e1-129">Imaginez que votre application comprend une page avec une redirection d’ouvrir, ``/Home/Redirect``.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-129">Imagine your app has a page with an open redirect, ``/Home/Redirect``.</span></span> <span data-ttu-id="ed6e1-130">Un attaquant pourrait créer, par exemple, un lien dans un message électronique qui accède à ``[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login``.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-130">An attacker could create, for example, a link in an email that goes to ``[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login``.</span></span> <span data-ttu-id="ed6e1-131">Un utilisateur à l’URL et voir qu'il commence par le nom de votre site.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-131">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="ed6e1-132">Approbation qui, elles devra cliquer sur le lien.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-132">Trusting that, they will click the link.</span></span> <span data-ttu-id="ed6e1-133">La redirection ouvrir envoie ensuite l’utilisateur vers le site de hameçonnage, qui est identique à la vôtre, et l’utilisateur aurait probablement connexion pour qu’ils pensent est votre site.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-133">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="ed6e1-134">Protection contre les attaques de redirection ouvert</span><span class="sxs-lookup"><span data-stu-id="ed6e1-134">Protecting against open redirect attacks</span></span>

<span data-ttu-id="ed6e1-135">Lorsque vous développez des applications web, traiter toutes les données fournies par l’utilisateur comme non fiables.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-135">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="ed6e1-136">Si votre application possède des fonctionnalités qui redirige l’utilisateur en fonction du contenu de l’URL, vérifiez que ces redirections sont effectuées uniquement localement dans votre application (ou à une URL connue, pas une URL qui peut être fournie dans la chaîne de requête).</span><span class="sxs-lookup"><span data-stu-id="ed6e1-136">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="ed6e1-137">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="ed6e1-137">LocalRedirect</span></span>

<span data-ttu-id="ed6e1-138">Utilisez le ``LocalRedirect`` méthode d’assistance à partir de la base de `Controller` classe :</span><span class="sxs-lookup"><span data-stu-id="ed6e1-138">Use the ``LocalRedirect`` helper method from the base `Controller` class:</span></span>

```
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="ed6e1-139">``LocalRedirect``lève une exception si une URL non local est spécifiée.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-139">``LocalRedirect`` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="ed6e1-140">Dans le cas contraire, il se comporte comme le ``Redirect`` (méthode).</span><span class="sxs-lookup"><span data-stu-id="ed6e1-140">Otherwise, it behaves just like the ``Redirect`` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="ed6e1-141">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="ed6e1-141">IsLocalUrl</span></span>

<span data-ttu-id="ed6e1-142">Utilisez le [IsLocalUrl](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.iurlhelper#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) méthode pour tester l’URL avant de rediriger les :</span><span class="sxs-lookup"><span data-stu-id="ed6e1-142">Use the [IsLocalUrl](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.iurlhelper#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="ed6e1-143">L’exemple suivant montre comment vérifier si une URL est locale avant de rediriger.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-143">The following example shows how to check whether a URL is local before redirecting.</span></span>

```
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

<span data-ttu-id="ed6e1-144">Le `IsLocalUrl` méthode protège les utilisateurs en cours par inadvertance redirigés vers un site malveillant.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-144">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="ed6e1-145">Vous pouvez consigner les détails de l’URL qui a été fourni lorsqu’une URL non local est fournie dans une situation dans laquelle vous vous attendiez une URL locale.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-145">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="ed6e1-146">Journalisation de redirection URL peuvent faciliter le diagnostic des attaques de redirection.</span><span class="sxs-lookup"><span data-stu-id="ed6e1-146">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
