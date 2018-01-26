---
uid: web-forms/overview/getting-started/hands-on-labs/whats-new-in-web-forms-in-aspnet-45
title: "Nouveautés de Web Forms dans ASP.NET 4.5 | Documents Microsoft"
author: rick-anderson
description: "La nouvelle version de Web Forms ASP.NET présente plusieurs améliorations consacré à améliorer l’expérience utilisateur lorsque vous travaillez avec des données. Dans les versions précédentes de..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/18/2013
ms.topic: article
ms.assetid: 0a1f88bd-97da-4ed1-86f1-605199dc75a4
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/getting-started/hands-on-labs/whats-new-in-web-forms-in-aspnet-45
msc.type: authoredcontent
ms.openlocfilehash: 560f949f79be8ba4936e4a6f8ee8ee32ef15acbf
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="whats-new-in-web-forms-in-aspnet-45"></a>Quelles sont les nouveautés dans les formulaires Web dans ASP.NET 4.5
====================
par [Web Camps équipe](https://twitter.com/webcamps)

> La nouvelle version de Web Forms ASP.NET présente plusieurs améliorations consacré à améliorer l’expérience utilisateur lorsque vous travaillez avec des données.
> 
> Dans les versions précédentes de Web Forms, lors de l’utilisation de la liaison de données à émettre la valeur d’un membre d’objet, vous avez utilisé les expressions de liaison de données Bind() ou Eval(). Dans la nouvelle version d’ASP.NET, vous êtes en mesure de déclarer le type de données un contrôle va être lié à l’aide d’une nouvelle propriété ItemType. Définition de cette propriété vous permettra d’utiliser une variable fortement typée pour profiter pleinement des avantages de l’expérience de développement Visual Studio, telles que IntelliSense, la navigation des membres et la vérification de la compilation.
> 
> Avec les contrôles liés aux données, vous pouvez désormais aussi spécifier vos propres méthodes personnalisées pour la sélection, la mise à jour, suppression et insertion de données, ce qui simplifie l’interaction entre les contrôles de page et votre logique d’application. En outre, les fonctionnalités de liaison de modèle ont été ajoutées pour ASP.NET, ce qui signifie que vous pouvez mapper des données à partir de la page directement dans les paramètres de type de méthode.
> 
> Validation des entrées utilisateur doit également être plus facile avec la dernière version de Web Forms. Vous pouvez maintenant annoter vos classes de modèle avec des attributs de validation à partir de la **System.ComponentModel.DataAnnotations** espace de noms et demande que tous les sites de votre contrôle valident des entrées d’utilisateur avec ces informations. La validation côté client dans les formulaires Web est désormais intégrée à jQuery, auquel le nettoyeur le code côté client et des fonctionnalités de JavaScript non obstructifs.
> 
> Dans la zone de la validation de requête, des améliorations ont été apportées pour le rendre plus facile désactiver la validation de la demande de certaines parties de vos applications ou de lire les données de demande non valide de manière sélective.
> 
> Des améliorations ont été apportées aux Web Forms des contrôles de serveur pour tirer parti des nouvelles fonctionnalités de HTML5 :
> 
> - La propriété TextMode du contrôle de zone de texte a été mis à jour pour prendre en charge les nouveaux types d’entrée de HTML5, par courrier électronique, datetime et ainsi de suite.
> - Le contrôle FileUpload prend désormais en charge plusieurs téléchargements de fichiers à partir de navigateurs qui prennent en charge cette fonctionnalité HTML5.
> - Le programme de validation contrôle désormais prise en charge de validation HTML5 éléments d’entrée.
> - Prend en charge les nouveaux éléments HTML5 qui ont des attributs qui représentent une URL maintenant runat =&quot;server&quot;. Par conséquent, vous pouvez utiliser les conventions ASP.NET dans les chemins d’URL, comme le ~ (opérateur) pour représenter la racine de l’application (par exemple, &lt;vidéo runat =&quot;server&quot; src =&quot;~/myVideo.wmv&quot; &gt; &lt;/vidéo&gt;).
> - Le contrôle UpdatePanel a été résolu pour prendre en charge des champs d’entrée de validation HTML5.
> 
> Vous trouverez plus d’exemples des nouvelles fonctionnalités dans ASP.NET Web Forms 4.5 dans le portail ASP.NET officiel : [Nouveautés de ASP.NET 4.5 et Visual Studio 2012](../../../../whitepapers/whats-new-in-aspnet-45-and-visual-studio-2012.md#_Toc318097385)
> 
> Tous les exemples de code et des extraits de code sont inclus dans le Kit de formation Camps Web, disponible à l’adresse [https://go.microsoft.com/fwlink/?LinkID=248297&clcid=0x409](https://go.microsoft.com/fwlink/?LinkID=248297&clcid=0x409).


<a id="Objectives"></a>
### <a name="objectives"></a>Objectifs

Dans cet atelier pratique, vous allez apprendre comment :

- Utiliser des expressions de liaison de données fortement typées
- Utiliser les nouvelles fonctionnalités de liaison de modèle dans les formulaires Web
- Utiliser des fournisseurs de valeurs pour le mappage des données de page aux méthodes de code-behind
- Utiliser des Annotations de données pour la validation d’entrée utilisateur
- Prendre advange d’unobstrusive la validation côté client avec jQuery dans les Web Forms
- Implémenter la validation de la demande granulaire
- Implémenter le traitement dans les formulaires Web asynchrone de la page

<a id="Prerequisites"></a>
### <a name="prerequisites"></a>Prérequis

Vous devez disposer des éléments suivants pour effectuer ce laboratoire :

- [Microsoft Visual Studio Express 2012 pour Web](https://www.microsoft.com/visualstudio/eng/products/visual-studio-express-for-web) ou supérieure (lecture [annexe A](#AppendixA) pour obtenir des instructions sur l’installation).

<a id="Setup"></a>
### <a name="setup"></a>Installation

**L’installation des extraits de Code**

Pour plus de commodité, une grande partie du code que vous gérez le long de cet atelier est disponible sous les extraits de code Visual Studio. Pour installer les extraits de code exécuter **.\Source\Setup\CodeSnippets.vsi** fichier.

Si vous n’êtes pas familiarisé avec les extraits de Code Visual Studio et vous souhaitez savoir comment les utiliser, vous pouvez faire référence à l’annexe de ce document &quot; [extraits de Code à l’aide de l’annexe c :](#AppendixC)&quot;.

<a id="Exercises"></a>
## <a name="exercises"></a>Exercices

Cet atelier pratique inclut les exercices suivants :

1. [Exercice 1 : Modèle de liaison dans les formulaires Web ASP.NET](#Exercise1)
2. [Exercice 2 : Validation des données](#Exercise2)
3. [Exercice 3 : Page asynchrone dans ASP.NET Web de traitement de formulaires](#Exercise3)

> [!NOTE]
> Chaque exercice est accompagné par un **fin** dossier qui contient la solution résultante, vous devez obtenir après avoir effectué les exercices. Si vous avez besoin d’aide fonctionne via les exercices, vous pouvez utiliser cette solution comme guide.


Durée estimée pour effectuer ce laboratoire : **60 minutes**.

<a id="Exercise1"></a>

<a id="Exercise_1_Model_Binding_in_ASPNET_Web_Forms"></a>
### <a name="exercise-1-model-binding-in-aspnet-web-forms"></a>Exercice 1 : Modèle de liaison dans les formulaires Web ASP.NET

La nouvelle version de Web Forms ASP.NET introduit de nombreuses améliorations centrée sur l’amélioration de l’expérience lorsque vous travaillez avec des données. Tout au long de cet exercice, vous allez en savoir plus sur les contrôles de données fortement typées et liaison de modèle.

<a id="Task_1_-_Using_Strongly-Typed_Data-Bindings"></a>
#### <a name="task-1---using-strongly-typed-data-bindings"></a>Tâche 1 - à l’aide de liaisons de données fortement typées

Dans cette tâche, vous allez découvrir les nouvelles fortement typée liaisons disponibles dans ASP.NET 4.5.

1. Ouvrez le **commencer** solution situé dans **début/ModelBinding-Ex1/Source/** dossier.

    1. Vous devez télécharger des packages NuGet manquants avant de poursuivre. Pour ce faire, cliquez sur le **projet** menu et sélectionnez **gérer les Packages NuGet**.
    2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **restaurer** afin de télécharger les packages manquants.
    3. Enfin, générez la solution en cliquant sur **générer** | **générer la Solution**.

    > [!NOTE]
    > Un des avantages de l’utilisation de NuGet est que vous ne devez expédier toutes les bibliothèques dans votre projet, ce qui réduit la taille du projet. Avec NuGet Power Tools, en spécifiant les versions de package dans le fichier Packages.config, vous serez en mesure de télécharger toutes les bibliothèques requises à la première fois que vous exécutez le projet. C’est pourquoi vous devez exécuter ces étapes après avoir ouvert une solution existante à partir de ce laboratoire.
2. Ouvrez le **Customers.aspx** page. Placer une liste non numérotée dans le contrôle principal et inclure un contrôle de répéteur à l’intérieur pour répertorier chaque client. Définissez le nom du répéteur sur **customersRepeater** comme indiqué dans le code suivant.

    Dans les versions précédentes de Web Forms, lors de l’utilisation de la liaison de données à émettre la valeur d’un membre d’un objet vous êtes de liaison de données, vous utiliseriez une expression de liaison de données, ainsi que d’un appel à la méthode Eval, transmettre le nom du membre en tant que chaîne.

    Lors de l’exécution, ces appels à Eval utiliser la réflexion sur l’objet actuellement lié pour lire la valeur du membre avec le nom donné et afficher le résultat dans le code HTML. Cette approche facilite à lier aux données sur des données arbitraires, unshaped.

    Malheureusement, vous perdez de nombreuses fonctionnalités une excellente expérience de développement dans Visual Studio, notamment IntelliSense pour les noms de membres, la prise en charge de navigation (par exemple, atteindre la définition) et la vérification de la compilation.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample1.aspx)]
3. Ouvrez le **Customers.aspx.cs** fichier.
4. Ajoutez le code suivant à l’aide d’instruction.

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample2.cs)]
5. Dans le **Page\_charge** (méthode), ajoutez le code pour remplir le répéteur avec la liste des clients.

    (Code d’extrait de code - *Web Source de données des clients Forms Lab - Ex01 - Bind*)

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample3.cs)]

    La solution utilise EntityFramework avec CodeFirst pour créer et accéder à la base de données. Dans le code suivant, le customersRepeater est liée à une requête de vue qui retourne tous les clients à partir de la base de données.
6. Appuyez sur **F5** pour exécuter la solution et accédez à la **clients** page pour afficher la répétition de l’action. Comme la solution est à l’aide de CodeFirst, la base de données sera créée et remplie dans votre instance locale de SQL Express lors de l’exécution de l’application.

    ![Les clients avec un répéteur](whats-new-in-web-forms-in-aspnet-45/_static/image1.png "répertoriant les clients avec un répéteur.")

    *Les clients avec un répéteur.*

    > [!NOTE]
    > Dans Visual Studio 2012, IIS Express est le serveur de développement Web par défaut.
7. Fermez le navigateur et revenez à Visual Studio.
8. Maintenant, remplacez l’implémentation pour utiliser les liaisons fortement typées. Ouvrez le **Customers.aspx** page et utiliser la nouvelle **ItemType** attribut dans le répéteur pour définir le **client** type que le type de liaison.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample4.aspx)]

    La propriété ItemType vous permet de déclarer le type de données le contrôle doit être lié à et vous permet d’utiliser fortement typée de liaison dans le contrôle lié aux données.
9. Remplacez l’ItemTemplate contenu par le code suivant.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample5.aspx)]

    Un inconvénient avec l’une des approches ci-dessus est les appels à Eval() et Bind() à liaison tardive - ce qui signifie que vous passez des chaînes pour représenter les noms de propriété. Cela signifie que vous n’obtenez pas Intellisense pour les noms de membres, la prise en charge pour la navigation du code (par exemple, atteindre la définition), ni prise en charge de la vérification de la compilation.

    Définition de la propriété ItemType entraîne deux nouvelles variables typées à générer dans l’étendue des expressions de liaison de données : **élément** et **BindItem**. Vous pouvez utiliser ces variables fortement typées dans les expressions de liaison de données et obtenir le meilleur parti de l’expérience de développement Visual Studio.

    Le &quot; **:** &quot; utilisé dans l’expression sera automatiquement encoder en HTML la sortie pour éviter les problèmes de sécurité (par exemple, les attaques scripts entre sites). Cette notation n’était disponible depuis le .NET 4 pour l’écriture de réponse, mais il est également disponible dans les expressions de liaison de données.

    > [!NOTE]
    > Le membre de l’élément fonctionne pour les liaisons unidirectionnelles. Si vous souhaitez effectuer une liaison bidirectionnelle utiliser le **BindItem** membre.

    ![Prise en charge d’IntelliSense dans la liaison de fortement typée](whats-new-in-web-forms-in-aspnet-45/_static/image2.png "prise en charge d’IntelliSense dans la liaison de fortement typé")

    *Prise en charge d’IntelliSense dans la liaison de fortement typé*
10. Appuyez sur **F5** pour exécuter la solution et accédez à la page de clients pour vous assurer que les modifications fonctionnent comme prévu.

    ![Affichage des détails sur le client](whats-new-in-web-forms-in-aspnet-45/_static/image3.png "affichage des détails du client")

    *Affichage des détails du client*
11. Fermez le navigateur et revenez à Visual Studio.

<a id="Task_2_-_Introducing_Model_Binding_in_Web_Forms"></a>
#### <a name="task-2---introducing-model-binding-in-web-forms"></a>Tâche 2 - modèle de présentation de liaison dans les formulaires Web

Dans les versions précédentes d’ASP.NET Web Forms, lorsque vous souhaitez effectuer la liaison de données bidirectionnelle, la récupération et de mise à jour des données, vous deviez utiliser un objet de Source de données. Cela peut être un objet de Source de données, une Source de données SQL, une Source de données LINQ et ainsi de suite. Toutefois si votre scénario nécessaire code personnalisé pour gérer les données, vous avez besoin d’utiliser la Source de données objet et cela mis des inconvénients. Par exemple, vous avez besoin éviter les types complexes et vous avez besoin de gérer les exceptions lors de l’exécution de la logique de validation.

Dans la nouvelle version de Web Forms ASP.NET les contrôles liés aux données prennent en charge la liaison de modèle. Cela signifie que vous pouvez spécifier la sélectionner, mettre à jour, insérer et supprimer les méthodes directement dans le contrôle lié aux données pour appeler la logique à partir de votre fichier code-behind ou d’une autre classe.

Pour plus d’informations, vous allez utiliser un GridView pour répertorier les catégories de produits à l’aide de la nouvelle **SelectMethod** attribut. Cet attribut vous permet de spécifier une méthode de récupération des données de GridView.

1. Ouvrez le **Products.aspx** page et inclure une **GridView**. Configurer le contrôle GridView comme indiqué ci-dessous pour utiliser des liaisons fortement typée et activer le tri et la pagination.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample6.aspx)]
2. Utilisez la nouvelle **SelectMethod** attribut pour configurer le contrôle GridView à appeler une **GetCategories** (méthode) pour sélectionner les données.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample7.aspx)]
3. Ouvrez le **Products.aspx.cs** code-behind du fichier et ajoutez le code suivant à l’aide d’instructions.

    (Code d’extrait de code - *Web Forms Lab - Ex01 - espaces de noms*)

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample8.cs)]
4. Ajouter un membre privé dans le **produits** classe et attribuer une nouvelle instance de **ProductsContext**. Cette propriété stocke le contexte de données Entity Framework qui permet de vous connecter à la base de données.

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample9.cs)]
5. Créer un **GetCategories** pour récupérer la liste des catégories à l’aide de LINQ. La requête inclut la **produits** propriété pour le contrôle GridView peut afficher la quantité de produits pour chaque catégorie. Notez que la méthode retourne un objet IQueryable brut qui représentent la requête pour être exécutée ultérieurement sur le cycle de vie de page.

    (Code d’extrait de code - *Web Forms Lab - Ex01 - GetCategories*)

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample10.cs)]

    > [!NOTE]
    > Dans les versions précédentes d’ASP.NET Web Forms, l’activation de tri et de pagination à l’aide de votre propre logique de référentiel dans un contexte de l’objet Source de données, requis pour écrire votre propre code personnalisé et recevoir tous les paramètres nécessaires. À présent, comme les méthodes de liaison de données peuvent retourner IQueryable et il s’agit d’une requête pour être exécutée, peuvent prendre en charge ASP.NET de la modification de la requête pour ajouter un tri et les paramètres de la pagination.
6. Appuyez sur **F5** pour démarrer le débogage du site et accédez à la page produits. Vous devez voir que le contrôle GridView est rempli avec les catégories retournés par la méthode GetCategories.

    ![Remplissage d’un contrôle GridView à l’aide de la liaison de modèle](whats-new-in-web-forms-in-aspnet-45/_static/image4.png "remplissage d’un contrôle GridView à l’aide de la liaison de modèle")

    *Remplissage d’un contrôle GridView à l’aide de la liaison de modèle*
7. Appuyez sur **MAJ**+**F5** arrêter le débogage.

<a id="Task_3_-_Value_Providers_in_Model_Binding"></a>
#### <a name="task-3---value-providers-in-model-binding"></a>Tâche 3 : les fournisseurs de valeur dans la liaison de modèle

Liaison de modèle non seulement vous permet de spécifier des méthodes personnalisées pour travailler avec vos données directement dans le contrôle lié aux données, mais permet également de mapper les données de la page dans les paramètres à partir de ces méthodes. Sur le paramètre de méthode, vous pouvez utiliser les attributs de fournisseur de valeur pour spécifier la source de données de la valeur. Exemple :

- Contrôles sur la page
- Valeurs de chaîne de requête
- Afficher les données
- État de session
- Cookies
- Données de formulaire publiées
- État d’affichage
- Fournisseurs de valeurs personnalisées sont également prises en charge

Si vous avez utilisé ASP.NET MVC 4, vous remarquerez que la prise en charge de liaison de modèle est similaire. En effet, ces fonctionnalités ont été effectuées à partir d’ASP.NET MVC et déplacées vers le **System.Web** assembly pour pouvoir l’utiliser aussi sur des Web Forms.

Dans cette tâche, vous mettrons à jour le contrôle GridView pour filtrer les résultats par la quantité de produits pour chaque catégorie, reçoit le paramètre de filtre avec la liaison de modèle.

1. Revenez à la **Products.aspx** page.
2. En haut du contrôle GridView, ajoutez un **étiquette** et un **ComboBox** pour sélectionner le nombre de produits pour chaque catégorie, comme illustré ci-dessous.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample11.aspx)]
3. Ajouter un **EmptyDataTemplate** au contrôle GridView pour afficher un message lorsque aucune catégorie avec le nombre sélectionné de produits.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample12.aspx)]
4. Ouvrez le **Products.aspx.cs** code-behind et ajoutez le code suivant à l’aide d’instruction.

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample13.cs)]
5. Modifier la **GetCategories** méthode pour recevoir un entier **minProductsCount** argument et filtrer les résultats retournés. Pour ce faire, remplacez la méthode par le code suivant.

    (Code d’extrait de code - *Web Forms Lab - Ex01 - GetCategories 2*)

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample14.cs)]

    La nouvelle **[contrôle]** de l’attribut le **minProductsCount** argument indiquera ASP.NET sa valeur doit être remplie à l’aide d’un contrôle dans la page. ASP.NET recherchera à n’importe quel contrôle qui correspond au nom de l’argument (minProductsCount) et effectuer le mappage nécessaire et conversion pour remplir le paramètre avec la valeur du contrôle.

    Vous pouvez également l’attribut fournit un constructeur surchargé qui vous permet de spécifier le contrôle à partir de laquelle obtenir la valeur.

    > [!NOTE]
    > Une des fonctionnalités de liaison de données vise à réduire la quantité de code qui doit être écrite pour l’interaction de la page. Mis à part le fournisseur de valeur [contrôle], vous pouvez utiliser d’autres fournisseurs de liaison de modèle dans vos paramètres de méthode. Certaines d'entre elles sont répertoriées dans l’introduction de la tâche.
6. Appuyez sur **F5** pour démarrer le débogage du site et accédez à la page produits. Sélectionnez un nombre de produits dans la liste déroulante et notez la façon dont le contrôle GridView est désormais mis à jour.

    ![Le contrôle GridView avec une valeur de liste déroulante de filtrage](whats-new-in-web-forms-in-aspnet-45/_static/image5.png "filtrage GridView avec une valeur de liste déroulante")

    *Le contrôle GridView avec une valeur de liste déroulante de filtrage*
7. Arrêter le débogage.

<a id="Task_4_-_Using_Model_Binding_for_Filtering"></a>
#### <a name="task-4---using-model-binding-for-filtering"></a>Tâche 4 : le modèle à l’aide de la liaison pour le filtrage

Dans cette tâche, vous allez ajouter une deuxième enfant GridView pour afficher les produits de la catégorie sélectionnée.

1. Ouvrez le **Products.aspx** page et de mettre à jour les catégories de GridView pour générer automatiquement le bouton de sélection.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample15.aspx)]
2. Ajoutez un deuxième **GridView** nommé **productsGrid** en bas. Définir le **ItemType** à **WebFormsLab.Model.Product**, le **DataKeyNames** à **ProductId** et **SelectMethod**  à **GetProducts**. Définissez **AutoGenerateColumns** à **false** et ajoutez les colonnes pour ProductId, ProductName, Description et prix unitaire.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample16.aspx)]
3. Ouvrez le **Products.aspx.cs** fichier code-behind. Implémentez la **GetProducts** pour recevoir l’ID de catégorie de la catégorie de GridView et filtrer les produits. Liaison de modèle définit la valeur de paramètre à l’aide de la ligne sélectionnée dans le **categoriesGrid**. Étant donné que le nom d’argument et le nom de contrôle ne correspondent pas, vous devez spécifier le nom du contrôle dans le fournisseur de valeur de contrôle.

    (Code d’extrait de code - *Web Forms Lab - Ex01 - GetProducts*)

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample17.cs)]

    > [!NOTE]
    > Cette approche facilite unité ces méthodes de test. Dans un contexte de test unitaire, où les formulaires Web n’est pas exécuté, l’attribut [contrôle] n’effectuera pas une action spécifique.
4. Ouvrez le **Products.aspx** page et rechercher les produits GridView. Mettre à jour les produits GridView pour afficher un lien pour modifier le produit sélectionné.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample18.aspx)]
5. Ouvrez le **ProductDetails.aspx** page code-behind et remplacez le **SelectProduct** méthode avec le code suivant.

    (Code d’extrait de code - *Web Forms Lab - Ex01 - SelectProduct méthode*)

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample19.cs)]

    > [!NOTE]
    > Notez que la **[QueryString]** attribut est utilisé pour remplir le paramètre de méthode à partir d’un paramètre de productId dans la chaîne de requête.
6. Appuyez sur **F5** pour démarrer le débogage du site et accédez à la page produits. Sélectionnez n’importe quelle catégorie parmi les catégories GridView et les produits GridView est mis à jour.

    ![Affichage des produits de la catégorie sélectionnée](whats-new-in-web-forms-in-aspnet-45/_static/image6.png "affichant les produits de la catégorie sélectionnée")

    *Affichage des produits de la catégorie sélectionnée*
7. Cliquez sur le **vue** lien sur un produit pour ouvrir la page ProductDetails.aspx.

    Notez que la page de la récupération du produit avec SelectMethod en utilisant le paramètre productId à partir de la chaîne de requête.

    ![Affichage des détails du produit](whats-new-in-web-forms-in-aspnet-45/_static/image7.png "affichage des détails du produit")

    *Affichage des détails du produit*

    > [!NOTE]
    > La possibilité de taper une description HTML sera implémentée dans l’exercice suivant.

<a id="Task_5_-_Using_Model_Binding_for_Update_Operations"></a>
#### <a name="task-5---using-model-binding-for-update-operations"></a>Tâche 5 - modèle à l’aide de la liaison pour les opérations de mise à jour

Dans la tâche précédente, vous avez utilisé la liaison de modèle principalement de la sélection des données, dans cette tâche, vous allez apprendre à utiliser la liaison de modèle dans les opérations de mise à jour.

Vous mettrez à jour les catégories de GridView pour permettre à l’utilisateur de mettre à jour des catégories.

1. Ouvrez le **Products.aspx** page et de mettre à jour les catégories de GridView pour générer automatiquement le bouton Modifier et utiliser le nouveau **UpdateMethod** attribut pour spécifier un **UpdateCategory ne**pour mettre à jour l’élément sélectionné.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample20.aspx)]

    L’attribut DataKeyNames dans le GridView définir qui sont les membres qui identifient de façon unique l’objet lié au modèle et par conséquent, qui sont les paramètres de que la méthode de mise à jour doit s’afficher au moins.
2. Ouvrez le **Products.aspx.cs** fichier code-behind et implémentez la **UpdateCategory ne** (méthode). La méthode doit recevoir l’ID de catégorie pour charger la catégorie actuelle et remplir les valeurs du contrôle GridView puis mettre à jour de la catégorie.

    (Code d’extrait de code - *Web Forms, Lab - Ex01 - UpdateCategory ne*)

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample21.cs)]

    La nouvelle **TryUpdateModel** méthode dans la classe de Page est chargée de remplissage de l’objet de modèle avec les valeurs des contrôles de la page. Dans ce cas, il remplace les valeurs mises à jour à partir de la ligne GridView actuelle en cours de modification dans le **catégorie** objet.

    > [!NOTE]
    > L’exercice suivant explique l’utilisation de la ModelState.IsValid pour valider les données entrées par l’utilisateur lors de la modification de l’objet.
3. Exécuter le site et accédez à la page produits. Modifier une catégorie. Tapez un nouveau nom, puis **mise à jour** pour conserver les modifications.

    ![Modification des catégories](whats-new-in-web-forms-in-aspnet-45/_static/image8.png "modification des catégories")

    *Modification des catégories*

<a id="Exercise2"></a>

<a id="Exercise_2_Data_Validation"></a>
### <a name="exercise-2-data-validation"></a>Exercice 2 : Validation des données

Dans cet exercice, vous découvrirez les nouvelles fonctionnalités de validation de données dans ASP.NET 4.5. Vous allez récupérer les nouvelles fonctionnalités de validation non obstrusive de Web Forms. Vous allez utiliser des annotations de données dans les classes de modèle d’application pour la validation d’entrée d’utilisateur, et enfin, vous allez apprendre à activer ou désactiver la validation de la demande par des contrôles individuels dans une page.

<a id="Task_1_-_Unobtrusive_Validation"></a>
#### <a name="task-1---unobtrusive-validation"></a>Tâche 1 - Validation discrète

Formulaires avec des données complexes, y compris les validateurs ont tendance à générer trop de code JavaScript dans la page, ce qui peut représenter environ 60 % du code. Avec la validation non obstrusive activée, le code HTML ressemble plus claire et utilisons.

Dans cette section, vous allez activer la validation non obstrusive ASP.NET pour comparer le code HTML généré par les deux configurations.

1. Ouvrez **Visual Studio 2012** et ouvrez le **commencer** solution situé dans le **Source\Ex2-Validation\Begin** dossier de ce laboratoire. Ou bien, vous pouvez continuer à travailler sur votre solution existante à partir de l’exercice précédent.

    1. Si vous avez ouvert le **commencer** solution, vous devez télécharger des packages NuGet manquants avant de poursuivre. Pour ce faire, dans l’Explorateur de solutions, cliquez sur le **WebFormsLab** projet **gérer les Packages NuGet**.
    2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **restaurer** afin de télécharger les packages manquants.
    3. Enfin, générez la solution en cliquant sur **générer** | **générer la Solution**.

    > [!NOTE]
    > Un des avantages de l’utilisation de NuGet est que vous ne devez expédier toutes les bibliothèques dans votre projet, ce qui réduit la taille du projet. Avec NuGet Power Tools, en spécifiant les versions de package dans le fichier Packages.config, vous serez en mesure de télécharger toutes les bibliothèques requises à la première fois que vous exécutez le projet. C’est pourquoi vous devez exécuter ces étapes après avoir ouvert une solution existante à partir de ce laboratoire.
2. Appuyez sur **F5** pour démarrer l’application web. Accédez aux clients de page, puis cliquez sur le **ajouter un nouveau client** lien.
3. Avec le bouton droit sur la page du navigateur, puis sélectionnez **afficher la Source** option pour ouvrir le code HTML généré par l’application.

    ![Affichage de la page de code HTML](whats-new-in-web-forms-in-aspnet-45/_static/image9.png "affichant la page de code HTML")

    *Affichage de la page de code HTML*
4. Faites défiler le code source de la page et notez que ASP.NET a injecté JavaScript code et les données de validation dans la page pour effectuer les validations et afficher la liste d’erreurs.

    ![Code JavaScript de validation dans la page de CustomerDetails](whats-new-in-web-forms-in-aspnet-45/_static/image10.png "code de Validation JavaScript dans la page de CustomerDetails")

    *Code JavaScript de validation dans la page de CustomerDetails*
5. Fermez le navigateur et revenez à Visual Studio.
6. Maintenant, vous allez activer la validation non obstrusive. Ouvrez **Web.Config** et recherchez **ValidationSettings:UnobtrusiveValidationMode** clé dans le **AppSettings** section **.** Définir la valeur de clé **WebForms**.

    [!code-xml[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample22.xml)]

    > [!NOTE]
    > Vous pouvez également définir cette propriété dans le &quot; **Page\_charge** &quot; événement dans le cas, vous souhaitez activer la Validation non Obstrusive uniquement pour certaines pages.
7. Ouvrez **CustomerDetails.aspx** et appuyez sur **F5** pour démarrer l’application Web.
8. Appuyez sur la touche F12 pour ouvrir les outils de développement Internet Explorer. Une fois que les outils de développement est ouvert, sélectionnez l’onglet de script. Sélectionnez **CustomerDetails.aspx** à partir du menu et prenez note que les scripts requis pour exécuter jQuery sur la page ont été chargés dans le navigateur à partir du site local.

    ![Le chargement de jQuery JavaScript fichiers directement depuis le serveur IIS local](whats-new-in-web-forms-in-aspnet-45/_static/image11.png "chargement jQuery JavaScript fichiers directement depuis le serveur IIS local")

    *Le chargement des fichiers JavaScript jQuery directement depuis le serveur IIS local*
9. Fermez le navigateur pour revenir à Visual Studio. Ouvrez le **Site.Master** à nouveau de fichiers et recherchez le **ScriptManager**. Ajoutez l’attribut **EnableCdn** propriété avec la valeur **True**. Cela obligera jQuery doit être chargé à partir de l’URL en ligne, et non les URL du site local.
10. Ouvrez **CustomerDetails.aspx** dans Visual Studio. Appuyez sur la touche F5 pour exécuter le site. Une fois Internet Explorer s’ouvre, appuyez sur la touche F12 pour ouvrir les outils de développement. Sélectionnez le **Script** onglet, puis examinez la liste déroulante. Notez que les fichiers JavaScript jQuery ne sont plus en cours chargés à partir du site local, mais plutôt à partir de la ligne jQuery CDN.

    ![Le chargement de jQuery JavaScript les fichiers à partir du CDN](whats-new-in-web-forms-in-aspnet-45/_static/image12.png "chargement jQuery JavaScript les fichiers à partir du CDN")

    *Le chargement des fichiers JavaScript jQuery du CDN*
11. Ouvrez le code source de page HTML en utilisant l’option de source de l’affichage dans le navigateur. Notez qu’en activant la validation non obstrusive ASP.NET a remplacé le code injecté JavaScript avec données - \*attributs.

    ![Code de validation non obstrusive](whats-new-in-web-forms-in-aspnet-45/_static/image13.png "code de validation non Obstrusive")

    *Code de validation non obstrusive*

    > [!NOTE]
    > Dans cet exemple, vous avez vu comment un résumé et annotations de données de validation a été simplifiée pour quelques HTML et JavaScript lignes. Auparavant, sans validation discrète, les contrôles de validation plus vous ajoutez, plus votre code de validation JavaScript augmente.

<a id="Task_2_-_Validating_the_Model_with_Data_Annotations"></a>
#### <a name="task-2---validating-the-model-with-data-annotations"></a>Tâche 2 : validation du modèle avec des Annotations de données

ASP.NET 4.5 introduit la validation des annotations de données de Web Forms. Au lieu d’avoir un contrôle de validation sur chaque entrée, vous pouvez maintenant définir des contraintes dans vos classes de modèle et les utiliser dans votre de l’application web. Dans cette section, vous allez apprendre à utiliser des annotations de données pour valider un formulaire nouveau/modifier un client.

1. Ouvrez **CustomerDetail.aspx** page. Notez que le client le prénom et nom de la deuxième dans la **EditItemTemplate** et **InsertItemTemplate** sections sont validées à l’aide d’un contrôle RequiredFieldValidator. Chaque programme de validation est associée à une condition particulière, vous devez inclure autant de validateurs en tant que les conditions à vérifier.
2. Ajouter des annotations de données pour valider la classe de modèle de client. Ouvrez **Customer.cs** classe dans le **modèle** dossier et *décorer* chaque propriété à l’aide des attributs d’annotations de données.

    (Code d’extrait de code - *Web Forms des Annotations de données Lab - Ex02 -*)

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample23.cs)]

    > [!NOTE]
    > .NET framework 4.5 a étendu de la collection d’annotations de données existant. Voici quelques-unes des annotations de données que vous pouvez utiliser : [CreditCard], [Phone], [EmailAddress], [plage], [comparer], [Url] et [FileExtensions], [Required], [clé], [RegularExpression].
    > 
    > Voici quelques exemples d’utilisation :
    > 
    > [clé]: Specifies that an attribute is the unique identifier
    > 
    > [Range(0.4, 0.5, ErrorMessage=&quot;{Write an error message}&quot;]: Double range
    > 
    > [EmailAddress(ErrorMessage=&quot;Invalid Email&quot;), MaxLength(56)]: Two annotations in the same line.
    > 
    > Vous pouvez également définir vos propres messages d’erreur au sein de chaque attribut.
3. Ouvrez **CustomerDetails.aspx** et supprimer toutes les RequiredFieldvalidators pour les champs nom et prénom dans les sections EditItemTemplate et InsertItemTemplate en du contrôle FormView.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample24.aspx)]

    > [!NOTE]
    > Un avantage de l’utilisation d’annotations de données est que la logique de validation n’est pas dupliquée dans vos pages d’application. Vous définissez qu’une seule fois dans le modèle et l’utiliser sur toutes les pages d’application qui manipulent les données.
4. Ouvrez **CustomerDetails.aspx** code-behind et recherchez la méthode SaveCustomer. Cette méthode est appelée lors de l’insertion d’un nouveau client et reçoit le paramètre de client à partir des valeurs de contrôle FormView. Lorsque le mappage entre les contrôles de page et le se produit lorsque des objets de paramètre, ASP.NET exécute la validation du modèle par rapport à toutes les annotations de données des attributs et remplir le dictionnaire ModelState avec les erreurs rencontrées, le cas échéant.

    Le ModelState.IsValid uniquement retourne true si tous les champs de votre modèle sont valides après l’exécution de la validation.

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample25.cs)]
5. Ajouter un **ValidationSummary** contrôle à la fin de la page CustomerDetails pour afficher la liste des erreurs de modèle.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample26.aspx)]

    Le **ShowModelStateErrors** est une nouvelle propriété sur le contrôle ValidationSummary contrôle que lorsque la valeur **true**, le contrôle affiche les erreurs à partir du dictionnaire ModelState. Ces erreurs proviennent de la validation d’annotations de données.
6. Appuyez sur **F5** pour exécuter l’application Web. Remplissez le formulaire avec quelques valeurs erronées et cliquez sur **enregistrer** pour exécuter la validation. Notez que le résumé en bas de l’erreur.

    ![Validation avec des Annotations de données](whats-new-in-web-forms-in-aspnet-45/_static/image14.png "Validation avec des Annotations de données")

    *Validation avec des Annotations de données*

<a id="Task_3_-_Handling_Custom_Database_Errors_with_ModelState"></a>
#### <a name="task-3---handling-custom-database-errors-with-modelstate"></a>Tâche 3 : gestion des erreurs de base de données personnalisées avec ModelState

Dans la version précédente de Web Forms, la gestion des erreurs de base de données comme une chaîne trop longue ou une violation de clé unique peut impliquer la levée d’exceptions dans votre code de référentiel et puis gestion des exceptions dans votre code-behind pour afficher une erreur. Une grande quantité de code est nécessaire pour effectuer une opération relativement simple.

Web Forms 4.5, l’objet ModelState peut être utilisé pour afficher les erreurs dans la page, à partir de votre modèle ou à partir de la base de données de façon cohérente.

Dans cette tâche, vous ajouterez du code pour gérer les exceptions de base de données et afficher le message approprié à l’utilisateur à l’aide de l’objet ModelState correctement.

1. Pendant que l’application est en cours d’exécution, essayez de mettre à jour le nom d’une catégorie à l’aide d’une valeur en double.

    ![Mise à jour une catégorie avec un nom dupliqué](whats-new-in-web-forms-in-aspnet-45/_static/image15.png "mise à jour une catégorie avec un nom en double")

    *Mise à jour une catégorie avec un nom en double*

    Notez qu’une exception est levée en raison du &quot;unique&quot; contrainte de la **CategoryName** colonne.

    ![Exception pour les noms de catégorie en double](whats-new-in-web-forms-in-aspnet-45/_static/image16.png "Exception pour les noms de catégorie en double")

    *Exception pour les noms de catégorie en double*
2. Arrêter le débogage. Dans le **Products.aspx.cs** fichier code-behind, mettez à jour le **UpdateCategory ne** méthode pour gérer les exceptions levées par la base de données. SaveChanges() méthode appeler et ajouter une erreur à la **ModelState** objet.

    La nouvelle **TryUpdateModel** méthode met à jour l’objet category récupérée à partir de la base de données à l’aide des données de formulaire fournies par l’utilisateur.

    (Code d’extrait de code - *Web Forms Lab - Ex02 - UpdateCategory n’Handle erreurs*)

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample27.cs)]

    > [!NOTE]
    > Dans l’idéal, vous devez identifier la cause de le DbUpdateException et vérifiez si la cause première est la violation d’une contrainte de clé unique.
3. Ouvrez **Products.aspx** et ajoutez un **ValidationSummary** contrôle sous les catégories GridView pour afficher la liste des erreurs de modèle.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample28.aspx)]
4. Exécuter le site et accédez à la page produits. Essayez de mettre à jour le nom d’une catégorie à l’aide d’une valeur en double.

    Notez que l’exception a été gérée et le message d’erreur s’affiche dans le **ValidationSummary** contrôle.

    ![Dupliqué erreur catégorie](whats-new-in-web-forms-in-aspnet-45/_static/image17.png "dupliqué erreur de catégorie")

    *Erreur de catégorie en double*

<a id="Task_4_-_Request_Validation_in_ASPNET_Web_Forms_45"></a>
#### <a name="task-4---request-validation-in-aspnet-web-forms-45"></a>Tâche 4 : demander la Validation de Web Forms ASP.NET 4.5

La fonctionnalité de validation de demande dans ASP.NET fournit un certain niveau de protection par défaut contre les attaques d’écriture de scripts entre sites (XSS). Dans les versions précédentes d’ASP.NET, la validation de la demande était activée par défaut et peut uniquement être désactivée pour une page entière. Avec la nouvelle version de Web Forms ASP.NET vous pouvez maintenant désactiver la validation de la demande pour un seul contrôle, effectuer une validation de requête différée ou accéder aux données de demande non validée (soyez prudent si vous le faites !).

1. Appuyez sur **Ctrl + F5** pour démarrer le site sans débogage et accédez à la page produits. Sélectionnez une catégorie, puis cliquez sur le **modifier** lien sur un des produits.
2. Tapez une description qui contient le contenu potentiellement dangereux, y compris les balises HTML pour l’instance. Prendre en compte l’exception levée en raison de la validation de la demande.

    ![Modification d’un produit avec un contenu potentiellement dangereux](whats-new-in-web-forms-in-aspnet-45/_static/image18.png "modification d’un produit avec un contenu potentiellement dangereux")

    *Modification d’un produit avec un contenu potentiellement dangereux*

    ![Exception levée en raison de la validation de la demande](whats-new-in-web-forms-in-aspnet-45/_static/image19.png "Exception levée en raison de la validation de la demande")

    *Exception levée en raison de la validation de la demande*
3. Fermez la page et, dans Visual Studio, appuyez sur **MAJ + F5** pour arrêter le débogage.
4. Ouvrez le **ProductDetails.aspx** page et recherchez le **Description** zone de texte.
5. Ajoutez le nouveau **ValidateRequestMode** propriété à la zone de texte et définissez sa valeur sur **désactivé**.

    La nouvelle **ValidateRequestMode** attribut vous permet de désactiver la validation de la demande granulaire sur chaque contrôle. Cela est utile lorsque vous souhaitez utiliser une entrée qui peut-être recevoir le code HTML, mais souhaitez conserver la validation pour le reste de la page.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample29.aspx)]
6. Appuyez sur **F5** pour exécuter l’application web. Ouvrez la page de modification du produit à nouveau et une description de produit, y compris les balises HTML complète. Notez que vous pouvez maintenant ajouter du contenu HTML à la description.

    ![Validation désactivée pour la description du produit de la demande](whats-new-in-web-forms-in-aspnet-45/_static/image20.png "désactivée pour la description du produit de validation de la demande")

    *Validation de la demande désactivée pour la description du produit*

    > [!NOTE]
    > Dans une application de production, vous devez assainir le code HTML entré par l’utilisateur pour vous assurer que des balises HTML uniquement sans échec sont entrés (par exemple, il y a aucune &lt;script&gt; balises). Pour ce faire, vous pouvez utiliser [bibliothèque de Protection de Microsoft Web](https://www.nuget.org/packages/AntiXSS).
7. Modifier le produit à nouveau. Tapez le code HTML dans le champ nom, cliquez sur **enregistrer**. Notez que la demande de Validation est désactivée uniquement pour le champ de Description et le reste des champs re toujours valider par rapport au contenu potentiellement dangereux.

    ![La validation activée dans le reste des champs de requête](whats-new-in-web-forms-in-aspnet-45/_static/image21.png "activée dans le reste des champs de validation de la demande")

    *Validation de la demande activée dans le reste des champs*

    Web Forms ASP.NET 4.5 inclut un nouveau mode de validation de demande pour valider une demande tardivement. Avec le mode de validation demande la valeur **4.5**, si une partie du code accède à *Request.Form [&quot;clé&quot;]*, demande validation ne concernent que de ASP.NET 4.5 la validation de requête pour cet élément spécifique dans la collection de formulaires.

    En outre, ASP.NET 4.5 inclut désormais les routines d’encodage core à partir de la version 4.0 de Microsoft Anti-XSS bibliothèque. Routines d’encodage sont implémentées par le nouveau Anti-XSS *AntiXssEncoder* type figurant dans le nouveau **System.Web.Security.AntiXss** espace de noms. Avec la **encoderType** paramètre configuré pour utiliser *AntiXssEncoder*, toutes les sorties automatiquement le codage dans ASP.NET utilise les routines de codage de nouveau.
8. Validation de la demande 4.5 ASP.NET prend également en charge les accès non validé pour demander des données. ASP.NET 4.5 ajoute une nouvelle propriété de collection pour le **HttpRequest** objet appelé **Unvalidated**. Lorsque vous naviguez dans **HttpRequest.Unvalidated** vous avez accès à toutes les parties communes des données de requête, y compris les formulaires, requête, les Cookies, URL et ainsi de suite.

    ![Objet de Request.Unvalidated](whats-new-in-web-forms-in-aspnet-45/_static/image22.png "Request.Unvalidated objet")

    *Objet de Request.Unvalidated*

    > [!NOTE]
    > **Utilisez la propriété HttpRequest.Unvalidated avec attention !** Assurez-vous de qu'effectuer avec soin validation personnalisée sur les données brutes de demande pour vous assurer que texte dangereuse n’est pas un aller-retour et rendu vers des clients non !

<a id="Exercise3"></a>

<a id="Exercise_3_Asynchronous_Page_Processing_in_ASPNET_Web_Forms"></a>
### <a name="exercise-3-asynchronous-page-processing-in-aspnet-web-forms"></a>Exercice 3 : Page asynchrone dans ASP.NET Web de traitement de formulaires

Dans cet exercice, vous seront introduites à la nouvelle page asynchrone du traitement des fonctionnalités dans ASP.NET Web Forms.

<a id="Task_1_-_Updating_the_Product_Details_Page_to_Upload_and_Show_Images"></a>
#### <a name="task-1---updating-the-product-details-page-to-upload-and-show-images"></a>Page pour télécharger et afficher les Images de détails de la tâche 1 - mise à jour du produit

Dans cette tâche, vous mettrez à jour la page Détails du produit pour autoriser l’utilisateur à spécifier une URL d’image pour le produit et les afficher dans la vue en lecture seule. Vous allez créer une copie locale de l’image spécifiée en le téléchargeant de façon synchrone. Dans la tâche suivante, vous mettrez à jour cette implémentation pour qu’il fonctionne de façon asynchrone.

1. Ouvrez **Visual Studio 2012** et charger la **commencer** solution situé dans **Source\Ex3-Async\Begin** à partir du dossier de ce laboratoire. Ou bien, vous pouvez continuer à travailler sur votre solution existante dans les exercices précédents.

    1. Si vous avez ouvert le **commencer** solution, vous devez télécharger des packages NuGet manquants avant de poursuivre. Pour ce faire, dans l’Explorateur de solutions, cliquez sur le **WebFormsLab** de projet et sélectionnez **gérer les Packages NuGet**.
    2. Dans le **gérer les Packages NuGet** boîte de dialogue, cliquez sur **restaurer** afin de télécharger les packages manquants.
    3. Enfin, générez la solution en cliquant sur **générer** | **générer la Solution**.

    > [!NOTE]
    > Un des avantages de l’utilisation de NuGet est que vous ne devez expédier toutes les bibliothèques dans votre projet, ce qui réduit la taille du projet. Avec NuGet Power Tools, en spécifiant les versions de package dans le fichier Packages.config, vous serez en mesure de télécharger toutes les bibliothèques requises à la première fois que vous exécutez le projet. C’est pourquoi vous devez exécuter ces étapes après avoir ouvert une solution existante à partir de ce laboratoire.
2. Ouvrez le **ProductDetails.aspx** page source et ajouter un champ dans ItemTemplate FormView pour afficher l’image du produit.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample30.aspx)]
3. Ajoutez un champ pour spécifier l’URL d’image dans le FormView EditTemplate.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample31.aspx)]
4. Ouvrez le **ProductDetails.aspx.cs** code-behind et ajoutez les directives d’espace de noms suivantes.

    (Code d’extrait de code - *Web Forms Lab - Ex03 - espaces de noms*)

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample32.cs)]
5. Créer un **UpdateProductImage** méthode pour stocker les images distantes locaux **Images** dossier et mise à jour de l’entité product avec la nouvelle valeur d’emplacement image.

    (Code d’extrait de code - *Web Forms Lab - Ex03 - UpdateProductImage*)

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample33.cs)]
6. Mise à jour la **UpdateProduct** méthode à appeler le **UpdateProductImage** (méthode).

    (Code d’extrait de code - *Web Forms Lab - Ex03 - UpdateProductImage appel*)

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample34.cs)]
7. Exécutez l’application et essayez de télécharger une image pour un produit. Par exemple, vous pouvez utiliser l’URL d’image suivants à partir d’Office Clip Arts : [ [http://officeimg.vo.msecnd.net/images/MB900437099.jpg](http://officeimg.vo.msecnd.net/images/MB900437099.jpg)](http://officeimg.vo.msecnd.net/images/MB900437099.jpg)

    ![Définition d’une image pour un produit](whats-new-in-web-forms-in-aspnet-45/_static/image23.png "définition d’une image pour un produit")

    *Définition d’une image pour un produit*

<a id="Task_2_-_Adding_Asynchronous_Processing_to_the_Product_Details_Page"></a>
#### <a name="task-2---adding-asynchronous-processing-to-the-product-details-page"></a>Tâche 2 - Ajout asynchrone du traitement de la Page Détails du produit

Dans cette tâche, vous mettrez à jour la page Détails du produit pour qu’il fonctionne de façon asynchrone. Vous allez améliorer une tâche de longue durée - le processus de téléchargement d’images - à l’aide du traitement de la page asynchrone ASP.NET 4.5.

Les méthodes asynchrones dans les applications web permet d’optimiser la façon dont les pools de threads ASP.NET sont utilisés. Dans ASP.NET, il sont un nombre limité de threads dans le pool de threads de se préoccuper des demandes, par conséquent, lorsque tous les threads sont occupés, ASP.NET commence à rejeter les nouvelles demandes, envoie des messages d’erreur application et ne permet pas votre site.

Opérations de longue durée sur votre site web sont des candidats idéaux pour la programmation asynchrone, car ils occupent le thread assigné pendant un certain temps. Cela inclut les demandes longues, les pages avec un grand nombre des différents éléments et les pages qui requièrent des opérations hors ligne, ces interroger une base de données ou l’accès à un serveur web externe. L’avantage est que si vous utilisez des méthodes asynchrones pour ces opérations, lors du traitement de la page, le thread est libéré et retourné au thread du pool et peut être utilisé pour assister à une nouvelle demande de page. Cela signifie que, la page commence à traiter dans un thread du pool de threads et peut terminer le traitement dans un autre, une fois le traitement asynchrone est terminée.

1. Ouvrez le **ProductDetails.aspx** page. Ajouter le **Async** d’attribut dans le **Page** élément et affectez-lui la valeur **true**. Cet attribut indique à ASP.NET d’implémenter l’interface IHttpAsyncHandler.


    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample35.aspx)]
2. Ajouter une étiquette au bas de la page pour afficher les détails des threads d’exécution de la page.

    [!code-aspx[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample36.aspx)]
3. Ouvrez **ProductDetails.aspx.cs** et ajoutez les directives d’espace de noms suivantes.

    (Code d’extrait de code - *Web Forms espaces de noms Lab - Ex03 - 2*)

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample37.cs)]
4. Modifier la **UpdateProductImage** méthode pour télécharger l’image avec une tâche asynchrone. Vous allez remplacer le **WebClient** **DownloadFile** méthode avec la **DownloadFileTaskAsync** (méthode) et inclure le **await** (mot clé).

    (Code d’extrait de code - *Web Forms Lab - Ex03 - UpdateProductImage Async*)

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample38.cs)]

    Le RegisterAsyncTask enregistre une nouvelle tâche asynchrone de page doit être exécuté dans un autre thread. Il reçoit une expression lambda avec la tâche (t) doit être exécuté. Le **await** mot clé dans le **DownloadFileTaskAsync** méthode convertit le reste de la méthode dans un rappel qui est appelé de façon asynchrone après le **DownloadFileTaskAsync** méthode est terminée. ASP.NET reprend l’exécution de la méthode par une maintenance automatique tous d’origine valeurs de requête. Le nouveau modèle de programmation asynchrone dans .NET 4.5 permet d’écrire du code asynchrone qui ressemble fortement à du code synchrone et laisser le compilateur gérer les défis liés à des fonctions de rappel ou de code de continuation.
    > [!NOTE]
    > RegisterAsyncTask et PageAsyncTask ont été déjà disponibles depuis le .NET 2.0. Le mot clé await est nouveau dans le modèle de programmation asynchrone .NET 4.5 et peut être utilisé avec les nouvelles méthodes TaskAsync à partir de l’objet .NET WebClient.
5. Ajoutez du code pour afficher les threads sur lequel le code a démarré et fin de l’exécution. Pour ce faire, mettez à jour le **UpdateProductImage** méthode avec le code suivant.

    (Code d’extrait de code - *Web Forms Lab - Ex03 - afficher threads*)

    [!code-csharp[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample39.cs)]
6. Ouvrez le site **Web.config** fichier. Ajoutez la variable appSetting suivante.

    [!code-xml[Main](whats-new-in-web-forms-in-aspnet-45/samples/sample40.xml)]
7. Appuyez sur **F5** pour exécuter l’application et de télécharger une image pour le produit. Notez l’ID de threads où le code de début et de fin peut être différent. Il s’agit comme tâches asynchrones s’exécutent sur un thread séparé du pool de threads ASP.NET. Lorsque la tâche se termine, ASP.NET place la tâche dans la file d’attente et attribue des threads disponibles.

    ![Télécharger une image de manière asynchrone](whats-new-in-web-forms-in-aspnet-45/_static/image24.png "télécharger une image de manière asynchrone")

    *Télécharger une image de manière asynchrone*

> [!NOTE]
> En outre, vous pouvez déployer cette application à Azure suit [annexe b : publication une Application ASP.NET MVC 4, à l’aide de Web Deploy](#AppendixB).


* * *

<a id="Summary"></a>
## <a name="summary"></a>Récapitulatif

Dans cet atelier pratique, les concepts suivants ont été traités et présentés icis :

- Utiliser des expressions de liaison de données fortement typées
- Utiliser les nouvelles fonctionnalités de liaison de modèle dans les formulaires Web
- Utiliser des fournisseurs de valeurs pour le mappage des données de page aux méthodes de code-behind
- Utiliser des Annotations de données pour la validation d’entrée utilisateur
- Prendre advange d’unobstrusive la validation côté client avec jQuery dans les Web Forms
- Implémenter la validation de la demande granulaire
- Implémenter le traitement dans les formulaires Web asynchrone de la page

<a id="AppendixA"></a>

<a id="Appendix_A_Installing_Visual_Studio_Express_2012_for_Web"></a>
## <a name="appendix-a-installing-visual-studio-express-2012-for-web"></a>Annexe a : installation de Visual Studio Express 2012 pour le Web

Vous pouvez installer **Microsoft Visual Studio Express 2012 pour Web** ou un autre &quot;Express&quot; à l’aide de la version du  **[Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx)** . Les instructions suivantes vous guident à travers les étapes requises pour installer *Visual studio Express 2012 pour le Web* à l’aide de *Microsoft Web Platform Installer*.

1. Accédez à [ [https://go.microsoft.com/?linkid=9810169](https://go.microsoft.com/?linkid=9810169)](https://go.microsoft.com/?linkid=9810169). Sinon, si vous avez déjà installé Web Platform Installer, vous pouvez ouvrir il et recherchez le produit &quot; *Visual Studio Express 2012 pour le Web avec Azure SDK*&quot;.
2. Cliquez sur **installer maintenant**. Si vous n’avez pas **Web Platform Installer** vous allez être redirigé pour télécharger et installer tout d’abord.
3. Une fois **Web Platform Installer** est ouvert, cliquez sur **installer** pour démarrer le programme d’installation.

    ![Installer Visual Studio Express](whats-new-in-web-forms-in-aspnet-45/_static/image25.png "installer Visual Studio Express")

    *Installer Visual Studio Express*
4. Lisez les termes et les licences de tous les produits et cliquez sur **J’accepte** pour continuer.

    ![Accepter les termes du contrat de licence](whats-new-in-web-forms-in-aspnet-45/_static/image26.png)

    *Accepter les termes du contrat de licence*
5. Attendez que le processus de téléchargement et l’installation se termine.

    ![Progression de l'installation](whats-new-in-web-forms-in-aspnet-45/_static/image27.png)

    *Progression de l’installation*
6. Une fois l’installation terminée, cliquez sur **Terminer**.

    ![Installation est terminée](whats-new-in-web-forms-in-aspnet-45/_static/image28.png)

    *Installation est terminée*
7. Cliquez sur **Exit** fermer Web Platform Installer.
8. Pour ouvrir Visual Studio Express pour le Web, accédez à la **Démarrer** écran et démarrer l’écriture &quot; **VS Express**&quot;, puis cliquez sur le **Visual Studio Express pour le Web** vignette.

    ![VS Express pour la vignette du Web](whats-new-in-web-forms-in-aspnet-45/_static/image29.png)

    *VS Express pour la vignette du Web*

<a id="AppendixB"></a>

<a id="Appendix_B_Publishing_an_ASPNET_MVC_4_Application_using_Web_Deploy"></a>
## <a name="appendix-b-publishing-an-aspnet-mvc-4-application-using-web-deploy"></a>Annexe b : publication d’une Application ASP.NET MVC 4, à l’aide de Web Deploy

Cette annexe sera vous montrent comment créer un nouveau site web à partir du portail Azure et de publier l’application que vous avez obtenu en suivant le laboratoire, en tirant parti de la fonctionnalité de publication Web Deploy fournie par Azure.

<a id="ApxBTask1"></a>

<a id="Task_1_-_Creating_a_New_Web_Site_from_the_Windows_Azure_Portal"></a>
#### <a name="task-1---creating-a-new-web-site-from-the-azure-portal"></a>Tâche 1 - Création d’un Site Web à partir du portail Azure

1. Accédez à la [portail de gestion Azure](https://manage.windowsazure.com/) et connectez-vous en utilisant les informations d’identification Microsoft associées à votre abonnement.

    > [!NOTE]
    > Avec Azure, vous pouvez héberger des Sites Web ASP.NET 10 gratuitement et puis faire évoluer à mesure que votre trafic augmente. Vous pouvez vous inscrire [ici](http://aka.ms/aspnet-hol-azure).

    ![Ouvrez une session sur le portail Windows Azure](whats-new-in-web-forms-in-aspnet-45/_static/image30.png "connectez-vous au portail Windows Azure")

    *Connectez-vous au portail*
2. Cliquez sur **nouveau** sur la barre de commandes.

    ![Création d’un Site Web](whats-new-in-web-forms-in-aspnet-45/_static/image31.png "création d’un Site Web")

    *Création d’un Site Web*
3. Cliquez sur **de calcul** | **Site Web**. Puis sélectionnez **création rapide** option. Fournir une URL disponible pour le nouveau site web et cliquez sur **créer un Site Web**.

    > [!NOTE]
    > Azure est l’hôte pour une application web en cours d’exécution dans le cloud que vous pouvez contrôler et gérer. L’option Création rapide vous permet de déployer une application web complète sur Azure à partir du portail à l’extérieur. Il n’inclut pas les étapes de configuration d’une base de données.

    ![Création d’un Site Web à l’aide de la création rapide](whats-new-in-web-forms-in-aspnet-45/_static/image32.png "création d’un Site Web à l’aide de la création rapide")

    *Création d’un Site Web à l’aide de la création rapide*
4. Attendez que la nouvelle **Site Web** est créé.
5. Une fois que le Site Web est créé, cliquez sur le lien situé sous le **URL** colonne. Vérifiez que le nouveau Site Web fonctionne.

    ![Navigation vers le nouveau site web](whats-new-in-web-forms-in-aspnet-45/_static/image33.png "exploration vers le nouveau site web")

    *Navigation vers le nouveau site web*

    ![Site Web en cours d’exécution](whats-new-in-web-forms-in-aspnet-45/_static/image34.png "site Web en cours d’exécution")

    *Site Web en cours d’exécution*
6. Revenez au portail et cliquez sur le nom du site web sous le **nom** colonne pour afficher les pages de gestion.

    ![Ouvrir les pages de gestion du site web](whats-new-in-web-forms-in-aspnet-45/_static/image35.png "ouvrir les pages de gestion de site web")

    *Ouvrir les pages de gestion de Site Web*
7. Dans le **tableau de bord** sous le **coup de œil rapide** , cliquez sur le **télécharger le profil de publication** lien.

    > [!NOTE]
    > Le *le profil de publication* contient toutes les informations nécessaires pour publier une application web sur Azure pour chaque méthode de publication activée. Le profil de publication contient les URL, les informations d’identification utilisateur et les chaînes de base de données requis pour se connecter à et de s’authentifier auprès de chacun des points de terminaison pour laquelle une méthode de publication est activée. **Microsoft WebMatrix 2**, **Microsoft Visual Studio Express pour Web** et **Microsoft Visual Studio 2012** prise en charge la lecture de publier les profils pour automatiser la configuration de ces programmes pour la publication d’applications web vers Azure.

    ![Profil de publication de téléchargement du site web](whats-new-in-web-forms-in-aspnet-45/_static/image36.png "profil de publication de téléchargement du site web")

    *Profil de publication de téléchargement du Site Web*
8. Téléchargez le fichier de profil de publication à un emplacement connu. Davantage dans cet exercice, vous verrez comment utiliser ce fichier pour publier une application web vers Azure à partir de Visual Studio.

    ![L’enregistrement du fichier de profil de publication](whats-new-in-web-forms-in-aspnet-45/_static/image37.png "l’enregistrement du profil de publication")

    *L’enregistrement du fichier de profil de publication*

<a id="ApxBTask2"></a>

<a id="Task_2_-_Configuring_the_Database_Server"></a>
#### <a name="task-2---configuring-the-database-server"></a>Tâche 2 : configuration du serveur de base de données

Si votre application se sert de SQL Server vous devez créer un serveur de base de données SQL des bases de données. Si vous souhaitez déployer une application simple qui n’utilise pas de SQL Server, vous pouvez ignorer cette tâche.

1. Vous devez un serveur de base de données SQL pour stocker la base de données de l’application. Vous pouvez afficher les serveurs de base de données SQL à partir de votre abonnement dans le portail de gestion Azure à **bases de données Sql** | **serveurs** | **tableau de bord du serveur**. Si vous ne disposez pas d’un serveur créé, vous pouvez créer un à l’aide de la **ajouter** bouton sur la barre de commandes. Prenez note de la **nom du serveur et les URL, nom de connexion d’administrateur et un mot de passe**, comme vous allez l’utiliser dans les tâches suivantes. Ne créez pas encore, la base de données telle qu’elle sera créée dans une étape ultérieure.

    ![Tableau de bord de serveur SQL de base de données](whats-new-in-web-forms-in-aspnet-45/_static/image38.png "tableau de bord de serveur SQL de base de données")

    *Tableau de bord de serveur SQL de base de données*
2. Dans la tâche suivante, vous allez tester la connexion de base de données à partir de Visual Studio, pour cette raison, vous devez inclure votre adresse IP locale dans la liste du serveur de **adresses IP autorisées**. Pour ce faire, cliquez sur **configurer**, sélectionnez l’adresse IP à partir de **adresse IP du Client actuel** et le coller dans le **adresse IP de début** et **adresse IP de fin** zones de texte et cliquez sur le ![add-client-ip-address-ok-button](whats-new-in-web-forms-in-aspnet-45/_static/image39.png) bouton.

    ![Ajout d’adresse IP du Client](whats-new-in-web-forms-in-aspnet-45/_static/image40.png)

    *Ajout d’adresse IP du Client*
3. Une fois la **adresse IP du Client** est ajouté aux adresses IP autorisées de liste, cliquez sur **enregistrer** pour confirmer les modifications.

    ![Confirmer les modifications](whats-new-in-web-forms-in-aspnet-45/_static/image41.png)

    *Confirmer les modifications*

<a id="ApxBTask3"></a>

<a id="Task_3_-_Publishing_an_ASPNET_MVC_4_Application_using_Web_Deploy"></a>
#### <a name="task-3---publishing-an-aspnet-mvc-4-application-using-web-deploy"></a>Tâche 3 : publication d’une Application ASP.NET MVC 4, à l’aide de Web Deploy

1. Revenez à la solution ASP.NET MVC 4. Dans le **l’Explorateur de solutions**, cliquez sur le projet de site web et sélectionnez **publier**.

    ![Publication de l’Application](whats-new-in-web-forms-in-aspnet-45/_static/image42.png "publication de l’Application")

    *Publier le site web*
2. Importer le profil de publication que vous avez enregistré dans la première tâche.

    ![L’importation du profil de publication](whats-new-in-web-forms-in-aspnet-45/_static/image43.png "l’importation du profil de publication")

    *Importation du profil de publication*
3. Cliquez sur **valider la connexion**. Une fois la Validation terminée. Cliquez sur **suivant**.

    > [!NOTE]
    > La validation est terminée une fois que vous voyez une coche verte apparaît en regard du bouton Valider la connexion.

    ![Validation de la connexion](whats-new-in-web-forms-in-aspnet-45/_static/image44.png "validation de la connexion")

    *Validation de la connexion*
4. Dans le **paramètres** sous le **bases de données** , cliquez sur le bouton en regard de la zone de texte de la connexion de votre base de données (par exemple, **DefaultConnection**).

    ![Configuration de déploiement Web](whats-new-in-web-forms-in-aspnet-45/_static/image45.png "configuration de déploiement Web")

    *Configuration de déploiement Web*
5. Configurer la connexion de base de données comme suit :

    - Dans le **nom du serveur** tapez votre URL de base de données SQL server à l’aide du *tcp :* préfixe.
    - Dans **nom d’utilisateur** tapez le nom de connexion de votre administrateur de serveur.
    - Dans **mot de passe** votre mot de passe du compte de connexion administrateur serveur.
    - Tapez un nouveau nom de base de données.

    ![Configuration de chaîne de connexion de destination](whats-new-in-web-forms-in-aspnet-45/_static/image46.png "configuration de chaîne de connexion de destination")

    *Configuration de chaîne de connexion de destination*
6. Cliquez ensuite sur **OK**. Lorsque vous êtes invité à créer la base de données, cliquez sur **Oui**.

    ![Création de la base de données](whats-new-in-web-forms-in-aspnet-45/_static/image47.png "création de la chaîne de la base de données")

    *Création de la base de données*
7. La chaîne de connexion que vous allez utiliser pour se connecter à la base de données SQL dans Azure est indiquée dans la zone de texte par défaut de connexion. Cliquez ensuite sur **Suivant**.

    ![Chaîne de connexion pointant vers la base de données SQL](whats-new-in-web-forms-in-aspnet-45/_static/image48.png "chaîne de connexion pointant vers la base de données SQL")

    *Chaîne de connexion pointant vers la base de données SQL*
8. Dans le **aperçu** , cliquez sur **publier**.

    ![Publication de l’application web](whats-new-in-web-forms-in-aspnet-45/_static/image49.png "publication de l’application web")

    *Publication de l’application web*
9. Une fois le processus de publication terminé, votre navigateur par défaut s’ouvre le site web publié.

<a id="AppendixC"></a>

<a id="Appendix_C_Using_Code_Snippets"></a>
## <a name="appendix-c-using-code-snippets"></a>Annexe c : à l’aide d’extraits de Code

Avec des extraits de code, vous avez tout le code que vous avez besoin. Le document lab vous indique exactement quand vous pouvez les utiliser, comme indiqué dans l’illustration suivante.

![À l’aide d’extraits de code Visual Studio pour insérer du code dans votre projet](whats-new-in-web-forms-in-aspnet-45/_static/image50.png "des extraits de code à l’aide de Visual Studio pour insérer du code dans votre projet")

*À l’aide d’extraits de code Visual Studio pour insérer du code dans votre projet*

***Pour ajouter un extrait de code à l’aide du clavier (c# uniquement)***

1. Placez le curseur où vous souhaitez insérer le code.
2. Commencez à taper le nom de l’extrait de code (sans espaces ou des traits d’union).
3. Observez comment IntelliSense affiche les noms des extraits de code de mise en correspondance.
4. Sélectionnez l’extrait de code correct (ou continuez à taper jusqu'à ce que le nom de l’extrait de code entier est sélectionné).
5. Appuyez sur la touche Tab à deux reprises pour insérer l’extrait de code à l’emplacement du curseur.

![Commencez à taper le nom de l’extrait de code](whats-new-in-web-forms-in-aspnet-45/_static/image51.png "commencez à taper le nom de l’extrait de code")

*Commencez à taper le nom de l’extrait de code*

![Appuyez sur Tab pour sélectionner l’extrait de code en surbrillance](whats-new-in-web-forms-in-aspnet-45/_static/image52.png "appuyez sur Tab pour sélectionner l’extrait de code en surbrillance")

*Appuyez sur Tab pour sélectionner l’extrait de code en surbrillance*

![Appuyez sur Tab à nouveau et l’extrait de code sont développés](whats-new-in-web-forms-in-aspnet-45/_static/image53.png "appuyez sur Tab à nouveau et l’extrait de code seront développe.")

*Appuyez sur Tab à nouveau et l’extrait de code seront développe.*

***Pour ajouter un extrait de code à l’aide de la souris (c#, Visual Basic et XML)*** 1. Clic droit où vous souhaitez insérer l’extrait de code.

1. Sélectionnez **insérer un extrait** suivie **mes extraits de Code**.
2. Sélectionnez l’extrait de code approprié dans la liste, en cliquant dessus.

![Avec le bouton sur lequel vous souhaitez insérer l’extrait de code et sélectionnez Insérer un extrait](whats-new-in-web-forms-in-aspnet-45/_static/image54.png "avec le bouton sur lequel vous souhaitez insérer l’extrait de code et sélectionnez Insérer un extrait")

*Avec le bouton droit sur lequel vous souhaitez insérer l’extrait de code et sélectionnez Insérer un extrait*

![Sélectionnez l’extrait de code approprié dans la liste, en cliquant dessus](whats-new-in-web-forms-in-aspnet-45/_static/image55.png "choisir l’extrait de code approprié dans la liste, en cliquant sur celle-ci")

*Sélectionnez l’extrait de code approprié dans la liste, en cliquant sur celle-ci*
