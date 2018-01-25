---
uid: web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2
title: Attribut de routage dans ASP.NET Web API 2 | Documents Microsoft
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/20/2014
ms.topic: article
ms.assetid: 979d6c9f-0129-4e5b-ae56-4507b281b86d
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2
msc.type: authoredcontent
ms.openlocfilehash: 173add73a150d3e13ae243d6548463da912dadee
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="attribute-routing-in-aspnet-web-api-2"></a>Routage d’attributs dans l’API Web ASP.NET 2
====================
par [Mike Wasson](https://github.com/MikeWasson)

*Routage* est comment API Web correspond à un URI à une action. API Web 2 prend en charge un nouveau type de routage, appelé *attribut routage*. Comme son nom l’indique, attribut routage utilise des attributs pour définir des itinéraires. Routage d’attributs vous donne davantage de contrôle sur les URI de votre API web. Par exemple, vous pouvez facilement créer des URI qui décrivent les hiérarchies de ressources.

Au antérieur style de routage, basé sur une convention, le routage est toujours entièrement pris en charge. En fait, vous pouvez combiner ces deux techniques dans le même projet.

Cette rubrique montre comment activer le routage de l’attribut et décrit les différentes options pour le routage de l’attribut. Pour un didacticiel de bout en bout qui utilise le routage d’attributs, consultez [créer une API REST avec le routage d’attribut dans l’API Web 2](create-a-rest-api-with-attribute-routing.md).


## <a name="prerequisites"></a>Prérequis

[Visual Studio 2017](https://www.visualstudio.com/vs/) Community, Professional ou Enterprise Edition

Vous pouvez également utiliser Gestionnaire de Package NuGet pour installer les packages nécessaires. À partir de la **outils** menu dans Visual Studio, sélectionnez **Gestionnaire de Package de bibliothèque**, puis sélectionnez **Package Manager Console**. Entrez la commande suivante dans la fenêtre de Console du Gestionnaire de Package :

`Install-Package Microsoft.AspNet.WebApi.WebHost`

<a id="why"></a>
## <a name="why-attribute-routing"></a>Attribut pourquoi routage ?

La première version de l’API Web utilisé *basée sur une convention* routage. Dans ce type de routage, vous définissez une ou plusieurs modèles d’itinéraire, qui sont essentiellement des paramétrables chaînes. Lorsque l’infrastructure reçoit une demande, elle correspond à l’URI sur le modèle d’itinéraire. (Pour plus d’informations sur le routage basé sur une convention, consultez [le routage ASP.NET Web API](routing-in-aspnet-web-api.md).

Un avantage de routage basé sur une convention est que les modèles sont définis dans un emplacement unique, et les règles de routage sont appliqués de manière cohérente sur tous les contrôleurs. Malheureusement, le routage basé sur une convention rend difficile à prendre en charge certains modèles URI qui sont communes dans les API RESTful. Par exemple, les ressources contiennent souvent des ressources enfants : les clients ont passé des commandes, films ont des acteurs, la documentation avoir auteurs et ainsi de suite. Il est naturel pour créer l’URI qui reflètent ces relations :

`/customers/1/orders`

Ce type d’URI est difficile à créer en utilisant le routage basé sur une convention. Bien qu’il est possible, les résultats ne sont pas évolutifs bien si vous avez de nombreux contrôleurs ou les types de ressources.

Avec le routage de l’attribut, il est trivial pour définir un itinéraire pour cet URI. Vous ajoutez simplement un attribut à l’action du contrôleur :

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample1.cs)]

Voici certains autres modèles cet attribut permet de routage simple.

**Versions d’API**

Dans cet exemple, « / api/v1/produits » serait routé vers un autre contrôleur que « / v2/api/produits ».

`/api/v1/products`  
`/api/v2/products`

**Segments d’URI surchargés**

Dans cet exemple, « 1 » est un numéro de commande, mais mappe « en attente » à une collection.

`/orders/1`  
`/orders/pending`

**Plusieurs types de paramètres**

Dans cet exemple, « 1 » est un numéro de commande, mais « 16/06/2013 » spécifie une date.

`/orders/1`  
`/orders/2013/06/16`

<a id="enable"></a>
## <a name="enabling-attribute-routing"></a>L’activation du routage d’attribut

Pour activer le routage d’attributs, appelez **MapHttpAttributeRoutes** lors de la configuration. Cette méthode d’extension est définie dans le **System.Web.Http.HttpConfigurationExtensions** classe.

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample2.cs)]

