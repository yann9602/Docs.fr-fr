---
uid: single-page-application/overview/templates/emberjs-template
title: "Modèle de EmberJS | Documents Microsoft"
author: xqiu
description: "Modèle de EmberJS"
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/30/2013
ms.topic: article
ms.assetid: 04d5f142-5f62-494a-b5ea-4f3d068d34cb
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /single-page-application/overview/templates/emberjs-template
msc.type: authoredcontent
ms.openlocfilehash: 1fb7633aee288be648d4f9681b43c8911b7dbab9
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="emberjs-template"></a>Modèle de EmberJS
====================
par [Xinyang Qiu](https://github.com/xqiu)

> Le modèle MVC EmberJS est rédigé par Nathan Totten, Thiago Santos et Xinyang Qiu.
> 
> [Téléchargez le modèle MVC de EmberJS](https://go.microsoft.com/fwlink/?LinkId=282647)


Le modèle EmberJS SPA est conçu pour vous aider à créer rapidement des applications web côté client interactives à l’aide de EmberJS.

« Application à page unique » (SPA) est le terme général pour une application web qui charge une page HTML unique, puis met à jour la page dynamiquement, au lieu de charger les nouvelles pages. Après le chargement de page initial, SPA communique avec le serveur via les requêtes AJAX.

![](emberjs-template/_static/image1.png)

AJAX n’est pas nouveau, mais aujourd'hui des infrastructures JavaScript qui le rendent plus facile créer et gérer une grande application SPA sophistiquée. En outre, HTML 5 et CSS3 sont qu’il soit plus facile de créer des interfaces utilisateur riches.

Le modèle de SPA EmberJS utilise le [Ember](http://emberjs.com/) bibliothèque JavaScript pour gérer les mises à jour de la page à partir de requêtes AJAX. Ember.js utilise la liaison de données pour synchroniser la page avec les dernières données. De cette façon, il est inutile d’écrire du code qui vous guide à travers les données JSON et met à jour le modèle DOM. Au lieu de cela, vous placez des attributs déclaratifs dans le code HTML qui indiquent Ember.js comment présenter les données.

Côté serveur, le modèle EmberJS est presque identique à la [modèle de KnockoutJS SPA](../introduction/knockoutjs-template.md). Il utilise ASP.NET MVC à répondre à des documents HTML et ASP.NET Web API pour gérer les demandes du client AJAX. Pour plus d’informations sur les aspects du modèle, reportez-vous à la [KnockoutJS modèle](../introduction/knockoutjs-template.md) documentation. Cette rubrique se concentre sur les différences entre le modèle de Knockout et le modèle EmberJS.

## <a name="create-an-emberjs-spa-template-project"></a>Créer un projet de modèle EmberJS SPA

Téléchargez et installez le modèle en cliquant sur le bouton Télécharger ci-dessus. Vous devrez peut-être redémarrer Visual Studio.

Dans le **modèles** volet, sélectionnez **modèles installés** et développez le **Visual C#** nœud. Sous **Visual C#**, sélectionnez **Web**. Dans la liste des modèles de projet, sélectionnez **ASP.NET MVC 4 Web Application**. Nommez le projet et cliquez sur **OK**.

![](emberjs-template/_static/image2.png)

Dans le **nouveau projet** Assistant, sélectionnez **Ember.js SPA projet**.

![](emberjs-template/_static/image4.png)

## <a name="emberjs-spa-template-overview"></a>Vue d’ensemble du modèle EmberJS SPA

Le modèle EmberJS utilise une combinaison de jQuery, Ember.js, Handlebars.js pour créer une interface utilisateur interactive lisse.

Ember.js est une bibliothèque JavaScript qui utilise un modèle MVC côté client.

- A *modèle*écrit dans le langage de création de modèles guidons, décrit l’interface utilisateur. En mode de mise en production, le [compilateur de guidons](https://github.com/Myslik/csharp-ember-handlebars) sert à regrouper et compiler le modèle guidons.
- A *modèle* stocke les données d’application qu’il obtient à partir du serveur (listes de tâches et éléments de tâche).
- A *contrôleur* stocke l’état de l’application. Contrôleurs présentent souvent des données de modèle pour les modèles correspondants.
- A *vue* traduit primitifs événements de l’application et transmet ces au contrôleur.
- A *routeur* gère l’état de l’application, synchronisation des URL et des modèles.

En outre, la bibliothèque les données peut être utilisée pour synchroniser des objets JSON (obtenus à partir du serveur via une API RESTful) et les modèles de client.

Le modèle EmberJS SPA organise les scripts huit couches :

- WebAPI\_adapter.js, webapi\_serializer.js : étend la bibliothèque les données pour travailler avec l’API Web ASP.NET.
- Scripts/Helpers.js : Définit les nouveaux programmes d’assistance les guidons.
- Scripts/App.js : Crée l’application et configure l’adaptateur et le sérialiseur.
- Scripts/application/modèles/\*.js : définit les modèles.
- Affichages/app/scripts/\*.js : définit les affichages.
- Scripts/application/contrôleurs/\*.js : définit les contrôleurs.
- Scripts/application/itinéraires, Scripts/app/router.js : Définit les itinéraires.
- Modèles /\*.hbs : définit les modèles guidons.

Examinons quelques-unes de ces scripts plus en détail.

## <a name="models"></a>Modèles

Les modèles sont définis dans le dossier Scripts/application/modèles. Il existe deux fichiers de modèle : todoItem.js et todoList.js.

**TODO.Model.js** définit les modèles côté client (navigateur) pour les listes de tâches. Il existe deux classes de modèle : todoItem et liste des tâches. Dans Ember, les modèles sont des sous-classes de service d’annuaire. Modèle. Un modèle peut avoir des propriétés avec des attributs :

[!code-javascript[Main](emberjs-template/samples/sample1.js)]

Les modèles peuvent définir des relations à d’autres modèles :

[!code-css[Main](emberjs-template/samples/sample2.css)]

Les modèles peuvent avoir calculée propriétés lier à d’autres propriétés :

[!code-javascript[Main](emberjs-template/samples/sample3.js)]

Les modèles peuvent avoir des fonctions Observateur, qui sont appelées lorsqu’une propriété observée change :

[!code-javascript[Main](emberjs-template/samples/sample4.js)]

## <a name="views"></a>Affichages

Les vues sont définies dans le dossier Scripts/application/vues. Une vue se traduit par des événements à partir de l’interface utilisateur de l’application. Un gestionnaire d’événements peut rappeler à certaines fonctions, ou simplement appeler directement le contexte de données.

Par exemple, le code suivant provient de views/TodoItemEditView.js. Il définit la gestion des événements pour un champ de texte d’entrée.

[!code-javascript[Main](emberjs-template/samples/sample5.js)]

## <a name="controller"></a>Contrôleur

Les contrôleurs sont définis dans le dossier Scripts/application/contrôleurs. Pour représenter un seul modèle, étendre `Ember.ObjectController`:

[!code-javascript[Main](emberjs-template/samples/sample6.js)]

Un contrôleur peut également représenter une collection de modèles en étendant `Ember.ArrayController`. Par exemple, le TodoListController représente un tableau de `todoList` objets. Le contrôleur de trie par ID de la liste des tâches, dans l’ordre décroissant :

[!code-javascript[Main](emberjs-template/samples/sample7.js)]

Le contrôleur définit une fonction nommée `addTodoList`, qui crée un nouveau todoList et l’ajoute au groupe. Pour voir comment cette fonction est appelée, ouvrez le fichier modèle nommé todoListTemplate.html, dans le dossier de modèles. Le code de modèle suivant lie un bouton à la `addTodoList` (fonction) :

[!code-html[Main](emberjs-template/samples/sample8.html)]

Le contrôleur contient également un `error` propriété, qui contient un message d’erreur. Voici le code du modèle pour afficher le message d’erreur (également dans todoListTemplate.html) :

[!code-html[Main](emberjs-template/samples/sample9.html)]

## <a name="routes"></a>Itinéraires

Router.js définit les itinéraires et le modèle par défaut à afficher, de configurer l’état de l’application et correspond à des URL pour les itinéraires :

[!code-javascript[Main](emberjs-template/samples/sample10.js)]

TodoListRoute.js charge les données pour le TodoListRoute en substituant la fonction setupController :

[!code-javascript[Main](emberjs-template/samples/sample11.js)]

Ember utilise les conventions d’affectation de noms pour faire correspondre les URL, les noms d’itinéraires, les contrôleurs et les modèles. Pour plus d’informations, consultez [http://emberjs.com/guides/routing/defining-your-routes/](http://emberjs.com/guides/routing/defining-your-routes/) à la documentation EmberJS.

## <a name="templates"></a>Modèles

Le dossier de modèles contient quatre modèles :

- application.HBS : le modèle par défaut qui est affiché quand l’application est démarrée.
- About.HBS : le modèle pour l’itinéraire « / environ ».
- index.HBS : le modèle à la racine de l’itinéraire « / ».
- todoList.hbs : le modèle pour le « / todo « itinéraire.
- \_navbar.HBS : le modèle définit le menu de navigation.

Le modèle d’application se comporte comme une page maître. Il contient un en-tête, un pied de page et une « {{prise}} » pour insérer d’autres modèles dans en fonction de l’itinéraire. Pour plus d’informations sur les modèles d’application dans Ember, consultez [http://guides.emberjs.com/v1.10.0/templates/the-application-template//](http://guides.emberjs.com/v1.10.0/templates/the-application-template/).

Le « / todoList « modèle contient deux expressions de boucle. La boucle externe est `{{#each controller}}`et la boucle est `{{#each todos}}`. Le code suivant montre un intégré `Ember.Checkbox` afficher un texte personnalisé `App.TodoItemEditView`, ainsi qu’un lien avec un `deleteTodo` action.

[!code-html[Main](emberjs-template/samples/sample12.html)]

Le `HtmlHelperExtensions` (classe), défini dans Controllers/HtmlHelperExensions.cs, définit un programme d’assistance afin de mettre en cache et insérer le modèle de fichiers **déboguer** a la valeur **true** dans le fichier Web.config. Cette fonction est appelée à partir du fichier de vue ASP.NET MVC défini dans Views/Home/App.cshtml :

[!code-cshtml[Main](emberjs-template/samples/sample13.cshtml)]

Appelée sans argument, la fonction affiche tous les fichiers de modèle dans le dossier de modèles. Vous pouvez également spécifier un sous-dossier ou un fichier de modèle spécifique.

Lorsque **déboguer** est **false** dans le fichier Web.config, l’application inclut l’élément de lot « ~/bundles/templates ». Cet élément de groupe est ajouté au BundleConfig.cs, à l’aide de la bibliothèque du compilateur guidons :

[!code-csharp[Main](emberjs-template/samples/sample14.cs)]
