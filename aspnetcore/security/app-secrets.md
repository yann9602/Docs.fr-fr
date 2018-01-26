---
title: "Stockage sécurisé des secrets d’application pendant le développement dans ASP.NET Core"
author: rick-anderson
description: "Montre comment stocker des clés secrètes en toute sécurité pendant le développement"
ms.author: riande
manager: wpickett
ms.date: 09/15/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/app-secrets
ms.openlocfilehash: 94356cef7a0333f0faac6420b1b5425920b99deb
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="safe-storage-of-app-secrets-during-development-in-aspnet-core"></a>Stockage sécurisé des secrets d’application pendant le développement dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Michel Roth](https://github.com/danroth27), et [Scott Addie](https://scottaddie.com) 

Ce document montre comment vous pouvez utiliser l’outil Gestionnaire de la clé secrète dans le développement de conserver les clés secrètes en dehors de votre code. Le point le plus important est de vous ne devez jamais stocker les mots de passe ou d’autres données sensibles dans le code source, et vous ne devez pas utiliser les clés secrètes de production en mode de développement et de test. Vous pouvez utiliser à la place la [configuration](xref:fundamentals/configuration/index) système à lire ces valeurs à partir de variables d’environnement ou à partir de valeurs stockées à l’aide du Gestionnaire de Secret outil. L’outil Gestionnaire de Secret empêche les données sensibles en cours de vérification dans le contrôle de code source. Le [configuration](xref:fundamentals/configuration/index) système peut lire les clés secrètes stockées avec l’outil Gestionnaire de la clé secrète décrit dans cet article.

L’outil Gestionnaire de la clé secrète est utilisée uniquement dans le développement. Vous pouvez protéger des secrets de test et de production Azure avec la [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) fournisseur de configuration. Consultez [fournisseur de configuration d’Azure Key Vault](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration) pour plus d’informations.

## <a name="environment-variables"></a>Variables d’environnement

Pour éviter de stocker des secrets de l’application dans le code ou dans les fichiers de configuration local, vous stockez des secrets dans des variables d’environnement. Vous pouvez configurer le [configuration](xref:fundamentals/configuration/index) framework pour lire les valeurs de variables d’environnement en appelant `AddEnvironmentVariables`. Vous pouvez ensuite utiliser des variables d’environnement pour remplacer des valeurs de configuration pour toutes les sources de configuration spécifié précédemment.

Par exemple, si vous créez une application web ASP.NET Core avec les comptes d’utilisateur individuels, il ajoutera une chaîne de connexion par défaut pour le *appsettings.json* fichier dans le projet avec la clé `DefaultConnection`. La chaîne de connexion par défaut est configuré pour utiliser la base de données locale, qui s’exécute en mode utilisateur et ne nécessite pas un mot de passe. Lorsque vous déployez votre application sur un serveur de test ou de production, vous pouvez remplacer le `DefaultConnection` valeur de la clé avec un paramètre de variable d’environnement qui contient la chaîne de connexion (avec éventuellement des informations d’identification sensibles) pour une base de données de test ou de production serveur.

>[!WARNING]
> Variables d’environnement sont généralement stockés en texte brut et ne sont pas chiffrés. Si l’ordinateur ou le processus est compromis, puis les variables d’environnement accessibles par des tiers non approuvés. Des mesures supplémentaires pour empêcher la divulgation de secrets de l’utilisateur peuvent toujours être nécessaire.

## <a name="secret-manager"></a>Gestionnaire de clé secrète

L’outil Gestionnaire de secret principal stocke des données sensibles pour les travaux de développement en dehors de l’arborescence de votre projet. L’outil Gestionnaire de la clé secrète est un outil de projet qui peut être utilisé pour stocker des secrets pour un [.NET Core](https://www.microsoft.com/net/core) projet pendant le développement. Avec l’outil Gestionnaire de la clé secrète, vous pouvez associer des secrets de l’application à un projet spécifique et les partager entre plusieurs projets.

>[!WARNING]
> Le Gestionnaire du Secret ne chiffrer les clés secrètes stockées et ne doit pas être traité comme un magasin approuvé. Il est uniquement à des fins de développement. Les clés et valeurs sont stockées dans un fichier de configuration JSON dans le répertoire de profil utilisateur.

## <a name="installing-the-secret-manager-tool"></a>Installation de l’outil Gestionnaire de la clé secrète

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Cliquez sur le projet dans l’Explorateur de solutions, puis sélectionnez **modifier \<project_name\>.csproj** dans le menu contextuel. Ajoutez la ligne en surbrillance à le *.csproj* de fichiers et enregistrer pour restaurer le package NuGet associé :

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-before.csproj?highlight=10)]

