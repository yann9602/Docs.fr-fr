---
title: "Formateurs personnalisés dans les API de web ASP.NET MVC de base"
author: tdykstra
description: "Découvrez comment créer et utiliser des formateurs personnalisés pour l’API web dans ASP.NET Core."
keywords: "ASP.NET Core, web api, formateurs personnalisés"
ms.author: tdykstra
manager: wpickett
ms.date: 02/08/2017
ms.topic: article
ms.assetid: 1fb6fdc2-e199-4469-9012-b909d1913422
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/custom-formatters
ms.openlocfilehash: 792e007232c751d3db9dc5e50adbedfb2bb1a7ae
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="custom-formatters-in-aspnet-core-mvc-web-apis"></a>Formateurs personnalisés dans les API de web ASP.NET MVC de base

Par [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core MVC prend en charge pour l’échange de données dans l’API web à l’aide de formats JSON, XML ou texte brut. Cet article explique comment ajouter la prise en charge des formats supplémentaires en créant des formateurs personnalisés.

[Afficher ou télécharger l’exemple à partir de GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample).

## <a name="when-to-use-custom-formatters"></a>Quand utiliser les formateurs personnalisés

Utiliser un formateur personnalisé lorsque vous souhaitez que le [négociation de contenu](xref:mvc/models/formatting) processus pour prendre en charge un type de contenu qui n’est pas pris en charge par les formateurs intégrés (JSON, XML et texte brut).

Par exemple, si certains des clients de votre API web peuvent gérer le [Protobuf](https://github.com/google/protobuf) format, vous souhaiterez utiliser Protobuf avec ces clients car il est plus efficace.  Vous pouvez également votre API web pour envoyer les noms de contact et les adresses de [vCard](https://wikipedia.org/wiki/VCard) format, un format couramment utilisé pour échanger des données de contact. L’exemple d’application fourni avec cet article implémente un formateur vCard simple.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Vue d’ensemble de l’utilisation d’un formateur personnalisé

Voici les étapes pour créer et utiliser un formateur personnalisé :

* Créer une classe de module de formatage de sortie si vous souhaitez sérialiser des données à envoyer au client.
* Créer une classe de formateur d’entrée si vous souhaitez désérialiser des données reçues du client. 
* Ajouter des instances de vos modules de formatage de la `InputFormatters` et `OutputFormatters` collections dans [MvcOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.mvcoptions).

Les sections suivantes fournissent des conseils et des exemples de code pour chacune de ces étapes.

## <a name="how-to-create-a-custom-formatter-class"></a>Comment créer une classe de formateur personnalisé

Pour créer un module de formatage :

* Dérivez la classe de la classe de base appropriée.
* Spécifier les encodages et types de support valide dans le constructeur.
* Substituer `CanReadType` / `CanWriteType` méthodes
* Substituer `ReadRequestBodyAsync` / `WriteResponseBodyAsync` méthodes
  
### <a name="derive-from-the-appropriate-base-class"></a>Dérive de la classe de base appropriée

Pour les types de média texte (par exemple, vCard) dérivent la [TextInputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) ou [TextOutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) classe de base.

[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

Pour les types binaires, dérivent de la [InputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.inputformatter) ou [OutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformatter) classe de base.

### <a name="specify-valid-media-types-and-encodings"></a>Spécifier les encodages et types de média valide

Dans le constructeur, spécifiez les encodages et types de support valide en ajoutant à la `SupportedMediaTypes` et `SupportedEncodings` collections.

[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

> [!NOTE]  
> Vous ne pouvez pas faire d’injection de dépendance de constructeur dans une classe de formateur. Par exemple, Impossible d’obtenir un journal en ajoutant un paramètre de l’enregistreur d’événements au constructeur. Pour accéder aux services, vous devez utiliser l’objet de contexte transmis à vos méthodes. Un exemple de code [ci-dessous](#read-write) montre comment effectuer cette opération.

### <a name="override-canreadtypecanwritetype"></a>Substituer CanReadType/CanWriteType 

Spécifiez le type, vous pouvez désérialiser dans ou à partir de sérialiser en substituant le `CanReadType` ou `CanWriteType` méthodes. Par exemple, vous seulement pourrez créer du texte de carte de visite à partir d’un `Contact` type et vice versa.

[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

#### <a name="the-canwriteresult-method"></a>La méthode CanWriteResult

Dans certains scénarios, vous devez substituer `CanWriteResult` au lieu de `CanWriteType`. Utilisez `CanWriteResult` si les conditions suivantes sont remplies :

  * Votre méthode d’action retourne une classe de modèle.
  * Il existe des classes dérivées qui peuvent être retournés lors de l’exécution.
  * Vous devez connaître lors de l’exécution qui dérivée classe a été retournée par l’action.  

Par exemple, supposons que votre signature de méthode d’action retourne un `Person` type, mais il peut retourner un `Student` ou `Instructor` type qui dérive de `Person`. Si vous souhaitez que votre formateur pour gérer uniquement `Student` objets, vérifiez le type de [objet](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) dans l’objet de contexte fourni à la `CanWriteResult` (méthode). Notez qu’il n’est pas nécessaire d’utiliser `CanWriteResult` lorsque la méthode d’action retourne `IActionResult`; dans ce cas, le `CanWriteType` méthode reçoit le type de runtime.

<a id="read-write"></a>
### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a>Substituer ReadRequestBodyAsync/WriteResponseBodyAsync 

C’est le travail réel de la sérialisation ou désérialisation dans `ReadRequestBodyAsync` ou `WriteResponseBodyAsync`.  Les lignes en surbrillance dans l’exemple suivant affichent comment obtenir des services à partir du conteneur d’injection de dépendance (ne peut pas avoir de paramètres du constructeur).

[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Comment configurer MVC pour utiliser un formateur personnalisé
 
Pour utiliser un formateur personnalisé, ajoutez une instance de la classe du formateur pour la `InputFormatters` ou `OutputFormatters` collection.

[!code-csharp[Main](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

Formateurs sont évalués dans l’ordre de qu'insertion. La première est prioritaire. 

## <a name="next-steps"></a>Étapes suivantes

Consultez le [exemple d’application](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample), qui implémente la vCard simple entrée et sortie formateurs.  L’application lit et écrit des cartes de visite qui ressemblent à l’exemple suivant :

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

Pour afficher la vCard de sortie, exécutez l’application et envoyer une demande Get avec accepter en-tête « texte/vcard » à `http://localhost:63313/api/contacts/` (lors de l’exécution à partir de Visual Studio) ou `http://localhost:5000/api/contacts/` (lors de l’exécution à partir de la ligne de commande).

Pour ajouter une carte de visite à la collection en mémoire des contacts, envoyez une demande Post à la même URL, avec l’en-tête Content-Type « texte/vcard » et texte vCard dans le corps, mis en forme comme l’exemple ci-dessus.
