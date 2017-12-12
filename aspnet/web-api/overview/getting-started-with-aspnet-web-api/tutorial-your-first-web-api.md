---
uid: web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api
title: Getting Started with ASP.NET Web API 2 (c#)
author: MikeWasson
description: "HTTP n’est pas simplement pour traiter des pages web. Il est également une plateforme puissante pour la création d’API qui exposent des services et des données. HTTP est simple et flexible et ubiq..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/28/2017
ms.topic: article
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api
msc.type: authoredcontent
ms.openlocfilehash: fa1cd068a7466e0b6b6fe7716090c8a7afd2a4d5
ms.sourcegitcommit: ec9371e2fbfcb8d62e7e7cae69e7752f3f205385
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
<a name="getting-started-with-aspnet-web-api-2-c"></a>Getting Started with ASP.NET Web API 2 (c#)
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](https://code.msdn.microsoft.com/Sample-code-of-Getting-c56ccb28)

HTTP n’est pas simplement pour traiter des pages web. HTTP est également une plateforme puissante pour la création d’API qui exposent des services et des données. HTTP est simple, flexible et très répandue. Presque n’importe quelle plateforme qui vous pouvez considérer a une bibliothèque de HTTP, pour les services HTTP peuvent atteindre un large éventail de clients, y compris les navigateurs des appareils mobiles et des applications de bureau traditionnelles.
 
API Web ASP.NET est une infrastructure pour la génération de web API par-dessus le .NET Framework. Dans ce didacticiel, vous allez utiliser des API Web ASP.NET pour créer une API qui renvoie une liste de produits web.

 
 ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
  
 - [Visual Studio 2017](https://www.visualstudio.com/downloads/)
 - API Web 2

Consultez [créer une API web avec ASP.NET Core et Visual Studio pour Windows](https://docs.microsoft.com/aspnet/core/tutorials/first-web-api) pour une version plus récente de ce didacticiel.

## <a name="create-a-web-api-project"></a>Créez un projet d’API Web

Dans ce didacticiel, vous allez utiliser des API Web ASP.NET pour créer une API qui renvoie une liste de produits web. La page web frontal utilise jQuery pour afficher les résultats.

![](tutorial-your-first-web-api/_static/image1.png)

Démarrez Visual Studio et sélectionnez **nouveau projet** à partir de la **Démarrer** page. Ou, à partir de la **fichier** menu, sélectionnez **nouveau** , puis **projet**.

Dans le **modèles** volet, sélectionnez **modèles installés** et développez le **Visual C#** nœud. Sous **Visual C#**, sélectionnez **Web**. Dans la liste des modèles de projet, sélectionnez **Application Web ASP.NET**. Nommez le projet « ProductsApp » et cliquez sur **OK**.

![](tutorial-your-first-web-api/_static/image2.png)

Dans le **nouveau projet ASP.NET** boîte de dialogue, sélectionnez le **vide** modèle. Sous &quot;ajouter des dossiers et de base pour les références&quot;, vérifiez **API Web**. Cliquez sur **OK**.

![](tutorial-your-first-web-api/_static/image3.png)

> [!NOTE]
> Vous pouvez également créer un projet d’API Web à l’aide du &quot;API Web&quot; modèle. Le modèle de l’API Web utilise ASP.NET MVC pour fournir des pages d’aide API. J’utilise le modèle vide pour ce didacticiel, car je souhaite afficher des API Web sans MVC. En règle générale, vous n’avez pas besoin de savoir ASP.NET MVC à utiliser l’API Web.


## <a name="adding-a-model"></a>Ajout d’un modèle

Un *modèle* est un objet qui représente les données dans votre application. API Web ASP.NET peut sérialiser automatiquement votre modèle à un autre format, XML ou JSON, puis écrire les données sérialisées dans le corps du message de réponse HTTP. Tant qu’un client peut lire le format de sérialisation, il peut désérialiser l’objet. La plupart des clients peut analyser XML ou JSON. En outre, le client peut indiquer quel format il souhaite en définissant l’en-tête Accept dans le message de demande HTTP.

Commençons par créer un modèle simple qui représente un produit.

Si l’Explorateur de solutions n’est pas déjà visible, cliquez sur le **vue** menu et sélectionnez **l’Explorateur de solutions**. Dans l’Explorateur de solutions, cliquez sur le dossier de modèles. Dans le menu contextuel, sélectionnez **ajouter** puis sélectionnez **classe**.

![](tutorial-your-first-web-api/_static/image4.png)

Nom de la classe &quot;produit&quot;. Ajoutez les propriétés suivantes à la `Product` classe.

[!code-csharp[Main](tutorial-your-first-web-api/samples/sample1.cs)]

## <a name="adding-a-controller"></a>Ajour d’un contrôleur

Dans l’API Web, un *contrôleur* est un objet qui gère les requêtes HTTP. Nous allons ajouter un contrôleur qui peut retourner une liste de produits ou un produit unique spécifié par ID.

> [!NOTE]
> Si vous avez utilisé ASP.NET MVC, vous êtes déjà familiarisé avec les contrôleurs. Contrôleurs de l’API Web sont semblables aux contrôleurs MVC, mais héritent la **ApiController** classe au lieu du **contrôleur** classe.

Dans **l’Explorateur de solutions**, cliquez sur le dossier contrôleurs. Sélectionnez **ajouter** , puis sélectionnez **contrôleur**.

![](tutorial-your-first-web-api/_static/image5.png)

Dans le **ajouter une vue de structure** boîte de dialogue, sélectionnez **contrôleur d’API Web - vide**. Cliquez sur **Ajouter**.

![](tutorial-your-first-web-api/_static/image6.png)

Dans le **ajouter un contrôleur** boîte de dialogue, le nom du contrôleur &quot;ProductsController&quot;. Cliquez sur **Ajouter**.

![](tutorial-your-first-web-api/_static/image7.png)

La génération de modèles automatique crée un fichier nommé ProductsController.cs dans le dossier contrôleurs.

![](tutorial-your-first-web-api/_static/image8.png)

> [!NOTE]
> Vous n’avez pas besoin de placer vos contrôleurs dans un dossier nommé contrôleurs. Le nom du dossier est simplement un moyen pratique d’organiser vos fichiers source.


Si ce fichier n’est pas déjà ouvert, double-cliquez sur le fichier pour l’ouvrir. Remplacez le code dans ce fichier avec les éléments suivants :

[!code-csharp[Main](tutorial-your-first-web-api/samples/sample2.cs)]

Pour que l’exemple reste simple, les produits sont stockés dans un tableau fixe à l’intérieur de la classe de contrôleur. Bien entendu, dans une application réelle, vous serez interroger une base de données ou utiliser une autre source de données externe.

Le contrôleur définit deux méthodes qui retournent des produits :

- Le `GetAllProducts` méthode renvoie la liste complète des produits en tant qu’un **IEnumerable&lt;produit&gt;**  type.
- Le `GetProduct` méthode recherche un produit unique par son ID.

Voilà ! Vous avez un site web API. Chaque méthode sur le contrôleur correspond à un ou plusieurs URI :

| Méthode de contrôleur | URI |
| --- | --- |
| GetAllProducts | produits/api / |
| GetProduct | /API/produits/*id* |

Pour le `GetProduct` (méthode), la *id* dans l’URI est un espace réservé. Par exemple, pour obtenir le produit avec l’ID de 5, l’URI est `api/products/5`.

Pour plus d’informations sur comment API Web achemine les demandes HTTP aux méthodes de contrôleur, consultez [le routage ASP.NET Web API](../web-api-routing-and-actions/routing-in-aspnet-web-api.md).

## <a name="calling-the-web-api-with-javascript-and-jquery"></a>Appel de l’API Web avec Javascript et jQuery

Dans cette section, nous allons ajouter une page HTML qui utilise AJAX pour appeler l’API web. Nous allons utiliser jQuery pour effectuer les appels AJAX et également à la page mises à jour avec les résultats.

Dans l’Explorateur de solutions, cliquez sur le projet et sélectionnez **ajouter**, puis sélectionnez **un nouvel élément**.

![](tutorial-your-first-web-api/_static/image9.png)

Dans le **ajouter un nouvel élément** boîte de dialogue, sélectionnez le **Web** nœud sous **Visual C#**, puis sélectionnez le **HTML Page** élément. Nommez la page &quot;index.html&quot;.

![](tutorial-your-first-web-api/_static/image10.png)

Remplacer tous les éléments de ce fichier avec les éléments suivants :

[!code-html[Main](tutorial-your-first-web-api/samples/sample3.html)]

Il existe plusieurs façons d’obtenir jQuery. Dans cet exemple, j’ai utilisé la [CDN Microsoft Ajax](../../../ajax/cdn/overview.md). Vous pouvez également le télécharger à partir de [http://jquery.com/](http://jquery.com/)et ASP.NET « API Web » modèle de projet inclut également de jQuery.

### <a name="getting-a-list-of-products"></a>Obtention d’une liste de produits

Pour obtenir une liste de produits, envoyez une requête HTTP GET pour &quot;/api/produits&quot;.

JQuery [getJSON](http://api.jquery.com/jQuery.getJSON/) fonction envoie une requête AJAX. Pour la réponse contient un tableau d’objets JSON. Le `done` fonction spécifie un rappel qui est appelé si la demande réussit. Dans le rappel, nous mettre à jour le modèle DOM avec les informations de produit.

[!code-html[Main](tutorial-your-first-web-api/samples/sample4.html)]

### <a name="getting-a-product-by-id"></a>Obtention d’un produit par ID

Pour obtenir un produit par ID, envoyez une demande HTTP GET pour &quot;/API/produits/*id*&quot;, où *id* est l’ID de produit.

[!code-javascript[Main](tutorial-your-first-web-api/samples/sample5.js)]

Nous avons toujours appeler `getJSON` pour envoyer la requête AJAX, mais cette fois, nous plaçons l’ID dans l’URI de requête. La réponse à partir de cette demande est une représentation JSON d’un produit unique.

## <a name="running-the-application"></a>Exécution de l'application

Appuyez sur F5 pour démarrer le débogage de l’application. La page web doit se présenter comme suit :

![](tutorial-your-first-web-api/_static/image11.png)

Pour obtenir un produit par ID, entrez l’ID, puis cliquez sur Rechercher :

![](tutorial-your-first-web-api/_static/image12.png)

Si vous entrez un ID non valide, le serveur retourne une erreur HTTP :

![](tutorial-your-first-web-api/_static/image13.png)

## <a name="using-f12-to-view-the-http-request-and-response"></a>À l’aide de la touche F12 pour afficher la requête et réponse HTTP

Lorsque vous travaillez avec un service HTTP, il peut être très utile pour voir la requête HTTP et les messages de demande. Vous pouvez le faire en utilisant les outils de développement F12 dans Internet Explorer 9. À partir d’Internet Explorer 9, appuyez sur **F12** pour ouvrir les outils. Cliquez sur le **réseau** onglet, puis appuyez sur **démarrer capture**. Revenez maintenant à la page web et appuyez sur **F5** pour recharger la page web. Internet Explorer capture le trafic HTTP entre le navigateur et le serveur web. La vue résumée affiche tout le trafic réseau pour une page :

![](tutorial-your-first-web-api/_static/image14.png)

Recherchez l’entrée de l’URI relatif « api/produits / ». Sélectionnez cette entrée et cliquez sur **vue détaillée**. Dans la vue détail, il existe les onglets pour afficher les en-têtes de demande et de réponse et le corps. Par exemple, si vous cliquez sur le **en-têtes de demande** onglet, vous pouvez voir que le client a demandé &quot;application/json&quot; dans l’en-tête Accept.

![](tutorial-your-first-web-api/_static/image15.png)

Si vous cliquez sur l’onglet du corps de réponse, vous pouvez voir comment la liste de produits a été sérialisée au format JSON. Autres navigateurs ont des fonctionnalités similaires. Est un autre outil utile [Fiddler](http://www.fiddler2.com/fiddler2/), un serveur web proxy de débogage. Vous pouvez utiliser Fiddler pour afficher le trafic HTTP et également pour composer des requêtes HTTP, ce qui vous donne un contrôle total sur les en-têtes HTTP dans la demande.

## <a name="see-this-app-running-on-azure"></a>Consultez cette application en cours d’exécution sur Azure

Vous voulez voir le site terminé en cours d’exécution comme une application web dynamique ? Vous pouvez déployer une version complète de l’application à votre compte Azure en cliquant simplement sur le bouton suivant.

[![](http://azuredeploy.net/deploybutton.png)](https://deploy.azure.com/?WT.mc_id=deploy_azure_aspnet&repository=https://github.com/tfitzmac/WebAPI-ProductsApp#/form/setup)

Vous avez besoin d’un compte Azure pour déployer cette solution sur Azure. Si vous n’avez pas déjà un compte, vous disposez des options suivantes :

- [Ouvrir un compte Azure gratuitement](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A443DD604) -vous obtenez des crédits que vous pouvez utiliser pour tester les services Azure payants et même après qu’ils soient utilisés jusqu'à, vous pouvez conserver le compte et libérer de l’utilisation des services Azure.
- [Activer les abonnés MSDN](https://azure.microsoft.com/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) -votre abonnement MSDN vous donne des crédits chaque mois que vous pouvez utiliser pour les services Azure payants.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir un exemple plus complet d’un service HTTP qui prend en charge les actions de POST, PUT et DELETE et écrit dans une base de données, consultez [à l’aide de Web API 2 avec Entity Framework 6](../data/using-web-api-with-entity-framework/part-1.md).
- Pour plus d’informations sur la création d’applications web fluide et réactif sur un service HTTP, consultez [Application à une Page ASP.NET](../../../single-page-application/index.md).
- Pour plus d’informations sur la façon de déployer un projet web Visual Studio pour le Service d’applications Azure, consultez [créer une application web ASP.NET dans Azure App Service](https://azure.microsoft.com/en-us/documentation/articles/web-sites-dotnet-get-started/).