Routage d’attributs peut être combiné avec [basée sur une convention](routing-in-aspnet-web-api.md) routage. Pour définir des itinéraires basée sur une convention, appelez le **MapHttpRoute** (méthode).

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample3.cs)]

Pour plus d’informations sur la configuration des API Web, consultez [configuration ASP.NET Web API 2](../advanced/configuring-aspnet-web-api.md).

<a id="config"></a>
### <a name="note-migrating-from-web-api-1"></a>Remarque : La migration à partir de l’API Web 1

Avant d’API Web 2, les modèles de projet Web API générée code similaire à celui-ci :

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample4.cs)]

Si le routage de l’attribut est activé, ce code lève une exception. Si vous mettez à niveau un projet d’API Web existant pour utiliser le routage de l’attribut, assurez-vous que mettre à jour de ce code de configuration pour les éléments suivants :

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample5.cs?highlight=4)]

> [!NOTE]
> Pour plus d’informations, consultez [configuration des API Web avec hébergement ASP.NET](../advanced/configuring-aspnet-web-api.md#webhost).


<a id="add-routes"></a>
## <a name="adding-route-attributes"></a>Ajout d’attributs d’itinéraire

Voici un exemple d’un itinéraire défini à l’aide d’un attribut :

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample6.cs)]

La chaîne &quot;clients / {customerId} / commandes&quot; est le modèle d’URI pour l’itinéraire. API Web tente de correspondre à l’URI de demande pour le modèle. Dans cet exemple, « customers » et « orders » sont des segments de littéral, et « {customerId} » est un paramètre de variable. Les URI suivants correspondrait à ce modèle :

- `http://localhost/customers/1/orders`
- `http://localhost/customers/bob/orders`
- `http://localhost/customers/1234-5678/orders`

Vous pouvez limiter la correspondance à l’aide de [contraintes](#constraints), comme décrit plus loin dans cette rubrique.

Notez que la &quot;{customerId}&quot; paramètre dans le modèle d’itinéraire correspond au nom de la *customerId* paramètre dans la méthode. Lors de l’API Web appelle l’action du contrôleur, il essaie de lier les paramètres d’itinéraire. Par exemple, si l’URI est `http://example.com/customers/1/orders`, API Web essaie de lier la valeur « 1 » pour le *customerId* paramètre dans l’action.

Un modèle d’URI peut avoir plusieurs paramètres :

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample7.cs)]

Toutes les méthodes de contrôleur qui n’ont pas un attribut d’itinéraire utilisent le routage basé sur une convention. De cette façon, vous pouvez combiner les deux types de routage dans le même projet.

## <a name="http-methods"></a>Méthodes HTTP

API Web sélectionne également des actions en fonction de la méthode HTTP de la demande (GET, POST, etc.). Par défaut, les API Web recherche une correspondance de la casse avec le début du nom de la méthode de contrôleur. Par exemple, une méthode de contrôleur nommée `PutCustomers` correspond à une requête HTTP PUT.

Vous pouvez remplacer cette convention en décorant la méthode avec les attributs suivants :

- **[HttpDelete]**
- **[HttpGet]**
- **[HttpHead]**
- **[HttpOptions]**
- **[HttpPatch]**
- **[HttpPost]**
- **[HttpPut]**

L’exemple suivant mappe la méthode CreateBook aux demandes HTTP POST.

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample8.cs)]

