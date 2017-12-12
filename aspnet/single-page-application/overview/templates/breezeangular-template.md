---
uid: single-page-application/overview/templates/breezeangular-template
title: "Modèle de jeu d’enfant/angulaire | Documents Microsoft"
author: madskristensen
description: "Modèle d’Application à Page unique est très simple/angulaire"
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/08/2013
ms.topic: article
ms.assetid: db31e909-563a-4516-aadd-62aa210ac7e4
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /single-page-application/overview/templates/breezeangular-template
msc.type: authoredcontent
ms.openlocfilehash: faf28a510a83b7fa07585904344176601c2e1f34
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="breezeangular-template"></a>Modèle de jeu d’enfant/angulaire
====================
par [Mads Kristensen](https://github.com/madskristensen)

> Le modèle MVC est très simple/angulaire a été écrit par Ward Bell
> 
> [Téléchargez le modèle MVC est très simple/angulaire](https://go.microsoft.com/fwlink/?LinkId=286437)


[AngularJS](http://angularjs.org) est une bibliothèque open source à partir de Google pour la création d’Applications à Page unique (ZPS). Il offre la liaison de données, l’injection de dépendances et la gestion de l’écran. Combiner avec [BreezeJS](http://www.breezejs.com/?utm_source=ms-spa), une autre bibliothèque open source pour la modélisation des données et gestion des données et que vous avez les composants essentiels pour une application client HTML/JavaScript.

Le modèle est très simple/angulaire SPA est une variante de la [modèle de KnockoutJS SPA](../introduction/knockoutjs-template.md) inclus dans ASP.NET et Web Tools 2012.2 mise à jour. Si vous avez Visual Studio, vous aurez un exemple SPA en cours d’exécution en moins de 60 secondes.

![](http://www.breezejs.com/sites/all/images/spa-template/NgRunningTodoPage.png)

L’aspect, l’application semble très similaire à celui du modèle KnockoutJS SPA. Mais il est très différente dans les coulisses. Le modèle de KnockoutJS utilise Knockout pour la liaison de données et AJAX brute pour accéder aux données. Le modèle est très simple/angulaire utilise angulaire pour la liaison de données et est très simple pour accéder aux données. Ces bibliothèques activent des fonctionnalités supplémentaires, y compris l’historique et la navigation entre les pages.

Voici la page de l’application sur :

![](http://www.breezejs.com/sites/all/images/spa-template/NgRunningAboutPage.png)

Cette page affiche un journal des événements pendant la session active de l’utilisateur, y compris :

- Pagination. Notez la création de contrôleur Todo #2 et #7.
- Requêtes distantes (n° 3) et les requêtes dans le cache local (#7).
- Enregistrement de nouveau (n° 5, #6) et modifié les entités (n° 4).
- Modifications validées sur le client (#9), afin de l’utilisateur peut corriger les erreurs avant de valider les modifications apportées à la base de données.

Il est plus à découvrir dans ce modèle, y compris :

- Chargement dynamique des modèles d’affichage HTML.
- Liaison de données personnalisée via angulaire « directives ».
- Injection de dépendance et de modularité.
- Filtres de requête trie, la pagination, projections et inclusion des entités associées.
- Partage de données sur plusieurs écrans.
- L’enregistrement de plusieurs modifications comme une transaction unique.
- Règles de validation propagées automatiquement à partir du serveur au client JavaScript.

Allons-y.

## <a name="create-a-breezeangular-template-project"></a>Créer un projet de modèle est très simple/angulaire

Téléchargez et installez le modèle en cliquant sur le bouton Télécharger ci-dessus. Le modèle est empaqueté comme un fichier d’Extension Visual Studio (VSIX). Vous devrez peut-être redémarrer Visual Studio.

Dans le **modèles** volet, sélectionnez **modèles installés** et développez le **Visual C#** nœud. Sous **Visual C#**, sélectionnez **Web**. Dans la liste des modèles de projet, sélectionnez **ASP.NET MVC 4 Web Application**. Nommez le projet et cliquez sur **OK**.

Dans le **nouveau projet** Assistant, sélectionnez **SPA angulaire du jeu d’enfant**.

![](http://www.breezejs.com/sites/all/images/spa-template/SelectBreezeNgSpaTemplate.png)

Appuyez sur Ctrl-F5 pour générer et exécuter l’application sans débogage, ou appuyez sur F5 pour exécuter avec débogage.

![](http://www.breezejs.com/sites/all/images/spa-template/ZephyrLogin.png)

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
- Cliquez sur le lien « About » dans le coin supérieur droit pour afficher un journal de ces activités.

La logique de validation est effectuée côté client en un clin de œil. Attributs de validation sur les classes de modèle de serveur sont propagées vers le client et exécutés automatiquement avant que le client contacte le serveur.

Examinez le trafic réseau. Notez qu’il n’existait aucun appel au serveur lorsque le jeu d’enfant a détecté une erreur. Chaque modification valide a entraîné une demande POST sur « / api/tâches/SaveChanges ». Est très simple regroupe les modifications et les envoie ensemble comme une demande unique pour le contrôleur d’API Web `SaveChanges` (méthode). Qui est différent de modèle KockoutJS SPA, ce qui rend PUT, POST et supprimer des requêtes pour chaque élément individuellement.

En outre, ne notez aucun trafic réseau lorsque vous basculez entre la liste des tâches et sur les pages. C’est parce que la requête a été limitée dans le cache local est très simple.

## <a name="peek-inside"></a>Lire dans

Cette application a un côté client et un côté serveur. La pile côté client se compose de quelques HTML et une combinaison de modules de l’application JavaScript (dans le dossier « application ») ainsi que des bibliothèques JavaScript de tiers (dans le dossier « Scripts »).

![](http://www.breezejs.com/sites/all/images/spa-template/NgClientArchitecture2.png)

L’architecture de l’interface utilisateur sépare les widgets HTML des vues à partir du code de présentation de prise en charge dans les contrôleurs. Le système de liaison de données angulaire coordonne les vues et contrôleurs afin que chacun peut effectuer son travail sans une connaissance approfondie de l’autre.

Le contrôleur de demande le contexte de données pour obtenir et enregistrer les entités du modèle. Le contexte de données délègue la plupart du travail à est très simple, ce qui crée automatiquement le suivi des objets de modèle à partir des résultats de requête JSON.

La pile côté serveur se compose d’un code de développeur et trois bibliothèques .NET de principe : Breeze.NET API Web et Entity Framework :

![](http://www.breezejs.com/sites/all/images/spa-template/ServerArchitecture.png)

L’architecture de base est la même que le modèle KockoutJS SPA. Toutefois, l’implémentation est beaucoup plus simple : les données ont été supprimées, et la plupart des détails de l’Entity Framework ont été déléguée à Breeze.NET.

## <a name="next-steps"></a>Étapes suivantes

Nous vous suggérons d’Explorer le code, guidé par le [description détaillée](http://www.breezejs.com/ng-spa-template?utm_source=ms-spa) du client et les piles de serveur sur le site Web est très simple.

Vous pouvez essayer de lecture de la requête du côté client est très simple. ajouter des filtres et les tris. Vous pouvez ajouter plusieurs propriétés de modèle et des entités supplémentaires pour obtenir une meilleure idée pour le développement de SPA de bout en bout. Lorsque vous êtes certain de la conception, vous pouvez supprimer les fonctionnalités de tâches et les remplacer par les vôtres.

Bon développement !
