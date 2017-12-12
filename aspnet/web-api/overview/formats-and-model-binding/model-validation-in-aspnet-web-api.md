---
uid: web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api
title: "Modèle de Validation dans ASP.NET Web API | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/20/2012
ms.topic: article
ms.assetid: 7d061207-22b8-4883-bafa-e89b1e7749ca
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api
msc.type: authoredcontent
ms.openlocfilehash: dc91ddb64294e686825076d5bcc636766f2f6f01
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="model-validation-in-aspnet-web-api"></a>Validation de modèle dans l’API Web ASP.NET
====================
par [Mike Wasson](https://github.com/MikeWasson)

Lorsqu’un client envoie des données à votre API web, vous souhaitez souvent valider les données avant de procéder à un traitement. Cet article explique comment annoter vos modèles, utiliser des annotations pour la validation des données et gérer les erreurs de validation dans votre API web.

## <a name="data-annotations"></a>Annotations de données

Dans ASP.NET Web API, vous pouvez utiliser des attributs à partir de la [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.aspx) espace de noms pour définir des règles de validation pour les propriétés de votre modèle. Considérez le modèle suivant :

[!code-csharp[Main](model-validation-in-aspnet-web-api/samples/sample1.cs)]

Si vous avez utilisé la validation de modèle dans ASP.NET MVC, cela doit sembler familière. Le **requis** attribut indique que le `Name` propriété ne doit pas être null. Le **plage** attribut indique que `Weight` doit être comprise entre 0 et 999.

Supposons qu’un client envoie une requête POST avec la représentation JSON suivante :

[!code-json[Main](model-validation-in-aspnet-web-api/samples/sample2.json)]

Vous pouvez voir que le client n’inclut pas le `Name` propriété, qui est marquée comme requis. Lorsque les API Web convertit le JSON dans un `Product` instance, il valide le `Product` sur les attributs de validation. Dans l’action de contrôleur, vous pouvez vérifier si le modèle est valid :

[!code-csharp[Main](model-validation-in-aspnet-web-api/samples/sample3.cs)]

Validation du modèle ne garantit pas que les données du client seront sécurisées. Une validation supplémentaire peut être nécessaire dans les autres couches de l’application. (Par exemple, la couche données peut appliquer des contraintes de clé étrangères.) Le didacticiel [à l’aide des API Web avec Entity Framework](../data/using-web-api-with-entity-framework/part-1.md) explore certains de ces problèmes.

**« Validation incomplète »**: incomplète de validation se produit lorsque le client quitte certaines propriétés. Par exemple, supposons que le client envoie les éléments suivants :

[!code-json[Main](model-validation-in-aspnet-web-api/samples/sample4.json)]

Ici, le client n’a pas spécifié des valeurs pour `Price` ou `Weight`. Le module de formatage JSON affecte une valeur par défaut de zéro pour les propriétés manquantes.

![](model-validation-in-aspnet-web-api/_static/image1.png)

L’état du modèle est valide, car le zéro est une valeur valide pour ces propriétés. S’il s’agit d’un problème dépend de votre scénario. Par exemple, dans une opération de mise à jour, vous souhaiterez faire la distinction entre « zéro » et « non définie ». Pour forcer les clients pour définir une valeur, vérifiez la propriété nullable et définissez la **requis** attribut :

[!code-csharp[Main](model-validation-in-aspnet-web-api/samples/sample5.cs?highlight=1-2)]

**« Trop de validation »**: un client peut également envoyer *plus* données que prévu. Exemple :

[!code-json[Main](model-validation-in-aspnet-web-api/samples/sample6.json)]

Ici, l’objet JSON inclut une propriété (« couleur ») qui n’existe pas dans la `Product` modèle. Dans ce cas, le module de formatage JSON ignore simplement cette valeur. (Le formateur XML fait de même.) Validation excessive entraîne des problèmes si votre modèle comporte des propriétés que vous souhaitez être en lecture seule. Exemple :

[!code-csharp[Main](model-validation-in-aspnet-web-api/samples/sample7.cs)]

Vous ne souhaitez pas aux utilisateurs de mettre à jour le `IsAdmin` propriété, eux-mêmes l’élévation pour les administrateurs ! La stratégie plus sûre consiste à utiliser une classe de modèle qui correspond exactement à ce que le client est autorisé à envoyer :

[!code-csharp[Main](model-validation-in-aspnet-web-api/samples/sample8.cs)]

> [!NOTE]
> Billet de blog de Brad Wilson »[vs de Validation d’entrée. Modèle de Validation dans ASP.NET MVC](http://bradwilson.typepad.com/blog/2010/01/input-validation-vs-model-validation-in-aspnet-mvc.html)« a une discussion de bon de validation incomplète et de validation excessive. Bien que la publication est sur ASP.NET MVC 2, les problèmes sont toujours applicables à l’API Web.


## <a name="handling-validation-errors"></a>La gestion des erreurs de Validation

API Web ne renvoie pas automatiquement de d’erreur au client lorsque la validation échoue. C’est à l’action du contrôleur pour vérifier l’état du modèle et de réagir de façon appropriée.

Vous pouvez également créer un filtre d’action pour vérifier l’état du modèle avant l’appel de l’action du contrôleur. Le code suivant montre un exemple :

[!code-csharp[Main](model-validation-in-aspnet-web-api/samples/sample9.cs)]

Si l’échec de la validation du modèle, ce filtre retourne une réponse HTTP qui contient les erreurs de validation. Dans ce cas, l’action du contrôleur n’est pas appelée.

[!code-console[Main](model-validation-in-aspnet-web-api/samples/sample10.cmd)]

Pour appliquer ce filtre à tous les contrôleurs de l’API Web, ajoutez une instance du filtre à la **HttpConfiguration.Filters** collection lors de la configuration :

[!code-csharp[Main](model-validation-in-aspnet-web-api/samples/sample11.cs)]

Une autre option consiste à définir le filtre en tant qu’attribut sur les contrôleurs individuels ou des actions de contrôleur :

[!code-csharp[Main](model-validation-in-aspnet-web-api/samples/sample12.cs)]
