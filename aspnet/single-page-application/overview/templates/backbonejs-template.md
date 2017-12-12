---
uid: single-page-application/overview/templates/backbonejs-template
title: "Modèle de réseau principal | Documents Microsoft"
author: madskristensen
description: "Modèle SPA backbone.js"
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/04/2013
ms.topic: article
ms.assetid: 00aca413-f067-4108-9bd1-cf21e64a2646
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /single-page-application/overview/templates/backbonejs-template
msc.type: authoredcontent
ms.openlocfilehash: 3b8eabd3cefcb96dc40bbf6cc6e3ee81accb0d7c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="backbone-template"></a>Modèle de réseau principal
====================
par [Mads Kristensen](https://github.com/madskristensen)

> Le modèle de SPA Backbone a été écrit par Kazi Manzur Rashid
> 
> [Téléchargez le modèle de SPA Backbone.js](https://go.microsoft.com/fwlink/?LinkId=293631)


Le modèle Backbone.js SPA est conçu pour vous aider à créer rapidement des applications web côté client interactives à l’aide [Backbone.js.](http://backbonejs.org/)

Le modèle fournit une structure initiale pour le développement d’une application Backbone.js dans ASP.NET MVC. Prêtes à l’emploi, il fournit les fonctionnalités de connexion utilisateur de base, y compris la réinitialisation de mot de passe d’abonnement, connectez-vous, l’utilisateur et la confirmation de l’utilisateur avec les modèles de messagerie de base.

Configuration requise :

- [Mise à jour ASP.NET et Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=282650)

## <a name="create-a-backbone-template-project"></a>Créer un projet de modèle principal

Téléchargez et installez le modèle en cliquant sur le bouton Télécharger ci-dessus. Le modèle est empaqueté comme un fichier d’Extension Visual Studio (VSIX). Vous devrez peut-être redémarrer Visual Studio.

Dans le **modèles** volet, sélectionnez **modèles installés** et développez le **Visual C#** nœud. Sous **Visual C#**, sélectionnez **Web**. Dans la liste des modèles de projet, sélectionnez **ASP.NET MVC 4 Web Application**. Nommez le projet et cliquez sur **OK**.

![](backbonejs-template/_static/image1.png)

Dans le **nouveau projet** Assistant, sélectionnez projet de SPA Backbone.js.

![](backbonejs-template/_static/image2.png)

Appuyez sur Ctrl-F5 pour générer et exécuter l’application sans débogage, ou appuyez sur F5 pour exécuter avec débogage.

![](backbonejs-template/_static/image3.png)

Cliquer sur « Mon compte » pour afficher la page de connexion :

![](backbonejs-template/_static/image4.png)

## <a name="walkthrough-client-code"></a>Procédure pas à pas : Le Code Client

Nous allons démarre avec le côté client. Les scripts d’application client se trouvent dans le dossier ~/Scripts/application. L’application est écrite [TypeScript](http://www.typescriptlang.org/) (fichiers .ts) qui sont compilés dans JavaScript (fichiers .js).

**Application**

`Application`est défini dans application.ts. Cet objet initialise l’application et agit comme l’espace de noms racine. Il gère les informations de configuration et l’état qui sont partagées entre l’application, telles que si l’utilisateur est connecté.

Le `application.start` méthode crée les affichages modaux et attache des gestionnaires d’événements pour les événements de niveau application, telles que l’authentification de l’utilisateur. Ensuite, il crée le routeur par défaut et vérifie si n’importe quelle URL côté client est spécifiée. Si non, il redirige vers l’url par défaut (#! /).

**Événements**

Les événements sont toujours importants lorsque développement faiblement couplés de composants. Applications effectuent souvent plusieurs opérations en réponse à une action de l’utilisateur. Backbone fournit des événements intégrés avec les composants, tels que le modèle, la Collection et afficher. Au lieu de créer des interdépendances entre ces composants, le modèle utilise un modèle « pub/sub » : le `events` objet, défini dans events.ts, agit comme un concentrateur d’événements pour la publication et de s’abonner aux événements d’application. Le `events` objet est un singleton. Le code suivant montre comment s’abonner à un événement, puis déclenche l’événement :

[!code-csharp[Main](backbonejs-template/samples/sample1.cs)]

**Routeur**

Dans Backbone.js, un routeur fournit des méthodes pour les pages du côté client de routage et en les connectant à des actions et des événements. Le modèle définit un seul routeur router.ts. Le routeur crée les vues activables et conserve l’état en changeant d’affichage. (Les vues activables sont décrites dans la section suivante). Au départ, le projet possède deux vues factices, particuliers et sur. Il possède également une vue NotFound qui s’affiche si l’itinéraire n’est pas connu.

**Vues**

Les vues sont définies dans ~/Scripts/application/vues. Il existe deux types de vues, les vues activables et les vues de la boîte de dialogue modale. Vues activables sont appelés par le routeur. Lorsqu’une vue activables est indiquée, toutes les autres vues activables devient inactives. Pour créer une vue activables, étendre l’affichage avec le `Activable` objet :

[!code-javascript[Main](backbonejs-template/samples/sample2.js)]

Extension avec `Activable` ajoute deux nouvelles méthodes à la vue, `activate` et `deactivate`. Le routeur appelle ces méthodes pour activer et désactiver l’affichage.

Affichages modaux sont implémentés en tant que [amorçage de Twitter](http://twitter.github.com/bootstrap/) boîtes de dialogue modales. Le `Membership` et `Profile` les affichages sont modales. Vues de modèle peuvent être appelées par les événements d’application. Par exemple, dans le `Navigation` , en cliquant sur le lien « Mon compte » affiche soit la `Membership` vue ou la `Profile` affichage, selon que l’utilisateur est connecté. Le `Navigation` attache cliquez sur les gestionnaires d’événements pour tous les éléments enfants qui ont le `data-command` attribut. Voici le code HTML :

[!code-html[Main](backbonejs-template/samples/sample3.html)]

Voici le code dans navigation.ts pour raccorder des événements :

[!code-csharp[Main](backbonejs-template/samples/sample4.cs)]

**Modèles**

Les modèles sont définis dans les modèles/~/Scripts/application. Les modèles ont tous trois opérations de base : par défaut des attributs, des règles de validation et un point de terminaison côté serveur. Voici un exemple typique :

[!code-javascript[Main](backbonejs-template/samples/sample5.js)]

**Plug-ins**

Le dossier ~/Scripts/application/lib contient plusieurs plug-ins de jQuery pratique. Le fichier form.ts définit un plug-in pour l’utilisation des données de formulaire. Vous devez souvent sérialiser ou désérialiser des données de formulaire et afficher des erreurs de validation de modèle. Le plug-in de form.ts a des méthodes telles que `serializeFields`, `deserializeFields`, et `showFieldErrors`. L’exemple suivant montre comment sérialiser un formulaire à un modèle.

[!code-javascript[Main](backbonejs-template/samples/sample6.js)]

Le plug-in de flashbar.ts fournit différents types de messages de commentaire à l’utilisateur. Les méthodes sont `$.showSuccessbar`, `$.showErrorbar` et `$.showInfobar`. En arrière-plan, il utilise les alertes de programme d’amorçage Twitter pour afficher les messages correctement animés.

Le plug-in de confirm.ts remplace le navigateur confirmer la boîte de dialogue, bien que l’API est quelque peu différent :

[!code-javascript[Main](backbonejs-template/samples/sample7.js)]

## <a name="walkthrough-server-code"></a>Procédure pas à pas : Code serveur

Maintenant examinons côté serveur.

**Contrôleurs**

Dans une application à page unique, le serveur ne joue qu’un rôle limité dans l’interface utilisateur. En règle générale, le serveur restitue la page initiale envoie et reçoit des données JSON.

Le modèle a deux contrôleurs MVC : `HomeController` restitue la page initiale, et `SupportsController` sert à confirmer les nouveaux comptes d’utilisateur et de réinitialiser les mots de passe. Tous les autres contrôleurs dans le modèle sont des contrôleurs de l’API Web ASP.NET, qui envoient et reçoivent des données JSON. Par défaut, les contrôleurs utilisent la nouvelle `WebSecurity` classe pour effectuer des tâches relatives à l’utilisateur. Toutefois, ils ont également des constructeurs facultatifs qui vous permettent de passer dans les délégués pour ces tâches. Cela facilite les tests et vous permet de remplacer `WebSecurity` par autre chose, à l’aide d’un conteneur inversion de contrôle. Voici un exemple :

[!code-csharp[Main](backbonejs-template/samples/sample8.cs)]

## <a name="views"></a>Affichages

Les vues sont conçues pour être modulaire : chaque section de la page possède sa propre vue dédié. Dans une application à page unique, il est courant pour inclure des vues qui n’ont pas de n’importe quel contrôleur correspondant. Vous pouvez inclure une vue en appelant `@Html.Partial('myView')`, mais cela est fastidieux. Pour faciliter cette opération, le modèle définit une méthode d’assistance, `IncludeClientViews`, qui affiche toutes les vues dans un dossier spécifié :

[!code-cshtml[Main](backbonejs-template/samples/sample9.cshtml)]

Si le nom du dossier n’est pas spécifié, le nom de dossier par défaut est « ClientViews ». Si votre vue du client utilise également les vues partielles, un nom de la vue partielle avec un caractère de soulignement (par exemple, `_SignUp`). Le `IncludeClientViews` méthode exclut toutes les vues dont le nom commence par un trait de soulignement. Pour inclure une vue partielle dans la vue du client, appelez `Html.ClientView('SignUp')` au lieu de `Html.Partial('_SignUp')`.

**Envoi de courrier électronique**

Pour envoyer un courrier électronique, le modèle utilise [Postal](http://aboutcode.net/postal). Toutefois, il est abstrait Postal du reste du code avec la `IMailer` interface, vous pouvez facilement remplacer par une autre implémentation. Les modèles de messagerie se trouvent dans le dossier vues/des messages électroniques. Adresse de messagerie de l’expéditeur est spécifié dans le fichier web.config, dans le `sender.email` clé de la **appSettings** section. Également, lorsque `debug="true"` dans le fichier web.config, l’application ne nécessite pas de confirmation par courrier électronique de l’utilisateur, pour accélérer le développement.

## <a name="github"></a>GitHub

Vous trouverez également le modèle Backbone.js SPA sur [GitHub](https://github.com/kazimanzurrashid/AspNetMvcBackboneJsSpa).
