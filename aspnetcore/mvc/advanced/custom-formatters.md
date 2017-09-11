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
ms.openlocfilehash: 0285b40cfacb79745d3a6488401677130f55a95b
ms.sourcegitcommit: 6ece943781d8a56784bb6160f14da85210d3fcea
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2017
---
# <a name="custom-formatters-in-aspnet-core-mvc-web-apis"></a><span data-ttu-id="9c948-104">Formateurs personnalisés dans les API de web ASP.NET MVC de base</span><span class="sxs-lookup"><span data-stu-id="9c948-104">Custom formatters in ASP.NET Core MVC web APIs</span></span>

<span data-ttu-id="9c948-105">Par [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="9c948-105">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="9c948-106">ASP.NET Core MVC prend en charge pour l’échange de données dans l’API web à l’aide de formats JSON, XML ou texte brut.</span><span class="sxs-lookup"><span data-stu-id="9c948-106">ASP.NET Core MVC has built-in support for data exchange in web APIs by using JSON, XML, or plain text formats.</span></span> <span data-ttu-id="9c948-107">Cet article explique comment ajouter la prise en charge des formats supplémentaires en créant des formateurs personnalisés.</span><span class="sxs-lookup"><span data-stu-id="9c948-107">This article shows how to add support for additional formats by creating custom formatters.</span></span>

<span data-ttu-id="9c948-108">[Afficher ou télécharger l’exemple à partir de GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="9c948-108">[View or download sample from GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample).</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="9c948-109">Quand utiliser les formateurs personnalisés</span><span class="sxs-lookup"><span data-stu-id="9c948-109">When to use custom formatters</span></span>

<span data-ttu-id="9c948-110">Utiliser un formateur personnalisé lorsque vous souhaitez que le [négociation de contenu](xref:mvc/models/formatting) processus pour prendre en charge un type de contenu qui n’est pas pris en charge par les formateurs intégrés (JSON, XML et texte brut).</span><span class="sxs-lookup"><span data-stu-id="9c948-110">Use a custom formatter when you want the [content negotiation](xref:mvc/models/formatting) process to support a content type that isn't supported by the built-in formatters (JSON, XML, and plain text).</span></span>

<span data-ttu-id="9c948-111">Par exemple, si certains des clients de votre API web peuvent gérer le [Protobuf](https://github.com/google/protobuf) format, vous souhaiterez utiliser Protobuf avec ces clients car il est plus efficace.</span><span class="sxs-lookup"><span data-stu-id="9c948-111">For example, if some of the clients for your web API can handle the [Protobuf](https://github.com/google/protobuf) format, you might want to use Protobuf with those clients because it's more efficient.</span></span>  <span data-ttu-id="9c948-112">Vous pouvez également votre API web pour envoyer les noms de contact et les adresses de [vCard](https://en.wikipedia.org/wiki/VCard) format, un format couramment utilisé pour échanger des données de contact.</span><span class="sxs-lookup"><span data-stu-id="9c948-112">Or you might want your web API to send contact names and addresses in [vCard](https://en.wikipedia.org/wiki/VCard) format, a commonly used format for exchanging contact data.</span></span> <span data-ttu-id="9c948-113">L’exemple d’application fourni avec cet article implémente un formateur vCard simple.</span><span class="sxs-lookup"><span data-stu-id="9c948-113">The sample app provided with this article implements a simple vCard formatter.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="9c948-114">Vue d’ensemble de l’utilisation d’un formateur personnalisé</span><span class="sxs-lookup"><span data-stu-id="9c948-114">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="9c948-115">Voici les étapes pour créer et utiliser un formateur personnalisé :</span><span class="sxs-lookup"><span data-stu-id="9c948-115">Here are the steps to create and use a custom formatter:</span></span>

* <span data-ttu-id="9c948-116">Créer une classe de module de formatage de sortie si vous souhaitez sérialiser des données à envoyer au client.</span><span class="sxs-lookup"><span data-stu-id="9c948-116">Create an output formatter class if you want to serialize data to send to the client.</span></span>
* <span data-ttu-id="9c948-117">Créer une classe de formateur d’entrée si vous souhaitez désérialiser des données reçues du client.</span><span class="sxs-lookup"><span data-stu-id="9c948-117">Create an input formatter class if you want to deserialize data received from the client.</span></span> 
* <span data-ttu-id="9c948-118">Ajouter des instances de vos modules de formatage de la `InputFormatters` et `OutputFormatters` collections dans [MvcOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.mvcoptions).</span><span class="sxs-lookup"><span data-stu-id="9c948-118">Add instances of your formatters to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

<span data-ttu-id="9c948-119">Les sections suivantes fournissent des conseils et des exemples de code pour chacune de ces étapes.</span><span class="sxs-lookup"><span data-stu-id="9c948-119">The following sections provide guidance and code examples for each of these steps.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="9c948-120">Comment créer une classe de formateur personnalisé</span><span class="sxs-lookup"><span data-stu-id="9c948-120">How to create a custom formatter class</span></span>

<span data-ttu-id="9c948-121">Pour créer un module de formatage :</span><span class="sxs-lookup"><span data-stu-id="9c948-121">To create a formatter:</span></span>

* <span data-ttu-id="9c948-122">Dérivez la classe de la classe de base appropriée.</span><span class="sxs-lookup"><span data-stu-id="9c948-122">Derive the class from the appropriate base class.</span></span>
* <span data-ttu-id="9c948-123">Spécifier les encodages et types de support valide dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="9c948-123">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="9c948-124">Substituer `CanReadType` / `CanWriteType` méthodes</span><span class="sxs-lookup"><span data-stu-id="9c948-124">Override `CanReadType`/`CanWriteType` methods</span></span>
* <span data-ttu-id="9c948-125">Substituer `ReadRequestBodyAsync` / `WriteResponseBodyAsync` méthodes</span><span class="sxs-lookup"><span data-stu-id="9c948-125">Override `ReadRequestBodyAsync`/`WriteResponseBodyAsync` methods</span></span>
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="9c948-126">Dérive de la classe de base appropriée</span><span class="sxs-lookup"><span data-stu-id="9c948-126">Derive from the appropriate base class</span></span>

<span data-ttu-id="9c948-127">Pour les types de média texte (par exemple, vCard) dérivent la [TextInputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) ou [TextOutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) classe de base.</span><span class="sxs-lookup"><span data-stu-id="9c948-127">For text media types (for example, vCard), derive from the [TextInputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

<span data-ttu-id="9c948-128">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]</span><span class="sxs-lookup"><span data-stu-id="9c948-128">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]</span></span>

<span data-ttu-id="9c948-129">Pour les types binaires, dérivent de la [InputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.inputformatter) ou [OutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformatter) classe de base.</span><span class="sxs-lookup"><span data-stu-id="9c948-129">For binary types, derive from the [InputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="9c948-130">Spécifier les encodages et types de média valide</span><span class="sxs-lookup"><span data-stu-id="9c948-130">Specify valid media types and encodings</span></span>

<span data-ttu-id="9c948-131">Dans le constructeur, spécifiez les encodages et types de support valide en ajoutant à la `SupportedMediaTypes` et `SupportedEncodings` collections.</span><span class="sxs-lookup"><span data-stu-id="9c948-131">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

<span data-ttu-id="9c948-132">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]</span><span class="sxs-lookup"><span data-stu-id="9c948-132">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]</span></span>

> [!NOTE]  
> <span data-ttu-id="9c948-133">Vous ne pouvez pas faire d’injection de dépendance de constructeur dans une classe de formateur.</span><span class="sxs-lookup"><span data-stu-id="9c948-133">You can't do constructor dependency injection in a formatter class.</span></span> <span data-ttu-id="9c948-134">Par exemple, Impossible d’obtenir un journal en ajoutant un paramètre de l’enregistreur d’événements au constructeur.</span><span class="sxs-lookup"><span data-stu-id="9c948-134">For example, you can't get a logger by adding a logger parameter to the constructor.</span></span> <span data-ttu-id="9c948-135">Pour accéder aux services, vous devez utiliser l’objet de contexte transmis à vos méthodes.</span><span class="sxs-lookup"><span data-stu-id="9c948-135">To access services, you have to use the context object that gets passed in to your methods.</span></span> <span data-ttu-id="9c948-136">Un exemple de code [ci-dessous](#read-write) montre comment effectuer cette opération.</span><span class="sxs-lookup"><span data-stu-id="9c948-136">A code example [below](#read-write) shows how to do this.</span></span>

### <a name="override-canreadtypecanwritetype"></a><span data-ttu-id="9c948-137">Substituer CanReadType/CanWriteType</span><span class="sxs-lookup"><span data-stu-id="9c948-137">Override CanReadType/CanWriteType</span></span> 

<span data-ttu-id="9c948-138">Spécifiez le type, vous pouvez désérialiser dans ou à partir de sérialiser en substituant le `CanReadType` ou `CanWriteType` méthodes.</span><span class="sxs-lookup"><span data-stu-id="9c948-138">Specify the type you can deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="9c948-139">Par exemple, vous seulement pourrez créer du texte de carte de visite à partir d’un `Contact` type et vice versa.</span><span class="sxs-lookup"><span data-stu-id="9c948-139">For example, you might only be able to create vCard text from a `Contact` type and vice versa.</span></span>

<span data-ttu-id="9c948-140">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]</span><span class="sxs-lookup"><span data-stu-id="9c948-140">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]</span></span>

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="9c948-141">La méthode CanWriteResult</span><span class="sxs-lookup"><span data-stu-id="9c948-141">The CanWriteResult method</span></span>

