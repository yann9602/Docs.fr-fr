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
ms.openlocfilehash: e06f4194d496b5b11aa867e66563bec317e735ff
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="setting-up-https-for-development-in-aspnet-core"></a><span data-ttu-id="2328c-104">Configuration de HTTPS pour le développement dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2328c-104">Setting up HTTPS for development in ASP.NET Core</span></span>

> [!NOTE] 
> <span data-ttu-id="2328c-105">Cette rubrique s’applique à ASP.NET Core 2.0 Preview 1</span><span class="sxs-lookup"><span data-stu-id="2328c-105">This topic applies to ASP.NET Core 2.0 Preview 1</span></span>

<span data-ttu-id="2328c-106">Vous pouvez configurer votre application pour utiliser HTTPS pendant le développement pour simuler HTTPS dans votre environnement de production.</span><span class="sxs-lookup"><span data-stu-id="2328c-106">You can configure your application to use HTTPS during development to simulate HTTPS in your production environment.</span></span> <span data-ttu-id="2328c-107">Activation de HTTPS peut-être être nécessaires pour activer l’intégration avec différents fournisseurs d’identité (telles que [Azure AD](https://azure.microsoft.com/services/active-directory) et [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c)).</span><span class="sxs-lookup"><span data-stu-id="2328c-107">Enabling HTTPS may be required to enable integration with various identity providers (like [Azure AD](https://azure.microsoft.com/services/active-directory) and [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c)).</span></span>

<a name="iisxpress"></a>

<span data-ttu-id="2328c-108">Sous Windows, si vous avez installé Visual Studio ou IIS Express, le certificat de développement IIS Express dans votre magasin de certificats LocalMachine.</span><span class="sxs-lookup"><span data-stu-id="2328c-108">On Windows if you’ve installed Visual Studio or IIS Express, the IIS Express Development Certificate will be in your LocalMachine certificate store.</span></span> <span data-ttu-id="2328c-109">Vous pouvez mettre à jour les propriétés de votre projet dans Visual Studio pour utiliser ce certificat lors de l’exécution derrière IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2328c-109">You can update your project properties in Visual Studio to use this certificate when running behind IIS Express.</span></span>

   * <span data-ttu-id="2328c-110">Dans l’Explorateur de solutions, cliquez sur le projet et sélectionnez **propriétés**.</span><span class="sxs-lookup"><span data-stu-id="2328c-110">In Solution Explorer, right-click the project and select **Properties**.</span></span>
   * <span data-ttu-id="2328c-111">Dans le volet gauche, sélectionnez **déboguer**.</span><span class="sxs-lookup"><span data-stu-id="2328c-111">On the left pane, select **Debug**.</span></span>
   * <span data-ttu-id="2328c-112">Vérifiez **activer SSL**</span><span class="sxs-lookup"><span data-stu-id="2328c-112">Check **Enable SSL**</span></span>
   * <span data-ttu-id="2328c-113">Copiez l’URL SSL et collez-la dans la **URL de l’application**</span><span class="sxs-lookup"><span data-stu-id="2328c-113">Copy the SSL URL and paste it into the **App URL**</span></span>

![Onglet de propriétés de l’application web de débogage](enforcing-ssl/_static/ssl.png)

<span data-ttu-id="2328c-115">Pour le développement, vous pouvez utiliser le certificat de développement IIS Express s’il est disponible, ou créer un nouveau certificat à des fins de développement.</span><span class="sxs-lookup"><span data-stu-id="2328c-115">For development you can use the IIS Express Development Certificate if it is available, or create a new certificate for development purposes.</span></span> <span data-ttu-id="2328c-116">Le certificat de développement doit être configuré dans le `appsettings.Development.json` afin qu’il n’est pas utilisé en production de fichiers :</span><span class="sxs-lookup"><span data-stu-id="2328c-116">The development certificate should be configured in the `appsettings.Development.json` file so that it is not used in production:</span></span>

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

<span data-ttu-id="2328c-117">Une application avec cette configuration en cours d’exécution en production lève une exception indiquant que « Aucun certificat nommé 'HTTPS' trouvés dans la configuration de l’environnement actuel (Production) ».</span><span class="sxs-lookup"><span data-stu-id="2328c-117">An app with this configuration running in production will throw an exception saying "No certificate named 'HTTPS' found in configuration for the current environment (Production)".</span></span> <span data-ttu-id="2328c-118">Pour basculer le [environnement](xref:fundamentals/environments) à `Development`, définissez le `ASPNETCORE_ENVIRONMENT` variable d’environnement `Development`.</span><span class="sxs-lookup"><span data-stu-id="2328c-118">To switch the [environment](xref:fundamentals/environments) to `Development`, set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development`.</span></span>

<span data-ttu-id="2328c-119">Si vous n’avez pas le certificat IIS Express développement installé, vous pouvez créer vous-même un certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="2328c-119">If you do not have the IIS Express Development Certificate installed, you can create a development certificate yourself.</span></span> <span data-ttu-id="2328c-120">Sur Windows, vous pouvez créer un certificat de développement et ajoutez-la au magasin racine de confiance pour l’utilisateur actuel en exécutant les commandes PowerShell suivantes dans une invite de commandes avec élévation de privilèges :</span><span class="sxs-lookup"><span data-stu-id="2328c-120">On Windows you can create a development certificate and add it to the trusted root store for the current user by running the following PowerShell commands in an elevated prompt:</span></span>

```powershell
$cert = New-SelfSignedCertificate -Subject localhost -DnsName localhost -FriendlyName "ASP.NET Core Development" -KeyUsage DigitalSignature -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1") 
Export-Certificate -Cert $cert -FilePath cert.cer
Import-Certificate -FilePath cert.cer -CertStoreLocation cert:/CurrentUser/Root
```

<a name="OpenSSL"></a>

## <a name="kestrel-on--macos-and-linux"></a><span data-ttu-id="2328c-121">Kestrel sur macOS et Linux</span><span class="sxs-lookup"><span data-stu-id="2328c-121">Kestrel on  macOS and Linux</span></span>

<span data-ttu-id="2328c-122">Vous pouvez configurer Kestrel pour écouter sur HTTPS en configurant un point de terminaison avec l’adresse, port et certificat IP souhaitée.</span><span class="sxs-lookup"><span data-stu-id="2328c-122">You can  configure Kestrel to listen over HTTPS by configuring an endpoint with the desired IP address, port, and certificate.</span></span> <span data-ttu-id="2328c-123">Le certificat peut être configuré en ligne, ou dans le niveau supérieur `Certificates` section, puis référencé par son nom :</span><span class="sxs-lookup"><span data-stu-id="2328c-123">The certificate can be configured inline, or in the top-level `Certificates` section and then referenced by name:</span></span>

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

<span data-ttu-id="2328c-124">Sur Mac OS et Linux, vous pouvez créer un certificat auto-signé pour l’utilisation de HTTPS [OpenSSL](https://www.openssl.org/):</span><span class="sxs-lookup"><span data-stu-id="2328c-124">On macOS and Linux you can create a self-signed certificate for HTTPS using [OpenSSL](https://www.openssl.org/):</span></span>

```bash
openssl req -new -x509 -newkey rsa:2048 -keyout localhost.key -out localhost.cer -days 365 -subj /CN=localhost
openssl pkcs12 -export -out certificate.pfx -inkey localhost.key -in localhost.cer
```

<span data-ttu-id="2328c-125">Une fois la `certificate.pfx` fichier a été généré, configurez le certificat HTTPS dans votre `appsettings.Development.json` fichier :</span><span class="sxs-lookup"><span data-stu-id="2328c-125">Once the `certificate.pfx` file has been generated, configure the HTTPS certificate in your `appsettings.Development.json` file:</span></span>

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

<span data-ttu-id="2328c-126">Vous devez également spécifier le mot de passe pour le certificat en définissant la propriété de configuration « Certificats : HTTPS:Password ».</span><span class="sxs-lookup"><span data-stu-id="2328c-126">You will also need to specify the passphrase for the certificate by setting the “Certificates:HTTPS:Password” config property.</span></span> <span data-ttu-id="2328c-127">Les mots de passe ne doivent pas être stockées en texte brut.</span><span class="sxs-lookup"><span data-stu-id="2328c-127">Passwords should not be stored in plain text.</span></span> <span data-ttu-id="2328c-128">Consultez [Safe stockage de clés secrètes au cours de développement d’applications](app-secrets.md) pour la gestion appropriée de la phrase secrète de certificat.</span><span class="sxs-lookup"><span data-stu-id="2328c-128">See [Safe Storage of App Secrets During Development](app-secrets.md) for appropriate handling of the certificate passphrase.</span></span>

<span data-ttu-id="2328c-129">Sur Mac OS, vous pouvez [ajouter le certificat à votre trousseau](https://support.apple.com/kb/PH20129?locale=en_US) et [modifier ses paramètres d’approbation](https://support.apple.com/kb/PH20127?locale=en_US&viewlocale=en_US) afin qu’il soit approuvé pour le protocole HTTPS au cours du développement.</span><span class="sxs-lookup"><span data-stu-id="2328c-129">On macOS you can [add the certificate to your keychain](https://support.apple.com/kb/PH20129?locale=en_US) and [change its trust settings](https://support.apple.com/kb/PH20127?locale=en_US&viewlocale=en_US) so that it is trusted for HTTPS during development.</span></span> <span data-ttu-id="2328c-130">Pour ajouter le certificat à votre chaîne de clé (l’équivalent de la `CurrentUser/My` stocker sur Windows) exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="2328c-130">To add the certificate to your keychain (the equivalent of the `CurrentUser/My` store on Windows) run the following command:</span></span>

```bash
security import certificate.pfx -k ~/Library/Keychains/login.keychain-db
```

<span data-ttu-id="2328c-131">Puis faire confiance au certificat :</span><span class="sxs-lookup"><span data-stu-id="2328c-131">And then to trust the certificate:</span></span>

```bash
security add-trusted-cert localhost.cer
```

<span data-ttu-id="2328c-132">Vous pouvez ensuite configurer votre application pour utiliser ce certificat dans le développement, comme suit :</span><span class="sxs-lookup"><span data-stu-id="2328c-132">You can then configure your app to use this certificate in development like this:</span></span>

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
