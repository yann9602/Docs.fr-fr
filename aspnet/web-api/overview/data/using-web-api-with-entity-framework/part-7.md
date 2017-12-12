---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-7
title: "Création de la vue (UI) | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/16/2014
ms.topic: article
ms.assetid: b2445062-a1fe-4133-8994-f510280f6d9a
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-7
msc.type: authoredcontent
ms.openlocfilehash: 8c5cc662e2e3c9cb07ca9e30ff57eb084d58e1bb
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="create-the-view-ui"></a>Création de la vue (UI)
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](https://github.com/MikeWasson/BookService)

Dans cette section, vous commencerez à définir le code HTML pour l’application et ajouter une liaison de données entre le code HTML et le modèle d’affichage.

Ouvrez le fichier Views/Home/Index.cshtml. Remplacez tout le contenu de ce fichier avec les éléments suivants.

[!code-cshtml[Main](part-7/samples/sample1.cshtml)]

La plupart de la `div` sont les éléments de [Bootstrap](http://getbootstrap.com/) styles. Les éléments importants sont ceux avec `data-bind` attributs. Cet attribut lie le code HTML pour le modèle d’affichage.

Exemple :

[!code-html[Main](part-7/samples/sample2.html)]

Dans cet exemple, le &quot; `text` &quot; liaison provoque la `<p>` élément pour afficher la valeur de la `error` propriété à partir du modèle de vue. N’oubliez pas que `error` a été déclaré comme un `ko.observable`:

[!code-javascript[Main](part-7/samples/sample3.js)]

Chaque fois qu’une nouvelle valeur est attribuée à `error`, Knockout met à jour le texte dans le `<p>` élément.

Le `foreach` liaison indique Knockout pour parcourir le contenu de le `books` tableau. Pour chaque élément du tableau, Knockout crée un &lt;li&gt; élément. Les liaisons dans le contexte de la `foreach` font référence aux propriétés sur l’élément de tableau. Exemple :

[!code-html[Main](part-7/samples/sample4.html)]

Ici le `text` liaison lit la propriété Author de chaque ouvrage.

Si vous exécutez l’application maintenant, il doit ressembler à ceci :

![](part-7/_static/image1.png)

La liste des livres charge en mode asynchrone, une fois le chargement de la page. Actuellement, le &quot;détails&quot; liens ne sont pas fonctionnels. Nous allons ajouter cette fonctionnalité dans la section suivante.

>[!div class="step-by-step"]
[Précédent](part-6.md)
[Suivant](part-8.md)
