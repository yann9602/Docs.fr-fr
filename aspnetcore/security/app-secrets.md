---
title: "Stockage sécurisé des secrets d’application pendant le développement dans ASP.NET Core"
author: rick-anderson
description: "Montre comment stocker des clés secrètes en toute sécurité pendant le développement"
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 09/15/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/app-secrets
ms.openlocfilehash: 280819a6a0afb72311f0d50f7d3b83a942e9fcc3
ms.sourcegitcommit: e3b1726cc04e80dc28464c35259edbd3bc39a438
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2017
---
# <a name="safe-storage-of-app-secrets-during-development-in-aspnet-core"></a><span data-ttu-id="08bd3-104">Stockage sécurisé des secrets d’application pendant le développement dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08bd3-104">Safe storage of app secrets during development in ASP.NET Core</span></span>

<span data-ttu-id="08bd3-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Michel Roth](https://github.com/danroth27), et [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="08bd3-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://scottaddie.com)</span></span> 

<span data-ttu-id="08bd3-106">Ce document montre comment vous pouvez utiliser l’outil Gestionnaire de la clé secrète dans le développement de conserver les clés secrètes en dehors de votre code.</span><span class="sxs-lookup"><span data-stu-id="08bd3-106">This document shows how you can use the Secret Manager tool in development to keep secrets out of your code.</span></span> <span data-ttu-id="08bd3-107">Le point le plus important est de vous ne devez jamais stocker les mots de passe ou d’autres données sensibles dans le code source, et vous ne devez pas utiliser les clés secrètes de production en mode de développement et de test.</span><span class="sxs-lookup"><span data-stu-id="08bd3-107">The most important point is you should never store passwords or other sensitive data in source code, and you shouldn't use production secrets in development and test mode.</span></span> <span data-ttu-id="08bd3-108">Vous pouvez utiliser à la place la [configuration](../fundamentals/configuration.md) système à lire ces valeurs à partir de variables d’environnement ou à partir de valeurs stockées à l’aide du Gestionnaire de Secret outil.</span><span class="sxs-lookup"><span data-stu-id="08bd3-108">You can instead use the [configuration](../fundamentals/configuration.md) system to read these values from environment variables or from values stored using the Secret Manager tool.</span></span> <span data-ttu-id="08bd3-109">L’outil Gestionnaire de Secret empêche les données sensibles en cours de vérification dans le contrôle de code source.</span><span class="sxs-lookup"><span data-stu-id="08bd3-109">The Secret Manager tool helps prevent sensitive data from being checked into source control.</span></span> <span data-ttu-id="08bd3-110">Le [configuration](../fundamentals/configuration.md) système peut lire les clés secrètes stockées avec l’outil Gestionnaire de la clé secrète décrit dans cet article.</span><span class="sxs-lookup"><span data-stu-id="08bd3-110">The [configuration](../fundamentals/configuration.md) system can read secrets stored with the Secret Manager tool described in this article.</span></span>

