---
title: Remplacement de '< machineKey >' dans ASP.NET
author: rick-anderson
description: Remplacement de '< machineKey >' dans ASP.NET
keywords: "ASP.NET Core, sécurité, '< machineKey >'"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 5ac13589-3837-4b4d-8abe-81f843942120
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: b7f260bd5d548588a51095537c9c1b1802553c54
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="replacing-machinekey-in-aspnet"></a><span data-ttu-id="596b8-104">En remplaçant `<machineKey>` dans ASP.NET</span><span class="sxs-lookup"><span data-stu-id="596b8-104">Replacing `<machineKey>` in ASP.NET</span></span>

<a name=compatibility-replacing-machinekey></a>

<span data-ttu-id="596b8-105">L’implémentation de la `<machineKey>` élément dans ASP.NET [remplaçable](http://blogs.msdn.com/b/webdev/archive/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2.aspx).</span><span class="sxs-lookup"><span data-stu-id="596b8-105">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](http://blogs.msdn.com/b/webdev/archive/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2.aspx).</span></span> <span data-ttu-id="596b8-106">Ainsi, la plupart des appels aux routines de chiffrement ASP.NET puissent être acheminés via un mécanisme de protection des données de remplacement, y compris le nouveau système de protection des données.</span><span class="sxs-lookup"><span data-stu-id="596b8-106">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="596b8-107">Installation du package</span><span class="sxs-lookup"><span data-stu-id="596b8-107">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="596b8-108">Le nouveau système de protection de données peut uniquement être installé dans une application ASP.NET existante ciblant .NET 4.5.1 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="596b8-108">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or higher.</span></span> <span data-ttu-id="596b8-109">Installation échoueront si l’application cible .NET 4.5 ou inférieur.</span><span class="sxs-lookup"><span data-stu-id="596b8-109">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="596b8-110">Pour installer le nouveau système de protection des données dans un projet de 4.5.1+ ASP.NET existant, installez le package Microsoft.AspNetCore.DataProtection.SystemWeb.</span><span class="sxs-lookup"><span data-stu-id="596b8-110">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="596b8-111">Cela instancie le système de protection de données à l’aide du [configuration par défaut](../configuration/default-settings.md#data-protection-default-settings) paramètres.</span><span class="sxs-lookup"><span data-stu-id="596b8-111">This will instantiate the data protection system using the [default configuration](../configuration/default-settings.md#data-protection-default-settings) settings.</span></span>

<span data-ttu-id="596b8-112">Lorsque vous installez le package, il insère une ligne dans *Web.config* qui indique à ASP.NET d’utiliser pour [plus les opérations de chiffrement](http://blogs.msdn.com/b/webdev/archive/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2.aspx), y compris les appels à l’authentification par formulaire et l’état d’affichage MachineKey.Protect.</span><span class="sxs-lookup"><span data-stu-id="596b8-112">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](http://blogs.msdn.com/b/webdev/archive/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2.aspx), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="596b8-113">La ligne est insérée lit comme suit.</span><span class="sxs-lookup"><span data-stu-id="596b8-113">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="596b8-114">Vous pouvez déterminer si le nouveau système de protection de données est actif en examinant les champs tels que `__VIEWSTATE`, qui doit commencer par « CfDJ8 » comme dans l’exemple ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="596b8-114">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="596b8-115">« CfDJ8 » est une représentation en base64 de l’en-tête magique « 09 F0 C9 F0 » qui identifie un contenu protégé par le système de protection des données.</span><span class="sxs-lookup"><span data-stu-id="596b8-115">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk..." />
```

## <a name="package-configuration"></a><span data-ttu-id="596b8-116">Configuration de package</span><span class="sxs-lookup"><span data-stu-id="596b8-116">Package configuration</span></span>

<span data-ttu-id="596b8-117">Le système de protection des données est instancié avec une configuration de zéro-le programme d’installation par défaut.</span><span class="sxs-lookup"><span data-stu-id="596b8-117">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="596b8-118">Toutefois, étant donné que par défaut, les clés sont conservées au système de fichiers local, cela ne fonctionne pas pour les applications qui sont déployées dans une batterie de serveurs.</span><span class="sxs-lookup"><span data-stu-id="596b8-118">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="596b8-119">Pour résoudre ce problème, vous pourrez fournir la configuration en créant un type qui sous-classe DataProtectionStartup et substitue sa méthode ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="596b8-119">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="596b8-120">Voici un exemple d’un type de démarrage de protection des données personnalisées qui configuré à la fois où les clés sont conservés et comment elles sont chiffrées au repos.</span><span class="sxs-lookup"><span data-stu-id="596b8-120">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="596b8-121">Il remplace également la stratégie d’isolation d’application par défaut en fournissant son propre nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="596b8-121">It also overrides the default app isolation policy by providing its own application name.</span></span>

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> <span data-ttu-id="596b8-122">Vous pouvez également utiliser `<machineKey applicationName="my-app" ... />` à la place d’un appel explicite à SetApplicationName.</span><span class="sxs-lookup"><span data-stu-id="596b8-122">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="596b8-123">Il s’agit d’un mécanisme pratique de ne pas forcer le développeur pour créer un type dérivé de DataProtectionStartup si toutes les qu’ils veulent configurer a été la définition du nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="596b8-123">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="596b8-124">Pour activer cette configuration personnalisée, revenez au fichier Web.config et recherchez la `<appSettings>` élément que le package d’installation ajouté au fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="596b8-124">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="596b8-125">Il doit ressembler à la balise suivante :</span><span class="sxs-lookup"><span data-stu-id="596b8-125">It will look like the following markup:</span></span>

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

<span data-ttu-id="596b8-126">Renseignez la valeur vide avec le nom qualifié d’assembly du type dérivé DataProtectionStartup que vous venez de créer.</span><span class="sxs-lookup"><span data-stu-id="596b8-126">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="596b8-127">Si le nom de l’application est DataProtectionDemo, cela ressemble à le ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="596b8-127">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="596b8-128">Le système de protection qui vient d’être configuré de données est maintenant prêt à être utilisé à l’intérieur de l’application.</span><span class="sxs-lookup"><span data-stu-id="596b8-128">The newly-configured data protection system is now ready for use inside the application.</span></span>
