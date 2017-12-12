---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v3/creating-an-odata-endpoint
title: "Création d’un point de terminaison OData v3 avec l’API Web 2 | Documents Microsoft"
author: MikeWasson
description: "Le protocole Open Data Protocol (OData) est un protocole d’accès aux données pour le web. OData fournit une méthode uniforme pour la structure des données, interroger les données et manipuler les données en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/25/2014
ms.topic: article
ms.assetid: 262843d6-43a2-4f1c-82d9-0b90ae6df0cf
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v3/creating-an-odata-endpoint
msc.type: authoredcontent
ms.openlocfilehash: cb466124aacf6b13c1ade22ad8b865b83e6351e2
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-an-odata-v3-endpoint-with-web-api-2"></a>Création d’un point de terminaison OData v3 avec l’API Web 2
====================
par [Mike Wasson](https://github.com/MikeWasson)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/ASPNET-Web-API-OData-cecdb524)

> Le [Open Data Protocol](http://www.odata.org/) (OData) est un protocole d’accès aux données pour le web. OData fournit une méthode uniforme pour la structure des données, interroger les données et manipuler le jeu de données via des opérations CRUD (créer, lire, mettre à jour et supprimer). OData prend en charge à la fois les formats JSON et AtomPub (XML). OData définit également une méthode pour exposer des métadonnées sur les données. Les clients peuvent utiliser les métadonnées pour découvrir les informations de type et les relations pour le jeu de données.
> 
> API Web ASP.NET facilite la création d’un point de terminaison OData pour un jeu de données. Vous pouvez contrôler exactement quelles opérations OData prend en charge par le point de terminaison. Vous pouvez héberger plusieurs points de terminaison OData, en même temps que les points de terminaison non-OData. Vous avez un contrôle total sur votre modèle de données, la logique d’entreprise principale et la couche données.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - API Web 2
> - OData Version 3
> - Entity Framework 6
> - [Web Fiddler débogage Proxy (facultatif)](http://www.fiddler2.com)
> 
> Prise en charge des API OData Web a été ajoutée dans [ASP.NET et Web Tools 2012.2 à jour](https://go.microsoft.com/fwlink/?LinkId=282650). Toutefois, ce didacticiel utilise la structure qui a été ajoutée dans Visual Studio 2013.


Dans ce didacticiel, vous allez créer un point de terminaison OData simple, les clients peuvent interroger. Vous allez également créer un client c# pour le point de terminaison. Après avoir terminé ce didacticiel, l’ensemble suivant de didacticiels montrent comment ajouter des fonctionnalités, y compris les relations d’entité, actions, puis développez $/ $.

- [Créer le projet Visual Studio](#create-project)
- [Ajouter un modèle d’entité](#add-model)
- [Ajouter un contrôleur OData](#add-controller)
- [Ajouter le modèle EDM et itinéraire](#edm)
- [Valeur initiale de la base de données (facultatif)](#seed-db)
- [Exploration du point de terminaison OData](#explore)
- [Formats de sérialisation OData](#formats)

<a id="create-project"></a>
## <a name="create-the-visual-studio-project"></a>Créer le projet Visual Studio

Dans ce didacticiel, vous allez créer un point de terminaison OData qui prend en charge les opérations CRUD de base. Le point de terminaison exposera une seule ressource, une liste de produits. Didacticiels ultérieurs ajouterez d’autres fonctionnalités.

Démarrez Visual Studio et sélectionnez **nouveau projet** à partir de la page de démarrage. Ou, à partir de la **fichier** menu, sélectionnez **nouveau** , puis **projet**.

Dans le **modèles** volet, sélectionnez **modèles installés** et développez le nœud Visual c#. Sous **Visual C#**, sélectionnez **Web**. Sélectionnez **l’Application Web ASP.NET** modèle.

![](creating-an-odata-endpoint/_static/image1.png)

Dans le **nouveau projet ASP.NET** boîte de dialogue, sélectionnez le **vide** modèle. Sous &quot;ajouter des dossiers et pour les références de base... &quot;, vérifiez **API Web**. Cliquez sur **OK**.

![](creating-an-odata-endpoint/_static/image2.png)

<a id="add-model"></a>
## <a name="add-an-entity-model"></a>Ajouter un modèle d’entité

Un *modèle* est un objet qui représente les données dans votre application. Pour ce didacticiel, nous avons besoin d’un modèle qui représente un produit. Le modèle correspond à notre type d’entité OData.

Dans l’Explorateur de solutions, cliquez sur le dossier de modèles. Dans le menu contextuel, sélectionnez **ajouter** puis sélectionnez **classe**.

![](creating-an-odata-endpoint/_static/image3.png)

Dans le **nouveau** élément boîte de dialogue, nommez la classe &quot;produit&quot;.

![](creating-an-odata-endpoint/_static/image4.png)

> [!NOTE]
> Par convention, les classes de modèle sont placées dans le dossier de modèles. Vous n’êtes pas obligé de suivent cette convention dans vos propres projets, mais nous allons l’utiliser pour ce didacticiel.


Dans le fichier Product.cs, ajoutez la définition de classe suivante :

[!code-csharp[Main](creating-an-odata-endpoint/samples/sample1.cs)]

La propriété ID sera la clé d’entité. Les clients peuvent interroger les produits par ID. Ce champ serait également la clé primaire dans la base de données principale.

Générez le projet maintenant. Dans l’étape suivante, nous allons utiliser certains la structure de Visual Studio qui utilise la réflexion pour rechercher le type de produit.

<a id="add-controller"></a>
## <a name="add-an-odata-controller"></a>Ajouter un contrôleur OData

A *contrôleur* est une classe qui gère les requêtes HTTP. Vous définissez un contrôleur distinct pour chaque entité définie dans votre service OData. Dans ce didacticiel, nous allons créer un seul contrôleur.

Dans l’Explorateur de solutions, cliquez sur le dossier contrôleurs. Sélectionnez **ajouter** , puis sélectionnez **contrôleur**.

![](creating-an-odata-endpoint/_static/image5.png)

Dans le **ajouter une vue de structure** boîte de dialogue, sélectionnez &quot;Web API 2 OData contrôleur avec actions, utilisant Entity Framework&quot;.

![](creating-an-odata-endpoint/_static/image6.png)

Dans le **ajouter un contrôleur** boîte de dialogue, le nom du contrôleur « ProductsController ». Sélectionnez le &quot;utiliser les actions de contrôleur asynchrones&quot; case à cocher. Dans le **modèle** liste déroulante, sélectionnez la classe de produit.

![](creating-an-odata-endpoint/_static/image7.png)

Cliquez sur le **nouveau contexte de données...**  bouton. Laissez le nom par défaut pour le type de contexte de données, puis cliquez sur **ajouter**.

![](creating-an-odata-endpoint/_static/image8.png)

Dans la boîte de dialogue Ajouter un contrôleur pour ajouter le contrôleur, cliquez sur Ajouter.

![](creating-an-odata-endpoint/_static/image9.png)

Remarque : Si vous obtenez un message d’erreur qui indique que &quot;comportait une erreur d’obtention du type... &quot;, assurez-vous que vous avez créé le projet Visual Studio une fois que vous avez ajouté la classe de produit. La génération de modèles automatique utilise la réflexion pour déterminer la classe.

![](creating-an-odata-endpoint/_static/image10.png)

La génération de modèles automatique ajoute deux fichiers de code au projet :

- Products.cs définit le contrôleur d’API Web qui implémente le point de terminaison OData.
- ProductServiceContext.cs fournit des méthodes pour interroger la base de données sous-jacente à l’aide d’Entity Framework.

![](creating-an-odata-endpoint/_static/image11.png)

<a id="edm"></a>
## <a name="add-the-edm-and-route"></a>Ajouter le modèle EDM et itinéraire

Dans l’Explorateur de solutions, développez l’application\_dossier Démarrer et d’ouvrir le fichier nommé WebApiConfig.cs. Cette classe contient le code de configuration pour l’API Web. Remplacez ce code avec les éléments suivants :

[!code-csharp[Main](creating-an-odata-endpoint/samples/sample2.cs)]

Ce code effectue deux opérations :

- Crée un modèle EDM (Entity Data Model) pour le point de terminaison OData.
- Ajoute un itinéraire pour le point de terminaison.

Un modèle EDM est un modèle abstrait des données. Le modèle EDM est utilisé pour créer le document de métadonnées et de définir l’URI pour le service. Le **ODataConventionModelBuilder** crée un modèle EDM à l’aide d’un ensemble de conventions d’affectation de noms par défaut EDM. Cette approche nécessite le moins de code. Si vous souhaitez davantage de contrôle sur le modèle EDM, vous pouvez utiliser la **odatamodelbuilder ayant** classe pour créer le modèle EDM en ajoutant explicitement les propriétés, les clés et les propriétés de navigation.

Le **EntitySet** méthode ajoute une entité au modèle EDM :

[!code-csharp[Main](creating-an-odata-endpoint/samples/sample3.cs)]

La chaîne « Products » définit le nom de l’ensemble d’entités. Le nom du contrôleur doit correspondre au nom de l’ensemble d’entités. Dans ce didacticiel, le jeu d’entités est nommé « Products » et le contrôleur est nommé `ProductsController`. Si vous avez nommé « ProductSet » du jeu d’entités, vous devez le nommer le contrôleur `ProductSetController`. Notez qu’un point de terminaison peut avoir plusieurs jeux d’entités. Appelez **EntitySet&lt;T&gt;**  pour chaque entité définie et ensuite définir un contrôleur correspondant.

Le **MapODataRoute** méthode ajoute un itinéraire pour le point de terminaison OData.

[!code-csharp[Main](creating-an-odata-endpoint/samples/sample4.cs)]

Le premier paramètre est un nom convivial pour l’itinéraire. Ce nom n’apparaît pas dans les clients de votre service. Le deuxième paramètre est le préfixe URI pour le point de terminaison. Étant donné ce code, l’URI pour le jeu d’entités de produits est http://*nom d’hôte*odata/produits. Votre application peut avoir plus d’un point de terminaison OData. Pour chaque point de terminaison, appelez **MapODataRoute** et fournir un nom d’itinéraire unique et un préfixe URI unique.

<a id="seed-db"></a>
## <a name="seed-the-database-optional"></a>Valeur initiale de la base de données (facultatif)

Dans cette étape, vous utiliserez Entity Framework pour amorcer la base de données avec des données de test. Cette étape est facultative, mais il vous permet de tester votre point de terminaison OData immédiatement.

À partir de la **outils** menu, sélectionnez **Gestionnaire de Package de bibliothèque**, puis sélectionnez **Package Manager Console**. Dans la fenêtre de Console du Gestionnaire de Package, entrez la commande suivante :

[!code-console[Main](creating-an-odata-endpoint/samples/sample5.cmd)]

Cela ajoute un dossier nommé Migrations et un fichier de code nommé Configuration.cs.

![](creating-an-odata-endpoint/_static/image12.png)

Ouvrez ce fichier et ajoutez le code suivant à la `Configuration.Seed` (méthode).

[!code-csharp[Main](creating-an-odata-endpoint/samples/sample6.cs)]

Dans la fenêtre de la Console Gestionnaire de Package, entrez les commandes suivantes :

[!code-console[Main](creating-an-odata-endpoint/samples/sample7.cmd)]

Ces commandes génèrent du code qui crée la base de données, puis exécute ce code.

<a id="explore"></a>
## <a name="exploring-the-odata-endpoint"></a>Exploration du point de terminaison OData

Dans cette section, nous allons utiliser la [Proxy de débogage Web Fiddler](http://www.fiddler2.com) pour envoyer des demandes au point de terminaison et examinez les messages de réponse. Cela vous aidera à comprendre les fonctions d’un point de terminaison OData.

Dans Visual Studio, appuyez sur F5 pour démarrer le débogage. Par défaut, Visual Studio ouvre votre navigateur `http://localhost:*port*`, où *port* est le numéro de port configuré dans les paramètres du projet.

Vous pouvez modifier le numéro de port dans les paramètres du projet. Dans l’Explorateur de solutions, cliquez sur le projet et sélectionnez **propriétés**. Dans la fenêtre Propriétés, sélectionnez **Web**. Entrez le numéro de port sous **Url de projet**.

### <a name="service-document"></a>Document de service

Le *document de service* contient une liste de jeux d’entités pour le point de terminaison OData. Pour obtenir le document de service, envoie une requête GET à l’URI du service de racine.

À l’aide de Fiddler, entrez l’URI suivant dans le **Composer** onglet : `http://localhost:port/odata/`, où *port* est le numéro de port.

![](creating-an-odata-endpoint/_static/image13.png)

Cliquez sur le **Execute** bouton. Fiddler envoie une requête HTTP GET à votre application. Vous devez voir la réponse dans la liste des Sessions Web. Si tout fonctionne, le code d’état sera de 200.

![](creating-an-odata-endpoint/_static/image14.png)

Double-cliquez sur la réponse dans la liste des Sessions Web pour afficher les détails du message de réponse dans l’onglet inspecteurs.

![](creating-an-odata-endpoint/_static/image15.png)

Le message de réponse HTTP brut doit ressembler à ce qui suit :

[!code-console[Main](creating-an-odata-endpoint/samples/sample8.cmd)]

Par défaut, les API Web retourne le document de service dans le format AtomPub. Pour demander à JSON, ajoutez l’en-tête suivant à la requête HTTP :

`Accept: application/json`

![](creating-an-odata-endpoint/_static/image16.png)

La réponse HTTP contient maintenant une charge utile JSON :

[!code-console[Main](creating-an-odata-endpoint/samples/sample9.cmd)]

### <a name="service-metadata-document"></a>Document de métadonnées de service

Le *document de métadonnées de service* décrit le modèle de données du service, à l’aide d’un langage XML appelé le schéma de définition du langage CSDL (Conceptual). Le document de métadonnées montre la structure des données dans le service et peut être utilisé pour générer le code client.

Pour obtenir le document de métadonnées, envoyez une demande GET pour `http://localhost:port/odata/$metadata`. Voici les métadonnées du point de terminaison indiqué dans ce didacticiel.

[!code-console[Main](creating-an-odata-endpoint/samples/sample10.cmd)]

### <a name="entity-set"></a>Jeu d'entités

Pour obtenir le jeu d’entités de produits, envoyez une demande GET pour `http://localhost:port/odata/Products`.

[!code-console[Main](creating-an-odata-endpoint/samples/sample11.cmd)]

### <a name="entity"></a>Entité

Pour obtenir un produit individuel, envoyez une demande GET pour `http://localhost:port/odata/Products(1)`, où « 1 » est l’ID de produit.

[!code-console[Main](creating-an-odata-endpoint/samples/sample12.cmd)]

<a id="formats"></a>
## <a name="odata-serialization-formats"></a>Formats de sérialisation OData

OData prend en charge plusieurs formats de sérialisation :

- Atom Pub (XML)
- JSON « light » (introduite dans OData v3)
- JSON « commentaires » (OData v2)

Par défaut, API Web utilise le format de AtomPubJSON « light ». 

Pour obtenir le format AtomPub, définissez l’en-tête Accept pour « application/atom + xml ». Voici un exemple de corps de réponse :

[!code-console[Main](creating-an-odata-endpoint/samples/sample13.cmd)]

Vous pouvez voir l’un des inconvénients évidents du format Atom : il est beaucoup plus détaillé que la lumière JSON. Toutefois, si vous avez un client qui comprend AtomPub, le client peut préférer ce format JSON.

Voici une version légère JSON de la même entité :

[!code-console[Main](creating-an-odata-endpoint/samples/sample14.cmd)]

Le format JSON light a été introduit dans la version 3 du protocole OData. Pour la compatibilité descendante, un client peut demander l’ancien format JSON « commentaires ». Pour demander JSON détaillés, définissez l’en-tête Accept `application/json;odata=verbose`. Voici la version détaillée :

[!code-console[Main](creating-an-odata-endpoint/samples/sample15.cmd)]

Ce format véhicule davantage de métadonnées dans le corps de réponse, ce qui peut ajouter une charge considérable sur un ensemble de la session. En outre, il ajoute un niveau d’indirection en encapsulant l’objet dans une propriété nommée « d ».

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter des Relations d’entité](working-with-entity-relations.md)
- [Ajouter des Actions OData](odata-actions.md)
- [Appeler le Service OData à partir d’un Client .NET](calling-an-odata-service-from-a-net-client.md)
