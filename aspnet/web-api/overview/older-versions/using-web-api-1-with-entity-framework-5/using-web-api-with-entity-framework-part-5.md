---
uid: web-api/overview/older-versions/using-web-api-1-with-entity-framework-5/using-web-api-with-entity-framework-part-5
title: "Partie 5 : Création d’une interface utilisateur dynamique avec Knockout.js | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/04/2012
ms.topic: article
ms.assetid: 9d9cb3b0-f4a7-434e-a508-9fc0ad0eb813
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/older-versions/using-web-api-1-with-entity-framework-5/using-web-api-with-entity-framework-part-5
msc.type: authoredcontent
ms.openlocfilehash: 20ebdb1b8ba710e0fbc6040f7cd4064b44658c53
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-5-creating-a-dynamic-ui-with-knockoutjs"></a>Partie 5 : Création d’une interface utilisateur dynamique avec Knockout.js
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/ASP-NET-Web-API-with-afa30545)

## <a name="creating-a-dynamic-ui-with-knockoutjs"></a>Création d’une interface utilisateur dynamique avec Knockout.js

Dans cette section, nous allons utiliser Knockout.js pour ajouter des fonctionnalités à la vue administration.

[Knockout.js](http://knockoutjs.com/) est une bibliothèque Javascript qui vous permettent de lier des contrôles HTML à des données. Knockout.js utilise le modèle Model-View-ViewModel (MVVM).

- Le *modèle* est la représentation sous forme de côté serveur des données dans le domaine d’entreprise (dans notre cas, les produits et les commandes).
- Le *vue* est la couche de présentation (HTML).
- Le *-modèle d’affichage* est un objet Javascript qui contient les données de modèle. Le modèle d’affichage est une abstraction de code de l’interface utilisateur. Il n’a aucune connaissance de la représentation sous forme de code HTML. Au lieu de cela, il représente les fonctionnalités abstraites de la vue, telles que « il s’agit d’une liste d’éléments ».

La vue est lié aux données pour le modèle d’affichage. Mises à jour le modèle d’affichage sont automatiquement reflétées dans la vue. Le modèle d’affichage également Obtient les événements à partir de la vue, telles que des clics de bouton et effectue des opérations sur le modèle, telles que la création d’une commande.

![](using-web-api-with-entity-framework-part-5/_static/image1.png)

Tout d’abord, nous allons définir le modèle d’affichage. Après cela, nous liera le balisage HTML pour le modèle d’affichage.

Ajoutez la section suivante de Razor à Admin.cshtml :

[!code-cshtml[Main](using-web-api-with-entity-framework-part-5/samples/sample1.cshtml)]

Vous pouvez ajouter cette section n’importe où dans le fichier. Lorsque la vue est restituée, la section apparaît au bas de la page HTML, avec le bouton droit avant la fermeture &lt;/corps&gt; balise.

Tous le script de cette page seront envoyé à l’intérieur de la balise de script indiquée par le commentaire :

[!code-html[Main](using-web-api-with-entity-framework-part-5/samples/sample2.html)]

Tout d’abord, définissez une classe de modèle d’affichage :

[!code-javascript[Main](using-web-api-with-entity-framework-part-5/samples/sample3.js)]

**ko.observableArray** est un type spécial d’objet de Knockout, appelé un *observable*. À partir de la [Knockout.js documentation](http://knockoutjs.com/documentation/observables.html): observable est un « objet JavaScript qui peut notifier les abonnés à propos des modifications. » Lorsque le contenu d’un observable change, la vue est automatiquement mis à jour.

Pour remplir le `products` de tableau, effectuer une requête AJAX à l’API web. Rappelez-vous que nous avons stocké l’URI de base pour l’API dans le sac d’affichage (voir [partie 4](using-web-api-with-entity-framework-part-4.md) du didacticiel).

[!code-javascript[Main](using-web-api-with-entity-framework-part-5/samples/sample4.js?highlight=5)]

Ensuite, ajoutez des fonctions pour le modèle d’affichage pour créer, mettre à jour et supprimer des produits. Ces fonctions soumettre les appels à l’API web AJAX et utilisent les résultats pour mettre à jour le modèle d’affichage.

[!code-javascript[Main](using-web-api-with-entity-framework-part-5/samples/sample5.js?highlight=7)]

Maintenant la partie la plus importante : lorsque le DOM est fulled appel chargé, le **ko.applyBindings** de fonction et le passer à une nouvelle instance de la `ProductsViewModel`:

[!code-javascript[Main](using-web-api-with-entity-framework-part-5/samples/sample6.js)]

Le **ko.applyBindings** méthode active Knockout et relie le modèle d’affichage à la vue.

Maintenant que nous avons un modèle d’affichage, nous pouvons créer les liaisons. Dans Knockout.js, cela en ajoutant `data-bind` attributs aux éléments HTML. Par exemple, pour lier une liste HTML à un tableau, utilisez la `foreach` liaison :

[!code-html[Main](using-web-api-with-entity-framework-part-5/samples/sample7.html?highlight=1)]

Le `foreach` liaison effectue une itération au sein du tableau et crée des éléments pour chaque objet enfant dans le tableau. Liaisons sur les éléments enfants peuvent faire référence aux propriétés sur les objets de tableau.

Ajoutez les liaisons suivantes à la liste « produits mis à jour » :

[!code-html[Main](using-web-api-with-entity-framework-part-5/samples/sample8.html)]

Le `<li>` élément se produit dans l’étendue de la **foreach** liaison. Que signifie va de Knockout restitue l’élément une fois pour chaque produit dans le `products` tableau. Toutes les liaisons dans le `<li>` élément faire référence à cette instance de produit. Par exemple, `$data.Name` fait référence à la `Name` propriété sur le produit.

Pour définir les valeurs des entrées de texte, utilisez la `value` liaison. Les boutons sont liés aux fonctions de la vue du modèle, à l’aide de la `click` liaison. L’instance de produit est passé en tant que paramètre à chaque fonction. Pour plus d’informations, le [Knockout.js documentation](http://knockoutjs.com/documentation/observables.html) a une bonne descriptions des liaisons différentes.

Ensuite, ajoutez une liaison pour le **envoyer** événements sur le formulaire Ajouter des produits :

[!code-html[Main](using-web-api-with-entity-framework-part-5/samples/sample9.html)]

Cette liaison appelle la `create` fonction sur le modèle d’affichage pour créer un nouveau produit.

Voici le code complet pour la vue de l’administrateur :

[!code-cshtml[Main](using-web-api-with-entity-framework-part-5/samples/sample10.cshtml)]

Exécutez l’application, connectez-vous avec le compte d’administrateur, cliquez sur le lien « Admin ». Vous devez voir la liste des produits et être en mesure de créer, mettre à jour ou supprimer des produits.

>[!div class="step-by-step"]
[Précédent](using-web-api-with-entity-framework-part-4.md)
[Suivant](using-web-api-with-entity-framework-part-6.md)
