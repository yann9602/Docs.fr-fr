---
uid: web-api/overview/testing-and-debugging/tracing-in-aspnet-web-api
title: "Le traçage dans ASP.NET Web API 2 | Documents Microsoft"
author: MikeWasson
description: "Montre comment activer le suivi dans l’API Web ASP.NET."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/25/2014
ms.topic: article
ms.assetid: 66a837e9-600b-4b72-97a9-19804231c64a
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/testing-and-debugging/tracing-in-aspnet-web-api
msc.type: authoredcontent
ms.openlocfilehash: 7392ae5d9bc4c3aab45a9373099a0ee18e873a4f
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="tracing-in-aspnet-web-api-2"></a>Le traçage dans ASP.NET Web API 2
====================
par [Mike Wasson](https://github.com/MikeWasson)

> Lorsque vous essayez de déboguer une application basée sur le web, il n’existe aucun remplacement pour un ensemble approprié de journaux de suivi. Ce didacticiel montre comment activer le suivi dans l’API Web ASP.NET. Vous pouvez utiliser cette fonctionnalité à ce que l’infrastructure API Web fait avant et après que qu’il appelle votre contrôleur de trace. Vous pouvez également l’utiliser pour votre propre code de trace.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - [Visual Studio 2017](https://www.visualstudio.com/downloads/) (fonctionne également avec Visual Studio 2015)
> - API Web 2
> - [Microsoft.AspNet.WebApi.Tracing](http://www.nuget.org/packages/Microsoft.AspNet.WebApi.Tracing)


## <a name="enable-systemdiagnostics-tracing-in-web-api"></a>Activer System.Diagnostics Tracing in Web API

Tout d’abord, nous allons créer un nouveau projet d’Application Web ASP.NET. Dans Visual Studio, à partir de la **fichier** menu, sélectionnez **nouveau**, puis **projet**. Sous **modèles**, **Web**, sélectionnez **Application Web ASP.NET**.

[![](tracing-in-aspnet-web-api/_static/image2.png)](tracing-in-aspnet-web-api/_static/image1.png)

Choisissez le modèle de projet d’API Web.

[![](tracing-in-aspnet-web-api/_static/image4.png)](tracing-in-aspnet-web-api/_static/image3.png)

À partir de la **outils** menu, sélectionnez **Gestionnaire de Package de bibliothèque**, puis **Console de gestion des packages**.

Dans la fenêtre de Console du Gestionnaire de Package, tapez les commandes suivantes.

[!code-console[Main](tracing-in-aspnet-web-api/samples/sample1.cmd)]

La première commande installe le dernier package de suivi d’API Web. Il met également à jour les principaux packages d’API Web. La deuxième commande met à jour le package WebApi.WebHost vers la dernière version.

> [!NOTE]
> Si vous souhaitez cibler une version spécifique de l’API Web, utilisez-indicateur de Version lorsque vous installez le package de traçage.


Ouvrez le fichier WebApiConfig.cs dans l’application\_dossier de démarrage. Ajoutez le code suivant à la **inscrire** (méthode).

[!code-csharp[Main](tracing-in-aspnet-web-api/samples/sample2.cs?highlight=6)]

Ce code ajoute la [SystemDiagnosticsTraceWriter](https://msdn.microsoft.com/library/system.web.http.tracing.systemdiagnosticstracewriter.aspx) classe pour le pipeline de l’API Web. Le **SystemDiagnosticsTraceWriter** classe écrit des traces à [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace).

Pour afficher les traces, exécutez l’application dans le débogueur. Dans le navigateur, accédez à `/api/values`.

![](tracing-in-aspnet-web-api/_static/image5.png)

Les instructions de trace sont écrites dans la fenêtre Sortie dans Visual Studio. (À partir de la **vue** menu, sélectionnez **sortie**).

[![](tracing-in-aspnet-web-api/_static/image7.png)](tracing-in-aspnet-web-api/_static/image6.png)

Étant donné que **SystemDiagnosticsTraceWriter** écrit des traces à **System.Diagnostics.Trace**, vous pouvez inscrire d’autres écouteurs de traçage ; par exemple, pour écrire des traces dans un fichier journal. Pour plus d’informations sur les enregistreurs de trace, consultez le [écouteurs de traçage](https://msdn.microsoft.com/library/4y5y10s7.aspx) rubrique sur MSDN.

### <a name="configuring-systemdiagnosticstracewriter"></a>Configuration SystemDiagnosticsTraceWriter

Le code suivant montre comment configurer le writer de suivi.

[!code-csharp[Main](tracing-in-aspnet-web-api/samples/sample3.cs)]

Il existe deux paramètres que vous pouvez contrôler :

- IsVerbose : Si la valeur est false, chaque trace contient un minimum d’informations. Si la valeur est true, les suivis inclure plus d’informations.
- MinimumLevel : Définit le niveau de suivi minimale. Niveaux de trace, dans l’ordre, sont Debug, Info, avertissement, erreur et irrécupérable.

## <a name="adding-traces-to-your-web-api-application"></a>Ajout de Traces à votre Application Web API

Ajout d’un writer de suivi vous donne un accès immédiat aux traces créées par le pipeline d’API Web. Vous pouvez également utiliser l’enregistreur de trace à votre propre code de trace :

[!code-csharp[Main](tracing-in-aspnet-web-api/samples/sample4.cs)]

Pour obtenir le writer de suivi, appelez **HttpConfiguration.Services.GetTraceWriter**. À partir d’un contrôleur, cette méthode est accessible via la **ApiController.Configuration** propriété.

Pour écrire une trace, vous pouvez appeler la **ITraceWriter.Trace** (méthode) directement, mais la [ITraceWriterExtensions](https://msdn.microsoft.com/library/system.web.http.tracing.itracewriterextensions.aspx) classe définit des méthodes d’extension qui sont plus convivial. Par exemple, le **Info** méthode illustrée ci-dessus crée une trace avec niveau de trace **informations**.

## <a name="web-api-tracing-infrastructure"></a>Infrastructure de traçage d’API Web

Cette section décrit comment écrire un writer de suivi personnalisé pour API Web.

Le package Microsoft.AspNet.WebApi.Tracing repose sur une infrastructure de suivi plus générale dans l’API Web. Au lieu d’utiliser Microsoft.AspNet.WebApi.Tracing, vous pouvez également incorporer dans une autre bibliothèque de traçage/invisible, tel que [NLog](http://nlog-project.org/) ou [log4net](http://logging.apache.org/log4net/).

Pour collecter les traces, vous devez implémenter la **ITraceWriter** interface. Voici un exemple simple :

[!code-csharp[Main](tracing-in-aspnet-web-api/samples/sample5.cs)]

Le **ITraceWriter.Trace** méthode crée une trace. L’appelant spécifie un niveau de la catégorie et la trace. La catégorie peut être n’importe quelle chaîne définie par l’utilisateur. Votre implémentation de **Trace** doit effectuer les opérations suivantes :

1. Créer un nouveau **TraceRecord**. Initialiser avec la demande, la catégorie et le niveau de suivi, comme indiqué. Ces valeurs sont fournies par l’appelant.
2. Appeler le *traceAction* déléguer. À l’intérieur de ce délégué, l’appelant est censé remplir le reste de la **TraceRecord**.
3. Écrire la **TraceRecord**, à l’aide de n’importe quelle technique d’enregistrement voulue. L’exemple suivant appelle simplement **System.Diagnostics.Trace**.

## <a name="setting-the-trace-writer"></a>Définition de l’enregistreur de Trace

Pour activer le suivi, vous devez configurer des API Web à utiliser votre **ITraceWriter** mise en œuvre. Cela via le **HttpConfiguration** de l’objet, comme indiqué dans le code suivant :

[!code-csharp[Main](tracing-in-aspnet-web-api/samples/sample6.cs)]

Writer de suivi qu’une seule peut être active. Par défaut, les API Web définit un &quot;nulle&quot; suivi qui ne fait rien. (Le &quot;nulle&quot; suivi existe afin que le code de traçage n’ait pas vérifier si le writer de suivi est **null** avant d’écrire une trace.)

## <a name="how-web-api-tracing-works"></a>Comment Web API Tracing Works

Le suivi des utilisations d’API Web une API Web en utilise un *façade* modèle : lorsque le traçage est activé, les API Web encapsule les différentes parties du pipeline de demande avec les classes qui effectuent le suivi des appels.

Par exemple, lorsque vous sélectionnez un contrôleur, le pipeline utilise la **IHttpControllerSelector** interface. Le traçage est activé, le pipleline insère une classe qui implémente **IHttpControllerSelector** mais les appels de l’implémentation réelle :

![Suivi de l’API Web utilise le modèle de façade.](tracing-in-aspnet-web-api/_static/image8.png)

Avantages de cette conception :

- Si vous n’ajoutez pas d’un writer de suivi, les composants de suivi ne sont pas instanciés et n’ont aucun impact sur les performances.
- Si vous remplacez tels que les services par défaut **IHttpControllerSelector** avec votre propre implémentation personnalisée, le suivi n’est pas affecté, car le suivi est effectué par l’objet de wrapper.

Vous pouvez également remplacer l’infrastructure de suivi API Web entière avec votre propre infrastructure personnalisé, en remplaçant la valeur par défaut **ITraceManager** service :

[!code-csharp[Main](tracing-in-aspnet-web-api/samples/sample7.cs)]

Implémentez **ITraceManager.Initialize** pour initialiser votre système de suivi. Sachez que cette opération remplace le *entière* framework de trace, y compris tout le code de traçage qui est intégré à l’API Web.