<span data-ttu-id="9c948-142">Dans certains scénarios, vous devez substituer `CanWriteResult` au lieu de `CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="9c948-142">In some scenarios you have to override `CanWriteResult` instead of `CanWriteType`.</span></span> <span data-ttu-id="9c948-143">Utilisez `CanWriteResult` si les conditions suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="9c948-143">Use `CanWriteResult` if the following conditions are true:</span></span>

  * <span data-ttu-id="9c948-144">Votre méthode d’action retourne une classe de modèle.</span><span class="sxs-lookup"><span data-stu-id="9c948-144">Your action method returns a model class.</span></span>
  * <span data-ttu-id="9c948-145">Il existe des classes dérivées qui peuvent être retournés lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="9c948-145">There are derived classes which might be returned at runtime.</span></span>
  * <span data-ttu-id="9c948-146">Vous devez connaître lors de l’exécution qui dérivée classe a été retournée par l’action.</span><span class="sxs-lookup"><span data-stu-id="9c948-146">You need to know at runtime which derived class was returned by the action.</span></span>  

<span data-ttu-id="9c948-147">Par exemple, supposons que votre signature de méthode d’action retourne un `Person` type, mais il peut retourner un `Student` ou `Instructor` type qui dérive de `Person`.</span><span class="sxs-lookup"><span data-stu-id="9c948-147">For example, suppose your action method signature returns a `Person` type, but it may return a `Student` or `Instructor` type that derives from `Person`.</span></span> <span data-ttu-id="9c948-148">Si vous souhaitez que votre formateur pour gérer uniquement `Student` objets, vérifiez le type de [objet](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) dans l’objet de contexte fourni à la `CanWriteResult` (méthode).</span><span class="sxs-lookup"><span data-stu-id="9c948-148">If you want your formatter to handle only `Student` objects, check the type of [Object](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="9c948-149">Notez qu’il n’est pas nécessaire d’utiliser `CanWriteResult` lorsque la méthode d’action retourne `IActionResult`; dans ce cas, le `CanWriteType` méthode reçoit le type de runtime.</span><span class="sxs-lookup"><span data-stu-id="9c948-149">Note that it's not necessary to use `CanWriteResult` when the action method returns `IActionResult`; in that case, the `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>
### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a><span data-ttu-id="9c948-150">Substituer ReadRequestBodyAsync/WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="9c948-150">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span></span> 

