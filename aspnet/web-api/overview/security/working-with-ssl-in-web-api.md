---
uid: web-api/overview/security/working-with-ssl-in-web-api
title: "Utilisation de SSL dans l’API Web | Documents Microsoft"
author: MikeWasson
description: "Montre comment utiliser le protocole SSL avec l’API Web ASP.NET, y compris l’utilisation de certificats de client SSL."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/12/2012
ms.topic: article
ms.assetid: 97f6164f-59cf-45c0-b820-e4aa29b45396
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/security/working-with-ssl-in-web-api
msc.type: authoredcontent
ms.openlocfilehash: 8c631900c8c5ab6097e0cb9fd4a71abbcba1c88b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="working-with-ssl-in-web-api"></a>Utilisation de SSL dans l’API Web
====================
par [Mike Wasson](https://github.com/MikeWasson)

Plusieurs schémas d’authentification courantes ne sont pas sécurisés sur HTTP en clair. En particulier, l’authentification de base et l’authentification par formulaire envoient des informations d’identification non chiffrées. Pour être sécurisés, ces schémas d’authentification *doit* utiliser SSL. En outre, les certificats clients SSL peuvent être utilisés pour authentifier les clients.

## <a name="enabling-ssl-on-the-server"></a>L’activation de SSL sur le serveur

Pour configurer SSL dans IIS 7 ou version ultérieure :

- Créez ou obtenez un certificat. Pour le test, vous pouvez créer un certificat auto-signé.
- Ajouter une liaison HTTPS.

Pour plus d’informations, consultez [comment la configuration de SSL sur IIS 7](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis).

Pour le test local, vous pouvez activer SSL dans IIS Express à partir de Visual Studio. Dans la fenêtre Propriétés, définissez **SSL activé** à **True**. Notez la valeur de **URL SSL**; utiliser cette URL pour le test des connexions HTTPS.

![](working-with-ssl-in-web-api/_static/image1.png)

### <a name="enforcing-ssl-in-a-web-api-controller"></a>Application de SSL dans un contrôleur d’API Web

Si vous avez à la fois une liaison HTTP et HTTPS, les clients peuvent toujours utiliser HTTP pour accéder au site. Vous pouvez autoriser des ressources pour être disponible via le protocole HTTP, tandis que les autres ressources exiger le protocole SSL. Dans ce cas, utilisez un filtre d’action pour exiger SSL pour les ressources protégées. Le code suivant montre un filtre API Web d’authentification qui vérifie si SSL :

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample1.cs)]

Ajoutez ce filtre à toutes les actions de l’API Web nécessitant SSL :

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample2.cs)]

## <a name="ssl-client-certificates"></a>Certificats clients SSL

Le protocole SSL fournit l’authentification à l’aide de certificats d’Infrastructure à clé publique. Le serveur doit fournir un certificat qui authentifie le serveur au client. Il est moins courant pour le client fournisse un certificat pour le serveur, mais il s’agit d’une option pour l’authentification de clients. Pour utiliser des certificats de client avec SSL, vous avez besoin d’un moyen de distribuer des certificats auto-signés pour vos utilisateurs. Pour de nombreux types d’application, il s’agit pas d’une bonne expérience utilisateur, mais dans certains environnements (par exemple, enterprise), il peut être possible.

| Avantages | Inconvénients |
| --- | --- |
| -Informations d’identification de certificat sont plus fortes que le nom d’utilisateur/mot de passe. -SSL fournit un canal sécurisé complète, avec l’authentification, l’intégrité des messages et le chiffrement du message. | -Vous devez obtenir et gérer des certificats d’infrastructure à clé publique. -La plateforme du client doit prendre en charge les certificats clients SSL. |

Pour configurer IIS pour accepter les certificats de client, ouvrez le Gestionnaire des services Internet et procédez comme suit :

1. Cliquez sur le nœud de site dans l’arborescence.
2. Double-cliquez sur le **paramètres SSL** fonctionnalité dans le volet central.
3. Sous **les certificats clients**, sélectionnez une des options suivantes : 

    - **Accepter**: IIS accepte un certificat à partir du client, mais n’en requiert pas.
    - **Nécessitent**: nécessitent un certificat client. (Pour activer cette option, vous devez également sélectionner « Exiger SSL »)

Vous pouvez également définir ces options dans le fichier ApplicationHost.config :

[!code-xml[Main](working-with-ssl-in-web-api/samples/sample3.xml)]

Le **SslNegotiateCert** indicateur signifie IIS accepte un certificat à partir du client, mais ne requiert pas (équivalent à l’option « Accepter » dans le Gestionnaire des services Internet). Pour demander un certificat, vous devez définir le **le paramètre SslRequireCert** indicateur. Pour le test, vous pouvez également définir ces options dans IIS Express, dans le hôte d’application local. Fichier de configuration situé dans « Documents\IISExpress\config ».

### <a name="creating-a-client-certificate-for-testing"></a>Création d’un certificat de Client de test

À des fins de test, vous pouvez utiliser [MakeCert.exe](https://msdn.microsoft.com/en-US/library/bfsktky3.aspx) pour créer un certificat client. Commencez par créer une autorité racine de test :

[!code-console[Main](working-with-ssl-in-web-api/samples/sample4.cmd)]

Makecert vous invite à entrer un mot de passe pour la clé privée.

Ensuite, ajoutez le certificat pour le test de stockage « Trusted Root Certification Authorities » du serveur, comme suit :

1. Ouvrez la console MMC.
2. Sous **fichier**, sélectionnez **ajouter/supprimer un composant logiciel enfichable**.
3. Sélectionnez **compte d’ordinateur**.
4. Sélectionnez **ordinateur Local** et terminez l’Assistant.
5. Sous le volet de navigation, développez le nœud « Trusted Root Certification Authorities ».
6. Sur le **Action** menu, pointez sur **toutes les tâches**, puis cliquez sur **importation** pour démarrer l’Assistant Importation de certificat.
7. Recherchez le fichier de certificat, TempCA.cer.
8. Cliquez sur **ouvrir**, puis cliquez sur **suivant** et terminez l’Assistant. (Vous devrez entrer à nouveau le mot de passe.)

Créer un certificat de client qui est signé par le premier certificat :

[!code-console[Main](working-with-ssl-in-web-api/samples/sample5.cmd)]

### <a name="using-client-certificates-in-web-api"></a>À l’aide de certificats de Client dans l’API Web

Côté serveur, vous pouvez obtenir le certificat client en appelant [GetClientCertificate](https://msdn.microsoft.com/en-us/library/system.net.http.httprequestmessageextensions.getclientcertificate.aspx) sur le message de demande. La méthode retourne null s’il n’existe aucun certificat client. Sinon, elle retourne un **X509Certificate2** instance. Utilisez cet objet pour obtenir des informations à partir du certificat, telles que l’émetteur et le sujet. Ensuite, vous pouvez utiliser ces informations pour l’authentification et/ou l’autorisation.

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample6.cs)]
