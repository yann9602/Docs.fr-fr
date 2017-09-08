---
title: Utilisation des fichiers statiques dans ASP.NET Core
author: rick-anderson
description: Utilisation des fichiers statiques
keywords: ASP.NET Core, fichiers statiques, les ressources statiques, HTML, CSS, JavaScript
ms.author: riande
manager: wpickett
ms.date: 4/07/2017
ms.topic: article
ms.assetid: e32245c7-4eee-4831-bd2e-915dbf9f5f70
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/static-files
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ea6c180332dd5ab3a7238dcd73a4a1c8534c6243
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="introduction-to-working-with-static-files-in-aspnet-core"></a><span data-ttu-id="23ef2-104">Introduction à l’utilisation des fichiers statiques dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23ef2-104">Introduction to working with static files in ASP.NET Core</span></span>

<a name=fundamentals-static-files></a>

<span data-ttu-id="23ef2-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="23ef2-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="23ef2-106">Les fichiers statiques, tels que HTML, CSS, image et JavaScript, sont actifs qu’une application ASP.NET Core peut gérer directement aux clients.</span><span class="sxs-lookup"><span data-stu-id="23ef2-106">Static files, such as HTML, CSS, image, and JavaScript, are assets that an ASP.NET Core app can serve directly to clients.</span></span>

[<span data-ttu-id="23ef2-107">Afficher ou télécharger l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="23ef2-107">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/static-files/sample)

## <a name="serving-static-files"></a><span data-ttu-id="23ef2-108">Fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="23ef2-108">Serving static files</span></span>

<span data-ttu-id="23ef2-109">Fichiers statiques sont généralement placés dans le `web root` (*\<contenu racine > / wwwroot*) dossier.</span><span class="sxs-lookup"><span data-stu-id="23ef2-109">Static files are typically located in the `web root` (*\<content-root>/wwwroot*) folder.</span></span> <span data-ttu-id="23ef2-110">Consultez [racine du contenu](xref:fundamentals/index#content-root) et [racine Web](xref:fundamentals/index#web-root) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="23ef2-110">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span> <span data-ttu-id="23ef2-111">Vous définissez généralement la racine de contenu du répertoire actif afin que votre projet `web root` introuvable dans le développement.</span><span class="sxs-lookup"><span data-stu-id="23ef2-111">You generally set the content root to be the current directory so that your project's `web root` will be found while in development.</span></span>

<span data-ttu-id="23ef2-112">[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=5&start=12&end=22)]</span><span class="sxs-lookup"><span data-stu-id="23ef2-112">[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=5&start=12&end=22)]</span></span>

<span data-ttu-id="23ef2-113">Fichiers statiques peuvent être stockées dans un dossier sous le `web root` et est accessible avec un chemin d’accès relatif à cette racine.</span><span class="sxs-lookup"><span data-stu-id="23ef2-113">Static files can be stored in any folder under the `web root` and accessed with a relative path to that root.</span></span> <span data-ttu-id="23ef2-114">Par exemple, lorsque vous créez un projet d’application Web par défaut à l’aide de Visual Studio, il existe plusieurs dossiers créés dans le *wwwroot* dossier - *css*, *images*, et *js*.</span><span class="sxs-lookup"><span data-stu-id="23ef2-114">For example, when you create a default Web application project using Visual Studio, there are several folders created within the *wwwroot*  folder - *css*, *images*, and *js*.</span></span> <span data-ttu-id="23ef2-115">L’URI pour accéder à une image dans le *images* sous-dossier :</span><span class="sxs-lookup"><span data-stu-id="23ef2-115">The URI to access an image in the *images* subfolder:</span></span>

* `http://<app>/images/<imageFileName>`
* `http://localhost:9189/images/banner3.svg`

