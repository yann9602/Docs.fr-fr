---
title: "Fournisseur de configuration du coffre de clés Azure"
author: guardrex
description: "Découvrez comment utiliser le fournisseur de Configuration du coffre de clé Azure pour configurer une application à l’aide de paires nom-valeur chargées pendant l’exécution."
keywords: ASP.NET Core, configuration, Azure Key Vault
ms.author: riande
manager: wpickett
ms.date: 08/09/2017
ms.topic: article
ms.assetid: 0292bdae-b3ed-4637-bd67-19b9bb8b65cb
ms.prod: asp.net-core
uid: security/key-vault-configuration
ms.openlocfilehash: c5d8506c1bc8e6364d01596a0c82e1da41eea4ca
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2017
---
# <a name="azure-key-vault-configuration-provider"></a>Fournisseur de configuration du coffre de clés Azure

Par [Luke Latham](https://github.com/guardrex) et [Andrew Stanton-infirmière](https://github.com/anurse)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Afficher ou télécharger l’exemple de code pour 2.x :

* [Exemple de base](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/samples/basic-sample/2.x) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample))-lit les valeurs des secrets dans une application.
* [Exemple de préfixe de nom de la clé](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/samples/key-name-prefix-sample/2.x) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample)) - lit les valeurs des secrets à l’aide d’un préfixe de nom de la clé qui représente la version d’une application, ce qui vous permet de charger un ensemble différent de valeurs de clé secrètes pour chaque version de l’application.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Afficher ou télécharger l’exemple de code pour 1.x :

* [Exemple de base](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/samples/basic-sample/1.x) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample))-lit les valeurs des secrets dans une application.
* [Exemple de préfixe de nom de la clé](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/samples/key-name-prefix-sample/1.x) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample)) - lit les valeurs des secrets à l’aide d’un préfixe de nom de la clé qui représente la version d’une application, ce qui vous permet de charger un ensemble différent de valeurs de clé secrètes pour chaque version de l’application. 

---

Ce document explique comment utiliser le [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) fournisseur de configuration à charger des valeurs de configuration d’application de secrets de coffre de clés Azure. Coffre de clés Azure est un service cloud qui vous aident à protéger les clés de chiffrement et les secrets utilisés par les applications et services. Scénarios courants incluent le contrôle d’accès aux données de configuration sensibles et répondre aux exigences de la norme FIPS 140-2 niveau 2 validé les Modules de sécurité matériel (HSM) lors du stockage des données de configuration. Cette fonctionnalité est disponible pour les applications qui ciblent ASP.NET Core 1.1 ou ultérieure.

