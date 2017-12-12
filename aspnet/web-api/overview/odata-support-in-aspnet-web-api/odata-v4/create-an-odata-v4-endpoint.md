---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v4/create-an-odata-v4-endpoint
title: "Créer un point de terminaison OData v4 à l’aide d’ASP.NET Web API 2.2 | Documents Microsoft"
author: MikeWasson
description: "Le protocole Open Data Protocol (OData) est un protocole d’accès aux données pour le web. OData fournit une méthode uniforme pour interroger et manipuler des jeux de données via des opérations CRUD..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/24/2014
ms.topic: article
ms.assetid: 1e1927c0-ded1-4752-80fd-a146628d2f09
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v4/create-an-odata-v4-endpoint
msc.type: authoredcontent
ms.openlocfilehash: a3f94818f9674b0e1e9a45b2a6cc9455edc79726
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="create-an-odata-v4-endpoint-using-aspnet-web-api-22"></a>Créer un point de terminaison OData v4 à l’aide d’ASP.NET Web API 2.2
====================
par [Mike Wasson](https://github.com/MikeWasson)

> Le protocole Open Data Protocol (OData) est un protocole d’accès aux données pour le web. OData fournit une méthode uniforme pour interroger et manipuler des jeux de données via des opérations CRUD (créer, lire, mettre à jour et supprimer).
> 
> ASP.NET Web API prend en charge v3 et v4 du protocole. Vous pouvez même demander un point de terminaison v4 qui s’exécute côte à côte avec un point de terminaison v3.
> 
> Ce didacticiel montre comment créer un point de terminaison OData v4 qui prend en charge les opérations CRUD.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - 2.2 d’API Web
> - OData v4
> - [Visual Studio 2013 Update 2](https://www.visualstudio.com/downloads/download-visual-studio-vs)
> - Entity Framework 6
> - .NET 4.5
> 
> 
> ## <a name="tutorial-versions"></a>Versions du didacticiels
> 
> Pour la Version 3 d’OData, consultez [création d’un point de terminaison OData v3](../odata-v3/creating-an-odata-endpoint.md).


## <a name="create-the-visual-studio-project"></a>Créer le projet Visual Studio

Dans Visual Studio, à partir de la **fichier** menu, sélectionnez **nouveau** &gt; **projet**.

Développez **installé** &gt; **modèles** &gt; **Visual C#** &gt; **Web**, puis sélectionnez le  **Application Web ASP.NET** modèle. Nommez le projet &quot;ProductService&quot;.

[![](create-an-odata-v4-endpoint/_static/image2.png)](create-an-odata-v4-endpoint/_static/image1.png)

Dans le **nouveau projet** boîte de dialogue, sélectionnez le **vide** modèle. Sous &quot;ajouter des dossiers et les références de base... &quot;, cliquez sur **API Web**. Cliquez sur **OK**.

[![](create-an-odata-v4-endpoint/_static/image4.png)](create-an-odata-v4-endpoint/_static/image3.png)

## <a name="install-the-odata-packages"></a>Installer les Packages OData

À partir de la **outils** menu, sélectionnez **Gestionnaire de Package NuGet** &gt; **Package Manager Console**. Dans la fenêtre de Console du Gestionnaire de Package, tapez :

[!code-console[Main](create-an-odata-v4-endpoint/samples/sample1.cmd)]

Cette commande installe les packages OData NuGet plus récente.

## <a name="add-a-model-class"></a>Ajouter une classe de modèle

A *modèle* est un objet qui représente une entité de données dans votre application.

Dans l’Explorateur de solutions, cliquez sur le dossier de modèles. Dans le menu contextuel, sélectionnez **ajouter** &gt; **classe**.

[![](create-an-odata-v4-endpoint/_static/image6.png)](create-an-odata-v4-endpoint/_static/image5.png)

> [!NOTE]
> Par convention, les classes de modèle sont placées dans le dossier de modèles, mais vous n’êtes pas obligé de suivent cette convention dans vos propres projets.


Nommez la classe `Product`. Dans le fichier Product.cs, remplacez le code réutilisable avec les éléments suivants :

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample2.cs)]

Le `Id` propriété est la clé d’entité. Les clients peuvent interroger des entités par clé. Par exemple, pour obtenir le produit avec l’ID de 5, l’URI est `/Products(5)`. Le `Id` propriété sera également être la clé primaire dans la base de données principale.

## <a name="enable-entity-framework"></a>Activer l’Entity Framework

Pour ce didacticiel, nous allons utiliser Entity Framework (EF) Code First pour créer la base de données principale.

> [!NOTE]
> OData d’API Web ne nécessite pas de EF. Utilisez n’importe quelle couche d’accès aux données qui peut se traduire par des entités de base de données des modèles.


Tout d’abord, installez le package NuGet pour EF. À partir de la **outils** menu, sélectionnez **Gestionnaire de Package NuGet** &gt; **Package Manager Console**. Dans la fenêtre de Console du Gestionnaire de Package, tapez :

[!code-console[Main](create-an-odata-v4-endpoint/samples/sample3.cmd)]

