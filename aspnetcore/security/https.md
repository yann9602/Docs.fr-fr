---
title: "Configuration de HTTPS pour le développement dans ASP.NET Core"
author: Rick-Anderson
description: "Montre comment configurer HTTPS pour le développement dans ASP.NET 2.0 de base."
keywords: ASP.NET Core, SSL, HTTPS
ms.author: riande
manager: wpickett
ms.date: 05/10/2017
ms.topic: article
ms.assetid: 94f2f1a4-7d46-45e2-a085-a57916e41724
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/https
ms.openlocfilehash: 7c366ffbac71152c2f29901ff12bac2962e83e3e
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="setting-up-https-for-development-in-aspnet-core"></a>Configuration de HTTPS pour le développement dans ASP.NET Core

> [!NOTE] 
> Cette rubrique s’applique à ASP.NET Core 2.0 Preview 1

Vous pouvez configurer votre application pour utiliser HTTPS pendant le développement pour simuler HTTPS dans votre environnement de production. Activation de HTTPS peut-être être nécessaires pour activer l’intégration avec différents fournisseurs d’identité (telles que [Azure AD](https://azure.microsoft.com/services/active-directory) et [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)).

<a name="iisxpress"></a>

Sous Windows, si vous avez installé Visual Studio ou IIS Express, le certificat de développement IIS Express dans votre magasin de certificats LocalMachine. Vous pouvez mettre à jour les propriétés de votre projet dans Visual Studio pour utiliser ce certificat lors de l’exécution derrière IIS Express.

   * Dans l’Explorateur de solutions, cliquez sur le projet et sélectionnez **propriétés**.
   * Dans le volet gauche, sélectionnez **déboguer**.
   * Vérifiez **activer SSL**
   * Copiez l’URL SSL et collez-la dans la **URL de l’application**

![Onglet de propriétés de l’application web de débogage](enforcing-ssl/_static/ssl.png)

Pour le développement, vous pouvez utiliser le certificat de développement IIS Express s’il est disponible, ou créer un nouveau certificat à des fins de développement. Le certificat de développement doit être configuré dans le `appsettings.Development.json` afin qu’il n’est pas utilisé en production de fichiers :

```json
{
  "Certificates": {
    "HTTPS": {
      "Source": "Store",
      "StoreLocation": "LocalMachine",
      "StoreName": "My",
      "Subject": "CN=localhost",
      "AllowInvalid": true
    }
  }
}
```

Une application avec cette configuration en cours d’exécution en production lève une exception indiquant que « Aucun certificat nommé 'HTTPS' trouvés dans la configuration de l’environnement actuel (Production) ». Pour basculer le [environnement](xref:fundamentals/environments) à `Development`, définissez le `ASPNETCORE_ENVIRONMENT` variable d’environnement `Development`.

Si vous n’avez pas le certificat IIS Express développement installé, vous pouvez créer vous-même un certificat de développement. Sur Windows, vous pouvez créer un certificat de développement et ajoutez-la au magasin racine de confiance pour l’utilisateur actuel en exécutant les commandes PowerShell suivantes dans une invite de commandes avec élévation de privilèges :

```powershell
$cert = New-SelfSignedCertificate -Subject localhost -DnsName localhost -FriendlyName "ASP.NET Core Development" -KeyUsage DigitalSignature -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1") 
Export-Certificate -Cert $cert -FilePath cert.cer
Import-Certificate -FilePath cert.cer -CertStoreLocation cert:/CurrentUser/Root
```

<a name="OpenSSL"></a>

## <a name="kestrel-on--macos-and-linux"></a>Kestrel sur macOS et Linux

Vous pouvez configurer Kestrel pour écouter sur HTTPS en configurant un point de terminaison avec l’adresse, port et certificat IP souhaitée. Le certificat peut être configuré en ligne, ou dans le niveau supérieur `Certificates` section, puis référencé par son nom :

```json
{
  "Kestrel": {
    "Endpoints": {
      "LocalhostHttps": {
        "Address": "127.0.0.1",
        "Port": "43434",
        "Certificate": "HTTPS"
      }
    }
  }
}

```

Sur Mac OS et Linux, vous pouvez créer un certificat auto-signé pour l’utilisation de HTTPS [OpenSSL](https://www.openssl.org/):

```bash
openssl req -new -x509 -newkey rsa:2048 -keyout localhost.key -out localhost.cer -days 365 -subj /CN=localhost
openssl pkcs12 -export -out certificate.pfx -inkey localhost.key -in localhost.cer
```

Une fois la `certificate.pfx` fichier a été généré, configurez le certificat HTTPS dans votre `appsettings.Development.json` fichier :

```json
{
  "Certificates": {
    "HTTPS": {
      "Source": "File",
      "Path": "certificate.pfx"
    }
  }
}
```

Vous devez également spécifier le mot de passe pour le certificat en définissant la propriété de configuration « Certificats : HTTPS:Password ». Les mots de passe ne doivent pas être stockées en texte brut. Consultez [Safe stockage de clés secrètes au cours de développement d’applications](app-secrets.md) pour la gestion appropriée de la phrase secrète de certificat.

Sur Mac OS, vous pouvez [ajouter le certificat à votre trousseau](https://support.apple.com/kb/PH20129?locale=en_US) et [modifier ses paramètres d’approbation](https://support.apple.com/kb/PH20127?locale=en_US&viewlocale=en_US) afin qu’il soit approuvé pour le protocole HTTPS au cours du développement. Pour ajouter le certificat à votre chaîne de clé (l’équivalent de la `CurrentUser/My` stocker sur Windows) exécutez la commande suivante :

```bash
security import certificate.pfx -k ~/Library/Keychains/login.keychain-db
```

Puis faire confiance au certificat :

```bash
security add-trusted-cert localhost.cer
```

Vous pouvez ensuite configurer votre application pour utiliser ce certificat dans le développement, comme suit :

```json
{
  "Certificates": {
    "HTTPS": {
      "Source": "Store",
      "StoreLocation": "CurrentUser",
      "StoreName": "My",
      "Subject": "CN=localhost",
      "AllowInvalid": true
    }
  }
}
```
