---
uid: web-forms/overview/older-versions-getting-started/deploying-web-site-projects/core-differences-between-iis-and-the-asp-net-development-server-vb
title: "Principales différences entre IIS et le serveur de développement ASP.NET (VB) | Documents Microsoft"
author: rick-anderson
description: "Lorsque vous testez une application ASP.NET localement, sans doute à l’aide de serveur Web de développement ASP.NET. Toutefois, le site Web de production est probablement pow..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/01/2009
ms.topic: article
ms.assetid: 090e9205-52f3-4d72-ae31-44775b8b8421
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deploying-web-site-projects/core-differences-between-iis-and-the-asp-net-development-server-vb
msc.type: authoredcontent
ms.openlocfilehash: 8e8ae3c0fd1d67fba6dff965704b550bdc919c6a
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="core-differences-between-iis-and-the-aspnet-development-server-vb"></a>Différences principales entre IIS et le serveur de développement ASP.NET (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/4/5/F/45F815EC-8B0E-46D3-9FB8-2DC015CCA306/ASPNET_Hosting_Tutorial_06_VB.zip) ou [télécharger le PDF](http://download.microsoft.com/download/E/8/9/E8920AE6-D441-41A7-8A77-9EF8FF970D8B/aspnet_tutorial06_WebServerDiff_vb.pdf)

> Lorsque vous testez une application ASP.NET localement, sans doute à l’aide de serveur Web de développement ASP.NET. Toutefois, le site Web de production est probablement IIS sous tension. Il existe des différences entre ces serveurs web comment gérer les demandes, et ces différences peuvent avoir des conséquences importantes. Ce didacticiel présente quelques-unes des différences plus pertinentes.


## <a name="introduction"></a>Introduction

Chaque fois qu’un utilisateur visite une application ASP.NET son navigateur envoie une demande pour le site Web. Cette demande est récupérée par le logiciel de serveur web, qui coordonne avec le runtime ASP.NET pour générer et retourner le contenu de la ressource demandée. Le[**I** Internet **I** nformations **S** ervices (IIS)](http://en.wikipedia.org/wiki/Internet_Information_Services) sont une suite de services qui fournissent des fonctionnalités communes basé sur Internet pour Serveurs Windows. IIS est le serveur web couramment utilisées pour les applications ASP.NET dans les environnements de production ; Il est très probablement le logiciel de serveur web utilisé par votre fournisseur d’hébergement web pour servir votre application ASP.NET. IIS peut également servir en tant que le logiciel de serveur web dans l’environnement de développement, bien que cela implique l’installation d’IIS et le configurer correctement.


Le serveur de développement ASP.NET est une option de serveur web de remplacement pour l’environnement de développement ; Il est fourni avec et est intégré à Visual Studio. À moins que l’application web a été configurée pour utiliser IIS, le serveur de développement ASP.NET sont automatiquement démarré et utilisé en tant que le serveur web, la première fois que vous visitez une page web à partir de Visual Studio. Les applications web de démonstration nous avons créé dans le [ *déterminer quels fichiers doivent être déployés* ](determining-what-files-need-to-be-deployed-vb.md) didacticiel ont été les deux applications web basés sur le système de fichiers qui n’ont pas été configurées pour utiliser IIS. Par conséquent, lors de la visite d’un de ces sites Web à partir de Visual Studio, le serveur de développement ASP.NET est utilisé.

Dans l’idéal, les environnements de développement et de production serait identiques. Toutefois, comme expliqué dans le didacticiel précédent qu’il n’est pas rare pour les environnements avec des paramètres de configuration différents. À l’aide du logiciel de serveur web différent dans les environnements ajoute une autre variable qui doit prendre en considération lors du déploiement d’une application. Ce didacticiel décrit les principales différences entre IIS et le serveur de développement ASP.NET. En raison de ces différences, il existe des scénarios où le code qui s’exécute parfaitement dans l’environnement de développement lève une exception ou se comporte différemment lors de l’exécution en production.

## <a name="security-context-differences"></a>Différences de contexte de sécurité

Chaque fois que le logiciel de serveur web traite une demande entrante il associe cette demande à un contexte de sécurité particulier. Ces informations de contexte de sécurité sont utilisées par le système d’exploitation pour déterminer quelles sont les actions autorisées par la demande. Par exemple, une page ASP.NET peut-être inclure du code qui consigne un message dans un fichier sur disque. Pour cette page ASP.NET de s’exécuter sans erreur, le contexte de sécurité doit avoir approprié les autorisations au niveau du système de fichiers, à savoir les autorisations sur ce fichier en écriture.

Le serveur de développement ASP.NET associe les demandes entrantes avec le contexte de sécurité de l’utilisateur actuellement connecté. Si vous êtes connecté à votre bureau en tant qu’administrateur, les pages ASP.NET pris en charge par le serveur de développement ASP.NET aura les mêmes droits d’accès en tant qu’administrateur. Toutefois, les demandes ASP.NET gérées par les services IIS sont associés à un compte d’ordinateur spécifique. Par défaut, le compte d’ordinateur de Service réseau est utilisé par IIS version 6 et 7, même si votre fournisseur d’hébergement web a peut-être configuré un compte unique pour chaque client. De plus, votre fournisseur d’hébergement web a probablement limité des autorisations accordées pour ce compte d’ordinateur. Le résultat net est que vous pouvez avoir des pages web qui s’exécutent sans erreur dans l’environnement de développement, mais génèrent des exceptions liées aux autorisations lorsqu’il est hébergé dans l’environnement de production.

Pour afficher ce type d’erreur en action, j’ai créé une page dans le site Web critiques qui crée un fichier sur disque qui stocke la date et l’heure la plus récente à une personne affiché le *apprendre vous-même ASP.NET 3.5 des dernières 24 heures* passez en revue. Pour suivre la procédure, vous devez ouvrir le `~/Tech/TYASP35.aspx` page et ajoutez le code suivant à la `Page_Load` Gestionnaire d’événements :

[!code-vb[Main](core-differences-between-iis-and-the-asp-net-development-server-vb/samples/sample1.vb)]

> [!NOTE]
> Le [ `File.WriteAllText` méthode](https://msdn.microsoft.com/library/system.io.file.writealltext.aspx) crée un fichier s’il n’existe pas et puis écrit le contenu spécifié. Si le fichier existe déjà, son contenu existant est remplacé.


Ensuite, visitez le *apprendre vous-même ASP.NET 3.5 des dernières 24 heures* page de révision de livres dans l’environnement de développement à l’aide du serveur de développement ASP.NET. En supposant que vous êtes connecté une session sur votre ordinateur avec un compte qui dispose des autorisations adéquates pour créer et modifier un fichier texte dans le site web répertoire racine de l’application la révision du livre s’affiche les mêmes qu’avant, mais chaque fois que la page est visité la date et l’heure et l’utilisateur  Adresse IP est stockée dans le `LastTYASP35Access.txt` fichier. Pointez votre navigateur sur ce fichier. Vous devez voir un message similaire à celui illustré dans la Figure 1.


[![Le fichier texte contient la Date et l’heure de la révision du livre a été visitée.&lt;](core-differences-between-iis-and-the-asp-net-development-server-vb/_static/image2.png)](core-differences-between-iis-and-the-asp-net-development-server-vb/_static/image1.png)

**Figure 1**: le fichier texte contient la Date et l’heure de la révision du livre a été visitée ([cliquez pour afficher l’image en taille réelle](core-differences-between-iis-and-the-asp-net-development-server-vb/_static/image3.png))


Déployer l’application web en production, puis vous accédez hébergé *apprendre vous-même ASP.NET 3.5 des dernières 24 heures* page de révision de livre. À ce stade, vous devez voir soit la page vérifier les livres sous la forme normale ou le message d’erreur indiqué dans la Figure 2. Certains fournisseurs d’hébergement web accorder des autorisations d’écriture pour le compte d’ordinateur ASP.NET anonyme, dans lequel cas la page fonctionnera sans erreur. Si, toutefois, votre fournisseur d’hébergement web interdit l’accès en écriture pour le compte anonyme alors un [ `UnauthorizedAccessException` exception](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) est déclenché lorsque la `TYASP35.aspx` page tente d’écrire la date et heure actuelles pour le `LastTYASP35Access.txt` fichier.


[![Le compte d’ordinateur par défaut utilisé par IIS ne dispose pas des autorisations pour écrire dans le système de fichiers](core-differences-between-iis-and-the-asp-net-development-server-vb/_static/image5.png)](core-differences-between-iis-and-the-asp-net-development-server-vb/_static/image4.png)

**Figure 2**: la valeur par défaut ordinateur compte utilisé par IIS est non ont les autorisations en écriture au système de fichiers ([cliquez pour afficher l’image en taille réelle](core-differences-between-iis-and-the-asp-net-development-server-vb/_static/image6.png))


La bonne nouvelle est que la plupart des fournisseurs d’hébergement web ont un quelconque outil d’autorisations qui vous permet de spécifier des autorisations de système de fichiers dans votre site Web. Accorder l’accès en écriture ASP.NET compte anonyme au répertoire racine et puis réexaminer la page vérifier les livre. (Si nécessaire, contactez votre fournisseur d’hébergement web pour obtenir de l’aide sur la façon d’accorder des autorisations en écriture sur le compte ASP.NET par défaut). Cette fois, la page doit charger sans erreur et la `LastTYASP35Access.txt` fichier doit être créé avec succès.

La suite ici est que le serveur de développement ASP.NET s’exécute sous un contexte de sécurité IIS, il est possible que vos pages ASP.NET de lire ou écrire dans le système de fichiers, lire ou écrire dans le journal des événements Windows, ou lire ou écrire dans les fenêtres  Registre fonctionne comme prévu sur le développement, mais génère des exceptions sur la production. Lors de la création d’une application web qui sera déployé sur un environnement d’hébergement de web partagée, ne pas lire ou écrire dans le journal des événements ou le Registre Windows. Notez également des pages ASP.NET de lire ou écrire dans le système de fichiers que vous devrez peut-être accorder en lecture et en écriture sur les dossiers appropriés dans l’environnement de production.

## <a name="differences-on-serving-static-content"></a>Différences sur le service de contenu statique

Une autre différence core entre IIS et le serveur de développement ASP.NET est la façon dont ils gèrent les demandes de contenu statique. Chaque demande qui entrée dans le serveur de développement ASP.NET, d’une page ASP.NET, une image ou un fichier JavaScript, est traité par le runtime ASP.NET. Par défaut, IIS appelle uniquement le runtime ASP.NET lorsqu’une demande arrive pour une ressource ASP.NET, comme une page web ASP.NET, un Service Web et ainsi de suite. Requêtes de contenu statique - images, fichiers CSS, les fichiers JavaScript, les fichiers PDF, fichiers ZIP, etc. - sont récupérés par IIS sans intervention de l’exécution d’ASP.NET. (Il est possible d’indiquer à IIS pour utiliser le runtime ASP.NET lorsque fournit du contenu statique ; dans ce didacticiel pour plus d’informations, consultez la section « Exécution l’authentification par formulaire et l’authentification d’URL sur les fichiers statiques avec IIS 7 »).

Le runtime ASP.NET effectue un nombre d’étapes pour générer le contenu demandé, y compris l’authentification (qui identifie le demandeur) et l’autorisation (qui détermine si le demandeur a l’autorisation d’afficher le contenu demandé). Une forme courante d’authentification est *l’authentification basée sur les formulaires*, dans lequel un utilisateur est identifié en entrant leurs informations d’identification, généralement un nom d’utilisateur et mot de passe, dans les zones de texte sur une page web. Lors de la validation de leurs informations d’identification, le site Web stocke un *ticket d’authentification* cookie sur le navigateur de l’utilisateur, qui est envoyé avec chaque demande suivante sur le site Web et est celui qui est utilisé pour authentifier l’utilisateur. En outre, il est possible de spécifier *l’autorisation d’URL* des règles qui déterminent ce que les utilisateurs peuvent ou ne peut pas accéder à un dossier particulier. Nombre de sites Web ASP.NET utilisent l’authentification basée sur les formulaires et l’autorisation d’URL pour prendre en charge les comptes d’utilisateur et de définir des portions du site qui sont uniquement accessibles aux utilisateurs authentifiés ou les utilisateurs qui appartiennent à un rôle donné.

> [!NOTE]
> Pour un examen approfondi de ASP. De NET l’authentification basée sur les formulaires, l’autorisation d’URL et autres fonctionnalités de compte utilisateur, veillez à consulter ma [didacticiels de sécurité du site Web](../../older-versions-security/introduction/security-basics-and-asp-net-support-cs.md).


Envisagez d’un site Web qui prend en charge les comptes d’utilisateur à l’aide d’autorisation basée sur les formulaires et un dossier, à l’aide de l’autorisation d’URL, est configuré pour autoriser uniquement les utilisateurs authentifiés. Imaginez que ce dossier contient les pages ASP.NET et les fichiers PDF et que l’objectif est que seuls les utilisateurs authentifiés peuvent afficher ces fichiers PDF.

Que se passe-t-il si un visiteur tente d’afficher un de ces fichiers PDF en entrant l’URL directement dans la barre d’adresse de son navigateur ? Pour déterminer, nous allons créer un nouveau dossier dans le site critiques, ajouter des fichiers PDF et configurer le site pour utiliser l’autorisation d’URL pour interdire aux utilisateurs anonymes d’accéder à ce dossier. Si vous téléchargez l’application de démonstration, vous allez voir que j’ai créé un dossier appelé `PrivateDocs` et ajouté un fichier PDF à partir de mon [didacticiels de sécurité du site Web](../../older-versions-security/introduction/security-basics-and-asp-net-support-cs.md) (comment raccord !). Le `PrivateDocs` dossier contient également un `Web.config` fichier qui spécifie les règles d’autorisation d’URL pour refuser les utilisateurs anonymes :

[!code-xml[Main](core-differences-between-iis-and-the-asp-net-development-server-vb/samples/sample2.xml)]

Enfin, j’ai configuré l’application web pour utiliser l’authentification basée sur les formulaires en mettant à jour le fichier Web.config dans le répertoire racine, en remplaçant :

[!code-xml[Main](core-differences-between-iis-and-the-asp-net-development-server-vb/samples/sample3.xml)]

Par :

[!code-xml[Main](core-differences-between-iis-and-the-asp-net-development-server-vb/samples/sample4.xml)]

À l’aide du serveur de développement ASP.NET, visitez le site, puis entrez l’URL directe de l’un des fichiers PDF dans la barre d’adresses de votre navigateur. Si vous avez téléchargé le site Web associé à ce didacticiel, à que l’URL doit ressembler :`http://localhost:portNumber/PrivateDocs/aspnet_tutorial01_Basics_vb.pdf`

Entrée de cette URL dans la barre d’adresses entraîne le navigateur à envoyer une demande au serveur de développement ASP.NET pour le fichier. Le serveur de développement ASP.NET remet la demande pour le runtime ASP.NET pour le traitement. Étant donné que nous n’avons pas encore connecté et parce que le `Web.config` dans les `PrivateDocs` dossier est configuré pour refuser l’accès anonyme, le runtime ASP.NET nous redirige automatiquement vers la page de connexion, `Login.aspx` (voir Figure 3). Lors de la redirection de l’utilisateur dans le journal dans la page, ASP.NET inclut un `ReturnUrl` paramètre querystring qui indique la page de l’utilisateur a tenté d’afficher. Une fois l’utilisateur connecté peut être redirigé vers cette page.


[![Les utilisateurs non autorisés sont automatiquement redirigé vers la Page de connexion](core-differences-between-iis-and-the-asp-net-development-server-vb/_static/image8.png)](core-differences-between-iis-and-the-asp-net-development-server-vb/_static/image7.png)

**Figure 3**: les utilisateurs non autorisés sont automatiquement redirigé vers la Page de connexion ([cliquez pour afficher l’image en taille réelle](core-differences-between-iis-and-the-asp-net-development-server-vb/_static/image9.png))


Maintenant nous allons voir comment cela se comporte sur la production. Déployer votre application et entrez l’URL directement à un des fichiers PDF dans le `PrivateDocs` dossier en production. Cela vous demande votre navigateur pour envoyer une demande IIS pour le fichier. Car un fichier statique est demandé, IIS extrait et retourne le fichier sans appeler le runtime ASP.NET. Par conséquent, il n’a aucune vérification de l’autorisation d’URL effectuée ; le contenu du fichier PDF censé privé est accessible à toute personne connaissant l’URL directe au fichier.


[![Les utilisateurs anonymes peuvent télécharger les fichiers de PDF de privé en entrant l’URL directe du fichier](core-differences-between-iis-and-the-asp-net-development-server-vb/_static/image11.png)](core-differences-between-iis-and-the-asp-net-development-server-vb/_static/image10.png)

**Figure 4**: anonymes les utilisateurs peuvent télécharger le privé PDF fichiers en entrant l’URL directe au fichier ([cliquez pour afficher l’image en taille réelle](core-differences-between-iis-and-the-asp-net-development-server-vb/_static/image12.png))


### <a name="performing-forms-based-authentication-and-url-authentication-on-static-files-with-iis-7"></a>Exécution de l’authentification basée sur les formulaires et l’authentification des URL sur des fichiers statiques avec IIS 7

Il existe quelques techniques que vous pouvez utiliser pour protéger du contenu statique des utilisateurs non autorisés. IIS 7 a introduit le *pipeline intégré*, lequel phares de flux de travail d’IIS avec les flux de travail du runtime ASP.NET. En bref, vous pouvez demander à IIS pour appeler l’authentification du runtime ASP.NET et les modules d’autorisation de toutes les demandes entrantes (y compris le contenu statique comme les fichiers PDF). Contactez votre fournisseur d’hébergement web pour savoir comment configurer votre site Web pour utiliser le pipeline intégré.

Une fois qu’IIS a été configuré pour utiliser le pipeline intégré ajoutez le balisage suivant à la `Web.config` fichier dans le répertoire racine :

[!code-xml[Main](core-differences-between-iis-and-the-asp-net-development-server-vb/samples/sample5.xml)]

Ce balisage indique à IIS 7 pour utiliser les modules d’authentification et d’autorisation basés sur ASP.NET. Déployez de nouveau votre application et puis vous accédez à nouveau le fichier PDF. Instant lorsqu’IIS gère la demande il permet à la logique d’authentification et d’autorisation du runtime ASP.NET pour examiner la demande. Étant donné que seuls les utilisateurs authentifiés sont autorisés à afficher le contenu dans le `PrivateDocs` dossier, l’utilisateur anonyme est automatiquement redirigée vers la page de connexion (voir la Figure 3).

> [!NOTE]
> Si votre fournisseur d’hébergement web est toujours à l’aide de IIS 6 vous ne pouvez pas utiliser la fonctionnalité pipeline intégré. Une solution de contournement consiste à placer vos documents privés dans un dossier qui interdit l’accès HTTP (comme `App_Data`), puis créer une page pour répondre à ces documents. Cette page peut être appelée `GetPDF.aspx`et reçoit le nom du fichier PDF via un paramètre de chaîne de requête. Le `GetPDF.aspx` page serait Vérifiez d’abord que l’utilisateur a l’autorisation d’afficher le fichier et, dans ce cas, utilisez le [ `Response.WriteFile(filePath)` ](https://msdn.microsoft.com/library/system.web.httpresponse.writefile.aspx) méthode pour envoyer le contenu du fichier PDF demandé au client demandeur. Cette technique fonctionne également pour IIS 7 si vous ne pas activer le pipeline intégré.


## <a name="summary"></a>Récapitulatif

Les applications Web dans un environnement de production sont hébergées à l’aide du logiciel de serveur web IIS de Microsoft. Dans l’environnement de développement, toutefois, l’application peut être hébergée à l’aide d’IIS ou le serveur de développement ASP.NET. Dans l’idéal, le même logiciel de serveur web doit être utilisé dans les deux environnements, car il est à l’aide de logiciels différents ajoute une autre variable dans la combinaison. Toutefois, la facilité d’utilisation du serveur de développement ASP.NET rend un choix intéressant dans l’environnement de développement. La bonne nouvelle est qu’il existe uniquement quelques différences fondamentales entre IIS et le serveur de développement ASP.NET, et si vous êtes conscient de ces différences vous pouvez prendre des mesures afin de garantir que l’application fonctionne et qu’il fonctionne de la même façon quel que soit le environnement.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Intégration d’ASP.NET à IIS 7.0](https://www.iis.net/learn/application-frameworks/building-and-running-aspnet-applications/aspnet-integration-with-iis)
- [À l’aide de l’authentification ASP.NET Forums avec tous les Types de contenu sur IIS 7](https://blogs.iis.net/bills/archive/2007/05/19/using-asp-net-forms-authentication-with-all-types-of-content-with-iis7-video.aspx) (vidéo)
- [Serveurs Web dans Visual Web Developer](https://msdn.microsoft.com/library/58wxa9w5.aspx)

>[!div class="step-by-step"]
[Précédent](common-configuration-differences-between-development-and-production-vb.md)
[Suivant](deploying-a-database-vb.md)