Cliquez à nouveau sur le projet dans l’Explorateur de solutions, puis sélectionnez **gérer les Secrets utilisateur** dans le menu contextuel. Cette opération ajoute un nouveau `UserSecretsId` nœud au sein d’un `PropertyGroup` de la *.csproj* fichier, comme illustré dans l’exemple suivant :

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-after.csproj?highlight=4)]

L’enregistrement modifié *.csproj* fichier également s’ouvre un `secrets.json` fichier dans l’éditeur de texte. Remplacez le contenu de la `secrets.json` fichier avec le code suivant :

```json
{
    "MySecret": "ValueOfMySecret"
}
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ajouter `Microsoft.Extensions.SecretManager.Tools` à la *.csproj* et exécutez `dotnet restore`. Vous pouvez utiliser les mêmes étapes pour installer le Gestionnaire de la clé secrète à l’aide de la ligne de commande.

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-before.csproj?highlight=10)]

Tester l’outil Gestionnaire de secret principal en exécutant la commande suivante :

```console
dotnet user-secrets -h
```

L’outil Gestionnaire de Secret affiche l’utilisation, options et l’aide de la commande.

> [!NOTE]
> Vous devez être dans le même répertoire que le *.csproj* fichier pour exécuter les outils définis dans le *.csproj* du fichier `DotNetCliToolReference` nœuds.

L’outil Gestionnaire de Secret opère sur les paramètres de configuration de projet spécifiques qui sont stockées dans votre profil utilisateur. Pour utiliser les secrets de l’utilisateur, le projet doit spécifier un `UserSecretsId` valeur dans sa *.csproj* fichier. La valeur de `UserSecretsId` est arbitraire, mais est généralement unique au projet. Les développeurs génèrent généralement un GUID pour le `UserSecretsId`.

Ajouter un `UserSecretsId` pour votre projet dans le *.csproj* fichier :

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-after.csproj?highlight=4)]

Utilisez l’outil Gestionnaire de Secret pour définir une clé secrète. Par exemple, dans une fenêtre de commande à partir du répertoire de projet, entrez les éléments suivants :

```console
dotnet user-secrets set MySecret ValueOfMySecret
```

Vous pouvez exécuter l’outil Gestionnaire de la clé secrète à partir d’autres annuaires, mais vous devez utiliser le `--project` option à passer dans le chemin d’accès à la *.csproj* fichier :
 
```console
dotnet user-secrets set MySecret ValueOfMySecret --project c:\work\WebApp1\src\webapp1
```

Vous pouvez également utiliser l’outil Gestionnaire de Secret répertorier, supprimer et effacer les secrets de l’application.

-----

## <a name="accessing-user-secrets-via-configuration"></a>L’accès à des secrets de l’utilisateur via la configuration

Vous accédez Secret Manager secrets via le système de configuration. Ajouter le `Microsoft.Extensions.Configuration.UserSecrets` du package et exécuter `dotnet restore`.

Ajouter la source de configuration utilisateur secrets pour la `Startup` méthode :

[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=16-19)]

Vous pouvez accéder à des secrets de l’utilisateur via l’API de configuration :

[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=26-29)]

## <a name="how-the-secret-manager-tool-works"></a>Fonctionne de l’outil Gestionnaire de la clé secrète

L’outil Gestionnaire de Secret élimine les détails d’implémentation, tels qu’où et comment les valeurs sont stockées. Vous pouvez utiliser l’outil sans connaître ces détails d’implémentation. Dans la version actuelle, les valeurs sont stockées dans un [JSON](http://json.org/) fichier de configuration dans le répertoire de profil utilisateur :

* Windows :`%APPDATA%\microsoft\UserSecrets\<userSecretsId>\secrets.json`

* Linux :`~/.microsoft/usersecrets/<userSecretsId>/secrets.json`

* Mac :`~/.microsoft/usersecrets/<userSecretsId>/secrets.json`

La valeur de `userSecretsId` provient de la valeur spécifiée dans *.csproj* fichier.

Vous ne devez pas écrire du code qui dépend de l’emplacement ou le format des données enregistrées avec l’outil Gestionnaire de la clé secrète, car ces détails d’implémentation peuvent changer. Par exemple, les valeurs de clé secrètes sont actuellement *pas* chiffré aujourd'hui, mais peut être un jour.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Configuration](xref:fundamentals/configuration/index)
