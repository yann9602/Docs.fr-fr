---
title: Travailler avec des fichiers statiques dans ASP.NET Core
author: rick-anderson
description: "Découvrez comment traiter et sécuriser les fichiers statiques et configurer fichier statique qui héberge les comportements d’intergiciel (middleware) dans une application de web ASP.NET Core."
keywords: ASP.NET Core, fichiers statiques, les ressources statiques, HTML, CSS, JavaScript
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 01/18/2018
ms.devlang: csharp
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: fundamentals/static-files
ms.openlocfilehash: 912923860939a1d1dd91ccc79862e23f9095d161
ms.sourcegitcommit: a3e88639a6bcf8fb4d634036dac93130c464a097
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="work-with-static-files-in-aspnet-core"></a>Travailler avec des fichiers statiques dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Scott Addie](https://twitter.com/Scott_Addie)

Fichiers statiques, tels que HTML, CSS, images et JavaScript, sont des ressources de qu'une application ASP.NET Core sert directement aux clients. Une configuration est requise pour permettre au service de ces fichiers.

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Les fichiers statiques

Fichiers statiques sont stockés dans le répertoire racine de votre projet web. Le répertoire par défaut est  *\<content_root > / wwwroot*, mais il peut être modifié le [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) (méthode). Consultez [racine du contenu](xref:fundamentals/index#content-root) et [racine Web](xref:fundamentals/index#web-root) pour plus d’informations.

Hôte de l’application web doit être informé du répertoire racine du contenu.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Le `WebHost.CreateDefaultBuilder` méthode définit la racine du contenu dans le répertoire actif :

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Définissez comme racine contenue dans le répertoire en cours en appelant [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) à l’intérieur de `Program.Main`:

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

---

Fichiers statiques sont accessibles via un chemin d’accès relatif à la racine web. Par exemple, le **Application Web** modèle de projet contient plusieurs dossiers dans le *wwwroot* dossier :

* **wwwroot**
  * **css**
  * **images**
  * **js**

Le format d’URI pour accéder à un fichier dans le *images* sous-dossier est *http://\<server_address > /images/\<nom_fichier_image >*. Par exemple, *http://localhost:9189/images/banner3.svg*.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Si vous ciblez .NET Framework, ajoutez le [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package pour votre projet. Si le ciblage de .NET Core, la [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) inclut ce package.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Ajouter le [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package pour votre projet.

---

Configurer le [intergiciel (middleware)](xref:fundamentals/middleware) qui permet les serveurs de fichiers statiques.

### <a name="serve-files-inside-of-web-root"></a>Les fichiers au sein de la racine web

Appeler le [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) méthode `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

Sans paramètre `UseStaticFiles` surcharge de méthode marque les fichiers dans la racine web en tant que servable. Les références suivantes de balisage *wwwroot/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

### <a name="serve-files-outside-of-web-root"></a>Les fichiers en dehors de la racine web

Considérez une hiérarchie de répertoires dans lesquels les fichiers statiques pour être pris en charge résident en dehors de la racine web :

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
      * *banner1.svg*

Une demande peut accéder à la *banner1.svg* fichier en configurant l’intergiciel (middleware) de fichiers statiques comme suit :

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

Dans le code précédent, le *MyStaticFiles* hiérarchie de répertoires est exposée publiquement via la *StaticFiles* segment d’URI. Une demande de *http://\<server_address > /StaticFiles/images/banner1.svg* sert le *banner1.svg* fichier.

Les références suivantes de balisage *MyStaticFiles/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Définir les en-têtes de réponse HTTP

A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) objet peut être utilisé pour définir les en-têtes de réponse HTTP. Outre la configuration des services de fichiers statiques à partir de la racine web, le code suivant définit le `Cache-Control` en-tête :

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]

Le [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) méthode existe dans le [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.

Les fichiers ont été apportées à la mise en cache publiquement pendant 10 minutes (600 secondes) :

![En-têtes de réponse indiquant l’en-tête Cache-Control a été ajouté.](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorisation de fichier statique

L’intergiciel (middleware) de fichier statique ne fournit pas les vérifications d’autorisation. Tous les fichiers pris en charge par, notamment ceux de *wwwroot*, sont accessibles publiquement. Pour traiter les fichiers en fonction de l’autorisation :

* Les stocker en dehors de *wwwroot* et n’importe quel répertoire accessible à l’intergiciel (middleware) de fichiers statiques **et**
* Traiter les via une méthode d’action à laquelle l’autorisation est appliquée. Retourner un [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) objet :

[!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Activer l’exploration des répertoires

Exploration des répertoires permet aux utilisateurs de votre application web afficher une liste de répertoires et fichiers contenus dans un répertoire spécifié. Exploration des répertoires est désactivée par défaut pour des raisons de sécurité (voir [considérations](#considerations)). Répertoire Enable navigation en appelant le [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) méthode dans `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Ajouter des services requis en appelant le [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) méthode à partir de `Startup.ConfigureServices`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Le code précédent permet l’exploration des répertoires de la *wwwroot/images* dossier à l’aide de l’URL *http://\<server_address > / MyImages*, avec des liens vers chaque fichier et dossier :

![exploration des répertoires](static-files/_static/dir-browse.png)

Consultez [considérations](#considerations) sur les risques de sécurité lors de l’exploration.

Notez les deux `UseStaticFiles` appelle dans l’exemple suivant. Le premier appel permet les serveurs de fichiers statiques dans le *wwwroot* dossier. Le deuxième appel permet l’exploration des répertoires de la *wwwroot/images* dossier à l’aide de l’URL *http://\<server_address > / MyImages*:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Prendre en charge un document par défaut

Définition d’une page d’accueil par défaut fournit des visiteurs un point de départ logique lors de la visite de votre site. Pour prendre en charge une page par défaut sans que l’utilisateur qualifié de l’URI, appelez le [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) méthode à partir de `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles`doit être appelé avant `UseStaticFiles` pour traiter le fichier par défaut. `UseDefaultFiles`est un module de réécriture d’URL qui ne servent réellement le fichier. Activer l’intergiciel (middleware) de fichiers statiques via `UseStaticFiles` pour traiter le fichier.

Avec `UseDefaultFiles`, les requêtes pour rechercher un dossier :

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

Le premier fichier trouvé dans la liste est traitée comme si la demande était l’URI complet. L’URL de navigateur continue refléter l’URI demandé.

Le code suivant modifie le nom de fichier par défaut à *mydefault.html*:

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

[UseFileServer](/dotnet/api/microsoft.aspnetcore.builder.fileserverextensions.usefileserver#Microsoft_AspNetCore_Builder_FileServerExtensions_UseFileServer_Microsoft_AspNetCore_Builder_IApplicationBuilder_) combine les fonctionnalités de `UseStaticFiles`, `UseDefaultFiles`, et `UseDirectoryBrowser`.

Le code suivant active les serveurs de fichiers statiques et le fichier par défaut. Exploration des répertoires n’est pas activée.

```csharp
app.UseFileServer();
```

Le code suivant s’appuie sur la surcharge sans paramètre en activant l’exploration des répertoires :

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Tenez compte de la hiérarchie de répertoires suivants :

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
      * *banner1.svg*
  * *default.html*

Le code suivant permet de fichiers statiques, fichiers et par défaut, exploration des répertoires de `MyStaticFiles`:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser`doit être appelé lorsque le `EnableDirectoryBrowsing` valeur de propriété est `true`:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

URL à l’aide de la hiérarchie des fichiers et qui précède le code, résoudre comme suit :

| URI            |                             Réponse  |
| ------- | ------|
| *http://\<server_address>/StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http://\<server_address>/StaticFiles*             |     MyStaticFiles/default.html |

Si aucun fichier de la valeur par défaut nommée existe dans le *MyStaticFiles* active, *http://\<server_address > / StaticFiles* renvoie la liste avec des liens interactifs des répertoires :

![Liste de fichiers statiques](static-files/_static/db2.png)

> [!NOTE]
> `UseDefaultFiles`et `UseDirectoryBrowser` utiliser l’URL *http://\<server_address > / StaticFiles* sans la barre oblique pour déclencher un côté client rediriger vers *http://\<server_address > / StaticFiles /*. Notez l’ajout de la barre oblique. Les URL relatives dans les documents sont considérés comme non valides sans barre oblique de fin.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

Le [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) classe contient un `Mappings` propriété agissant comme un mappage des extensions de fichier pour les types de contenu MIME. Dans l’exemple suivant, plusieurs extensions de fichiers sont inscrits pour les types MIME connus. Le *.rtf* extension est remplacée, et *.mp4* est supprimé.

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Consultez [les types de contenu MIME](http://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Types de contenu non standard

L’intergiciel (middleware) de fichiers statiques comprend des types de contenu est connu presque 400. Si l’utilisateur demande un fichier d’un type de fichier inconnu, l’intergiciel (middleware) fichier statique retourne une réponse HTTP 404 (introuvable). Si l’exploration des répertoires est activée, un lien vers le fichier s’affiche. L’URI retourne une erreur HTTP 404.

Le code suivant permet de servir des types inconnus et rend le fichier inconnu en tant qu’image :

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

Avec le code précédent, une demande pour un fichier avec un type de contenu inconnu est retournée en tant qu’image.

> [!WARNING]
> L’activation de [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) est un risque de sécurité. Il est désactivé par défaut, et son utilisation est déconseillée. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) fournit une alternative plus sûre pour servir des fichiers avec les extensions non standard.

### <a name="considerations"></a>Éléments à prendre en considération

> [!WARNING]
> `UseDirectoryBrowser`et `UseStaticFiles` peut entraîner une fuite secrets. Pour désactiver l’exploration de répertoire production est fortement recommandé. Lisez attentivement les répertoires qui sont activés via `UseStaticFiles` ou `UseDirectoryBrowser`. L’ensemble du répertoire et ses sous-répertoires accessibles publiquement. Stockage de fichiers approprié pour le service au public dans un dossier dédié, tel que  *\<content_root > / wwwroot*. À partir de vues MVC, les Pages Razor (2.x uniquement), les fichiers de configuration, etc., séparez ces fichiers.

* Les URL pour le contenu exposé avec `UseDirectoryBrowser` et `UseStaticFiles` sont soumis aux restrictions de caractères du système de fichiers sous-jacent et respect de la casse. Par exemple, Windows respecte la casse&mdash;Mac et Linux ne sont pas.

* Les applications ASP.NET Core hébergées dans IIS utilisation le [ASP.NET Core Module (ANCM)](xref:fundamentals/servers/aspnet-core-module) pour transférer toutes les demandes à l’application, y compris les demandes de fichiers statiques. Le Gestionnaire de fichiers statiques d’IIS n’est pas utilisé. Il a sans possibilité de traiter les demandes avant qu’ils sont gérés par le ANCM.

* Effectuez les étapes suivantes dans le Gestionnaire des services Internet pour supprimer le Gestionnaire de fichiers statiques d’IIS au niveau du serveur ou le site Web :
    1. Accédez à la **Modules** fonctionnalité.
    1. Sélectionnez **StaticFileModule** dans la liste.
    1. Cliquez sur **supprimer** dans les **Actions** barre latérale.

> [!WARNING]
> Si le Gestionnaire de fichiers statiques d’IIS est activé **et** le ANCM est incorrectement configurée, les fichiers statiques sont pris en charge. Cela se produit, par exemple, si le *web.config* fichier n’est pas déployé.

* Placez les fichiers de code (y compris *.cs* et *.cshtml*) en dehors de l’application racine du projet web. Par conséquent, création d’une séparation logique entre contenu côté client et le code basé sur le serveur de l’application. Cela empêche le code côté serveur à partir de la fuite.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Intergiciel (middleware)](xref:fundamentals/middleware)

* [Présentation d’ASP.NET Core](xref:index)