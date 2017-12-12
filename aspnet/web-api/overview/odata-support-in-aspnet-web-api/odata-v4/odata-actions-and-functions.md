---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v4/odata-actions-and-functions
title: "Actions et fonctions dans OData v4, à l’aide d’ASP.NET Web API 2.2 | Documents Microsoft"
author: MikeWasson
description: "Dans OData, les fonctions et les actions sont un moyen d’ajouter des comportements côté serveur qui ne sont pas définies facilement comme des opérations CRUD sur les entités. Ce didacticiel montre comment..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/27/2014
ms.topic: article
ms.assetid: 0e6fb03c-b16d-4bb0-ab0b-552bd2b6ece1
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v4/odata-actions-and-functions
msc.type: authoredcontent
ms.openlocfilehash: 532362f0c0faaaf0cb0c04726856f0497e5261b5
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="actions-and-functions-in-odata-v4-using-aspnet-web-api-22"></a>Actions et fonctions dans OData v4, à l’aide d’ASP.NET Web API 2.2
====================
par [Mike Wasson](https://github.com/MikeWasson)

> Dans OData, les fonctions et les actions sont un moyen d’ajouter des comportements côté serveur qui ne sont pas définies facilement comme des opérations CRUD sur les entités. Ce didacticiel montre comment ajouter des fonctions et les actions à un point de terminaison de v4 OData, à l’aide de Web API 2.2. Le didacticiel s’appuie sur le didacticiel [créer un Using ASP.NET Web API 2 OData v4 point de terminaison](create-an-odata-v4-endpoint.md)
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - 2.2 d’API Web
> - OData v4
> - [Visual Studio 2013 Update 2](https://www.visualstudio.com/downloads/download-visual-studio-vs)
> - .NET 4.5
> 
> 
> ## <a name="tutorial-versions"></a>Versions du didacticiels
> 
> Pour OData Version 3, consultez [Actions OData dans ASP.NET Web API 2](../odata-v3/odata-actions.md).


La différence entre *actions* et *fonctions* que les actions peuvent avoir des effets secondaires, et les fonctions ne sont pas. Les actions et les fonctions peuvent retourner des données. Certaines utilisations pour les actions sont les suivantes :

- Transactions complexes.
- La manipulation de plusieurs entités à la fois.
- Autoriser les mises à jour uniquement certaines propriétés d’une entité.
- Envoi de données qui n’est pas une entité.

Les fonctions sont utiles pour renvoyer des informations qui ne correspondant pas directement à une entité ou une collection.

Une action (ou une fonction) peut cibler une seule entité ou une collection. Dans la terminologie d’OData, il s’agit du *liaison*. Vous pouvez également avoir &quot;indépendant&quot; actions/fonctions sont appelées en tant qu’opérations statiques sur le service.

## <a name="example-adding-an-action"></a>Exemple : Ajout d’une Action

Nous allons définir une action pour évaluer un produit.

> [!NOTE]
> Ce didacticiel s’appuie sur le didacticiel [créer un Using ASP.NET Web API 2 OData v4 point de terminaison](create-an-odata-v4-endpoint.md)


Tout d’abord, ajoutez un `ProductRating` modèle pour représenter les évaluations.

[!code-csharp[Main](odata-actions-and-functions/samples/sample1.cs)]

Ajoutez également une **DbSet** à la `ProductsContext` classe, afin que EF crée une table de contrôle d’accès dans la base de données.

[!code-csharp[Main](odata-actions-and-functions/samples/sample2.cs)]

### <a name="add-the-action-to-the-edm"></a>Ajoutez l’Action à l’EDM

Dans WebApiConfig.cs, ajoutez le code suivant :

[!code-csharp[Main](odata-actions-and-functions/samples/sample3.cs)]

Le **EntityTypeConfiguration.Action** méthode ajoute une action à l’entity data model (EDM). Le **paramètre** méthode spécifie un paramètre typé pour l’action.

Ce code définit également l’espace de noms pour le modèle EDM. L’espace de noms est importante, car l’URI pour l’action inclut le nom qualifié complet :

[!code-console[Main](odata-actions-and-functions/samples/sample4.cmd)]

> [!NOTE]
> Dans une configuration IIS par défaut, le point de cette URL entraîne IIS retourner l’erreur 404. Vous pouvez résoudre ce problème en ajoutant la section suivante à votre fichier Web.Config :

[!code-xml[Main](odata-actions-and-functions/samples/sample5.xml)]

### <a name="add-a-controller-method-for-the-action"></a>Ajouter une méthode de contrôleur pour l’Action

Pour activer la &quot;taux&quot; action, ajoutez la méthode suivante à `ProductsController`:

[!code-csharp[Main](odata-actions-and-functions/samples/sample6.cs)]

Notez que le nom de méthode correspond au nom de l’action. Le **[HttpPost]** attribut spécifie la méthode est une méthode HTTP POST.

Pour appeler l’action, le client envoie une requête HTTP POST comme suit :

[!code-console[Main](odata-actions-and-functions/samples/sample7.cmd)]

Le &quot;taux&quot; action est liée à des instances de produit, par conséquent, l’URI de l’action est le nom qualifié complet des actions ajouté à l’URI de l’entité. (Rappelez-vous que nous avons défini l’espace de noms EDM &quot;ProductService&quot;, de sorte que le nom qualifié complet est &quot;ProductService.Rate&quot;.)

Le corps de la demande contient les paramètres d’action comme une charge utile JSON. API Web convertit automatiquement la charge JSON pour un **ODataActionParameters** objet, qui est simplement un dictionnaire de valeurs de paramètre. Ce dictionnaire permet d’accéder aux paramètres dans votre méthode de contrôleur.

Si le client envoie les paramètres d’action au bon format, la valeur de **ModelState.IsValid** a la valeur false. Contrôler cet indicateur dans votre méthode de contrôleur et de retourner une erreur si **IsValid** a la valeur false.

[!code-csharp[Main](odata-actions-and-functions/samples/sample8.cs)]

## <a name="example-adding-a-function"></a>Exemple : Ajout d’une fonction

Maintenant vous allez ajouter une fonction OData qui retourne le produit plus coûteux. Comme avant, la première étape ajoute la fonction dans le modèle EDM. Dans WebApiConfig.cs, ajoutez le code suivant.

[!code-csharp[Main](odata-actions-and-functions/samples/sample9.cs)]

Dans ce cas, la fonction est liée à la collection de produits, plutôt qu’à des instances individuelles de produit. Les clients appeler la fonction en envoyant une demande GET :

[!code-console[Main](odata-actions-and-functions/samples/sample10.cmd)]

Voici la méthode de contrôleur pour cette fonction :

[!code-csharp[Main](odata-actions-and-functions/samples/sample11.cs)]

Notez que le nom de méthode correspond au nom de fonction. Le **[HttpGet]** attribut spécifie la méthode est une méthode HTTP GET.

Voici la réponse HTTP :

[!code-console[Main](odata-actions-and-functions/samples/sample12.cmd)]

## <a name="example-adding-an-unbound-function"></a>Exemple : Ajout d’une fonction non liée

L’exemple précédent a une fonction liée à une collection. Dans l’exemple suivant, nous allons créer une *indépendant* (fonction). Indépendant de fonctions est appelé en tant qu’opérations statiques sur le service. La fonction dans cet exemple renvoie la taxe pour un code postal donné.

Dans le fichier WebApiConfig, ajoutez la fonction à l’EDM :

[!code-csharp[Main](odata-actions-and-functions/samples/sample13.cs)]

Notez que nous appelons **fonction** directement sur le **odatamodelbuilder ayant**, plutôt qu’au type d’entité ou de la collection. Le Générateur de modèles vous indique que la fonction est indépendante.

Voici la méthode du contrôleur qui implémente la fonction :

[!code-csharp[Main](odata-actions-and-functions/samples/sample14.cs)]

Il n’a pas d’importance quel contrôleur Web API vous placez cette méthode dans. Vous pouvez le placer `ProductsController`, ou définir un contrôleur distinct. Le **[ODataRoute]** attribut définit le modèle d’URI pour la fonction.

Voici un exemple de demande client :

[!code-console[Main](odata-actions-and-functions/samples/sample15.cmd)]

La réponse HTTP :

[!code-console[Main](odata-actions-and-functions/samples/sample16.cmd)]
