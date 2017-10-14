---
title: Remplacement de '< machineKey >' dans ASP.NET
author: rick-anderson
description: Remplacement de '< machineKey >' dans ASP.NET
keywords: "ASP.NET Core, sécurité, < machineKey >, machineKey"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 5ac13589-3837-4b4d-8abe-81f843942120
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 2c2f6e04c42d547592cc1c2e6e66da7133b8bbef
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="replacing-machinekey-in-aspnet"></a>En remplaçant `<machineKey>` dans ASP.NET

<a name="compatibility-replacing-machinekey"></a>

L’implémentation de la `<machineKey>` élément dans ASP.NET [remplaçable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/). Ainsi, la plupart des appels aux routines de chiffrement ASP.NET puissent être acheminés via un mécanisme de protection des données de remplacement, y compris le nouveau système de protection des données.

## <a name="package-installation"></a>Installation du package

> [!NOTE]
> Le nouveau système de protection de données peut uniquement être installé dans une application ASP.NET existante ciblant .NET 4.5.1 ou version ultérieure. Installation échoueront si l’application cible .NET 4.5 ou inférieur.

Pour installer le nouveau système de protection des données dans un projet de 4.5.1+ ASP.NET existant, installez le package Microsoft.AspNetCore.DataProtection.SystemWeb. Cela instancie le système de protection de données à l’aide du [configuration par défaut](../configuration/default-settings.md#data-protection-default-settings) paramètres.

Lorsque vous installez le package, il insère une ligne dans *Web.config* qui indique à ASP.NET d’utiliser pour [plus les opérations de chiffrement](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), y compris les appels à l’authentification par formulaire et l’état d’affichage MachineKey.Protect. La ligne est insérée lit comme suit.

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> Vous pouvez déterminer si le nouveau système de protection de données est actif en examinant les champs tels que `__VIEWSTATE`, qui doit commencer par « CfDJ8 » comme dans l’exemple ci-dessous. « CfDJ8 » est une représentation en base64 de l’en-tête magique « 09 F0 C9 F0 » qui identifie un contenu protégé par le système de protection des données.

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk..." />
```

## <a name="package-configuration"></a>Configuration de package

Le système de protection des données est instancié avec une configuration de zéro-le programme d’installation par défaut. Toutefois, étant donné que par défaut, les clés sont conservées au système de fichiers local, cela ne fonctionne pas pour les applications qui sont déployées dans une batterie de serveurs. Pour résoudre ce problème, vous pourrez fournir la configuration en créant un type qui sous-classe DataProtectionStartup et substitue sa méthode ConfigureServices.

Voici un exemple d’un type de démarrage de protection des données personnalisées qui configuré à la fois où les clés sont conservés et comment elles sont chiffrées au repos. Il remplace également la stratégie d’isolation d’application par défaut en fournissant son propre nom de l’application.

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
> Vous pouvez également utiliser `<machineKey applicationName="my-app" ... />` à la place d’un appel explicite à SetApplicationName. Il s’agit d’un mécanisme pratique de ne pas forcer le développeur pour créer un type dérivé de DataProtectionStartup si toutes les qu’ils veulent configurer a été la définition du nom de l’application.

Pour activer cette configuration personnalisée, revenez au fichier Web.config et recherchez la `<appSettings>` élément que le package d’installation ajouté au fichier de configuration. Il doit ressembler à la balise suivante :

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

Renseignez la valeur vide avec le nom qualifié d’assembly du type dérivé DataProtectionStartup que vous venez de créer. Si le nom de l’application est DataProtectionDemo, cela ressemble à le ci-dessous.

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

Le système de protection qui vient d’être configuré de données est maintenant prêt à être utilisé à l’intérieur de l’application.
