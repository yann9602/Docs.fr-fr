---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-6
title: "Créer le Client JavaScript | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/16/2014
ms.topic: article
ms.assetid: 20360326-b123-4b1e-abae-1d350edf4ce4
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-6
msc.type: authoredcontent
ms.openlocfilehash: b397c5a413ae213c9b79da1c0e0626efe21c7e21
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="create-the-javascript-client"></a>Créer le Client JavaScript
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](https://github.com/MikeWasson/BookService)

Dans cette section, vous allez créer le client pour l’application, à l’aide de HTML, JavaScript et le [Knockout.js](http://knockoutjs.com/) bibliothèque. Nous allons construire l’application cliente en étapes :

- Affichage d’une liste de livres.
- Affiche un détail de livre.
- Ajout d’un nouveau livre.

La bibliothèque de Knockout utilise le modèle Model-View-ViewModel (MVVM) :

- Le **modèle** est la représentation sous forme de côté serveur des données dans le domaine d’entreprise (dans notre cas, la documentation et d’auteurs).
- Le **vue** est la couche de présentation (HTML).
- Le **le modèle de vue** est un objet JavaScript qui contient les modèles. Le modèle d’affichage est une abstraction de code de l’interface utilisateur. Il n’a aucune connaissance de la représentation sous forme de code HTML. Au lieu de cela, il représente fonctionnalités abstraites de la vue, telles que &quot;une liste de livres&quot;.

La vue est lié aux données pour le modèle d’affichage. Mises à jour le modèle d’affichage sont automatiquement reflétées dans la vue. Le modèle d’affichage obtient également les événements à partir de la vue, tels que les clics de bouton.

![](part-6/_static/image1.png)

Cette approche facilite modifier la disposition et l’interface utilisateur de votre application, comme vous pouvez modifier les liaisons, sans réécrire le code. Par exemple, vous pouvez afficher une liste d’éléments en tant qu’un `<ul>`, puis le modifier ultérieurement à une table.

## <a name="add-the-knockout-library"></a>Ajoutez la bibliothèque Knockout

Dans Visual Studio, à partir de la **outils** menu, sélectionnez **Gestionnaire de Package de bibliothèque**. Puis sélectionnez **Package Manager Console**. Dans la fenêtre de Console du Gestionnaire de Package, entrez la commande suivante :

[!code-console[Main](part-6/samples/sample1.cmd)]

Cette commande ajoute les fichiers Knockout dans le dossier Scripts.

## <a name="create-the-view-model"></a>Créer le modèle d’affichage

Ajouter un fichier JavaScript nommé app.js dans le dossier Scripts. (Dans l’Explorateur de solutions, cliquez sur le dossier Scripts, sélectionnez **ajouter**, puis sélectionnez **fichier JavaScript**.) Collez le code suivant :

[!code-javascript[Main](part-6/samples/sample2.js)]

Dans Knockout, la `observable` classe permet la liaison de données. Lorsque le contenu d’un observable change, l’observable avertit tous les contrôles liés aux données, ils peuvent mettre à jour eux-mêmes. (Le `observableArray` classe est la version du tableau de *observable*.) Pour commencer, notre modèle de vue possède deux observables :

- `books`contient la liste de livres.
- `error`contient un message d’erreur si un appel AJAX échoue.

Le `getAllBooks` méthode effectue un appel AJAX pour obtenir la liste de livres. Puis il exécute un push du résultat dans la `books` tableau.

Le `ko.applyBindings` méthode fait partie de la bibliothèque de masquage. Il prend le modèle d’affichage en tant que paramètre et configure la liaison de données.

## <a name="add-a-script-bundle"></a>Ajouter un regroupement de Script

Regroupement est une fonctionnalité de ASP.NET 4.5 qui vous permettent de combiner ou de regrouper plusieurs fichiers dans un seul fichier. Regroupement permet de réduire le nombre de demandes au serveur, ce qui peut améliorer les temps de chargement de page.

Ouvrez le fichier App\_Start/BundleConfig.cs. Ajoutez le code suivant à la méthode RegisterBundles.

[!code-csharp[Main](part-6/samples/sample3.cs)]

>[!div class="step-by-step"]
[Précédent](part-5.md)
[Suivant](part-7.md)