Ouvrez le fichier Web.config et ajoutez la section suivante à l’intérieur de la **configuration** élément, après le **configSections** élément.

[!code-xml[Main](create-an-odata-v4-endpoint/samples/sample4.xml?highlight=6)]

Ce paramètre ajoute une chaîne de connexion pour une base de données de la base de données locale. Cette base de données sera utilisé lorsque vous exécutez l’application localement.

Ensuite, ajoutez une classe nommée `ProductsContext` dans le dossier Modèles :

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample5.cs)]

Dans le constructeur, `"name=ProductsContext"` donne le nom de la chaîne de connexion.

## <a name="configure-the-odata-endpoint"></a>Configurer le point de terminaison OData

Ouvrez le fichier App\_Start/WebApiConfig.cs. Ajoutez le code suivant **à l’aide de** instructions :

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample6.cs)]

Puis ajoutez le code suivant à la **inscrire** méthode :

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample7.cs)]

Ce code effectue deux opérations :

- Crée un Entity Data Model (EDM).
- Ajoute un itinéraire.

Un modèle EDM est un modèle abstrait des données. Le modèle EDM est utilisé pour créer le document de métadonnées de service. Le **ODataConventionModelBuilder** classe crée un modèle EDM à l’aide des conventions d’affectation de noms par défaut. Cette approche nécessite le moins de code. Si vous souhaitez davantage de contrôle sur le modèle EDM, vous pouvez utiliser la **odatamodelbuilder ayant** classe pour créer le modèle EDM en ajoutant explicitement les propriétés, les clés et les propriétés de navigation.

A *itinéraire* indique la façon de router les requêtes HTTP vers le point de terminaison API Web. Pour créer un itinéraire d’OData v4, appelez le **MapODataServiceRoute** méthode d’extension.

Si votre application comporte plusieurs points de terminaison OData, créez un itinéraire distinct pour chacun. Un nom d’itinéraire unique et un préfixe, donnez à chaque itinéraire.

## <a name="add-the-odata-controller"></a>Ajouter le contrôleur OData

A *contrôleur* est une classe qui gère les requêtes HTTP. Vous créez un contrôleur distinct pour chaque jeu d’entités dans votre service OData. Dans ce didacticiel, vous allez créer un seul contrôleur, pour le `Product` entité.

Dans l’Explorateur de solutions, cliquez sur le dossier contrôleurs, puis sélectionnez **ajouter** &gt; **classe**. Nommez la classe `ProductsController`.

> [!NOTE]
> La version de ce didacticiel pour OData v3 utilise le **ajouter un contrôleur** génération de modèles automatique. Actuellement, il n’existe aucune génération de modèles automatique pour OData v4.


Remplacez le code réutilisable dans ProductsController.cs avec les éléments suivants.

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample8.cs)]

Le contrôleur emploie la `ProductsContext` classe pour accéder à la base de données à l’aide de EF. Notez que le contrôleur substitue le **Dispose** méthode pour les supprimer de la **ProductsContext**.

Il s’agit du point de départ pour le contrôleur. Ensuite, nous allons ajouter des méthodes pour toutes les opérations CRUD.

## <a name="querying-the-entity-set"></a>Interrogation de l’ensemble d’entités

Ajoutez les méthodes suivantes pour `ProductsController`.

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample9.cs)]

La version sans paramètre de la `Get` méthode retourne l’ensemble de produits. Le `Get` méthode avec un *clé* paramètre recherche un produit par sa clé (dans ce cas, le `Id` propriété).

Le **[EnableQuery]** attribut permet aux clients de modifier la requête, à l’aide des options de requête comme $filter, $sort et $page. Pour plus d’informations, consultez [prise en charge des Options de requête OData](../supporting-odata-query-options.md).

## <a name="adding-an-entity-to-the-entity-set"></a>Ajout d’une entité au jeu d’entités

Pour permettre aux clients d’ajouter un nouveau produit à la base de données, ajoutez la méthode suivante à `ProductsController`.

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample10.cs)]

## <a name="updating-an-entity"></a>Mise à jour d'une entité

OData prend en charge les deux une sémantique différente pour mettre à jour une entité, PATCH et PUT.

- CORRECTIF effectue une mise à jour partielle. Le client spécifie uniquement les propriétés à mettre à jour.
- PUT remplace l’entité toute entière.

L’inconvénient de PUT est que le client doit envoyer des valeurs pour toutes les propriétés de l’entité, y compris les valeurs qui ne changent pas. Le [spécification OData](http://docs.oasis-open.org/odata/odata/v4.0/os/part1-protocol/odata-v4.0-os-part1-protocol.html#_Toc372793719) indique que le correctif est préféré.

Dans tous les cas, voici le code pour les méthodes PATCH et PUT :

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample11.cs)]

Dans le cas de correction, le contrôleur utilise le **Delta&lt;T&gt;**  type pour le suivi des modifications.

## <a name="deleting-an-entity"></a>Suppression d'une entité

Pour activer les clients supprimer un produit à partir de la base de données, ajoutez la méthode suivante à `ProductsController`.

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample12.cs)]
