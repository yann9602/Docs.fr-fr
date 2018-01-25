---
uid: web-api/overview/error-handling/web-api-global-error-handling
title: Global Gestion des erreurs dans ASP.NET Web API 2 | Documents Microsoft
author: davidmatson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/03/2014
ms.topic: article
ms.assetid: bffd7863-f63b-4b23-a13c-372b5492e9fb
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/error-handling/web-api-global-error-handling
msc.type: authoredcontent
ms.openlocfilehash: c593c56ba3d0ee8ebf6dc425408d2c3b91c83f93
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="global-error-handling-in-aspnet-web-api-2"></a>Global Gestion des erreurs dans ASP.NET Web API 2
====================
par [David Matson](https://github.com/davidmatson), [Rick Anderson](https://github.com/Rick-Anderson)

Aujourd'hui il n’existe aucun moyen facile dans l’API Web pour se connecter ou gérer les erreurs globalement. Certaines exceptions non gérées peuvent être traitées [filtres d’exception](exception-handling.md), mais il existe un nombre de cas qui ne peut pas gérer les filtres d’exception. Exemple :

1. Exceptions levées à partir des constructeurs de contrôleur.
2. Exceptions levées à partir des gestionnaires de messages.
3. Exceptions levées pendant le routage.
4. Exceptions levées pendant la sérialisation de contenu de réponse.

Nous voulons fournir un moyen simple et cohérent pour vous connecter et gérer (le cas échéant) ces exceptions. 

Il existe deux cas principales pour la gestion des exceptions, le cas où nous sommes en mesure d’envoyer une réponse d’erreur et le cas où tout ce que nous pouvons faire est de l’exception de journal. Un exemple de ce dernier cas est lorsqu’une exception est levée au milieu de diffusion en continu de contenu de la réponse ; Dans ce cas il est trop tard pour envoyer un message de réponse dans la mesure où le code d’état, en-têtes et contenu partiel sont déjà passées via le câble, donc nous abandonner simplement la connexion. Même si l’exception ne peut pas être traitée pour produire un nouveau message de réponse, nous avons toujours prendre en charge la journalisation de l’exception. Dans les cas où nous pouvons détecter une erreur, nous pouvons retourner une réponse d’erreur approprié, comme indiqué dans les éléments suivants :

[!code-csharp[Main](web-api-global-error-handling/samples/sample1.cs?highlight=6)]

### <a name="existing-options"></a>Options existantes

En plus de [filtres d’exception](exception-handling.md), [gestionnaires de messages](../advanced/http-message-handlers.md) peut être utilisé dès aujourd'hui pour observer toutes les réponses de niveau 500, mais agissant sur les réponses est difficile, car ils ne disposent pas de contexte relatives à l’erreur d’origine. Gestionnaires de messages ont également certaines des limitations concernant les cas, elles peuvent gérer les filtres d’exception. Lors de l’API Web dispose d’une infrastructure de traçage qui capture les conditions d’erreur l’infrastructure de suivi est destiné à des fins de diagnostics et n'est pas conçu ou adapté pour l’exécution dans les environnements de production. Global Gestion des exceptions et journalisation doivent être des services qui peuvent s’exécuter lors de la production et être connectés à des solutions de contrôle existantes (par exemple, [ELMAH](https://code.google.com/p/elmah/) ).

### <a name="solution-overview"></a>Vue d’ensemble de la solution

 Nous fournir deux nouveaux services remplaçable par l’utilisateur, [IExceptionLogger](../releases/whats-new-in-aspnet-web-api-21.md) et IExceptionHandler, pour vous connecter et gérer les exceptions non gérées. Les services sont très similaires, et les deux principales différences :

1. Nous prenons en charge l’inscription de plusieurs enregistreurs d’événements exception, mais uniquement un seul gestionnaire d’exceptions.
2. Enregistreurs d’événements exception toujours appelées, même si nous sommes sur le point d’abandonner la connexion. Gestionnaires d’exceptions appelés uniquement lorsque nous sommes toujours en mesure de choisir le message de réponse à envoyer.

Les deux services offrent un accès à un contexte d’exception qui contient des informations pertinentes à partir du point où l’exception a été détectée, en particulier le [HttpRequestMessage](https://msdn.microsoft.com/library/system.net.http.httprequestmessage(v=vs.110).aspx), le [HttpRequestContext](https://msdn.microsoft.com/library/system.web.http.controllers.httprequestcontext(v=vs.118).aspx), la levée d’exception et la source de l’exception (détails ci-dessous).

### <a name="design-principles"></a>Principes de conception

1. **Aucune modification avec rupture** , car cette fonctionnalité est ajoutée dans une version mineure, une contrainte importante ayant un impact sur la solution est qu’il y avoir aucune modification avec rupture, soit vers le type de contrat ou un comportement. Cette contrainte exclu un nettoyage que nous aimerions avoir fait en termes de blocs catch existants activation des exceptions dans les 500 réponses. Ce nettoyage supplémentaire est quelque chose que nous pouvons prendre en compte pour une version majeure à venir. Si cela est important pour vous Veuillez Votez à [voix d’utilisateur ASP.NET Web API](http://aspnet.uservoice.com/forums/147201-asp-net-web-api/suggestions/5451321-add-flag-to-enable-iexceptionlogger-and-iexception).
2. **Maintenir la cohérence avec l’API Web construit** pipeline de filtre de l’API Web est un excellent moyen de gérer des problèmes transversaux avec la flexibilité de l’application de la logique dans une étendue spécifique à l’action globale ou contrôleur spécifique. Filtres, y compris les filtres d’exception, ont toujours des contextes d’action et de contrôleur, même lorsque l’inscrit dans la portée globale. Que le contrat de sens pour les filtres, mais cela signifie que les filtres d’exception, ceux qui sont de même portée, ne sont pas adapté à certains cas, par exemple, des exceptions à partir des gestionnaires de messages, exceptions où aucun contexte d’action ou contrôleur existe. Si vous souhaitez utiliser l’étendue flexible offertes par les filtres pour la gestion des exceptions, nous devons encore des filtres d’exception. Si nous avons besoin de gérer les exceptions en dehors d’un contexte de contrôleur, nous devons également une construction distincte pour gérer les erreurs complète global (quelque chose sans les contraintes de contexte de contrôleur contexte et action).

### <a name="when-to-use"></a>Quand utiliser

- Enregistreurs d’événements exception sont la solution de voir tous les exception non gérée interceptée par l’API Web.
- Gestionnaires d’exceptions sont la solution de personnalisation de toutes les réponses possibles aux exceptions non gérées interceptées par l’API Web.
- Filtres d’exception sont la solution la plus simple pour traiter les exceptions non prises en charge de sous-ensemble liées à une action spécifique ou un contrôleur.

### <a name="service-details"></a>Détails du service

 Les interfaces de service de journal et le Gestionnaire d’exception sont les méthodes async simple en prenant les contextes respectifs : 

[!code-csharp[Main](web-api-global-error-handling/samples/sample2.cs)]

 Nous fournissons également des classes de base pour ces deux interfaces. Substitution des méthodes core (synchronisation ou async) est tout ce qui est requis pour se connecter ou gérer à la fois. Pour la journalisation, la `ExceptionLogger` classe de base garantit que la méthode de journalisation principal est uniquement appelée une fois pour chaque exception (même si elle se propage vers ultérieurement plus haut dans la pile d’appel et est interceptée à nouveau). La `ExceptionHandler` classe de base appellera le cœur de méthode de gestion uniquement pour les exceptions en haut de la pile des appels, en ignorant hérité imbriquée catch (blocs). (Les versions simplifiées de ces classes de base sont dans l’annexe ci-dessous.) Les deux `IExceptionLogger` et `IExceptionHandler` recevoir des informations sur l’exception via un `ExceptionContext`.

[!code-csharp[Main](web-api-global-error-handling/samples/sample3.cs)]

Lorsque l’infrastructure appelle un enregistreur d’exceptions ou un gestionnaire d’exceptions, il sera toujours fournir un `Exception` et un `Request`. À l’exception des tests unitaires, il fournit également un `RequestContext`. Elle fournira rarement un `ControllerContext` et `ActionContext` (uniquement lors de l’appel du bloc catch pour les filtres d’exception). Très rarement fournira un `Response`(uniquement dans certains cas IIS lorsque, au milieu d’une tentative d’écriture de la réponse). Étant donné que certaines de ces propriétés peuvent être `null` il incombe au consommateur de vérifier les `null` avant d’accéder aux membres de la classe d’exception.`CatchBlock` est une chaîne indiquant le bloc catch vu l’exception. Les chaînes de bloc catch sont les suivantes :

- HttpServer (méthode SendAsync)
- HttpControllerDispatcher (méthode SendAsync)
- HttpBatchHandler (méthode SendAsync)
- IExceptionFilter (traitement du ApiController du pipeline de filtre d’exception dans ExecuteAsync)
- Hôte OWIN :

    - HttpMessageHandlerAdapter.BufferResponseContentAsync (pour la mise en mémoire tampon de sortie)
    - HttpMessageHandlerAdapter.CopyResponseContentAsync (pour la diffusion en continu de sortie)
- Hôte Web :

    - HttpControllerHandler.WriteBufferedResponseContentAsync (pour la mise en mémoire tampon de sortie)
    - HttpControllerHandler.WriteStreamedResponseContentAsync (pour la diffusion en continu de sortie)
    - HttpControllerHandler.WriteErrorResponseContentAsync (pour les défaillances de la récupération des erreurs en mode de mise en mémoire tampon de sortie)

La liste des chaînes de bloc catch est également disponible via les propriétés statiques en lecture seule. (La chaîne de bloc catch core se trouvent sur le ExceptionCatchBlocks statique ; le reste s’affichent dans une classe statique chaque pour OWIN et web hôte).`IsTopLevelCatchBlock` est utile pour suivre le modèle recommandé de la gestion des exceptions uniquement en haut de la pile des appels. Au lieu de l’activation des exceptions dans les 500 réponses partout où qu'un bloc catch imbriqué se produit, un gestionnaire d’exceptions peut propager exceptions jusqu'à ce qu’ils sont sur le point d’être vus par l’hôte.

Outre la `ExceptionContext`, un enregistreur d’événements Obtient une pièce d’informations via la version complète `ExceptionLoggerContext`:

[!code-csharp[Main](web-api-global-error-handling/samples/sample4.cs)]

La deuxième propriété, `CanBeHandled`, permet un enregistreur d’événements identifier une exception qui ne peut pas être gérée. Quand la connexion est sur le point d’être abandonnée et aucun nouveau message de réponse ne peut être envoyé, les enregistreurs d’événements seront appelées, mais le gestionnaire sera ***pas*** appelé, les enregistreurs d’événements peuvent d’identifier ce scénario à partir de cette propriété.

Dans supplémentaires pour le `ExceptionContext`, un gestionnaire obtient une seule propriété supplémentaire, elle peut donner à la version complète `ExceptionHandlerContext` pour gérer l’exception :

[!code-csharp[Main](web-api-global-error-handling/samples/sample5.cs)]

Un gestionnaire d’exception indique qu’il a géré une exception en définissant le `Result` propriété à un résultat d’action (par exemple, un [ExceptionResult](https://msdn.microsoft.com/library/system.web.http.results.exceptionresult(v=vs.118).aspx), [InternalServerErrorResult](https://msdn.microsoft.com/library/system.web.http.results.internalservererrorresult(v=vs.118).aspx), [ StatusCodeResult](https://msdn.microsoft.com/library/system.web.http.results.statuscoderesult(v=vs.118).aspx), ou un résultat personnalisé). Si le `Result` propriété a la valeur null, l’exception n’est pas gérée et l’exception d’origine sera levée de nouveau.

Pour les exceptions en haut de la pile des appels, nous avons effectué une étape supplémentaire pour garantir que la réponse est appropriée pour les appelants de l’API. Si l’exception se propage jusqu'à l’ordinateur hôte, l’appelant verriez l’écran jaune de décès ou un autre hôte fourni de réponse qui est en général, HTML et sont généralement pas une réponse d’erreur API appropriée. Dans ce cas, le démarrage de résultat non null, et uniquement si un gestionnaire d’exceptions personnalisé définit de manière explicite jusqu'à `null` (non gérées) sera l’exception se propager à l’ordinateur hôte. Paramètre `Result` à `null` dans ce cas, peut être utile pour deux scénarios :

1. OWIN hébergé API Web avec les exceptions personnalisées d’intergiciel (middleware) inscrit avant/extérieur API Web.
2. Le débogage local via un navigateur, où l’écran jaune de décès est réellement une réponse utile pour une exception non gérée.

Pour les gestionnaires d’exceptions et les enregistreurs d’événements exception, nous ne le faites pas tout récupérer si l’enregistreur d’événements ou d’un gestionnaire lui-même lève une exception. (Autre que de laisser l’exception se propager, laissez l’option commentaires au bas de cette page si vous avez une meilleure approche.) Le contrat pour les gestionnaires et les enregistreurs d’événements exception est qu’ils ne doivent pas propager exceptions jusqu'à leurs appelants ; Sinon, l’exception uniquement propagera, souvent à l’hôte, ce qui entraîne une erreur HTML (par exemple, la fonctionnalité d’ASP. Écran de jaune de NET) envoyé au client (qui ne pose généralement pas l’option recommandée pour les appelants d’API qui attendent JSON ou XML).

## <a name="examples"></a>Exemples

### <a name="tracing-exception-logger"></a>Enregistreur d’exceptions de suivi

L’enregistreur d’événements de l’exception ci-dessous envoyer des données d’exception pour les sources de Trace configurés (y compris la fenêtre de sortie de débogage dans Visual Studio).

[!code-csharp[Main](web-api-global-error-handling/samples/sample6.cs)]

### <a name="custom-error-message-exception-handler"></a>Gestionnaire d’exceptions Message erreur personnalisée

Ce qui suit génère une réponse d’erreur personnalisée pour les clients, y compris une adresse de messagerie pour contacter le support.

[!code-csharp[Main](web-api-global-error-handling/samples/sample7.cs)]

## <a name="registering-exception-filters"></a>L’inscription des filtres d’Exception

Si vous utilisez le modèle de projet « Application Web ASP.NET MVC 4 » pour créer votre projet, placez votre code de configuration de API Web à l’intérieur de la `WebApiConfig` de classe, dans le *application/_démarrer* dossier :

[!code-csharp[Main](exception-handling/samples/sample7.cs?highlight=5)]

## <a name="appendix-base-class-details"></a>L’annexe : Détails de classe de Base

[!code-csharp[Main](web-api-global-error-handling/samples/sample8.cs)]
