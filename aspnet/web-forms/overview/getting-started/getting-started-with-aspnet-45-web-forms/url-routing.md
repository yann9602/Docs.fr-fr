---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/url-routing
title: "Routage d’URL | Documents Microsoft"
author: Erikre
description: "Cette série de didacticiels, vous allez apprendre les principes fondamentaux de la création d’une application Web Forms ASP.NET à l’aide de ASP.NET 4.5 et Microsoft Visual Studio Express 2013 pour nous..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/08/2014
ms.topic: article
ms.assetid: 4f4bf092-c400-471f-a876-78fda0417890
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/url-routing
msc.type: authoredcontent
ms.openlocfilehash: 279617e4ebb475d935c0d1e01e08a3a2def0f9e9
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="url-routing"></a>Routage d’URL
====================
Par [Erik Reitan](https://github.com/Erikre)

[Télécharger Wingtip Toys exemple de projet (c#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) ou [télécharger des livres (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)

> Cette série de didacticiels, vous allez apprendre les principes fondamentaux de la création d’une application Web Forms ASP.NET à l’aide de ASP.NET 4.5 et Microsoft Visual Studio Express 2013 pour le Web. Un Visual Studio 2013 [projet avec le code source c#](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) est disponible pour accompagner cette série de didacticiels.


Dans ce didacticiel, vous allez modifier l’exemple d’application Wingtip Toys pour prendre en charge le routage d’URL. Routage permet à votre application web utiliser des URL qui sont mieux pris en charge par les moteurs de recherche, plus facile à mémoriser et convivial. Ce didacticiel s’appuie sur le didacticiel précédent, « L’appartenance et Administration » et fait partie de la série de didacticiels Wingtip Toys.

## <a name="what-youll-learn"></a>Ce que vous allez apprendre :

- Comment inscrire des itinéraires pour une application Web Forms ASP.NET.
- Comment ajouter des itinéraires à une page web.
- Comment sélectionner des données dans une base de données pour prendre en charge des itinéraires.

## <a name="aspnet-routing-overview"></a>Vue d’ensemble du routage ASP.NET

Routage d’URL vous permet de configurer une application à accepter les URL qui ne sont pas mappent à des fichiers physiques de la requête. Une URL de demande est simplement l’URL dans laquelle un utilisateur entre dans son navigateur pour rechercher une page sur votre site web. Utiliser le routage pour définir des URL qui sont sémantiquement significatives pour les utilisateurs et qui peuvent aider à avec l’optimisation de moteur de recherche (SEO).

Par défaut, le modèle Web Forms comprend [les URL conviviales ASP.NET](http://www.nuget.org/packages/Microsoft.AspNet.FriendlyUrls/). L’essentiel du travail de routage base sera implémentée à l’aide de *URL conviviales*. Toutefois, dans ce didacticiel, vous allez ajouter des fonctionnalités de routage personnalisées.

Avant de personnaliser le routage d’URL, l’exemple d’application Wingtip Toys peut lier à un produit à l’aide de l’URL suivante :

`https://localhost:44300/ProductDetails.aspx?productID=2`

En personnalisant le routage d’URL, l’exemple d’application Wingtip Toys sera lier avec le même produit à l’aide d’une URL de lisibilité :

`https://localhost:44300/Product/Convertible%20Car`

### <a name="routes"></a>Itinéraires

Un itinéraire est un modèle d’URL qui est mappé à un gestionnaire. Le gestionnaire peut être un fichier physique, tel qu’un fichier .aspx dans une application Web Forms. Un gestionnaire peut également être une classe qui traite la demande. Pour définir un itinéraire, vous créez une instance de la classe d’itinéraire en spécifiant le type d’URL, le gestionnaire et éventuellement un nom pour l’itinéraire.

Ajouter de l’itinéraire à l’application en ajoutant le `Route` objet à la méthode statique `Routes` propriété de la `RouteTable` classe. La propriété d’itinéraires est une `RouteCollection` objet qui stocke tous les itinéraires de l’application.

### <a name="url-patterns"></a>Modèles d’URL

Un modèle d’URL peut contenir des valeurs littérales et des espaces réservés de variable (appelés paramètres d’URL). Les littéraux et les espaces réservés se trouvent dans des segments de l’URL qui sont délimités par une barre oblique (`/`) caractères.

Lorsqu’une demande à votre application web est effectuée, l’URL est analysée dans les segments et des espaces réservés et les valeurs des variables sont fournies au Gestionnaire de requêtes. Ce processus est similaire à la façon dont les données dans une chaîne de requête sont analysées et passées au Gestionnaire de requêtes. Dans les deux cas, les informations sur les variables sont incluses dans l’URL et passées au gestionnaire sous la forme de paires clé-valeur. Pour les chaînes de requête, les clés et les valeurs sont dans l’URL. Pour les itinéraires, les clés sont les noms de l’espace réservé définis dans le modèle d’URL, et seules les valeurs figurent dans l’URL.

Dans un modèle d’URL, vous définissez des espaces réservés en les plaçant entre accolades ( `{` et `}` ). Vous pouvez définir plusieurs espaces réservés dans un segment, mais les espaces réservés doivent être séparés par une valeur littérale. Par exemple, `{language}-{country}/{action}` est un modèle d’itinéraire valide. Toutefois, `{language}{country}/{action}` n’est pas un modèle valide, car il n’existe aucune valeur littérale ou un séparateur entre les espaces réservés. Par conséquent, le routage ne peut pas déterminer où séparer la valeur de l’espace réservé de langage à partir de la valeur de l’espace réservé de pays.

### <a name="mapping-and-registering-routes"></a>Mappage et de l’inscription d’itinéraires

Avant d’inclure les itinéraires vers les pages de l’exemple d’application Wingtip Toys, vous devez enregistrer les itinéraires lorsque l’application démarre. Pour enregistrer les itinéraires, vous allez modifier le `Application_Start` Gestionnaire d’événements.

1. Dans **l’Explorateur de solutions**de Visual Studio, recherchez et ouvrez le *Global.asax.cs* fichier.
2. Ajoutez le code mis en surbrillance en jaune pour le *Global.asax.cs* de fichiers comme suit :   

    [!code-csharp[Main](url-routing/samples/sample1.cs?highlight=30-31,34-46)]

Lorsque le Wingtip Toys exemple de démarrage de l’application, il appelle le `Application_Start` Gestionnaire d’événements. À la fin de ce gestionnaire d’événements, la `RegisterCustomRoutes` méthode est appelée. Le `RegisterCustomRoutes` méthode ajoute chaque itinéraire en appelant le `MapPageRoute` méthode de la `RouteCollection` objet. Itinéraires sont définis à l’aide d’un nom d’itinéraire, une URL de routage et une URL physique.

Le premier paramètre («`ProductsByCategoryRoute`») est le nom d’itinéraire. Elle est utilisée pour appeler l’itinéraire lorsqu’il est nécessaire. Le deuxième paramètre («`Category/{categoryName}`») définit le remplacement convivial URL qui peut être dynamique basée sur du code. Vous utilisez cet itinéraire lorsque vous remplissez un contrôle de données avec des liens qui sont générés en fonction des données. Un itinéraire est la suivante :

[!code-csharp[Main](url-routing/samples/sample2.cs)]

Le deuxième paramètre de l’itinéraire inclut une valeur dynamique spécifiée par des accolades (`{ }`). Dans ce cas, le `categoryName` est une variable qui doit servir à déterminer le chemin de routage approprié.

> [!NOTE] 
> 
> **Optional**
> 
> Il peut s’avérer plus facile à gérer votre code en déplaçant le `RegisterCustomRoutes` méthode à une classe distincte. Dans le *logique* dossier, créez un distinct `RouteActions` classe. Déplacer des éléments ci-dessus `RegisterCustomRoutes` méthode à partir de la *Global.asax.cs* fichier dans le nouvel `RoutesActions` classe. Utilisez le `RoleActions` classe et le `createAdmin` un exemple de l’appel à la méthode le `RegisterCustomRoutes` méthode à partir de la *Global.asax.cs* fichier.


Vous aurez peut-être également remarqué la `RegisterRoutes` appel de méthode à l’aide du `RouteConfig` objet au début de la `Application_Start` Gestionnaire d’événements. Cet appel est effectué pour implémenter le routage par défaut. Il a été fourni en tant que code par défaut lorsque vous avez créé l’application à l’aide du modèle de Web Forms de Visual Studio.

## <a name="retrieving-and-using-route-data"></a>La récupération et à l’aide des données d’itinéraire

Comme indiqué ci-dessus, les itinéraires peuvent être définies. Le code que vous avez ajouté à la `Application_Start` Gestionnaire d’événements dans le *Global.asax.cs* fichier charge les itinéraires définis.

### <a name="setting-routes"></a>Définition des itinéraires

Itinéraires vous amener à ajouter du code. Dans ce didacticiel, vous utiliserez la liaison de modèle pour récupérer un `RouteValueDictionary` objet qui est utilisé lors de la génération des itinéraires à l’aide des données à partir d’un contrôle de données. Le `RouteValueDictionary` objet contient la liste des noms de produits qui appartiennent à une catégorie spécifique de produits. Un lien est créé pour chaque produit basé sur les données et l’itinéraire.

#### <a name="enable-routes-for-categories-and-products"></a>Activer les itinéraires pour les catégories et les produits

Ensuite, vous allez mettre à jour l’application pour utiliser le `ProductsByCategoryRoute` pour déterminer l’itinéraire correct à inclure pour chaque lien de catégorie de produit. Vous devez également mettre à jour le *ProductList.aspx* page pour inclure un lien de routage pour chaque produit. Les liens seront affichés car ils se trouvaient avant la modification, toutefois, les liens utiliseront dorénavant de routage d’URL.

1. Dans **l’Explorateur de solutions**, ouvrez le *Site.Master* page si elle n’est pas déjà ouverte.
2. Mise à jour la **ListView** contrôle nommé «`categoryList`» avec les modifications de mise en surbrillance en jaune, par conséquent, le balisage se présente comme suit :   

    [!code-aspx[Main](url-routing/samples/sample3.aspx?highlight=7-9)]
3. Dans **l’Explorateur de solutions**, ouvrez le *ProductList.aspx* page.
4. Mise à jour la `ItemTemplate` élément de la *ProductList.aspx* page avec les mises à jour en surbrillance en jaune, donc le balisage se présente comme suit :   

    [!code-aspx[Main](url-routing/samples/sample4.aspx?highlight=6-9,14-16)]
5. Ouvrez le fichier code-behind de *ProductList.aspx.cs* et ajoutez l’espace de noms suivant en tant que mises en surbrillance en jaune :  

    [!code-csharp[Main](url-routing/samples/sample5.cs?highlight=9)]
6. Remplacez le `GetProducts` méthode le code-behind (*ProductList.aspx.cs*) avec le code suivant :   

    [!code-csharp[Main](url-routing/samples/sample6.cs)]

#### <a name="add-code-for-product-details"></a>Ajouter du Code pour des détails sur le produit

À présent, mettez à jour le code-behind (*ProductDetails.aspx.cs*) pour le *ProductDetails.aspx* page à utiliser les données d’itinéraire. Notez que la nouvelle `GetProduct` méthode accepte également une valeur de chaîne de requête pour le cas où l’utilisateur a un lien de signet qui utilise l’URL non conviviales et non routé plus anciens.

1. Remplacez le `GetProduct` méthode le code-behind (*ProductDetails.aspx.cs*) avec le code suivant :   

    [!code-csharp[Main](url-routing/samples/sample7.cs)]

## <a name="running-the-application"></a>Exécution de l'application

Vous pouvez exécuter l’application maintenant pour voir les itinéraires de mise à jour.

1. Appuyez sur **F5** pour exécuter l’exemple d’application Wingtip Toys.  
 Le navigateur s’ouvre et affiche le *Default.aspx* page.
2. Cliquez sur le **produits** lien en haut de la page.  
 Tous les produits sont affichés sur la *ProductList.aspx* page. L’URL suivante (à l’aide de votre numéro de port) est affiché pour le navigateur :  
    `https://localhost:44300/ProductList`
3. Ensuite, cliquez sur le **voitures** lien vers le haut de la page de la catégorie.  
 Uniquement les voitures sont affichées sur la *ProductList.aspx* page. L’URL suivante (à l’aide de votre numéro de port) est affiché pour le navigateur :  
    `https://localhost:44300/Category/Cars`
4. Cliquez sur le lien contenant le nom de la première voiture répertorié dans la page («**Convertible voiture**») pour afficher les détails du produit.  
 L’URL suivante (à l’aide de votre numéro de port) est affiché pour le navigateur :  
    `https://localhost:44300/Product/Convertible%20Car`
5. Ensuite, entrez l’URL suivante non routé (à l’aide de votre numéro de port) dans le navigateur :  
    `https://localhost:44300/ProductDetails.aspx?productID=2`  
 Le code reconnaît toujours une URL qui inclut une chaîne de requête, dans le cas où un utilisateur a un lien de signet.

## <a name="summary"></a>Résumé

Dans ce didacticiel, vous avez ajouté des itinéraires pour les catégories et les produits. Vous avez appris comment les itinéraires peuvent être intégrés à des contrôles de données qui utilisent la liaison de modèle. Dans l’étape suivante du didacticiel, vous allez implémenter la gestion des erreurs global.

## <a name="additional-resources"></a>Ressources supplémentaires

[URL conviviales ASP.NET](http://www.nuget.org/packages/Microsoft.AspNet.FriendlyUrls/)  
[Déployer une application de formulaires Web ASP.NET sécurisées avec l’appartenance, OAuth et base de données SQL dans Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/)  
[Microsoft Azure - version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/)

>[!div class="step-by-step"]
[Précédent](membership-and-administration.md)
[Suivant](aspnet-error-handling.md)
