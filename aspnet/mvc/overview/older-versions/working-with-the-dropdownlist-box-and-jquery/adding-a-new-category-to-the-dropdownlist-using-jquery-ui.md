---
uid: mvc/overview/older-versions/working-with-the-dropdownlist-box-and-jquery/adding-a-new-category-to-the-dropdownlist-using-jquery-ui
title: "Ajout d’une nouvelle catégorie à DropDownList à l’aide de l’interface utilisateur jQuery | Documents Microsoft"
author: Rick-Anderson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/12/2012
ms.topic: article
ms.assetid: 44aa1ac4-6ea2-48a2-972d-52710c48eae5
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/working-with-the-dropdownlist-box-and-jquery/adding-a-new-category-to-the-dropdownlist-using-jquery-ui
msc.type: authoredcontent
ms.openlocfilehash: de661616ff3ca83052ae74d3ae6810d014aff764
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="adding-a-new-category-to-the-dropdownlist-using-jquery-ui"></a>Ajout d’une nouvelle catégorie à DropDownList à l’aide de jQuery UI
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

Le code HTML `Select` balise est idéal pour présenter une liste de données de catégorie fixe, mais il peut arriver que vous avez besoin ajouter une nouvelle catégorie. Supposons que nous souhaitons ajouter le genre « Opera » pour les catégories dans notre base de données ? Dans cette section, nous allons utiliser l’interface utilisateur jQuery pour ajouter une boîte de dialogue que nous pouvons utiliser pour ajouter une nouvelle catégorie. L’image ci-dessous montre comment l’interface utilisateur présente dans le navigateur.

![](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/_static/image1.png)

Lorsqu’un utilisateur sélectionne le **ajouter un nouveau Genre** lien, une boîte de dialogue invite l’utilisateur à un nouveau nom de genre (et éventuellement une description). L’image ci-dessous montrent les **ajouter un Genre** boîte de dialogue contextuelle.

![](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/_static/image2.png)

Lors de l’entrée un nouveau nom de genre et la **enregistrer** bouton est enfoncé, les éléments suivants se produit :

1. Un appel AJAX publie les données à la méthode de création du contrôleur de Genre, qui enregistre le genre de nouveau à la base de données et retourne les nouvelles informations genre (nom du genre et ID) au format JSON.
2. JavaScript ajoute les nouvelles données de genre à la liste de sélection.
3. JavaScript rend le genre de nouveau l’élément sélectionné.

 Dans l’image ci-dessous, **Opera** a été ajouté à la base de données et sélectionné dans le **Genre** zone de liste déroulante. 

![](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/_static/image3.png)

Ouvrez le *Views\StoreManager\Create.cshtml* de fichier et remplacez la balise de genre par ce qui suit le code suivant :

[!code-cshtml[Main](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/samples/sample1.cshtml)]

Le `_ChooseGenre` contient toute la logique pour raccorder le JavaScript et jQuery utilisée pour implémenter la fonctionnalité de genre ajouter nouvelle vue partielle. Une fois que nous avons terminé le code, il est simple à faire de même avec l’auteur de l’interface utilisateur.

Dans l’Explorateur de solutions, avec le bouton droit sur le *Views\StoreManager* et sélectionnez **ajouter**, puis **vue**. Dans le **nom de la vue** d’entrée, entrez `_ChooseGenre` puis sélectionnez **ajouter**. Remplacez la balise dans le *Views\StoreManager\\_ChooseGenre.cshtml* fichier avec les éléments suivants :

[!code-cshtml[Main](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/samples/sample2.cshtml)]

La première ligne déclare que nous passons dans un `Album` en tant que notre modèle, exactement de la même instruction se trouvant dans la vue de créer de modèle. Les quelques lignes sont le **étiquette** balisage de l’application d’assistance. La ligne suivante est la **DropDownList** appeler d’assistance, exactement comme dans la vue Création d’origine. La ligne suivante ajoute un lien avec le nom `Add New Genre`, et il styles comme un bouton. La ligne contenant `ValidationMessageFor` est copié directement à partir de la vue à créer. Les lignes suivantes :

