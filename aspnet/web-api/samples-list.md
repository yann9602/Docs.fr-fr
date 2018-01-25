---
uid: web-api/samples-list
title: "Exemples d’API Web liste | Documents Microsoft"
author: rick-anderson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/18/2012
ms.topic: article
ms.assetid: 8cbd9d7f-7027-4390-b098-cb81a63ecd6f
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/samples-list
msc.type: content
ms.openlocfilehash: 1e1f43bbeedfc052f0b3a3924f51b544a5a79dca
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="web-api-samples-list"></a>Liste d’exemples API Web
====================
## <a name="httpclient-samples"></a>Exemples de HttpClient

**Exemple de traduire Bing** | [source de Visual Studio 2012](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet45%2fCS%2fHttpClient%2fBingTranslateSample%2fReadMe.txt)

Montre comment appeler le [service de Microsoft Translator](https://msdn.microsoft.com/library/ff512419.aspx) à l’aide de la **HttpClient** classe. L’API du service Microsoft Translator requiert un jeton OAuth, qui obtient de l’application en envoyant une demande au serveur de jeton Azure pour chaque demande au service de traduction. Le résultat à partir du serveur de jeton est chargé dans la demande envoyée au service de traduction. Avant d’exécuter cet exemple, vous devez obtenir un [clé d’application à partir d’Azure Marketplace](https://msdn.microsoft.com/library/hh454950.aspx) et renseignez les informations contenues dans l’exemple de classe AccessTokenMessageHandler.

**Exemple de Google Maps** | [description détaillée](https://blogs.msdn.com/b/henrikn/archive/2012/02/17/downloading-a-google-map-to-local-file.aspx) | [source de Visual Studio 2012](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet45%2fCS%2fHttpClient%2fGoogleMapsSample%2fReadMe.txt)

Utilise **HttpClient** pour télécharger un mappage de Redmond, WA de [Google Maps API](https://developers.google.com/maps/)enregistre sous la forme d’un fichier local et ouvre la visionneuse d’images par défaut.

**Twitter Client exemple** | [description détaillée](https://blogs.msdn.com/b/henrikn/archive/2012/02/16/extending-httpclient-with-oauth-to-access-twitter.aspx) | [source de Visual Studio 2012](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet45%2fCS%2fHttpClient%2fTwitterSample%2fReadMe.txt)

Montre comment écrire un client Twitter simple à l’aide de **HttpClient**. L’exemple utilise un **HttpMessageHandler** pour insérer des informations d’authentification OAuth dans sortant **HttpRequestMessage**. Le résultat de Twitter est lue à l’aide de JSON.NET. Avant d’exécuter cet exemple, vous devez obtenir un [clé d’application de Twitter](https://dev.twitter.com/)et renseignez les informations contenues dans l’exemple de classe OAuthMessageHandler.

**Exemple de la banque mondiale** | [description détaillée](https://blogs.msdn.com/b/henrikn/archive/2012/02/16/httpclient-is-here.aspx) | [source de Visual Studio 2010](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet4%2fCS%2fHttpClient%2fWorldBankSample%2fReadMe.txt) | [source de Visual Studio 2012](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet45%2fCS%2fHttpClient%2fWorldBankSample%2fReadMe.txt)

Montre comment récupérer des données à partir du site de données de banque mondiale, à l’aide de JSON.NET pour analyser le résultat.

## <a name="web-api-samples"></a>Exemples d’API Web

**Mise en route avec ASP.NET Web API** | [source de Visual Studio 2012](overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md)

Montre comment créer une base web API qui prend en charge les requêtes HTTP GET. Contient le code source pour le didacticiel [votre première ASP.NET Web API](overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md).

**Scénarios de JavaScript de l’API Web ASP.NET – commentaires** | [source de Visual Studio 2012](https://code.msdn.microsoft.com/ASPNET-Web-API-JavaScript-d0d64dd7)

Montre comment utiliser l’API Web ASP.NET pour générer les API qui prennent en charge les clients de navigateur et peut être facilement appelé à l’aide de jQuery web.

**Le Gestionnaire de contacts** | [source de Visual Studio 2010](https://code.msdn.microsoft.com/Contact-Manager-Web-API-0e8e373d)

Cet exemple utilise l’API Web ASP.NET pour créer une application du Gestionnaire de contacts simple. L’application se compose d’un gestionnaire de contacts API web qui est utilisé par une application ASP.NET MVC et une application Windows Phone pour afficher et gérer une liste de contacts.

**Exemple de traitement par lot** | [description détaillée](http://trocolate.wordpress.com/2012/07/19/mitigate-issue-260-in-batching-scenario/) | [source de Visual Studio 2012](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet45%2fCS%2fWebApi%2fHostedBatchSample%2fReadMe.txt)

Montre comment implémenter le traitement par lot de HTTP au sein d’ASP.NET. Le traitement par lots se compose de placer plusieurs requêtes HTTP au sein d’un corps unique de l’entité en plusieurs parties MIME, qui sont ensuite envoyées au serveur en tant qu’une requête HTTP POST. Les demandes sont traitées individuellement, et les réponses sont placés dans un autre corps MIME en plusieurs parties d’entité, qui est retourné au client.

**Exemple de contrôleur de contenu** | [description détaillée](https://blogs.msdn.com/b/henrikn/archive/2012/02/24/async-actions-in-asp-net-web-api.aspx) | [source de Visual Studio 2010](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet4%2fCS%2fWebApi%2fContentControllerSample%2fReadMe.txt) | [source de Visual Studio 2012](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet45%2fCS%2fWebApi%2fContentControllerSample%2fReadMe.txt)

Montre comment lire et écrire des entités de demande et de réponse en mode asynchrone à l’aide de flux de données. Le contrôleur de l’exemple a deux actions : une action de PUT qui lit le corps d’entité de demande de façon asynchrone et le stocke dans un fichier local et une action GET qui retourne le contenu du fichier local.

**Exemple de programme de résolution d’Assembly personnalisé** | [source de Visual Studio 2012](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet45%2fCS%2fWebApi%2fCustomAssemblyResolverSample%2fReadMe.txt)

Montre comment modifier les API de Web ASP.NET pour prendre en charge la découverte de contrôleurs à partir d’un assembly de bibliothèque chargée dynamiquement. L’exemple implémente un personnalisé **IAssembliesResolver** qui appelle l’implémentation par défaut, puis l’ajoute l’assembly de bibliothèque pour les résultats par défaut.

**Exemple de formateur de Type média personnalisé** | [description détaillée](https://blogs.msdn.com/b/henrikn/archive/2012/04/23/using-cookies-with-asp-net-web-api.aspx) | [source de Visual Studio 2010](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet4%2fCS%2fWebApi%2fCustomMediaTypeFormatterSample%2fReadMe.txt)

Montre comment créer un formateur de type de média personnalisé à l’aide de la **BufferedMediaTypeFormatter** classe de base. Cette classe de base est conçue pour les modules de formatage principalement en lecture synchrone et les opérations d’écriture qui. En plus de l’affichage formateur de type de média, l’exemple montre comment raccorder en l’inscrivant dans le cadre de la **HttpConfiguration** pour votre application. Notez qu’il est également possible d’utiliser le **MediaTypeFormatter** classe de base directement, pour les formateurs qui utilisent principalement asynchrone de lecture et les opérations d’écriture.

**Exemple de liaison de paramètre personnalisé** | [description détaillée](https://blogs.msdn.com/b/jmstall/archive/2012/05/11/webapi-parameter-binding-under-the-hood.aspx) | [source de Visual Studio 2010](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet4%2fCS%2fWebApi%2fCustomParameterBinding%2fReadMe.txt)

Montre comment personnaliser le processus de liaison de paramètre, qui est le processus qui détermine comment les informations à partir d’une demande sont liées aux paramètres d’action. Dans cet exemple, le contrôleur Home a quatre actions :

1. BindPrincipal montre comment lier un paramètre IPrincipal à partir d’un principal générique personnalisé, non à partir d’un message HTTP GET.
2. BindCustomComplexTypeFromUriOrBody montre comment lier un paramètre de type complexe, qui peut provenir du corps du message ou de la demande URI d’un message HTTP POST.
3. BindCustomComplexTypeFromUriWithRenamedProperty montre comment lier un paramètre de type complexe avec une propriété renommé qui provient de la demande URI d’un message HTTP POST.
4. PostMultipleParametersFromBody montre comment lier plusieurs paramètres à partir du corps d’un message POST ;

**Télécharger un exemple de fichier** | [description détaillée](https://blogs.msdn.com/b/henrikn/archive/2012/03/01/file-upload-and-asp-net-web-api.aspx) | [source de Visual Studio 2012](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet45%2fCS%2fWebApi%2fFileUploadSample%2fReadMe.txt)

Montre comment télécharger des fichiers à un **ApiController** à l’aide du téléchargement de fichier MIME à parties multiples et comment configurer des notifications de progression avec **HttpClient** à l’aide de **ProgressNotificationHandler**. Le contrôleur lit le contenu d’un téléchargement de fichier HTML de manière asynchrone et écrit une ou plusieurs parties de corps dans un fichier local. La réponse contient des informations sur le fichier téléchargé (ou fichiers).

**Fichier de téléchargement dans l’exemple Azure Blob Store** | [description détaillée](https://blogs.msdn.com/b/yaohuang1/archive/2012/07/02/asp-net-web-api-and-azure-blob-storage.aspx) | [source de Visual Studio 2012](http://aspnet.codeplex.com/SourceControl/changeset/view/61dfed023e50#Samples%2fNet45%2fCS%2fWebApi%2fAzureBlobsFileUploadSample%2fReadMe.txt)

Cet exemple est semblable à l’exemple de téléchargement de fichier, mais au lieu d’enregistrer les fichiers téléchargés sur le disque local, elle charge de façon asynchrone les fichiers à [magasin d’objets Blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-how-to-use-blobs) à l’aide de [Windows Azure SDK pour .NET](https://www.windowsazure.com/develop/net/). Il fournit également un mécanisme permettant de répertorier les objets BLOB présents dans un [le conteneur de stockage d’objets Blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-how-to-use-blobs). Vous pouvez essayer de l’exemple en cours d’exécution par rapport à **émulateur de stockage Azure** qui est livré avec le Kit de développement logiciel Azure. Si vous avez un [compte de stockage Azure](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-how-to-use-blobs), vous pouvez exécuter sur le service de stockage réel également.

**Exemple de Pipeline gestionnaire HTTP Message** | [description détaillée](https://blogs.msdn.com/b/henrikn/archive/2012/08/07/httpclient-httpclienthandler-and-httpwebrequesthandler.aspx) | [source de Visual Studio 2010](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet4%2fCS%2fWebApi%2fHttpMessageHandlerPipelineSample%2fReadMe.txt)

Montre comment associer **HttpMessageHandler** instances sur le client (**HttpClient**) et serveur (ASP.NET Web API). Dans l’exemple, le même gestionnaire est utilisé sur le client et le serveur. Il est rare que le même gestionnaire exact s’exécute dans deux emplacements, le modèle objet est le même sur le client et côté serveur.

**Exemple de JSON télécharger** | [source de Visual Studio 2012](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet45%2fCS%2fWebApi%2fJsonUploadSample%2fReadMe.txt)

Montre comment charger et télécharger JSON vers et depuis un **ApiController**. L’exemple utilise un minimum **ApiController** et y accède à l’aide de **HttpClient**.

**Exemple d’application Web hybride** | [description détaillée](https://blogs.msdn.com/b/henrikn/archive/2012/03/03/async-mashups-using-asp-net-web-api.aspx) | [source de Visual Studio 2012](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet45%2fCS%2fWebApi%2fMashupSample%2fReadMe.txt)

Montre comment accéder de façon asynchrone plusieurs sites à distance à partir d’un **ApiController** action. Chaque fois que l’action est atteint, les requêtes sont exécutées de façon asynchrone, afin qu’aucun threads ne sont bloqués.

**Exemple de traçage de mémoire** | [description détaillée](https://blogs.msdn.com/b/roncain/archive/2012/04/12/tracing-in-asp-net-web-api.aspx) | [source de Visual Studio 2010](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet4%2fCS%2fWebApi%2fMemoryTracingSample%2fReadMe.txt)

Cet exemple de projet crée un package Nuget qui installera un writer de suivi personnalisé de mémoire dans les applications API Web ASP.NET.

**Exemple de MongoDB** | [description détaillée](https://blogs.msdn.com/b/henrikn/archive/2012/02/19/using-web-api-with-mongodb.aspx) | [source de Visual Studio 2012](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet45%2fCS%2fWebApi%2fMongoSample%2fReadMe.txt)

Montre comment utiliser MongoDB en tant que le magasin persistant pour une **ApiController**, à l’aide d’un modèle de référentiel.

**Exemple de traitement du corps de réponse** | [source de Visual Studio 2012](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet45%2fCS%2fWebApi%2fResponseEntityProcessorSample%2fReadMe.txt)

Montre comment copier une entité de réponse (autrement dit, un corps de réponse HTTP) dans un fichier local avant d’être transmis au client et effectuer un traitement supplémentaire sur ce fichier de façon asynchrone. L’exemple implémente un **HttpMessageHandler** qui encapsule l’entité de réponse avec une qu’à la fois écrit elle-même à la sortie normale et dans un fichier local.

**Télécharger l’exemple de XDocument** | [description détaillée](https://blogs.msdn.com/b/henrikn/archive/2012/02/17/push-and-pull-streams-using-httpclient.aspx) | [source de Visual Studio 2012](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet45%2fCS%2fWebApi%2fUploadXDocumentSample%2fReadMe.txt)

Montre comment télécharger un XDocument à un **ApiController** à l’aide de **PushStreamContent** et **HttpClient**.

**Exemple de validation de** | [source de Visual Studio 2010](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet4%2fCS%2fWebApi%2fValidationSample%2fReadMe.txt)

Montre comment utiliser des attributs de validation sur vos modèles dans ASP.NET WebAPI pour valider le contenu de la requête HTTP. Montre comment marquer les propriétés comme requis, comment utiliser les deux définies par l’infrastructure et les attributs de validation personnalisés pour annoter votre modèle et comment retourner des réponses d’erreur pour les États de modèles non valide.

**Exemple de formulaire de Web** | [description détaillée](https://blogs.msdn.com/b/henrikn/archive/2012/02/23/using-asp-net-web-api-with-asp-net-web-forms.aspx) | [source de Visual Studio 2010](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet4%2fCS%2fWebApi%2fWebFormSample%2fReadMe.txt)

Montre un ApiController ajouté à un projet Web Forms.

**[Exemple de RestBugs](https://github.com/howarddierking/RestBugs)**

RestBugs est une application qui montre comment utiliser l’API Web ASP.NET et la nouvelle bibliothèque HTTP Client pour créer un système piloté par les hypermédia de suivi des bogues simple. L’exemple inclut des implémentations de client et le serveur, à l’aide des API Web ASP.NET. Le serveur utilise un formateur de Razor personnalisé pour générer des représentations sous forme de ressources. L’exemple fournit également un serveur node.js pour illustrer les avantages d’utiliser une conception hypermédia découpler les clients et serveurs.

## <a name="web-api-extensions-preview-samples"></a>Exemples d’API Web Extensions Preview

**Exemple d’utilisable dans une requête OData** | [description détaillée](https://blogs.msdn.com/b/alexj/archive/2012/08/15/odata-support-in-asp-net-web-api.aspx) | [source de Visual Studio 2010](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet4%2fCS%2fWebApi%2fODataQueryableSample%2fReadMe.txt)

Montre comment introduire des requêtes OData dans ASP.NET Web API à l’aide la `[Queryable]` attribut, ou à l’aide de la **ODataQueryOptions** paramètre d’action qui permet l’action à inspecter manuellement la requête avant son exécution.

Le CustomerController illustre l’utilisation de [Queryable] attribut et le OrderController montre comment utiliser le paramètre ODataQueryOptions. Le ResponseController est similaire à la CustomerController, mais au lieu de l’action GET retour `IEnumerable<Customer>` , elle retourne un **HttpResponseMessage**. Cela nous permet d’ajouter des champs d’en-tête supplémentaire, manipuler le code d’état, etc. tout en utilisant la fonctionnalité de requête. L’exemple illustre des requêtes à l’aide de $orderby, $skip, $top, any(), all() et $filter.

**Exemple de Service OData** | [description détaillée](https://blogs.msdn.com/b/alexj/archive/2012/08/15/odata-support-in-asp-net-web-api.aspx) | [source de Visual Studio 2010](http://aspnet.codeplex.com/SourceControl/changeset/view/15dfe7e0759f#Samples%2fNet4%2fCS%2fWebApi%2fODataServiceSample%2fReadMe.txt)

Cet exemple illustre comment créer un service OData comprenant trois entités et trois contrôleurs Web API. Les contrôleurs fournissent différents niveaux de fonctionnalités en termes de la fonctionnalité de OData qu’ils exposent :

Le SupplierController expose un sous-ensemble de fonctionnalités, y compris la requête, par clé et de créer, d’obtenir en gérant ces demandes :

- OBTENIR /Suppliers
- OBTENIR /Suppliers(key)
- GET /Suppliers ? $filter =... &amp;$orderby =... &amp;$top =... &amp;$skip =...
- POST /Suppliers

L’expose ProductsController GET, PUT, POST, supprime et de correctif logiciel en mettant en œuvre une action pour chacune de ces opérations directement.

Le ProductFamilesController tire parti de la classe de base EntitySetController qui expose un modèle utile pour implémenter un service OData rich.

En outre le service OData expose un document $metadata qui permet les données consommés par les clients du Service de données WCF et d’autres clients qui acceptent le format $metadata.