<span data-ttu-id="9c948-151">C’est le travail réel de la sérialisation ou désérialisation dans `ReadRequestBodyAsync` ou `WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="9c948-151">You do the actual work of deserializing or serializing in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span>  <span data-ttu-id="9c948-152">Les lignes en surbrillance dans l’exemple suivant affichent comment obtenir des services à partir du conteneur d’injection de dépendance (ne peut pas avoir de paramètres du constructeur).</span><span class="sxs-lookup"><span data-stu-id="9c948-152">The highlighted lines in the following example show how to get services from the dependency injection container (you can't get them from constructor parameters).</span></span>

<span data-ttu-id="9c948-153">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]</span><span class="sxs-lookup"><span data-stu-id="9c948-153">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]</span></span>

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="9c948-154">Comment configurer MVC pour utiliser un formateur personnalisé</span><span class="sxs-lookup"><span data-stu-id="9c948-154">How to configure MVC to use a custom formatter</span></span>
 
<span data-ttu-id="9c948-155">Pour utiliser un formateur personnalisé, ajoutez une instance de la classe du formateur pour la `InputFormatters` ou `OutputFormatters` collection.</span><span class="sxs-lookup"><span data-stu-id="9c948-155">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

<span data-ttu-id="9c948-156">[!code-csharp[Main](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]</span><span class="sxs-lookup"><span data-stu-id="9c948-156">[!code-csharp[Main](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]</span></span>

<span data-ttu-id="9c948-157">Formateurs sont évalués dans l’ordre de qu'insertion.</span><span class="sxs-lookup"><span data-stu-id="9c948-157">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="9c948-158">La première est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="9c948-158">The first one takes precedence.</span></span> 

## <a name="next-steps"></a><span data-ttu-id="9c948-159">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="9c948-159">Next steps</span></span>

<span data-ttu-id="9c948-160">Consultez le [exemple d’application](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample), qui implémente la vCard simple entrée et sortie formateurs.</span><span class="sxs-lookup"><span data-stu-id="9c948-160">See the [sample application](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample), which implements simple vCard input and output formatters.</span></span>  <span data-ttu-id="9c948-161">L’application lit et écrit des cartes de visite qui ressemblent à l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="9c948-161">The application reads and writes vCards that look like the following example:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

<span data-ttu-id="9c948-162">Pour afficher la vCard de sortie, exécutez l’application et envoyer une demande Get avec accepter en-tête « texte/vcard » à `http://localhost:63313/api/contacts/` (lors de l’exécution à partir de Visual Studio) ou `http://localhost:5000/api/contacts/` (lors de l’exécution à partir de la ligne de commande).</span><span class="sxs-lookup"><span data-stu-id="9c948-162">To see vCard output, run the application and send a Get request with Accept header "text/vcard" to `http://localhost:63313/api/contacts/` (when running from Visual Studio) or `http://localhost:5000/api/contacts/` (when running from the command line).</span></span>

<span data-ttu-id="9c948-163">Pour ajouter une carte de visite à la collection en mémoire des contacts, envoyez une demande Post à la même URL, avec l’en-tête Content-Type « texte/vcard » et texte vCard dans le corps, mis en forme comme l’exemple ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="9c948-163">To add a vCard to the in-memory collection of contacts, send a Post request to the same URL, with Content-Type header "text/vcard" and with vCard text in the body, formatted like the example above.</span></span>
