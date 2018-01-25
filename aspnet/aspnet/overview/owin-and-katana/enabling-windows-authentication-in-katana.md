---
uid: aspnet/overview/owin-and-katana/enabling-windows-authentication-in-katana
title: "L’activation de l’authentification Windows dans Katana | Documents Microsoft"
author: MikeWasson
description: "Cet article explique comment activer l’authentification Windows dans Katana. Elle couvre les deux scénarios : à l’aide d’IIS à l’hôte Katana et HttpListener auto-hébergement Kat..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/30/2013
ms.topic: article
ms.assetid: 82324ef0-3b75-4f63-a217-76ef4036ec93
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/owin-and-katana/enabling-windows-authentication-in-katana
msc.type: authoredcontent
ms.openlocfilehash: 8a26d356f7abafba021199761f9a49dcb81765c5
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="enabling-windows-authentication-in-katana"></a>L’activation de l’authentification Windows dans Katana
====================
par [Mike Wasson](https://github.com/MikeWasson)

> Cet article explique comment activer l’authentification Windows dans Katana. Elle couvre les deux scénarios : à l’aide d’IIS à l’hôte Katana et HttpListener auto-hébergement Katana dans un processus personnalisé. Barry Dorrans, David Matson et Chris Ross Merci d’avoir relu cet article.


Katana est l’implémentation Microsoft de [OWIN](http://owin.org/), l’Interface Web ouverte pour .NET. Vous trouverez une présentation de OWIN et Katana [ici](an-overview-of-project-katana.md). L’architecture OWIN a plusieurs couches :

- Hôte : Gère le processus dans lequel s’exécute le pipeline OWIN.
- Serveur : Ouvre un socket réseau et écoute les demandes.
- Intergiciel (middleware) : Traite la demande et réponse HTTP.

Katana fournit actuellement les deux serveurs, qui prennent en charge l’authentification intégrée de Windows :

- **Microsoft.Owin.Host.SystemWeb**. Utilise IIS avec le pipeline ASP.NET.
- **Microsoft.Owin.Host.HttpListener**. Utilise [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener.aspx). Ce serveur est actuellement l’option par défaut lors de l’hébergement des interconnexions.

> [!NOTE]
> Katana ne fournit pas actuellement d’intergiciel (middleware) OWIN pour l’authentification Windows, car cette fonctionnalité est déjà disponible dans les serveurs.


## <a name="windows-authentication-in-iis"></a>Authentification Windows dans IIS

Vous pouvez simplement à l’aide de Microsoft.Owin.Host.SystemWeb, pour activer l’authentification Windows dans IIS.

Commençons par la création d’une application ASP.NET à l’aide du modèle de projet « Application Web ASP.NET vide ».

![](enabling-windows-authentication-in-katana/_static/image1.png)

Ensuite, ajoutez les packages NuGet. À partir de la **outils** menu, sélectionnez **Gestionnaire de Package de bibliothèque**, puis sélectionnez **Package Manager Console**. Dans la fenêtre de Console du Gestionnaire de Package, entrez la commande suivante :

[!code-console[Main](enabling-windows-authentication-in-katana/samples/sample1.cmd)]

Ajoutez ensuite une classe nommée `Startup` avec le code suivant :

[!code-csharp[Main](enabling-windows-authentication-in-katana/samples/sample2.cs)]

Qui est que nécessaire pour créer une application « Hello world » pour OWIN, s’exécutant sur IIS. Appuyez sur F5 pour déboguer l'application. Vous devez voir « Hello World ! » dans la fenêtre du navigateur.

![](enabling-windows-authentication-in-katana/_static/image2.png)

Ensuite, nous allons activer l’authentification Windows dans IIS Express. À partir de la **vue** menu, sélectionnez **propriétés**. Cliquez sur le nom du projet dans l’Explorateur de solutions pour afficher les propriétés du projet.

Dans le **propriétés** , configurez **l’authentification anonyme** à **désactivé** et **l’authentification Windows** à  **Activé**.

![](enabling-windows-authentication-in-katana/_static/image3.png)

Lorsque vous exécutez l’application à partir de Visual Studio, IIS Express requiert les informations d’identification Windows. Vous pouvez le voir à l’aide de [Fiddler](http://fiddler2.com/home) ou HTTP un autre outil de débogage. Voici un exemple de réponse HTTP :

[!code-console[Main](enabling-windows-authentication-in-katana/samples/sample3.cmd?highlight=1,5-6)]

Les en-têtes WWW-Authenticate dans cette réponse indiquent que le serveur prend en charge la [Negotiate](http://www.ietf.org/rfc/rfc4559.txt) protocole, qui utilise l’authentification Kerberos ou NTLM.

Plus tard, lorsque vous déployez l’application sur un serveur, suivez [suit](https://www.iis.net/configreference/system.webserver/security/authentication/windowsauthentication) pour activer l’authentification Windows dans IIS sur ce serveur.

## <a name="windows-authentication-in-httplistener"></a>Authentification Windows dans HttpListener

Si vous utilisez Microsoft.Owin.Host.HttpListener pour Katana l’auto-hébergement, vous pouvez activer l’authentification Windows directement sur le **HttpListener** instance.

Tout d’abord, créez une nouvelle application console. Ensuite, ajoutez les packages NuGet. À partir de la **outils** menu, sélectionnez **Gestionnaire de Package de bibliothèque**, puis sélectionnez **Package Manager Console**. Dans la fenêtre de Console du Gestionnaire de Package, entrez la commande suivante :

[!code-console[Main](enabling-windows-authentication-in-katana/samples/sample4.cmd)]

Ajoutez ensuite une classe nommée `Startup` avec le code suivant :

[!code-csharp[Main](enabling-windows-authentication-in-katana/samples/sample5.cs)]

Cette classe implémente l’exemple « Hello world » avant, mais il définit également l’authentification Windows en tant que le schéma d’authentification.

À l’intérieur de la `Main` de fonction, le pipeline OWIN de début :

[!code-csharp[Main](enabling-windows-authentication-in-katana/samples/sample6.cs)]

Vous pouvez envoyer une demande de Fiddler pour confirmer que l’application utilise l’authentification Windows :

[!code-console[Main](enabling-windows-authentication-in-katana/samples/sample7.cmd?highlight=1,4-5)]

## <a name="related-topics"></a>Rubriques connexes

[Vue d’ensemble du projet Katana](an-overview-of-project-katana.md)

[System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener.aspx)

[Présentation de l’authentification de formulaires OWIN dans MVC 5](https://blogs.msdn.com/b/webdev/archive/2013/07/03/understanding-owin-forms-authentication-in-mvc-5.aspx)