Pour toutes les autres méthodes HTTP, y compris les méthodes non standard, utilisent la **AcceptVerbs** attribut, qui prend une liste de méthodes HTTP.

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample9.cs)]

<a id="prefixes"></a>
## <a name="route-prefixes"></a>Préfixes d’itinéraire

Souvent, les itinéraires dans un contrôleur démarrent tous avec le même préfixe. Exemple :

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample10.cs)]

Vous pouvez définir un préfixe commun pour un contrôleur entière à l’aide de la **[RoutePrefix]** attribut :

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample11.cs)]

Utilisez un tilde (~) sur l’attribut de méthode pour remplacer le préfixe d’itinéraire :

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample12.cs)]

Le préfixe d’itinéraire peut inclure des paramètres :

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample13.cs)]

<a id="constraints"></a>
## <a name="route-constraints"></a>Contraintes d’itinéraire

Contraintes d’itinéraire vous permettent de limiter la correspondance des paramètres dans le modèle d’itinéraire. La syntaxe générale est &quot;{ : contrainte de paramètre}&quot;. Exemple :

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample14.cs)]

Ici, le premier itinéraire est uniquement activée si le &quot;id&quot; segment de l’URI est un entier. Sinon, le second itinéraire est choisi.

Le tableau suivant répertorie les contraintes qui sont pris en charge.

| Contrainte | Description | Exemple |
| --- | --- | --- |
| alpha | Les correspondances en majuscules ou minuscules de l’alphabet Latin (a-z, A-Z) | {x:alpha} |
| bool | Correspond à une valeur booléenne. | {x : bool} |
| datetime | Correspond à un **DateTime** valeur. | {x:datetime} |
| decimal | Correspond à une valeur décimale. | {x:decimal} |
| double | Correspond à une valeur à virgule flottante 64 bits. | {x:double} |
| float | Correspond à une valeur à virgule flottante 32 bits. | {x:float} |
| GUID | Correspond à une valeur GUID. | {x:guid} |
| int | Correspond à une valeur d’entier 32 bits. | {x : int} |
| length | Correspond à une chaîne avec la longueur spécifiée ou dans une plage spécifiée de longueurs. | {x : length(6)} {x : length(1,20)} |
| long | Correspond à une valeur d’entier 64 bits. | {x : long} |
| max | Correspond à un entier avec une valeur maximale. | {x:max(10)} |
| MaxLength | Correspond à une chaîne avec une longueur maximale. | {x:maxlength(10)} |
| min | Correspond à un entier avec une valeur minimale. | {x:min(10)} |
| minLength | Correspond à une chaîne avec une longueur minimale. | {x : minlength(10)} |
| range | Correspond à un entier compris dans une plage de valeurs. | {x:range(10,50)} |
| regex | Correspond à une expression régulière. | {x:regex(^\d{3}-\d{3}-\d{4}$)} |

Notez que certains des contraintes, telles que &quot;min&quot;, acceptent des arguments entre parenthèses. Vous pouvez appliquer plusieurs contraintes à un paramètre, séparé par un signe deux-points.

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample15.cs)]

### <a name="custom-route-constraints"></a>Contraintes d’itinéraire personnalisé

Vous pouvez créer des contraintes d’itinéraire personnalisées en implémentant la **IHttpRouteConstraint** interface. Par exemple, la contrainte suivante restreint un paramètre à une valeur entière non nulle.

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample16.cs)]

Le code suivant montre comment inscrire la contrainte :

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample17.cs)]

Maintenant, vous pouvez appliquer la contrainte dans votre parcours de :

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample18.cs)]

Vous pouvez également remplacer l’ensemble de **DefaultInlineConstraintResolver** classe en implémentant la **IInlineConstraintResolver** interface. Cette opération remplace toutes les contraintes intégrées, à moins que votre implémentation de **IInlineConstraintResolver** spécifiquement les ajoute.