[!code-html[Main](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/samples/sample3.html)]

Crée un élément div masqué, avec l’ID de `genreDialog`. Nous allons utiliser jQuery pour raccorder notre **ajouter un Genre** boîte de dialogue avec l’ID `genreDialog` dans cette balise div. Les deux dernières balises de script contiennent des liens vers les fichiers JavaScript que nous allons utiliser pour implémenter la fonctionnalité de genre nouveau ajouter. Le */Scripts/chooseGenre.js* fichier est fourni pour vous dans le projet, nous allons l’examiner ultérieurement dans le didacticiel.

Exécutez l’application et cliquez sur le **ajouter un nouveau Genre** bouton. Dans le **ajouter un Genre** boîte de dialogue, entrez **Opera** dans les **nom** zone d’entrée.

![](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/_static/image4.png)

Cliquez sur le **enregistrer** bouton. Un appel AJAX crée la catégorie Opera remplit la liste déroulante avec Opera et définit Opera comme le genre sélectionné.

![](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/_static/image5.png)

Entrez un artiste, le titre et le prix, puis sélectionnez le **créer** bouton. Si vous entrez un prix inférieur à $8,99, le nouvel album s’affiche en haut de la vue Index. Vérifiez que la nouvelle entrée album a été enregistrée dans la base de données.

![](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/_static/image6.png)

Essayez de créer un nouveau genre avec une seule lettre. Le code suivant dans le *Models\Genre.cs* fichier définit la longueur minimale et maximale du nom de genre.

[!code-csharp[Main](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/samples/sample4.cs)]

Validation côté client signale que vous devez entrer une chaîne entre 2 et 20 caractères.

![](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/_static/image7.png)

### <a name="examining-how-a-new-genre-is-added-to-the-database-and-the-select-list"></a>Examiner comment un Genre de nouveau est ajouté à la base de données et de la liste Select.

Ouvrez le *Scripts\chooseGenre.js* de fichiers et examinez le code.

[!code-javascript[Main](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/samples/sample5.js)]

La deuxième ligne utilise l’ID de `genreDialog` pour créer une boîte de dialogue sur la balise div dans les *Views\StoreManager\\_ChooseGenre.cshtml* fichier. La plupart des paramètres nommés est explicites. Le `autoOpen` paramètre est défini sur false, en sélectionnant le **créer un Genre** bouton permet d’ouvrir la boîte de dialogue explicitement (qui est décrit ce dernier sur). La boîte de dialogue a deux boutons, **enregistrer** et **Annuler**. Le **Annuler** bouton ferme la boîte de dialogue. Le code suivant illustre la **enregistrer** bouton de fonction.

[!code-javascript[Main](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/samples/sample6.js)]

Le `var createGenreForm` est sélectionné dans le `createGenreForm` ID. Le `createGenreForm` ID a été défini dans le code suivant trouvé dans le *Views\Genre\\_CreateGenre.cshtml* fichier.

[!code-cshtml[Main](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/samples/sample7.cshtml)]

