---
title: "Brève enquête d’autres fournisseurs d’authentification."
author: rick-anderson
ms.author: riande
manager: wpickett
ms.date: 11/3/2016
ms.topic: article
ms.assetid: BC36CA84-3DE8-496E-9AA2-2F1B74AE8309
ms.prod: asp.net-core
uid: security/authentication/otherlogins
ms.openlocfilehash: 421db8c89e01cebba0c1f98cc9286288a75777f2
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="short-survey-of-other-authentication-providers"></a><span data-ttu-id="61674-102">Brève enquête d’autres fournisseurs d’authentification</span><span class="sxs-lookup"><span data-stu-id="61674-102">Short survey of other authentication providers</span></span>

<a name=security-authentication-other-logins></a>

<span data-ttu-id="61674-103">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), et [Valeriy Novytskyy](https://github.com/01binary)</span><span class="sxs-lookup"><span data-stu-id="61674-103">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), and [Valeriy Novytskyy](https://github.com/01binary)</span></span>

<span data-ttu-id="61674-104">Sont définir ici des instructions pour que d’autres fournisseurs OAuth courantes.</span><span class="sxs-lookup"><span data-stu-id="61674-104">Here are set up instructions for some other common OAuth providers.</span></span> <span data-ttu-id="61674-105">Les packages NuGet tiers tels que ceux gérés par [aspnet-cotisation](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth) peut être utilisé pour compléter les fournisseurs d’authentification implémentées par l’équipe ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="61674-105">Third-party NuGet packages such as the ones maintained by [aspnet-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth) can be used to complement authentication providers implemented by the ASP.NET Core team.</span></span>

* <span data-ttu-id="61674-106">Configurer **LinkedIn** connectez-vous : [https://developer.linkedin.com/my-apps](https://developer.linkedin.com/my-apps).</span><span class="sxs-lookup"><span data-stu-id="61674-106">Set up **LinkedIn** sign in: [https://developer.linkedin.com/my-apps](https://developer.linkedin.com/my-apps).</span></span> <span data-ttu-id="61674-107">Consultez [étapes officiels](https://developer.linkedin.com/docs/oauth2).</span><span class="sxs-lookup"><span data-stu-id="61674-107">See [official steps](https://developer.linkedin.com/docs/oauth2).</span></span>

* <span data-ttu-id="61674-108">Configurer **Instagram** connectez-vous : [https://www.instagram.com/developer/clients/manage](https://www.instagram.com/developer/clients/manage).</span><span class="sxs-lookup"><span data-stu-id="61674-108">Set up **Instagram** sign in: [https://www.instagram.com/developer/clients/manage](https://www.instagram.com/developer/clients/manage).</span></span> <span data-ttu-id="61674-109">Consultez [étapes officiels](https://www.instagram.com/developer/authentication/).</span><span class="sxs-lookup"><span data-stu-id="61674-109">See [official steps](https://www.instagram.com/developer/authentication/).</span></span>

* <span data-ttu-id="61674-110">Configurer **Reddit** connectez-vous : [https://www.reddit.com/prefs/apps](https://www.reddit.com/prefs/apps).</span><span class="sxs-lookup"><span data-stu-id="61674-110">Set up **Reddit** sign in: [https://www.reddit.com/prefs/apps](https://www.reddit.com/prefs/apps).</span></span> <span data-ttu-id="61674-111">Consultez [étapes officiels](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example).</span><span class="sxs-lookup"><span data-stu-id="61674-111">See [official steps](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example).</span></span>

* <span data-ttu-id="61674-112">Configurer **Github** connectez-vous : [https://github.com/settings/applications/new](https://github.com/settings/applications/new).</span><span class="sxs-lookup"><span data-stu-id="61674-112">Set up **Github** sign in: [https://github.com/settings/applications/new](https://github.com/settings/applications/new).</span></span> <span data-ttu-id="61674-113">Consultez [étapes officiels](https://developer.github.com/v3/oauth/).</span><span class="sxs-lookup"><span data-stu-id="61674-113">See [official steps](https://developer.github.com/v3/oauth/).</span></span>

* <span data-ttu-id="61674-114">Configurer **Yahoo** connectez-vous : [https://developer.yahoo.com/apps/create/](https://developer.yahoo.com/apps/create/).</span><span class="sxs-lookup"><span data-stu-id="61674-114">Set up **Yahoo** sign in: [https://developer.yahoo.com/apps/create/](https://developer.yahoo.com/apps/create/).</span></span> <span data-ttu-id="61674-115">Consultez [étapes officiels](https://developer.yahoo.com/bbauth/user.html).</span><span class="sxs-lookup"><span data-stu-id="61674-115">See [official steps](https://developer.yahoo.com/bbauth/user.html).</span></span>

* <span data-ttu-id="61674-116">Configurer **Tumblr** connectez-vous : [https://www.tumblr.com/oauth/apps](https://www.tumblr.com/oauth/apps).</span><span class="sxs-lookup"><span data-stu-id="61674-116">Set up **Tumblr** sign in: [https://www.tumblr.com/oauth/apps](https://www.tumblr.com/oauth/apps).</span></span> <span data-ttu-id="61674-117">Consultez [étapes officiels](https://www.tumblr.com/docs/en/api/v2#auth).</span><span class="sxs-lookup"><span data-stu-id="61674-117">See [official steps](https://www.tumblr.com/docs/en/api/v2#auth).</span></span>

* <span data-ttu-id="61674-118">Configurer **Pinterest** connectez-vous : [https://developers.pinterest.com/apps](https://developers.pinterest.com/apps).</span><span class="sxs-lookup"><span data-stu-id="61674-118">Set up **Pinterest** sign in: [https://developers.pinterest.com/apps](https://developers.pinterest.com/apps).</span></span> <span data-ttu-id="61674-119">Consultez [étapes officiels](https://developers.pinterest.com/docs/api/overview/?).</span><span class="sxs-lookup"><span data-stu-id="61674-119">See [official steps](https://developers.pinterest.com/docs/api/overview/?).</span></span>

* <span data-ttu-id="61674-120">Configurer **Pocket** connectez-vous : [http://getpocket.com/developer/apps/new](http://getpocket.com/developer/apps/new).</span><span class="sxs-lookup"><span data-stu-id="61674-120">Set up **Pocket** sign in: [http://getpocket.com/developer/apps/new](http://getpocket.com/developer/apps/new).</span></span> <span data-ttu-id="61674-121">Consultez [étapes officiels](https://getpocket.com/developer/docs/authentication).</span><span class="sxs-lookup"><span data-stu-id="61674-121">See [official steps](https://getpocket.com/developer/docs/authentication).</span></span>

* <span data-ttu-id="61674-122">Configurer **Flickr** connectez-vous : [https://www.flickr.com/services/apps/create](https://www.flickr.com/services/apps/create).</span><span class="sxs-lookup"><span data-stu-id="61674-122">Set up **Flickr** sign in: [https://www.flickr.com/services/apps/create](https://www.flickr.com/services/apps/create).</span></span> <span data-ttu-id="61674-123">Consultez [étapes officiels](https://www.flickr.com/services/api/auth.oauth.html).</span><span class="sxs-lookup"><span data-stu-id="61674-123">See [official steps](https://www.flickr.com/services/api/auth.oauth.html).</span></span>

* <span data-ttu-id="61674-124">Configurer **Dribble** connectez-vous : [https://dribbble.com/signup](https://dribbble.com/signup).</span><span class="sxs-lookup"><span data-stu-id="61674-124">Set up **Dribble** sign in: [https://dribbble.com/signup](https://dribbble.com/signup).</span></span> <span data-ttu-id="61674-125">Consultez [étapes officiels](http://developer.dribbble.com/v1/oauth/).</span><span class="sxs-lookup"><span data-stu-id="61674-125">See [official steps](http://developer.dribbble.com/v1/oauth/).</span></span>

* <span data-ttu-id="61674-126">Configurer **Vimeo** connectez-vous : [https://developer.vimeo.com/apps](https://developer.vimeo.com/apps).</span><span class="sxs-lookup"><span data-stu-id="61674-126">Set up **Vimeo** sign in: [https://developer.vimeo.com/apps](https://developer.vimeo.com/apps).</span></span> <span data-ttu-id="61674-127">Consultez [étapes officiels](https://developer.vimeo.com/api/authentication).</span><span class="sxs-lookup"><span data-stu-id="61674-127">See [official steps](https://developer.vimeo.com/api/authentication).</span></span>

* <span data-ttu-id="61674-128">Configurer **SoundCloud** connectez-vous : [http://soundcloud.com/you/apps/new](http://soundcloud.com/you/apps/new).</span><span class="sxs-lookup"><span data-stu-id="61674-128">Set up **SoundCloud** sign in: [http://soundcloud.com/you/apps/new](http://soundcloud.com/you/apps/new).</span></span> <span data-ttu-id="61674-129">Consultez [étapes officiels](https://developers.soundcloud.com/blog/we-love-oauth-2).</span><span class="sxs-lookup"><span data-stu-id="61674-129">See [official steps](https://developers.soundcloud.com/blog/we-love-oauth-2).</span></span>

* <span data-ttu-id="61674-130">Configurer **VK** connectez-vous : [https://vk.com/apps?act=manage](https://vk.com/apps?act=manage).</span><span class="sxs-lookup"><span data-stu-id="61674-130">Set up **VK** sign in: [https://vk.com/apps?act=manage](https://vk.com/apps?act=manage).</span></span> <span data-ttu-id="61674-131">Consultez [étapes officiels](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites).</span><span class="sxs-lookup"><span data-stu-id="61674-131">See [official steps](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites).</span></span>
