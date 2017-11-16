---
title: "Introduction à ASP.NET Core MVC sur Mac, Linux ou Windows"
author: rick-anderson
description: "Bien démarrer avec ASP.NET Core MVC et Visual Studio Code sur Mac, Linux et Windows"
keywords: ASP.NET Core, MVC, VS Code, Visual Studio Code, Mac, Linux, Windows
ms.author: riande
manager: wpickett
ms.date: 07/07/2017
ms.topic: get-started-article
ms.assetid: 1d18b589-1638-4dc6-1638-fb0f41998d78
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app-xplat/start-mvc
ms.openlocfilehash: 87ce5dca011a7bc37d34799db159d933c158cba1
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="getting-started-with-aspnet-core-mvc--on-mac-linux-or-windows"></a><span data-ttu-id="47518-104">Bien démarrer avec ASP.NET Core MVC sur Mac, Linux ou Windows</span><span class="sxs-lookup"><span data-stu-id="47518-104">Getting started with ASP.NET Core MVC  on Mac, Linux, or Windows</span></span>

<span data-ttu-id="47518-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="47518-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="47518-106">Ce didacticiel présente les principes de base de la génération d’une application web ASP.NET Core MVC à l’aide de [Visual Studio Code](https://code.visualstudio.com) (VS Code).</span><span class="sxs-lookup"><span data-stu-id="47518-106">This tutorial will teach you the basics of building an ASP.NET Core MVC web app using [Visual Studio Code](https://code.visualstudio.com) (VS Code).</span></span> <span data-ttu-id="47518-107">Il part du principe que vous connaissez déjà VS Code.</span><span class="sxs-lookup"><span data-stu-id="47518-107">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="47518-108">Pour plus d’informations, consultez [Bien démarrer avec VS Code](https://code.visualstudio.com/docs) et [Aide de Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="47518-108">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span> [!INCLUDE[consider RP](../../includes/razor.md)]

<span data-ttu-id="47518-109">Il existe trois versions de ce didacticiel :</span><span class="sxs-lookup"><span data-stu-id="47518-109">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="47518-110">macOS : [Créer une application ASP.NET Core MVC avec Visual Studio pour Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="47518-110">macOS: [Create an ASP.NET Core MVC app with Visual Studio for Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span></span>
* <span data-ttu-id="47518-111">Windows : [Créer une application ASP.NET Core MVC avec Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="47518-111">Windows: [Create an ASP.NET Core MVC app with Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span></span>
* <span data-ttu-id="47518-112">macOS, Linux et Windows : [Créer une application ASP.NET Core MVC avec Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="47518-112">macOS, Linux, and Windows: [Create an ASP.NET Core MVC app with Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span></span> 

## <a name="install-vs-code-and-net-core"></a><span data-ttu-id="47518-113">Installer VS Code et .NET Core</span><span class="sxs-lookup"><span data-stu-id="47518-113">Install VS Code and .NET Core</span></span>

<span data-ttu-id="47518-114">Ce didacticiel nécessite le [SDK .NET Core 2.0.0](https://www.microsoft.com/net/core) ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="47518-114">This tutorial requires the [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) or later.</span></span> <span data-ttu-id="47518-115">Consultez [ce fichier PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-mvc-app-mac/start-mvc/8-23-17.pdf) pour la version ASP.NET Core 1.1.</span><span class="sxs-lookup"><span data-stu-id="47518-115">See [the pdf](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-mvc-app-mac/start-mvc/8-23-17.pdf) for the ASP.NET Core 1.1 version.</span></span>

<span data-ttu-id="47518-116">Installez les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="47518-116">Install the following:</span></span>

* <span data-ttu-id="47518-117">[SDK .NET Core 2.0.0](https://www.microsoft.com/net/core) ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="47518-117">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) or later.</span></span>
* [<span data-ttu-id="47518-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47518-118">Visual Studio Code</span></span>](https://code.visualstudio.com)
* <span data-ttu-id="47518-119">[Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) VS Code</span><span class="sxs-lookup"><span data-stu-id="47518-119">VS Code [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)</span></span> 

## <a name="create-a-web-app-with-dotnet"></a><span data-ttu-id="47518-120">Créer une application web avec dotnet</span><span class="sxs-lookup"><span data-stu-id="47518-120">Create a web app with dotnet</span></span>

<span data-ttu-id="47518-121">À partir d’un terminal, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="47518-121">From a terminal, run the following commands:</span></span>

```console
mkdir MvcMovie
cd MvcMovie
dotnet new mvc
```

<span data-ttu-id="47518-122">Ouvrez le dossier *MvcMovie* dans Visual Studio Code (VS Code), puis sélectionnez le fichier *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="47518-122">Open the *MvcMovie* folder in Visual Studio Code (VS Code) and select the *Startup.cs* file.</span></span>

- <span data-ttu-id="47518-123">Sélectionnez **Oui** quand une boîte de dialogue **Avertissement** s’affiche en indiquant le message suivant : « Les composants nécessaires à la build et au débogage sont manquants dans 'MvcMovie'.</span><span class="sxs-lookup"><span data-stu-id="47518-123">Select **Yes** to the **Warn** message "Required assets to build and debug are missing from 'MvcMovie'.</span></span> <span data-ttu-id="47518-124">Faut-il les ajouter ? »</span><span class="sxs-lookup"><span data-stu-id="47518-124">Add them?"</span></span>
- <span data-ttu-id="47518-125">Sélectionnez **Restaurer** quand une boîte de dialogue **Informations** s’affiche en indiquant qu’il existe des dépendances non résolues.</span><span class="sxs-lookup"><span data-stu-id="47518-125">Select **Restore** to the **Info** message "There are unresolved dependencies".</span></span>

![VS Code avec le message d’avertissement indiquant « Les composants nécessaires à la build et au débogage sont manquants dans 'MvcMovie'.](../web-api-vsc/_static/vsc_restore.png)

<span data-ttu-id="47518-129">Appuyez sur **Déboguer** (F5) pour générer et exécuter le programme.</span><span class="sxs-lookup"><span data-stu-id="47518-129">Press **Debug** (F5) to build and run the program.</span></span>

![application en cours d’exécution](../first-mvc-app/start-mvc/_static/1.png)

<span data-ttu-id="47518-131">VS Code démarre le serveur web [Kestrel](xref:fundamentals/servers/kestrel) et exécute votre application.</span><span class="sxs-lookup"><span data-stu-id="47518-131">VS Code starts the [Kestrel](xref:fundamentals/servers/kestrel) web server and runs your app.</span></span> <span data-ttu-id="47518-132">Notez que la barre d’adresse affiche `localhost:5000`, et non quelque chose comme `example.com`.</span><span class="sxs-lookup"><span data-stu-id="47518-132">Notice that the address bar shows `localhost:5000` and not something like `example.com`.</span></span> <span data-ttu-id="47518-133">En effet, `localhost` est le nom d’hôte standard de votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="47518-133">That's because `localhost` is the standard hostname for your local computer.</span></span>

<span data-ttu-id="47518-134">Le modèle par défaut donne des liens **Accueil, À propos** et **Contact** fonctionnels.</span><span class="sxs-lookup"><span data-stu-id="47518-134">The default template gives you working **Home, About** and **Contact** links.</span></span> <span data-ttu-id="47518-135">L’image de navigateur ci-dessus ne montre pas ces liens.</span><span class="sxs-lookup"><span data-stu-id="47518-135">The browser image above doesn't show these links.</span></span> <span data-ttu-id="47518-136">En fonction de la taille de votre navigateur, vous devrez peut-être cliquer sur l’icône de navigation pour les afficher.</span><span class="sxs-lookup"><span data-stu-id="47518-136">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![Icône de navigation en haut à droite](../first-mvc-app/start-mvc/_static/2.png)

<span data-ttu-id="47518-138">Dans la prochaine partie de ce didacticiel, nous allons découvrir MVC et commencer à écrire du code.</span><span class="sxs-lookup"><span data-stu-id="47518-138">In the next part of this tutorial, we'll learn about MVC and start writing some code.</span></span>

## <a name="visual-studio-code-help"></a><span data-ttu-id="47518-139">Aide de Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47518-139">Visual Studio Code help</span></span>

- [<span data-ttu-id="47518-140">Prise en main</span><span class="sxs-lookup"><span data-stu-id="47518-140">Getting started</span></span>](https://code.visualstudio.com/docs)
- [<span data-ttu-id="47518-141">Débogage</span><span class="sxs-lookup"><span data-stu-id="47518-141">Debugging</span></span>](https://code.visualstudio.com/docs/editor/debugging)
- [<span data-ttu-id="47518-142">Terminal intégré</span><span class="sxs-lookup"><span data-stu-id="47518-142">Integrated terminal</span></span>](https://code.visualstudio.com/docs/editor/integrated-terminal)
- [<span data-ttu-id="47518-143">Raccourcis clavier</span><span class="sxs-lookup"><span data-stu-id="47518-143">Keyboard shortcuts</span></span>](https://code.visualstudio.com/docs/getstarted/keybindings#_keyboard-shortcuts-reference)

  - [<span data-ttu-id="47518-144">Raccourcis clavier Mac</span><span class="sxs-lookup"><span data-stu-id="47518-144">Mac keyboard shortcuts</span></span>](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf)
  - [<span data-ttu-id="47518-145">Raccourcis clavier Linux</span><span class="sxs-lookup"><span data-stu-id="47518-145">Linux keyboard shortcuts</span></span>](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-linux.pdf)
  - [<span data-ttu-id="47518-146">Raccourcis clavier Windows</span><span class="sxs-lookup"><span data-stu-id="47518-146">Windows keyboard shortcuts</span></span>](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf)

>[!div class="step-by-step"]
[<span data-ttu-id="47518-147">Suivant - Ajouter un contrôleur</span><span class="sxs-lookup"><span data-stu-id="47518-147">Next - Add a controller</span></span>](adding-controller.md)
