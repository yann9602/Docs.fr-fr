---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-4
title: "La gestion des Relations d’entité | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/16/2014
ms.topic: article
ms.assetid: d2f5710c-23c7-40a5-9cd9-5d0516570cba
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-4
msc.type: authoredcontent
ms.openlocfilehash: 58a9dfb621630f23b37247b96ed3a19a661857f1
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="handling-entity-relations"></a>Gestion des Relations d’entité
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](https://github.com/MikeWasson/BookService)

Cette section décrit certains détails de comment EF charge les entités associées et comment gérer les propriétés de navigation circulaires dans vos classes de modèle. (Cette section fournit des connaissances générales et n’est pas nécessaire pour effectuer ce didacticiel. Si vous préférez, passez à le [partie 5.](part-5.md).)

## <a name="eager-loading-versus-lazy-loading"></a>Eager chargement par rapport à chargement différé

Lorsque vous utilisez EF avec une base de données relationnelle, il est important de comprendre comment EF charge les données associées.

Il est également utile afficher les requêtes SQL qui génère d’EF. Pour tracer le code SQL, ajoutez la ligne suivante de code pour le `BookServiceContext` constructeur :

[!code-csharp[Main](part-4/samples/sample1.cs)]

Si vous envoyez une requête GET à /api/books, il retourne un JSON comme suit :

[!code-console[Main](part-4/samples/sample2.cmd)]

Vous pouvez voir que la propriété Author est null, même si le catalogue contient une AuthorId valide. Est que EF ne charge pas les entités auteur associées. Confirme le journal de suivi de la requête SQL :

[!code-console[Main](part-4/samples/sample3.sql)]

L’instruction SELECT prend à partir de la table de la documentation et ne fait pas référence à la table de l’auteur.

Pour référence, voici la méthode dans la `BooksController` classe qui retourne la liste de livres.

[!code-csharp[Main](part-4/samples/sample4.cs)]

Voyons comment nous pouvons retourner l’auteur en tant que partie des données JSON. Il existe trois façons de charger des données associées dans Entity Framework : chargement hâtif, chargement différé et chargement explicit. Il existe des compromis avec chaque technique, il est donc important de comprendre comment elles fonctionnent.

### <a name="eager-loading"></a>Chargement hâtif

Avec *chargement hâtif*, EF charge les entités associées dans le cadre de la requête de base de données initiale. Pour effectuer un chargement hâtif, utilisez le **System.Data.Entity.Include** méthode d’extension.

[!code-csharp[Main](part-4/samples/sample5.cs)]

Cela indique à EF pour inclure les données de l’auteur dans la requête. Si vous apportez cette modification, et exécuter l’application, les données JSON ressemblent à ceci :

[!code-console[Main](part-4/samples/sample6.cmd)]

Le journal de suivi montre que EF effectuée une jointure sur les tables de livre et l’auteur.

[!code-console[Main](part-4/samples/sample7.cmd)]

### <a name="lazy-loading"></a>Chargement différé

Chargement différé, EF automatiquement charge une entité associée lors de la propriété de navigation pour cette entité est déréférencée. Pour activer le chargement différé, vérifiez la propriété de navigation virtuel. Par exemple, dans la classe Book :

[!code-csharp[Main](part-4/samples/sample8.cs?highlight=6)]

Examinons à présent le code suivant :

[!code-csharp[Main](part-4/samples/sample9.cs)]

Lorsque le chargement différé est activé, l’accès à la `Author` propriété `books[0]` provoque EF interroger la base de données de l’auteur.

Chargement différé requiert plusieurs allers-retours de base de données, étant donné que EF envoie une requête chaque fois qu’il récupère une entité associée. En règle générale, vous souhaitez désactivé pour les objets que vous sérialisez de chargement différé. Le sérialiseur doit lire toutes les propriétés sur le modèle, ce qui déclenche le chargement des entités associées. Par exemple, voici les requêtes SQL lorsque EF sérialise la liste des livres avec chargement différé activé. Vous pouvez voir que EF effectue trois requêtes distinctes pour les auteurs de trois.

[!code-console[Main](part-4/samples/sample10.sql)]

Il existe toujours une fois lorsque vous pouvez souhaiter utiliser le chargement différé. Chargement hâtif risque EF générer une jointure très complexe. Ou vous devrez peut-être les entités associées pour un petit sous-ensemble des données, et le chargement tardif serait plus efficace.

Une façon d’éviter les problèmes de sérialisation doit sérialiser des objets de transfert de données (DTO) au lieu d’objets d’entité. Je vais expliquer cette approche plus loin dans l’article.

### <a name="explicit-loading"></a>Chargement explicite

Chargement explicite est similaire à chargement différé, sauf que vous explicitement les données associées dans le code ; Il ne se produit automatiquement lorsque vous accédez à une propriété de navigation. Chargement explicite vous donne davantage de contrôle sur quand charger des données connexes, mais nécessite du code supplémentaire. Pour plus d’informations sur le chargement explicite, consultez [le chargement des entités associées](https://msdn.microsoft.com/data/jj574232#explicit).

## <a name="navigation-properties-and-circular-references"></a>Propriétés de navigation et les références circulaires

Lorsque j’ai défini les modèles de livre et auteur, j’ai défini une propriété de navigation sur le `Book` classe pour la relation de l’auteur du livre, mais vous n’avez pas défini une propriété de navigation dans l’autre direction.

Que se passe-t-il si vous ajoutez la propriété de navigation correspondant à la `Author` classe ?

[!code-csharp[Main](part-4/samples/sample11.cs?highlight=7)]

Malheureusement, ceci pose un problème lorsque vous sérialisez les modèles. Si vous chargez les données associées, il crée un graphique d’objet circulaire.

![](part-4/_static/image1.png)

Lorsque le module de formatage JSON ou XML tente de sérialiser le graphique, elle lève une exception. Les deux formateurs lever les messages d’exception différent. Voici un exemple pour le formateur JSON :

[!code-console[Main](part-4/samples/sample12.cmd)]

Voici le formateur XML :

[!code-xml[Main](part-4/samples/sample13.xml)]

Une solution consiste à utiliser les DTO, je décris dans la section suivante. Vous pouvez également configurer les formateurs JSON et XML pour gérer les cycles de graphique. Pour plus d’informations, consultez [la gestion des références d’objet circulaires](../../formats-and-model-binding/json-and-xml-serialization.md#handling_circular_object_references).

Pour ce didacticiel, vous n’avez pas besoin du `Author.Book` propriété de navigation, donc vous pouvez la laisser.

>[!div class="step-by-step"]
[Précédent](part-3.md)
[Suivant](part-5.md)
