---
uid: single-page-application/overview/introduction/other-libraries
title: "Savoir une bibliothèque de Knockout ? | Microsoft Docs"
author: madskristensen
description: "Savoir une bibliothèque de Knockout ?"
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/05/2013
ms.topic: article
ms.assetid: a8367c6d-ef94-4dff-a010-5eff9e6eea96
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /single-page-application/overview/introduction/other-libraries
msc.type: authoredcontent
ms.openlocfilehash: 5a863f50401a4e2bab3f772374b7fd178f6c6cdf
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="know-a-library-other-than-knockout"></a>Savoir une bibliothèque de Knockout ?
====================
par [Mads Kristensen](https://github.com/madskristensen)

Le [modèle d’Application de Page unique (SPA)](knockoutjs-template.md) est un excellent moyen pour commencer à écrire des applications à page unique. Le modèle utilise [KnockoutJS](http://knockoutjs.com/) pour lier des données d’application pour les éléments DOM.

Mais Knockout n’est pas la bibliothèque JavaScript uniquement pour la création d’applications clientes riches. Autres bibliothèques de relever les défis similaires de différentes façons. Vous préférerez peut-être une une bibliothèque vers un autre, donc nous avons apporté plusieurs modèles créés par la Communauté disponibles en téléchargement. Chacun de ces modèles utilise une combinaison différente des bibliothèques JavaScript client.

Pour installer un modèle créés par la Communauté, visitez l’un du modèle de pages répertoriées ci-dessous et cliquez sur le bouton Télécharger. Les modèles sont fournis en tant que fichiers VSIX.

## <a name="backbonejs"></a>BackboneJS

[Modèle SPA backbone.js](../templates/backbonejs-template.md). Ce modèle fournit une structure initiale pour le développement d’un [Backbone.js](http://backbonejs.org/) application dans ASP.NET MVC. Prêtes à l’emploi, il fournit les fonctionnalités de connexion utilisateur de base, y compris la réinitialisation de mot de passe d’abonnement, connectez-vous, l’utilisateur et la confirmation de l’utilisateur avec les modèles de messagerie de base.

## <a name="breezejs"></a>BreezeJS

[BreezeJS](http://www.breezejs.com/?utm_source=ms-spa) est une bibliothèque open source pour la gestion des données riches dans un client JavaScript. Est très simple gère l’interrogation, la mise en cache, le suivi des modifications, la validation et bien plus encore. Deux modèles de fonctionnalités est très simple :

- Le [est très simple/Knockout](../templates/breezeknockout-template.md) modèle étend le modèle de Knockout SPA, montrant comment facilement, vous pouvez générer une application de page unique avec un jeu d’enfant pour la gestion des données et KnockoutJS pour la liaison de données.
- Le [est très simple/angulaire](../templates/breezeangular-template.md) modèle permet également d’étendre le modèle de Knockout SPA avec est très simple, mais à l’aide de la [AngularJS](http://angularjs.org) bibliothèque pour la liaison de données, l’injection de dépendances et la gestion de l’écran.

En outre, le [modèle d’à chaud linge SPA](../templates/hottowel-template.md) utilise BreezeJS.

## <a name="emberjs"></a>EmberJS

[Modèle de EmberJS SPA](../templates/emberjs-template.md). Ce modèle utilise [Ember](http://emberjs.com/), une bibliothèque MVC JavaScript puissante qui permet de résoudre un grand nombre de difficultés de création d’applications clientes riches.

Le modèle les SPA est une implémentation de nouveau du modèle Knockout SPA, à l’aide de la création de modèles EmberJS et guidons.

## <a name="hot-towel"></a>Linge à chaud

[Modèle de linge SPA à chaud](../templates/hottowel-template.md). Ce modèle permet de plusieurs bibliothèques de JavaScript, y compris les est très simple, Knockout, RequireJS et programme d’amorçage Twitter.

Comparaison avec les autres modèles répertoriés ici, la teample linge à chaud fournit une application plus complète à partir de laquelle vous pouvez créer vos propres. Il existe plusieurs concepts à connaître, mais une fois que vous comprenez les, ce modèle peut être simplement ce que vous recherchez. Si vous souhaitez générer un SPA mais que vous ne peut pas choisir l’emplacement Démarrer, utilisez linge à chaud et en secondes, vous aurez un SPA et tous les outils que vous deviez générer sur celui-ci.

## <a name="feature-table"></a>Tableau des fonctionnalités

Voici les fonctionnalités fournies par chaque modèle SPA :

|  | Application à page unique ASP.NET | Réseau principal | Jeu d’enfant/angulaire | Jeu d’enfant/KO | Ember | Linge à chaud |
| --- | --- | --- | --- | --- | --- | --- |
| Exemple de tâches | &#10003; |  | &#10003; | &#10003; | &#10003; |  |
| Modèle de récupération |  | &#10003; |  |  |  | &#10003; |
| Navigation et l’historique |  | &#10003; | &#10003; |  | &#10003; | &#10003; |
| Suivies |  |  |  |  |  |  |
| angulaire |  |  | &#10003; |  |  |  |
| &#8195; Réseau principal |  | &#10003; |  |  |  |  |
| Jeu d’enfant |  |  | &#10003; | &#10003; |  | &#10003; |
| durandal |  |  |  |  |  | &#10003; |
| Ember |  |  |  |  | &#10003; |  |
| Knockout | &#10003; |  |  | &#10003; |  | &#10003; |
