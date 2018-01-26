---
uid: web-api/overview/formats-and-model-binding/parameter-binding-in-aspnet-web-api
title: "Paramètre de liaison dans l’API Web ASP.NET | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/11/2013
ms.topic: article
ms.assetid: e42c8388-04ed-4341-9fdb-41b1b4c06320
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/formats-and-model-binding/parameter-binding-in-aspnet-web-api
msc.type: authoredcontent
ms.openlocfilehash: 5aa532137436922519c86246ebfa834910ac0d86
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="parameter-binding-in-aspnet-web-api"></a>Paramètre de liaison dans l’API Web ASP.NET
====================
par [Mike Wasson](https://github.com/MikeWasson)

Lors de l’API Web appelle une méthode sur un contrôleur, il doit définir des valeurs pour les paramètres, un processus appelé *liaison*. Cet article décrit la manière dont les API Web lie les paramètres, et comment vous pouvez personnaliser le processus de liaison.

Par défaut, les API Web utilise les règles suivantes pour lier les paramètres :

- Si le paramètre est un type « simple », API Web essaie d’obtenir la valeur de l’URI. Exemples de types simples .NET [les types primitifs](https://msdn.microsoft.com/library/system.type.isprimitive.aspx) (**int**, **bool**, **double**, et ainsi de suite), ainsi que **TimeSpan**, **DateTime**, **Guid**, **décimal**, et **chaîne**, *plus* tout type avec un convertisseur de type qui peut être converti à partir d’une chaîne. (Plus d’informations sur les convertisseurs de type plus tard.)
- Pour les types complexes, API Web tente de lire la valeur du corps du message, à l’aide un [formateur de type de média](media-formatters.md).

Par exemple, voici une méthode de contrôleur d’API Web classique :

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample1.cs)]

Le *id* paramètre est un &quot;simple&quot; tapez, afin de l’API Web essaie d’obtenir la valeur de l’URI de requête. Le *élément* paramètre est un type complexe, afin de l’API Web utilise un formateur de type de média à lire la valeur dans le corps de la demande.

Pour obtenir une valeur de l’URI, une API Web recherche dans les données d’itinéraire et de la chaîne de requête URI. Les données d’itinéraire sont remplies lorsque le système de routage analyse l’URI et l’associe à un itinéraire. Pour plus d’informations, consultez [routage et sélection de l’Action](../web-api-routing-and-actions/routing-and-action-selection.md).

Dans le reste de cet article, je vais montrer comment vous pouvez personnaliser le processus de liaison de modèle. Pour les types complexes, toutefois, envisagez d’utiliser les formateurs de type de média autant que possible. Un principe clé du protocole HTTP est que les ressources sont envoyées dans le corps du message, à l’aide de la négociation de contenu pour spécifier la représentation sous forme de la ressource. Formateurs de type de média ont été conçus dans ce but précis.

## <a name="using-fromuri"></a>À l’aide de [FromUri]

Pour forcer l’API Web pour lire un type complexe à partir de l’URI, ajoutez le **[FromUri]** au paramètre. L’exemple suivant définit un `GeoPoint` type, ainsi que d’une méthode de contrôleur qui obtient le `GeoPoint` à partir de l’URI.

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample2.cs)]

Le client peut placer les valeurs de Latitude et Longitude dans la chaîne de requête et API Web les utilise pour construire un `GeoPoint`. Exemple :

`http://localhost/api/values/?Latitude=47.678558&Longitude=-122.130989`

## <a name="using-frombody"></a>À l’aide de [FromBody]

Pour forcer l’API Web pour lire un type simple à partir du corps de la demande, vous devez ajouter le **[FromBody]** au paramètre :

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample3.cs)]

Dans cet exemple, API Web utilisera un formateur de type de média à lire la valeur de *nom* à partir du corps de la demande. Voici un exemple de demande client.

[!code-console[Main](parameter-binding-in-aspnet-web-api/samples/sample4.cmd)]

