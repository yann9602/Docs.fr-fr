---
uid: aspnet/overview/owin-and-katana/owin-middleware-in-the-iis-integrated-pipeline
title: "Intergiciel OWIN dans IIS intégré pipeline | Documents Microsoft"
author: Praburaj
description: "Cet article explique comment exécuter les composants d’intergiciel (middleware) OWIN (OMCs) dans le pipeline intégré IIS, et comment définir l’événement de pipeline un OMC s’exécute sur. Tu devrais..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/07/2013
ms.topic: article
ms.assetid: d031c021-33c2-45a5-bf9f-98f8fa78c2ab
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/owin-and-katana/owin-middleware-in-the-iis-integrated-pipeline
msc.type: authoredcontent
ms.openlocfilehash: 4e1270f9fb58032d22380117f4ec18b00bd725fc
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="owin-middleware-in-the-iis-integrated-pipeline"></a>Intergiciel OWIN dans le pipeline intégré IIS
====================
par [Praburaj Thiagarajan](https://github.com/Praburaj), [Rick Anderson](https://github.com/Rick-Anderson)

> Cet article explique comment exécuter les composants d’intergiciel (middleware) OWIN (OMCs) dans le pipeline intégré IIS, et comment définir l’événement de pipeline un OMC s’exécute sur. Vous devez passer en revue [une vue d’ensemble du projet Katana](an-overview-of-project-katana.md) et [détection de classe de démarrage OWIN](owin-startup-class-detection.md) avant la lecture de ce didacticiel. Ce didacticiel a été rédigé par Rick Anderson ( [ @RickAndMSFT ](https://twitter.com/#!/RickAndMSFT) ), Chris Ross Praburaj Thiagarajan et Howard Dierking ( [ @howard \_dierking](https://twitter.com/howard_dierking) ).


Bien que [OWIN](an-overview-of-project-katana.md) composants d’intergiciel (middleware) (OMCs) sont principalement conçus pour s’exécuter dans un pipeline indépendant du serveur, il est possible d’exécuter un OMC dans le pipeline intégré IIS ainsi (**en mode classique est *pas* pris en charge**). Un OMC peut être défini pour travailler dans le pipeline intégré IIS en installant le package suivant à partir de la Console Gestionnaire de Package (PMC) :

[!code-console[Main](owin-middleware-in-the-iis-integrated-pipeline/samples/sample1.cmd)]

Cela signifie que toutes les infrastructures d’application, y compris ceux qui ne sont pas encore en mesure d’exécuter en dehors d’IIS et System.Web, peuvent tirer parti des composants d’intergiciel (middleware) OWIN existants. 

> [!NOTE]
> Tous les `Microsoft.Owin.Security.*` packages expédition avec le nouveau système d’identité dans Visual Studio 2013 (par exemple : Cookies Account Microsoft, Google, Facebook, Twitter, [jetons de support](http://self-issued.info/docs/draft-ietf-oauth-v2-bearer.html), OAuth, serveur d’autorisation, JSON, Azure Active Directory, Active Directory federation services) sont créés en tant que OMCs et peuvent être utilisé dans les scénarios auto-hébergés et hébergés dans IIS.

## <a name="how-owin-middleware-executes-in-the-iis-integrated-pipeline"></a>Comment intergiciel (middleware) OWIN s’exécute dans le Pipeline intégré IIS

Pour les applications de console OWIN, le pipeline d’application générée à l’aide du [configuration de démarrage](owin-startup-class-detection.md) est définie par l’ordre les composants sont ajoutés à l’aide de la `IAppBuilder.Use` (méthode). Autrement dit, le pipeline OWIN dans le [Katana](an-overview-of-project-katana.md) runtime traitera OMCs dans l’ordre qu’ils ont été inscrits à l’aide de `IAppBuilder.Use`. Dans le pipeline intégré IIS, le pipeline de requête se compose de [HttpModules](https://msdn.microsoft.com/library/ms178468(v=vs.85).aspx) abonné à un ensemble prédéfini des événements de pipeline, tel que [BeginRequest](https://msdn.microsoft.com/library/system.web.httpapplication.beginrequest.aspx), [AuthenticateRequest](https://msdn.microsoft.com/library/system.web.httpapplication.authenticaterequest.aspx), [AuthorizeRequest](https://msdn.microsoft.com/library/system.web.httpapplication.authorizerequest.aspx), etc.

Si nous comparer un OMC à celle d’un [HttpModule](https://msdn.microsoft.com/library/zec9k340(v=vs.85).aspx) dans le monde d’ASP.NET, un OMC doit être inscrit à l’événement correct de pipeline prédéfinies. Par exemple, HttpModule `MyModule` sera invoquée lorsqu’une requête arrive la [AuthenticateRequest](https://msdn.microsoft.com/library/system.web.httpapplication.authenticaterequest.aspx) étape du pipeline :

[!code-csharp[Main](owin-middleware-in-the-iis-integrated-pipeline/samples/sample2.cs?highlight=10)]

Dans l’ordre pour un OMC devant être inclus dans cette même sur des événements, l’exécution de l’organisation, la [Katana](an-overview-of-project-katana.md) code du runtime parcourt la [configuration de démarrage](owin-startup-class-detection.md) et s’abonne à chacun des composants d’intergiciel (middleware) à un événements du pipeline intégré. Par exemple, le code suivant OMC et l’inscription permet de vous permet de voir l’enregistrement d’événements par défaut des composants d’intergiciel (middleware). (Pour plus d’instructions sur la création d’une classe de démarrage OWIN, consultez [détection de classe de démarrage OWIN](owin-startup-class-detection.md).)

1. Créez un projet d’application web vide et nommez-la **owin2**.
2. À partir de Package Manager Console (PMC), exécutez la commande suivante : 

    [!code-console[Main](owin-middleware-in-the-iis-integrated-pipeline/samples/sample3.cmd)]
3. Ajouter un `OWIN Startup Class` et nommez-le `Startup`. Remplacez le code généré par le code suivant (les modifications sont mises en surbrillance) :  

    [!code-csharp[Main](owin-middleware-in-the-iis-integrated-pipeline/samples/sample4.cs?highlight=5-7,15-36)]
4. Appuyez sur F5 pour exécuter l’application.

La configuration de démarrage définit un pipeline avec les composants d’intergiciel (middleware) trois, les deux premières affichant des informations de diagnostic et la dernière réponse à des événements (et en affichant des informations de diagnostic). Le `PrintCurrentIntegratedPipelineStage` méthode affiche l’événement de pipeline intégré appelé cet intergiciel (middleware) et un message. Les fenêtres de sortie affiche les informations suivantes :

[!code-console[Main](owin-middleware-in-the-iis-integrated-pipeline/samples/sample5.cmd)]

Le runtime Katana mappés chacun des composants d’intergiciel (middleware) OWIN pour [PreExecuteRequestHandler](https://msdn.microsoft.com/library/system.web.httpapplication.prerequesthandlerexecute.aspx) par défaut, ce qui correspond à l’événement de pipeline IIS [PreRequestHandlerExecute](https://msdn.microsoft.com/library/system.web.httpapplication.prerequesthandlerexecute.aspx).

## <a name="stage-markers"></a>Marqueurs d’étape

Vous pouvez marquer OMCs à exécuter à plusieurs étapes du pipeline à l’aide de la `IAppBuilder UseStageMarker()` méthode d’extension. Pour exécuter un ensemble de composants d’intergiciel (middleware) au cours d’une étape spécifique, insérez un marqueur d’étape juste après le dernier composant est l’ensemble lors de l’inscription. Il existe des règles sur l’étape du pipeline à laquelle vous pouvez exécuter intergiciel (middleware) et les composants de la commande doivent exécuter (les règles sont expliqués plus loin dans ce didacticiel). Ajouter le `UseStageMarker` méthode à la `Configuration` de code comme indiqué ci-dessous :

[!code-csharp[Main](owin-middleware-in-the-iis-integrated-pipeline/samples/sample6.cs?highlight=13,19)]

Le `app.UseStageMarker(PipelineStage.Authenticate)` appel configure tous les composants d’intergiciel (middleware) précédemment inscrites (dans ce cas, les deux composants de diagnostic) pour s’exécuter sur l’étape d’authentification du pipeline. Le dernier composant d’intergiciel (middleware) (qui affiche des diagnostics et répond aux demandes) s’exécutera sur le `ResolveCache` stade (le [ResolveRequestCache](https://msdn.microsoft.com/library/system.web.httpapplication.resolverequestcache.aspx) événement).

Appuyez sur F5 pour exécuter l’application. La fenêtre Sortie affiche les informations suivantes :

[!code-console[Main](owin-middleware-in-the-iis-integrated-pipeline/samples/sample7.cmd)]

## <a name="stage-marker-rules"></a>Règles de marqueur d’étape

Composants d’intergiciel (middleware) Owin (OMC) peuvent être configurés pour exécuter les événements d’étape de pipeline OWIN suivant :

[!code-csharp[Main](owin-middleware-in-the-iis-integrated-pipeline/samples/sample8.cs)]

1. Par défaut, OMCs s’exécutent sur le dernier événement (`PreHandlerExecute`). C’est pourquoi notre premier exemple de code affiche « PreExecuteRequestHandler ».
2. Vous pouvez utiliser l’un `pp.UseStageMarker` méthode pour inscrire un OMC pour s’exécuter plus tôt, à n’importe quelle étape du pipeline OWIN répertoriées dans le `PipelineStage` enum.
3. Le pipeline OWIN et le pipeline d’IIS est triée, par conséquent, les appels à `app.UseStageMarker` doit être dans l’ordre. Impossible de définir le Gestionnaire d’événements à un événement qui précède le dernier événement inscrit auprès de `app.UseStageMarker`. Par exemple, *après* appel :

    [!code-console[Main](owin-middleware-in-the-iis-integrated-pipeline/samples/sample9.cmd)]

 les appels à `app.UseStageMarker` passage `Authenticate` ou `PostAuthenticate` ne sera pas pris en compte et aucune exception n’est levée. OMCs s’exécutent à la dernière phase, ce qui par défaut est `PreHandlerExecute`. Les marqueurs d’étape sont utilisés pour les rendre à exécuter plus tôt. Si vous spécifiez les marqueurs d’étape en désordre, nous arrondir au marqueur antérieur. En d’autres termes, l’ajout d’un marqueur d’étape indique « Exécuter après le stade X ». Exécution de OMC sur le marqueur d’étape plus tôt ajouté après ces derniers dans le pipeline OWIN.
4. La première étape d’appels à `app.UseStageMarker` wins. Par exemple, si vous changez l’ordre des `app.UseStageMarker` appels à partir de l’exemple précédent :

    [!code-csharp[Main](owin-middleware-in-the-iis-integrated-pipeline/samples/sample10.cs?highlight=13,19)]

 Affiche la fenêtre Sortie : 

    [!code-console[Main](owin-middleware-in-the-iis-integrated-pipeline/samples/sample11.cmd)]

 Les OMCs tous s’exécuter dans le `AuthenticateRequest` à l’étape, car la dernière OMC enregistré avec le `Authenticate` événement et le `Authenticate` événement précède tous les autres événements.
