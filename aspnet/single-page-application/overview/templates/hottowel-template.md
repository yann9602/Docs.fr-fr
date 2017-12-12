---
uid: single-page-application/overview/templates/hottowel-template
title: "Modèle de linge à chaud | Documents Microsoft"
author: madskristensen
description: "Modèle de HotTowel"
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/09/2013
ms.topic: article
ms.assetid: 75af2e17-6ed3-4d24-8ea1-bc340027c318
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /single-page-application/overview/templates/hottowel-template
msc.type: authoredcontent
ms.openlocfilehash: bfc6e2c884c422f44e8be5f4f29554ae86f7ecb6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="hot-towel-template"></a>Modèle de linge à chaud
====================
par [Mads Kristensen](https://github.com/madskristensen)

> Le modèle MVC linge à chaud est écrits par John Papa
> 
> Choisir la version à télécharger :
> 
> [Modèle MVC linge à chaud pour Visual Studio 2012](https://visualstudiogallery.msdn.microsoft.com/1f68fbe8-b4e9-4968-9fd3-ddc7cbc52dca)
> 
> [Modèle MVC linge à chaud pour Visual Studio 2013](https://visualstudiogallery.msdn.microsoft.com/1eb8780d-d522-4dcf-bf56-56f0eab305c2)


> Linge à chaud : Étant donné que vous ne souhaitez pas atteindre le SPA sans un !


Pour créer un SPA mais ne peut pas déterminer où commencer ? Utilisez linge à chaud et en secondes, vous aurez un SPA et tous les outils dont vous avez besoin pour développer !

Linge à chaud crée un excellent point de départ pour la création d’une Application de Page unique (SPA) avec ASP.NET. Prêtes à l’emploi vous il fournit une structure modulaire pour votre code de navigation de la vue, liaison de données, gestion des données riches et style simple mais élégant. Linge à chaud offre tout ce dont vous avez besoin pour générer un SPA, afin de vous concentrer sur votre application, pas l’élément.

> En savoir plus sur la création d’un SPA à partir de [de John Papa vidéos, didacticiels et les cours Pluralsight](http://johnpapa.net/spa?vsix).


## <a name="application-structure"></a>Structure de l’application

À chaud linge SPA fournit un dossier d’application qui contient les fichiers JavaScript et HTML qui définissent votre application.

Dans le dossier de l’application :

- durandal
- services
- ViewModel
- vues

Le dossier de l’application contient une collection de modules. Ces modules encapsulent des fonctionnalités et déclarent des dépendances sur d’autres modules. Le dossier views contient le code HTML pour votre application et le dossier ViewModel contient la logique de présentation pour les vues (MVVM courant). Le dossier services est idéal pour héberger tous les services courants tels que la récupération des données HTTP ou d’une interaction de stockage local requis par votre application. Il est courant pour plusieurs ViewModel réutiliser du code à partir des modules de service.

## <a name="aspnet-mvc-server-side-application-structure"></a>Structure de l’Application côté serveur ASP.NET MVC

Linge à chaud s’appuie sur la structure ASP.NET MVC familier et puissante.

- Application\_Démarrer
- Contenu
- Contrôleurs
- Modèles
- scripts ;
- Affichages

## <a name="featured-libraries"></a>Bibliothèques proposées

- ASP.NET MVC
- API web ASP.NET
- Optimisation du Web ASP.NET - groupement et minimisation
- [Breeze.js](http://Breezejs.com) -gestion des données riche
- [Durandal.js](http://Durandaljs.com) -navigation et composition de la vue
- [Knockout.js](http://Knockoutjs.com) -liaisons de données
- [Require.js](http://requirejs.org) -modularité avec AMD et optimisation
- [Toastr.js](http://jpapa.me/c7toastr) -messages contextuels
- [Twitter Bootstrap](http://twitter.github.com/bootstrap/) - styles de CSS robuste

## <a name="installing-via-the-visual-studio-2012-hot-towel-spa-template"></a>Installation via le modèle SPA linge à chaud Visual Studio 2012

Linge à chaud peut être installé comme un modèle Visual Studio 2012. Cliquez simplement sur `File`  |  `New Project` et choisissez `ASP.NET MVC 4 Web Application`. Puis sélectionnez le ' Application à Page unique à chaud linge « modèle et exécuter !

## <a name="installing-via-the-nuget-package"></a>Installation via le Package NuGet

Linge à chaud est également un package NuGet qui augmente d’un projet ASP.NET MVC vide existant. Installez simplement à l’aide de Nuget, puis exécutez !

[!code-powershell[Main](hottowel-template/samples/sample1.ps1)]

## <a name="how-do-i-build-on-hot-towel"></a>Comment créer linge à chaud ?

Démarrez simplement ajouter du code !

1. Ajoutez votre propre code côté serveur, de préférence Entity Framework et WebAPI (qui vraiment éclairer avec Breeze.js)
2. Ajouter des vues à le `App/views` dossier
3. Ajouter ViewModel pour le `App/viewmodels` dossier
4. Ajouter le HTML et le masquage des liaisons de données pour vos nouveaux affichages
5. Mettre à jour des itinéraires de navigation`shell.js`

## <a name="walkthrough-of-the-htmljavascript"></a>Procédure pas à pas du HTML/JavaScript

### <a name="viewshottowelindexcshtml"></a>Views/HotTowel/index.cshtml

index.cshtml est l’itinéraire et la vue de l’application MVC départ. Il contient tous les balises meta standard, liens css et JavaScript les références souhaitées. Le corps contient un seul `<div>` qui est où tout le contenu (vos vues) sera placé lorsqu’elles sont demandées. Le `@Scripts.Render` utilise Require.js pour exécuter le point d’entrée pour le code de l’application, qui est contenu dans le `main.js` fichier. Un écran de démarrage est fourni pour montrer comment créer un écran de démarrage pendant le charge de votre application.

[!code-cshtml[Main](hottowel-template/samples/sample2.cshtml)]

### <a name="appmainjs"></a>App/main.js

Le `main.js` fichier contient le code qui s’exécutera dès que votre application est chargée. Il s’agit dans lequel vous souhaitez définir votre parcours de navigation, les vues de démarrage et effectuer tout le programme d’installation/amorçage telles que l’amorçage de données de votre application.

Le `main.js` fichier définit plusieurs des modules de durandal pour aider à commencer la réunion de lancement d’application. L’instruction de définition vous aide à résoudre les dépendances de modules afin qu’ils soient disponibles pour la fonction. Tout d’abord les messages de débogage sont activées, ce qui envoient des messages sur les événements d’exécution dans la fenêtre de console. Le code app.start indique framework durandal pour démarrer l’application. Les conventions sont définies de sorte que durandal connaît toutes les vues et ViewModel est contenus dans les mêmes dossiers nommés, respectivement. Enfin, le `app.setRoot` intervient charges le `shell` à l’aide de prédéfini `entrance` animation.

[!code-javascript[Main](hottowel-template/samples/sample3.js)]

## <a name="views"></a>Affichages

Vues sont trouvent dans le `App/views` dossier.

### <a name="shellhtml"></a>Shell.HTML

La `shell.html` contient la mise en page maître pour votre code HTML. Toutes les autres vues seront composé quelque part dans la partie de votre `shell` vue. Linge à chaud fournit un `shell` avec trois régions de ce type : un en-tête, une zone de contenu et un pied de page. Chacune de ces régions est chargé avec contenu forme d’autres vues lorsqu’il est demandé.

Le `compose` liaisons pour l’en-tête et le pied de page sont codées en durs dans linge à chaud pour pointer vers le `nav` et `footer` consulte, respectivement. La liaison de message pour la section `#content` est lié à la `router` élément d’actif du module. En d’autres termes, lorsque vous cliquez sur un lien de navigation elle est vue correspondante est chargé dans ce domaine.

[!code-html[Main](hottowel-template/samples/sample4.html)]

### <a name="navhtml"></a>NAV.HTML

Le `nav.html` contient les liens de navigation pour SPA. Il s’agit où la structure de menu peut être placée, par exemple. Il s’agit souvent les données liées (à l’aide de Knockout) à la `router` module pour afficher la barre de navigation que vous avez définies dans le `shell.js`. Knockout recherche la liaison de données des attributs et celles qui lie la `shell` viewmodel pour afficher les itinéraires de navigation et afficher un progressbar (à l’aide d’amorçage de Twitter) si le `router` module est en cours de la navigation d’une vue à une autre (voir `router.isNavigating`).

[!code-html[Main](hottowel-template/samples/sample5.html)]

### <a name="homehtml-and-detailshtml"></a>Home.HTML et details.html

Ces vues contiennent HTML pour des vues personnalisées. Lorsque le `home` lien dans le `nav` clic sur le menu de la vue, le `home` affichage sera placé dans la zone de contenu de la `shell` vue. Ces vues peuvent être augmentées ou remplacées par vos propres affichages personnalisés.

### <a name="footerhtml"></a>Footer.HTML

Le `footer.html` contient du code HTML qui s’affiche dans le pied de page, en bas de la `shell` vue.

## <a name="viewmodels"></a>ViewModel

ViewModel se trouvent dans le `App/viewmodels` dossier.

### <a name="shelljs"></a>Shell.js

Le `shell` viewmodel contient les propriétés et les fonctions qui sont liées à la `shell` vue. Il s’agit souvent où sont trouvent les liaisons de navigation de menu (consultez la `router.mapNav` logique).

[!code-javascript[Main](hottowel-template/samples/sample6.js)]

### <a name="homejs-and-detailsjs"></a>Home.js et details.js

Ces modèles ViewModel contiennent les propriétés et les fonctions qui sont liées à la `home` vue. Il contient la logique de présentation de la vue et vous est le lien entre les données et la vue.

[!code-javascript[Main](hottowel-template/samples/sample7.js)]

## <a name="services"></a>Services

Services sont trouvent dans le dossier services d’application. Dans l’idéal, vos futures services comme un module dataservice, qui est responsable de la mise en route et la validation des données distantes, peut être placés.

### <a name="loggerjs"></a>Logger.js

Linge à chaud fournit un `logger` module dans le dossier services. Le `logger` module est idéal pour les messages de journalisation pour la console et l’utilisateur dans la fenêtre contextuelle toasts.