Le [Html.BeginForm](https://msdn.microsoft.com/library/dd492714.aspx) surcharge d’assistance utilisée dans le *Views\Genre\\_CreateGenre.cshtml* fichier génère du code HTML avec un attribut d’action contenant l’URL pour envoyer le formulaire. Vous pouvez le voir en affichant la page de l’album créer dans un navigateur et en sélectionnant Afficher source dans le navigateur. Le balisage suivant montre le code HTML généré, contenant la balise de formulaire.

[!code-html[Main](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/samples/sample8.html)]

JQuery `$.post` ligne effectue un appel AJAX à l’attribut d’action (`/StoreManager/Create`) et transmet les données à partir de la **créer un Genre** boîte de dialogue. Les données se comprennent du nom de la nouvelle genre et une description facultative. Si l’appel AJAX est réussie, le nouveau nom de genre et la valeur sont ajoutés à la balise Select, et le nouveau genre est défini sur la valeur sélectionnée. Comme il s’agit de balisage généré dynamiquement, vous ne peut pas voir la nouvelle option de sélection en affichant la source dans le navigateur. Vous pouvez voir le nouveau code HTML avec les outils de développement Internet Explorer 9 F12. Pour afficher la nouvelle option de sélection, dans Internet Explorer 9, appuyez sur la touche F12 pour démarrer les outils de développement F12. Accédez à la page Créer et ajouter un nouveau genre pour le nouveau genre est sélectionné dans la liste de sélection de genre. Dans les outils de développement F12 :

1. Sélectionnez l’onglet HTML.
2. Appuyez sur l’icône d’actualisation.  
    ![](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/_static/image8.png)
3. Dans la zone de recherche, entrez GenreID.
4. À l’aide de l’icône suivante,   
    ![](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/_static/image9.png)  
 Accédez à la balise select suivante :

    [!code-html[Main](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/samples/sample9.html)]
5. Développez la dernière valeur d’option.

![](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/_static/image10.png)

Le code suivant dans le *Scripts\chooseGenre.js* fichier illustre la façon dont le **ajouter un nouveau Genre** bouton obtient connecté à l’événement click et comment la **ajouter un nouveau Genre** boîte de dialogue est créé.

[!code-javascript[Main](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/samples/sample10.js)]

La première ligne crée une fonction de clic attachée à la **ajouter un nouveau Genre** bouton. Le balisage suivant à partir de la Views\StoreManager\\_ChooseGenre.cshtml fichier montre comment la **ajouter un nouveau Genre** bouton est créé :

[!code-cshtml[Main](adding-a-new-category-to-the-dropdownlist-using-jquery-ui/samples/sample11.cshtml)]

La méthode load crée et ouvre la boîte de dialogue Ajouter un Genre et appelle la jQuery `parse` méthode de validation côté client se produit sur les données entrées dans la boîte de dialogue.

Dans cette section, vous avez appris à créer une boîte de dialogue qui peut être utilisé pour ajouter de nouvelles données de catégorie à une liste de sélection. Vous pouvez suivre la même procédure pour créer l’interface utilisateur pour ajouter un nouvel artiste à la liste de sélection artiste. Ce didacticiel a fourni une vue d’ensemble de l’utilisation de l’application d’assistance ASP.NET MVC HTML **DropDownList**. Pour plus d’informations sur l’utilisation de la **DropDownList**, consultez la section de références addition ci-dessous. Faites-nous savoir si ce didacticiel a été utile.

Rick.Anderson[at]Microsoft.com

### <a name="additional-references"></a>Références supplémentaires

- [ASP.NET MVC – cascade de liste déroulante répertorie didacticiel](https://weblogs.asp.net/raduenuca/archive/2011/03/06/asp-net-mvc-cascading-dropdown-lists-tutorial-part-1-defining-the-problem-and-the-context.aspx) par [Radu Enuca](https://weblogs.asp.net/raduenuca/default.aspx)
- [Choisi](http://harvesthq.github.com/chosen/) plug-in A JavaScript qui prennent en charge la sélection multiple et de filtrage.

### <a name="contributors"></a>Contributors

- [Radu Enuca](https://weblogs.asp.net/raduenuca/default.aspx)
- Jean-Sébastien Goupil
- [Brad Wilson.](http://bradwilson.typepad.com/)

### <a name="reviewers"></a>Réviseurs

- Jean-Sébastien Goupil
- [Brad Wilson.](http://bradwilson.typepad.com/)
- Protocole de Mike
- Tom Dykstra

>[!div class="step-by-step"]
[Précédent](examining-how-aspnet-mvc-scaffolds-the-dropdownlist-helper.md)