<span data-ttu-id="23ef2-116">Pour des fichiers statiques, vous devez configurer le [intergiciel (middleware)](middleware.md) pour ajouter des fichiers statiques pour le pipeline.</span><span class="sxs-lookup"><span data-stu-id="23ef2-116">In order for static files to be served, you must configure the [Middleware](middleware.md) to add static files to the pipeline.</span></span> <span data-ttu-id="23ef2-117">L’intergiciel (middleware) de fichiers statiques peut être configuré en ajoutant une dépendance sur le *Microsoft.AspNetCore.StaticFiles* package pour votre projet et en appelant le `UseStaticFiles` méthode d’extension à partir de `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="23ef2-117">The static file middleware can be configured by adding a dependency on the *Microsoft.AspNetCore.StaticFiles* package to your project and then calling the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

<span data-ttu-id="23ef2-118">[!code-csharp[Main](../fundamentals/static-files/sample/StartupStaticFiles.cs?highlight=3&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="23ef2-118">[!code-csharp[Main](../fundamentals/static-files/sample/StartupStaticFiles.cs?highlight=3&name=snippet1)]</span></span>

<span data-ttu-id="23ef2-119">`app.UseStaticFiles();`rend les fichiers dans `web root` (*wwwroot* par défaut) servable.</span><span class="sxs-lookup"><span data-stu-id="23ef2-119">`app.UseStaticFiles();` makes the files in `web root` (*wwwroot* by default) servable.</span></span> <span data-ttu-id="23ef2-120">Plus tard, je vais montrer comment rendre le contenu du répertoire autres servable avec `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="23ef2-120">Later I'll show how to make other directory contents servable with `UseStaticFiles`.</span></span>

<span data-ttu-id="23ef2-121">Vous devez inclure le package NuGet « Microsoft.AspNetCore.StaticFiles ».</span><span class="sxs-lookup"><span data-stu-id="23ef2-121">You must include the NuGet package "Microsoft.AspNetCore.StaticFiles".</span></span>

> [!NOTE]
> <span data-ttu-id="23ef2-122">`web root`valeur par défaut est la *wwwroot* active, mais vous pouvez définir le `web root` répertoire `UseWebRoot`.</span><span class="sxs-lookup"><span data-stu-id="23ef2-122">`web root` defaults to the *wwwroot* directory, but you can set the `web root` directory with `UseWebRoot`.</span></span>

<span data-ttu-id="23ef2-123">Supposez que vous avez une hiérarchie de projet dans lequel les fichiers statiques que vous souhaitez proposer sont en dehors de la `web root`.</span><span class="sxs-lookup"><span data-stu-id="23ef2-123">Suppose you have a project hierarchy where the static files you wish to serve are outside the `web root`.</span></span> <span data-ttu-id="23ef2-124">Exemple :</span><span class="sxs-lookup"><span data-stu-id="23ef2-124">For example:</span></span>

* <span data-ttu-id="23ef2-125">wwwroot</span><span class="sxs-lookup"><span data-stu-id="23ef2-125">wwwroot</span></span>
  * <span data-ttu-id="23ef2-126">css</span><span class="sxs-lookup"><span data-stu-id="23ef2-126">css</span></span>
  * <span data-ttu-id="23ef2-127">images</span><span class="sxs-lookup"><span data-stu-id="23ef2-127">images</span></span>
  * <span data-ttu-id="23ef2-128">...</span><span class="sxs-lookup"><span data-stu-id="23ef2-128">...</span></span>
* <span data-ttu-id="23ef2-129">MyStaticFiles</span><span class="sxs-lookup"><span data-stu-id="23ef2-129">MyStaticFiles</span></span>
  * <span data-ttu-id="23ef2-130">test.png</span><span class="sxs-lookup"><span data-stu-id="23ef2-130">test.png</span></span>

<span data-ttu-id="23ef2-131">Pour une demande d’accès aux *test.png*, configurez l’intergiciel (middleware) des fichiers statiques comme suit :</span><span class="sxs-lookup"><span data-stu-id="23ef2-131">For a request to access *test.png*, configure the static files middleware as follows:</span></span>

