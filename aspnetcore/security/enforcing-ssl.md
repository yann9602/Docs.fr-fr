---
title: Application de SSL dans une application ASP.NET Core
author: rick-anderson
description: "Montre comment exiger le protocole SSL dans un cœur d’ASP.NET web app"
keywords: ASP.NET Core, SSL, HTTPS, RequireHttpsAttribute, IIS Express
ms.author: riande
manager: wpickett
ms.date: 07/19/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/enforcing-ssl
ms.openlocfilehash: 35554939bd574b2826053004ed437bee46154c2b
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="enforcing-ssl-in-an-aspnet-core-app"></a><span data-ttu-id="1c704-104">Application de SSL dans une application ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c704-104">Enforcing SSL in an ASP.NET Core app</span></span>

<span data-ttu-id="1c704-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1c704-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1c704-106">Ce document montre comment :</span><span class="sxs-lookup"><span data-stu-id="1c704-106">This document shows how to:</span></span>

- <span data-ttu-id="1c704-107">Exiger le protocole SSL pour toutes les demandes (uniquement pour les requêtes HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1c704-107">Require SSL for all requests (HTTPS requests only).</span></span>
- <span data-ttu-id="1c704-108">Rediriger toutes les demandes HTTP vers HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1c704-108">Redirect all HTTP requests to HTTPS.</span></span>

## <a name="require-ssl"></a><span data-ttu-id="1c704-109">Exiger le protocole SSL</span><span class="sxs-lookup"><span data-stu-id="1c704-109">Require SSL</span></span>

<span data-ttu-id="1c704-110">Le [RequireHttpsAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.requirehttpsattribute) est utilisée pour exiger SSL.</span><span class="sxs-lookup"><span data-stu-id="1c704-110">The [RequireHttpsAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.requirehttpsattribute) is used to require SSL.</span></span> <span data-ttu-id="1c704-111">Vous pouvez la décorer contrôleurs ou méthodes avec cet attribut, ou vous pouvez l’appliquer globalement, comme indiqué ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="1c704-111">You can decorate controllers or methods with this attribute or you can apply it globally as shown below:</span></span>

<span data-ttu-id="1c704-112">Ajoutez le code suivant à `ConfigureServices` dans `Startup`:</span><span class="sxs-lookup"><span data-stu-id="1c704-112">Add the following code to `ConfigureServices` in `Startup`:</span></span>

<span data-ttu-id="1c704-113">[!code-csharp[Main](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-)]</span><span class="sxs-lookup"><span data-stu-id="1c704-113">[!code-csharp[Main](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-)]</span></span>

<span data-ttu-id="1c704-114">Le code en surbrillance ci-dessus nécessite l’utilisation de toutes les demandes `HTTPS`, par conséquent, les requêtes HTTP sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="1c704-114">The highlighted code above requires all requests use `HTTPS`, therefore HTTP requests are ignored.</span></span> <span data-ttu-id="1c704-115">Le code en surbrillance suivant redirige toutes les demandes HTTP vers HTTPS :</span><span class="sxs-lookup"><span data-stu-id="1c704-115">The following highlighted code redirects all HTTP requests to HTTPS:</span></span>

<span data-ttu-id="1c704-116">[!code-csharp[Main](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-)]</span><span class="sxs-lookup"><span data-stu-id="1c704-116">[!code-csharp[Main](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-)]</span></span>

<span data-ttu-id="1c704-117">Consultez [intergiciel (middleware) réécriture d’URL](xref:fundamentals/url-rewriting) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="1c704-117">See [URL Rewriting Middleware](xref:fundamentals/url-rewriting) for more information.</span></span>

<span data-ttu-id="1c704-118">Nécessitant HTTPS globalement (`options.Filters.Add(new RequireHttpsAttribute());`) est une meilleure pratique de sécurité.</span><span class="sxs-lookup"><span data-stu-id="1c704-118">Requiring HTTPS globally (`options.Filters.Add(new RequireHttpsAttribute());`) is a security best practice.</span></span> <span data-ttu-id="1c704-119">Appliquer le `[RequireHttps]` attribut à tous les contrôleur n’est pas considéré comme autant nécessitant HTTPS globalement.</span><span class="sxs-lookup"><span data-stu-id="1c704-119">Applying the `[RequireHttps]` attribute to all controller is not considered as secure as requiring HTTPS globally.</span></span> <span data-ttu-id="1c704-120">Vous ne pouvez pas garantir la sécurité ajoutées à votre application de nouveaux contrôleurs penser à appliquer le `[RequireHttps]` attribut.</span><span class="sxs-lookup"><span data-stu-id="1c704-120">You can't guarantee new controllers added to your app will remember to apply the `[RequireHttps]` attribute.</span></span>

## <a name="set-up-iis-express-for-sslhttps"></a><span data-ttu-id="1c704-121">Configurer IIS Express pour SSL/HTTPS</span><span class="sxs-lookup"><span data-stu-id="1c704-121">Set up IIS Express for SSL/HTTPS</span></span>

<span data-ttu-id="1c704-122">Consultez [configuration de HTTPS pour le développement dans ASP.NET Core](xref:security/https#iisxpress).</span><span class="sxs-lookup"><span data-stu-id="1c704-122">See [Setting up HTTPS for development in ASP.NET Core](xref:security/https#iisxpress).</span></span>
