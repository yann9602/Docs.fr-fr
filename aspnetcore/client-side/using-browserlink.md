---
title: Lien du navigateur dans ASP.NET Core
author: ncarandini
description: "Une fonctionnalité de Visual Studio qui lie l’environnement de développement avec un ou plusieurs navigateurs web"
keywords: ASP.NET Core, lien du navigateur, la synchronisation CSS
ms.author: riande
manager: wpickett
ms.date: 12/28/2016
ms.topic: article
ms.assetid: 11813d4c-3f8a-445a-b23b-e4a57d001abc
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/using-browserlink
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b2ff38288cee3e9ca42a07c219521bb79a00a359
ms.sourcegitcommit: 4e84d8bf5f404bb77f3d41665cf7e7374fc39142
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2017
---
# <a name="introduction-to-browser-link-in-aspnet-core"></a><span data-ttu-id="bf9be-104">Introduction au lien du navigateur dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf9be-104">Introduction to Browser Link in ASP.NET Core</span></span> 

<span data-ttu-id="bf9be-105">Par [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), et [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="bf9be-105">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="bf9be-106">Lien du navigateur est une fonctionnalité de Visual Studio qui crée un canal de communication entre l’environnement de développement et un ou plusieurs navigateurs web.</span><span class="sxs-lookup"><span data-stu-id="bf9be-106">Browser Link is a feature in Visual Studio that creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="bf9be-107">Vous pouvez utiliser lien du navigateur pour actualiser votre application web dans plusieurs navigateurs à la fois, ce qui est utile pour le test de plusieurs navigateurs.</span><span class="sxs-lookup"><span data-stu-id="bf9be-107">You can use Browser Link to refresh your web application in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="bf9be-108">Programme d’installation lien du navigateur</span><span class="sxs-lookup"><span data-stu-id="bf9be-108">Browser Link setup</span></span>

<span data-ttu-id="bf9be-109">Le cœur d’ASP.NET **Application Web** modèles de projet dans Visual Studio 2015 et versions ultérieures comprennent tous les éléments nécessaires pour le lien du navigateur.</span><span class="sxs-lookup"><span data-stu-id="bf9be-109">The ASP.NET Core **Web Application** project templates in Visual Studio 2015 and later include everything needed for Browser Link.</span></span>

<span data-ttu-id="bf9be-110">Pour ajouter le lien du navigateur à un projet que vous avez créé à l’aide de l’ASP.NET Core **vide** ou **API Web** modèle, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="bf9be-110">To add Browser Link to a project that you created by using the ASP.NET Core **Empty** or **Web API** template, follow these steps:</span></span>

1. <span data-ttu-id="bf9be-111">Ajouter le *Microsoft.VisualStudio.Web.BrowserLink.Loader* package</span><span class="sxs-lookup"><span data-stu-id="bf9be-111">Add the *Microsoft.VisualStudio.Web.BrowserLink.Loader* package</span></span> 
2. <span data-ttu-id="bf9be-112">Ajoutez le code de configuration dans le *Startup.cs* fichier.</span><span class="sxs-lookup"><span data-stu-id="bf9be-112">Add configuration code in the *Startup.cs* file.</span></span>

### <a name="add-the-package"></a><span data-ttu-id="bf9be-113">Ajoutez le package</span><span class="sxs-lookup"><span data-stu-id="bf9be-113">Add the package</span></span>

<span data-ttu-id="bf9be-114">Dans la mesure où il s’agit d’une fonctionnalité de Visual Studio, pour ajouter le package le plus simple consiste à ouvrir le **Package Manager Console** (**vue > autres fenêtres > Package Manager Console**) et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="bf9be-114">Since this is a Visual Studio feature, the easiest way to add the package is to open the **Package Manager Console** (**View > Other Windows > Package Manager Console**) and run the following command:</span></span>

```console
install-package Microsoft.VisualStudio.Web.BrowserLink.Loader
```

<span data-ttu-id="bf9be-115">Vous pouvez également utiliser **Gestionnaire de Package NuGet**.</span><span class="sxs-lookup"><span data-stu-id="bf9be-115">Alternatively, you can use **NuGet Package Manager**.</span></span>  <span data-ttu-id="bf9be-116">Cliquez sur le nom du projet dans **l’Explorateur de solutions**, puis choisissez **gérer les Packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="bf9be-116">Right-click the project name in **Solution Explorer**, and choose **Manage NuGet Packages**.</span></span> 

![Gestionnaire de Package NuGet ouvert](using-browserlink/_static/open-nuget-package-manager.png)

<span data-ttu-id="bf9be-118">Puis recherchez et installez le package.</span><span class="sxs-lookup"><span data-stu-id="bf9be-118">Then find and install the package.</span></span>

![Ajouter un package avec le Gestionnaire de Package NuGet](using-browserlink/_static/add-package-with-nuget-package-manager.png)

### <a name="add-configuration-code"></a><span data-ttu-id="bf9be-120">Ajoutez le code de configuration</span><span class="sxs-lookup"><span data-stu-id="bf9be-120">Add configuration code</span></span>

<span data-ttu-id="bf9be-121">Ouvrez le *Startup.cs* fichier, puis, dans le `Configure` méthode ajoutez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="bf9be-121">Open the *Startup.cs* file, and in the `Configure` method add the following code:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="bf9be-122">Ce code est généralement à l’intérieur d’un `if` bloc qui permet le lien du navigateur uniquement dans l’environnement de développement, comme illustré ici :</span><span class="sxs-lookup"><span data-stu-id="bf9be-122">Usually that code is inside an `if` block that enables Browser Link only in the Development environment, as shown here:</span></span>

<span data-ttu-id="bf9be-123">[!code-csharp[Main](./using-browserlink/sample/BrowserLinkSample/src/BrowserLinkSample/Startup.cs?highlight=1,4&range=40-44)]</span><span class="sxs-lookup"><span data-stu-id="bf9be-123">[!code-csharp[Main](./using-browserlink/sample/BrowserLinkSample/src/BrowserLinkSample/Startup.cs?highlight=1,4&range=40-44)]</span></span>

<span data-ttu-id="bf9be-124">Pour plus d’informations, consultez [fonctionne avec plusieurs environnements](../fundamentals/environments.md).</span><span class="sxs-lookup"><span data-stu-id="bf9be-124">For more information, see [Working with Multiple Environments](../fundamentals/environments.md).</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="bf9be-125">Comment utiliser le lien du navigateur</span><span class="sxs-lookup"><span data-stu-id="bf9be-125">How to use Browser Link</span></span>

<span data-ttu-id="bf9be-126">Lorsque vous avez un projet ASP.NET Core ouvert, Visual Studio affiche le contrôle de barre d’outils du lien du navigateur à côté du **cible de débogage** contrôle de barre d’outils :</span><span class="sxs-lookup"><span data-stu-id="bf9be-126">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Menu de liste déroulante de lien de navigateur](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="bf9be-128">À partir du contrôle de barre d’outils de lien de navigateur, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="bf9be-128">From the Browser Link toolbar control, you can:</span></span>

- <span data-ttu-id="bf9be-129">Actualiser à la fois l’application web dans plusieurs navigateurs</span><span class="sxs-lookup"><span data-stu-id="bf9be-129">Refresh the web application in several browsers at once</span></span>
- <span data-ttu-id="bf9be-130">Ouvrez le **tableau de bord lien de navigateur**</span><span class="sxs-lookup"><span data-stu-id="bf9be-130">Open the **Browser Link Dashboard**</span></span>
- <span data-ttu-id="bf9be-131">Activer ou désactiver **lien du navigateur**</span><span class="sxs-lookup"><span data-stu-id="bf9be-131">Enable or disable **Browser Link**</span></span>
- <span data-ttu-id="bf9be-132">Activer ou désactiver la synchronisation automatique CSS</span><span class="sxs-lookup"><span data-stu-id="bf9be-132">Enable or disable CSS Auto-Sync</span></span>

> [!NOTE]
> <span data-ttu-id="bf9be-133">Certains plug-ins de Visual Studio, notamment *Web Extension Pack 2015* et *Web Extension Pack 2017*, proposent des fonctionnalités étendues pour le lien du navigateur, mais certaines des fonctionnalités supplémentaires ne fonctionnent pas avec ASP. Projets NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf9be-133">Some Visual Studio plug-ins, most notably *Web Extension Pack 2015* and *Web Extension Pack 2017*, offer extended functionality for Browser Link, but some of the additional features don't work with ASP.NET Core projects.</span></span>

## <a name="refresh-the-web-application-in-several-browsers-at-once"></a><span data-ttu-id="bf9be-134">Actualiser à la fois l’application web dans plusieurs navigateurs</span><span class="sxs-lookup"><span data-stu-id="bf9be-134">Refresh the web application in several browsers at once</span></span>

<span data-ttu-id="bf9be-135">Pour choisir un navigateur web unique pour lancer au démarrage du projet, utilisez le menu déroulant dans le **cible de débogage** contrôle de barre d’outils :</span><span class="sxs-lookup"><span data-stu-id="bf9be-135">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![Menu déroulant de F5](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="bf9be-137">Pour ouvrir plusieurs navigateurs à la fois, choisissez **naviguer avec...**  à partir de la même liste déroulante.</span><span class="sxs-lookup"><span data-stu-id="bf9be-137">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span>  <span data-ttu-id="bf9be-138">Maintenez enfoncée la touche CTRL ENFONCÉE pour sélectionner les navigateurs de votre choix, puis cliquez sur **Parcourir**:</span><span class="sxs-lookup"><span data-stu-id="bf9be-138">Hold down the CTRL key to select the browsers you want, and then click **Browse**:</span></span>

![Ouvrir à la fois des nombreux navigateurs](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="bf9be-140">Voici une capture d’écran exemple où Visual Studio avec la vue de l’Index et de deux navigateurs ouverts :</span><span class="sxs-lookup"><span data-stu-id="bf9be-140">Here's a sample screenshot showing Visual Studio with the Index view open and two open browsers:</span></span>

![Synchroniser avec l’exemple de deux navigateurs](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="bf9be-142">Placez le curseur sur le contrôle de barre d’outils de lien de navigateur pour voir les navigateurs qui sont connectés au projet :</span><span class="sxs-lookup"><span data-stu-id="bf9be-142">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Placez le curseur pointe](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="bf9be-144">Modifier l’affichage de l’Index, et tous les navigateurs connectés sont mis à jour lorsque vous cliquez sur le bouton Actualiser de lien du navigateur :</span><span class="sxs-lookup"><span data-stu-id="bf9be-144">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![navigateurs-sync-changes](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="bf9be-146">Lien du navigateur fonctionne également avec les navigateurs que vous lancez en dehors de Visual Studio et accédez à l’URL de l’application.</span><span class="sxs-lookup"><span data-stu-id="bf9be-146">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the application URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="bf9be-147">Le tableau de bord de lien de navigateur</span><span class="sxs-lookup"><span data-stu-id="bf9be-147">The Browser Link Dashboard</span></span>

<span data-ttu-id="bf9be-148">Dans la liste déroulante de lien du navigateur vers le bas du menu pour gérer les connexions avec les navigateurs ouverts, ouvrez le tableau de bord de lien de navigateur :</span><span class="sxs-lookup"><span data-stu-id="bf9be-148">Open the Browser Link Dashboard from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![Ouvrir-browserslink-tableau de bord](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="bf9be-150">Si aucun navigateur n’est connecté, vous pouvez démarrer une session de débogage non en cliquant sur le _afficher dans le navigateur_ lien :</span><span class="sxs-lookup"><span data-stu-id="bf9be-150">If no browser is connected, you can start a non debugging session clicking the _View in Browser_ link:</span></span>

![browserlink-tableau de bord-no-connexions](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="bf9be-152">Dans le cas contraire, les navigateurs connectés sont affichés avec le chemin d’accès à la page qui affiche chaque navigateur :</span><span class="sxs-lookup"><span data-stu-id="bf9be-152">Otherwise, the connected browsers are shown, with the path to the page that each browser is showing:</span></span>

![browserlink du tableau de bord deux connexions](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="bf9be-154">Si vous le souhaitez, vous pouvez cliquer sur un nom de navigateur répertoriés pour actualiser ce seul navigateur.</span><span class="sxs-lookup"><span data-stu-id="bf9be-154">If you like, you can click on a listed browser name to refresh that single browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="bf9be-155">Activer ou désactiver le lien du navigateur</span><span class="sxs-lookup"><span data-stu-id="bf9be-155">Enable or disable Browser Link</span></span>

<span data-ttu-id="bf9be-156">Lorsque vous réactivez lien du navigateur après l’avoir désactivé, vous devez actualiser le navigateur pour les reconnecter.</span><span class="sxs-lookup"><span data-stu-id="bf9be-156">When you re-enable Browser Link after disabling it, you have to refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="bf9be-157">Activer ou désactiver la synchronisation automatique CSS</span><span class="sxs-lookup"><span data-stu-id="bf9be-157">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="bf9be-158">Lorsque la synchronisation automatique CSS est activée, les navigateurs connectés sont automatiquement actualisées lorsque vous modifiez des fichiers CSS.</span><span class="sxs-lookup"><span data-stu-id="bf9be-158">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-does-it-work"></a><span data-ttu-id="bf9be-159">Comment cela fonctionne-t-il ?</span><span class="sxs-lookup"><span data-stu-id="bf9be-159">How does it work?</span></span>

<span data-ttu-id="bf9be-160">Lien du navigateur utilise SignalR pour créer un canal de communication entre Visual Studio et le navigateur.</span><span class="sxs-lookup"><span data-stu-id="bf9be-160">Browser Link uses SignalR to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="bf9be-161">Lorsque le lien du navigateur est activée, Visual Studio fait Office de serveur SignalR plusieurs clients (navigateurs) pouvant se connecter à.</span><span class="sxs-lookup"><span data-stu-id="bf9be-161">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="bf9be-162">Lien du navigateur enregistre également un composant d’intergiciel (middleware) dans le pipeline de demande ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="bf9be-162">Browser Link also registers a middleware component in the ASP.NET request pipeline.</span></span> <span data-ttu-id="bf9be-163">Ce composant injecte spéciaux `<script>` références dans chaque demande de page à partir du serveur.</span><span class="sxs-lookup"><span data-stu-id="bf9be-163">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="bf9be-164">Vous pouvez consulter les références de script en sélectionnant **afficher la source** dans le navigateur et le défilement à la fin de la `<body>` le contenu de la balise :</span><span class="sxs-lookup"><span data-stu-id="bf9be-164">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```javascript
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="bf9be-165">Vos fichiers sources ne sont pas modifiés.</span><span class="sxs-lookup"><span data-stu-id="bf9be-165">Your source files are not modified.</span></span> <span data-ttu-id="bf9be-166">Le composant d’intergiciel (middleware) injecte les références de script dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="bf9be-166">The middleware component injects the script references dynamically.</span></span> 

<span data-ttu-id="bf9be-167">Le code côté navigateur étant toutes les fonctions JavaScript, il fonctionne sur tous les navigateurs prenant en charge SignalR, sans nécessiter de n’importe quel plug-in de navigateur.</span><span class="sxs-lookup"><span data-stu-id="bf9be-167">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports, without requiring any browser plug-in.</span></span>