<a id="optional"></a>
## <a name="optional-uri-parameters-and-default-values"></a>Paramètres d’URI facultatif et les valeurs par défaut

Vous pouvez rendre un paramètre d’URI facultatif en ajoutant un point d’interrogation pour le paramètre d’itinéraire. Si un paramètre d’itinéraire est facultatif, vous devez définir une valeur par défaut pour le paramètre de méthode.

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample19.cs)]

Dans cet exemple, `/api/books/locale/1033` et `/api/books/locale` retournent la même ressource.

Vous pouvez également, vous pouvez spécifier une valeur par défaut dans le modèle d’itinéraire, comme suit :

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample20.cs)]

Il est presque identique à l’exemple précédent, mais il existe une légère différence de comportement lorsque la valeur par défaut est appliquée.

- Dans le premier exemple (« {lcid ?} »), la valeur par défaut 1033 est affectée directement au paramètre de méthode, donc le paramètre aura cette valeur exacte.
- Dans le deuxième exemple (« {lcid = 1033} »), la valeur par défaut « 1033 » passe par le processus de liaison de modèle. Le classeur de modèles par défaut convertira « 1033 » à la valeur numérique 1033. Toutefois, vous pouvez connecter dans un classeur de modèles personnalisés, ce qui peut faire quelque chose d’autre.

(Dans la plupart des cas, sauf si vous avez des classeurs de modèles personnalisés dans votre pipeline, les deux formes sera équivalentes.)

<a id="route-names"></a>
## <a name="route-names"></a>Noms d’itinéraires

Dans l’API Web, tous les itinéraires a un nom. Les noms d’itinéraires sont utiles pour générer des liens, afin que vous pouvez inclure un lien dans une réponse HTTP.

Pour spécifier le nom d’itinéraire, définissez la **nom** propriété sur l’attribut. L’exemple suivant montre comment définir le nom d’itinéraire et également comment utiliser le nom d’itinéraire lors de la génération d’un lien.

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample21.cs)]

<a id="order"></a>
## <a name="route-order"></a>Ordre de l’itinéraire

Lorsque l’infrastructure essaie de faire correspondre un URI avec un itinéraire, il évalue les itinéraires dans un ordre particulier. Pour spécifier l’ordre, définissez la **RouteOrder** propriété sur l’attribut d’itinéraire. Les valeurs inférieures sont évalués en premier. La valeur d’ordre par défaut est égale à zéro.

Voici comment l’ordonnancement total est déterminé :

1. Comparer les **RouteOrder** propriété de l’attribut d’itinéraire.
2. Examinez chaque segment d’URI dans le modèle d’itinéraire. Pour chaque segment, commande comme suit : 

    1. Segments de littéral.
    2. Paramètres d’itinéraire avec des contraintes.
    3. Paramètres d’itinéraire sans contraintes.
    4. Segments de paramètre générique avec des contraintes.
    5. Segments de paramètre générique sans contrainte.
3. Dans le cas d’égalité, les itinéraires sont classés par une comparaison de chaînes ordinale pas la casse ([OrdinalIgnoreCase](https://msdn.microsoft.com/library/system.stringcomparer.ordinalignorecase.aspx)) du modèle d’itinéraire.

Voici un exemple : Supposons que vous définissez le contrôleur suivant :

[!code-csharp[Main](attribute-routing-in-web-api-2/samples/sample22.cs)]

Ces itinéraires sont ordonnées comme suit.

1. orders/details
2. commandes / {id}
3. orders/{customerName}
4. orders/{\*date}
5. commandes / en attente

Notez que « détails » sont un segment de littéral et apparaît avant « {id} », mais « en attente » apparaît dernier, car le **RouteOrder** propriété est 1. (Cet exemple suppose qu’il n’est aucun client nommé « détails » ou « en attente ». En général, essayez d’éviter les itinéraires ambiguës. Dans cet exemple, un meilleur modèle d’itinéraire pour `GetByCustomer` est « clients / {customerName} »)
