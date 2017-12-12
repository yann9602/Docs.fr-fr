---
uid: web-pages/overview/ui-layouts-and-themes/twitter-helper
title: Application auxiliaire avec ASP.NET Web Pages Twitter | Documents Microsoft
author: tfitzmac
description: "Cette rubrique, l’application montrent comment ajouter une application auxiliaire Twitter à votre projet WebMatrix 3. Il contient le code de l’application auxiliaire Twitter et montre comment appeler l’application d’assistance..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/07/2014
ms.topic: article
ms.assetid: c1a1244e-b9c8-42e6-a00b-8456a4ec027c
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/twitter-helper
msc.type: authoredcontent
ms.openlocfilehash: 07d9c4d485c42b78a42c54c9740af5f67cb44763
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="twitter-helper-with-aspnet-web-pages"></a>Programme d’assistance de Twitter avec les Pages Web ASP.NET
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cette rubrique, l’application montrent comment ajouter une application auxiliaire Twitter à votre projet WebMatrix 3. Il contient le code de l’application auxiliaire Twitter et montre comment appeler les méthodes d’assistance.
> 
> Ce code pour le fichier Twitter.cshtml a été développé par **Tian panoramique** de Microsoft.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 3
>   
> 
> Ce didacticiel fonctionne également avec ASP.NET Web Pages 2.


## <a name="introduction"></a>Introduction

Cette rubrique montre comment ajouter une application auxiliaire Twitter à votre application et utiliser la syntaxe Razor pour appeler les méthodes d’assistance. L’application auxiliaire Twitter facilite l’incorporer des widgets dans votre application et les boutons de Twitter. Pour utiliser un widget Twitter, tels que la chronologie de l’utilisateur ou les résultats de recherche pour un #sqlhelp, vous devez d’abord créer le [widget sur Twitter](https://twitter.com/settings/widgets). Après avoir créé votre widget, vous recevez un id de widget. Vous passez cet id de widget en tant que paramètre lorsque vous appelez les méthodes d’assistance qui montrent le widget.

Cette rubrique a été écrit pour la version 1.1 de l’API Twitter. En ajoutant directement le code de l’application auxiliaire Twitter à votre projet, vous pouvez mettre à jour le code d’assistance si l’API Twitter change.

Pour plus d’informations sur l’installation de WebMatrix, consultez [Introducing ASP.NET Web Pages 2 - mise en route](../getting-started/introducing-aspnet-web-pages-2/getting-started.md).

## <a name="add-twitter-helper-to-your-project"></a>Ajouter l’application auxiliaire Twitter à votre projet

Pour ajouter l’application auxiliaire Twitter, tout d’abord, ajoutez un dossier nommé **application\_Code** à votre projet. Ensuite, créez un fichier nommé **Twitter.cshtml**.

![Dossier App_Code](twitter-helper/_static/image1.png)

Remplacez le code par défaut dans Twitter.cshtml par le code suivant.

[!code-cshtml[Main](twitter-helper/samples/sample1.cshtml)]

## <a name="call-twitter-methods-from-your-web-pages"></a>Appeler des méthodes Twitter à partir de vos pages web

L’exemple suivant montre comment utiliser les méthodes de l’application auxiliaire Twitter à partir d’une page dans votre projet. Dans votre projet, vous devez remplacer les valeurs de paramètre avec des valeurs qui correspondent à vos besoins. Vous pouvez utiliser les ID de widget fourni pour Explorer le fonctionnement des méthodes, mais que vous souhaitez générer vos propres widgets pour votre projet.

Tous les paramètres indiqués ci-dessous sont requises. Les paramètres facultatifs sont utilisés pour personnaliser la façon dont le bouton ou le widget s’affiche. Par exemple, le bouton suivre requiert uniquement le nom d’utilisateur à suivre, mais l’exemple montre comment inclure le nombre de suivis et comment spécifier la taille du bouton et la langue.

