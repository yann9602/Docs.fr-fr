---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v3/calling-an-odata-service-from-a-net-client
title: "Appel à un Service OData à partir d’un Client .NET (c#) | Documents Microsoft"
author: MikeWasson
description: "Ce didacticiel montre comment appeler un service OData à partir d’une application cliente c#. Versions des logiciels utilisées dans le didacticiel Visual Studio 2013 (fonctionne avec Visual S..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/26/2014
ms.topic: article
ms.assetid: 6f448917-ad23-4dcc-9789-897fad74051b
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v3/calling-an-odata-service-from-a-net-client
msc.type: authoredcontent
ms.openlocfilehash: 497102cfa98680f2156a56ff9e36d84b7c820020
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="calling-an-odata-service-from-a-net-client-c"></a>Appel à un Service OData à partir d’un Client .NET (c#)
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/ASPNET-Web-API-OData-cecdb524)

> Ce didacticiel montre comment appeler un service OData à partir d’une application cliente c#.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads) (fonctionne avec Visual Studio 2012)
> - [Bibliothèque cliente WCF Data Services](https://msdn.microsoft.com/library/cc668772.aspx)
> - API Web 2. (L’exemple de service OData est créé à l’aide des API Web 2, mais l’application cliente ne dépend pas des API Web).


Dans ce didacticiel, je vais guider la création d’une application cliente qui appelle un service OData. Le service OData expose les entités suivantes :

- `Product`
- `Supplier`
- `ProductRating`

![](calling-an-odata-service-from-a-net-client/_static/image1.png)

Les articles suivants décrivent comment implémenter le service OData dans l’API Web. (Vous n’avez pas besoin de les lire pour comprendre ce didacticiel, toutefois.)

- [Création d’un point de terminaison OData dans l’API Web 2](creating-an-odata-endpoint.md)
- [Relations d’entité OData dans l’API Web 2](working-with-entity-relations.md)
- [Actions OData dans Web API 2](odata-actions.md)

## <a name="generate-the-service-proxy"></a>Générer le Proxy de Service

La première étape consiste à générer un proxy de service. Le proxy de service est une classe .NET qui définit des méthodes pour accéder au service OData. Le proxy traduit les appels de méthode dans les requêtes HTTP.

![](calling-an-odata-service-from-a-net-client/_static/image2.png)

Commencez par ouvrir le projet de service OData dans Visual Studio. Appuyez sur CTRL + F5 pour exécuter le service localement dans IIS Express. Notez l’adresse locale, y compris le numéro de port qui affecte de Visual Studio. Vous aurez besoin de cette adresse lorsque vous créez le proxy.

Ensuite, ouvrez une autre instance de Visual Studio et créez un projet d’application console. L’application console sera notre application de client OData. (Vous pouvez également ajouter le projet à la même solution que le service).

> [!NOTE]
> Les étapes restantes font référence le projet de console.


Dans l’Explorateur de solutions, cliquez sur **références** et sélectionnez **ajouter une référence de Service**.

![](calling-an-odata-service-from-a-net-client/_static/image3.png)

Dans le **ajouter une référence de Service** boîte de dialogue, tapez l’adresse du service OData :

[!code-console[Main](calling-an-odata-service-from-a-net-client/samples/sample1.cmd)]

où *port* est le numéro de port.

[![](calling-an-odata-service-from-a-net-client/_static/image5.png)](calling-an-odata-service-from-a-net-client/_static/image4.png)

Pour **Namespace**, tapez « ProductService ». Cette option définit l’espace de noms de la classe proxy.

Cliquez sur **OK**. Visual Studio lit le document de métadonnées OData pour découvrir les entités dans le service.

[![](calling-an-odata-service-from-a-net-client/_static/image7.png)](calling-an-odata-service-from-a-net-client/_static/image6.png)

Cliquez sur **OK** pour ajouter la classe proxy à votre projet.

![](calling-an-odata-service-from-a-net-client/_static/image8.png)

## <a name="create-an-instance-of-the-service-proxy-class"></a>Créez une Instance de la classe de Proxy de Service

À l’intérieur de votre `Main` (méthode), créez une nouvelle instance de la classe proxy, comme suit :

[!code-csharp[Main](calling-an-odata-service-from-a-net-client/samples/sample2.cs)]

Là encore, utilisez le numéro de port réel où votre service est en cours d’exécution. Lorsque vous déployez votre service, vous allez utiliser l’URI du service live. Vous n’avez pas besoin de mettre à jour le serveur proxy.

Le code suivant ajoute un gestionnaire d’événements qui imprime les URI de demande dans la fenêtre de console. Cette étape n’est pas obligatoire, mais il est intéressant de voir les URI pour chaque requête.

[!code-csharp[Main](calling-an-odata-service-from-a-net-client/samples/sample3.cs)]

## <a name="query-the-service"></a>Le Service de requête

Le code suivant obtient la liste des produits à partir du service OData.

[!code-csharp[Main](calling-an-odata-service-from-a-net-client/samples/sample4.cs)]

Notez que vous n’avez pas besoin d’écrire du code pour envoyer la demande HTTP ou d’analyser la réponse. La classe proxy effectue cela automatiquement lorsque vous énumérez les `Container.Products` collection dans le **foreach** boucle.

Lorsque vous exécutez l’application, la sortie doit ressembler à ce qui suit :

[!code-console[Main](calling-an-odata-service-from-a-net-client/samples/sample5.cmd)]

Pour obtenir une entité par ID, utilisez un `where` clause.

[!code-csharp[Main](calling-an-odata-service-from-a-net-client/samples/sample6.cs)]

Pour le reste de cette rubrique, je n’affiche pas l’ensemble du `Main` fonctionne, que le code nécessaire pour appeler le service.

## <a name="apply-query-options"></a>Appliquer les Options de requête

OData définit [options de requête](../supporting-odata-query-options.md) qui peut être utilisé pour filtrer, trier, données de la page et ainsi de suite. Dans le proxy de service, vous pouvez appliquer ces options à l’aide de diverses expressions LINQ.

Dans cette section, je vous montrerai courts exemples. Pour plus d’informations, consultez la rubrique [considérations de LINQ (WCF Data Services)](https://msdn.microsoft.com/library/ee622463.aspx) sur MSDN.

### <a name="filtering-filter"></a>Filtrage ($filter)

Pour filtrer, utilisez un `where` clause. L’exemple suivant filtre par catégorie de produit.

[!code-csharp[Main](calling-an-odata-service-from-a-net-client/samples/sample7.cs)]

Ce code correspond à la requête OData.

[!code-console[Main](calling-an-odata-service-from-a-net-client/samples/sample8.cmd)]

Notez que le proxy convertit le `where` clause dans un OData `$filter` expression.

### <a name="sorting-orderby"></a>Tri ($orderby)

Pour trier, utilisez un `orderby` clause. L’exemple suivant trie par prix, du plus élevé au plus bas.

[!code-csharp[Main](calling-an-odata-service-from-a-net-client/samples/sample9.cs)]

Voici la requête OData correspondante.

[!code-console[Main](calling-an-odata-service-from-a-net-client/samples/sample10.cmd)]

### <a name="client-side-paging-skip-and-top"></a>La pagination côté client ($skip et $top)

Pour les jeux d’entités volumineux, le client souhaite peut limiter le nombre de résultats. Par exemple, un client peut afficher des entrées de 10 à la fois. Il s’agit *la pagination côté client*. (Il existe également [la pagination côté serveur](../supporting-odata-query-options.md#server-paging), où le serveur limite le nombre de résultats.) Pour effectuer la pagination côté client, utilisez LINQ **ignorer** et **prendre** méthodes. L’exemple suivant ignore les 40 premiers résultats et prend les 10 suivants.

[!code-csharp[Main](calling-an-odata-service-from-a-net-client/samples/sample11.cs)]

Voici la requête OData correspondante :

[!code-console[Main](calling-an-odata-service-from-a-net-client/samples/sample12.cmd)]

### <a name="select-select-and-expand-expand"></a>Select ($select) et développer ($expand)

Pour inclure les entités associées, utilisez le `DataServiceQuery<t>.Expand` (méthode). Par exemple, pour inclure la `Supplier` pour chaque `Product`:

[!code-csharp[Main](calling-an-odata-service-from-a-net-client/samples/sample13.cs)]

Voici la requête OData correspondante :

[!code-console[Main](calling-an-odata-service-from-a-net-client/samples/sample14.cmd)]

Pour modifier la forme de la réponse, utilisez LINQ **sélectionnez** clause. L’exemple suivant obtient simplement le nom de chaque produit, avec aucune autre propriété.

[!code-csharp[Main](calling-an-odata-service-from-a-net-client/samples/sample15.cs)]

Voici la requête OData correspondante :

[!code-console[Main](calling-an-odata-service-from-a-net-client/samples/sample16.cmd)]

Une clause select peut inclure des entités associées. Dans ce cas, n’appelez pas **Expand**; le proxy inclut automatiquement l’expansion dans ce cas. L’exemple suivant obtient le nom et le fournisseur de chaque produit.

[!code-csharp[Main](calling-an-odata-service-from-a-net-client/samples/sample17.cs)]

Voici la requête OData correspondante. Notez qu’il contient le **$expand** option.

[!code-console[Main](calling-an-odata-service-from-a-net-client/samples/sample18.cmd)]

Pour plus d’informations sur $select et $développez, consultez [à l’aide de $select, $expand et $value dans l’API Web 2](../using-select-expand-and-value.md).

## <a name="add-a-new-entity"></a>Ajouter une nouvelle entité

Pour ajouter une nouvelle entité à un jeu d’entités, appelez `AddToEntitySet`, où *EntitySet* est le nom du jeu d’entités. Par exemple, `AddToProducts` ajoute un nouveau `Product` à la `Products` jeu d’entités. Lorsque vous générez le proxy, WCF Data Services crée automatiquement ces fortement typée **AddTo** méthodes.

[!code-csharp[Main](calling-an-odata-service-from-a-net-client/samples/sample19.cs)]

Pour ajouter un lien entre deux entités, utilisez le **AddLink** et **SetLink** méthodes. Le code suivant ajoute un nouveau fournisseur et un nouveau produit et crée ensuite des liens entre eux.

[!code-csharp[Main](calling-an-odata-service-from-a-net-client/samples/sample20.cs)]

Utilisez **AddLink** lorsque la propriété de navigation est une collection. Dans cet exemple, nous ajoutons un produit à la `Products` collection sur le fournisseur.

Utilisez **SetLink** lorsque la propriété de navigation est une entité unique. Dans cet exemple, nous définissons le `Supplier` propriété sur le produit.

## <a name="update--patch"></a>Mettre à jour / correctifs

Pour mettre à jour une entité, appelez le **UpdateObject** (méthode).

[!code-csharp[Main](calling-an-odata-service-from-a-net-client/samples/sample21.cs)]

La mise à jour est effectuée lorsque vous appelez **SaveChanges**. Par défaut, WCF envoie une demande HTTP MERGE. Le **PatchOnUpdate** option indique à WCF pour envoyer un HTTP PATCH à la place.

> [!NOTE]
> Pourquoi des correctifs et la fusion ? La spécification HTTP 1.1 d’origine ([RCF 2616](http://tools.ietf.org/html/rfc2616)) n’a pas défini de n’importe quelle méthode HTTP avec la sémantique de « mise à jour partielle ». Pour prendre en charge les mises à jour partielles, la spécification OData définie par la méthode de fusion. En 2010, [RFC 5789](http://tools.ietf.org/html/rfc5789) défini par la méthode PATCH pour les mises à jour partielles. Vous pouvez lire la partie de l’historique dans ce [billet de blog](https://blogs.msdn.com/b/astoriateam/archive/2008/05/20/merge-vs-replace-semantics-for-update-operations.aspx) sur le Blog de WCF Data Services. Aujourd'hui, un correctif logiciel est préféré sur la fusion. Le contrôleur OData créé par la génération de modèles automatique Web API prend en charge les deux méthodes.


Si vous souhaitez remplacer l’entité toute entière (sémantique PUT), spécifiez la **ReplaceOnUpdate** option. Cela provoque une WCF envoyer une demande HTTP PUT.

[!code-csharp[Main](calling-an-odata-service-from-a-net-client/samples/sample22.cs)]

## <a name="delete-an-entity"></a>Supprimer une entité

Pour supprimer une entité, appelez **DeleteObject**.

[!code-csharp[Main](calling-an-odata-service-from-a-net-client/samples/sample23.cs)]

## <a name="invoke-an-odata-action"></a>Appeler une Action OData

Dans OData, [actions](odata-actions.md) permettent d’ajouter des comportements côté serveur qui ne sont pas définies facilement comme des opérations CRUD sur les entités.

Bien que le document de métadonnées OData décrit les actions, la classe proxy ne crée pas de méthodes fortement typées pour eux. Vous pouvez toujours appeler une action OData à l’aide de l’objet générique **Execute** (méthode). Toutefois, vous devez connaître les types de données des paramètres et la valeur de retour.

Par exemple, le `RateProduct` action prend le paramètre nommé « Évaluation » de type `Int32` et retourne un `double`. Le code suivant montre comment appeler cette action.

[!code-csharp[Main](calling-an-odata-service-from-a-net-client/samples/sample24.cs)]

Pour plus d’informations, consultez[appeler les opérations de Service et les Actions](https://msdn.microsoft.com/library/hh230677.aspx).

Une option consiste à étendre le **conteneur** classe afin de fournir une méthode fortement typée qui appelle l’action :

[!code-csharp[Main](calling-an-odata-service-from-a-net-client/samples/sample25.cs)]