<span data-ttu-id="08bd3-111">L’outil Gestionnaire de la clé secrète est utilisée uniquement dans le développement.</span><span class="sxs-lookup"><span data-stu-id="08bd3-111">The Secret Manager tool is used only in development.</span></span> <span data-ttu-id="08bd3-112">Vous pouvez protéger des secrets de test et de production Azure avec la [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="08bd3-112">You can safeguard Azure test and production secrets with the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) configuration provider.</span></span> <span data-ttu-id="08bd3-113">Consultez [fournisseur de configuration d’Azure Key Vault](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="08bd3-113">See [Azure Key Vault configuration provider](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration) for more information.</span></span>

## <a name="environment-variables"></a><span data-ttu-id="08bd3-114">Variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="08bd3-114">Environment variables</span></span>

<span data-ttu-id="08bd3-115">Pour éviter de stocker des secrets de l’application dans le code ou dans les fichiers de configuration local, vous stockez des secrets dans des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="08bd3-115">To avoid storing app secrets in code or in local configuration files, you store secrets in environment variables.</span></span> <span data-ttu-id="08bd3-116">Vous pouvez configurer le [configuration](../fundamentals/configuration.md) framework pour lire les valeurs de variables d’environnement en appelant `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="08bd3-116">You can setup the [configuration](../fundamentals/configuration.md) framework to read values from environment variables by calling `AddEnvironmentVariables`.</span></span> <span data-ttu-id="08bd3-117">Vous pouvez ensuite utiliser des variables d’environnement pour remplacer des valeurs de configuration pour toutes les sources de configuration spécifié précédemment.</span><span class="sxs-lookup"><span data-stu-id="08bd3-117">You can then use environment variables to override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="08bd3-118">Par exemple, si vous créez une application web ASP.NET Core avec les comptes d’utilisateur individuels, il ajoutera une chaîne de connexion par défaut pour le *appsettings.json* fichier dans le projet avec la clé `DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="08bd3-118">For example, if you create a new ASP.NET Core web app with individual user accounts, it will add a default connection string to the *appsettings.json* file in the project with the key `DefaultConnection`.</span></span> <span data-ttu-id="08bd3-119">La chaîne de connexion par défaut est configuré pour utiliser la base de données locale, qui s’exécute en mode utilisateur et ne nécessite pas un mot de passe.</span><span class="sxs-lookup"><span data-stu-id="08bd3-119">The default connection string is setup to use LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="08bd3-120">Lorsque vous déployez votre application sur un serveur de test ou de production, vous pouvez remplacer le `DefaultConnection` valeur de la clé avec un paramètre de variable d’environnement qui contient la chaîne de connexion (avec éventuellement des informations d’identification sensibles) pour une base de données de test ou de production serveur.</span><span class="sxs-lookup"><span data-stu-id="08bd3-120">When you deploy your application to a test or production server, you can override the `DefaultConnection` key value with an environment variable setting that contains the connection string (potentially with sensitive credentials) for a test or production database server.</span></span>

>[!WARNING]
> <span data-ttu-id="08bd3-121">Variables d’environnement sont généralement stockés en texte brut et ne sont pas chiffrés.</span><span class="sxs-lookup"><span data-stu-id="08bd3-121">Environment variables are generally stored in plain text and are not encrypted.</span></span> <span data-ttu-id="08bd3-122">Si l’ordinateur ou le processus est compromis, puis les variables d’environnement accessibles par des tiers non approuvés.</span><span class="sxs-lookup"><span data-stu-id="08bd3-122">If the machine or process is compromised, then environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="08bd3-123">Des mesures supplémentaires pour empêcher la divulgation de secrets de l’utilisateur peuvent toujours être nécessaire.</span><span class="sxs-lookup"><span data-stu-id="08bd3-123">Additional measures to prevent disclosure of user secrets may still be required.</span></span>

## <a name="secret-manager"></a><span data-ttu-id="08bd3-124">Gestionnaire de clé secrète</span><span class="sxs-lookup"><span data-stu-id="08bd3-124">Secret Manager</span></span>

<span data-ttu-id="08bd3-125">L’outil Gestionnaire de secret principal stocke des données sensibles pour les travaux de développement en dehors de l’arborescence de votre projet.</span><span class="sxs-lookup"><span data-stu-id="08bd3-125">The Secret Manager tool stores sensitive data for development work outside of your project tree.</span></span> <span data-ttu-id="08bd3-126">L’outil Gestionnaire de la clé secrète est un outil de projet qui peut être utilisé pour stocker des secrets pour un [.NET Core](https://www.microsoft.com/net/core) projet pendant le développement.</span><span class="sxs-lookup"><span data-stu-id="08bd3-126">The Secret Manager tool is a project tool that can be used to store secrets for a [.NET Core](https://www.microsoft.com/net/core) project during development.</span></span> <span data-ttu-id="08bd3-127">Avec l’outil Gestionnaire de la clé secrète, vous pouvez associer des secrets de l’application à un projet spécifique et les partager entre plusieurs projets.</span><span class="sxs-lookup"><span data-stu-id="08bd3-127">With the Secret Manager tool, you can associate app secrets with a specific project and share them across multiple projects.</span></span>

>[!WARNING]
> <span data-ttu-id="08bd3-128">Le Gestionnaire du Secret ne chiffre pas les clés secrètes stockées et ne doit pas être considérée comme un magasin approuvé.</span><span class="sxs-lookup"><span data-stu-id="08bd3-128">The Secret Manager tool does not encrypt the stored secrets and should not be treated as a trusted store.</span></span> <span data-ttu-id="08bd3-129">Il est uniquement à des fins de développement.</span><span class="sxs-lookup"><span data-stu-id="08bd3-129">It is for development purposes only.</span></span> <span data-ttu-id="08bd3-130">Les clés et valeurs sont stockées dans un fichier de configuration JSON dans le répertoire de profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="08bd3-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="installing-the-secret-manager-tool"></a><span data-ttu-id="08bd3-131">Installation de l’outil Gestionnaire de la clé secrète</span><span class="sxs-lookup"><span data-stu-id="08bd3-131">Installing the Secret Manager tool</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="08bd3-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="08bd3-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="08bd3-133">Cliquez sur le projet dans l’Explorateur de solutions, puis sélectionnez **modifier \<project_name\>.csproj** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="08bd3-133">Right-click the project in Solution Explorer, and select **Edit \<project_name\>.csproj** from the context menu.</span></span> <span data-ttu-id="08bd3-134">Ajoutez la ligne en surbrillance à le *.csproj* de fichiers et enregistrer pour restaurer le package NuGet associé :</span><span class="sxs-lookup"><span data-stu-id="08bd3-134">Add the highlighted line to the *.csproj* file, and save to restore the associated NuGet package:</span></span>

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-before.csproj?highlight=10)]