Lorsqu’un paramètre a [FromBody], API Web utilise l’en-tête Content-Type pour sélectionner un module de formatage. Dans cet exemple, le type de contenu est &quot;application/json&quot; et le corps de la demande est une chaîne JSON brute (pas un objet JSON).

Au moins un paramètre est autorisé à lire le corps du message. Par conséquent, cela ne fonctionne pas :

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample5.cs)]

La raison de cette règle est que le corps de la demande peut être stocké dans un flux non mis en mémoire tampon qui peut être lu uniquement une seule fois.

## <a name="type-converters"></a>Convertisseurs de type

Vous pouvez apporter les API Web traiter une classe comme un type simple (de sorte que les API Web tente de se lier à partir de l’URI) en créant un **TypeConverter** et en fournissant une conversion de chaîne.

Le code suivant illustre un `GeoPoint` classe qui représente un point géographique, plus une **TypeConverter** qui convertit des chaînes en `GeoPoint` instances. Le `GeoPoint` classe est décorée avec un **[TypeConverter]** attribut pour spécifier le convertisseur de type. (Cet exemple a été inspiré par billet de blog de Mike Stall [comment lier à des objets personnalisés dans les signatures d’action dans MVC/WebAPI](https://blogs.msdn.com/b/jmstall/archive/2012/04/20/how-to-bind-to-custom-objects-in-action-signatures-in-mvc-webapi.aspx).)

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample6.cs)]

Maintenant l’API Web traitera `GeoPoint` comme un type simple, ce qui signifie qu’il tente de se lier `GeoPoint` paramètres à partir de l’URI. Vous n’avez pas besoin d’inclure **[FromUri]** sur le paramètre.

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample7.cs)]

Le client peut appeler la méthode avec un URI comme suit :

`http://localhost/api/values/?location=47.678558,-122.130989`

## <a name="model-binders"></a>Classeurs de modèles

Une option plus souple à un convertisseur de type consiste à créer un classeur de modèles personnalisés. Avec un classeur de modèles, vous avez accès à des éléments tels que la requête HTTP, la description de l’action et les valeurs brutes à partir des données d’itinéraire.

Pour créer un classeur de modèles, vous devez implémenter la **classeur de modèles** interface. Cette interface définit une méthode unique, **BindModel**:

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample8.cs)]

Voici un classeur de modèles pour `GeoPoint` objets.

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample9.cs)]

Un classeur de modèles Obtient les valeurs d’entrée brutes à partir d’un *fournisseur de valeur*. Cette conception sépare les deux fonctions distinctes :

- Le fournisseur de valeur prend la requête HTTP et remplit un dictionnaire de paires clé-valeur.
- Le classeur de modèles utilise ce dictionnaire pour remplir le modèle.

Le fournisseur de valeur par défaut dans l’API Web obtient les valeurs de données d’itinéraire et de la chaîne de requête. Par exemple, si l’URI est `http://localhost/api/values/1?location=48,-122`, le fournisseur de valeur crée les paires clé-valeur suivantes :

- id = &quot;1&quot;
- emplacement = &quot;48,122&quot;

(Je suis en supposant que le modèle d’itinéraire par défaut, qui est &quot;api / {controller} / {id}&quot;.)

Le nom du paramètre à lier est stocké dans le **ModelBindingContext.ModelName** propriété. Le classeur de modèles recherche une clé avec cette valeur dans le dictionnaire. Si la valeur existe et qu’il peut être convertie en un `GeoPoint`, le classeur de modèles affecte la valeur liée à la **ModelBindingContext.Model** propriété.

Notez que le classeur de modèles n’est pas limité à une conversion de type simple. Dans cet exemple, le classeur de modèles recherche tout d’abord dans une table des emplacements connus, et en cas d’échec, il utilise la conversion de type.

**Définir le classeur de modèles**

Il existe plusieurs façons de définir un classeur de modèles. Tout d’abord, vous pouvez ajouter un **[ModelBinder]** au paramètre.

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample10.cs)]

