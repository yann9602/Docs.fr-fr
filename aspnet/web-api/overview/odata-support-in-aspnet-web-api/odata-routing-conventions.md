---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-routing-conventions
title: "Conventions d’itinéraire dans ASP.NET Web API 2 Odata | Documents Microsoft"
author: MikeWasson
description: "Cet article décrit les conventions de routage utilise des API Web pour les points de terminaison OData."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/31/2013
ms.topic: article
ms.assetid: adbc175a-14eb-4ab2-a441-d056ffa8266f
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-routing-conventions
msc.type: authoredcontent
ms.openlocfilehash: cd24a85a05e427f83d28cae876431d04cc295f17
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="routing-conventions-in-aspnet-web-api-2-odata"></a>Conventions d’itinéraire dans ASP.NET Web API 2 Odata
====================
par [Mike Wasson](https://github.com/MikeWasson)

> Cet article décrit les conventions de routage utilise des API Web pour les points de terminaison OData.


Lors de l’API Web reçoit une requête OData, il mappe la demande à un nom de contrôleur et un nom d’action. Le mappage est basé sur la méthode HTTP et de l’URI. Par exemple, `GET /odata/Products(1)` mappe à `ProductsController.GetProduct`.

Dans la partie 1 de cet article, je décris les conventions d’itinéraire OData intégrées. Ces conventions sont conçues spécifiquement pour les points de terminaison OData, et remplacer le système de routage d’API Web par défaut. (Le remplacement se produit lorsque vous appelez **MapODataRoute**.)

Dans la partie 2, je montrent comment ajouter des conventions d’itinéraire personnalisées. Actuellement les conventions intégrées ne couvrent pas la plage entière d’OData URIs, mais vous pouvez les étendre pour gérer les cas supplémentaires.

- [Conventions de routage intégrées](#conventions)
- [Conventions de routage personnalisées](#custom)

<a id="conventions"></a>
## <a name="built-in-routing-conventions"></a>Conventions de routage intégrées

Avant que je décris les conventions d’itinéraire OData dans l’API Web, il est utile de comprendre les OData URIs. Un [URI OData](http://www.odata.org/documentation/odata-v3-documentation/url-conventions/) se compose de :

- La racine du service
- Le chemin d’accès de ressource
- Options de requête

![](odata-routing-conventions/_static/image1.png)

Pour le routage, le point essentiel est le chemin d’accès de ressource. Le chemin d’accès de ressource est divisée en segments. Par exemple, `/Products(1)/Supplier` a trois segments :

- `Products`fait référence à un jeu d’entités nommé « Products ».
- `1`est une clé d’entité, en sélectionnant une seule entité du jeu.
- `Supplier`est une propriété de navigation qui sélectionne une entité associée.

Par conséquent, ce chemin d’accès sélectionne le fournisseur du produit 1.

> [!NOTE]
> Segments de chemin d’accès OData ne correspondent pas toujours aux segments de l’URI. Par exemple, « 1 » est considéré comme un segment de chemin d’accès.


**Noms de contrôleur.** Le nom du contrôleur est toujours dérivé de l’entité défini à la racine du chemin d’accès de ressource. Par exemple, si le chemin d’accès de ressource est `/Products(1)/Supplier`, API Web recherche un contrôleur nommé `ProductsController`.

**Noms d’actions.** Les noms d’actions sont dérivés de l’entity data model (EDM), ainsi que les segments de chemin d’accès, comme indiqué dans les tableaux suivants. Dans certains cas, vous avez deux possibilités pour le nom d’action. Par exemple, « Get » ou &quot;GetProducts&quot;.

**Interrogation des entités**

| Requête | Exemple d’URI | Nom de l’action | Exemple d’Action |
| --- | --- | --- | --- |
| OBTENIR /entityset | / Produits | GetEntitySet ou Get | GetProducts |
| OBTENIR /entityset(key) | /Products(1) | GetEntityType ou Get | GetProduct |
| OBTENIR /entityset (key) / effectué | / Produits (1) /Models.Book | GetEntityType ou Get | GetBook |

Pour plus d’informations, consultez [créer un point de terminaison OData en lecture seule](odata-v3/creating-an-odata-endpoint.md).

**Création, mise à jour et suppression d’entités**

| Requête | Exemple d’URI | Nom de l’action | Exemple d’Action |
| --- | --- | --- | --- |
| VALIDER /entityset | / Produits | PostEntityType ou Post | PostProduct |
| PUT /entityset(key) | /Products(1) | PutEntityType ou Put | PutProduct |
| PUT /entityset (key) / effectué | / Produits (1) /Models.Book | PutEntityType ou Put | PutBook |
| Correctif logiciel /entityset(key) | /Products(1) | PatchEntityType ou un correctif | PatchProduct |
| Correctif logiciel /entityset (key) / effectué | / Produits (1) /Models.Book | PatchEntityType ou un correctif | PatchBook |
| SUPPRIMER /entityset(key) | /Products(1) | DeleteEntityType ou Delete | DeleteProduct |
| SUPPRIMER des /entityset (key) ou effectuer un cast | / Produits (1) /Models.Book | DeleteEntityType ou Delete | DeleteBook |

**Interrogation d’une propriété de Navigation**

| Requête | Exemple d’URI | Nom de l’action | Exemple d’Action |
| --- | --- | --- | --- |
| GET /entityset (key) / navigation | / Produits (1) / fournisseur | GetNavigationFromEntityType ou GetNavigation | GetSupplierFromProduct |
| OBTENIR /entityset (key) / cast/de navigation | / Produits (1) /Models.Book/Author | GetNavigationFromEntityType ou GetNavigation | GetAuthorFromBook |

Pour plus d’informations, consultez [utilisation des Relations d’entité](odata-v3/working-with-entity-relations.md).

**Création et suppression de liens**

| Requête | Exemple d’URI | Nom de l’action |
| --- | --- | --- |
| POST /entityset (key) / $links/navigation | / Liens de (1) / $ produits/fournisseur | CreateLink |
| PUT /entityset (key) / $links/navigation | / Liens de (1) / $ produits/fournisseur | CreateLink |
| DELETE /entityset (key) / $links/navigation | / Liens de (1) / $ produits/fournisseur | DeleteLink |
| SUPPRIMER /entityset(key)/$links/navigation(relatedKey) | /Products/(1)/$Links/Suppliers(1) | DeleteLink |

Pour plus d’informations, consultez [utilisation des Relations d’entité](odata-v3/working-with-entity-relations.md).

**Propriétés**

*Requiert l’API Web 2*

| Requête | Exemple d’URI | Nom de l’action | Exemple d’Action |
| --- | --- | --- | --- |
| GET /entityset (key) / propriété | / (1) / nom du produit | GetPropertyFromEntityType ou GetProperty | GetNameFromProduct |
| OBTENIR/cast et des propriétés /entityset (clé) | / Produits (1) /Models.Book/Author | GetPropertyFromEntityType ou GetProperty | GetTitleFromBook |

**Actions**

| Requête | Exemple d’URI | Nom de l’action | Exemple d’Action |
| --- | --- | --- | --- |
| POST /entityset (key) / action | / Produits (1) / taux | ActionNameOnEntityType ou ActionName | RateOnProduct |
| VALIDER /entityset (key) / cast/action | / Produits (1) /Models.Book/CheckOut | ActionNameOnEntityType ou ActionName | CheckOutOnBook |

Pour plus d’informations, consultez [Actions OData](odata-v3/odata-actions.md).

**Signatures de méthode**

Voici quelques règles pour les signatures de méthode :

- Si le chemin d’accès contient une clé, l’action doit avoir un paramètre nommé *clé*.
- Si le chemin d’accès contient une clé dans une propriété de navigation, l’action doit avoir un paramètre nommé *relatedKey*.
- Décorer *clé* et *relatedKey* paramètres avec le **[FromODataUri]** paramètre.
- VALIDER et les demandes PUT prennent un paramètre de type d’entité.
- Les demandes de correctif prendre un paramètre de type **Delta&lt;T&gt;**, où *T* est le type d’entité.

Pour référence, Voici un exemple qui montre les signatures de méthode pour chaque convention de routage OData intégrée.

[!code-csharp[Main](odata-routing-conventions/samples/sample1.cs)]

<a id="custom"></a>
## <a name="custom-routing-conventions"></a>Conventions de routage personnalisées

Actuellement les conventions intégrées ne couvrent pas tous les OData URIs possibles. Vous pouvez ajouter de nouvelles conventions en implémentant la **IODataRoutingConvention** interface. Cette interface possède deux méthodes :

[!code-csharp[Main](odata-routing-conventions/samples/sample2.cs)]

- **SelectController** retourne le nom du contrôleur.
- **SelectAction** retourne le nom de l’action.

Pour les deux méthodes, si la convention ne s’applique pas à cette demande, la méthode doit retourner null.

Le **ODataPath** paramètre représente le chemin d’accès de ressource OData analysé. Il contient une liste de  **[ODataPathSegment](https://msdn.microsoft.com/en-us/library/system.web.http.odata.routing.odatapathsegment.aspx)**  instances, une pour chaque segment du chemin d’accès de ressource. **ODataPathSegment** est une classe abstraite ; chaque type de segment est représenté par une classe qui dérive de **ODataPathSegment**.

Le **ODataPath.TemplatePath** propriété est une chaîne qui représente la concaténation de tous les segments de chemin d’accès. Par exemple, si l’URI est `/Products(1)/Supplier`, le modèle de chemin d’accès est &quot;~/entityset/key/navigation&quot;. Notez que les segments ne correspondent pas directement aux segments URI. Par exemple, la clé d’entité (1) est représentée en tant que son propre **ODataPathSegment**.

En règle générale, une implémentation de **IODataRoutingConvention** effectue les opérations suivantes :

1. Comparer le modèle de chemin d’accès afin de déterminer si cette convention s’applique à la demande en cours. Si elle ne s’applique pas, retourne null.
2. Si la convention s’applique, utilisez les propriétés de la **ODataPathSegment** instances pour dériver les noms de contrôleur et d’action.
3. Pour les actions, ajoutez toutes les valeurs pour le dictionnaire d’itinéraires qui doit être lié aux paramètres d’action (en général, les clés d’entité).

Examinons un exemple spécifique. Les conventions de routage intégrées ne gèrent pas l’indexation dans une collection de navigation. En d’autres termes, il n’existe aucune convention d’URI comme suit :

[!code-javascript[Main](odata-routing-conventions/samples/sample3.js)]

Voici une convention de routage personnalisée pour gérer ce type de requête.

[!code-csharp[Main](odata-routing-conventions/samples/sample4.cs)]

Remarques :

1. Dériver une classe à partir de **EntitySetRoutingConvention**, car le **SelectController** méthode dans cette classe est appropriée pour cette nouvelle convention de routage. Cela signifie que vous n’avez pas besoin de réimplémenter **SelectController**.
2. La convention s’applique uniquement aux demandes GET, et uniquement lorsque le modèle de chemin d’accès est &quot;~/entityset/key/navigation/key&quot;.
3. Le nom d’action est &quot;obtenir {EntityType}&quot;, où *{EntityType}* est le type de la collection de navigation. Par exemple, &quot;GetSupplier&quot;. Vous pouvez utiliser n’importe quel convention d’affectation de noms que vous aimez &#8212; Assurez-vous que les actions de contrôleur correspondent.
4. L’action prend deux paramètres nommés *clé* et *relatedKey*. (Pour obtenir la liste de certains noms de paramètres prédéfinies, consultez [ODataRouteConstants](https://msdn.microsoft.com/en-us/library/system.web.http.odata.routing.odatarouteconstants.aspx).)

L’étape suivante ajoute la nouvelle convention à la liste des conventions d’itinéraire. Cela se produit lors de la configuration, comme indiqué dans le code suivant :

[!code-csharp[Main](odata-routing-conventions/samples/sample5.cs)]

Voici quelques autres exemple conventions d’itinéraire qui est utile pour étudier la :

- [CompositeKeyRoutingConvention](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/WebApi/ODataCompositeKeySample/ODataCompositeKeySample/Extensions/CompositeKeyRoutingConvention.cs)
- [CustomNavigationRoutingConvention](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/WebApi/ODataServiceSample/ODataService/Extensions/CustomNavigationRoutingConvention.cs)
- [NonBindableActionRoutingConvention](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/WebApi/ODataActionsSample/ODataActionsSample/NonBindableActionRoutingConvention.cs)
- [ODataVersionRouteConstraint](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/WebApi/ODataVersioningSample/ODataVersioningSample/Extensions/ODataVersionRouteConstraint.cs)

Et naturellement API Web elle-même est open source, vous pouvez voir les [code source](http://aspnetwebstack.codeplex.com/) pour les conventions de routage intégrées. Elles sont définies dans le **System.Web.Http.OData.Routing.Conventions** espace de noms.
