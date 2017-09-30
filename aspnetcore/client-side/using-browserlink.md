---
title: Lien du navigateur dans ASP.NET Core
author: ncarandini
description: "Découvrez comment le lien du navigateur est une fonctionnalité de Visual Studio qui lie l’environnement de développement avec un ou plusieurs navigateurs web."
keywords: ASP.NET Core, lien du navigateur, la synchronisation CSS
ms.author: riande
manager: wpickett
ms.date: 09/22/2017
ms.topic: article
ms.assetid: 11813d4c-3f8a-445a-b23b-e4a57d001abc
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/using-browserlink
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 67ddc58e38962bd876050739a2a1447be4f589bb
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2017
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="f0914-104">Lien du navigateur dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f0914-104">Browser Link in ASP.NET Core</span></span> 

<span data-ttu-id="f0914-105">Par [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), et [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="f0914-105">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="f0914-106">Lien du navigateur est une fonctionnalité de Visual Studio qui crée un canal de communication entre l’environnement de développement et un ou plusieurs navigateurs web.</span><span class="sxs-lookup"><span data-stu-id="f0914-106">Browser Link is a feature in Visual Studio that creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="f0914-107">Vous pouvez utiliser lien du navigateur pour actualiser votre application web dans plusieurs navigateurs à la fois, ce qui est utile pour le test de plusieurs navigateurs.</span><span class="sxs-lookup"><span data-stu-id="f0914-107">You can use Browser Link to refresh your web application in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="f0914-108">Programme d’installation lien du navigateur</span><span class="sxs-lookup"><span data-stu-id="f0914-108">Browser Link setup</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="f0914-109">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="f0914-109">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="f0914-110">Le cœur d’ASP.NET 2.x **Application Web**, **vide**, et **API Web** modèle projets utilisent la [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) méta-package qui contient une référence de package pour [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="f0914-110">The ASP.NET Core 2.x **Web Application**, **Empty**, and **Web API** template projects use the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) meta-package, which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="f0914-111">Par conséquent, à l’aide de la `Microsoft.AspNetCore.All` méta-package ne nécessite aucune action supplémentaire pour que le lien du navigateur disponibles.</span><span class="sxs-lookup"><span data-stu-id="f0914-111">Therefore, using the `Microsoft.AspNetCore.All` meta-package requires no further action to make Browser Link available for use.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="f0914-112">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="f0914-112">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="f0914-113">Le cœur d’ASP.NET 1.x **Application Web** modèle de projet a une référence de package pour le [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span><span class="sxs-lookup"><span data-stu-id="f0914-113">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="f0914-114">Le **vide** ou **API Web** les projets de modèle, vous devez ajouter une référence de package à `Microsoft.VisualStudio.Web.BrowserLink`.</span><span class="sxs-lookup"><span data-stu-id="f0914-114">The **Empty** or **Web API** template projects require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

<span data-ttu-id="f0914-115">Dans la mesure où cela est une fonctionnalité de Visual Studio, le moyen le plus simple pour ajouter le package à un **vide** ou **API Web** modèle de projet consiste à ouvrir le **Package Manager Console** (**Vue** > **autres fenêtres** > **Package Manager Console**) et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f0914-115">Since this is a Visual Studio feature, the easiest way to add the package to an **Empty** or **Web API** template project is to open the **Package Manager Console** (**View** > **Other Windows** > **Package Manager Console**) and run the following command:</span></span>

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

<span data-ttu-id="f0914-116">Vous pouvez également utiliser **Gestionnaire de Package NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f0914-116">Alternatively, you can use **NuGet Package Manager**.</span></span> <span data-ttu-id="f0914-117">Cliquez sur le nom du projet dans **l’Explorateur de solutions** et choisissez **gérer les Packages NuGet**:</span><span class="sxs-lookup"><span data-stu-id="f0914-117">Right-click the project name in **Solution Explorer** and choose **Manage NuGet Packages**:</span></span>

![Gestionnaire de Package NuGet ouvert](using-browserlink/_static/open-nuget-package-manager.png)

<span data-ttu-id="f0914-119">Recherchez et installez le package :</span><span class="sxs-lookup"><span data-stu-id="f0914-119">Find and install the package:</span></span>

![Ajouter un package avec le Gestionnaire de Package NuGet](using-browserlink/_static/add-package-with-nuget-package-manager.png)

---

### <a name="configuration"></a><span data-ttu-id="f0914-121">Configuration</span><span class="sxs-lookup"><span data-stu-id="f0914-121">Configuration</span></span>

<span data-ttu-id="f0914-122">Dans le `Configure` méthode de la *Startup.cs* fichier :</span><span class="sxs-lookup"><span data-stu-id="f0914-122">In the `Configure` method of the *Startup.cs* file:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="f0914-123">Le code est généralement à l’intérieur d’un `if` bloc qui permet uniquement de lien de navigateur dans l’environnement de développement, comme indiqué ici :</span><span class="sxs-lookup"><span data-stu-id="f0914-123">Usually the code is inside an `if` block that only enables Browser Link in the Development environment, as shown here:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="f0914-124">Pour plus d’informations, consultez [Utilisation de plusieurs environnements](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f0914-124">For more information, see [Working with Multiple Environments](xref:fundamentals/environments).</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="f0914-125">Comment utiliser le lien du navigateur</span><span class="sxs-lookup"><span data-stu-id="f0914-125">How to use Browser Link</span></span>

<span data-ttu-id="f0914-126">Lorsque vous avez un projet ASP.NET Core ouvert, Visual Studio affiche le contrôle de barre d’outils du lien du navigateur à côté du **cible de débogage** contrôle de barre d’outils :</span><span class="sxs-lookup"><span data-stu-id="f0914-126">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Menu de liste déroulante de lien de navigateur](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="f0914-128">À partir du contrôle de barre d’outils de lien de navigateur, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="f0914-128">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="f0914-129">Actualiser l’application web dans plusieurs navigateurs à la fois.</span><span class="sxs-lookup"><span data-stu-id="f0914-129">Refresh the web application in several browsers at once.</span></span>
* <span data-ttu-id="f0914-130">Ouvrez le **le tableau de bord navigateur lien**.</span><span class="sxs-lookup"><span data-stu-id="f0914-130">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="f0914-131">Activer ou désactiver **lien du navigateur**.</span><span class="sxs-lookup"><span data-stu-id="f0914-131">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="f0914-132">Remarque : Le lien du navigateur est désactivée par défaut dans Visual Studio 2017 (15,3).</span><span class="sxs-lookup"><span data-stu-id="f0914-132">Note: Browser Link is disabled by default in Visual Studio 2017 (15.3).</span></span>
* <span data-ttu-id="f0914-133">Activer ou désactiver la synchronisation automatique CSS.</span><span class="sxs-lookup"><span data-stu-id="f0914-133">Enable or disable CSS Auto-Sync.</span></span>

> [!NOTE]
> <span data-ttu-id="f0914-134">Certains plug-ins de Visual Studio, notamment *Web Extension Pack 2015* et *Web Extension Pack 2017*, proposent des fonctionnalités étendues pour le lien du navigateur, mais certaines des fonctionnalités supplémentaires ne fonctionnent pas avec ASP. Projets NET Core.</span><span class="sxs-lookup"><span data-stu-id="f0914-134">Some Visual Studio plug-ins, most notably *Web Extension Pack 2015* and *Web Extension Pack 2017*, offer extended functionality for Browser Link, but some of the additional features don't work with ASP.NET Core projects.</span></span>

## <a name="refresh-the-web-application-in-several-browsers-at-once"></a><span data-ttu-id="f0914-135">Actualiser à la fois l’application web dans plusieurs navigateurs</span><span class="sxs-lookup"><span data-stu-id="f0914-135">Refresh the web application in several browsers at once</span></span>

<span data-ttu-id="f0914-136">Pour choisir un navigateur web unique pour lancer au démarrage du projet, utilisez le menu déroulant dans le **cible de débogage** contrôle de barre d’outils :</span><span class="sxs-lookup"><span data-stu-id="f0914-136">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![Menu déroulant de F5](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="f0914-138">Pour ouvrir plusieurs navigateurs à la fois, choisissez **naviguer avec...**  à partir de la même liste déroulante.</span><span class="sxs-lookup"><span data-stu-id="f0914-138">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="f0914-139">Maintenez enfoncée la touche CTRL ENFONCÉE pour sélectionner les navigateurs de votre choix, puis cliquez sur **Parcourir**:</span><span class="sxs-lookup"><span data-stu-id="f0914-139">Hold down the CTRL key to select the browsers you want, and then click **Browse**:</span></span>

![Ouvrir à la fois des nombreux navigateurs](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="f0914-141">Voici une capture d’écran montrant Visual Studio avec la vue Index ouvert et de deux navigateurs ouverts :</span><span class="sxs-lookup"><span data-stu-id="f0914-141">Here's a screenshot showing Visual Studio with the Index view open and two open browsers:</span></span>

![Synchroniser avec l’exemple de deux navigateurs](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="f0914-143">Placez le curseur sur le contrôle de barre d’outils de lien de navigateur pour voir les navigateurs qui sont connectés au projet :</span><span class="sxs-lookup"><span data-stu-id="f0914-143">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Placez le curseur pointe](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="f0914-145">Modifier l’affichage de l’Index, et tous les navigateurs connectés sont mis à jour lorsque vous cliquez sur le bouton Actualiser de lien du navigateur :</span><span class="sxs-lookup"><span data-stu-id="f0914-145">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![navigateurs-sync-changes](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="f0914-147">Lien du navigateur fonctionne également avec les navigateurs que vous lancez en dehors de Visual Studio et accédez à l’URL de l’application.</span><span class="sxs-lookup"><span data-stu-id="f0914-147">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the application URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="f0914-148">Le tableau de bord de lien de navigateur</span><span class="sxs-lookup"><span data-stu-id="f0914-148">The Browser Link Dashboard</span></span>

<span data-ttu-id="f0914-149">Dans la liste déroulante de lien du navigateur vers le bas du menu pour gérer les connexions avec les navigateurs ouverts, ouvrez le tableau de bord de lien de navigateur :</span><span class="sxs-lookup"><span data-stu-id="f0914-149">Open the Browser Link Dashboard from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![Ouvrir-browserslink-tableau de bord](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="f0914-151">Si aucun navigateur n’est connecté, vous pouvez démarrer une session de débogage non en sélectionnant le *afficher dans le navigateur* lien :</span><span class="sxs-lookup"><span data-stu-id="f0914-151">If no browser is connected, you can start a non-debugging session by selecting the *View in Browser* link:</span></span>

![browserlink-tableau de bord-no-connexions](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="f0914-153">Dans le cas contraire, les navigateurs connectés sont affichés avec le chemin d’accès à la page qui affiche chaque navigateur :</span><span class="sxs-lookup"><span data-stu-id="f0914-153">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink du tableau de bord deux connexions](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="f0914-155">Si vous le souhaitez, vous pouvez cliquer sur un nom de navigateur répertoriés pour actualiser ce seul navigateur.</span><span class="sxs-lookup"><span data-stu-id="f0914-155">If you like, you can click on a listed browser name to refresh that single browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="f0914-156">Activer ou désactiver le lien du navigateur</span><span class="sxs-lookup"><span data-stu-id="f0914-156">Enable or disable Browser Link</span></span>

<span data-ttu-id="f0914-157">Lorsque vous réactivez lien du navigateur après l’avoir désactivé, vous devez actualiser le navigateur pour vous reconnecter les.</span><span class="sxs-lookup"><span data-stu-id="f0914-157">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="f0914-158">Activer ou désactiver la synchronisation automatique CSS</span><span class="sxs-lookup"><span data-stu-id="f0914-158">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="f0914-159">Lorsque la synchronisation automatique CSS est activée, les navigateurs connectés sont automatiquement actualisées lorsque vous modifiez des fichiers CSS.</span><span class="sxs-lookup"><span data-stu-id="f0914-159">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-does-it-work"></a><span data-ttu-id="f0914-160">Comment cela fonctionne-t-il ?</span><span class="sxs-lookup"><span data-stu-id="f0914-160">How does it work?</span></span>

<span data-ttu-id="f0914-161">Lien du navigateur utilise SignalR pour créer un canal de communication entre Visual Studio et le navigateur.</span><span class="sxs-lookup"><span data-stu-id="f0914-161">Browser Link uses SignalR to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="f0914-162">Lorsque le lien du navigateur est activée, Visual Studio fait Office de serveur SignalR plusieurs clients (navigateurs) pouvant se connecter à.</span><span class="sxs-lookup"><span data-stu-id="f0914-162">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="f0914-163">Lien du navigateur enregistre également un composant d’intergiciel (middleware) dans le pipeline de demande ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="f0914-163">Browser Link also registers a middleware component in the ASP.NET request pipeline.</span></span> <span data-ttu-id="f0914-164">Ce composant injecte spéciaux `<script>` références dans chaque demande de page à partir du serveur.</span><span class="sxs-lookup"><span data-stu-id="f0914-164">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="f0914-165">Vous pouvez consulter les références de script en sélectionnant **afficher la source** dans le navigateur et le défilement à la fin de la `<body>` le contenu de la balise :</span><span class="sxs-lookup"><span data-stu-id="f0914-165">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```javascript
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="f0914-166">Vos fichiers sources ne sont pas modifiés.</span><span class="sxs-lookup"><span data-stu-id="f0914-166">Your source files aren't modified.</span></span> <span data-ttu-id="f0914-167">Le composant d’intergiciel (middleware) injecte les références de script dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="f0914-167">The middleware component injects the script references dynamically.</span></span> 

<span data-ttu-id="f0914-168">Le code côté navigateur étant toutes les fonctions JavaScript, il fonctionne sur tous les navigateurs prenant en charge SignalR sans nécessiter un plug-in de navigateur.</span><span class="sxs-lookup"><span data-stu-id="f0914-168">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
