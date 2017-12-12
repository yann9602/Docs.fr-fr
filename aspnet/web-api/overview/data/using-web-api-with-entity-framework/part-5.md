---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-5
title: "Créer des objets de transfert de données (DTO) | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/16/2014
ms.topic: article
ms.assetid: 0fd07176-b74b-48f0-9fac-0f02e3ffa213
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-5
msc.type: authoredcontent
ms.openlocfilehash: e179dcd52200734a45c84b3c7c3069a4727904c1
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="create-data-transfer-objects-dtos"></a>Créer des objets de transfert de données (DTO)
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](https://github.com/MikeWasson/BookService)

À droite, nos API web expose désormais les entités de base de données au client. Le client reçoit des données qui est directement mappé à votre base de données. Toutefois, qui n’est pas toujours une bonne idée. Parfois, vous souhaitez modifier la forme des données que vous envoyez au client. Vous pouvez, par exemple, décider d'effectuer les opérations suivantes :

- Supprimez les références circulaires (voir la section précédente).
- Masquer des propriétés particulières qui les clients ne sont pas supposés à afficher.
- Omettre certaines propriétés afin de réduire la taille de la charge.
- Aplanir des graphiques d’objets qui contiennent des objets imbriqués, pour les rendre plus pratique pour les clients.
- Éviter de « trop de valider « vulnérabilités. (Consultez [Validation du modèle](../../formats-and-model-binding/model-validation-in-aspnet-web-api.md) pour une présentation de validation excessive.)
- Dissocier votre couche de service à partir de votre couche de base de données.

Pour ce faire, vous pouvez définir un *objet de transfert de données* (DTO). Un DTO est un objet qui définit comment les données seront être envoyées sur le réseau. Nous allons voir comment cela fonctionne avec l’entité Book. Dans le dossier de modèles, ajoutez deux classes DTO :

[!code-csharp[Main](part-5/samples/sample1.cs)]

Le `BookDetailDTO` classe inclut toutes les propriétés du modèle proposé, à ceci près que `AuthorName` est une chaîne qui contienne le nom de l’auteur. Le `BookDTO` classe contient un sous-ensemble de propriétés à partir de `BookDetailDTO`.

Ensuite, remplacez les deux méthodes GET dans le `BooksController` (classe), avec les versions qui retournent des données. Nous allons utiliser LINQ **sélectionnez** instruction pour convertir à partir des entités livre DTO.

[!code-csharp[Main](part-5/samples/sample2.cs)]

Voici le code SQL généré par la nouvelle `GetBooks` (méthode). Vous pouvez voir que EF traduit LINQ **sélectionnez** dans une instruction SQL SELECT.

[!code-sql[Main](part-5/samples/sample3.sql)]

Enfin, modifiez le `PostBook` DTO de retour de méthode.

[!code-csharp[Main](part-5/samples/sample4.cs)]

> [!NOTE]
> Dans ce didacticiel, nous allons convertir DTO manuellement dans le code. Une autre option consiste à utiliser une bibliothèque, par exemple [AutoMapper](http://automapper.org/) qui gère la conversion automatique.

>[!div class="step-by-step"]
[Précédent](part-4.md)
[Suivant](part-6.md)
