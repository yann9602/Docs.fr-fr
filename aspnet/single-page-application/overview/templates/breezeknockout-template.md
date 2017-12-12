---
uid: single-page-application/overview/templates/breezeknockout-template
title: "Modèle de jeu d’enfant/Knockout | Documents Microsoft"
author: madskristensen
description: "Modèle d’Application à Page unique est très simple/masquage"
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/30/2013
ms.topic: article
ms.assetid: 3bd94827-3c59-448f-abc3-36e6df4858db
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /single-page-application/overview/templates/breezeknockout-template
msc.type: authoredcontent
ms.openlocfilehash: 07ec099a0381458fe42c1972a2554f76fd34638c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="breezeknockout-template"></a>Modèle de jeu d’enfant/masquage
====================
par [Mads Kristensen](https://github.com/madskristensen)

> Le modèle MVC est très simple/Knockout a été écrit par Ward Bell
> 
> [Télécharger le modèle MVC est très simple/masquage](https://go.microsoft.com/fwlink/?LinkId=282649)


Vous avez entendu parler de « application à page unique » (SPA) et de vous demander ce qu’il est. Pendant que vous pouvez lire sur celui-ci, vous serez plutôt l’expérience par vous-même. Mais qui a le temps de télécharger un exemple ? Si vous avez Visual Studio, vous devez obtenir un exemple SPA et en cours d’exécution en moins de 60 secondes avec le ASP.NET MVC 4 modèle « Application à Page unique est très simple/Knockout ».

![](http://www.breezejs.com/sites/all/images/spa-template/ZephyrRunning.png)

## <a name="what-is-the-breezeknockout-spa-template"></a>Qu’est le modèle de SPA est très simple/Knockout ?

La plupart des modèles de projet de génèrent un squelette d’application. Mettre flesh sur ces segments en ajoutant votre code et éventuellement fournir une application opérationnelle. Le modèle est très simple/Knockout SPA est différent. Il génère un exemple d’application vous permet d’étudier. Il montre une conception d’application SPA et de nombreuses techniques pour la création d’un SPA.

Le modèle est très simple/Knockout est une variante de la [modèle de KnockoutJS SPA](../introduction/knockoutjs-template.md) inclus dans ASP.NET et Web Tools 2012.2 mise à jour. Le modèle est très simple SPA génère une application avec la même expérience utilisateur, mais il possède une implémentation différente, à l’aide du jeu d’enfant pour la gestion des données.

Le modèle KnockoutJS SPA envoie des demandes de service avec brute jQuery AJAX, qui est suffisant pour une application simple. Mais les applications plus sophistiquées ont des exigences de gestion des données plus strictes. Par exemple, la plupart des applications :

- Interroger et ré-interroger le serveur pendant une session de l’étendue de l’utilisateur.
- Ajouter des filtres de requête, le tri et la pagination.
- Partager les mêmes données sur plusieurs écrans.
- Accumuler les modifications à de nombreux objets, puis les enregistrer sous la forme d’une transaction unique.
- Valider les modifications apportées sur le client, afin de l’utilisateur peut corriger les erreurs avant de valider les modifications apportées à la base de données.

La bibliothèque BreezeJS gère ces tâches pour vous, vous permettant de développer l’expérience utilisateur et la logique d’application qui importent le plus.

[**Est très simple** ](http://www.breezejs.com/?utm_source=ms-spa) est une bibliothèque open source pour créer des applications de données enrichies en JavaScript et HTML, les types d’applications qui ont été remises historiquement en tant qu’applications de bureau autonomes.

Le modèle est très simple/Knockout vous permet de prendre cette première étape essentielle vers une infrastructure de gestion des données plus robuste. Il génère un exemple d’application Todo qui l’aspect est identique au modèle KnockoutJS SPA. À l’intérieur, elle remplace la couche de données AJAX est très simple, pour vous pouvez de comparer les deux approches côte-à-côte. Bien entendu, il touche à peine le potentiel d’une application est très simple. Mais vous verrez le fonctionne est très simple et très peu est requis pour effectuer cette transition.

Allons-y.

## <a name="create-a-breezeknockout-template-project"></a>Créer un projet de modèle est très simple/masquage

Téléchargez et installez le modèle en cliquant sur le bouton Télécharger ci-dessus. Le modèle est empaqueté comme un fichier d’Extension Visual Studio (VSIX). Vous devrez peut-être redémarrer Visual Studio.

Dans le **modèles** volet, sélectionnez **modèles installés** et développez le **Visual C#** nœud. Sous **Visual C#**, sélectionnez **Web**. Dans la liste des modèles de projet, sélectionnez **ASP.NET MVC 4 Web Application**. Nommez le projet et cliquez sur **OK**.

Dans le **nouveau projet** Assistant, sélectionnez **est très simple Knockout SPA**.

![](http://www.breezejs.com/sites/all/images/spa-template/SelectBreezeKOSpaTemplate.png)

Appuyez sur Ctrl-F5 pour générer et exécuter l’application sans débogage, ou appuyez sur F5 pour exécuter avec débogage.

![](http://www.breezejs.com/sites/all/images/spa-template/ZephyrRunning.png)

Lorsque l’application s’exécute tout d’abord, il affiche un écran de connexion. Cliquez sur le lien « Inscription » et une nouvelle page glides dans la vue, dans lequel vous pouvez entrer un nom d’utilisateur et un mot de passe. (Les pages de connexion et d’inscription sont construites à l’aide d’ASP.NET MVC.) Quand vous envoyez le formulaire d’inscription, le serveur génère une liste des tâches avec deux éléments de votre compte. Puis il les présente sur une note jaune.

![](http://www.breezejs.com/sites/all/images/spa-template/TodoList.png)

Vous êtes maintenant dans la terre de SPA. Tout ce dont vous consultez et rencontrez lors de la manipulation de Todos est rendu et géré sur le client à l’aide de Knockout et est très simple. Explorer l’application en tant qu’utilisateur... mais avec les yeux d’un développeur. Utilisez les outils de développement dans votre navigateur pour capturer le trafic réseau. (Dans Internet Explorer : appuyez sur F12, sélectionnez le **réseau** onglet, puis cliquez sur **démarrer la capture**.) Maintenant, essayez ce qui suit :

- Ajouter un nouvel élément de tâche.
- Cliquez sur l’étiquette et de modifier le titre de l’élément Todo
- Cochez une case pour marquer l’élément terminé. Notez que la zone de texte est désactivé, le titre n’est plus modifiable.
- Cliquez sur le « x » à droite de l’étiquette. L’élément disparaît et est supprimé de la base de données.
- Choisissez un autre élément et effacer son titre. Vous obtiendrez une erreur de validation que le titre est obligatoire. Après une brève pause, le titre de la précédent est restauré.
- Tapez un titre ridiculement long. Vous obtiendrez une erreur de validation autre que le titre est trop long.
- Cliquez sur le bouton « Ajouter une liste Todo ». Une nouvelle liste apparaît à gauche de la liste précédente.
- Manipuler le titre de TodoList, déclencher son requis et les validations de longueur.
- Cliquez dans la zone de texte titre pour effacer le message d’erreur.
- Cliquez sur le « x » dans le cercle dans le coin supérieur droit pour supprimer la liste des tâches et ses todos.

La logique de validation est effectuée côté client en un clin de œil. Attributs de validation sur les classes de modèle de serveur sont propagées vers le client et exécutés automatiquement avant que le client contacte le serveur.

Examinez le trafic réseau. Notez qu’il n’existait aucun appel au serveur lorsque le jeu d’enfant a détecté une erreur. Chaque modification valide a entraîné une demande POST sur « / api/tâches/SaveChanges ». Est très simple regroupe les modifications et les envoie ensemble comme une demande unique pour le contrôleur d’API Web `SaveChanges` (méthode). Qui est différent de modèle KockoutJS SPA, ce qui rend PUT, POST et supprimer des requêtes pour chaque élément individuellement.

## <a name="peek-inside"></a>Lire dans

Cette application a un côté client et un côté serveur. La pile côté client se compose de quelques HTML et une combinaison de modules de l’application JavaScript (dans le dossier « application ») ainsi que des bibliothèques JavaScript de tiers (dans le dossier « Scripts »).

![](http://www.breezejs.com/sites/all/images/spa-template/ClientArchitecture.png)

Si vous avez examiné le modèle KnockoutJS SPA, cela doit sembler très familière. Vous concentrer sur les zones bleues. L’architecture de l’interface utilisateur est Model-View-ViewModel (MVVM), dans laquelle les widgets HTML de la vue sont nettement séparées du code de présentation prise en charge dans le modèle d’affichage. Un système de liaison de données (Knockout dans ce cas) coordonne la vue et le modèle d’affichage afin que chacun peut effectuer son travail sans une connaissance approfondie de l’autre.

Le modèle encapsule les données de tâches. Entités dans le modèle sont construites en un clin de œil avec les propriétés observables Knockout, ils peuvent être liés directement à des widgets dans la vue. Le modèle d’affichage vous demande le contexte de données pour obtenir et enregistrer les entités du modèle. Le contexte de données délègue la plupart du travail à un jeu d’enfant.

La pile côté serveur se compose d’un code de développeur et trois bibliothèques .NET de principe : Breeze.NET API Web et Entity Framework :

![](http://www.breezejs.com/sites/all/images/spa-template/ServerArchitecture.png)

L’architecture de base est la même que le modèle KockoutJS SPA. Toutefois, l’implémentation est beaucoup plus simple : les données ont été supprimées, et la plupart des détails de l’Entity Framework ont été déléguée à Breeze.NET.

## <a name="next-steps"></a>Étapes suivantes

Nous vous suggérons d’Explorer le code, guidé par le [description détaillée](http://www.breezejs.com/spa-template?utm_source=ms-spa) du client et les piles de serveur sur le site Web est très simple.

Vous pouvez essayer de lecture de la requête du côté client est très simple. ajouter des filtres et les tris. Vous pouvez ajouter plusieurs propriétés de modèle et des entités supplémentaires pour obtenir une meilleure idée pour le développement de SPA de bout en bout. Lorsque vous êtes certain de la conception, vous pouvez supprimer les fonctionnalités de tâches et les remplacer par les vôtres.

Dès vous serez prêt pour l’étape suivante de big : ajout d’écrans du côté client et la navigation entre eux. Vous allez laisse ce modèle SPA et l’activer à une pile SPA plus complet, tel que [linge à chaud de John Papa](https://github.com/johnpapa/HotTowel#readme "linge à chaud"), qui ajoute Durandal à la combinaison est très simple et de masquage.
