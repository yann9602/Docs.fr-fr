---
title: Lien du navigateur dans ASP.NET Core
author: ncarandini
description: "Explique comment le lien du navigateur est une fonctionnalité de Visual Studio qui lie l’environnement de développement avec un ou plusieurs navigateurs web."
ms.author: riande
manager: wpickett
ms.date: 09/22/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/using-browserlink
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d5db65c268923e96c45b034639437fc3496ccac1
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="browser-link-in-aspnet-core"></a>Lien du navigateur dans ASP.NET Core 

Par [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), et [Tom Dykstra](https://github.com/tdykstra)

Lien du navigateur est une fonctionnalité de Visual Studio qui crée un canal de communication entre l’environnement de développement et un ou plusieurs navigateurs web. Vous pouvez utiliser lien du navigateur pour actualiser votre application web dans plusieurs navigateurs à la fois, ce qui est utile pour le test de plusieurs navigateurs.

## <a name="browser-link-setup"></a>Programme d’installation lien du navigateur

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Le cœur d’ASP.NET 2.x **Application Web**, **vide**, et **API Web** modèle projets utilisent la [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) méta-package qui contient une référence de package pour [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Par conséquent, à l’aide de la `Microsoft.AspNetCore.All` méta-package ne nécessite aucune action supplémentaire pour que le lien du navigateur disponibles.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Le cœur d’ASP.NET 1.x **Application Web** modèle de projet a une référence de package pour le [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package. Le **vide** ou **API Web** les projets de modèle, vous devez ajouter une référence de package à `Microsoft.VisualStudio.Web.BrowserLink`.

Dans la mesure où cela est une fonctionnalité de Visual Studio, le moyen le plus simple pour ajouter le package à un **vide** ou **API Web** modèle de projet consiste à ouvrir le **Package Manager Console** (**Vue** > **autres fenêtres** > **Package Manager Console**) et exécutez la commande suivante :

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

Vous pouvez également utiliser **Gestionnaire de Package NuGet**. Cliquez sur le nom du projet dans **l’Explorateur de solutions** et choisissez **gérer les Packages NuGet**:

![Gestionnaire de Package NuGet ouvert](using-browserlink/_static/open-nuget-package-manager.png)

Recherchez et installez le package :

![Ajouter un package avec le Gestionnaire de Package NuGet](using-browserlink/_static/add-package-with-nuget-package-manager.png)

---

### <a name="configuration"></a>Configuration

Dans le `Configure` méthode de la *Startup.cs* fichier :

```csharp
app.UseBrowserLink();
```

Le code est généralement à l’intérieur d’un `if` bloc qui permet uniquement de lien de navigateur dans l’environnement de développement, comme indiqué ici :

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Pour plus d’informations, consultez [Utilisation de plusieurs environnements](xref:fundamentals/environments).

## <a name="how-to-use-browser-link"></a>Comment utiliser le lien du navigateur

Lorsque vous avez un projet ASP.NET Core ouvert, Visual Studio affiche le contrôle de barre d’outils du lien du navigateur à côté du **cible de débogage** contrôle de barre d’outils :

![Menu de liste déroulante de lien de navigateur](using-browserlink/_static/browserLink-dropdown-menu.png)

À partir du contrôle de barre d’outils de lien de navigateur, vous pouvez :

* Actualiser l’application web dans plusieurs navigateurs à la fois.
* Ouvrez le **le tableau de bord navigateur lien**.
* Activer ou désactiver **lien du navigateur**. Remarque : Le lien du navigateur est désactivée par défaut dans Visual Studio 2017 (15,3).
* Activer ou désactiver [la synchronisation automatique CSS](#enable-or-disable-css-auto-sync).

> [!NOTE]
> Certains plug-ins de Visual Studio, notamment *Web Extension Pack 2015* et *Web Extension Pack 2017*, proposent des fonctionnalités étendues pour le lien du navigateur, mais certaines des fonctionnalités supplémentaires ne fonctionnent pas avec ASP. Projets NET Core.

## <a name="refresh-the-web-application-in-several-browsers-at-once"></a>Actualiser à la fois l’application web dans plusieurs navigateurs

Pour choisir un navigateur web unique pour lancer au démarrage du projet, utilisez le menu déroulant dans le **cible de débogage** contrôle de barre d’outils :

![Menu déroulant de F5](using-browserlink/_static/debug-target-dropdown-menu.png)

Pour ouvrir plusieurs navigateurs à la fois, choisissez **naviguer avec...**  à partir de la même liste déroulante. Maintenez enfoncée la touche CTRL ENFONCÉE pour sélectionner les navigateurs de votre choix, puis cliquez sur **Parcourir**:

![Ouvrir à la fois des nombreux navigateurs](using-browserlink/_static/open-many-browsers-at-once.png)

Voici une capture d’écran montrant Visual Studio avec la vue Index ouvert et de deux navigateurs ouverts :

![Synchroniser avec l’exemple de deux navigateurs](using-browserlink/_static/sync-with-two-browsers-example.png)

Placez le curseur sur le contrôle de barre d’outils de lien de navigateur pour voir les navigateurs qui sont connectés au projet :

![Placez le curseur pointe](using-browserlink/_static/hoover-tip.png)

Modifier l’affichage de l’Index, et tous les navigateurs connectés sont mis à jour lorsque vous cliquez sur le bouton Actualiser de lien du navigateur :

![navigateurs-sync-changes](using-browserlink/_static/browsers-sync-to-changes.png)

Lien du navigateur fonctionne également avec les navigateurs que vous lancez en dehors de Visual Studio et accédez à l’URL de l’application.

### <a name="the-browser-link-dashboard"></a>Le tableau de bord de lien de navigateur

Dans la liste déroulante de lien du navigateur vers le bas du menu pour gérer les connexions avec les navigateurs ouverts, ouvrez le tableau de bord de lien de navigateur :

![Ouvrir-browserslink-tableau de bord](using-browserlink/_static/open-browserlink-dashboard.png)

Si aucun navigateur n’est connecté, vous pouvez démarrer une session de débogage non en sélectionnant le *afficher dans le navigateur* lien :

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

Dans le cas contraire, les navigateurs connectés sont affichés avec le chemin d’accès à la page qui affiche chaque navigateur :

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Si vous le souhaitez, vous pouvez cliquer sur un nom de navigateur répertoriés pour actualiser ce seul navigateur.

### <a name="enable-or-disable-browser-link"></a>Activer ou désactiver le lien du navigateur

Lorsque vous réactivez lien du navigateur après l’avoir désactivé, vous devez actualiser le navigateur pour vous reconnecter les.

### <a name="enable-or-disable-css-auto-sync"></a>Activer ou désactiver la synchronisation automatique CSS

Lorsque la synchronisation automatique CSS est activée, les navigateurs connectés sont automatiquement actualisées lorsque vous modifiez des fichiers CSS.

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Lien du navigateur utilise SignalR pour créer un canal de communication entre Visual Studio et le navigateur. Lorsque le lien du navigateur est activée, Visual Studio fait Office de serveur SignalR plusieurs clients (navigateurs) pouvant se connecter à. Lien du navigateur enregistre également un composant d’intergiciel (middleware) dans le pipeline de demande ASP.NET. Ce composant injecte spéciaux `<script>` références dans chaque demande de page à partir du serveur. Vous pouvez consulter les références de script en sélectionnant **afficher la source** dans le navigateur et le défilement à la fin de la `<body>` le contenu de la balise :

```javascript
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Vos fichiers sources ne sont pas modifiés. Le composant d’intergiciel (middleware) injecte les références de script dynamiquement. 

Le code côté navigateur étant toutes les fonctions JavaScript, il fonctionne sur tous les navigateurs prenant en charge SignalR sans nécessiter un plug-in de navigateur.
