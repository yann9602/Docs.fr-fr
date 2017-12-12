---
title: Migration des gestionnaires HTTP et des modules pour ASP.NET Core intergiciel (middleware)
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: tdykstra
manager: wpickett
ms.date: 12/07/2016
ms.topic: article
ms.assetid: 9c826a76-fbd2-46b5-978d-6ca6df53531a
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/http-modules
ms.openlocfilehash: f217e5264742826f285444dcbaea4b28b97c4d7e
ms.sourcegitcommit: 8f42ab93402c1b8044815e1e48d0bb84c81f8b59
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="migrating-http-handlers-and-modules-to-aspnet-core-middleware"></a>Migration des gestionnaires HTTP et des modules pour ASP.NET Core intergiciel (middleware) 

Par [Matt Perdeck](https://www.linkedin.com/in/mattperdeck)

Cet article explique comment migrer ASP.NET existants [modules HTTP et des gestionnaires à partir de system.webserver](https://docs.microsoft.com/iis/configuration/system.webserver/) à ASP.NET Core [intergiciel (middleware)](../fundamentals/middleware.md).

## <a name="modules-and-handlers-revisited"></a>Modules et les gestionnaires revisitées

Avant de procéder à un intergiciel (middleware) ASP.NET Core, tout d’abord récapitulons le fonctionnement des gestionnaires et des modules HTTP :

![Gestionnaire de modules](http-modules/_static/moduleshandlers.png)

**Les gestionnaires sont :**

   * Classes qui implémentent [IHttpHandler](https://docs.microsoft.com/dotnet/api/system.web.ihttphandler)

   * Permet de gérer les demandes avec un nom de fichier donné ou une extension, tel que *report*

   * [Configuré](https://docs.microsoft.com//iis/configuration/system.webserver/handlers/) dans *Web.config*

**Les modules sont :**

   * Classes qui implémentent [IHttpModule](https://docs.microsoft.com/dotnet/api/system.web.ihttpmodule)

   * Appelé pour chaque demande

   * La possibilité de court-circuit (interrompre le traitement d’une demande)

   * En mesure d’ajouter à la réponse HTTP, ou créer leurs propres

   * [Configuré](https://docs.microsoft.com//iis/configuration/system.webserver/modules/) dans *Web.config*

**L’ordre dans lequel les modules de traitent les demandes entrantes est déterminé par :**

   1. Le [cycle de vie d’application](https://msdn.microsoft.com/library/ms227673.aspx), qui est représenté par une série déclenchés par ASP.NET : [BeginRequest](https://docs.microsoft.com/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](https://docs.microsoft.com/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Chaque module peut créer un gestionnaire pour un ou plusieurs événements.

   2. Pour le même événement, l’ordre dans lequel ils sont configurés dans *Web.config*.

En plus des modules, vous pouvez ajouter des gestionnaires pour les événements de cycle de vie à votre *Global.asax.cs* fichier. Ces gestionnaires exécutés après les gestionnaires dans les modules configurés.

## <a name="from-handlers-and-modules-to-middleware"></a>À partir des gestionnaires et des modules pour intergiciel (middleware)

**Intergiciel (middleware) sont plus simples que les gestionnaires et modules HTTP :**

   * Modules, gestionnaires, *Global.asax.cs*, *Web.config* (à l’exception de la configuration d’IIS) et le cycle de vie d’application ont disparu

   * Les rôles des modules et des gestionnaires ont été prises en charge par l’intergiciel (middleware)

   * Intergiciel (middleware) sont configurés à l’aide de code plutôt que dans *Web.config*

   * [Créer une branche de pipeline](../fundamentals/middleware.md#middleware-run-map-use) vous permet d’envoyer les demandes à un intergiciel (middleware) de spécifique, en fonction de non seulement l’URL, mais également sur les en-têtes de demande, les chaînes de requête, etc.

**Intergiciel (middleware) sont très semblables aux modules :**

   * Appelé en principe pour chaque demande

   * La possibilité de court-circuit une demande, par [ne pas passer la demande à l’intergiciel (middleware) suivant](#http-modules-shortcircuiting-middleware)

   * La possibilité de créer leur propres réponse HTTP

**Intergiciel (middleware) et les modules sont traités dans un ordre différent :**

   * Ordre des intergiciels (middleware) est basé sur l’ordre dans lequel ils sont insérés dans le pipeline de demande, tandis que l’ordre des modules est principalement basé sur [cycle de vie d’application](https://msdn.microsoft.com/library/ms227673.aspx) événements

   * Ordre de l’intergiciel (middleware) pour les réponses est l’inverse de celui pour les demandes, tandis que l’ordre des modules est le même pour les demandes et réponses

   * Consultez [création d’un pipeline de l’intergiciel (middleware) avec IApplicationBuilder](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder)

![Intergiciel (middleware)](http-modules/_static/middleware.png)

Notez comment, dans l’image ci-dessus, l’intergiciel (middleware) d’authentification court-circuité la demande.

## <a name="migrating-module-code-to-middleware"></a>Migration de code de module pour un intergiciel (middleware)

Un module HTTP existant doit ressembler à ceci :

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

Comme indiqué dans le [intergiciel (middleware)](../fundamentals/middleware.md) page, un intergiciel (middleware) ASP.NET Core est une classe qui expose un `Invoke` prise de méthode un `HttpContext` et en retournant un `Task`. Votre nouveau middleware doit ressembler à ceci :

<a name="http-modules-usemiddleware"></a>

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

Le modèle d’intergiciel (middleware) ci-dessus a été effectuée à partir de la section sur [l’écriture d’intergiciel (middleware)](../fundamentals/middleware.md#middleware-writing-middleware).

Le *MyMiddlewareExtensions* classe d’assistance rend plus facile à configurer votre intergiciel (middleware) dans votre `Startup` classe. Le `UseMyMiddleware` méthode ajoute votre classe d’intergiciel (middleware) au pipeline de demande. Les services requis par l’intergiciel (middleware) injectés dans le constructeur de d’intergiciel (middleware).

<a name="http-modules-shortcircuiting-middleware"></a>

Votre module peut mettre fin à une demande, par exemple, si l’utilisateur n’est pas autorisé :

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

Un intergiciel (middleware) gère cela en appelant ne pas `Invoke` sur l’intergiciel (middleware) suivant dans le pipeline. Gardez à l’esprit que cela ne termine pas entièrement la demande, car middlewares précédente est toujours appelé lorsque la réponse effectue sa manière via le pipeline.

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

Lorsque vous migrez des fonctionnalités de votre module vers votre intergiciel (middleware) de nouveau, vous souhaiterez peut-être que votre code ne se compile pas, car la `HttpContext` classe a été considérablement modifiée dans ASP.NET Core. [Plus tard](#migrating-to-the-new-httpcontext), vous allez apprendre à migrer à nouveau ASP.NET Core HttpContext.

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>Migration insertion de module dans le pipeline de demande

Les modules HTTP sont généralement ajoutés au pipeline de demande à l’aide de *Web.config*:

[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

Convertir en [ajoutant votre intergiciel (middleware) nouvelle](../fundamentals/middleware.md#creating-a-middleware-pipeline-with-iapplicationbuilder) vers le pipeline de demande dans votre `Startup` classe :

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

La position exacte dans le pipeline où vous insérez votre intergiciel (middleware) nouvelle dépend de l’événement qu’il est géré en tant que module (`BeginRequest`, `EndRequest`, etc.) et son ordre dans la liste des modules dans *Web.config*.

Comme précédemment indiqué, il n’est aucun cycle de vie des applications dans ASP.NET Core et l’ordre dans lequel les réponses sont traitées par un intergiciel (middleware) diffère de l’ordre utilisé par les modules. Cela pourrait rendre votre décision de tri plus complexe.

Si le tri devient un problème, vous pourriez fractionner votre module dans plusieurs composants d’intergiciel (middleware) qui peuvent être classés de manière indépendante.

## <a name="migrating-handler-code-to-middleware"></a>Migration de code de gestionnaire pour l’intergiciel (middleware)

Un gestionnaire HTTP ressemble à ceci :

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

Dans votre projet ASP.NET Core, vous serez traduire à un intergiciel (middleware) similaire à ceci :

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

Cet intergiciel (middleware) est très similaire à l’intergiciel (middleware) correspondant aux modules. La seule différence est qu’ici n’est pas appelée pour `_next.Invoke(context)`. C’est logique, car le gestionnaire n’est à la fin du pipeline de demande, il y aura aucune intergiciel (middleware) suivant à appeler.

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>Migration insertion de gestionnaire dans le pipeline de demande

Configuration d’un gestionnaire HTTP est effectuée dans *Web.config* et ressemble à ceci :

[!code-xml[Main](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

Vous pouvez le convertir en ajoutant votre nouvelle intergiciel (middleware) gestionnaire au pipeline de demande dans votre `Startup` (classe), similaire à un intergiciel (middleware) converti à partir de modules. Le problème avec cette approche est qu’il envoie toutes les demandes à votre nouvelle intergiciel (middleware) gestionnaire. Toutefois, vous seulement souhaitez que les demandes avec une extension donnée pour atteindre votre intergiciel (middleware). Qui permet d’obtenir les mêmes fonctionnalités que vous aviez avec votre gestionnaire HTTP.

Une solution consiste à créer une branche le pipeline pour les demandes avec une extension donnée, à l’aide de la `MapWhen` méthode d’extension. Cela dans le même `Configure` méthode dans laquelle vous ajoutez l’autres intergiciel (middleware) :

[!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen`utilise ces paramètres :

1. Une expression lambda qui prend le `HttpContext` et retourne `true` si la demande doit être transmis vers le bas de la branche. Cela signifie que vous pouvez créer une branche de demandes non seulement en fonction de leur extension, mais également sur les en-têtes de demande, les paramètres de chaîne de requête, etc.

2. Une expression lambda qui prend un `IApplicationBuilder` et ajoute tous les intergiciels (middleware) de la branche. Cela signifie que vous pouvez ajouter des intergiciels (middleware) supplémentaire à la branche devant votre intergiciel (middleware) gestionnaire.

Intergiciel (middleware) sont ajoutés au pipeline avant la branche sera appelée sur toutes les demandes ; la branche aura aucun impact sur ces derniers.

## <a name="loading-middleware-options-using-the-options-pattern"></a>Options d’intergiciel (middleware) à l’aide du modèle d’options de chargement

Certains modules et les gestionnaires offrent des options de configuration qui sont stockées dans *Web.config*. Toutefois, un nouveau modèle de configuration dans ASP.NET Core est utilisé à la place de *Web.config*.

La nouvelle [système de configuration](xref:fundamentals/configuration/index) vous offre des options suivantes pour résoudre ce problème :

* Injecter directement les options dans l’intergiciel (middleware), comme indiqué dans le [section suivante](#loading-middleware-options-through-direct-injection).

* Utilisez le [modèle d’options](xref:fundamentals/configuration/options):

1.  Créez une classe pour contenir les options de l’intergiciel (middleware), par exemple :

    [!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2.  Stocker les valeurs d’option

    Le système de configuration vous permet de vous permet de stocker des valeurs d’option partout où que vous souhaitez. Toutefois, les sites plus utiliser *appsettings.json*, donc nous allons cette approche :

    [!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

    *MyMiddlewareOptionsSection* ici est un nom de section. Il ne doit pas être le même que le nom de votre classe d’options.

3. Associer les valeurs d’option de la classe d’options

    Le modèle d’options utilise infrastructure d’injection de dépendance de ASP.NET Core pour associer le type options (telles que `MyMiddlewareOptions`) avec un `MyMiddlewareOptions` présentant les options de.

    Mise à jour votre `Startup` classe :

    1.  Si vous utilisez *appsettings.json*, ajoutez-le au Générateur de configuration dans le `Startup` constructeur :

      [!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

    2.  Configurer le service d’options :

      [!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    3.  Associer vos options à votre classe d’options :

      [!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4.  Injecter les options dans votre constructeur d’intergiciel (middleware). Cela est similaire à l’injection d’options dans un contrôleur.

  [!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

  Le [UseMiddleware](#http-modules-usemiddleware) méthode d’extension qui ajoute votre intergiciel (middleware) pour le `IApplicationBuilder` prend en charge l’injection de dépendances.

  Cela n’est pas limitée à `IOptions` objets. De cette manière peut être ajoutée à n’importe quel autre objet nécessitant votre intergiciel (middleware).

## <a name="loading-middleware-options-through-direct-injection"></a>Chargement des options d’intergiciel (middleware) par le biais d’injection directe

Le modèle d’options présente l’avantage qu’il crée le faible couplage entre les valeurs des options et leurs consommateurs. Une fois que vous avez associé une classe d’options avec les valeurs d’options réel, toute autre classe peut accéder aux options via l’infrastructure d’injection de dépendance. Il n’est pas nécessaire de passer autour des valeurs d’options.

Cela répartit bien que si vous souhaitez utiliser l’intergiciel (middleware) même à deux reprises, avec différentes options. Par exemple un intergiciel d’autorisation utilisé dans différentes branches autorisant les différents rôles. Vous ne pouvez pas associer deux objets de différentes options la classe d’une options.

La solution consiste à obtenir les objets d’options avec les valeurs d’options réelles dans votre `Startup` classe et transfèrent ces informations directement à chaque instance de votre intergiciel (middleware).

1.  Ajouter une deuxième clé à *appsettings.json*

    Pour ajouter un deuxième ensemble d’options pour le *appsettings.json* , utilisez une nouvelle clé pour identifier de manière unique :

    [!code-json[Main](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2.  Récupérer les valeurs des options et les passer à un intergiciel (middleware). Le `Use...` méthode d’extension (ce qui ajoute votre intergiciel (middleware) au pipeline) est un emplacement logique pour transmettre les valeurs d’option : 

    [!code-csharp[Main](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

4.  Activer l’intergiciel (middleware) prendre un paramètre options. Fournissez une surcharge de la `Use...` méthode d’extension (qui prend le paramètre options et passe à `UseMiddleware`). Lorsque `UseMiddleware` est appelée avec des paramètres, il transmet les paramètres à votre constructeur d’intergiciel (middleware) lorsqu’il instancie l’objet de l’intergiciel (middleware).

    [!code-csharp[Main](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

    Notez comment cela encapsule l’objet d’options dans un `OptionsWrapper` objet. Il implémente `IOptions`, comme prévu par le constructeur d’intergiciel (middleware).

## <a name="migrating-to-the-new-httpcontext"></a>Migration vers le nouveau HttpContext

Vous savez que la `Invoke` méthode dans votre intergiciel (middleware) prend un paramètre de type `HttpContext`:

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext`a changé de manière significative dans ASP.NET Core. Cette section montre comment traduire les propriétés couramment utilisées de [System.Web.HttpContext](https://docs.microsoft.com/dotnet/api/system.web.httpcontext) au nouveau `Microsoft.AspNetCore.Http.HttpContext`.

### <a name="httpcontext"></a>HttpContext

**HttpContext.Items** se traduit par :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**ID de demande unique (pas d’équivalent System.Web.HttpContext)**

Vous donne un id unique pour chaque demande. Très utiles à inclure dans vos journaux.

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext.Request

**HttpContext.Request.HttpMethod** se traduit par :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**HttpContext.Request.QueryString** se traduit par :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**HttpContext.Request.Url** et **HttpContext.Request.RawUrl** traduire en :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext.Request.IsSecureConnection** se traduit par :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext.Request.UserHostAddress** se traduit par :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext.Request.Cookies** se traduit par :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**HttpContext.Request.RequestContext.RouteData** se traduit par :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**HttpContext.Request.Headers** se traduit par :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**HttpContext.Request.UserAgent** se traduit par :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**HttpContext.Request.UrlReferrer** se traduit par :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpContext.Request.ContentType** se traduit par :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpContext.Request.Form** se traduit par :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> Lire les valeurs de formulaire uniquement si le type de contenu sub est *x--www-form-urlencoded* ou *des données de formulaire*.

**HttpContext.Request.InputStream** se traduit par :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> Utilisez ce code uniquement dans un gestionnaire type intergiciel (middleware), à la fin d’un pipeline.
>
>Vous pouvez lire le corps brut comme indiqué plus haut qu’une seule fois par requête. Intergiciel (middleware) tente de lire le corps après la première lecture lira le corps est vide.
>
>Cela ne s’applique pas à la lecture d’un formulaire comme indiqué précédemment, qui est effectuée à partir d’une mémoire tampon.

### <a name="httpcontextresponse"></a>HttpContext.Response

**HttpContext.Response.Status** et **HttpContext.Response.StatusDescription** traduire en :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**HttpContext.Response.ContentEncoding** et **HttpContext.Response.ContentType** traduire en :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

**HttpContext.Response.ContentType** sur son propre également se traduit par :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpContext.Response.Output** se traduit par :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext.Response.TransmitFile**

Accès à un fichier est décrite [ici](../fundamentals/request-features.md#middleware-and-request-features).

**HttpContext.Response.Headers**

Envoi des en-têtes de réponse est compliqué par le fait que si vous leur attribuez une fois que tout a été écrite dans le corps de réponse, ils n’être envoyées.

La solution consiste à définir une méthode de rappel qui sera appelée droite avant d’écrire sur le démarrage de la réponse. Cette opération s’effectue mieux au début de la `Invoke` méthode dans votre intergiciel (middleware). Il s’agit de cette méthode de rappel qui définit les en-têtes de réponse.

Le code suivant définit une méthode de rappel appelée `SetHeaders`:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

Le `SetHeaders` méthode de rappel ressemble à ceci :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext.Response.Cookies**

Les cookies sont transmis au navigateur dans un *Set-Cookie* en-tête de réponse. Par conséquent, envoi de cookies nécessite le rappel de même que celle utilisée pour l’envoi des en-têtes de réponse :

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

Le `SetCookies` méthode de rappel se présente comme suit :

[!code-csharp[Main](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [Vue d’ensemble des Modules HTTP et les gestionnaires HTTP](https://docs.microsoft.com/iis/configuration/system.webserver/)

* [Configuration](xref:fundamentals/configuration/index)

* [Démarrage d’une application](../fundamentals/startup.md)

* [Intergiciel (middleware)](../fundamentals/middleware.md)