<span data-ttu-id="23ef2-132">[!code-csharp[Main](../fundamentals/static-files/sample/StartupTwoStaticFiles.cs?highlight=5,6,7,8,9,10&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="23ef2-132">[!code-csharp[Main](../fundamentals/static-files/sample/StartupTwoStaticFiles.cs?highlight=5,6,7,8,9,10&name=snippet1)]</span></span>

<span data-ttu-id="23ef2-133">Une demande de `http://<app>/StaticFiles/test.png` servira le *test.png* fichier.</span><span class="sxs-lookup"><span data-stu-id="23ef2-133">A request to `http://<app>/StaticFiles/test.png` will serve the *test.png* file.</span></span>

<span data-ttu-id="23ef2-134">`StaticFileOptions()`peut définir des en-têtes de réponse.</span><span class="sxs-lookup"><span data-stu-id="23ef2-134">`StaticFileOptions()` can set response headers.</span></span> <span data-ttu-id="23ef2-135">Par exemple, le code ci-dessous configure d’utilisation à partir de fichiers statiques le *wwwroot* dossier et définit le `Cache-Control` en-tête pour les rendre public pouvant être pendant 10 minutes (600 secondes) :</span><span class="sxs-lookup"><span data-stu-id="23ef2-135">For example, the code below sets up static file serving from the *wwwroot* folder and sets the `Cache-Control` header to make them publicly cacheable for 10 minutes (600 seconds):</span></span>

<span data-ttu-id="23ef2-136">[!code-csharp[Main](../fundamentals/static-files/sample/StartupAddHeader.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="23ef2-136">[!code-csharp[Main](../fundamentals/static-files/sample/StartupAddHeader.cs?name=snippet1)]</span></span>

![En-têtes de réponse indiquant l’en-tête Cache-Control a été ajouté.](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="23ef2-138">Autorisation de fichier statique</span><span class="sxs-lookup"><span data-stu-id="23ef2-138">Static file authorization</span></span>

<span data-ttu-id="23ef2-139">Le module de fichier statique fournit **aucune** vérifications d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="23ef2-139">The static file module provides **no** authorization checks.</span></span> <span data-ttu-id="23ef2-140">Tous les fichiers pris en charge par, notamment ceux de *wwwroot* sont accessibles.</span><span class="sxs-lookup"><span data-stu-id="23ef2-140">Any files served by it, including those under *wwwroot* are publicly available.</span></span> <span data-ttu-id="23ef2-141">Pour traiter les fichiers en fonction de l’autorisation :</span><span class="sxs-lookup"><span data-stu-id="23ef2-141">To serve files based on authorization:</span></span>

* <span data-ttu-id="23ef2-142">Les stocker en dehors de *wwwroot* et n’importe quel répertoire accessible à l’intergiciel (middleware) de fichiers statiques **et**</span><span class="sxs-lookup"><span data-stu-id="23ef2-142">Store them outside of *wwwroot* and any directory accessible to the static file middleware **and**</span></span>

* <span data-ttu-id="23ef2-143">Prendre en charge via une action de contrôleur, en retournant un `FileResult` où l’autorisation est appliquée</span><span class="sxs-lookup"><span data-stu-id="23ef2-143">Serve them through a controller action, returning a `FileResult` where authorization is applied</span></span>

## <a name="enabling-directory-browsing"></a><span data-ttu-id="23ef2-144">Activer l’exploration de répertoire</span><span class="sxs-lookup"><span data-stu-id="23ef2-144">Enabling directory browsing</span></span>

<span data-ttu-id="23ef2-145">Exploration des répertoires permet à l’utilisateur de votre application web afficher la liste des répertoires et fichiers contenus dans un répertoire spécifié.</span><span class="sxs-lookup"><span data-stu-id="23ef2-145">Directory browsing allows the user of your web app to see a list of directories and files within a specified directory.</span></span> <span data-ttu-id="23ef2-146">Exploration des répertoires est désactivée par défaut pour des raisons de sécurité (voir [considérations](#considerations)).</span><span class="sxs-lookup"><span data-stu-id="23ef2-146">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="23ef2-147">Pour activer l’exploration des répertoires, appelez le `UseDirectoryBrowser` méthode d’extension à partir de `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="23ef2-147">To enable directory browsing, call the `UseDirectoryBrowser` extension method from  `Startup.Configure`:</span></span>

<span data-ttu-id="23ef2-148">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="23ef2-148">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet1)]</span></span>

<span data-ttu-id="23ef2-149">Et ajouter des services requis en appelant `AddDirectoryBrowser` méthode d’extension à partir de `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="23ef2-149">And add required services by calling `AddDirectoryBrowser` extension method from `Startup.ConfigureServices`:</span></span>

<span data-ttu-id="23ef2-150">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet2)]</span><span class="sxs-lookup"><span data-stu-id="23ef2-150">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet2)]</span></span>

<span data-ttu-id="23ef2-151">Le code ci-dessus permet l’exploration des répertoires de la *wwwroot/images* dossier à l’aide de l’URL http://\<application > / MyImages, avec des liens vers chaque fichier et dossier :</span><span class="sxs-lookup"><span data-stu-id="23ef2-151">The code above allows directory browsing of the *wwwroot/images* folder using the URL http://\<app>/MyImages, with links to each file and folder:</span></span>

![exploration des répertoires](static-files/_static/dir-browse.png)

<span data-ttu-id="23ef2-153">Consultez [considérations](#considerations) sur les risques de sécurité lors de l’exploration.</span><span class="sxs-lookup"><span data-stu-id="23ef2-153">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="23ef2-154">Notez les deux `app.UseStaticFiles` appels.</span><span class="sxs-lookup"><span data-stu-id="23ef2-154">Note the two `app.UseStaticFiles` calls.</span></span> <span data-ttu-id="23ef2-155">Le premier élément est requis pour servir le CSS, des images et JavaScript dans les *wwwroot* dossier et le deuxième appel pour l’exploration des répertoires de la *wwwroot/images* dossier à l’aide de l’URL http://\<application > / MyImages :</span><span class="sxs-lookup"><span data-stu-id="23ef2-155">The first one is required to serve the CSS, images and JavaScript in the *wwwroot* folder, and the second call for directory browsing of the *wwwroot/images* folder using the URL http://\<app>/MyImages:</span></span>

<span data-ttu-id="23ef2-156">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?highlight=3,5&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="23ef2-156">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?highlight=3,5&name=snippet1)]</span></span>

## <a name="serving-a-default-document"></a><span data-ttu-id="23ef2-157">Desservant un document par défaut</span><span class="sxs-lookup"><span data-stu-id="23ef2-157">Serving a default document</span></span>

<span data-ttu-id="23ef2-158">Définition d’une page d’accueil par défaut donne les visiteurs du site un point de départ lors de la visite de votre site.</span><span class="sxs-lookup"><span data-stu-id="23ef2-158">Setting a default home page gives site visitors a place to start when visiting your site.</span></span> <span data-ttu-id="23ef2-159">Dans l’ordre pour votre application Web à répondre à une page par défaut sans qualifier complètement l’URI de l’utilisateur, appelez le `UseDefaultFiles` méthode d’extension à partir de `Startup.Configure` comme suit.</span><span class="sxs-lookup"><span data-stu-id="23ef2-159">In order for your Web app to serve a default page without the user having to fully qualify the URI, call the `UseDefaultFiles` extension method from `Startup.Configure` as follows.</span></span>

<span data-ttu-id="23ef2-160">[!code-csharp[Main](../fundamentals/static-files/sample/StartupEmpty.cs?highlight=3&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="23ef2-160">[!code-csharp[Main](../fundamentals/static-files/sample/StartupEmpty.cs?highlight=3&name=snippet1)]</span></span>

> [!NOTE]
> <span data-ttu-id="23ef2-161">`UseDefaultFiles`doit être appelé avant `UseStaticFiles` pour traiter le fichier par défaut.</span><span class="sxs-lookup"><span data-stu-id="23ef2-161">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="23ef2-162">`UseDefaultFiles`est une réécriture URL qui ne servent réellement le fichier.</span><span class="sxs-lookup"><span data-stu-id="23ef2-162">`UseDefaultFiles` is a URL re-writer that doesn't actually serve the file.</span></span> <span data-ttu-id="23ef2-163">Vous devez activer l’intergiciel (middleware) de fichiers statiques (`UseStaticFiles`) pour traiter le fichier.</span><span class="sxs-lookup"><span data-stu-id="23ef2-163">You must enable the static file middleware (`UseStaticFiles`) to serve the file.</span></span>

<span data-ttu-id="23ef2-164">Avec `UseDefaultFiles`, rechercheront les demandes vers un dossier :</span><span class="sxs-lookup"><span data-stu-id="23ef2-164">With `UseDefaultFiles`, requests to a folder will search for:</span></span>

* <span data-ttu-id="23ef2-165">default.htm</span><span class="sxs-lookup"><span data-stu-id="23ef2-165">default.htm</span></span>
* <span data-ttu-id="23ef2-166">default.html</span><span class="sxs-lookup"><span data-stu-id="23ef2-166">default.html</span></span>
* <span data-ttu-id="23ef2-167">index.htm</span><span class="sxs-lookup"><span data-stu-id="23ef2-167">index.htm</span></span>
* <span data-ttu-id="23ef2-168">index.html</span><span class="sxs-lookup"><span data-stu-id="23ef2-168">index.html</span></span>

<span data-ttu-id="23ef2-169">Le premier fichier trouvé dans la liste sera pris en charge que si la demande a l’adresse URI complète (bien que l’URL de navigateur continue d’afficher l’URI demandé).</span><span class="sxs-lookup"><span data-stu-id="23ef2-169">The first file found from the list will be served as if the request was the fully qualified URI (although the browser URL will continue to show the URI requested).</span></span>

<span data-ttu-id="23ef2-170">Le code suivant montre comment modifier le nom de fichier par défaut à *mydefault.html*.</span><span class="sxs-lookup"><span data-stu-id="23ef2-170">The following code shows how to change the default file name to *mydefault.html*.</span></span>

<span data-ttu-id="23ef2-171">[!code-csharp[Main](static-files/sample/StartupDefault.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="23ef2-171">[!code-csharp[Main](static-files/sample/StartupDefault.cs?name=snippet1)]</span></span>

## <a name="usefileserver"></a><span data-ttu-id="23ef2-172">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="23ef2-172">UseFileServer</span></span>

<span data-ttu-id="23ef2-173">`UseFileServer`combine les fonctionnalités de `UseStaticFiles`, `UseDefaultFiles`, et `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="23ef2-173">`UseFileServer` combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="23ef2-174">Le code suivant permet de fichiers statiques et le fichier par défaut pour être pris en charge, mais n’autorise ne pas l’exploration des répertoires :</span><span class="sxs-lookup"><span data-stu-id="23ef2-174">The following code enables static files and the default file to be served, but does not allow directory browsing:</span></span>

```csharp
app.UseFileServer();
   ```

<span data-ttu-id="23ef2-175">Le code suivant permet de valeur par défaut des fichiers statiques et exploration de répertoire :</span><span class="sxs-lookup"><span data-stu-id="23ef2-175">The following code enables static files, default files and  directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
   ```

<span data-ttu-id="23ef2-176">Consultez [considérations](#considerations) sur les risques de sécurité lors de l’exploration.</span><span class="sxs-lookup"><span data-stu-id="23ef2-176">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span> <span data-ttu-id="23ef2-177">Comme avec `UseStaticFiles`, `UseDefaultFiles`, et `UseDirectoryBrowser`, si vous souhaitez que les fichiers qui existent en dehors de la `web root`, instancier et de configurer un `FileServerOptions` objet que vous passez en tant que paramètre à `UseFileServer`.</span><span class="sxs-lookup"><span data-stu-id="23ef2-177">As with `UseStaticFiles`, `UseDefaultFiles`, and `UseDirectoryBrowser`, if you wish to serve files that exist outside the `web root`, you instantiate and configure an `FileServerOptions` object that you pass as a parameter to `UseFileServer`.</span></span> <span data-ttu-id="23ef2-178">Par exemple, étant donné la hiérarchie de répertoires suivants dans votre application Web :</span><span class="sxs-lookup"><span data-stu-id="23ef2-178">For example, given the following directory hierarchy in your Web app:</span></span>

* <span data-ttu-id="23ef2-179">wwwroot</span><span class="sxs-lookup"><span data-stu-id="23ef2-179">wwwroot</span></span>

  * <span data-ttu-id="23ef2-180">css</span><span class="sxs-lookup"><span data-stu-id="23ef2-180">css</span></span>

  * <span data-ttu-id="23ef2-181">images</span><span class="sxs-lookup"><span data-stu-id="23ef2-181">images</span></span>

  * <span data-ttu-id="23ef2-182">...</span><span class="sxs-lookup"><span data-stu-id="23ef2-182">...</span></span>

* <span data-ttu-id="23ef2-183">MyStaticFiles</span><span class="sxs-lookup"><span data-stu-id="23ef2-183">MyStaticFiles</span></span>

  * <span data-ttu-id="23ef2-184">test.png</span><span class="sxs-lookup"><span data-stu-id="23ef2-184">test.png</span></span>

  * <span data-ttu-id="23ef2-185">default.html</span><span class="sxs-lookup"><span data-stu-id="23ef2-185">default.html</span></span>

<span data-ttu-id="23ef2-186">À l’aide de l’exemple de hiérarchie ci-dessus, vous pouvez également activer des fichiers statiques, les fichiers par défaut et la navigation pour la `MyStaticFiles` active.</span><span class="sxs-lookup"><span data-stu-id="23ef2-186">Using the hierarchy example above, you might want to enable static files, default files, and browsing for the `MyStaticFiles` directory.</span></span> <span data-ttu-id="23ef2-187">Dans l’extrait de code suivant, qui est effectuée avec un seul appel à `FileServerOptions`.</span><span class="sxs-lookup"><span data-stu-id="23ef2-187">In the following code snippet, that is accomplished with a single call to `FileServerOptions`.</span></span>

<span data-ttu-id="23ef2-188">[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?highlight=5,6,7,8,9,10,11&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="23ef2-188">[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?highlight=5,6,7,8,9,10,11&name=snippet1)]</span></span>

<span data-ttu-id="23ef2-189">Si `enableDirectoryBrowsing` a la valeur `true` vous sont requis pour appeler `AddDirectoryBrowser` méthode d’extension à partir de `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="23ef2-189">If `enableDirectoryBrowsing` is set to `true` you are required to call `AddDirectoryBrowser` extension method from  `Startup.ConfigureServices`:</span></span>

<span data-ttu-id="23ef2-190">[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?name=snippet2)]</span><span class="sxs-lookup"><span data-stu-id="23ef2-190">[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?name=snippet2)]</span></span>

<span data-ttu-id="23ef2-191">À l’aide de la hiérarchie de fichiers et le code ci-dessus :</span><span class="sxs-lookup"><span data-stu-id="23ef2-191">Using the file hierarchy and code above:</span></span>

| <span data-ttu-id="23ef2-192">URI</span><span class="sxs-lookup"><span data-stu-id="23ef2-192">URI</span></span>            |                             <span data-ttu-id="23ef2-193">Réponse</span><span class="sxs-lookup"><span data-stu-id="23ef2-193">Response</span></span>  |
| ------- | ------|
| `http://<app>/StaticFiles/test.png`    |      <span data-ttu-id="23ef2-194">MyStaticFiles/test.png</span><span class="sxs-lookup"><span data-stu-id="23ef2-194">MyStaticFiles/test.png</span></span> |
| `http://<app>/StaticFiles`              |     <span data-ttu-id="23ef2-195">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="23ef2-195">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="23ef2-196">Si aucune valeur par défaut nommé de fichiers se trouvent dans le *MyStaticFiles* répertoire, http://\<application > / StaticFiles renvoie la liste avec des liens interactifs des répertoires :</span><span class="sxs-lookup"><span data-stu-id="23ef2-196">If no default named files are in the *MyStaticFiles* directory, http://\<app>/StaticFiles returns the directory listing with clickable links:</span></span>

![Liste de fichiers statiques](static-files/_static/db2.PNG)

> [!NOTE]
> <span data-ttu-id="23ef2-198">`UseDefaultFiles`et `UseDirectoryBrowser` prendra l’url http://\<application > / StaticFiles sans la barre oblique de fin et la cause de redirection côté client à http://\<application > /StaticFiles/ (ajout de la barre oblique).</span><span class="sxs-lookup"><span data-stu-id="23ef2-198">`UseDefaultFiles` and `UseDirectoryBrowser` will take the url http://\<app>/StaticFiles without the trailing slash and cause a client side redirect to http://\<app>/StaticFiles/ (adding the trailing slash).</span></span> <span data-ttu-id="23ef2-199">Sans les barre oblique à droite les URL relatives dans les documents serait incorrects.</span><span class="sxs-lookup"><span data-stu-id="23ef2-199">Without the trailing slash relative URLs within the documents would be incorrect.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="23ef2-200">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="23ef2-200">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="23ef2-201">La `FileExtensionContentTypeProvider` classe contient une collection qui mappe les extensions de fichier pour les types de contenu MIME.</span><span class="sxs-lookup"><span data-stu-id="23ef2-201">The `FileExtensionContentTypeProvider` class contains a  collection that maps file extensions to MIME content types.</span></span> <span data-ttu-id="23ef2-202">Dans l’exemple suivant, plusieurs extensions de fichiers sont inscrits pour les types MIME connus, le « .rtf » est remplacé, et « .mp4 » est supprimé.</span><span class="sxs-lookup"><span data-stu-id="23ef2-202">In the following sample, several file extensions are registered to known MIME types, the ".rtf" is replaced, and ".mp4" is removed.</span></span>

<span data-ttu-id="23ef2-203">[!code-csharp[Main](../fundamentals/static-files/sample/StartupFileExtensionContentTypeProvider.cs?highlight=3,4,5,6,7,8,9,10,11,12,19&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="23ef2-203">[!code-csharp[Main](../fundamentals/static-files/sample/StartupFileExtensionContentTypeProvider.cs?highlight=3,4,5,6,7,8,9,10,11,12,19&name=snippet1)]</span></span>

<span data-ttu-id="23ef2-204">Consultez [les types de contenu MIME](http://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="23ef2-204">See   [MIME content types](http://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="23ef2-205">Types de contenu non standard</span><span class="sxs-lookup"><span data-stu-id="23ef2-205">Non-standard content types</span></span>

<span data-ttu-id="23ef2-206">L’intergiciel de fichier statique ASP.NET comprend des types de contenu est connu presque 400.</span><span class="sxs-lookup"><span data-stu-id="23ef2-206">The ASP.NET static file middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="23ef2-207">Si l’utilisateur demande un fichier d’un type de fichier inconnu, l’intergiciel (middleware) fichier statique retourne une réponse HTTP 404 (introuvable).</span><span class="sxs-lookup"><span data-stu-id="23ef2-207">If the user requests a file of an unknown file type, the static file middleware returns a HTTP 404 (Not found) response.</span></span> <span data-ttu-id="23ef2-208">Si l’exploration des répertoires est activée, un lien vers le fichier s’affiche, mais l’URI renvoie une erreur HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="23ef2-208">If directory browsing is enabled, a link to the file will be displayed, but the URI will return an HTTP 404 error.</span></span>

<span data-ttu-id="23ef2-209">Le code suivant permet de servir des types inconnus et restitue le fichier inconnu en tant qu’image.</span><span class="sxs-lookup"><span data-stu-id="23ef2-209">The following code enables serving unknown types and will render the unknown file as an image.</span></span>

<span data-ttu-id="23ef2-210">[!code-csharp[Main](static-files/sample/StartupServeUnknownFileTypes.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="23ef2-210">[!code-csharp[Main](static-files/sample/StartupServeUnknownFileTypes.cs?name=snippet1)]</span></span>

<span data-ttu-id="23ef2-211">Avec le code ci-dessus, une demande pour un fichier avec un type de contenu inconnu s’affichera en tant qu’image.</span><span class="sxs-lookup"><span data-stu-id="23ef2-211">With the code above, a request for a file with an unknown content type will be returned as an image.</span></span>

>[!WARNING]
> <span data-ttu-id="23ef2-212">L’activation de `ServeUnknownFileTypes` constitue un risque de sécurité et l’utilisation est déconseillée.</span><span class="sxs-lookup"><span data-stu-id="23ef2-212">Enabling `ServeUnknownFileTypes` is a security risk and using it is discouraged.</span></span>  <span data-ttu-id="23ef2-213">`FileExtensionContentTypeProvider`(voir ci-dessus) fournit une alternative plus sûre pour servir des fichiers avec les extensions non standard.</span><span class="sxs-lookup"><span data-stu-id="23ef2-213">`FileExtensionContentTypeProvider`  (explained above) provides a safer alternative to serving files with non-standard extensions.</span></span>

### <a name="considerations"></a><span data-ttu-id="23ef2-214">Éléments à prendre en considération</span><span class="sxs-lookup"><span data-stu-id="23ef2-214">Considerations</span></span>

>[!WARNING]
> <span data-ttu-id="23ef2-215">`UseDirectoryBrowser`et `UseStaticFiles` peut entraîner une fuite secrets.</span><span class="sxs-lookup"><span data-stu-id="23ef2-215">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="23ef2-216">Il est recommandé que vous **pas** activer l’exploration de répertoire production.</span><span class="sxs-lookup"><span data-stu-id="23ef2-216">We recommend that you **not** enable directory browsing in production.</span></span> <span data-ttu-id="23ef2-217">Soyez prudent sur les répertoires dans lesquels vous activez avec `UseStaticFiles` ou `UseDirectoryBrowser` comme l’ensemble du répertoire et tous les sous-répertoires seront accessibles.</span><span class="sxs-lookup"><span data-stu-id="23ef2-217">Be careful about which directories you enable with `UseStaticFiles` or `UseDirectoryBrowser` as the entire directory and all sub-directories will be accessible.</span></span> <span data-ttu-id="23ef2-218">Nous recommandons de conserver le contenu public dans son propre répertoire tel que  *\<contenu racine > / wwwroot*, en s’éloignant de vues de l’application, les fichiers de configuration, etc..</span><span class="sxs-lookup"><span data-stu-id="23ef2-218">We recommend keeping public content in its own directory such as *\<content root>/wwwroot*, away from application views, configuration files, etc.</span></span>

* <span data-ttu-id="23ef2-219">Les URL pour le contenu exposé avec `UseDirectoryBrowser` et `UseStaticFiles` sont soumis aux restrictions de caractères de leur système de fichiers sous-jacent et respect de la casse.</span><span class="sxs-lookup"><span data-stu-id="23ef2-219">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of their underlying file system.</span></span> <span data-ttu-id="23ef2-220">Par exemple, Windows respecte la casse, mais Mac et Linux ne sont pas.</span><span class="sxs-lookup"><span data-stu-id="23ef2-220">For example, Windows is case insensitive, but Mac and Linux are not.</span></span>

* <span data-ttu-id="23ef2-221">ASP.NET Core applications hébergées dans IIS utilisent le Module de base ASP.NET pour transférer toutes les demandes à l’application, y compris les demandes de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="23ef2-221">ASP.NET Core applications hosted in IIS use the ASP.NET Core Module to forward all requests to the application including requests for static files.</span></span> <span data-ttu-id="23ef2-222">Le Gestionnaire de fichiers statiques d’IIS n’est pas utilisé, car vous ne trouverez pas une chance de traiter les demandes avant qu’ils sont gérés par le Module de base ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="23ef2-222">The IIS static file handler is not used because it doesn't get a chance to handle requests before they are handled by the ASP.NET Core Module.</span></span>

* <span data-ttu-id="23ef2-223">Pour supprimer le Gestionnaire de fichiers statiques d’IIS (au niveau du serveur ou le site Web) :</span><span class="sxs-lookup"><span data-stu-id="23ef2-223">To remove the IIS static file handler (at the server or website level):</span></span>

     * <span data-ttu-id="23ef2-224">Accédez à la **Modules** fonctionnalité</span><span class="sxs-lookup"><span data-stu-id="23ef2-224">Navigate to the **Modules** feature</span></span>

     * <span data-ttu-id="23ef2-225">Sélectionnez **StaticFileModule** dans la liste</span><span class="sxs-lookup"><span data-stu-id="23ef2-225">Select **StaticFileModule** in the list</span></span>

     * <span data-ttu-id="23ef2-226">Appuyez sur **supprimer** dans les **Actions** barre latérale</span><span class="sxs-lookup"><span data-stu-id="23ef2-226">Tap **Remove** in the **Actions** sidebar</span></span>

>[!WARNING]
> <span data-ttu-id="23ef2-227">Si le Gestionnaire de fichiers statiques d’IIS est activé **et** le Module de base ASP.NET (ANCM) n’est pas configuré correctement (par exemple si *web.config* n’était pas déployé), fichiers statiques seront pris en charge.</span><span class="sxs-lookup"><span data-stu-id="23ef2-227">If the IIS static file handler is enabled **and** the ASP.NET Core Module (ANCM) is not correctly configured (for example if *web.config* was not deployed), static files will be served.</span></span>

* <span data-ttu-id="23ef2-228">Les fichiers de code (y compris les langages c# et Razor) doivent être placés en dehors du projet d’application `web root` (*wwwroot* par défaut).</span><span class="sxs-lookup"><span data-stu-id="23ef2-228">Code files (including c# and Razor) should be placed outside of the app project's `web root` (*wwwroot* by default).</span></span> <span data-ttu-id="23ef2-229">Cette opération crée une séparation nette entre le contenu de côté client de votre application et de code de source côté serveur, ce qui empêche la divulgation code côté serveur.</span><span class="sxs-lookup"><span data-stu-id="23ef2-229">This creates a clean separation between your app's client side content and server side source code, which prevents server side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="23ef2-230">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="23ef2-230">Additional Resources</span></span>

* [<span data-ttu-id="23ef2-231">Intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="23ef2-231">Middleware</span></span>](middleware.md)

* [<span data-ttu-id="23ef2-232">Introduction à ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23ef2-232">Introduction to ASP.NET Core</span></span>](../index.md)