Vous pouvez également ajouter un **[ModelBinder]** pour le type d’attribut. API Web utilise le classeur de modèles spécifié pour tous les paramètres de ce type.

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample11.cs)]

Enfin, vous pouvez ajouter un fournisseur de classeur de modèles à la **HttpConfiguration**. Un fournisseur de classeur de modèles est simplement une classe de fabrique qui crée un classeur de modèles. Vous pouvez créer un fournisseur en dérivant de la [ModelBinderProvider](https://msdn.microsoft.com/library/system.web.http.modelbinding.modelbinderprovider.aspx) classe. Toutefois, si votre classeur de modèles gère un type unique, il est plus facile à utiliser la fonction intégrée **SimpleModelBinderProvider**, qui est conçu à cet effet. Le code suivant montre comment procéder.

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample12.cs)]

Avec un fournisseur de la liaison de modèle, vous devez toujours ajouter la **[ModelBinder]** au paramètre pour indiquer les API Web qu’il doit utiliser un classeur de modèles et pas un formateur de type de média. Toutefois, vous ne devez désormais spécifier le type de classeur de modèles dans l’attribut :

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample13.cs)]

## <a name="value-providers"></a>Fournisseurs de valeur

Un classeur de modèles Obtient des valeurs à partir d’un fournisseur de valeur indiqué. Pour écrire un fournisseur de valeur personnalisée, vous devez implémenter la **IValueProvider** interface. Voici un exemple qui extrait les valeurs dans les cookies dans la demande :

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample14.cs)]

Vous devez également créer une fabrique de fournisseur de valeur en dérivant de la **ValueProviderFactory** classe.

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample15.cs)]

Ajouter la fabrique de fournisseur de valeur pour le **HttpConfiguration** comme suit.

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample16.cs)]

API Web compose tous les fournisseurs de valeur, par conséquent, lorsqu’un classeur de modèles appelle **ValueProvider.GetValue**, le classeur de modèles reçoit la valeur du premier fournisseur de valeurs qui est en mesure de générer.

Vous pouvez également définir la fabrique de fournisseur de valeur au niveau du paramètre à l’aide de la **ValueProvider** d’attribut, comme suit :

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample17.cs)]

Cela indique les API Web à utiliser la liaison de modèle avec la fabrique de fournisseur de valeur spécifiée et ne doit ne pas utiliser un des autres fournisseurs de valeur enregistrée.

## <a name="httpparameterbinding"></a>HttpParameterBinding

Classeurs de modèles sont une instance spécifique d’un mécanisme plus général. Si vous examinez la **[ModelBinder]** attribut, vous verrez qu’il dérive abstraite **ParameterBindingAttribute** classe. Cette classe définit une méthode unique, **GetBinding**, qui retourne un **HttpParameterBinding** objet :

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample18.cs)]

Un **HttpParameterBinding** est responsable de la liaison d’un paramètre à une valeur. Dans le cas de **[ModelBinder]**, l’attribut retourne un **HttpParameterBinding** implémentation utilise une **classeur de modèles** pour effectuer la liaison réelle. Vous pouvez également implémenter votre propre **HttpParameterBinding**.

Par exemple, supposons que vous souhaitez obtenir l’eTag à partir `if-match` et `if-none-match` en-têtes dans la demande. Nous allons commencer en définissant une classe pour représenter les ETags.

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample19.cs)]

Nous allons également définir une énumération pour indiquer s’il faut obtenir l’ETag à partir de la `if-match` en-tête ou le `if-none-match` en-tête.

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample20.cs)]

Voici une **HttpParameterBinding** qui obtient l’ETag à partir de l’en-tête de votre choix et le lie à un paramètre de type ETag :

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample21.cs)]

Le **ExecuteBindingAsync** méthode effectue la liaison. Dans cette méthode, ajoutez la valeur de paramètre lié à la **ActionArgument** dictionnaire dans le **HttpActionContext**.