## <a name="package"></a>Package
Pour utiliser le fournisseur, ajouter une référence à la [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.

## <a name="application-configuration"></a>Configuration d’application
Vous pouvez explorer le fournisseur avec le [exemples d’applications](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/samples). Une fois que vous établissez un coffre de clés et créez des clés secrètes dans le coffre, les exemples d’applications en toute sécurité par chargement les valeurs de secret principal dans leurs configurations et les affichent dans les pages Web.

Le fournisseur est ajouté à la `ConfigurationBuilder` avec le `AddAzureKeyVault` extension. Dans les exemples d’applications, l’extension utilise trois valeurs de configuration chargés à partir de la *appsettings.json* fichier.

| Paramètre d’application    | Description                    | Exemple                                      |
| -------------- | ------------------------------ | -------------------------------------------- |
| `Vault`        | Nom de coffre de clés Azure           | contosovault                                 |
| `ClientId`     | Id d’application Azure Active Directory  | 627e911e-43CC-61d4-992e-12db9c81b413         |
| `ClientSecret` | Clé de l’application Azure Active Directory | g58K3dtg59o1Pa + e59v2Tx829w6VxTB2yv9sv/101di = |

[!code-csharp[Program](key-vault-configuration/samples/basic-sample/2.x/Program.cs?name=snippet1&highlight=2,7-10)]

## <a name="creating-key-vault-secrets-and-loading-configuration-values-basic-sample"></a>Création des clés secrètes de coffre de clés et du chargement des valeurs de configuration (exemple de base)
1. Créer un coffre de clés et configurer Azure Active Directory (Azure AD) pour l’application en suivant les instructions de [prise en main d’Azure Key Vault](https://azure.microsoft.com/documentation/articles/key-vault-get-started/).
  * Ajouter les clés secrètes au coffre de clés à l’aide la [PowerShell Module d’Azure Resource Manager clé de coffre](/powershell/module/azurerm.keyvault) disponible à partir de la [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureRM.KeyVault), le [API REST de coffre de clés Azure](/rest/api/keyvault/), ou le [Portail azure](https://portal.azure.com/). Les secrets sont créés en tant que *manuel* ou *certificat* secrets. *Certificat* secrets sont des certificats pour les applications et services, mais ne sont pas pris en charge par le fournisseur de configuration. Vous devez utiliser le *manuel* option permettant de créer des clés secrètes de paire nom-valeur pour une utilisation avec le fournisseur de configuration.
    * Les secrets simples sont créées en tant que paires nom-valeur. Les noms de secret de coffre de clés Azure sont limités à des caractères alphanumériques et des tirets.
    * Utilisent des valeurs hiérarchiques (sections de configuration) `--` (deux tirets) comme séparateur dans l’exemple. Signes deux-points, qui sont normalement utilisés pour délimiter une section à partir d’une sous-clé dans [configuration d’ASP.NET Core](xref:fundamentals/configuration), ne sont pas autorisés dans les noms de secret principal. Par conséquent, les deux tirets sont utilisés et échangés avec un signe deux-points, lorsque les clés secrètes sont chargés dans la configuration de l’application.
    * Créez deux *manuel* secrets avec les paires nom-valeur suivantes. La question secrète du premier est une valeur et le nom simple, et la question secrète du deuxième crée une valeur de secret principal avec une section et le sous-clé dans le nom de clé secrète :
      * `SecretName`: `secret_value_1`
      * `Section--SecretName`: `secret_value_2`
  * Inscrire l’exemple d’application avec Azure Active Directory.
  * Autoriser l’application à accéder au coffre de clés. Lorsque vous utilisez la `Set-AzureRmKeyVaultAccessPolicy` fournissent de l’applet de commande PowerShell pour autoriser l’application à accéder au coffre de clés, `List` et `Get` accès aux clés secrètes avec `-PermissionsToKeys list,get`.
2. Mettre à jour de l’application *appsettings.json* fichier avec les valeurs de `Vault`, `ClientId`, et `ClientSecret`.
3. Exécutez l’exemple d’application, qui obtient ses valeurs de configuration à partir de `IConfigurationRoot` avec le même nom que le nom secret.
  * Valeurs non hiérarchique : la valeur de `SecretName` est obtenu avec `config["SecretName"]`.
  * Valeurs hiérarchiques (sections) : utilisez `:` la notation (deux-points) ou le `GetSection` méthode d’extension. Pour obtenir la valeur de configuration, utilisez une des ces approches :
    * `config["Section:SecretName"]`
    * `config.GetSection("Section")["SecretName"]`

Lorsque vous exécutez l’application, une page Web affiche les valeurs de secret principal chargés :

![Fenêtre de navigateur affichant les valeurs des secrets chargée via le fournisseur de Configuration Azure Key Vault](key-vault-configuration/_static/sample1.png)

## <a name="creating-prefixed-key-vault-secrets-and-loading-configuration-values-key-name-prefix-sample"></a>Création des clés secrètes de coffre de clés avec préfixe et du chargement des valeurs de configuration (touche-nom-préfixe-exemple)
`AddAzureKeyVault`fournit également une surcharge qui accepte une implémentation de `IKeyVaultSecretManager`, qui vous permet de contrôler comment les secrets de coffre sont convertis en clés de configuration. Par exemple, vous pouvez implémenter l’interface pour charger les valeurs des secrets selon une valeur de préfixe que vous fournissez au démarrage de l’application. Cela vous permet, par exemple, pour charger des secrets selon la version de l’application.

> [!WARNING]
> N’utilisez pas de préfixes sur les secrets de coffre de clés pour placer des secrets pour plusieurs applications dans le coffre de clés même ou à l’environnement de secrets (par exemple, *développement* verus *production* secrets) dans le même coffre. Nous conseillons différentes applications et environnements de développement/production des coffres de clés distinctes pour isoler les environnements d’application pour le niveau le plus élevé de sécurité.

À l’aide de la deuxième exemple d’application, vous créez une clé secrète dans le coffre de clés pour `5000-AppSecret` (périodes ne sont pas autorisés dans les noms de coffre de clés secrètes) représentant un secret d’application pour la version 5.0.0.0 de votre application. Pour une autre version, 5.1.0.0, vous créez une clé secrète pour `5100-AppSecret`. Chaque version de l’application charge sa propre valeur secrète dans sa configuration en tant que `AppSecret`, extraction, la version tandis qu’il charge la clé secrète. Implémentation de l’exemple est illustrée ci-dessous :

[!code-csharp[Configuration builder](key-vault-configuration/samples/key-name-prefix-sample/2.x/Program.cs?name=snippet1&highlight=12)]

[!code-csharp[PrefixKeyVaultSecretManager](key-vault-configuration/samples/key-name-prefix-sample/2.x/Startup.cs?name=snippet1)]

Le `Load` méthode est appelée par un fournisseur d’algorithme qui effectue une itération dans les clés secrètes de coffre pour trouver celles qui ont le préfixe de la version. Quand un préfixe de version a été trouvé avec `Load`, l’algorithme utilise le `GetKey` méthode pour retourner le nom de la configuration du nom du secret principal. Il supprime le préfixe de la version du nom de la clé secrète et retourne le reste du nom du secret principal pour le chargement dans la configuration de l’application de paires nom-valeur.

Lorsque vous implémentez cette approche :

1. Les clés secrètes du coffre de clés sont chargés.
2. Le secret de chaîne pour `5000-AppSecret` est mis en correspondance.
3. La version, `5000` (avec le tiret), est supprimé sur le nom de clé en laissant `AppSecret` pour charger avec la valeur de secret principal dans la configuration de l’application.

> [!NOTE]
> Vous pouvez également fournir votre propre `KeyVaultClient` implémentation à `AddAzureKeyVault`. En fournissant un client personnalisé vous permet de partager une seule instance du client entre le fournisseur de configuration et d’autres parties de votre application.

1. Créer un coffre de clés et configurer Azure Active Directory (Azure AD) pour l’application en suivant les instructions de [prise en main d’Azure Key Vault](https://azure.microsoft.com/documentation/articles/key-vault-get-started/).
  * Ajouter les clés secrètes au coffre de clés à l’aide la [PowerShell Module d’Azure Resource Manager clé de coffre](/powershell/module/azurerm.keyvault) disponible à partir de la [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureRM.KeyVault), le [API REST de coffre de clés Azure](/rest/api/keyvault/), ou le [Portail azure](https://portal.azure.com/). Les secrets sont créés en tant que *manuel* ou *certificat* secrets. *Certificat* secrets sont des certificats pour les applications et services, mais ne sont pas pris en charge par le fournisseur de configuration. Vous devez utiliser le *manuel* option permettant de créer des clés secrètes de paire nom-valeur pour une utilisation avec le fournisseur de configuration.
    * Utilisent des valeurs hiérarchiques (sections de configuration) `--` (deux tirets) comme séparateur.
    * Créez deux *manuel* secrets avec les paires nom-valeur suivantes :
      * `5000-AppSecret`: `5.0.0.0_secret_value`
      * `5100-AppSecret`: `5.1.0.0_secret_value`
  * Inscrire l’exemple d’application avec Azure Active Directory.
  * Autoriser l’application à accéder au coffre de clés. Lorsque vous utilisez la `Set-AzureRmKeyVaultAccessPolicy` fournissent de l’applet de commande PowerShell pour autoriser l’application à accéder au coffre de clés, `List` et `Get` accès aux clés secrètes avec `-PermissionsToKeys list,get`.
2. Mettre à jour de l’application *appsettings.json* fichier avec les valeurs de `Vault`, `ClientId`, et `ClientSecret`.
3. Exécutez l’exemple d’application, qui obtient ses valeurs de configuration à partir de `IConfigurationRoot` avec le même nom que le nom secret avec préfixe. Dans cet exemple, le préfixe est la version de l’application que vous avez fournies pour les `PrefixKeyVaultSecretManager` quand vous avez ajouté le fournisseur de configuration du coffre de clés Azure. La valeur de `AppSecret` est obtenu avec `config["AppSecret"]`. La page Web générée par l’application affiche la valeur chargée :

   ![Fenêtre de navigateur indiquant une valeur secrète chargée via le fournisseur de Configuration Azure Key Vault lors de la version de l’application est 5.0.0.0](key-vault-configuration/_static/sample2-1.png)

4. Modifier la version de l’assembly de l’application dans le fichier de projet à partir de `5.0.0.0` à `5.1.0.0` et réexécutez l’application. Cette fois, la valeur secrète retournée est `5.1.0.0_secret_value`. La page Web générée par l’application affiche la valeur chargée :

   ![Fenêtre de navigateur indiquant une valeur secrète chargée via le fournisseur de Configuration Azure Key Vault lors de la version de l’application est 5.1.0.0](key-vault-configuration/_static/sample2-2.png)

## <a name="controlling-access-to-the-clientsecret"></a>Contrôle l’accès à la ClientSecret
Utilisez le [Secret gestionnaire](xref:security/app-secrets) pour maintenir le `ClientSecret` en dehors de votre arborescence source du projet. Avec le Gestionnaire de clé secrète, associer des secrets de l’application à un projet spécifique, les partager entre plusieurs projets.

Lorsque vous développez une application .NET Framework dans un environnement qui prend en charge les certificats, vous pouvez vous authentifier à Azure Key Vault avec un certificat X.509. Les clé privée du certificat X.509 sont gérée par le système d’exploitation. Pour plus d’informations, consultez [authentifier avec un certificat au lieu d’une clé secrète Client](https://docs.microsoft.com/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret). Utilisez le `AddAzureKeyVault` surcharge qui accepte un `X509Certificate2`.

```csharp
var store = new X509Store(StoreLocation.CurrentUser);
store.Open(OpenFlags.ReadOnly);
var cert = store.Certificates.Find(X509FindType.FindByThumbprint, config["CertificateThumbprint"], false);

builder.AddAzureKeyVault(
    config["Vault"],
    config["ClientId"],
    cert.OfType<X509Certificate2>().Single(),
    new EnvironmentSecretManager(env.ApplicationName));
store.Close();

Configuration = builder.Build();
```

## <a name="reloading-secrets"></a>Rechargement des clés secrètes
Les secrets sont mis en cache jusqu'à ce que `IConfigurationRoot.Reload()` est appelée. Expiré, désactivé, et les secrets mis à jour dans le coffre de clés ne sont pas respectées par l’application tant que `Reload` est exécutée.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Secrets désactivés et expirés
Secrets désactivés et expirées lever un `KeyVaultClientException`. Pour empêcher votre application de lever, remplacez votre application, ou mettre à jour le secret désactivé/expiré.

## <a name="troubleshooting"></a>Résolution des problèmes
Lorsque l’application ne parvient pas à charger la configuration de l’utilisation du fournisseur, un message d’erreur est écrit pour le [infrastructure de journalisation d’ASP.NET](xref:fundamentals/logging). Les conditions suivantes empêchent de se charger :
* L’application n’est pas configurée correctement dans Azure Active Directory.
* Le coffre de clés n’existe pas dans le coffre de clés Azure.
* L’application n’est pas autorisée à accéder au coffre de clés.
* N’inclut pas la stratégie d’accès `Get` et `List` autorisations.
* Dans le coffre de clés, les données de configuration (paire nom-valeur) sont correctement nommées, manquant, désactivé ou expiré.
* L’application a le nom de coffre de clés incorrect (`Vault`), Id d’application Azure AD (`ClientId`), ou la clé Azure AD (`ClientSecret`).
* La clé d’Azure AD (`ClientSecret`) a expiré.
* La clé de configuration (nom) est incorrecte dans l’application pour la valeur que vous essayez de charger.

## <a name="additional-resources"></a>Ressources supplémentaires
* <xref:fundamentals/configuration>
* [Microsoft Azure : Coffre de clés](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure : Documentation de coffre de clés](https://docs.microsoft.com/azure/key-vault/)
* [Comment générer et transférer protégée par HSM clés pour le coffre de clés Azure](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)
* [Classe de KeyVaultClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