<span data-ttu-id="08bd3-135">Cliquez à nouveau sur le projet dans l’Explorateur de solutions, puis sélectionnez **gérer les Secrets utilisateur** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="08bd3-135">Right-click the project in Solution Explorer again, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="08bd3-136">Cette opération ajoute un nouveau `UserSecretsId` nœud au sein d’un `PropertyGroup` de la *.csproj* fichier, comme illustré dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="08bd3-136">This gesture adds a new `UserSecretsId` node within a `PropertyGroup` of the *.csproj* file, as highlighted in the following sample:</span></span>

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-after.csproj?highlight=4)]

<span data-ttu-id="08bd3-137">L’enregistrement modifié *.csproj* fichier également s’ouvre un `secrets.json` fichier dans l’éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="08bd3-137">Saving the modified *.csproj* file also opens a `secrets.json` file in the text editor.</span></span> <span data-ttu-id="08bd3-138">Remplacez le contenu de la `secrets.json` fichier avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="08bd3-138">Replace the contents of the `secrets.json` file with the following code:</span></span>

```json
{
    "MySecret": "ValueOfMySecret"
}
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="08bd3-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="08bd3-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="08bd3-140">Ajouter `Microsoft.Extensions.SecretManager.Tools` à la *.csproj* et exécutez `dotnet restore`.</span><span class="sxs-lookup"><span data-stu-id="08bd3-140">Add `Microsoft.Extensions.SecretManager.Tools` to the *.csproj* file and run `dotnet restore`.</span></span> <span data-ttu-id="08bd3-141">Vous pouvez utiliser les mêmes étapes pour installer le Gestionnaire de la clé secrète à l’aide de la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="08bd3-141">You can use the same steps to install the Secret Manager Tool using for the command line.</span></span>

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-before.csproj?highlight=10)]

<span data-ttu-id="08bd3-142">Tester l’outil Gestionnaire de secret principal en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="08bd3-142">Test the Secret Manager tool by running the following command:</span></span>

```console
dotnet user-secrets -h
```

<span data-ttu-id="08bd3-143">L’outil Gestionnaire de Secret affiche l’utilisation, options et l’aide de la commande.</span><span class="sxs-lookup"><span data-stu-id="08bd3-143">The Secret Manager tool will display usage, options and command help.</span></span>

> [!NOTE]
> <span data-ttu-id="08bd3-144">Vous devez être dans le même répertoire que le *.csproj* fichier pour exécuter les outils définis dans le *.csproj* du fichier `DotNetCliToolReference` nœuds.</span><span class="sxs-lookup"><span data-stu-id="08bd3-144">You must be in the same directory as the *.csproj* file to run tools defined in the *.csproj* file's `DotNetCliToolReference` nodes.</span></span>

<span data-ttu-id="08bd3-145">L’outil Gestionnaire de Secret opère sur les paramètres de configuration de projet spécifiques qui sont stockées dans votre profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="08bd3-145">The Secret Manager tool operates on project-specific configuration settings that are stored in your user profile.</span></span> <span data-ttu-id="08bd3-146">Pour utiliser les secrets de l’utilisateur, le projet doit spécifier un `UserSecretsId` valeur dans sa *.csproj* fichier.</span><span class="sxs-lookup"><span data-stu-id="08bd3-146">To use user secrets, the project must specify a `UserSecretsId` value in its *.csproj* file.</span></span> <span data-ttu-id="08bd3-147">La valeur de `UserSecretsId` est arbitraire, mais est généralement unique au projet.</span><span class="sxs-lookup"><span data-stu-id="08bd3-147">The value of `UserSecretsId` is arbitrary, but is generally unique to the project.</span></span> <span data-ttu-id="08bd3-148">Les développeurs génèrent généralement un GUID pour le `UserSecretsId`.</span><span class="sxs-lookup"><span data-stu-id="08bd3-148">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

<span data-ttu-id="08bd3-149">Ajouter un `UserSecretsId` pour votre projet dans le *.csproj* fichier :</span><span class="sxs-lookup"><span data-stu-id="08bd3-149">Add a `UserSecretsId` for your project in the *.csproj* file:</span></span>

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-after.csproj?highlight=4)]

<span data-ttu-id="08bd3-150">Utilisez l’outil Gestionnaire de Secret pour définir une clé secrète.</span><span class="sxs-lookup"><span data-stu-id="08bd3-150">Use the Secret Manager tool to set a secret.</span></span> <span data-ttu-id="08bd3-151">Par exemple, dans une fenêtre de commande à partir du répertoire de projet, entrez les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="08bd3-151">For example, in a command window from the project directory, enter the following:</span></span>

```console
dotnet user-secrets set MySecret ValueOfMySecret
```

<span data-ttu-id="08bd3-152">Vous pouvez exécuter l’outil Gestionnaire de la clé secrète à partir d’autres annuaires, mais vous devez utiliser le `--project` option à passer dans le chemin d’accès à la *.csproj* fichier :</span><span class="sxs-lookup"><span data-stu-id="08bd3-152">You can run the Secret Manager tool from other directories, but you must use the `--project` option to pass in the path to the *.csproj* file:</span></span>
 
```console
dotnet user-secrets set MySecret ValueOfMySecret --project c:\work\WebApp1\src\webapp1
```

<span data-ttu-id="08bd3-153">Vous pouvez également utiliser l’outil Gestionnaire de Secret répertorier, supprimer et effacer les secrets de l’application.</span><span class="sxs-lookup"><span data-stu-id="08bd3-153">You can also use the Secret Manager tool to list, remove and clear app secrets.</span></span>

-----

## <a name="accessing-user-secrets-via-configuration"></a><span data-ttu-id="08bd3-154">L’accès à des secrets de l’utilisateur via la configuration</span><span class="sxs-lookup"><span data-stu-id="08bd3-154">Accessing user secrets via configuration</span></span>

<span data-ttu-id="08bd3-155">Vous accédez Secret Manager secrets via le système de configuration.</span><span class="sxs-lookup"><span data-stu-id="08bd3-155">You access Secret Manager secrets through the configuration system.</span></span> <span data-ttu-id="08bd3-156">Ajouter le `Microsoft.Extensions.Configuration.UserSecrets` du package et exécuter `dotnet restore`.</span><span class="sxs-lookup"><span data-stu-id="08bd3-156">Add the `Microsoft.Extensions.Configuration.UserSecrets` package and run `dotnet restore`.</span></span>

<span data-ttu-id="08bd3-157">Ajouter la source de configuration utilisateur secrets pour la `Startup` méthode :</span><span class="sxs-lookup"><span data-stu-id="08bd3-157">Add the user secrets configuration source to the `Startup` method:</span></span>

[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=16-19)]

<span data-ttu-id="08bd3-158">Vous pouvez accéder à des secrets de l’utilisateur via l’API de configuration :</span><span class="sxs-lookup"><span data-stu-id="08bd3-158">You can access user secrets via the configuration API:</span></span>

[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=26-29)]

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="08bd3-159">Fonctionne de l’outil Gestionnaire de la clé secrète</span><span class="sxs-lookup"><span data-stu-id="08bd3-159">How the Secret Manager tool works</span></span>

<span data-ttu-id="08bd3-160">L’outil Gestionnaire de Secret élimine les détails d’implémentation, tels qu’où et comment les valeurs sont stockées.</span><span class="sxs-lookup"><span data-stu-id="08bd3-160">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="08bd3-161">Vous pouvez utiliser l’outil sans connaître ces détails d’implémentation.</span><span class="sxs-lookup"><span data-stu-id="08bd3-161">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="08bd3-162">Dans la version actuelle, les valeurs sont stockées dans un [JSON](http://json.org/) fichier de configuration dans le répertoire de profil utilisateur :</span><span class="sxs-lookup"><span data-stu-id="08bd3-162">In the current version, the values are stored in a [JSON](http://json.org/) configuration file in the user profile directory:</span></span>

* <span data-ttu-id="08bd3-163">Windows :`%APPDATA%\microsoft\UserSecrets\<userSecretsId>\secrets.json`</span><span class="sxs-lookup"><span data-stu-id="08bd3-163">Windows: `%APPDATA%\microsoft\UserSecrets\<userSecretsId>\secrets.json`</span></span>

* <span data-ttu-id="08bd3-164">Linux :`~/.microsoft/usersecrets/<userSecretsId>/secrets.json`</span><span class="sxs-lookup"><span data-stu-id="08bd3-164">Linux: `~/.microsoft/usersecrets/<userSecretsId>/secrets.json`</span></span>

* <span data-ttu-id="08bd3-165">Mac :`~/.microsoft/usersecrets/<userSecretsId>/secrets.json`</span><span class="sxs-lookup"><span data-stu-id="08bd3-165">Mac: `~/.microsoft/usersecrets/<userSecretsId>/secrets.json`</span></span>

<span data-ttu-id="08bd3-166">La valeur de `userSecretsId` provient de la valeur spécifiée dans *.csproj* fichier.</span><span class="sxs-lookup"><span data-stu-id="08bd3-166">The value of `userSecretsId` comes from the value specified in *.csproj* file.</span></span>

<span data-ttu-id="08bd3-167">Vous ne devez pas écrire le code qui dépend de l’emplacement ou le format des données enregistrées avec l’outil Gestionnaire de la clé secrète, car ces détails d’implémentation peuvent changer.</span><span class="sxs-lookup"><span data-stu-id="08bd3-167">You should not write code that depends on the location or format of the data saved with the Secret Manager tool, as these implementation details might change.</span></span> <span data-ttu-id="08bd3-168">Par exemple, les valeurs de clé secrètes sont actuellement *pas* chiffré aujourd'hui, mais peut être un jour.</span><span class="sxs-lookup"><span data-stu-id="08bd3-168">For example, the secret values are currently *not* encrypted today, but could be someday.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="08bd3-169">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="08bd3-169">Additional Resources</span></span>

* [<span data-ttu-id="08bd3-170">Configuration</span><span class="sxs-lookup"><span data-stu-id="08bd3-170">Configuration</span></span>](../fundamentals/configuration.md)