> [!NOTE]
> Si votre **ExecuteBindingAsync** méthode lit le corps du message de demande, remplacez le **WillReadBody** propriété pour retourner true. Le corps de la demande peut être un flux non tamponné qui ne peut être lu qu’une seule fois, afin de l’API Web applique une règle qu’au maximum une liaison peut lire le corps du message.


Pour appliquer une personnalisée **HttpParameterBinding**, vous pouvez définir un attribut qui dérive de **ParameterBindingAttribute**. Pour `ETagParameterBinding`, nous allons définir deux attributs, un pour `if-match` en-têtes et l’autre pour `if-none-match` en-têtes. Les deux dérivent de la classe de base abstraite.

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample22.cs)]

Voici une méthode de contrôleur qui utilise le `[IfNoneMatch]` attribut.

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample23.cs)]

En outre **ParameterBindingAttribute**, il existe un autre crochet pour l’ajout d’un personnalisé **HttpParameterBinding**. Sur le **HttpConfiguration** objet, le **ParameterBindingRules** propriété est une collection de fonctions anomymous de type (**HttpParameterDescriptor**  - &gt; **HttpParameterBinding**). Par exemple, vous pouvez ajouter une règle qui utilise de n’importe quel paramètre ETag sur une méthode GET `ETagParameterBinding` avec `if-none-match`:

[!code-csharp[Main](parameter-binding-in-aspnet-web-api/samples/sample24.cs)]

La fonction doit retourner `null` pour les paramètres lorsque la liaison n’est pas applicable.

## <a name="iactionvaluebinder"></a>IActionValueBinder

Tout le processus de liaison de paramètre est contrôlé par un service enfichable, **IActionValueBinder**. L’implémentation par défaut de **IActionValueBinder** effectue les opérations suivantes :

1. Recherchez un **ParameterBindingAttribute** sur le paramètre. Cela inclut les **[FromBody]**, **[FromUri]**, et **[ModelBinder]**, ou les attributs personnalisés.
2. Sinon, recherchez dans **HttpConfiguration.ParameterBindingRules** pour une fonction qui retourne une valeur non null **HttpParameterBinding**.
3. Sinon, utilisez les règles par défaut décrite précédemment. 

    - Si le type de paramètre est « simple » ou un convertisseur de type, lier à partir de l’URI. Cela équivaut à placer le **[FromUri]** attribut sur le paramètre.
    - Sinon, essayez de lire le paramètre dans le corps du message. Cela équivaut à placer **[FromBody]** sur le paramètre.

Si vous le souhaitez, vous pouvez remplacer l’ensemble de **IActionValueBinder** service avec une implémentation personnalisée.

## <a name="additional-resources"></a>Ressources supplémentaires

[Exemple de liaison de paramètre personnalisé](http://aspnet.codeplex.com/sourcecontrol/latest#Samples/WebApi/CustomParameterBinding/ReadMe.txt)

Mike Stall écrit une série de bon de billets de blog sur la liaison de paramètre d’API Web :

- [Fonctionnement des API Web sur la liaison de paramètre](https://blogs.msdn.com/b/jmstall/archive/2012/04/16/how-webapi-does-parameter-binding.aspx)
- [Liaison de paramètre de Style de MVC pour API Web](https://blogs.msdn.com/b/jmstall/archive/2012/04/18/mvc-style-parameter-binding-for-webapi.aspx)
- [La liaison à des objets personnalisés dans les signatures d’action dans l’API MVC/Web](https://blogs.msdn.com/b/jmstall/archive/2012/04/20/how-to-bind-to-custom-objects-in-action-signatures-in-mvc-webapi.aspx)
- [Comment créer un fournisseur de valeur personnalisée dans l’API Web](https://blogs.msdn.com/b/jmstall/archive/2012/04/23/how-to-create-a-custom-value-provider-in-webapi.aspx)
- [Liaison de paramètre d’API Web sous le capot](https://blogs.msdn.com/b/jmstall/archive/2012/05/11/webapi-parameter-binding-under-the-hood.aspx)
