---
uid: aspnet/overview/web-development-best-practices/what-not-to-do-in-aspnet-and-what-to-do-instead
title: "Procédure ne pas à suivre dans ASP.NET et comment procéder à la place | Documents Microsoft"
author: tfitzmac
description: "Cette rubrique décrit plusieurs erreurs courantes que les utilisateurs font dans les projets web ASP.NET. Il fournit des recommandations pour les mesures à prendre pour éviter ces norme..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/08/2014
ms.topic: article
ms.assetid: c39b9965-545c-4b04-8f55-21be7f28a9e5
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/web-development-best-practices/what-not-to-do-in-aspnet-and-what-to-do-instead
msc.type: authoredcontent
ms.openlocfilehash: 24c6a35a6b663ebb0f8d0e3e7988322fa5d9018c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="what-not-to-do-in-aspnet-and-what-to-do-instead"></a>Procédure ne pas à suivre dans ASP.NET et comment procéder à la place
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cette rubrique décrit plusieurs erreurs courantes que les utilisateurs font dans les projets web ASP.NET. Il fournit des recommandations pour les mesures à prendre pour éviter ces erreurs courantes. Il est basé sur un [présentation](http://vimeo.com/68390507) par **Damian Edwards** à norvégien conférence de développeurs.


## <a name="disclaimer"></a>Exclusion de responsabilité

Cette rubrique n’est pas destinée à vérifier si que votre application est sécurisé et efficace comme un guide complet. Vous devez toujours suivre les meilleures pratiques de sécurité et de performances qui ne sont pas décrites dans cette rubrique. Elle suggère uniquement comment éviter les erreurs courantes liées aux processus et des classes .NET.

## <a name="overview"></a>Vue d'ensemble

Cette rubrique contient les sections suivantes :

- [Conformité aux normes](#standards)

    - [Adaptateurs de contrôle](#adapters)
    - [Propriétés de style sur les contrôles](#styleprop)
    - [Page et les rappels de contrôle](#callback)
    - [Détection de la fonctionnalité du navigateur](#browsercap)
- [Sécurité](#security)

    - [Validation de la demande](#validation)
    - [Session et l’authentification par formulaire sans cookie](#cookieless)
    - [EnableViewStateMac](#viewstatemac)
    - [Confiance moyenne](#medium)
    - [&lt;appSettings&gt;](#appsettings)
    - [UrlPathEncode](#urlpathencode)
- [Fiabilité et performances](#performance)

    - [PreSendRequestHeaders et PreSendRequestContext](#presend)
    - [Événements de Page asynchrone avec Web Forms](#asyncevents)
    - [Incendie et oublient de travail](#fire)
    - [Corps d’entité](#requestentity)
    - [Response.Redirect et Response.End](#redirect)
    - [EnableViewState et ViewStateMode](#viewstatemode)
    - [SqlMembershipProvider](#sqlprovider)
    - [Les demandes en cours d’exécution longue (> 110 secondes)](#long)

<a id="standards"></a>

## <a name="standards-compliance"></a>Conformité aux normes

<a id="adapters"></a>

### <a name="control-adapters"></a>Adaptateurs de contrôle

Recommandation : Arrêter à l’aide des adaptateurs de contrôle pour un rendu adaptable et utiliser à la place des requêtes de média CSS et HTML conforme aux normes.

Les adaptateurs de contrôles ont été introduites dans .NET 2.0 pour rendre le code de présentation qui a été personnalisé pour les environnements et les différents appareils. Désormais, ce rendu adaptable peut être accompli avec CSS et HTML. Vous devez arrêter d’utiliser les adaptateurs de contrôle et convertir tous les adaptateurs existants pour le code CSS et HTML.

Pour plus d’informations, consultez [les requêtes de média](http://www.w3.org/TR/css3-mediaqueries/) et [Comment : ajouter des Pages à votre ASP.NET Web Forms Mobile / Application MVC](../../../whitepapers/add-mobile-pages-to-your-aspnet-web-forms-mvc-application.md).

<a id="styleprop"></a>

### <a name="style-properties-on-controls"></a>Propriétés de style sur les contrôles

Recommandation : Arrêter la définition des valeurs de style de la balise de contrôle et le définir à la place des valeurs de mise en forme dans les feuilles de style CSS.

Contrôles serveur Web contiennent des dizaines de propriétés qui peuvent être utilisées pour définir les propriétés de style de ligne. Par exemple, la propriété ForeColor définit la couleur du texte pour un contrôle. Vous pouvez effectuer cette même effet plus efficacement par le biais de feuilles de style CSS. Feuilles de style permettent de centraliser les valeurs de style et évitez d’utiliser ces valeurs dans votre application.

L’exemple suivant montre une classe CSS le texte de jeux de couleur rouge.

[!code-css[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample1.css)]

L’exemple suivant montre comment appliquer de manière dynamique la classe CSS.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample2.cs)]

<a id="callback"></a>

### <a name="page-and-control-callbacks"></a>Page et les rappels de contrôle

Recommandation : Arrêter à l’aide de la page et contrôle les rappels et à la place utiliser les caractères suivants : AJAX, UpdatePanel, méthodes d’action MVC, API Web ou SignalR.

Dans les versions antérieures d’ASP.NET, les méthodes de rappel de Page et de contrôle activé vous permet de mettre à jour de la partie de la page web sans actualiser une page entière. Vous pouvez maintenant effectuer des mises à jour de page partielle [AJAX](../../../ajax/index.md), [UpdatePanel](https://msdn.microsoft.com/en-US/library/bb386454.aspx), [MVC](../../../mvc/index.md), [API Web](../../../web-api/index.md) ou [SignalR](../../../signalr/index.md). Vous devez arrêter à l’aide des méthodes de rappel, car ils peuvent provoquer des problèmes avec des URL conviviales et le routage. Par défaut, les contrôles ne permettent pas de méthodes de rappel, mais si vous avez activé cette fonctionnalité dans un contrôle, vous devez les désactiver.

<a id="browsercap"></a>

### <a name="browser-capability-detection"></a>Détection de la fonctionnalité du navigateur

Recommandation : Arrêter à l’aide de la détection de la fonctionnalité du navigateur statique et utiliser à la place de la détection de fonction dynamique.

Dans les versions antérieures d’ASP.NET, les fonctionnalités prises en charge pour chaque navigateur étaient stockées dans un fichier XML. Prise en charge des fonctionnalités Détection d’une recherche statique n’est pas la meilleure approche. Maintenant, vous pouvez détecter dynamiquement un navigateur de fonctionnalités prises en charge à l’aide d’une infrastructure de détection de fonctionnalité, tel que [Modernizr](http://modernizr.com/). Détection de la fonction détermine la prise en charge en tente d’utiliser une méthode ou propriété, puis vérifie si le navigateur a produit le résultat souhaité. Par défaut, Modernizr est inclus dans les modèles d’application Web.

<a id="security"></a>

## <a name="security"></a>Sécurité

<a id="validation"></a>

### <a name="request-validation"></a>Validation de la demande

Recommandation : Valider l’entrée d’utilisateur et coder la sortie à partir des utilisateurs.

Validation de la demande est une fonctionnalité d’ASP.NET qui inspecte chaque demande et s’arrête à la demande si une menace perçue est trouvée. Ne dépendent pas de validation de la demande pour la sécurisation de votre application contre les attaques de scripts entre sites. Au lieu de cela, validez toutes les entrées d’utilisateurs et coder la sortie. Dans certains cas, vous pouvez utiliser des expressions régulières pour valider l’entrée, mais dans les cas plus complexes, que vous devez valider l’entrée d’utilisateur à l’aide de classes .NET qui déterminent si la valeur correspond à des valeurs autorisées.

L’exemple suivant montre comment utiliser une méthode statique dans la classe Uri pour déterminer si l’Uri fourni par un utilisateur est valide.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample3.cs)]

Toutefois, pour suffisamment vérifier l’Uri, vous devez également vérifier pour vous assurer qu’il spécifie `http` ou `https`. L’exemple suivant utilise les méthodes d’instance pour vérifier que l’Uri est valide.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample4.cs)]

Avant le rendu de l’entrée d’utilisateur au format HTML ou d’inclure l’entrée d’utilisateur dans une requête SQL, encoder les valeurs pour garantir un code malveillant n’est pas inclus.

Vous pouvez HTML encoder la valeur dans le balisage avec la &lt;% : %&gt; syntaxe, comme indiqué ci-dessous.

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample5.aspx?highlight=1)]

Dans la syntaxe Razor, vous pouvez HTML Encoder avec @, comme illustré ci-dessous.

[!code-cshtml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample6.cshtml?highlight=1)]

L’exemple suivant montre comment au format HTML encode une valeur dans le code-behind.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample7.cs)]

Pour encoder l’en toute sécurité une valeur pour les commandes SQL, utilisez les paramètres de commande tels que le [SqlParameter](https://msdn.microsoft.com/en-us/library/system.data.sqlclient.sqlparameter.aspx). <a id="cookieless"></a>

### <a name="cookieless-forms-authentication-and-session"></a>Session et l’authentification par formulaire sans cookie

Recommandation : Requièrent les cookies.

Passer des informations d’authentification dans la chaîne de requête n’est pas sécurisée. Par conséquent, requièrent des cookies lorsque votre application inclut l’authentification. Si votre cookie stocke des informations sensibles, envisagez d’exiger SSL pour le cookie.

L’exemple suivant montre comment spécifier dans le fichier Web.config que l’authentification par formulaire nécessite un cookie est transmis via le protocole SSL.

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample8.xml)]

<a id="viewstatemac"></a>

### <a name="enableviewstatemac"></a>EnableViewStateMac

Recommandation : Jamais a la valeur false.

Par défaut, EnbableViewStateMac est définie sur true. Même si votre application n’utilise pas l’état d’affichage, ne définissez pas EnableViewStateMac sur false. La valeur false rend votre application vulnérable aux scripts entre sites.

À partir de ASP.NET 4.5.2, le runtime applique **EnableViewStateMac = true**. Même si vous affectez la valeur false, le runtime ignore cette valeur et se poursuit avec la valeur définie sur true. Pour plus d’informations, consultez [ASP.NET 4.5.2 et EnableViewStateMac](https://blogs.msdn.com/b/webdev/archive/2014/05/07/asp-net-4-5-2-and-enableviewstatemac.aspx).

L’exemple suivant montre comment définir EnableViewStateMac sur true. Vous n’avez pas besoin réellement définir cette valeur sur « true », car il a la valeur true par défaut. Toutefois, si vous avez affectez-lui la valeur false sur n’importe quelle page dans votre application, vous devez corriger immédiatement cette valeur.

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample9.aspx)]

<a id="medium"></a>

### <a name="medium-trust"></a>Confiance moyenne

Recommandation : Ne dépendent pas de confiance moyenne (ou tout autre niveau de confiance) en tant que limite de sécurité.

Confiance partielle ne protège pas correctement votre application et ne doit pas être utilisée. Au lieu de cela, utilisez la confiance totale et isoler les applications non approuvées dans des pools d’applications distincts. En outre, exécutez chaque pool d’applications sous une identité unique. Pour plus d’informations, consultez [ASP.NET de confiance partielle ne garantit pas l’isolation des applications](https://support.microsoft.com/kb/2698981).

<a id="appsettings"></a>

### <a name="ltappsettingsgt"></a>&lt;appSettings&gt;

Recommandation : Ne désactivez pas les paramètres de sécurité dans &lt;appSettings&gt; élément.

L’élément appSettings contient de nombreuses valeurs qui sont obligatoires pour les mises à jour de sécurité. Vous ne devez pas modifier ou désactiver ces valeurs. Si vous devez désactiver ces valeurs lors du déploiement d’une mise à jour, réactivez immédiatement après avoir effectué le déploiement.

Pour plus d’informations, consultez [ASP.NET appSettings, élément](https://msdn.microsoft.com/en-us/library/hh975440.aspx).

<a id="urlpathencode"></a>

### <a name="urlpathencode"></a>UrlPathEncode

Recommandation : Utiliser [codent en URL](https://msdn.microsoft.com/en-us/library/zttxte6w.aspx) à la place.

La méthode UrlPathEncode a été ajoutée au .NET Framework pour résoudre un problème de compatibilité de navigateur très spécifique. Il n’encode pas correctement une URL et ne protège pas votre application à partir de scripts entre sites. Vous devez jamais l’utiliser dans votre application. Au lieu de cela, utilisez [codent en URL](https://msdn.microsoft.com/en-us/library/zttxte6w.aspx).

L’exemple suivant montre comment passer une URL encodée comme paramètre de chaîne de requête pour un contrôle de lien hypertexte.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample10.cs)]

<a id="performance"></a>

## <a name="reliability-and-performance"></a>Fiabilité et performances

<a id="presend"></a>

### <a name="presendrequestheaders-and-presendrequestcontext"></a>PreSendRequestHeaders et PreSendRequestContext

Recommandation : N’utilisez pas ces événements avec les modules managés. À la place, écrire un module IIS natif pour effectuer la tâche requise. Consultez [création de Modules de Code natif HTTP](https://msdn.microsoft.com/en-us/library/ms693629.aspx).

Vous pouvez utiliser la [PreSendRequestHeaders](https://msdn.microsoft.com/en-us/library/system.web.httpapplication.presendrequestheaders.aspx) et [PreSendRequestContext](https://msdn.microsoft.com/en-us/library/system.web.httpapplication.presendrequestcontent.aspx) événements avec les modules IIS natifs, mais ne les utilisez pas avec les modules managés qui implémentent IHttpModule. Définition de ces propriétés peut entraîner des problèmes avec des requêtes asynchrones.

<a id="asyncevents"></a>

### <a name="asynchronous-page-events-with-web-forms"></a>Événements de Page asynchrone avec Web Forms

Recommandation : Dans les formulaires Web, évitez d’écriture asynchrone des méthodes void pour les événements de cycle de vie de Page et à la place utiliser [Page.RegisterAsyncTask](https://msdn.microsoft.com/en-us/library/system.web.ui.page.registerasynctask.aspx) pour le code asynchrone.

Lorsque vous marquez un événement de page avec **async** et **void**, vous ne peut pas déterminer quand le code asynchrone a terminé. Au lieu de cela, utilisez Page.RegisterAsyncTask pour exécuter le code asynchrone d’une manière qui vous permet d’effectuer le suivi de son achèvement.

L’exemple suivant montre un bouton Cliquez sur Gestionnaire qui contient du code asynchrone. Cet exemple inclut la lecture d’une valeur de chaîne de façon asynchrone, qui est fourni uniquement comme un exemple simplifié d’une tâche asynchrone et non comme une pratique recommandée.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample11.cs)]

Si vous utilisez des tâches asynchrones, vous pouvez définir le framework cible du runtime Http 4.5 dans le fichier Web.config. Définition de l’infrastructure cible sur 4.5 active sur le nouveau contexte de synchronisation qui a été ajoutée dans .NET 4.5. Cette valeur est définie par défaut dans les nouveaux projets dans Visual Studio 2012, mais n’est ne pas être définie si vous travaillez avec un projet existant.

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample12.xml)]

<a id="fire"></a>

### <a name="fire-and-forget-work"></a>Incendie et oublient de travail

Recommandation : Lors du traitement d’une demande au sein d’ASP.NET, évitez de lancement du travail incendie initiale (ces appelant la méthode ThreadPool.QueueUserWorkItem ou en créant un minuteur qui appelle à plusieurs reprises un délégué).

Si votre application a un travail incendie initiale qui s’exécute au sein d’ASP.NET, votre application peut être désynchronisée. À tout moment, le domaine d’application peut être détruit ce qui signifie que votre processus en cours ne corresponde plus à l’état actuel de l’application.

Vous devez passer ce type de travail en dehors d’ASP.NET. Vous pouvez utiliser une exécution de traitements Web, Service Windows ou un rôle de travail dans Azure pour effectuer un travail en cours et exécuter ce code à partir d’un autre processus.

Si vous devez effectuer ce travail au sein d’ASP.NET, vous pouvez ajouter le package Nuget appelé [WebBackgrounder](http://www.nuget.org/packages/webbackgrounder) pour exécuter le code.

<a id="requestentity"></a>

### <a name="request-entity-body"></a>Corps d’entité

Recommandation : Évitez la lecture Request.Form ou Request.InputStream avant d’exécuter l’événement du gestionnaire.

Le plus ancien que vous devez lire à partir de Request.Form ou Request.InputStream est au cours du gestionnaire exécuter événement. Dans MVC, le contrôleur est le gestionnaire et est de l’événement d’exécution lors de l’exécution de la méthode d’action. Dans les formulaires Web, la Page est le gestionnaire et est de l’événement d’exécution lorsque l’événement Page.Init se déclenche. Si vous lisez le corps d’entité de demande antérieure à l’événement d’exécution, vous interférer avec le traitement de la demande.

Si vous avez besoin lire le corps d’entité de demande avant l’événement d’exécution, utilisez [Request.GetBufferlessInputStream](https://msdn.microsoft.com/en-us/library/ff406798.aspx) ou [Request.GetBufferedInputStream](https://msdn.microsoft.com/en-us/library/system.web.httprequest.getbufferedinputstream.aspx). Lorsque vous utilisez GetBufferlessInputStream, vous obtenez le flux brut à partir de la demande et la responsabilité de traiter la requête entière. Après avoir appelé GetBufferlessInputStream, Request.Form et Request.InputStream ne sont pas disponibles car ils n’ont pas été remplies par ASP.NET. Lorsque vous utilisez GetBufferedInputStream, vous obtenez une copie du flux de données à partir de la demande. Request.Form et Request.InputStream sont toujours disponibles plus loin dans la demande, car ASP.NET remplit l’autre exemplaire.

<a id="redirect"></a>

### <a name="responseredirect-and-responseend"></a>Response.Redirect et Response.End

Recommandation : Tenez compte des différences dans les modalités de thread après avoir appelé [Response.Redirect(String)](https://msdn.microsoft.com/en-us/library/t9dwyts4.aspx).

Le [Response.Redirect(String)](https://msdn.microsoft.com/en-us/library/t9dwyts4.aspx) méthode appelle la méthode Response.End. Dans un processus synchrone, l’appel Request.Redirect provoque abandonne immédiatement le thread en cours. Toutefois, dans un processus asynchrone, l’appel de Response.Redirect n’abandonne pas le thread actuel, afin de l’exécution de code se poursuit pour la demande. Dans un processus asynchrone, vous devez retourner la tâche à partir de la méthode pour arrêter l’exécution de code.

Dans un projet MVC, vous ne devez pas appeler Response.Redirect (). Au lieu de cela, retournent un RedirectResult.

<a id="viewstatemode"></a>

### <a name="enableviewstate-and-viewstatemode"></a>EnableViewState et ViewStateMode

Recommandation : Utilisez ViewStateMode, au lieu de EnableViewState, pour fournir un contrôle granulaire sur lequel les contrôles utilisent l’état d’affichage.

Lorsque vous définissez EnableViewState sur false dans la directive de Page, l’état d’affichage est désactivé pour tous les contrôles dans la page et ne peut pas être activée. Si vous souhaitez activer l’état d’affichage que pour certains contrôles dans votre page, la valeur ViewStateMode désactivé pour la Page.

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample13.aspx)]

Ensuite, définissez ViewStateMode sur activé sur les seuls les contrôles qui ont réellement besoin l’état d’affichage.

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample14.aspx)]

En activant l’état d’affichage pour seulement les contrôles qui en ont besoin, vous pouvez réduire la taille de l’état d’affichage de vos pages web.

<a id="sqlprovider"></a>

### <a name="sqlmembershipprovider"></a>SqlMembershipProvider

Recommandation : Utiliser les fournisseurs universels.

Dans les modèles de projet actuel, SqlMembershipProvider a été remplacé par [ASP.NET Universal Providers](http://www.nuget.org/packages/Microsoft.AspNet.Providers), qui est disponible comme package NuGet. Si vous utilisez SqlMembershipProvider dans un projet qui a été généré avec une version antérieure des modèles, vous devez basculer vers des fournisseurs universels. Les fournisseurs universels fonctionnent avec toutes les bases de données qui sont prises en charge par Entity Framework.

Pour plus d’informations, consultez [Introducing ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx).

<a id="long"></a>

### <a name="long-running-requests-110-seconds"></a>Requêtes longues (> 110 secondes)

Recommandation : Utiliser [WebSockets](https://msdn.microsoft.com/en-us/library/system.net.websockets.websocket.aspx) ou [SignalR](../../../signalr/index.md) pour les clients connectés et utilisez les opérations d’e/s asynchrones.

Demandes d’exécution longue peuvent entraîner des résultats imprévisibles et des performances médiocres dans votre application web. Le paramètre de délai d’attente par défaut pour une demande est de 110 secondes. Si vous utilisez l’état de session avec une demande d’exécution longue, ASP.NET sera libérer le verrou sur l’objet Session après 110 secondes. Toutefois, votre application peut être occupé à une opération sur l’objet de Session lors de la libération du verrou, et l’opération ne peut pas effectuer correctement. Si une deuxième demande à partir de l’utilisateur est bloquée pendant l’exécution de la première demande, la deuxième demande peut accéder à l’objet de Session dans un état incohérent.

Si votre application inclut des opérations d’e/s bloquantes (ou synchrones), l’application sera ne répond pas.

Pour améliorer les performances, utilisez les opérations d’e/s asynchrones dans le .NET Framework. En outre, utilisez WebSockets ou SignalR pour les clients qui se connectent au serveur. Ces fonctionnalités sont conçues pour gérer efficacement les requêtes longues.
