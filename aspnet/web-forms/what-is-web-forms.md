---
uid: web-forms/what-is-web-forms
title: "Nouveautés des Web Forms | Documents Microsoft"
author: rick-anderson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/21/2014
ms.topic: article
ms.assetid: 5fa1daf9-1161-4cfa-bd4c-658f48b2c229
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/what-is-web-forms
msc.type: content
ms.openlocfilehash: dcaba60a8640ce83f73460a5c8ee457257b9397e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="what-is-web-forms"></a>Nouveautés de Web Forms
====================
Web Forms ASP.NET fait partie de l’infrastructure d’application web ASP.NET et est inclus avec [Visual Studio](https://www.asp.net/downloads). C’est un des quatre modèles de programmation que vous pouvez utiliser pour créer des applications web ASP.NET, les autres sont ASP.NET MVC, les Pages Web ASP.NET et ASP.NET Applications à Page unique.

Web Forms sont des pages de vos utilisateurs demandent à l’aide de leur navigateur. Ces pages peuvent être écrites à l’aide d’une combinaison de HTML, un script client, les contrôles serveur et les code serveur. Lorsque des utilisateurs demandent une page, il est compilé et exécuté sur le serveur par le framework, et l’infrastructure génère ensuite le balisage HTML qui peut restituer du navigateur. Une page Web Forms ASP.NET présente des informations à l’utilisateur dans n’importe quel navigateur ou le périphérique client.

À l’aide de Visual Studio, vous pouvez créer des Web Forms ASP.NET. L’environnement de développement intégré de Visual Studio (IDE) vous permet de faire glisser des contrôles de serveur pour présenter votre page Web Forms. Vous pouvez ensuite facilement définir propriétés, méthodes et événements pour les contrôles sur la page ou de la page elle-même. Ces propriétés, méthodes et événements sont utilisés pour définir le comportement de la page web, apparence et la convivialité et ainsi de suite. Pour écrire le code serveur pour gérer la logique de la page, vous pouvez utiliser un langage .NET tels que Visual Basic ou c#.

> [!NOTE] 
> 
> Documentation d’ASP.NET et Visual Studio s’étend sur plusieurs versions. Les rubriques qui présentent les fonctionnalités des versions précédentes peuvent être utiles pour vos tâches en cours et les scénarios à l’aide des versions les plus récentes.


**ASP.NET Web Forms sont :** 

- Basé sur la technologie Microsoft ASP.NET, dans laquelle le code qui s’exécute dynamiquement sur le serveur génère la sortie de page Web sur le navigateur ou le périphérique client.
- Compatible avec n’importe quel navigateur ou l’appareil mobile. Une page Web ASP.NET restitue automatiquement le HTML conforme au navigateur correct des fonctionnalités telles que les styles, disposition et ainsi de suite.
- Compatible avec n’importe quel langage pris en charge par le common language runtime .NET, tels que Microsoft Visual Basic et Microsoft Visual c#.
- Basé sur Microsoft .NET Framework. Cela fournit tous les avantages de l’infrastructure, y compris un environnement géré, sécurité de type et l’héritage.
- Flexible, car vous pouvez ajouter de créés par l’utilisateur et de contrôles pour les tiers.

**ASP.NET Web Forms offre :** 

- Séparation du code HTML et tout autre code d’interface utilisateur à partir de la logique d’application.
- Une suite riche des contrôles de serveur pour les tâches courantes, notamment l’accès aux données.
- Puissantes liaison de données, avec prise en charge de l’outil idéal.
- Prise en charge des scripts côté client qui s’exécute dans le navigateur.
- Prise en charge de diverses autres fonctionnalités, notamment le routage, sécurité, performances, internationalisation, test, de débogage, la gestion des erreurs et la gestion d’état.

## <a name="aspnet-web-forms-helps-you-overcome-challenges"></a>Web Forms ASP.NET vous permet de relever les défis

Programmation d’applications Web présente des défis qui n’est généralement pas confronté lors de la programmation des applications clientes traditionnelles. Parmi les principaux défis sont :

- **Implémentation d’interface utilisateur Web enrichie** - il peut être difficile et fastidieuse concevoir et implémenter un utilisateur de l’interface à l’aide des fonctionnalités HTML de base, en particulier si la page a une disposition complexe, une grande quantité de contenu dynamique et complet objets interactif avec l’utilisateur.
- **Séparation du client et serveur** -application dans un Web, le client (navigateur) et le serveur sont différents de programmes en cours d’exécution sur des ordinateurs différents (et même sur différents systèmes d’exploitation). Par conséquent, les deux moitiés de l’application de partagent très peu d’informations ; ils peuvent communiquer, mais généralement échanger uniquement de petits segments d’informations simples.
- **L’exécution sans état** - lorsqu’un serveur Web reçoit une demande pour une page, il recherche la page, la traite, envoie au navigateur et puis ignore toutes les informations de la page. Si l’utilisateur demande la même page de nouveau, le serveur répète toute la séquence, le retraitement de la page à partir de zéro. Autrement dit, un serveur n’a aucune mémoire de pages qu’il a traité : page sont sans état. Par conséquent, si une application doit mettre à jour les informations sur une page, sa nature sans état peut devenir un problème.
- **Fonctionnalités du client inconnu** -dans de nombreux cas, les applications Web sont accessibles à de nombreux utilisateurs à l’aide de différents navigateurs. Navigateurs ont des fonctions différentes, ce qui complique la création d’une application qui s’exécute aussi bien sur chacun d'entre eux.
- **Complications avec accès aux données** -lecture et l’écriture dans une source de données dans les applications Web traditionnelles peuvent être complexes et gourmandes en ressources.
- **Complications relatives à l’extensibilité** - dans de nombreux cas, les applications Web conçues avec les méthodes existantes ne satisfait pas les objectifs d’évolutivité en raison du manque de compatibilité entre les différents composants de l’application. Il s’agit souvent un point d’échec courants pour les applications sous un cycle de croissance importante.

Ces défis pour les applications Web peuvent nécessiter l’effort et prendre du temps. Web Forms ASP.NET et l’infrastructure ASP.NET relever ces défis de plusieurs manières :

- **Modèle objet intuitif et cohérent** -infrastructure de page ASP.NET présente un modèle objet qui vous permet de considérer vos formulaires en tant qu’unité, et non comme des éléments distincts de client et le serveur. Dans ce modèle, vous pouvez programmer la page d’une façon plus intuitive que dans les applications Web traditionnelles, y compris la possibilité de définir les propriétés d’éléments de la page et répondre aux événements. En outre, les contrôles serveur ASP.NET sont une abstraction du contenu physique d’une page HTML et de l’interaction directe entre le navigateur et le serveur. En règle générale, vous pouvez utiliser les contrôles serveur la façon que vous pouvez utiliser les contrôles dans une application cliente et sans avoir à réfléchir sur la façon de créer le code HTML pour présenter et traiter les contrôles et leur contenu.
- **Modèle de programmation pilotée par événements** -ASP.NET Web Forms offrent aux applications Web le modèle familier d’écriture de gestionnaires d’événements pour les événements qui se produisent sur le serveur ou client. L’infrastructure de page ASP.NET résume ce modèle de sorte que le mécanisme sous-jacent de capture d’un événement sur le client, il transmet au serveur et appeler la méthode appropriée soit entièrement automatique et invisible pour vous. Le résultat est une structure claire et facile à écrire de code qui prend en charge le développement piloté par les événements.
- **Gestion des états intuitive** : infrastructure de page ASP.NET gère automatiquement la tâche de maintenance de l’état de votre page et ses contrôles, et il vous fournit des méthodes explicites pour maintenir l’état des informations spécifiques à l’application. Cela s’effectue sans utilisation intensive des ressources du serveur et peut être implémentée avec ou sans l’envoi des cookies dans le navigateur.
- **Applications indépendantes du navigateur** -infrastructure de page ASP.NET vous permet de créer toute la logique d’application sur le serveur, en éliminant la nécessité de coder explicitement pour les différences dans les navigateurs. Toutefois, il permet toujours vous permet de tirer parti des fonctionnalités spécifiques au navigateur en écrivant du code côté client pour fournir de meilleures performances et une expérience plus riche de client.
- **Prise en charge de .NET framework common language runtime** -infrastructure de page ASP.NET repose sur le .NET Framework, l’infrastructure entière est donc disponible pour n’importe quelle application ASP.NET. Vos applications peuvent être écrites dans n’importe quel langage compatible avec le runtime. En outre, l’accès aux données est simplifié à l’aide de l’infrastructure d’accès de données fournie par le .NET Framework, y compris ADO.NET.
- **Performance des serveurs évolutifs .NET framework** -infrastructure de page ASP.NET vous permet de mettre à l’échelle votre application Web à partir d’un ordinateur avec un seul processeur à une batterie de serveurs Web sur plusieurs ordinateurs et sans modifications complexes apportées à l’application logique.

## <a name="features-of-aspnet-web-forms"></a>Fonctionnalités des Web Forms ASP.NET

- **Contrôles serveur**-contrôles serveur Web ASP.NET sont des objets dans les pages Web ASP.NET qui s’exécutent lorsque la page est demandée et ce balisage rendu dans le navigateur. De nombreux contrôles de serveur Web sont semblables à des éléments HTML familiers, tels que des boutons et des zones de texte. Autres contrôles ont un comportement complexe, par exemple un calendrier de contrôles et des contrôles que vous pouvez utiliser pour se connecter aux sources de données et afficher des données.
- **Pages maîtres**-pages maîtres ASP.NET permettent de créer une disposition cohérente pour les pages dans votre application. Une seule page maître définit l’apparence et le comportement standard que vous souhaitez pour toutes les pages (ou un groupe de pages) de votre application. Vous pouvez ensuite créer des pages de contenu individuelles qui contiennent le contenu que vous souhaitez afficher. Lorsque des utilisateurs demandent les pages de contenu, elles fusionnent avec la page maître pour produire une sortie qui associe la disposition de la page maître avec le contenu à partir de la page de contenu.
- **Utilisation des données**-ASP.NET fournit de nombreuses options pour le stockage, la récupération et l’affichage des données. Dans une application ASP.NET Web Forms, les contrôles liés aux données vous permet d’automatiser la présentation ou l’entrée de données dans les éléments d’interface utilisateur de page web tels que des tables et des zones de texte et des listes déroulantes.
- **L’appartenance**-identité ASP.NET stocke les informations d’identification de vos utilisateurs dans une base de données créée par l’application. Quand vos utilisateurs de se connectent, l’application valide leurs informations d’identification en lisant la base de données. De votre projet *compte* dossier contient les fichiers qui implémentent les différentes parties de l’appartenance : l’inscription, connexion, la modification d’un mot de passe et autoriser l’accès. En outre, ASP.NET Web Forms prend en charge OAuth et OpenID. Ces améliorations de l’authentification permettent aux utilisateurs de se connecter à votre site à l’aide des informations d’identification existantes, à partir de ces comptes en tant que Facebook, Twitter, Windows Live et Google. Par défaut, le modèle crée une base de données d’appartenance à l’aide d’un nom de base de données par défaut sur une instance de SQL Server Express LocalDB, le serveur de base de données de développement qui est fourni avec Visual Studio Express 2013 pour le Web.
- **Script client et Client infrastructures**-vous pouvez améliorer les fonctionnalités de serveur d’ASP.NET en incluant des fonctionnalités de script client dans les pages ASP.NET Web Form. Vous pouvez utiliser le script client pour fournir une interface utilisateur plus riche et plus réactives aux utilisateurs. Vous pouvez également utiliser le script client pour effectuer des appels asynchrones sur le serveur Web pendant l’exécution d’une page dans le navigateur.
- **Routage**-routage d’URL vous permet de configurer une application à accepter les URL qui ne sont pas mappent à des fichiers physiques de la requête. Une URL de demande est simplement l’URL dans laquelle un utilisateur entre dans son navigateur pour rechercher une page sur votre site web. Utiliser le routage pour définir des URL qui sont sémantiquement significatives pour les utilisateurs et qui peuvent aider à avec l’optimisation de moteur de recherche (SEO).
- **La gestion d’état**-Web Forms ASP.NET inclut plusieurs options qui vous aident à conservent les données sur une base par page et une application à l’échelle.
- **Sécurité**-une partie importante du développement d’une application plus sécurisée consiste à comprendre les menaces. Microsoft a développé un moyen de classer les menaces : usurpation, falsification, répudiation, divulgation d’informations, déni de service, une élévation de privilèges (STRIDE). Dans ASP.NET Web Forms, vous pouvez ajouter des points d’extensibilité et les options de configuration qui vous permettent de personnaliser les différents comportements de sécurité dans ASP.NET Web Forms.
- **Performances**-performances peuvent être un facteur clé dans un projet ou un site Web a réussi. ASP.NET Web Forms vous permet de modifier les performances associées à la page et le traitement du contrôle serveur, la gestion d’état, accès aux données, configuration de l’application et le chargement et efficace de pratiques de codage.
- **Internationalisation**: ASP.NET Web Forms permet de vous permet de créer des pages web qui peut obtenir du contenu et autres données en fonction du paramètre de langue par défaut pour le navigateur ou, selon le choix explicite de l’utilisateur du langage. Contenu et d’autres données CONSTITUES en tant que ressources, et ces données peuvent être stockées dans les fichiers de ressources ou d’autres sources. Une page Web Forms ASP.NET, vous permet de configurer des contrôles pour obtenir leurs valeurs de propriété à partir des ressources. Au moment de l’exécution, les expressions de ressource sont remplacées par des ressources à partir du fichier de ressources localisé approprié.
- **Débogage et gestion des erreurs**-ASP.NET inclut des fonctionnalités pour vous aider à diagnostiquer les problèmes susceptibles de survenir dans votre application Web Forms. Débogage et gestion des erreurs sont également prises en charge dans les formulaires Web ASP.NET afin que vos applications compiler et exécuter efficacement.
- **Déploiement et hébergement**-Visual Studio, ASP.NET, Azure et IIS fournissent des outils qui vous guident tout au long du processus de déploiement et d’hébergement de votre application Web Forms.

## <a name="deciding-when-to-create-a-web-forms-application"></a>Quand créer une Application Web Forms

Vous devez déterminer avec soin si pour implémenter une application Web en utilisant l’ASP.NET Web Forms modèle ou un autre modèle, telles que l’infrastructure ASP.NET MVC. L’infrastructure MVC ne remplace pas le modèle Web Forms ; Vous pouvez utiliser l’infrastructure pour les applications Web. Avant de décider d’utiliser le modèle Web Forms ou l’infrastructure MVC pour un site Web spécifique, évaluez les avantages de chaque approche.

### <a name="advantages-of-a-web-forms-based-web-application"></a>Avantages d’une Application Web basée sur des formulaires de Web

L’infrastructure basée sur des Web Forms offre les avantages suivants :

- Il prend en charge un modèle d’événement qui conserve l’état via HTTP, ce qui profite du développement d’applications Web line-of-business. L’application basée sur des Web Forms fournit des dizaines d’événements qui sont pris en charge dans des centaines de contrôles serveur.
- Elle utilise un modèle de contrôleur de Page qui ajoute des fonctionnalités aux pages individuelles. Pour plus d’informations, consultez [Page Controller](https://go.microsoft.com/fwlink/?LinkId=106359 "Page Controller") sur le site Web MSDN.
- Elle utilise l’état d’affichage ou les formulaires serveur, qui peuvent faciliter la gestion des informations d’état.
- Elle fonctionne bien pour les petites équipes de développeurs et concepteurs qui souhaitent tirer parti des nombreux composants disponibles pour le développement rapide d’application Web.
- En général, il est moins complexe pour le développement d’applications, car les composants (le **Page** classe, des contrôles, etc.) sont étroitement intégrés et requièrent habituellement moins de code que le modèle MVC.

### <a name="advantages-of-an-mvc-based-web-application"></a>Avantages d’une Application Web basée sur MVC

L’infrastructure ASP.NET MVC offre les avantages suivants :

- Il est plus facile à gérer la complexité en décomposant une application dans le modèle, la vue et le contrôleur.
- Il n’utilise pas l’état d’affichage ou des formulaires basés sur le serveur. Cela rend l’infrastructure MVC idéale pour les développeurs qui souhaitent un contrôle total sur le comportement d’une application.
- Elle utilise un modèle de contrôleur frontal qui traite les demandes d’application Web via un seul contrôleur. Cela vous permet de concevoir une application qui prend en charge une infrastructure de routage complète. Pour plus d’informations, consultez [Front Controller](https://go.microsoft.com/fwlink/?LinkId=106357 "Front Controller") sur le site Web MSDN.
- Il offre un meilleur support pour le développement piloté par test (TDD).
- Elle fonctionne bien pour les applications Web qui sont pris en charge par de grandes équipes de développeurs et concepteurs Web qui ont besoin d’un degré élevé de contrôle sur le comportement de l’application.
