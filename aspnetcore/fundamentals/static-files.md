---
title: Utilisation des fichiers statiques dans ASP.NET Core
author: rick-anderson
description: "Découvrez comment travailler avec des fichiers statiques dans ASP.NET Core."
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
ms.openlocfilehash: 40c9a799c6ac8a2ce712df4b8fbf3c142ef3fd82
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="working-with-static-files-in-aspnet-core"></a>Utilisation des fichiers statiques dans ASP.NET Core

<a name="fundamentals-static-files"></a>

De [Rick Anderson](https://twitter.com/RickAndMSFT)

Les fichiers statiques, tels que HTML, CSS, image et JavaScript, sont actifs qu’une application ASP.NET Core peut gérer directement aux clients.

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/static-files/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="serving-static-files"></a>Fichiers statiques

Fichiers statiques sont généralement placés dans le `web root` (*\<contenu racine > / wwwroot*) dossier. Consultez [racine du contenu](xref:fundamentals/index#content-root) et [racine Web](xref:fundamentals/index#web-root) pour plus d’informations. Vous définissez généralement la racine de contenu du répertoire actif afin que votre projet `web root` introuvable dans le développement.

[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=5&start=12&end=22)]

Fichiers statiques peuvent être stockées dans un dossier sous le `web root` et est accessible avec un chemin d’accès relatif à cette racine. Par exemple, lorsque vous créez un projet d’application Web par défaut à l’aide de Visual Studio, il existe plusieurs dossiers créés dans le *wwwroot* dossier - *css*, *images*, et *js*. L’URI pour accéder à une image dans le *images* sous-dossier :

* `http://<app>/images/<imageFileName>`
* `http://localhost:9189/images/banner3.svg`

Pour des fichiers statiques, vous devez configurer le [intergiciel (middleware)](middleware.md) pour ajouter des fichiers statiques pour le pipeline. L’intergiciel (middleware) de fichiers statiques peut être configuré en ajoutant une dépendance sur le *Microsoft.AspNetCore.StaticFiles* package pour votre projet et en appelant le `UseStaticFiles` méthode d’extension à partir de `Startup.Configure`:

[!code-csharp[Main](../fundamentals/static-files/sample/StartupStaticFiles.cs?highlight=3&name=snippet1)]

`app.UseStaticFiles();`rend les fichiers dans `web root` (*wwwroot* par défaut) servable. Plus tard, je vais montrer comment rendre le contenu du répertoire autres servable avec `UseStaticFiles`.

Vous devez inclure le package NuGet « Microsoft.AspNetCore.StaticFiles ».

> [!NOTE]
> `web root`valeur par défaut est la *wwwroot* active, mais vous pouvez définir le `web root` répertoire `UseWebRoot`.

Supposez que vous avez une hiérarchie de projet dans lequel les fichiers statiques que vous souhaitez proposer sont en dehors de la `web root`. Exemple :

* wwwroot
  * css
  * images
  * ...
* MyStaticFiles
  * test.png

Pour une demande d’accès aux *test.png*, configurez l’intergiciel (middleware) des fichiers statiques comme suit :

[!code-csharp[Main](../fundamentals/static-files/sample/StartupTwoStaticFiles.cs?highlight=5,6,7,8,9,10&name=snippet1)]

Une demande de `http://<app>/StaticFiles/test.png` servira le *test.png* fichier.

`StaticFileOptions()`peut définir des en-têtes de réponse. Par exemple, le code ci-dessous configure d’utilisation à partir de fichiers statiques le *wwwroot* dossier et définit le `Cache-Control` en-tête pour les rendre public pouvant être pendant 10 minutes (600 secondes) :

[!code-csharp[Main](../fundamentals/static-files/sample/StartupAddHeader.cs?name=snippet1)]

![En-têtes de réponse indiquant l’en-tête Cache-Control a été ajouté.](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorisation de fichier statique

Le module de fichier statique fournit **aucune** vérifications d’autorisation. Tous les fichiers pris en charge par, notamment ceux de *wwwroot* sont accessibles. Pour traiter les fichiers en fonction de l’autorisation :

* Les stocker en dehors de *wwwroot* et n’importe quel répertoire accessible à l’intergiciel (middleware) de fichiers statiques **et**

* Prendre en charge via une action de contrôleur, en retournant un `FileResult` où l’autorisation est appliquée

## <a name="enabling-directory-browsing"></a>Activer l’exploration de répertoire

Exploration des répertoires permet à l’utilisateur de votre application web afficher la liste des répertoires et fichiers contenus dans un répertoire spécifié. Exploration des répertoires est désactivée par défaut pour des raisons de sécurité (voir [considérations](#considerations)). Pour activer l’exploration des répertoires, appelez le `UseDirectoryBrowser` méthode d’extension à partir de `Startup.Configure`:

[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet1)]

Et ajouter des services requis en appelant `AddDirectoryBrowser` méthode d’extension à partir de `Startup.ConfigureServices`:

[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet2)]

Le code ci-dessus permet l’exploration des répertoires de la *wwwroot/images* dossier à l’aide de l’URL http://\<application > / MyImages, avec des liens vers chaque fichier et dossier :

![exploration des répertoires](static-files/_static/dir-browse.png)

Consultez [considérations](#considerations) sur les risques de sécurité lors de l’exploration.

Notez les deux `app.UseStaticFiles` appels. Le premier élément est requis pour servir le CSS, des images et JavaScript dans les *wwwroot* dossier et le deuxième appel pour l’exploration des répertoires de la *wwwroot/images* dossier à l’aide de l’URL http://\<application > / MyImages :

[!code-csharp[Main](static-files/sample/StartupBrowse.cs?highlight=3,5&name=snippet1)]

## <a name="serving-a-default-document"></a>Desservant un document par défaut

Définition d’une page d’accueil par défaut donne les visiteurs du site un point de départ lors de la visite de votre site. Dans l’ordre pour votre application Web à répondre à une page par défaut sans qualifier complètement l’URI de l’utilisateur, appelez le `UseDefaultFiles` méthode d’extension à partir de `Startup.Configure` comme suit.

[!code-csharp[Main](../fundamentals/static-files/sample/StartupEmpty.cs?highlight=3&name=snippet1)]

> [!NOTE]
> `UseDefaultFiles`doit être appelé avant `UseStaticFiles` pour traiter le fichier par défaut. `UseDefaultFiles`est une réécriture URL qui ne servent réellement le fichier. Vous devez activer l’intergiciel (middleware) de fichiers statiques (`UseStaticFiles`) pour traiter le fichier.

Avec `UseDefaultFiles`, rechercheront les demandes vers un dossier :

* default.htm
* default.html
* index.htm
* index.html

Le premier fichier trouvé dans la liste sera pris en charge que si la demande a l’adresse URI complète (bien que l’URL de navigateur continue d’afficher l’URI demandé).

Le code suivant montre comment modifier le nom de fichier par défaut à *mydefault.html*.

[!code-csharp[Main](static-files/sample/StartupDefault.cs?name=snippet1)]

## <a name="usefileserver"></a>UseFileServer

`UseFileServer`combine les fonctionnalités de `UseStaticFiles`, `UseDefaultFiles`, et `UseDirectoryBrowser`.

Le code suivant permet de fichiers statiques et le fichier par défaut pour être pris en charge, mais n’autorise ne pas l’exploration des répertoires :

```csharp
app.UseFileServer();
   ```

Le code suivant permet de valeur par défaut des fichiers statiques et exploration de répertoire :

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
   ```

Consultez [considérations](#considerations) sur les risques de sécurité lors de l’exploration. Comme avec `UseStaticFiles`, `UseDefaultFiles`, et `UseDirectoryBrowser`, si vous souhaitez que les fichiers qui existent en dehors de la `web root`, instancier et de configurer un `FileServerOptions` objet que vous passez en tant que paramètre à `UseFileServer`. Par exemple, étant donné la hiérarchie de répertoires suivants dans votre application Web :

* wwwroot

  * css

  * images

  * ...

* MyStaticFiles

  * test.png

  * default.html

À l’aide de l’exemple de hiérarchie ci-dessus, vous pouvez également activer des fichiers statiques, les fichiers par défaut et la navigation pour la `MyStaticFiles` active. Dans l’extrait de code suivant, qui est effectuée avec un seul appel à `FileServerOptions`.

[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?highlight=5,6,7,8,9,10,11&name=snippet1)]

Si `enableDirectoryBrowsing` a la valeur `true` vous sont requis pour appeler `AddDirectoryBrowser` méthode d’extension à partir de `Startup.ConfigureServices`:

[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?name=snippet2)]

À l’aide de la hiérarchie de fichiers et le code ci-dessus :

| URI            |                             Réponse  |
| ------- | ------|
| `http://<app>/StaticFiles/test.png`    |      MyStaticFiles/test.png |
| `http://<app>/StaticFiles`              |     MyStaticFiles/default.html |

Si aucune valeur par défaut nommé de fichiers se trouvent dans le *MyStaticFiles* répertoire, http://\<application > / StaticFiles renvoie la liste avec des liens interactifs des répertoires :

![Liste de fichiers statiques](static-files/_static/db2.PNG)

> [!NOTE]
> `UseDefaultFiles`et `UseDirectoryBrowser` prendra l’url http://\<application > / StaticFiles sans la barre oblique de fin et la cause de redirection côté client à http://\<application > /StaticFiles/ (ajout de la barre oblique). Sans les barre oblique à droite les URL relatives dans les documents serait incorrects.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

La `FileExtensionContentTypeProvider` classe contient une collection qui mappe les extensions de fichier pour les types de contenu MIME. Dans l’exemple suivant, plusieurs extensions de fichiers sont inscrits pour les types MIME connus, le « .rtf » est remplacé, et « .mp4 » est supprimé.

[!code-csharp[Main](../fundamentals/static-files/sample/StartupFileExtensionContentTypeProvider.cs?highlight=3,4,5,6,7,8,9,10,11,12,19&name=snippet1)]

Consultez [les types de contenu MIME](http://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Types de contenu non standard

L’intergiciel de fichier statique ASP.NET comprend des types de contenu est connu presque 400. Si l’utilisateur demande un fichier d’un type de fichier inconnu, l’intergiciel (middleware) fichier statique retourne une réponse HTTP 404 (introuvable). Si l’exploration des répertoires est activée, un lien vers le fichier s’affiche, mais l’URI renvoie une erreur HTTP 404.

Le code suivant permet de servir des types inconnus et restitue le fichier inconnu en tant qu’image.

[!code-csharp[Main](static-files/sample/StartupServeUnknownFileTypes.cs?name=snippet1)]

Avec le code ci-dessus, une demande pour un fichier avec un type de contenu inconnu s’affichera en tant qu’image.

>[!WARNING]
> L’activation de `ServeUnknownFileTypes` constitue un risque de sécurité et l’utilisation est déconseillée.  `FileExtensionContentTypeProvider`(voir ci-dessus) fournit une alternative plus sûre pour servir des fichiers avec les extensions non standard.

### <a name="considerations"></a>Éléments à prendre en considération

>[!WARNING]
> `UseDirectoryBrowser`et `UseStaticFiles` peut entraîner une fuite secrets. Il est recommandé que vous **pas** activer l’exploration de répertoire production. Soyez prudent sur les répertoires dans lesquels vous activez avec `UseStaticFiles` ou `UseDirectoryBrowser` comme l’ensemble du répertoire et tous les sous-répertoires seront accessibles. Nous recommandons de conserver le contenu public dans son propre répertoire tel que  *\<contenu racine > / wwwroot*, en s’éloignant de vues de l’application, les fichiers de configuration, etc.

* Les URL pour le contenu exposé avec `UseDirectoryBrowser` et `UseStaticFiles` sont soumis aux restrictions de caractères de leur système de fichiers sous-jacent et respect de la casse. Par exemple, Windows respecte la casse, mais Mac et Linux ne sont pas.

* ASP.NET Core applications hébergées dans IIS utilisent le Module de base ASP.NET pour transférer toutes les demandes à l’application, y compris les demandes de fichiers statiques. Le Gestionnaire de fichiers statiques d’IIS n’est pas utilisé, car vous ne trouverez pas une chance de traiter les demandes avant qu’ils sont gérés par le Module de base ASP.NET.

* Pour supprimer le Gestionnaire de fichiers statiques d’IIS (au niveau du serveur ou le site Web) :

     * Accédez à la **Modules** fonctionnalité

     * Sélectionnez **StaticFileModule** dans la liste

     * Appuyez sur **supprimer** dans les **Actions** barre latérale

>[!WARNING]
> Si le Gestionnaire de fichiers statiques d’IIS est activé **et** le Module de base ASP.NET (ANCM) n’est pas configuré correctement (par exemple si *web.config* n’était pas déployé), fichiers statiques seront pris en charge.

* Les fichiers de code (y compris les langages c# et Razor) doivent être placés en dehors du projet d’application `web root` (*wwwroot* par défaut). Cette opération crée une séparation nette entre le contenu de côté client de votre application et de code de source côté serveur, ce qui empêche la divulgation code côté serveur.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Intergiciel (middleware)](middleware.md)

* [Présentation d’ASP.NET Core](../index.md)
