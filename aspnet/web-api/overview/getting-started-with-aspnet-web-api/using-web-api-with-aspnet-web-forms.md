---
uid: web-api/overview/getting-started-with-aspnet-web-api/using-web-api-with-aspnet-web-forms
title: "À l’aide des API Web avec ASP.NET Web Forms | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/03/2012
ms.topic: article
ms.assetid: 25da8c3f-4e90-4946-9765-4f160985e1e4
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/getting-started-with-aspnet-web-api/using-web-api-with-aspnet-web-forms
msc.type: authoredcontent
ms.openlocfilehash: af918671a8bcc97a0050ea033ccd14dd96416e73
ms.sourcegitcommit: 037d3900f739dbaa2ba14158e3d7dc81478952ad
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
<a name="using-web-api-with-aspnet-web-forms"></a>À l’aide des API Web avec ASP.NET Web Forms
====================
par [Mike Wasson](https://github.com/MikeWasson)

Bien que l’API Web ASP.NET est empaqueté avec ASP.NET MVC, il est facile d’ajouter des API Web à une application Web Forms ASP.NET traditionnelle. Ce didacticiel vous guide à travers les étapes.

## <a name="overview"></a>Vue d'ensemble

Pour utiliser des API Web dans une application Web Forms, il existe deux étapes principales :

- Ajouter un contrôleur d’API Web qui dérive de la **ApiController** classe.
- Ajouter une table de routage pour le **Application\_Démarrer** (méthode).

## <a name="create-a-web-forms-project"></a>Créer un projet Web Forms

Démarrez Visual Studio et sélectionnez **nouveau projet** à partir de la **Démarrer** page. Ou, à partir de la **fichier** menu, sélectionnez **nouveau** , puis **projet**.

Dans le **modèles** volet, sélectionnez **modèles installés** et développez le **Visual C#** nœud. Sous **Visual C#**, sélectionnez **Web**. Dans la liste des modèles de projet, sélectionnez **Application ASP.NET Web Forms**. Entrez un nom pour le projet et cliquez sur **OK**.

![](using-web-api-with-aspnet-web-forms/_static/image1.png)

## <a name="create-the-model-and-controller"></a>Créer le modèle et le contrôleur

Ce didacticiel utilise les mêmes classes de modèle et du contrôleur en tant que le [mise en route](tutorial-your-first-web-api.md) didacticiel.

Tout d’abord, ajoutez une classe de modèle. Dans **l’Explorateur de solutions**, cliquez sur le projet et sélectionnez **ajouter une classe**. Nom de la classe de produit et ajoutez l’implémentation suivante :

[!code-csharp[Main](using-web-api-with-aspnet-web-forms/samples/sample1.cs)]

Ensuite, ajoutez un contrôleur d’API Web au projet., A *contrôleur* est l’objet qui gère les requêtes HTTP pour l’API Web.

Dans **l’Explorateur de solutions**, cliquez sur le projet. Sélectionnez **ajouter un nouvel élément**.

![](using-web-api-with-aspnet-web-forms/_static/image2.png)

Sous **modèles installés**, développez **Visual C#** et sélectionnez **Web**. Puis, dans la liste des modèles, sélectionnez **classe contrôleur d’API Web**. Nommez le contrôleur « ProductsController » et cliquez sur **ajouter**.

![](using-web-api-with-aspnet-web-forms/_static/image3.png)

Le **ajouter un nouvel élément** Assistant va créer un fichier nommé ProductsController.cs. Supprimez les méthodes inclus par l’Assistant et ajoutez les méthodes suivantes :

[!code-csharp[Main](using-web-api-with-aspnet-web-forms/samples/sample2.cs)]

Pour plus d’informations sur le code de ce contrôleur, consultez le [mise en route](tutorial-your-first-web-api.md) didacticiel.

## <a name="add-routing-information"></a>Ajouter des informations de routage

Ensuite, nous allons ajouter un itinéraire de l’URI, cet URI sous la forme &quot;/API/produits/&quot; sont acheminés vers le contrôleur.

Dans **l’Explorateur de solutions**, double-cliquez sur Global.asax pour ouvrir le fichier code-behind Global.asax.cs. Ajoutez le code suivant **à l’aide de** instruction.

[!code-csharp[Main](using-web-api-with-aspnet-web-forms/samples/sample3.cs)]

Puis ajoutez le code suivant à la **Application\_Démarrer** méthode :

[!code-csharp[Main](using-web-api-with-aspnet-web-forms/samples/sample4.cs)]

Pour plus d’informations sur les tables de routage, consultez [le routage ASP.NET Web API](../web-api-routing-and-actions/routing-in-aspnet-web-api.md).

## <a name="add-client-side-ajax"></a>Ajouter les AJAX côté Client

Qui est que nécessaire pour créer un site web API que les clients peuvent accéder. Maintenant vous allez ajouter une page HTML qui utilise jQuery pour appeler l’API.

Assurez-vous que votre page maître (par exemple, *Site.Master*) inclut un `ContentPlaceHolder` avec `ID="HeadContent"`:

[!code-html[Main](using-web-api-with-aspnet-web-forms/samples/sample8.html)]

Ouvrez le fichier Default.aspx. Remplacez le texte réutilisable qui se trouve dans la section de contenu principale, comme indiqué :

[!code-aspx[Main](using-web-api-with-aspnet-web-forms/samples/sample5.aspx)]

Ensuite, ajoutez une référence au fichier source jQuery dans les `HeaderContent` section :

[!code-aspx[Main](using-web-api-with-aspnet-web-forms/samples/sample6.aspx?highlight=2)]

Remarque : Vous pouvez facilement ajouter la référence de script en faisant glisser le fichier à partir de **l’Explorateur de solutions** dans la fenêtre de l’éditeur de code.

![](using-web-api-with-aspnet-web-forms/_static/image4.png)

Sous la balise de script jQuery, ajoutez le bloc de script suivant :

[!code-html[Main](using-web-api-with-aspnet-web-forms/samples/sample7.html)]

Lors du chargement du document, ce script effectue une requête AJAX à &quot;api/produits&quot;. La requête retourne une liste de produits au format JSON. Le script ajoute les informations de produit à la table HTML.

Lorsque vous exécutez l’application, il doit ressembler à ceci :

![](using-web-api-with-aspnet-web-forms/_static/image5.png)