[!code-html[Main](twitter-helper/samples/sample2.html)]

## <a name="see-the-results"></a>Afficher les résultats

Le code ci-dessus génère les boutons et les widgets suivants. Ces boutons et les widgets sont entièrement fonctionnelles, pas les captures d’écran. Le bouton suivre est affiché en espagnol, car le paramètre de langue a été défini sur **es**.

### <a name="follow-button"></a>Suivez le bouton

[Suivez @aspnet)](https://twitter.com/aspnet)<script>! (fonction) (d, s, id) {var js, fjs = d.getElementsByTagName(s) [0], p = /^http:/.test(d.location) ? 'http' : 'https'. Si ( ! d.getElementById(id)) {js = d.createElement(s) ; js.id = id ; js.src = p + ' : / / platform.twitter.com/widgets.js' ; fjs.parentNode.insertBefore (js, fjs) ;}} (document, 'script', 'twitter-wjs') ;</script>

### <a name="tweet-button"></a>Bouton de tweet

[Tweet](https://twitter.com/share)<script>! (fonction) (d, s, id) {var js, fjs = d.getElementsByTagName(s) [0], p = /^http:/.test(d.location) ? 'http' : 'https'. Si ( ! d.getElementById(id)) {js = d.createElement(s) ; js.id = id ; js.src = p + ' : / / platform.twitter.com/widgets.js' ; fjs.parentNode.insertBefore (js, fjs) ;}} (document, 'script', 'twitter-wjs') ;</script>

### <a name="user-timeline-profile"></a>Chronologie de l’utilisateur (profil)

[Tweets par @aspnet ](https://twitter.com/aspnet) <script>! (fonction) (d, s, id) {var js, fjs = d.getElementsByTagName(s) [0], p = /^http:/.test(d.location) ? 'http' : 'https'. Si ( ! d.getElementById(id)) {js = d.createElement(s) ; js.id = id ; js.src = p + » : / / platform.twitter.com/widgets.js » ; fjs.parentNode.insertBefore (js, fjs) ;}} (document, « script », « wjs-twitter ») ;</script>

### <a name="favorites"></a>Favoris

[Tweet favori par @Microsoft ](https://twitter.com/Microsoft/favorites) <script>! (fonction) (d, s, id) {var js, fjs = d.getElementsByTagName(s) [0], p = /^http:/.test(d.location) ? 'http' : 'https'. Si ( ! d.getElementById(id)) {js = d.createElement(s) ; js.id = id ; js.src = p + » : / / platform.twitter.com/widgets.js » ; fjs.parentNode.insertBefore (js, fjs) ;}} (document, « script », « wjs-twitter ») ;</script>

### <a name="list"></a>Liste

[Tweets de @Microsoft/MS \_consommateur\_bandes](https://twitter.com/microsoft/ms-consumer-brands/)<script>! (fonction) (d, s, id) {var js, fjs = d.getElementsByTagName(s) [0], p = /^http:/.test(d.location) ? 'http' : 'https'. Si ( ! d.getElementById(id)) {js = d.createElement(s) ; js.id = id ; js.src = p + » : / / platform.twitter.com/widgets.js » ; fjs.parentNode.insertBefore (js, fjs) ;}} (document, « script », « wjs-twitter ») ;</script>

### <a name="search"></a>Rechercher

[Tweets sur &quot;#asp.net&quot;](https://twitter.com/search?q=%23asp.net)<script>! (fonction) (d, s, id) {var js, fjs = d.getElementsByTagName(s) [0], p = /^http:/.test(d.location) ? 'http' : 'https'. Si ( ! d.getElementById(id)) {js = d.createElement(s) ; js.id = id ; js.src = p + » : / / platform.twitter.com/widgets.js » ; fjs.parentNode.insertBefore (js, fjs) ;}} (document, « script », « wjs-twitter ») ;</script>
